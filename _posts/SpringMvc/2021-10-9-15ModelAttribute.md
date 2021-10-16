# @ModelAttribute

#### @ModelAttribute

- @RequestParam처럼 요청에 들어있는 데이터를 받아오는 방법 중 하나.
- @RequestParam은 단순 타입 데이터로 하나씩 받아오게 설정했다면 ModelAttribute는 복합 객체로 가져올 수 있다.
- 여러 곳에 있는 단순 타입 데이터를 복합 타입 객체로 받아오거나 해당 객체를 새로 만들 때 사용할 수 있다.

- 여러 곳? URI 패스, 요청 매개변수, 세션 등

- 생략 가능

#### 값을 바인딩 할 수 없는 경우에는? 

- BindException 발생 400 에러

#### 바인딩 에러를 직접 다루고 싶은 경우

- BindingResult 타입의 아규먼트를 바로 오른쪽에 추가한다.

#### 바인딩 이후에 검증 작업을 추가로 하고 싶은 경우

- @Valid 또는 @Validated 애노테이션을 사용한다.



```java
@Controller
public class SampleController{
  @GetMapping("/events/form")
  public String eventsForm(Model model){
    Event newEvent = new Event();
    newEvent.setLimit(50);
    model.addAttribute("event", new Event());
    return "events/form";
  }
  
  @PostMapping("/events")
  @ResponsBody
  public Event getEvent(@ModelAttribute Event event){
    return event;
  } 
}
```

```java
@Test
public void deleteEvent() throws Exception {
  mockMvc.perform(post("/events")
                  .param("name", "jungwoo")
                  .param("limit", 20))
    .andDo(print())
    .andExpect(status().isOk())
    .andExpect(jsonPath("name").value("jungwoo"));
    
}
```

```java
  mockMvc.perform(post("/events?name=jungwoo")
                  .param("limit", 20))
```

```java
  mockMvc.perform(post("/events?name=jungwoo&limit=20"))
```

이런 식으로 요청해도 된다.



만약에 limit에 맞지 않는 타입 아규먼트가 들어온다면 에러가 뜰 것이다. 그것 해결 방법은 아래와 같다.

```java
  @PostMapping("/events")
  @ResponsBody
  public Event getEvent(@ModelAttribute Event event, BindingResult bindingResult){
    if(bindingResult.hasErrors()){
      bindingResult.getAllErrors().forEach(c->{
        System.out.println(c.toString());
      })
    }
    return event;
  } 
}
```

이렇게하면 에러 떠서 exception을 뜨지 않고 bindingResult에 바인딩 에러를 담아주고 요청을 처리해 준다.







#### 참고:

● https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-modelattrib-method-args