# JPA, Hibernate, Spring Data JPA 의 차이가 무엇인가요?

## 1. 요약

### 1.1. 결론

JPA는 객체-DB 매핑 방법을 정의한 표준 명세, Hibernate는 그 명세를 실제로 구현한 ORM 프레임워크, Spring Data JPA는 그 위에
얹혀 Repository 구현 코드를 대신 만들어주는 스프링의 편의 계층입니다.

### 1.2. 왜

특정 구현체에 종속되지 않고 표준화된 방식으로 영속성 계층을 다루기 위해 JDBC가 표준 인터페이스와 벤더별 드라이버를 분리한
것과 같은 명세-구현 분리 구조를 택했고, Spring Data JPA는 그 위에서 반복적으로 작성해야 했던 Repository 구현 코드를 없애기
위해 얹힌 계층입니다.

### 1.3. 원리

JPA는 EntityManager, @Entity 같은 인터페이스·애노테이션 규약만 정의하고 실제 동작은 구현하지 않으며, Hibernate가 이 규약을 실제로 구현해 SQL 생성과 영속성 컨텍스트 동작을 담당합니다. Spring Data JPA는 JpaRepository를 상속받아 메서드 이름 규칙에 맞춰 선언만 하면 그에 맞는 쿼리 구현체를 런타임에 자동으로 만들어 빈으로 등록해줍니다.

### 1.4. 장단점 및 실무

Spring Data JPA는 JPA를 대체하는 게 아니라 그 위의 편의 계층일 뿐이므로, 문제가 생겼을 때 Repository 계층(Spring Data     
JPA)의 이슈인지 매핑·쿼리 실행(Hibernate/JPA)의 이슈인지 구분해서 디버깅해야 합니다. 애플리케이션 코드가 JPA 표준         
인터페이스에 맞춰 작성돼 있다면 구현체를 교체해도 원칙적으로 크게 수정할 필요가 없지만, 실무에서는 Hibernate가 가장 널리  
쓰이고 생태계와 자료도 풍부해 사실상 표준처럼 사용됩니다.


