# credentials

이력서와 포트폴리오에 적은 내용을 보고 면접관이 던지는 질문. 개념 설명이 필요한 질문이 섞여 나와도 여기서 같이 다룬다 — 서류에서 출발한 질문은 [01-cs](../01-cs)로 빼지 않는다.

## 포맷

근거 문장 → 30초 답변 → 수치·구성 근거 → 한계 → 꼬리질문

서류에는 이미 "문제 → 해결 → 결과"가 적혀 있다. 여기서 채울 건 **그 문서에 없는 것** — 측정 조건과 한계다.

- **근거 문장** — 서류 원문 그대로 옮기고 원본 링크를 단다. 답변 수치가 서류와 어긋나면 그 자리에서 무너진다.
- **30초 답변** — 상황 → 문제 → 판단 → 결과. 이것만 말해도 답이 되게. 장애든 설계 진화든 이 네 칸으로 쓴다. `문제` 칸이 장애의 원인 분석과 설계의 구조적 한계를 함께 받고, `해결`이 아니라
  `판단`이라 대안 비교를 건너뛸 수 없다.
- **수치·구성 근거** — 숫자 하나마다 측정 조건과 방법을 붙인다. 조건 없는 숫자는 적지 않는다. 성능 수치가 없는 주제는 구성표(정책·TTL·단계)가 근거다.
- **한계** — 이 구조가 보장 못 하는 것, 안 해본 것, 다시 한다면.
- **꼬리질문** — 3층으로 나눈다. 1차는 설명, 2차는 대안 비교, 3차는 약점 찌르기.

3차가 비어 있으면 아직 방어 준비가 안 된 글이다.

## 글 구조

파일 하나 = 서류에 적은 주장 하나. 출처에 따라 세 갈래로 나눈다.

- `flowmate/` — FlowMate 성과
- `nemo/` — NE:MO 성과
- `profile/` — 프로젝트 밖 이력 (전공 전환, 일하는 방식 등)

## 면접 기출

실제로 나왔던 질문. 재출제 확률이 높아 우선 쓴다.

- [ ] 인증 4단계와 토큰 4종 분리 이유 — AT를 localStorage에 두지 않은 이유, RT를 HttpOnly cookie에 둔 이유와 cookie의 위험, 멀티디바이스 로그인 해제 정책, 같은 기기 여러 탭의 동시 refresh, 만료된 RT와 폐기된 RT의 차이, 만료된 AT로 세 API를 동시 호출할 때 동작 (
  `flowmate/auth-evolution.md`)
- [ ] `@Transactional` rollback과 self-invocation, `REQUIRES_NEW` (`flowmate/auth-revoke-transaction.md`)
- [ ] Redis Pub/Sub 유실과 재진입 snapshot 복구, 구독과 조회 순서, Kafka·Redis Streams 대신 선택한 이유 (`flowmate/redis-pubsub.md`)
- [ ] Timer 최초 INSERT 경로의 InnoDB deadlock (`flowmate/timer-deadlock.md`)
- [ ] SSE 연결이 60초에 끊긴 원인을 HTTP 연결 관점에서 설명 (`flowmate/sse-timeout.md`)
- [ ] k6 부하 테스트의 목표와 동시 사용자·처리량 기준 (`flowmate/load-test-k6.md`)
- [ ] 단위 테스트와 통합 테스트의 사용 기준 (`flowmate/test-strategy.md`)
- [ ] GCP 단일 VM의 사양과 용량 판단 근거, Docker layer cache와 빌드 시간 단축 (`nemo/infra-3-phase.md`)
- [ ] Kubernetes namespace 간 통신, 단일 control plane 장애 시 worker의 동작 (`nemo/k8s-kubeadm-dev.md`)
- [ ] NE:MO에서 맡은 역할과 사용한 Cloud Platform (`nemo/infra-3-phase.md`)
- [ ] AI가 제시한 선택지를 검증하는 기준 (`profile/ai-workflow.md`)
