# @Validated

#### 스프링 MVC 핸들러 메소드 아규먼트에 사용할 수 있으며 validation group이라는 힌트를 사용할 수 있다. 

#### @Valid 애노테이션에는 그룹을 지정할 방법이 없다. 

#### @Validated는 스프링이 제공하는 애노테이션으로 그룹 클래스를 설정할 수 있다.



```java
public class Event{
  
  interface ValidateLimit{}
  interface ValidateName{}
  
  @NotBlan(groups = ValidateNmae.class)
  private String name;
  
  @Min(value = 0, groups = ValidateLimit.class)
  private Interger limit;
  
  //세터,게터 생략
  
}

```

위와 같이 validation group을 설정한다.



```java
  @PostMapping("/events")
  @ResponsBody
  public Event getEvent(@Validated(Event.ValidateLimit.class) @ModelAttribute Event event, BindingResult bindingResult){
    if(bindingResult.hasErrors()){
      bindingResult.getAllErrors().forEach(c->{
        System.out.println(c.toString());
      })
    }
    return event;
  } 
}
```

@Validated(Event.ValidateLimit.class)은 ValidateLimit을 사용해서 검증을 하겠다라는 것.

이런 검증은 이벤트를 설계하는 도중에 하는 것이 좋다.





