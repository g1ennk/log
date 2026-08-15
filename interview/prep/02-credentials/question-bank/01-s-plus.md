# S+ 대표 성과 방어

이력서 주요 성과 자체를 검증하는 질문 모음.

## 인증·보안

### 인증 구조 4단계

`auth-evolution.md` §1·3~6

- [x] (시작) 인증 구조 전체 설명
    - 게스트와 회원을 모두 지원하는 이중 구조이고, 4단계에 걸쳐 발전시켰다.
        - 처음 MVP 때는 백엔드 없이 브라우저 로컬 스토리지에만 데이터를 저장했다.
        - 이후 백엔드를 도입하면서, 브라우저에서 생성된 UUID를 X-Client-Id 헤더로 보내 게스트를 구분했다.
        - 이후 JWT와 OAuth를 도입해, 게스트는 Guest JWT, 회원은 OAuth 기반 Access Token과 Refresh Token으로 강화했다.
        - 이후 타이머 상태가 기기 간에 공유되지 않아 SSE 동기화를 도입했는데, 그러려면 여러 기기가 동시에 로그인 상태여야 해서 login 때 기존 RT를 유지하는 기기별 chain으로 바꾸고 RTR을 함께 적용했다.
        - 마지막으로 폐기된 RT가 들어오면 탈취로 간주해 해당 사용자의 활성 RT를 전부 무효화하는 Reuse Detection을 도입했다.
- [x] (시작) 4단계 각각 무엇이 부족해서 다음으로 넘어갔나
    - 1단계 X-Client-Id는 서명과 TTL이 없는 임의 문자열이라 값을 바꿀 수 있었다. 식별은 되지만 인증 수단으로는 부족했다.
        - 그래서 게스트는 Guest JWT, 회원은 OAuth와 Access Token과 Refresh Token 구조로 변경했다.
    - 2단계는 로그인은 해결했지만 멀티디바이스 요구사항이 추가됐다. SSE 동기화를 도입했는데, 새 기기 로그인이 기존 Refresh Token을 전부 폐기하면 전체 로그인 흐름이 끊겨 동기화 목적과 충돌했다.
        - 그래서 login 때는 기존 active Refresh Token을 유지해 기기별 chain을 만들고, refresh 시엔 현재 토큰만 회전시키는 Refresh Token Rotation을 적용했다.
    - 3단계 Refresh Token Rotation이 '폐기된 RT'라는 상태를 만들면서, 그 토큰이 다시 쓰이는 상황을 탐지할 필요가 남았다.
        - 그래서 폐기된 RT가 다시 들어오면 탈취로 보고 활성 RT를 전부 무효화하도록 확장했다.
- [x] (꼬리) 처음부터 지금 구조로 안 간 이유
    - 처음부터 JWT, OAuth, Refresh Token Rotation, Reuse Detection 등을 모두 갖추고 시작했다면 복잡도가 높아 진입 장벽이 높았을 거라고 생각한다.
    - 다만, 그때그때 요구사항에 따라 인증 구조를 발전시키면서 각 단계에서 어떤 게 부족하고 위험한지를 직접 확인할 수 있어, 각 선택의 필요성과 한계를 명확하게 알 수 있었다.
- [x] (꼬리) X-Client-Id는 어떤 방식이었나
    - FE단에서 브라우저가 랜덤 문자열 UUID를 만들어 localstorage에 저장하고, 모든 API 요청에 X-Client-Id 헤더로 실어 보냈다.
    - 초기엔 간단한 문자열로 사용자별 데이터를 나눌 수 있어 MVP에선 합리적인 선택이라고 생각한다.
- [x] (꼬리) 브라우저가 보낸 UUID를 왜 인증으로 못 보나
    - 인증은 결국 신원을 증명하는 절차인데, X-Client-Id는 증명 수단으로 부족하다.
    - 그저 랜덤 문자열이 localstorage에 있어 사용자가 개발자 도구로 바꿀 수도 있고, 서명이 없어 그 값이 자기가 발급한 것인지 판별할 방법이 없다.
    - 즉, 남의 UUID를 알아내 헤더에 넣으면 그대로 그 사람의 데이터를 조회하고 조작할 수 있다.
- [x] (꼬리) 백엔드는 X-Client-Id의 무엇을 검증했나
    - 백엔드단에선 UUID 형식만 검증하는 유효성 검사일 뿐 인증이 아니었다.
