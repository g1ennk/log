# S 핵심 확장

서류를 제대로 읽은 면접관이면 높은 확률로 이어 묻는 질문.

## 인증·보안

### Spring Security 적용 범위 ⚠

코드 `SecurityConfig`(`SecurityFilterChain`, `STATELESS`, `csrf.disable()`, `hasRole("MEMBER")`),
`JwtAuthFilter` · ⚠ 쓰긴 쓰되 OAuth는 `auth-evolution.md` §4대로 **OAuth2 Client 없이 수동 구현**

- [ ] (시작) Spring Security를 어떻게 쓰고 있나
- [ ] (시작) JWT 요청이 오면 내부에서 어떤 순서로 처리되나
- [ ] (꼬리) `SecurityFilterChain`을 어떻게 구성했나
- [ ] (꼬리) JWT 검증을 왜 `UsernamePasswordAuthenticationFilter` 앞에 뒀나
- [ ] (꼬리) `SecurityContext`와 `Authentication`
- [ ] (꼬리) 인증 정보를 Service에서 어떻게 꺼내나
- [ ] (꼬리) `STATELESS`는 뭘 끄는 설정인가
- [ ] (꼬리) 게스트와 회원 권한을 어떻게 갈랐나 (`role` claim → `hasRole`)
- [ ] (꼬리) `UserDetailsService`를 항상 실패하게 둔 이유
- [ ] (개념) 401과 403의 차이
- [ ] (개념) `AuthenticationEntryPoint`와 `AccessDeniedHandler`
- [ ] (개념) Filter와 Interceptor의 차이
- [ ] (개념) JWT를 Filter에서 처리하는 이유
- [ ] (약점) Security를 쓰면서 OAuth2 Client는 왜 안 썼나 ⚠
- [ ] (약점) 수동 구현으로 얻은 것과 잃은 것
- [ ] (약점) 언제 OAuth2 Client로 옮길 건가

### 로그아웃·게스트 전환

`auth-evolution.md` §6 revoke 정책, §7 「logout → cookie 삭제 → 게스트 전환」

- [ ] (시작) 로그아웃을 어떻게 구현했나
- [ ] (시작) JWT 환경에서 로그아웃이 정확히 뭘 하는 건가
- [ ] (꼬리) 로그아웃하면 어떤 RT가 revoke되나
- [ ] (꼬리) RT만 revoke하면 AT는 15분 살아있지 않나
- [ ] (꼬리) 로그아웃 후 게스트로 전환되나
- [ ] (꼬리) silent refresh 실패 시 왜 게스트로 폴백하지 않나
- [ ] (개념) AT를 즉시 무효화하는 방법
- [ ] (개념) Blacklist의 장단점
- [ ] (개념) JWT에서 강제 로그아웃이 어려운 이유
- [ ] (약점) 세션이었으면 더 단순하지 않았나
- [ ] (약점) Stateless의 이점이 실제로 어디에 나타났나
- [ ] (약점) 이유 없이 로그아웃됐다는 신고가 오면 뭘 확인하나

### SSE 쿼리 파라미터 인증

`sse-sync.md` §4.5 · `SecurityConfig`에서 `/api/timer/sse`는 `permitAll`, 컨트롤러가 직접 검증 · 기각한 대안: 단기 티켓

- [ ] (시작) SSE는 어떻게 인증하나
- [ ] (꼬리) 왜 Authorization 헤더를 못 쓰나
- [ ] (꼬리) 쿼리 파라미터로 토큰을 넘기면 뭐가 위험한가
- [ ] (꼬리) 그 위험을 뭘로 완화했나
- [ ] (꼬리) 단기 티켓을 기각한 이유
- [ ] (꼬리) `permitAll`인데 인증은 어디서 하나
- [ ] (꼬리) 게스트가 SSE에 붙으면
- [ ] (개념) 토큰이 URL에 실리면 어디에 남나
- [ ] (개념) 인증을 필터 체인 밖에서 하면 뭘 포기하나
- [ ] (약점) AT가 15분마다 갱신되면 연결도 매번 끊나
- [ ] (약점) 연결 중 토큰이 만료되면
- [ ] (약점) 다시 만들면 티켓 방식으로 갈 건가

## 실시간·분산

### SSE 3중 타임아웃

`sse-timeout.md` §2~3

