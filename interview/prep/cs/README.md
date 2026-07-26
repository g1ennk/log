# cs

"REST가 뭔가요", "GC는 어떻게 동작하나요" 같은, 개념 자체를 설명하라는 질문.

## 포맷

정의 → 원리 → 예시 → 장단점 → 실무

## 글 구조

파일 하나를 이 세 파트로 쓴다.

1. **요약** — 위 포맷을 라벨로 나눠 정리
2. **개념** — 배경과 원리를 상세히 풀어 쓴 설명
3. **예상 질문** — 나올 법한 꼬리질문과 답변

## 다음에 쓸 글

- [ ] Terraform state locking — 동시 apply 충돌 메커니즘
- [ ] Terraform state의 secret 관리 원칙
- [ ] InnoDB gap lock + insert intention lock으로 인한 deadlock
- [ ] Kubernetes namespace 간 통신 (기본은 열려있음, NetworkPolicy로 제한)
- [ ] 단일 control plane 장애 시 동작 (worker 자동 승격 안 됨, kubelet vs controller/scheduler 역할)
- [ ] Redis Pub/Sub vs Kafka vs Redis Streams 트레이드오프
