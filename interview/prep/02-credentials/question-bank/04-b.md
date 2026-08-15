# B 기본기 확장

서류에 적은 기술을 축으로 넓히거나, 운영 규모가 커졌을 때를 가정한 질문. 개념 정의와 판단 기준만 잡아둔다.

## 인증·보안

### 장애·극단 상황·키 회전

구현으로 적힌 기능이 아니라 인증·분산 성과에서 파생되는 가정 질문. NE:MO 위키 §2 위협 모델이 출발점 · ⚠ 「현재 구현은 X, 규모가 커지면 Y를 고려」 형태로 답할 것

- [ ] (시작) JWT Secret이 유출되면 어떤 순서로 대응하나
- [ ] (꼬리) 기존 RT revoke 후 새 RT 발급 전에 서버가 죽으면
- [ ] (꼬리) RT는 저장됐는데 응답 전달이 실패하면
- [ ] (꼬리) cookie 설정이 실패하면
- [ ] (꼬리) Redis publish가 실패하면 사용자 요청은 어떻게 되나
- [ ] (꼬리) OAuth Client Secret이 유출되면
- [ ] (개념) Key Rotation
- [ ] (개념) 서명 키를 무중단으로 교체하려면 (kid, 병행 검증)
- [ ] (개념) 전체 사용자를 강제 로그아웃시키려면
- [ ] (약점) 보안 일관성과 사용자 경험이 부딪히면 뭘 기준으로 판단하나
- [ ] (약점) 지금 구조에서 키를 교체하면 사용자에게 무슨 일이 생기나

## DB·동시성

### MySQL·JPA 기본기

이력서 Skills · 실제로 부딪힌 지점 — Dirty Checking과 트랜잭션 경계, `saveAndFlush` + `DataIntegrityViolationException`,
`@Enumerated(STRING)` · ⚠ 개념만 묻는 버전은 [01-cs](../../01-cs)에 있음

- [ ] (시작) JPA를 쓴 이유와 ORM의 장단점
- [ ] (꼬리) 영속성 컨텍스트를 FlowMate 어디서 체감했나
- [ ] (꼬리) Dirty Checking은 됐는데 DB에 안 남았던 사례
- [ ] (꼬리) flush와 commit의 차이
- [ ] (꼬리) `save`와 `saveAndFlush`. 왜 후자를 썼나
- [ ] (꼬리) `saveAndFlush`가 아니었으면 예외가 어디서 터졌을까
- [ ] (꼬리) Enum을 STRING으로 저장한 이유
- [ ] (개념) Lazy와 Eager
- [ ] (개념) N+1과 해결법
- [ ] (개념) 1차 캐시의 역할
- [ ] (개념) OSIV를 끄면
- [ ] (약점) JPA가 만든 SQL을 못 믿는 상황
- [ ] (약점) Native Query를 골라야 하는 기준
- [ ] (약점) FlowMate 규모에서 N+1을 실제로 만난 적 있나

### Redis 기본기

이력서 Skills · `architecture.md` §1 「Redis는 SSE 이벤트 전파 Pub/Sub으로만」

- [ ] (시작) Redis를 어떤 목적으로 쓰나
- [ ] (꼬리) 캐시로는 왜 안 쓰나. 넣는다면 어디부터
- [ ] (개념) 단일 스레드인데 Redis가 빠른 이유
- [ ] (개념) 메모리에만 저장되나 (RDB·AOF)
- [ ] (개념) Pub/Sub과 Streams의 차이
- [ ] (개념) Sentinel과 Cluster
- [ ] (개념) Cache Aside, Cache Stampede
- [ ] (약점) 메시지 보장·재처리가 필요해지면 뭘로 옮기나
- [ ] (약점) Redis 장애 시 서비스는 어디까지 도나
- [ ] (약점) 메모리가 가득 차면

## 인프라·운영

### 네트워크 기본기

NE:MO의 private subnet·Cloud NAT·WireGuard·NLB 구성에서 파생 · ⚠ TCP/UDP·핸드셰이크·OSI는 [01-cs/network](../../01-cs/network)에 있음

- [ ] (시작) Public과 Private Subnet의 차이
- [ ] (꼬리) NAT Gateway와 Internet Gateway
- [ ] (꼬리) private subnet 서버가 외부로 나가려면
- [ ] (꼬리) VPN의 동작 원리
- [ ] (꼬리) WireGuard와 OpenVPN의 차이
- [ ] (개념) L4와 L7 로드밸런서. NLB와 ALB는 각각 언제 쓰나
- [ ] (개념) Reverse Proxy와 Forward Proxy
- [ ] (개념) TLS는 어느 계층에서 뭘 보장하나
- [ ] (개념) TLS 종료 지점에 따라 뭐가 달라지나
- [ ] (개념) Connection Timeout과 Read Timeout
- [ ] (약점) 트래픽 급증 시 네트워크·앱·DB 병목을 어떤 순서로 보나