- [ ] (시작) SSE가 60초에 끊긴 원인을 HTTP 연결 관점에서 설명
- [ ] (꼬리) 처음에 왜 CORS로 봤고 어떻게 아니라고 확인했나
- [ ] (꼬리) 서비스워커가 SSE를 왜 처리 못 했나
- [ ] (꼬리) `Bypass for network`로 확인한 것
- [ ] (꼬리) 최초 응답이 200 OK였다는 데서 뭘 좁혔나
- [ ] (꼬리) SSE 전용 location을 분리한 이유
- [ ] (꼬리) `proxy_buffering off`와 `X-Accel-Buffering`이 막는 것
- [ ] (꼬리) Spring async timeout을 1시간으로 맞춘 이유
- [ ] (꼬리) 세 계층 타임아웃이 어긋나면
- [ ] (꼬리) `AsyncRequestTimeoutException`과 `AsyncRequestNotUsableException`을 다르게 매핑한 이유
- [ ] (개념) Idle Timeout과 Request Timeout의 차이
- [ ] (개념) 504는 언제 나나
- [ ] (개념) Keep-Alive와 SSE 연결 유지의 차이
- [ ] (약점) async timeout은 총 시간 기준이라 heartbeat로 못 막는데 1시간 뒤엔
- [ ] (약점) 도입 전에 예상할 수 있었나
- [ ] (약점) 중간망은 여전히 통제 밖 아닌가

### heartbeat·fire-and-forget·self-echo

`sse-sync.md` §4.4 · `redis-sse-pubsub.md` §3~4 `SseLocalDispatcher` → `sseDispatchExecutor`

- [ ] (시작) 한 사용자의 여러 기기·탭 emitter를 어떻게 관리하나
- [ ] (꼬리) `ConcurrentHashMap` + `CopyOnWriteArrayList`인 이유
- [ ] (꼬리) completion·timeout·error를 같은 정리 로직으로 묶은 이유
- [ ] (꼬리) heartbeat 25초의 근거
- [ ] (꼬리) 인프라 타임아웃이 1시간인데 왜 heartbeat가 필요한가
- [ ] (꼬리) broadcast에서 전송 실패를 삼키는 이유
- [ ] (꼬리) 삼켜도 데이터가 안 깨지는 근거
- [ ] (꼬리) Redis 구독 스레드에서 바로 안 보내고 executor에 넘긴 이유
- [ ] (꼬리) 발신자에게도 자기 이벤트를 보내는 이유
- [ ] (개념) `connected`·`heartbeat`·`timer-state` 각각의 계약
- [ ] (개념) slow consumer 문제
- [ ] (약점) executor 큐가 포화되면
- [ ] (약점) 죽은 연결은 정리 전까지 얼마나 남나
- [ ] (약점) heartbeat 스케줄러 스레드가 연결 수만큼 늘지 않나

## DB·동시성

### MySQL 정본·LWW

`sse-sync.md` §6 · `redis-sse-pubsub.md` §2

- [ ] (시작) Redis로 이벤트를 보내면서 왜 MySQL이 정본인가
- [ ] (꼬리) Redis를 정본으로 쓰면 안 되나
- [ ] (꼬리) 이벤트에 state가 들어있는데도 정본이 MySQL인 이유
- [ ] (꼬리) 다른 인스턴스에서 거의 동시에 바뀌면 최종 상태는 뭘로 결정되나
- [ ] (개념) Source of Truth
- [ ] (개념) Last-Writer-Wins가 맞는 데이터는 어떤 것
- [ ] (개념) Eventual과 Strong Consistency의 차이
- [ ] (개념) 멱등성과 version 검증의 차이
- [ ] (약점) LWW를 택한 건 어떤 시나리오를 포기한 건가
- [ ] (약점) 지금 구조를 Strong Consistency라 할 수 있나
- [ ] (약점) 결제 도메인이었으면 뭘 바꿨겠나

### 데이터 모델 ⚠

`data-model.md` §2·§4-1 · ⚠ 코드 확인 — `issueGuestToken()`이 매번 새 UUID를 발급하고 게스트 `sub`=clientId / 회원 `sub`=`users.id`라 *
*게스트→회원 승계 로직이 없음**