- [x] (꼬리) Guest와 Member 인증을 왜 분리했나
    - 사용자 입장에서 서비스가 뭔지도 모르고 곧바로 회원가입을 하는 것보단 서비스를 체험 후 회원가입을 유도하게끔 하고 싶었다.
    - 비회원은 거의 모든 기능을 사용 가능하지만, 여러 기기에서 타이머를 사용하거나 AI 리포트 기능은 제한되어 회원가입을 유도했다.
- [x] (꼬리) 게스트에게도 JWT를 도입한 이유
    - 게스트 사용자도 서버에서 자신의 데이터를 안전하게 사용할 수 있게 하기 위해서였다.
    - 초기 브라우저 생성 기반 X-Client-Id는 단순히 UUID 형식만 검증해 클라이언트가 값을 바꾸거나 탈취한 UUID를 넣어도 이를 인증할 수 없는 구조였다.
    - 회원 로그인에 JWT를 도입해야 했기에 자연스럽게 JWT 기반 인증 체계로 통합하고자 했다.
    - 단, Guest JWT가 막는 건 임의 UUID의 위조·변조까지다. Guest JWT도 bearer token이라 탈취되면 90일 만료 전까지 그대로 재사용될 수 있고, 게스트는 서버에 토큰 기록이 없어 폐기 수단도 없다. 재방문 연속성을 우선한 트레이드오프다.
- [x] (개념) 인증과 인가의 차이
    - 인증은 사용자가 누구인지 확인하는 절차이고, 인가는 확인된 사용자가 특정 작업을 수행할 권한이 있는지 확인하는 절차이다.
    - 현 프로젝트에서는 JWT의 서명과 만료시간 등을 검증해 요청자가 누구인지 확인하는 것이 인증이고, 이후 인증된 사용자 정보를 기준으로 해당 리소스에 접근할 수 있는지 또는 회원 전용 기능을 사용할 수 있는지 판단하는 것이 인가이다.
- [x] (개념) 인증과 식별의 차이
    - 식별은 '나는 누구다'라는 주체를 구분하거나 주장하는 것이고, 인증은 그 주장이 맞다는 것을 증명하는 것이다.
    - 기존 X-Client-Id로 사용자를 식별했지만, 그 값이 진짜 그 사용자의 것인지를 증명하는 수단은 없었다.
    - 이후 JWT를 도입하면서 서명을 통해 본격적인 인증 구조로 넘어갔다.
- [x] (개념) 세션 인증과 토큰 인증의 차이
    - 가장 큰 차이점은 상태로 서버가 인증 상태를 가지고 있냐 없느냐로, 세션은 서버가 인증 상태를 저장하고, 토큰은 클라이언트가 인증 정보를 담은 토큰을 보관해 매 요청마다 전달하는 방식이다.
        - 세션: 로그인 -> 서버가 세션 생성 -> 클라이언트에 session_id 전달 -> 이후 해당 id로 서버 세션 조회
        - 토큰: 로그인 -> 서버가 토큰 발급 -> 클라이언트에 토큰 저장 -> 요청마다 토큰 전달 -> 서버가 토큰 검증
    - 서버가 세션을 즉시 통제하거나 강제 로그아웃 등을 단순하게 처리해야 한다면 세션 방식을, 여러 서버 환경에서 인증을 하고 싶다면 토큰 방식이 유리하다.
- [x] (개념) JWT 인증의 장단점
    - Access JWT는 서명 검증만으로 처리할 수 있어 인증 경로를 stateless하게 구성할 수 있다는 점이 장점이다.
        - 토큰에 민감 정보를 제외한 필요한 정보가 담겨 있어 검증에 별도의 세션 저장소 조회가 없고, 서버가 여러 대여도 서명 키만 공유하면 인증이 가능하다.
        - 단, JWT를 쓴다고 시스템 전체가 stateless가 되는 건 아니다. FlowMate도 Refresh Token은 DB에 두고 stateful하게 관리한다.
    - 단점은 크게 세 가지이다.
        - payload에 정보를 많이 넣을수록 토큰이 길어져 네트워크 낭비가 생길 수 있고,
        - 발급 후에는 만료 전까지 서버가 강제 만료시키기 어렵고,
        - payload 자체가 Base64URL 인코딩이라 누구나 디코딩하여 정보를 확인할 수 있다. (따라서 민감 정보 포함은 절대 안됨)
