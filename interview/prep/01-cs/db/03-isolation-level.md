# 트랜잭션 격리수준에 대해서 설명해주세요.

## 1. 요약

### 1.1. 결론

트랜잭션 격리 수준은 동시에 실행되는 트랜잭션이 서로에게 얼마나 간섭하는 걸 허용할지 단계별로 정하는 설정으로,
정합성과 동시성 성능 사이의 균형점을 고르는 장치입니다.

### 1.2. 왜

동시 트랜잭션 사이의 간섭을 완벽히 막기 위해선 트랜잭션을 한 줄로 세워 순차 실행하면 되지만, 그러면 동시 처리 능력이 현저히 떨어지기 때문에, 어디까지 허용할지 선택할 수 있게 만들어졌습니다.

### 1.3. 원리

낮은 쪽부터 READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE 네 단계로,
위로 갈수록 Dirty Read, Non-Repeatable Read, Phantom Read 순서대로 막아냅니다.

즉, 위로 갈수록 정합성은 오르고 동시성은 떨어집니다.

### 1.4. 장단점 및 실무

MySQL InnoDB 기본값인 REPEATABLE READ는 MVCC 스냅샷 읽기와 갭 락 덕분에 이론과 달리 Phantom Read까지 거의 막아 SERIALIZABLE 없이도 대부분 충분한 정합성을 줍니다. 따라서 일반적인 서비스는 REPEATABLE READ를, 정합성이 절대적으로 중요한 일부 작업에만 SERIALIZABLE이나 명시적 락을 선택합니다.