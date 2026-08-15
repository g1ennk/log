# A 심층 확인·운영

서류를 깊게 읽거나 관련 기술을 파고들 때 나오는 질문

## 실시간·분산

### Gemini AI 리포트

포트폴리오 주요 기능 · `data-model.md` Report — `content JSON`(keep/problem/try/referenceQuestion), `prompt_version`,
`(user_id,type,period_start)` UNIQUE, 회원 전용 · 코드 `/api/ai/report/**` → `hasRole("MEMBER")`

- [ ] (시작) AI 회고 리포트는 어떻게 동작하나
- [ ] (꼬리) 어떤 데이터를 모아 프롬프트를 만드나
- [ ] (꼬리) 4개 키가 항상 존재하게 한 이유
- [ ] (꼬리) `referenceQuestion`만 nullable인 이유
- [ ] (꼬리) `prompt_version`을 둔 이유
- [ ] (꼬리) 재생성하면 기존 row는 어떻게 되나
- [ ] (꼬리) 게스트를 막은 이유
- [ ] (꼬리) `reviews`와 `reports`를 왜 다른 테이블로 나눴나
- [ ] (개념) LLM 응답이 스키마를 안 지키면
- [ ] (개념) 외부 API 호출을 동기로 할지 비동기로 할지의 기준
- [ ] (약점) Gemini가 느리거나 실패하면 사용자에게 어떻게 보이나
- [ ] (약점) 타임아웃·재시도 정책
- [ ] (약점) 비용과 호출량은 어떻게 통제하나
- [ ] (약점) 프롬프트를 바꾸면 과거 리포트와 형식이 달라지는데

## DB·동시성

### 멱등 키와 역정규화

`data-model.md` TodoSession — `client_session_id`, `(todo_id, client_session_id)` UNIQUE, 재요청 시
`break_seconds`만 증가 갱신 · §4-2 Todo 집계 역정규화

- [ ] (시작) 집중 세션 기록은 어떻게 저장하나
- [ ] (꼬리) `client_session_id`는 누가 만드나
- [ ] (꼬리) 클라이언트가 만든 ID를 왜 유일 키로 믿나
- [ ] (꼬리) 같은 요청이 두 번 오면
- [ ] (꼬리) 재요청 시 `break_seconds`만 증가 방향으로 갱신하는 이유
- [ ] (꼬리) `session_order`에도 UNIQUE를 건 이유
- [ ] (꼬리) 정본이 `todo_sessions`인데 집계를 왜 중복 저장했나
- [ ] (개념) 멱등성. 어떤 HTTP 메서드가 멱등한가
- [ ] (개념) 멱등 키와 낙관적 락의 차이
- [ ] (개념) 역정규화가 정당화되는 조건
- [ ] (약점) 캐시와 정본 합계가 어긋나면 어떻게 아나
- [ ] (약점) 어긋났을 때 복구 수단이 있나
- [ ] (약점) 클라이언트가 매번 새 ID를 만들면 멱등성이 무너지지 않나

### Flyway

`data-model.md` 기준 스키마 `db/migration/` · V7 복합 인덱스 추가

- [ ] (시작) 스키마 변경을 어떻게 관리하나
- [ ] (꼬리) `ddl-auto` 대신 Flyway를 고른 이유
- [ ] (꼬리) 인덱스를 V7로 뒤늦게 추가한 판단 시점과 근거
- [ ] (꼬리) dev와 prod 스키마 버전을 어떻게 맞추나
- [ ] (개념) Flyway는 이력을 어떻게 추적하나 (checksum)
- [ ] (개념) 적용된 마이그레이션을 수정하면
- [ ] (개념) 무중단 배포에서 스키마 변경 순서 (expand-and-contract)
- [ ] (약점) 컬럼 DROP을 바로 안 하는 이유
- [ ] (약점) 롤백이 필요한 마이그레이션은
- [ ] (약점) 대용량 테이블에 인덱스를 걸면 운영 중 무슨 일이 생기나

## 인프라·운영

### FlowMate AWS 구조와 배포

포트폴리오 · `architecture.md` §1(4컨테이너, `127.0.0.1:8080`만 노출, actuator는 health만), §4(dev 수동 트리거 / prod tag
`v*.*.*` + semver 가드, ECR)

