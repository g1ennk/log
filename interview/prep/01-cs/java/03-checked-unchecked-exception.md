# 자바에서 Checked Exception과 Unchecked Exception에 대해서 설명해주세요.

## 1. 요약

### 1.1. 결론

Checked Exception과 Unchecked Exception은 컴파일러가 예외 처리를 강제하는지 여부로 갈립니다.

### 1.2. 왜

프로그램 바깥 상황 때문에 실패하는 경우(네트워크 오류)와 코드 자체의 결함 때문에 실패하는 경우(Null 참조)를 컴파일러 차원에서 구분해 강제 처리 여부를 다르게 두기 위해 둘을 나눕니다.

### 1.3. 원리

먼저 Checked Exception은 Exception을 상속하되 RuntimeException은 상속하지 않는 예외(IOException)로 컴파일러가 처리를 강제하고,
Unchecked Exception은 RuntimeException을 상속하는 예외(NullPointerException)로 처리를 개발자에게 맡깁니다.

### 1.4. 장단점 및 실무

결론적으로 호출한 쪽이 재시도나 대체 로직으로 실제 복구할 수 있다면 Checked, 애초에 발생하지 말았어야 할 프로그래밍 오류면 Unchecked로 설계합니다.