- [x] (약점) RT를 DB에 두는데 이걸 Stateless라 할 수 있나
    - 엄밀히 말하면 완전히 stateless라고 하기는 어렵다. 현 프로젝트는 Access Token 검증은 stateless, 세션 수명 관리는 stateful인 하이브리드 구조이다.
        - 일반적인 API 요청에는 Access Token의 서명, 만료, 역할 등을 검증하여 DB 조회를 하지 않아 stateless라고 할 수 있다.
        - 반면 Refresh Token은 DB에 해시화해 저장하여 기기별로 세션 관리를 하므로 stateful하다.
- [x] (약점) 다시 설계하면 세션과 JWT 중 무엇 그리고 이유는?
    - 다시 설계해도 JWT 기반으로 간다.
        - 다만 멀티디바이스나 수평 확장 때문에 JWT가 필수인 건 아니다. Redis 세션으로도 둘 다 되기 때문에, 그것만으로는 근거가 안 된다.
        - FlowMate가 원한 건 트래픽 대부분을 차지하는 일반 API 요청의 인증 검증을 서버 간 공유 저장소 조회 없이 끝내는 것이었고, Access JWT가 그 경로에 적합했다.
        - 대신 세션 수명 관리가 필요한 Refresh Token은 stateful하게 DB에 두고, 회전과 재사용 탐지로 통제한다.
- [x] (약점) 4단계를 거치며 남은 부채
    - Refresh Token을 DB에 저장하기에 만료된 토큰을 물리적으로 정리하는 배치가 없다.
        - 해결한다면 주기적으로 만료 토큰을 정리하는 배치를 두거나, Redis TTL 저장소로 옮기는 선택지가 있다.
        - 현재 서비스 규모에선 크게 문제가 되지 않는다고 판단했다.
    - 지연·중복 요청으로 이미 폐기된 Refresh Token이 다시 전송되면, 현재는 탈취 재사용으로 간주해 해당 사용자의 모든 활성 Refresh Token을 무효화한다.
        - FlowMate는 개인 생산성 서비스라 전체 기기 재로그인이 큰 불편은 아니라고 판단해 별도 완화 정책을 두지 않았다.
        - 개선한다면 기기 또는 로그인 세션별 token family를 도입해 재사용 탐지 시 해당 family만 폐기하고, 방금 회전된 직전 Refresh Token에만 짧은 유예 시간을 둬 네트워크 지연과 중복 요청을 흡수할 수 있다.
    - JWT 특성상 즉시 폐기가 어렵다.
        - 게스트의 경우 90일, 회원 Access Token의 경우 15분의 TTL을 가지고 있다.
        - 현 규모에선 TTL로 감수했지만, 개선한다면 Redis 등의 저장소에 blacklist나 token version을 관리하는 방식처럼 서버 측 상태를 추가하는 방안을 검토할 수 있다.
        - 단, 조건이 붙는다. 현재 JwtProvider는 sub·role·iat·exp만 넣고 jti를 발급하지 않아, blacklist를 쓰려면 토큰 단위 식별자인 jti부터 추가해야 한다.
        - token version은 매 요청마다 서버 조회가 필요해, 위에서 말한 Access JWT의 stateless 이점을 일부 포기하는 선택이다.
    - OAuth State JWT는 stateless라 일회성 사용을 보장하지 못한다.
        - 서명과 5분 TTL은 검증하지만 서버가 사용 이력을 저장하지 않아, TTL 안에서는 같은 state로 다시 콜백을 태울 수 있다.
        - 현재는 5분 TTL과 클라이언트 sessionStorage 값 비교로 방어한다.
        - 개선한다면 State JWT의 sub(발급 시 만든 랜덤 UUID)를 Redis에 TTL과 함께 저장하고, 콜백 성공 시 즉시 소비 처리해 재사용을 차단하겠다. State JWT는 sub이 이미 토큰마다 고유해서, Access Token과 달리 jti를 새로 넣지 않아도 된다.
    - 마지막으로 OAuth를 수동으로 구현했다.
        - 현재는 카카오 단일 provider로, oauth 동작 원리를 이해하기 위해 그렇게 구현했고, OAuthProvider 전략 패턴을 적용해 추후 별도 provider가 추가되더라도 현재 규모에선 관리 가능한 수준이다.
        - 단, 서비스가 확장하면서 OAuth 자체 보안 검증, 예외 처리 등 유지보수 비용이 커지면, 검증된 Spring Security OAuth2 Client를 사용하는 것이 적합하다고 생각한다.