- [ ] (시작) 운영 아키텍처 설명
- [ ] (꼬리) SPA를 S3+CloudFront로 배포한 이유
- [ ] (꼬리) EC2에서 Docker Compose를 쓴 이유
- [ ] (꼬리) NE:MO는 K8s까지 갔는데 FlowMate는 왜 안 썼나
- [ ] (꼬리) Spring Boot를 `127.0.0.1`로만 연 이유
- [ ] (꼬리) actuator를 health만 연 이유
- [ ] (꼬리) dev는 수동, prod는 tag push로 나눈 이유
- [ ] (꼬리) semver 아닌 tag가 prod를 안 건드리게 어떻게 막았나
- [ ] (개념) CDN이 SPA 배포에서 해결하는 것
- [ ] (개념) CloudFront invalidation이 필요한 이유
- [ ] (개념) Reverse Proxy와 Load Balancer의 차이
- [ ] (약점) 현재 구조의 SPOF
- [ ] (약점) 트래픽이 커지면 어떤 순서로 확장하나
- [ ] (약점) EC2 한 대에 MySQL과 Redis가 같이 있는데
- [ ] (약점) 배포 중 다운타임은 있나

### NE:MO CI/CD

포트폴리오 v2 · NE:MO 위키 `CI 파이프라인`·`CD 파이프라인` §3 도구 비교, §4.3

- [ ] (시작) CI/CD 파이프라인 설명
- [ ] (꼬리) CI에서 뭘 검증했나
- [ ] (꼬리) Artifact Registry의 역할
- [ ] (꼬리) 이미지 태그를 어떻게 관리했나
- [ ] (꼬리) `latest`만 쓰면 뭐가 문제인가
- [ ] (꼬리) 롤백은 어떻게 했나
- [ ] (꼬리) Docker layer cache로 빌드 시간을 어떻게 줄였나
- [ ] (꼬리) layer 순서를 어떻게 잡아야 캐시가 먹나
- [ ] (꼬리) MIG 롤링 배포의 헬스체크는 뭘 보나
- [ ] (개념) Blue-Green·Rolling·Canary의 차이
- [ ] (개념) 무중단 배포가 되려면 앱이 뭘 만족해야 하나
- [ ] (약점) 배포 실패를 자동 감지·복구하려면 뭐가 더 필요한가
- [ ] (약점) 마이그레이션이 낀 배포의 롤백

### Helm + Argo CD

포트폴리오 v3 · `Kubeadm‐Dev` §4-1 (Helm chart 구조, Actions → ArgoCD, Metrics Server + HPA)

- [ ] (시작) GitOps 기반 CD를 어떻게 구축했나
- [ ] (꼬리) GitHub Actions와 Argo CD의 역할 분담
- [ ] (꼬리) 순수 YAML로는 왜 부족했나
- [ ] (꼬리) Chart와 `values.yaml`이 각각 담는 것
- [ ] (꼬리) 이미지 태그가 바뀌면 뭐가 커밋되고 Argo CD는 어떻게 아나
- [ ] (개념) GitOps
- [ ] (개념) Push CD와 Pull CD의 차이
- [ ] (개념) Drift, Sync, Auto Sync, Self-heal
- [ ] (약점) Git과 클러스터 상태가 다르면 어떤 정책으로 복구하나
- [ ] (약점) GitOps에서 Secret은 어떻게 다루나
- [ ] (약점) Argo CD 자체는 누가 배포하나

### Observability

`monitoring-stack.md` §2~5 · Metrics는 15초 pull, Logs는 docker socket tail, Traces는 OTel push

- [ ] (시작) Grafana Cloud로 바꾼 이유
- [ ] (꼬리) self-hosted의 문제 세 가지
- [ ] (꼬리) 비용 절감이 목적이었나 운영 단순화가 목적이었나
- [ ] (꼬리) Alloy가 하는 일
- [ ] (꼬리) Metrics는 pull, Traces는 push인 이유
- [ ] (꼬리) 직접 만든 대시보드에 담은 질문
- [ ] (꼬리) 커뮤니티 대시보드는 그대로 쓰고 직접 만든 것만 git으로 관리하는 이유
- [ ] (개념) Monitoring과 Observability의 차이
- [ ] (개념) Metrics·Logs·Traces가 답하는 질문
- [ ] (개념) RED Method
- [ ] (개념) p95·p99. 평균만 보면 안 되는 이유
- [ ] (약점) 외부 SaaS 의존 리스크
- [ ] (약점) 무료 플랜 한도를 넘으면
- [ ] (약점) 알람은 어떤 조건으로 걸었나

### HikariCP pool 실험

`monitoring-stack.md` §6 v3/v4 breakpoint(2026-05) — pending 증가 → pool 의심 → JVM·slow query 정상, host CPU 100% → pool 10→20 후 throughput 25%↓

- [ ] (시작) 병목을 잘못 짚었다가 바로잡은 경험 설명
- [ ] (꼬리) pending이 늘어난 걸 보고 처음에 뭘 의심했나
- [ ] (꼬리) pool 부족이 아니라고 본 근거 지표들
- [ ] (꼬리) 10→20으로 늘렸는데 왜 처리량이 25% 떨어졌나
- [ ] (꼬리) 진짜 원인은 뭐였나
- [ ] (개념) 커넥션 풀 크기는 뭘로 정하나
- [ ] (개념) pool을 늘려도 처리량이 안 오르는 이유
- [ ] (개념) HikariCP의 pending·active·idle
- [ ] (개념) 원인 지표와 결과 지표를 어떻게 구별하나
- [ ] (약점) CPU 병목과 DB 병목을 어떻게 가르나
- [ ] (약점) 이 경험 이후 지표 보는 순서가 어떻게 바뀌었나
- [ ] (약점) Scale-up과 Scale-out 중 뭘 먼저 보나

