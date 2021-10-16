# DataBinder - @InitBiner



#### 특정 컨트롤러에서 바인딩 또는 검증 설정을 변경하고 싶을 때 사용

```java
@InitBinder
public void initEventBinder(WebDataBinder webDataBinder) {
  webDataBinder.setDisallowedFields("id"); 
}
```

모든 요청을 처리하기 전에 이 initEventBinder를 호출하게 된다.

#### 바인딩 설정

- webDataBinder.setDisallowedFields();
  - 받고 싶지 않은 필드값을 걸러낼 수 있다. 블랙리스트 기반, 화이트리스트 기반도 있음.

#### 포매터 설정

- webDataBinder.addCustomFormatter();

#### Validator 설정

 ● webDataBinder.addValidators();

- 1. Validator class 생성 
  2. 사용

```java
//EventValidator
package me.whiteship.demowebmvc;

import org.springframework.validation.Errors;
import org.springframework.validation.Validator;

public class EventValidator implements Validator {
    @Override
    public boolean supports(Class<?> aClass) {
        return Event.class.isAssignableFrom(aClass);
    }

    @Override
    public void validate(Object o, Errors errors) {
        Event event = (Event)o;

        if(event.getName().equalsIgnoreCase("aaa")){
            errors.rejectValue("name", "wrongValue", "the value is not allowed");
        }
    }
}
```

Validator class를 생성한 것이다. Validator를 사용하여 supports, validate를 orverride한다.

supports는 Event class를 검증한다는 것을 설정해주고

validate는 어떠한 내용을 검증할 것인지 설정한다. 지금은 event의 name이 aaa라는 경우에 잘못된 이름이라고 reject할 것이다.

```java
@InitBinder
    public void initEventBinder(WebDataBinder webDataBinder)
    {
        webDataBinder.setDisallowedFields("id");
        webDataBinder.addValidators(new EventValidator());
    }
```

.addValidators()을 사용하여 validator를 사용한다.

또는 우리가 명시적으로 원하는 시점에서만 사용하고 싶다면 

EventValidator class에 @Component를 하여 빈으로 설정하고

해당 컨트롤러 클래스에 @Autowired를 사용하여 주입받은 후에 원하는 메소드에 아래와 같은 코드를 추가한다.

```java
eventValidator.validate(event,bindingResult);
```



#### 특정 모델 객체에만 바인딩 또는 Validator 설정을 적용하고 싶은 경우

- @InitBinder(“event”)
  - 이 이름의 모델 객체를 바인딩 받을 때만 initBinder를 사용할 수 있다.

#### 참고

- https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-initbinder
- https://github.com/spring-projects/spring-petclinic/blob/master/src/main/java/org/springframework/samples/petclinic/owner/PetController.java