### JWT 구조와 검증

`auth-evolution.md` §4 · 코드 `JwtProvider`, jjwt 0.12.3

- [ ] (시작) JWT가 뭐고 왜 썼나
- [ ] (꼬리) Header·Payload·Signature 각각의 역할
- [ ] (꼬리) 변조를 서버가 어떻게 알아채나
- [ ] (꼬리) Guest JWT와 Member AT의 claim 차이
- [ ] (꼬리) `sub`이 게스트는 clientId, 회원은 userId인 게 하위 로직에 주는 영향
- [ ] (개념) Payload는 암호화되어 있나
- [ ] (개념) Base64와 암호화의 차이
- [ ] (개념) 서명은 기밀성과 무결성 중 무엇을 보장하나
- [ ] (개념) exp·iat·sub은 무엇
- [ ] (개념) HMAC과 RSA 서명의 차이
- [ ] (약점) JWT는 정말 안전한가
- [ ] (약점) 서명이 있는데 탈취되면 왜 위험한가
- [ ] (약점) signing key가 유출되면 어떻게 대응하나
- [ ] (약점) 키를 교체하면 기존 JWT는 어떻게 되나

### 토큰 4종 저장 위치·TTL

`auth-evolution.md` §4 토큰 정책

- [ ] (시작) 네 토큰을 다른 위치에 둔 이유
- [ ] (꼬리) AT를 localStorage 대신 메모리에 둔 이유
- [ ] (꼬리) 메모리면 새로고침에 사라지는데 로그인 상태는 어떻게 복원하나
- [ ] (꼬리) silent refresh는 무슨 힌트로 회원 여부를 아나
- [ ] (꼬리) RT를 HttpOnly cookie에 둔 이유
- [ ] (꼬리) Guest JWT는 왜 localStorage인가
- [ ] (꼬리) State JWT는 왜 sessionStorage인가
- [ ] (개념) Cookie와 localStorage의 차이
- [ ] (개념) localStorage와 sessionStorage의 차이
- [ ] (개념) HttpOnly·Secure·SameSite·Path의 역할
- [ ] (개념) TTL이 길거나 짧으면 각각 어떤 문제
- [ ] (약점) AT는 XSS 때문에 피했다면서 Guest JWT는 localStorage인 건 모순 아닌가
- [ ] (약점) 90일/15분/14일/5분의 근거
- [ ] (약점) Guest JWT가 탈취되면 피해 범위

### XSS·CSRF·Cookie

포트폴리오 위협 매트릭스 · `auth-evolution.md` §2 · 코드 `SecurityConfig`의 `csrf.disable()`

- [ ] (시작) 설계하며 고려한 보안 위협
- [ ] (꼬리) localStorage에 JWT를 두면 왜 위험한가
- [ ] (꼬리) RT를 cookie에 담으면 CSRF가 생기지 않나
- [ ] (꼬리) CSRF를 무엇으로 막고 있나
- [ ] (꼬리) Strict가 아니라 Lax를 고른 이유
- [ ] (꼬리) Path를 `/api/auth`로 제한하면 뭐가 달라지나
- [ ] (꼬리) Spring Security CSRF 보호를 disable한 근거
- [ ] (개념) XSS와 CSRF의 차이
- [ ] (개념) SOP와 CORS
- [ ] (개념) SameSite Strict·Lax·None이 각각 허용하는 것
- [ ] (약점) AT를 메모리에 두면 XSS로부터 안전한가
- [ ] (약점) CSRF 토큰 없이 SameSite+Path만으로 충분한가
- [ ] (약점) CORS 설정으로 CSRF를 막을 수 있나

### AT·RT 분리와 RTR

`auth-evolution.md` §5

