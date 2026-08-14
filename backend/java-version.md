# 자바 버전 발전 과정

## Java 8

### 1. Lambda & Stream

- 이전 Java에서 동작을 전달하려면 인터페이스를 구현하거나 익명 클래스를 만들어야 했다.
    - Java 8은 람다, 메서드 레퍼런스, 스트림, default method를 도입하며 함수형 스타일을 본격적으로 지원했다.
        - 기존 반복문 방식
        ````java
        List<String> names = Arrays.asList("Kim", "Lee", "Park");

        for (String name : names) {
            if (name.startsWith("K")) {
                System.out.println(name);
            }
        }
        ````

        - Stream 방식
        ````java
        names.stream()
            .filter(name -> name.startsWith("K"))
            .forEach(System.out::println);
        ````

### 2. `Optional`

- `Optional`은 값이 없을 수 있는 반환값을 명시적으로 표현하는 데 적합하다. 모든 `null`을 대체하기보다는, 호출자가 값의 부재를 처리하도록 API 의도를 드러내는 용도로 사용한다.
    - `Optional` 사용 전
    ````java
    User user = findUserOrNull();

    if (user != null) {
        System.out.println(user.getName());
    }
    ````

    - `Optional` 사용 후 (`findUser()`의 반환 타입은 `Optional<User>`)
    ````java
    Optional<User> user = findUser();

    user.ifPresent(u ->
        System.out.println(u.getName())
    );
    ````

### 3. `java.time`

- 8부터 날짜와 시간 API도 크게 개선되어 기존에 `Date`,
  `Calendar`가 가지고 있던 여러 문제를 개선하기 위해 `LocalDate`, `LocalDateTime`, `Instant`, `Duration` 등의 새로운 Date-Time API가 들어왔다.

## Java 11

### 1. 플랫폼 현대화

- HTTP Client API가 정식 표준 API가 되었다.
    - JDK 9와 10에서 incubator API로 제공되던 HTTP Client가 Java 11에서 `java.net.http.HttpClient`로 표준화되었다. HTTP/1.1, HTTP/2와 `CompletableFuture` 기반 비동기 요청을 지원한다.

## Java 17

### 1. Record

- record 이전 DTO는 다음과 같이 장황했다. 생성자, getter, `equals`, `hashCode`, `toString` 등 작성할 코드가 많았다.
    ````java
    public class User {

        private final Long id;
        private final String name;

        public User(Long id, String name) {
            this.id = id;
            this.name = name;
        }

        public Long getId() {
            return id;
        }

        public String getName() {
            return name;
        }

        // equals
        // hashCode
        // toString
    }
    ````

- `User`가 `id`와 `name`을 가진다는 사실을 표현하기 위해 부수적인 코드가 많았다. record를 사용하면 이를 다음처럼 간결하게 표현할 수 있다.
    ````java
    public record User(
        Long id,
        String name
    ) {}
    ````

### 2. Sealed Class와 Sealed Interface

- Java 17의 `sealed` 타입으로 상속하거나 구현할 수 있는 타입을 제한할 수 있게 되었다.
    ````java
    public interface Payment {
    }

    class CardPayment implements Payment {
    }

    class CashPayment implements Payment {
    }

    class BitcoinPayment implements Payment {
    }

    class SomethingPayment implements Payment {
    }
    ````
- sealed interface를 사용하면 다음처럼 허용할 타입을 명시할 수 있다. 타입을 제한해 도메인을 명확하게 만들고, 컴파일러가 가능한 타입 전체를 알 수 있게 한다.
    ````java
    sealed interface Payment permits CardPayment, CashPayment {}

    final class CardPayment implements Payment {}

    final class CashPayment implements Payment {}
    ````

- sealed 타입은 Java 21에서 정식화된 pattern matching for `switch`와 함께 사용할 때 특히 유용하다.

## Java 21

### 1. Virtual Thread

- Java 21에서 virtual thread가 정식 기능으로 도입되었다.
    - 기존 platform thread는 실행 수명 동안 OS thread를 점유하므로, 대량 생성의 비용이 커 보통 thread pool을 사용했다.
        - 예를 들어 pool 크기가 200이면 많은 작업을 최대 200개의 platform thread가 나눠 처리한다.
- virtual thread는 OS thread와 1:1로 대응하지 않는다. 여러 virtual thread가 carrier thread(= platform thread)를 공유해 다음처럼 동작한다.
    ````text
    // Before
    Java Platform Thread 1 ── OS Thread 1
    Java Platform Thread 2 ── OS Thread 2
    Java Platform Thread 3 ── OS Thread 3

    // After
    Virtual Thread 1 ┐
    Virtual Thread 2 │
    Virtual Thread 3 ├── Carrier Thread A ── OS Thread
    Virtual Thread 4 │
    Virtual Thread 5 ┘

    Virtual Thread 6 ┐
    Virtual Thread 7 ├── Carrier Thread B ── OS Thread
    Virtual Thread 8 ┘
    ````

- 따라서 I/O 대기 시간이 긴 서버 작업에서 효율적이다.
    - 예: HTTP API 호출, Redis 요청, 파일 I/O, 네트워크 I/O
    - 다만 DB 커넥션 풀, 외부 API의 처리량 등 다른 자원 병목은 virtual thread를 사용해도 그대로 남는다.

### 2. Record Pattern과 Pattern Matching for `switch`

- Java 21에서는 record pattern과 pattern matching for `switch`가 정식 기능이 되었다. sealed 타입과 record를 함께 사용하면 타입별 처리를 간결하고 빠짐없이 작성할 수 있다.

````java
sealed interface Payment permits Card, Cash {}

record Card(String number) implements Payment {}

record Cash(int amount) implements Payment {}
````

````java
String process(Payment payment) {
    return switch (payment) {
        case Card(String number) -> "카드 결제: " + number;
        case Cash(int amount) -> "현금 결제: " + amount;
    };
}
````

- 즉, 전반적인 흐름이 다음과 같다.
    - `sealed`로 가능한 데이터 종류를 제한하고
    - `record`로 데이터를 간결하게 표현하며
    - record pattern으로 데이터 내부를 간결하게 꺼내고
    - pattern matching for `switch`로 타입별 로직을 처리한다.

## Java 25 (작성 예정)