### Secret 관리

NE:MO 위키 §6 「VM `.env` 평문 → GCP Secret Manager, 배포 시점에만 fetch」 · §8 트레이드오프

- [ ] (시작) Secret을 평문으로 두면 왜 위험한가
- [ ] (꼬리) Secret Manager로 옮기고 실제로 뭐가 편해졌나
- [ ] (꼬리) 배포 시점에만 fetch한다는 흐름
- [ ] (꼬리) 그래도 컨테이너 환경변수엔 평문으로 들어가는데
- [ ] (꼬리) 이미 Git history에 올라갔다면
- [ ] (개념) Secret Rotation
- [ ] (개념) Access Key 대신 IAM Role을 쓰면
- [ ] (개념) Kubernetes Secret은 기본 암호화되어 있나
- [ ] (약점) Secret Manager 접근 권한 자체는 어떻게 보호하나
- [ ] (약점) 대규모 조직이면 환경별·서비스별로 어떻게 나누나

### NE:MO Kafka·HPA

`Kubeadm‐Dev` §3-7(Spring Producer → Broker+Zookeeper → Consumer → 알림톡), §4-1(Metrics Server + HPA, replica 3) · ⚠ 서류엔 없고 위키에만 있음

- [ ] (시작) Kafka는 어디에 썼나
- [ ] (꼬리) 알림톡에 왜 브로커가 필요했나
- [ ] (꼬리) Producer와 Consumer는 각각 뭐였나
- [ ] (꼬리) Zookeeper의 역할
- [ ] (꼬리) 클러스터 안에 띄웠다면 상태 저장은 (StatefulSet)
- [ ] (꼬리) NE:MO는 Kafka인데 FlowMate는 왜 Redis Pub/Sub인가
- [ ] (꼬리) HPA는 어떤 지표로 도나
- [ ] (꼬리) Metrics Server가 없으면 어떻게 되나
- [ ] (개념) Deployment와 StatefulSet의 차이
- [ ] (개념) Kafka 파티션과 컨슈머 그룹
- [ ] (약점) Kafka 구성은 어디까지 직접 했나
- [ ] (약점) 알림 전송이 실패하면 재처리는 어떻게 되나
- [ ] (약점) 클러스터 안에서 상태 저장 서비스를 운영하는 게 맞나

## 프로필

### AI 활용과 검증

이력서 Summary · 테스트 186개, 스펙 20건, 플랜 32건, 위키 7편 · ⚠ 「AI가 더 잘한다」가 아니라 판단·검증·책임은 본인이라는 프레임으로

- [ ] (시작) 개발하며 AI를 어떻게 쓰나
- [ ] (시작) AI가 준 선택지를 검증하는 기준
- [ ] (꼬리) AI가 쓴 코드를 어떤 절차로 검증하나
- [ ] (꼬리) 스펙과 플랜을 따로 남긴 이유
- [ ] (꼬리) 위키 7편은 왜 썼나
- [ ] (꼬리) AI가 잘못 만든 코드를 발견한 사례
- [ ] (꼬리) 문제가 안 풀릴 때 접근 방식
- [ ] (꼬리) 기술 선택에서 가장 중요하게 보는 조건
- [ ] (개념) 뭘 AI에 맡기고 뭘 직접 하나
- [ ] (개념) 보안·동시성 코드에선 뭘 더 보나
- [ ] (약점) AI가 실력을 떨어뜨린다는 우려에 대해
- [ ] (약점) AI가 만든 코드의 책임은 누구에게 있나
- [ ] (약점) AI 없이 같은 결과를 낼 수 있나

### 협업 구조화

이력서 「작업 160여 건, GitHub Project 칸반」 · 4개월 8스프린트, 스크럼 09:10 / 회고 17:50 · FE 1·BE 1·AI 2·Cloud 2

- [ ] (시작) 4개 파트 협업을 어떻게 관리했나
- [ ] (꼬리) 어떤 문제가 있어서 칸반을 만들었나
- [ ] (꼬리) 파트·스프린트·사이즈 라벨의 기준
- [ ] (꼬리) 스크럼과 회고에서 뭘 공유했나
- [ ] (꼬리) Cloud와 Backend가 충돌한 사례
- [ ] (꼬리) 짝꿍과 역할을 어떻게 나눴나 (dev / prod)
- [ ] (개념) 협업에서 문서화가 중요한 이유
- [ ] (개념) 본인 의견이 안 받아들여지면
- [ ] (약점) 프로세스가 속도를 늦추는 상황은 어떻게 피하나
- [ ] (약점) 이 루틴 중 효과 있던 것과 형식적이던 것