- [ ] (시작) AT와 RT를 분리한 이유, RTR이 뭔지
- [ ] (꼬리) AT 하나를 길게 발급하면 안 되나
- [ ] (꼬리) RTR 없는 일반 RT의 문제
- [ ] (꼬리) refresh 성공 후 기존 RT는 어떻게 되나
- [ ] (꼬리) 로그인 때 기존 RT를 전부 폐기하지 않은 이유
- [ ] (꼬리) 기기별 RT chain이 무슨 뜻인가
- [ ] (개념) AT TTL을 짧게 하는 게 왜 보안에 도움이 되나
- [ ] (개념) login·refresh·logout·reuse의 revoke 정책이 다른 이유
- [ ] (약점) 계속 로그인하면 RT row가 쌓이지 않나 (실측 14개)
- [ ] (약점) 사용자당 세션 개수 제한이 필요하지 않나
- [ ] (약점) 만료 RT 정리 배치가 없는데 어떻게 할 건가
- [ ] (약점) 전체 기기 로그아웃은 어떻게 구현하겠나

### Reuse Detection

`auth-evolution.md` §6

- [ ] (시작) Reuse Detection이 뭐고 왜 넣었나
- [ ] (꼬리) 폐기된 RT가 다시 오는 걸 왜 탈취로 보나
- [ ] (꼬리) 401만 반환하면 안 되나
- [ ] (꼬리) 왜 active RT 전체를 revoke하나
- [ ] (꼬리) 정상 쓰던 다른 기기까지 끊는 게 맞나
- [ ] (꼬리) 만료 RT와 폐기 RT를 다르게 처리하는 이유
- [ ] (꼬리) `isValid()`와 `isStolenReuse()`의 조건
- [ ] (개념) 로그아웃 revoke와 rotation revoke는 의미가 같나
- [ ] (약점) 전부 탈취로 보면 오탐이 나지 않나
- [ ] (약점) 공격자가 정상 사용자보다 먼저 refresh하면
- [ ] (약점) 정상 사용자가 먼저 refresh한 뒤 공격자가 옛 RT를 쓰면
- [ ] (약점) 탐지해도 이미 나간 15분짜리 AT는 살아있지 않나

### 동시 refresh·다중 탭 race

`auth-evolution.md` §8 「RTR — 클라이언트 refresh 동시성 관리 필요」

- [ ] (시작) 같은 RT로 refresh 두 개가 동시에 오면
- [ ] (꼬리) 여러 탭이 동시에 refresh하면
- [ ] (꼬리) A가 rotate한 직후 B가 옛 RT를 쓰면 reuse로 잡히지 않나
- [ ] (꼬리) 그 순간 모든 기기가 로그아웃되나
- [ ] (꼬리) 프론트에서 refresh를 단일화했나
- [ ] (꼬리) 만료 AT로 API 세 개를 동시 호출하면 클라이언트와 서버 동작
- [ ] (꼬리) 401 받은 요청들은 refresh 후 어떻게 재시도되나
- [ ] (개념) 비관적 락·낙관적 락·grace period·멱등 중 어떤 접근이 가능한가
- [ ] (약점) 서버에서도 동시성 제어를 하나
- [ ] (약점) 정상 race와 실제 탈취를 서버가 구별할 수 있나
- [ ] (약점) 오탐을 줄이면서 탐지 강도를 유지할 정책

### RT SHA-256 hash 저장

포트폴리오 위협 매트릭스 · 스키마 `auth_refresh_tokens.token_hash` UNIQUE

- [ ] (시작) RT를 DB에 어떻게 저장했고 왜 원문이 아닌가
- [ ] (꼬리) DB가 털리면 원문 저장은 뭐가 문제인가
- [ ] (꼬리) hash만 있으면 refresh 요청을 어떻게 대조하나
- [ ] (꼬리) `token_hash`에 UNIQUE를 건 이유
- [ ] (개념) Hash와 Encryption의 차이
- [ ] (개념) Salt가 뭐고 왜 쓰나
- [ ] (개념) 비밀번호는 bcrypt인데 랜덤 토큰은 SHA-256이어도 되는 이유
- [ ] (개념) CSPRNG와 token entropy
- [ ] (약점) RT hash에도 salt가 필요하지 않나
- [ ] (약점) SHA-256은 빠른데 brute force에 안 취약한가

### OAuth 2.0·State JWT

`auth-evolution.md` §7 시퀀스 · provider는 kakao 하나

