# S+ 대표 성과 방어

이력서 주요 성과 자체를 검증하는 질문 모음.

## 인증·보안

### 인증 구조 4단계

`auth-evolution.md` §1·3~6

- [ ] (시작) 인증 구조 전체 설명
- [ ] (시작) 4단계 각각 무엇이 부족해서 다음으로 넘어갔나
- [ ] (꼬리) 처음부터 지금 구조로 안 간 이유
- [ ] (꼬리) X-Client-Id는 어떤 방식이었나
- [ ] (꼬리) 브라우저가 보낸 UUID를 왜 인증으로 못 보나
- [ ] (꼬리) 백엔드는 X-Client-Id의 무엇을 검증했나
- [ ] (꼬리) Guest와 Member 인증을 왜 분리했나
- [ ] (꼬리) 게스트에게도 JWT가 필요했던 이유. UUID를 계속 쓰면 안 됐나
- [ ] (개념) 인증과 인가의 차이
- [ ] (개념) 인증과 식별의 차이
- [ ] (개념) 세션 인증과 토큰 인증의 차이
- [ ] (개념) JWT 인증의 장단점
- [ ] (약점) RT를 DB에 두는데 이걸 Stateless라 할 수 있나
- [ ] (약점) 다시 설계하면 세션과 JWT 중 무엇
- [ ] (약점) 4단계를 거치며 남은 부채

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
