---
title:  "ExceptionHandler"
excerpt: ""

categories:
  - Spring-Boot
tags:
  - [Spring-Boot, Web, Mvc]
 
date: 2021-10-17
last_modified_at: 2021-10-17
---



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
  - template나 static에 error 디렉토리를 만들어서
  - 에러 상태코드 값과 같은 이름으로 html만들기.
- 404.html
  - 404번 에러를 처리하는 정적인 페이지
- 5xx.html
  - 500번대 발생하는 에러를 처리하는 정적인 페이지
- ErrorViewResolver 구현



```java
//SampleException.class java
package com.example.demo;


public class SampleException extends RuntimeException {
}

```

```java
//AppError.class java
package com.example.demo;

public class AppError {

    private String message;

    private String reason;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public String getReason() {
        return reason;
    }

    public void setReason(String reason) {
        this.reason = reason;
    }
}
```



```java
package com.example.demo;


import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class SampleController {

    @GetMapping("/hello")
    public String hello(){
        throw new SampleException();
    }

    @ExceptionHandler(SampleException.class)
    public @ResponseBody AppError sampleError(SampleException e){
        AppError appError = new AppError();
        appError.setMessage("error.app.key");
        appError.setReason("IDK IDK IDK");
        return appError;
    }

}
```

이 SampleController class안에서 SampleException이라는 예외가 발생하면 이 핸들러를 사용하겠다는 것.



/hello를 요청을 보내면 만들어 놓은 SampleException 에러가 발생하고 이 에러를 처리하는 Exception 핸들러인 AppError가 실행된다.

에러 정보를 e로 받아와 AppError에서 처리를 한다.

이 커스텀한 에러 핸들러는 이 컨트롤러 안에서만 가능하고 전역적으로 하고싶다면 @ControllerAdvice를 붙인 클래스를 새로 만든다.





```java
@Controller
@RequestMapping({"${server.error.path:${error.path:/error}}"})
public class BasicErrorController extends AbstractErrorController 
```

${server.error.path:${error.path:/error}}이 부분은 오른쪽에 key값에 해당하는 error.path가 있으면 이 프로퍼티를 사용하고 없으면 /error를 사용하겠다는 문법.

또 프로퍼티에 server.error.path가 정의되어있으면 server.error.path를 사용하고 아니면 오른쪽값을 사용.

결국에는 우리가 정의한 것이 없으니 /error를 사용하는 것.