- [ ] (시작) OAuth 로그인 흐름 설명
- [ ] (시작) State를 왜 쓰나
- [ ] (꼬리) Authorization Code는 어디서 어디로 가나
- [ ] (꼬리) Redirect URI를 왜 사전 등록하나
- [ ] (꼬리) Kakao의 AT와 FlowMate의 AT는 어떻게 다른가
- [ ] (꼬리) State가 없으면 가능한 공격
- [ ] (꼬리) 랜덤 문자열 대신 State JWT를 쓴 이유
- [ ] (꼬리) 서명 검증을 하는데 왜 sessionStorage 값과도 비교하나
- [ ] (개념) OAuth 2.0은 인증인가 인가인가
- [ ] (개념) Authorization Code Flow
- [ ] (개념) OAuth와 OIDC의 차이
- [ ] (개념) State와 nonce의 차이
- [ ] (개념) PKCE는 무엇을 막나
- [ ] (약점) State JWT는 stateless라 1회 사용 보장이 안 되는데
- [ ] (약점) Authorization Code가 탈취되면
- [ ] (약점) provider가 늘면 뭘 바꿔야 하나

### revoke-all 롤백 버그

이력서 성과4 · `auth-reuse-detection-rollback.md` §1~3

- [ ] (시작) 트랜잭션 버그 설명
- [ ] (시작) 코드는 revoke했는데 DB엔 왜 안 남았나
- [ ] (꼬리) 어떤 상황에서 발견했나
- [ ] (꼬리) `AuthenticationFailedException`이 준 영향
- [ ] (꼬리) Dirty Checking은 됐는데 왜 DB에 없나
- [ ] (꼬리) Spring 선언적 트랜잭션의 기본 rollback 정책
- [ ] (꼬리) 사용자 입장에서 뭐가 잘못된 동작이었나
- [ ] (개념) `@Transactional`의 동작
- [ ] (개념) Checked와 Unchecked의 rollback 차이
- [ ] (개념) `rollbackFor`는 언제 쓰나
- [ ] (약점) 예외 대신 응답을 직접 반환하면 해결 아닌가
- [ ] (약점) 설계 단계에서 막을 수 있었나
- [ ] (약점) 같은 유형이 다른 실패 경로에도 있지 않나

### REQUIRES_NEW·self-invocation

`auth-reuse-detection-rollback.md` §3~4 · 코드 `RefreshTokenRevoker`

- [ ] (시작) REQUIRES_NEW를 쓴 이유와 별도 Bean으로 뺀 이유
- [ ] (꼬리) REQUIRED와 REQUIRES_NEW의 차이
- [ ] (꼬리) REQUIRES_NEW가 시작되면 기존 트랜잭션은 어떻게 되나
- [ ] (꼬리) 내부는 commit, 외부는 rollback이면 최종 결과
- [ ] (꼬리) self-invocation이 뭐고 왜 `@Transactional`이 안 먹나
- [ ] (개념) Spring AOP와 Proxy
- [ ] (개념) JDK Dynamic Proxy와 CGLIB의 차이
- [ ] (개념) private 메서드에 `@Transactional`을 붙이면
- [ ] (개념) Propagation 종류
- [ ] (약점) 요청 하나인데 일부만 먼저 commit하면 원자성이 깨지는 것 아닌가
- [ ] (약점) REQUIRES_NEW를 남용하면
- [ ] (약점) 커넥션 풀과의 관계 (중첩 시 커넥션 2개 점유)
- [ ] (약점) self-invocation을 피할 다른 방법은 없었나

## 실시간·분산

### SSE+REST 채널 선택

`sse-sync.md` §2

- [ ] (시작) 멀티디바이스 동기화를 어떻게 구현했나
- [ ] (시작) WebSocket이 아니라 SSE인 이유
- [ ] (꼬리) 클라이언트→서버는 왜 REST인가
- [ ] (꼬리) 데이터 흐름의 비대칭이 무슨 뜻인가
- [ ] (꼬리) Polling이 부적합했던 이유
- [ ] (꼬리) STOMP가 과잉이라 본 근거
- [ ] (꼬리) 게스트를 동기화에서 뺀 이유
- [ ] (개념) SSE와 WebSocket의 차이
- [ ] (개념) SSE의 Content-Type과 응답 포맷
- [ ] (개념) `EventSource`의 자동 재연결
- [ ] (약점) 연결이 수만 개면 뭐가 먼저 병목인가
- [ ] (약점) WebSocket으로 갈아탈 기준
- [ ] (약점) HTTP/1.1 브라우저 동시 연결 제한은 문제 없나

