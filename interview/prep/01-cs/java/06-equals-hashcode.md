# equals와 hashCode는 왜 함께 재정의해야 할까요?

## 1. 요약

### 1.1. 결론

equals와 hashCode는 동등한 객체가 반드시 같은 해시코드를 가져야 한다는 자바 규약 때문에 함께 재정의해야 합니다.

### 1.2. 왜

HashMap, HashSet 같은 해시 기반 컬렉션은 hashcode로 먼저 저장 위치(버킷)를 정의하고 그 버킷 안에서만 equals로 세부 비교를 하는 방식으로 동작하는데, equals만 재정의하고 hashCode를 그대로 두면 값이 같은 두 객체가 서로 다른 버킷에 들어가 컬렉션 내부 탐색 로직이 깨집니다.

### 1.3. 원리

equals만 재정의하고 hashCode를 그대로 두면 논리적으로 같은 두 객체가 서로 다른 버킷에 들어가, HashSet은 중복 제거가 안 되고 HashMap은 저장할 때와 조회할 때 버킷이 어긋나 get()이 null을 반환합니다.

### 1.4. 장단점 및 실무

따라서 equals를 재정의하는 클래스는 반드시 hashCode도 함께 재정의하거나, IDE 자동 생성 기능, Lombok의 @EqualsAndHashCode를 활용해 두 메서드가 항상 같은 필드를 참조하도록 관리해야 합니다.