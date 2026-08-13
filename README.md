# log

## 목차

- [backend](./backend)
    - [JWT](backend/jwt.md)
- [interview](./interview)
    - [prep](./interview/prep)
        - [01-cs](./interview/prep/01-cs)
            <details>
            <summary><a href="./interview/prep/01-cs/network">network</a> (5)</summary>

            - [TCP와 UDP의 차이점은 무엇인가요?](interview/prep/01-cs/network/01-tcp-udp.md)
            - [TCP 3-way handshake에 대해서 설명해주세요.](interview/prep/01-cs/network/02-3-way-handshake.md)
            - [OSI 7 계층 모델을 설명해주세요](interview/prep/01-cs/network/03-osi-7.md)
            - [TCP 4-way handshake(연결 종료 과정)에 대해 설명해주세요](interview/prep/01-cs/network/04-4-way-handshake.md)
            - [네트워크에서 회선 교환 방식과 패킷 교환 방식은 어떤 차이점 있나요?](interview/prep/01-cs/network/05-circuit-packet.md)
            </details>
            <details>
            <summary><a href="./interview/prep/01-cs/os">os</a> (2)</summary>

            - [GC 알고리즘은 어떤 것이 있나요?](interview/prep/01-cs/os/01-gc-algorithm.md)
            - [왜 코루틴 방식이 기존 스레드 방식보다 가벼운지 설명해주세요.](interview/prep/01-cs/os/02-coroutine.md)
            </details>
            <details>
            <summary><a href="./interview/prep/01-cs/java">java</a> (14)</summary>

            - [자바 실행 흐름](interview/prep/01-cs/java/01-java-execution-flow.md)
            - [JVM의 장단점](interview/prep/01-cs/java/02-jvm.md)
            - [Java Memory Model(JMM)이란 무엇인가요?](interview/prep/01-cs/java/jmm.md)
            - [volatile 키워드의 역할은?](interview/prep/01-cs/java/volatile.md)
            - [자바에서 클래스 정보는 어떻게 알아낼 수 있을까요?](interview/prep/01-cs/java/reflection-api.md)
            - [자바에서 Checked Exception과 Unchecked Exception에 대해서 설명해주세요.](interview/prep/01-cs/java/03-checked-unchecked-exception.md)
            - [Call By Value와 Call By Reference에 대해서 설명해주세요.](interview/prep/01-cs/java/04-call-by-value-reference.md)
            - [동일성과 동등성에 대해서 설명해주세요.](interview/prep/01-cs/java/05-identity-equality.md)
            - [equals와 hashCode는 왜 함께 재정의해야 할까요?](interview/prep/01-cs/java/06-equals-hashcode.md)
            - [String 객체는 가변일까요, 불변일까요?](interview/prep/01-cs/java/07-string-immutable.md)
            - [HashMap의 내부 동작 방식(해시 버킷, 트리화)을 설명해주세요](interview/prep/01-cs/java/08-hashmap-internal.md)
            - [시간 복잡도와 공간 복잡도의 차이점은 무엇인가요?](interview/prep/01-cs/java/09-time-space-complexity.md)
            - [자료구조 스택에 대해서 설명해주세요.](interview/prep/01-cs/java/10-stack.md)
            - [연결 리스트에 대해서 설명해주세요.](interview/prep/01-cs/java/11-linked-list.md)
            - [이진 트리에 대해서 설명해 주세요.](interview/prep/01-cs/java/12-binary-tree.md)
            </details>
            <details>
            <summary><a href="./interview/prep/01-cs/spring">spring</a> (15)</summary>

            - [Spring과 Spring Boot의 차이를 말해주세요.](interview/prep/01-cs/spring/01-spirng-vs-springboot.md)
            - [IoC 컨테이너란 무엇인가요?](interview/prep/01-cs/spring/02-ioc-container.md)
            - [의존성 주입이란 무엇인가요?](interview/prep/01-cs/spring/03-di.md)
            - [Spring에서 객체를 Bean으로 관리하는 이유를 설명해주세요](interview/prep/01-cs/spring/04-bean-management-reason.md)
            - [@Component, @Controller, @Service, @Repository의 차이점에 대해서 설명해주세요.](interview/prep/01-cs/spring/05-component-annotation.md)
            - [Spring MVC의 실행 흐름에 대해 설명해주세요.](interview/prep/01-cs/spring/06-spring-mvc-flow.md)
            - [@Controller 와 @RestController 의 차이점을 설명해주세요.](interview/prep/01-cs/spring/07-controller-vs-restcontroller.md)
            - [AOP의 동작 원리와 프록시 기반 구현을 설명해주세요.](interview/prep/01-cs/spring/08-aop-proxy.md)
            - [스프링 트랜잭션 AOP 동작 흐름에 대해서 설명해주세요.](interview/prep/01-cs/spring/09-transactional-aop-flow.md)
            - [영속성 컨텍스트의 1차 캐시·쓰기 지연·변경 감지는 어떻게 동작하나요?](interview/prep/01-cs/spring/10-persistence-context.md)
            - [Spring Bean의 생명주기를 설명해주세요](interview/prep/01-cs/spring/11-bean-lifecycle.md)
            - [Spring의 Bean Scope 종류를 설명해주세요](interview/prep/01-cs/spring/12-bean-scope.md)
            - [스프링에서 순환 참조가 발생하는 이유와 해결법은?](interview/prep/01-cs/spring/13-circular-reference.md)
            - [AutoConfiguration 동작 원리를 설명해주세요.](interview/prep/01-cs/spring/14-autoconfiguration.md)
            - [@Value 어노테이션 사용 시 주의할 점을 설명해주세요.](interview/prep/01-cs/spring/15-value-annotation.md)
            </details>
            <details>
            <summary><a href="./interview/prep/01-cs/db">db</a> (4)</summary>

            - [관계형 데이터베이스와 비 관계형 데이터베이스의 차이점은 무엇인가요?](interview/prep/01-cs/db/01-rdb-vs-nosql.md)
            - [ACID에 대해서 설명해주세요.](interview/prep/01-cs/db/02-acid.md)
            - [트랜잭션 격리수준에 대해서 설명해주세요.](interview/prep/01-cs/db/03-isolation-level.md)
            - [데이터베이스 인덱스에 대해서 설명해주세요.](interview/prep/01-cs/db/04-db-index.md)
            </details>
        <details>
        <summary><a href="./interview/review">review</a> (3)</summary>

        - [260723](./interview/review/260723.md)
        - [260724](./interview/review/260724.md)
        - [260731](./interview/review/260731.md)
        </details>