- [ ] (시작) 데이터 모델 설명
- [ ] (시작) Guest와 Member를 어떻게 모델링했나
- [ ] (꼬리) 핵심 테이블에 `users` FK를 안 둔 이유
- [ ] (꼬리) 게스트 전용 테이블 분리를 기각한 이유
- [ ] (꼬리) Todo는 통합인데 Timer는 왜 회원 전용인가
- [ ] (꼬리) `auth_social_accounts`와 `auth_refresh_tokens`만 FK를 건 이유
- [ ] (꼬리) 게스트가 로그인하면 기존 게스트 Todo는 어떻게 되나 ⚠
- [ ] (개념) Natural Key와 Surrogate Key
- [ ] (개념) FK를 쓸 때와 안 쓸 때의 차이
- [ ] (개념) DB 무결성과 애플리케이션 무결성
- [ ] (약점) 게스트 데이터가 로그인 후 사라지는 건 제품 문제 아닌가 ⚠
- [ ] (약점) 승계를 구현한다면 어떤 설계로 갈 건가
- [ ] (약점) 이미 회원 데이터가 있으면 어떻게 병합하나
- [ ] (약점) FK가 없어 참조 무결성이 약한 건 어떻게 보완하나
- [ ] (약점) 같은 이메일의 다른 provider 계정은 같은 회원인가

## 테스트·검증

### 테스트 전략

`auth-reuse-detection-rollback.md` §2·§5.1 `AuthServiceIT` Red/Green · ⚠ 서류는 186개, 현재 `@Test` 193개

- [ ] (시작) 단위 테스트와 통합 테스트를 어떤 기준으로 나누나
- [ ] (시작) 186개를 어떤 기준으로 뭘 테스트했나
- [ ] (꼬리) Reuse Detection 버그를 단위 테스트가 못 잡은 이유
- [ ] (꼬리) mock repository로는 왜 롤백을 못 보나
- [ ] (꼬리) `revoke()` 호출 검증과 commit 검증의 차이
- [ ] (꼬리) `AuthServiceIT`는 뭘 띄워서 뭘 조회하나
- [ ] (꼬리) 다른 사용자 RT·정상 refresh·만료 RT까지 검증한 이유
- [ ] (꼬리) REQUIRES_NEW를 일부러 빼서 Red를 본 이유
- [ ] (개념) Test Pyramid
- [ ] (개념) State Verification과 Behavior Verification
- [ ] (개념) Mocking의 한계
- [ ] (약점) 개수가 많으면 좋은 테스트인가
- [ ] (약점) 지금 테스트가 못 잡는 버그는 어떤 종류인가
- [ ] (약점) 유지보수 비용이 커지면 뭘 기준으로 정리하나
- [ ] (약점) 서류는 186인데 지금 세면 다른데 이 숫자는 뭐 기준인가 ⚠

### H2와 Testcontainers 병용

`build.gradle` — H2(testRuntimeOnly), testcontainers-redis 2.2.2 · 사용처 `RedisConfigTest`, `SseBroadcasterTest`,
`MultiInstanceSseIntegrationTest`

- [ ] (시작) 통합 테스트 환경을 어떻게 구성했나
- [ ] (꼬리) DB는 H2인데 Redis는 왜 Testcontainers인가
- [ ] (꼬리) Redis를 mock으로 대체하지 않은 이유
- [ ] (꼬리) DB도 Testcontainers로 통일하지 않은 이유
- [ ] (개념) H2와 MySQL의 동작 차이는 어디서 나나
- [ ] (개념) Testcontainers의 동작 방식
- [ ] (약점) H2로는 gap lock을 못 만드는데 deadlock 회귀는 어떻게 막나
- [ ] (약점) H2에서 통과한 쿼리가 MySQL에서 깨진 적 있나
- [ ] (약점) CI 실행 시간은 감수할 만한가

## 인프라·운영

### NE:MO 인프라 3단계

포트폴리오 3단계 요약표 · NE:MO 위키 `Big Bang`·`Docker 컨테이너화`·`Kubeadm‐Dev`