### version 단조 증가와 soft delete

`sse-sync.md` §4.2~4.3 · `data-model.md` §4-3

- [ ] (시작) 이벤트 순서가 뒤바뀌는 문제를 어떻게 풀었나
- [ ] (꼬리) version은 어떻게 계산하나
- [ ] (꼬리) `currentTimeMillis()`만 쓰면 뭐가 깨지나
- [ ] (꼬리) `lastVersion+1`만 쓰면 뭘 잃나
- [ ] (꼬리) 클라이언트는 version을 어떤 단위로 갖고 있나
- [ ] (꼬리) 정지 시 행을 지우지 않고 `state_json`을 NULL로 두는 이유
- [ ] (꼬리) idle에 행을 지우면 어떤 오판이 생기나
- [ ] (개념) 단조 증가 보장이 왜 필요한가
- [ ] (개념) 논리 삭제와 물리 삭제의 트레이드오프
- [ ] (약점) version이 서버 시계에 의존하는데 서버가 여러 대면
- [ ] (약점) idle row는 어떻게 정리할 건가
- [ ] (약점) JPA `@Version` 낙관적 락으로 안 한 이유

### Redis Pub/Sub 수평 확장

`redis-sse-pubsub.md` §1~3

- [ ] (시작) SSE를 Redis Pub/Sub으로 확장한 이유
- [ ] (꼬리) 인스턴스 2대면 정확히 뭐가 안 되나
- [ ] (꼬리) registry가 메모리에 있으면 왜 공유가 안 되나
- [ ] (꼬리) Sticky Session으로 해결하면 안 됐나
- [ ] (꼬리) A의 변경이 B의 기기까지 가는 경로
- [ ] (꼬리) Kafka·Redis Streams 대신 Pub/Sub인 이유
- [ ] (개념) Message Queue와 Pub/Sub의 차이
- [ ] (개념) Fan-out
- [ ] (개념) Redis Pub/Sub은 메시지를 저장하나
- [ ] (개념) at-most-once / at-least-once / exactly-once
- [ ] (약점) 서버가 10대·100대여도 유지되나
- [ ] (약점) Redis가 SPOF 아닌가
- [ ] (약점) 모든 인스턴스가 모든 이벤트를 받는 건 낭비 아닌가

### 유실 복구와 구독·snapshot 순서

`redis-sse-pubsub.md` §6 · `sse-sync.md` §4 앱 초기화 `GET /api/timer/state`

- [ ] (시작) publish가 유실돼 push가 안 가면 동기화는 어떻게 되나
- [ ] (시작) 언제 앱 초기화나 스냅샷을 조회하나
- [ ] (꼬리) 구독과 snapshot 중 뭘 먼저 하나
- [ ] (꼬리) 구독 먼저면 / snapshot 먼저면 각각의 문제
- [ ] (꼬리) 그 사이에 온 이벤트는 어떻게 되나
- [ ] (꼬리) 유실을 클라이언트가 알 수 있나
- [ ] (꼬리) 유실을 감수해도 되는 근거
- [ ] (개념) Last-Event-ID를 쓰면 뭐가 달라지나
- [ ] (개념) Eventual Consistency
- [ ] (약점) 화면을 계속 보고 있으면 재조회 트리거가 없는데
- [ ] (약점) 주기적 폴링을 안전망으로 안 둔 이유
- [ ] (약점) 유실률을 실제로 재본 적 있나

### AFTER_COMMIT publish

`redis-sse-pubsub.md` §3 핵심 설계 결정

- [ ] (시작) 왜 AFTER_COMMIT에 발행하나
- [ ] (꼬리) 커밋 전에 발행하면 생기는 문제 (유령 이벤트)
- [ ] (꼬리) publish는 됐는데 DB가 롤백되면
- [ ] (꼬리) commit 후 publish가 실패하면
- [ ] (꼬리) 그 실패가 PUT 응답에 영향을 주나
- [ ] (꼬리) `TimerService`가 도메인 이벤트만 발행하게 바꾼 이유
- [ ] (개념) `@TransactionalEventListener`
- [ ] (개념) BEFORE_COMMIT·AFTER_COMMIT·AFTER_ROLLBACK
- [ ] (개념) Dual Write 문제
- [ ] (개념) Transactional Outbox는 뭘 해결하나
- [ ] (약점) AFTER_COMMIT도 원자성 보장은 아니지 않나
- [ ] (약점) Outbox까지 안 간 이유
- [ ] (약점) 어느 규모부터 Outbox나 CDC로 가야 하나