### k6 부하 테스트

`timer-deadlock.md` §1(max 12 VU, 10분, 112,132건) · `redis-sse-pubsub.md` §5.3(동시 200, PUT/s 1,000, 30,000건)

- [ ] (시작) 부하 테스트의 목표와 동시 사용자·처리량 기준을 어떻게 잡았나
- [ ] (꼬리) smoke·baseline·breakpoint가 각각 확인하는 것
- [ ] (꼬리) max 12 VU의 근거
- [ ] (꼬리) 수정 전후 조건을 어떻게 통제했나
- [ ] (꼬리) SSE 부하는 REST와 어떻게 다르게 설계했나
- [ ] (꼬리) 도달률과 유실을 어떻게 쟀나
- [ ] (꼬리) 전역 threshold만 보면 안 되는 이유
- [ ] (개념) VU, TPS와 RPS의 차이
- [ ] (개념) Throughput과 Latency의 관계
- [ ] (개념) `checks`와 `http_req_failed`가 세는 것
- [ ] (약점) dev 측정치를 prod 성능으로 말해도 되나
- [ ] (약점) 성능 목표는 뭘 근거로 잡나
- [ ] (약점) 부하 생성기가 병목이 아니란 건 어떻게 아나

### 부하테스트 대회

이력서 Awards — 기능 혁신상(2025.08), 인스턴스 30대 한도 3-Tier로 TPS 3,000 · 상세 `apply/interview-prep/04-loadtest.md`

- [ ] (시작) 대회에서 어떤 인프라를 설계했나
- [ ] (꼬리) 30대 제약 안에서 어떻게 배분했고 근거는 뭔가
- [ ] (꼬리) TPS 3,000은 뭘 기준으로 잰 수치인가
- [ ] (꼬리) 오토스케일 정책은 어떤 지표로 걸었나
- [ ] (꼬리) 병목은 어디였고 어떻게 풀었나
- [ ] (꼬리) 기능 혁신상의 차별점
- [ ] (개념) 스케일 아웃 시 세션·캐시를 어떻게 다루나
- [ ] (개념) ALB 헬스체크와 커넥션 드레이닝
- [ ] (약점) 대회 조건과 실제 운영의 차이
- [ ] (약점) 30대 제한이 없었으면 뭘 다르게 했겠나
- [ ] (약점) 본인이 직접 한 부분은 어디까지인가

### Docker

포트폴리오 v2 · NE:MO 위키 `Docker 컨테이너화 배포`

- [ ] (시작) Docker로 실제로 뭐가 해결됐나
- [ ] (꼬리) 환경 불일치가 컨테이너화로 어떻게 사라졌나
- [ ] (꼬리) 태그 기반 배포·롤백이 구체적으로 무슨 뜻인가
- [ ] (개념) VM과 컨테이너의 차이. 컨테이너가 가벼운 이유
- [ ] (개념) Image와 Container
- [ ] (개념) Layer는 어떻게 만들어지고 캐시는 언제 깨지나
- [ ] (개념) Multi-stage build가 해결하는 것
- [ ] (개념) Volume은 언제 쓰나. 재시작하면 데이터는 어떻게 되나
- [ ] (개념) Compose와 Kubernetes의 차이
- [ ] (약점) Production DB를 컨테이너로 운영하는 것에 대한 생각
- [ ] (약점) 이미지 크기를 줄이려면

### 운영 트러블슈팅

`monitoring-stack.md` §6과 `sse-timeout.md` §5에서 실제로 밟은 순서를 일반 상황에 적용

- [ ] (시작) 특정 API 응답이 평소보다 3배 느려지면 뭘 먼저 보나
- [ ] (꼬리) 지표를 어떤 순서로 보나
- [ ] (꼬리) 전체가 느린 건지 한 엔드포인트만 느린 건지 어떻게 가르나
- [ ] (꼬리) 로그·메트릭·트레이스 중 뭘 먼저 보나
- [ ] (꼬리) 즉시 완화와 원인 수정 중 뭘 먼저 하나
- [ ] (꼬리) 에러 메시지를 그대로 믿으면 안 됐던 사례
- [ ] (개념) "언제 발생하고 언제 사라지는가"로 좁히는 게 왜 중요한가
- [ ] (약점) 재현이 안 되는 문제는 어떻게 접근하나
- [ ] (약점) 지표는 정상인데 사용자만 느리다고 하면