- [ ] (시작) 인프라 3단계 진화 설명
- [ ] (시작) NE:MO에서 맡은 역할과 쓴 Cloud Platform
- [ ] (꼬리) 처음부터 Kubernetes로 안 간 이유
- [ ] (꼬리) v1에서 단일 VM을 고른 근거
- [ ] (꼬리) VM 사양은 뭐였고 용량은 어떤 근거로 판단했나
- [ ] (꼬리) 배포에 20~25분 걸린 건 어떤 작업들 때문인가
- [ ] (꼬리) 롤백이 불가능했다는 게 무슨 뜻인가
- [ ] (꼬리) v2에서 Docker·3-Tier·MIG를 넣은 이유
- [ ] (꼬리) 환경 불일치를 어떻게 겪었나 (uvicorn 버전 충돌)
- [ ] (꼬리) MIG가 있는데 v3로 간 이유
- [ ] (개념) Big Bang과 Rolling 배포의 차이
- [ ] (개념) VM 단위 스케일링과 Pod 단위 스케줄링
- [ ] (개념) Self-healing, SPOF
- [ ] (약점) 다시 만들면 처음부터 Kubernetes로 갈 건가
- [ ] (약점) Kubernetes가 과한 상황은 언제인가
- [ ] (약점) VM→Docker→K8s를 고르는 기준을 한 문장으로

### Kubeadm vs EKS와 control plane

포트폴리오 v3 대안 표 · `Kubeadm‐Dev` §2.1 · Master 1 + Worker 2, prod는 팀원이 EKS

- [ ] (시작) 매니지드 대신 Kubeadm을 고른 이유
- [ ] (꼬리) Kubeadm과 EKS/GKE의 차이
- [ ] (꼬리) 비용과 학습 말고 다른 판단 요소는 없었나
- [ ] (꼬리) Master 1 + Worker 2의 문제
- [ ] (꼬리) Master가 죽으면
- [ ] (꼬리) control plane이 죽어도 기존 Pod는 도나
- [ ] (꼬리) 그동안 Pod가 죽으면 누가 복구하나
- [ ] (개념) API Server·etcd·Scheduler·Controller Manager의 역할
- [ ] (개념) kubelet과 kube-proxy
- [ ] (개념) etcd가 손상되면 뭘 잃나
- [ ] (약점) 실제 Production에서도 Kubeadm을 고르겠나
- [ ] (약점) dev는 Kubeadm, prod는 EKS인 parity 손해를 어떻게 정당화하나
- [ ] (약점) 직접 구성해서 실제로 알게 된 게 뭔가

### K8s 네트워크·Ingress·Namespace

포트폴리오 v3 · `Kubeadm‐Dev` §2.6·3-5·3-7

- [ ] (시작) 외부 요청이 Pod까지 가는 경로
- [ ] (꼬리) NLB와 NGINX Ingress Controller의 역할
- [ ] (꼬리) Ingress와 Service의 차이
- [ ] (꼬리) ClusterIP·NodePort·LoadBalancer는 각각 언제
- [ ] (꼬리) Calico를 고른 이유
- [ ] (꼬리) Namespace 간 통신은 어떻게 되나
- [ ] (꼬리) Bastion Host를 둔 이유
- [ ] (개념) CNI가 왜 필요한가
- [ ] (개념) 클러스터 내부 DNS
- [ ] (개념) Readiness와 Liveness Probe의 차이
- [ ] (개념) NetworkPolicy
- [ ] (약점) Ingress Controller가 죽으면
- [ ] (약점) Namespace만으로 실제 격리가 되나

### 외부 노출 8→2

NE:MO 위키 `.env 스캔 대응` 전체 · 2025-05-15 19:40 Netdata Discord 알림, Critical 2분 20초

- [ ] (시작) .env 스캔 대응 과정 설명
- [ ] (꼬리) 공격을 어떻게 발견했나
- [ ] (꼬리) Netdata는 뭘 근거로 Critical을 띄웠나
- [ ] (꼬리) 실제 침해였나 자동 스캔이었나
- [ ] (꼬리) 유출이 없었는데 왜 구조를 바꿨나
- [ ] (꼬리) Nginx 경로 차단만으로 왜 부족했나
- [ ] (꼬리) 열려 있던 8개 포트는 각각 뭐였나
- [ ] (꼬리) private subnet으로 옮기면 배포와 운영 접근은 어떻게 하나
- [ ] (꼬리) outbound는 어떻게 했나 (Cloud NAT)
- [ ] (꼬리) WireGuard를 OpenVPN 대신 고른 이유와 감수한 비용
- [ ] (개념) Attack Surface
- [ ] (개념) VPN과 방화벽의 역할 차이
- [ ] (약점) WAF나 방화벽 강화만으로는 안 됐나
- [ ] (약점) VPN 서버가 새 SPOF이자 공격 표면 아닌가
- [ ] (약점) 팀원별 키 배포·회수는 어떻게 관리했나
