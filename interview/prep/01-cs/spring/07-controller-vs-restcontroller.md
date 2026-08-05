# @Controller 와 @RestController 의 차이점을 설명해주세요.

## 1. 요약

### 1.1. 결론

먼저 두 개 모두 요청을 받아 처리하는 Spring MVC 컨트롤러이지만, @Controller는 반환값을 뷰 이름으로 해석해 ViewResolver로 HTML을 렌더링하는 반면, @RestController는 @ResponseBody가 결합돼 HttpMessageConverter로 JSON 직렬화해 응답 바디에 직접 쓴다는 차이점이 있습니다.

### 1.2. 왜

초창기 MVC는 서버 렌더링을 기본 전제로 설계되었지만, 이후 JSON 데이터만 주고받는 API 서버가 늘어나면서 매번 컨트롤러 메서드마다 @ResponseBody를 반복해서 붙여야 했고, 이러한 번거로움을 줄이기 위해 @Controller + @ResponseBody를 합친 @RestController가 도입되었습니다.

### 1.3. 원리

@Controller는 Thymeleaf 같은 서버 렌더링에는 적합하지만, 데이터 반환 시 메서드마다 @ResponseBody가 필요하고, @RestController는 API 응답에 최적화되어 있지만 화면 반환에는 사용하지 않습니다.

### 1.4. 장단점 및 실무

따라서 화면과 API를 함께 쓰는 프로젝트는 @Controller에 데이터용 메서드만 @ResponseBody를 붙이고, API 전용 서버는 @RestController를 기본으로 사용합니다.