# Spring MVC의 실행 흐름에 대해 설명해주세요.

## 1. 요약

### 1.1. 결론

Spring MVC는 모든 요청을 DispatcherServlet이 받아 HandlerMapping, HandlerAdapter, ViewResolver에 위임하는 프론트 컨트롤러 패턴입니다.

### 1.2. 왜

전통적인 서블릿 방식에서는 URL마다 서블릿을 따로 뒀기에 인코딩, 인증 같은 공통 로직이 중복됐고, 한곳에서 공통 처리 후 위임하는 구조가 필요했습니다.

### 1.3. 원리

DispatcherServlet은 HandlerMapping으로 핸들러를 찾고 HandlerAdapter로 실행합니다. 뷰 이름을 반환하면 ViewResolver가 렌더링하고, @RestController는 HttpMessageConverter로 JSON을 응답 바디에 씁니다.

### 1.4. 장단점 및 실무

이러한 구조 덕분에 공통 관심사는 DispatcherServlet에서 일관되게 처리할 수 있게 되고 이 흐름을 정확하게 알아야 문제가 발생했을 때 정확히 어느 지점에서 발생했는지를 빠르게 판단할 수 있습니다.