# **@ResponseBody & ResponseEntity**

#### @ResponseBody

- 데이터를 HttpMessageConverter를 사용해 응답 본문 메시지로 보낼 때 사용한다.
- @RestController 사용시 자동으로 모든 핸들러 메소드에 적용 된다.

#### ResponseEntity

- 응답 헤더 상태 코드 본문을 직접 다루고 싶은 경우에 사용한다.

#### 참고

- https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-responsebody
- https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-responseentity





```java
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;

@RestController
@RequestMapping("/api/events")
public class EventApi {

    @PostMapping
    public Event createEvent(@RequestBody @Valid Event event, BindingResult bindingResult) {
        //save event
        if(bindingResult.hasErrors()){
            bindingResult.getAllErrors().forEach(error->{
                System.out.println(error);
            });
        }
        return event;
    }
}
```

@ResponseBody를 메소드에 사용하면 이 메소드가 리턴하는 값을 Http 응답 본문에 담아주는데, 담아 줄 때 HttpMessageConverter를 사용하여 리턴하는 값을 응답 본문에 있는 값으로 Convert

브라우저에서 기본적으로 accept 헤더에 html, xml 등을 원한다는 accept 헤더가 들어간다. 그렇지 않은 경우에는 우리가 콘솔에서 curl을 사용하거나 postman을 사용하는 경우에는 json으로 응답을 준다.





```java
package org.springframework.web.bind.annotation;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {
    @AliasFor(
        annotation = Controller.class
    )
    String value() default "";
}
```

@RestContontroller가 붙어있는 경우에는 그 메소드들을 @ResponseBody를 생략해도 된다. 왜냐하면 @RestContontroller에 이미 있기 때문이다.



```java
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;

@RestController
@RequestMapping("/api/events")
public class EventApi {

    @PostMapping
    public ResponseEntity<Event> createEvent(@RequestBody @Valid Event event, BindingResult bindingResult) {
        //save event
        if(bindingResult.hasErrors()){
            ResponseEntity.badRequest().build;
        }
        return ResponseEntity.ok(event);
    }
}
```

ResponseEntity은 응답 헤더 상태 코드 본문을 직접 다루고 싶은 경우에 사용한다.