## DB·동시성

### Deadlock 69→0

`timer-deadlock.md` 전체

- [ ] (시작) 데드락 케이스를 간단히 설명
- [ ] (꼬리) 전체 실패율 0.06%인데 어떻게 문제로 봤나
- [ ] (꼬리) 처음에 `PESSIMISTIC_WRITE`를 건 이유
- [ ] (꼬리) row가 있을 때와 없을 때 `SELECT FOR UPDATE`가 다른 이유
- [ ] (꼬리) gap lock과 insert intention lock이 만든 순환 대기
- [ ] (꼬리) `SHOW ENGINE INNODB STATUS`에서 확인한 것
- [ ] (꼬리) 검토한 세 대안과 `@Lock` 제거를 고른 근거
- [ ] (꼬리) lock을 없애면 동시 INSERT가 나는데 그건
- [ ] (꼬리) catch에서 winner를 재조회하고 version을 다시 계산한 이유
- [ ] (꼬리) 재계산 안 하면 무슨 일이 생기나
- [ ] (약점) native upsert·retry·낙관적 락·Redis 락 대신 이걸 고른 근거
- [ ] (약점) 요청이 46% 는 상태의 비교가 왜 유효한가
- [ ] (약점) p95가 좋아진 이유
- [ ] (약점) catch-retry도 1회뿐인데 또 충돌하면
- [ ] (약점) H2로는 재현이 안 되는데 회귀는 어떻게 막나

### InnoDB 락과 gap lock

`timer-deadlock.md` §2 deadlock graph

- [ ] (시작) 갭을 잠근다는 게 구체적으로 어떤 상황인가
- [ ] (시작) 갭을 잠그는 범위는 어디까지인가
- [ ] (시작) MySQL은 왜 갭 락을 거나
- [ ] (꼬리) 팬텀 리드가 뭐고 gap lock이 어떻게 막나
- [ ] (꼬리) PRIMARY 인덱스에서 gap은 어떻게 정해지나
- [ ] (개념) Record·Gap·Next-Key·Insert Intention Lock
- [ ] (개념) Deadlock과 Lock Wait Timeout의 차이
- [ ] (개념) InnoDB 기본 격리 수준
- [ ] (개념) RR과 RC의 락 동작 차이
- [ ] (개념) MVCC와 락의 관계
- [ ] (약점) RC로 낮추면 사라지나. 그럼 뭘 잃나
- [ ] (약점) InnoDB는 victim을 어떻게 고르나
- [ ] (약점) 유니크 인덱스 조회에도 gap lock이 걸리나

### revoke-all 복합 인덱스

`auth-reuse-detection-rollback.md` §5.2 · Flyway V7 `idx_refresh_tokens_active_by_user` · 로컬 MySQL 8.0.46, 합성 10만건

- [ ] (시작) revoke-all 쿼리 성능을 어떻게 확인하고 고쳤나
- [ ] (꼬리) 기존엔 어떤 인덱스만 있었고 왜 부족했나
- [ ] (꼬리) `(user_id, revoked_at, expires_at)` 순서의 근거
- [ ] (꼬리) 동등 조건을 앞, 범위 조건을 뒤에 두는 이유
- [ ] (꼬리) UPDATE가 아니라 SELECT로 측정한 이유
- [ ] (꼬리) 읽은 행이 10만→1,000으로 준 게 무슨 의미인가
- [ ] (개념) 인덱스가 왜 빠른가 (B+Tree)
- [ ] (개념) Covering Index
- [ ] (개념) leftmost prefix 규칙
- [ ] (개념) 카디널리티가 인덱스 효율에 주는 영향
- [ ] (개념) EXPLAIN과 EXPLAIN ANALYZE의 차이
- [ ] (개념) secondary index에 PK가 포함된다는 뜻 (clustered index)
- [ ] (약점) 137ms→0.929ms를 운영 응답시간으로 말해도 되나
- [ ] (약점) 인덱스를 추가하며 감수한 비용
- [ ] (약점) `revoked_at`은 NULL 비교인데 인덱스가 타나
- [ ] (약점) 실제 토큰 분포가 합성과 다르면
