# Java Heap 구조 / GC 실행 시점 (Java 8 기준)

## 1. 요약

### 1.1. 결론

Java 8의 Heap은 객체 생존 기간에 따라 Young Generation과 Old Generation으로 나뉘어 각 영역은 서로 다른 시점에 GC가 실행됩니다.

### 1.2. 왜

Young Generation은 새 객체가 생성되는 Eden과 살아남은 객체를 옮겨담는 두 개의 Survivor 영역으로 구성되고, Old Generation은 Young에 여러 번 살아남아 승격된 객체가 오래 머무는 공간입니다.

### 1.3. 원리

Eden이 가득 차면 Minor GC가 발생해 살아있는 객체를 Survivor 영역으로 옮기고 죽은 객체를 회수하며, 객체는 Minor GC를 거칠 때마다 나이가 증가해 임계치를 넘으면 Old 영역으로 승격됩니다. Old 영역 사용량이 임계치를 넘으면 Old 영역 전체를 검사하는 Major GC가 발생합니다. Old 영역이 훨씬 크고 살아있는 객체 비율도 높아 Minor GC보다 Major GC가 Stop-The-World 시간이 훨씬 깁니다.

### 1.4. 장단점 및 실무

실무에선 GC 로그를 모니터링하면서 Major GC 빈도가 지나치게 높다면 Young의 크기나 객체 생성 패턴을 조정해, 불필요한 조기 승격을 줄여 애플리케이션 응답 지연을 최소화하는 방향으로 접근하기도 합니다.

