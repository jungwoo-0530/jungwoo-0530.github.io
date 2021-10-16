# ExceptionHandler

#### 스프링 @MVC 예외 처리 방법

- @ControllerAdvice
- @ExchangepHandler

#### 스프링 부트가 제공하는 기본 예외 처리기

- BasicErrorController
  - HTML과 JSON 응답 지원

- 커스터마이징 방법
  - ErrorController 구현

#### 커스텀 에러 페이지

- 상태코드값에따라에러페이지보여주기
- src/main/resources/static|template/error/
- 404.html
- 5xx.html
- ErrorViewResolver 구현

