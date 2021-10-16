# @ModelAttribute 또 다른 사용법



#### @ModelAttribute의 다른 용법

- @RequestMapping을 사용한 핸들러 메소드의 아규먼트에 사용하기 (이전 post한 게시물)
- @Controller 또는 @ControllerAdvice (이 애노테이션은 뒤에서 다룹니다.)를 사용한 클래스에서 모델 정보를 초기화 할 때 사용한다.
- @RequestMapping과 같이 사용하면 해당 메소드에서 리턴하는 객체를 모델에 넣어 준다.
  -  RequestToViewNameTranslator 





#### @ModelAttribute 메소드

```java
@ModelAttribute
public void subjects(Model model) {
  model.addAttribute("subject", List.of("study", "seminar", "hobby", "social")); 
}
```

Model라는 메소드 아규먼트를 설정하여 모델 정보를 담아 줄 수 있다. 그 모델 정보를 뷰에서 참조하여 렌더링을 한다.

어떤 공통적인 참고해야하는 정보라면 모든 핸들러에 쓰는 것 보다 @ModelAttribute을 사용하여 모든 요청에 이 정보를 주고 싶을 때 사용한다.

위와 같이 subject라는 이름으로 study,seminar,hobby,social을 넣는 것.

리턴으로 줄 수도 있다.

```java
@ModelAttribute("subject")
public List<String> subjects(Model model) {
  return List.of("study", "seminar", "hobby", "social"); 
}
```

---------------------------------------------------------------------------------------------------------------------



```java
    @GetMapping("/events/form/name")
    @ModelAttribute
    public Event eventsFormName() {
        return new Event();
    }
```

이렇게하면 리턴하는 객체를 모델에 바로 담아주는 것. @ModelAttribute을 생략해도 된다. 그렇다면 view 이름은?

RequestToViewNameTranslator라는 인터페이스가 이 요청에 있는 /events/form/name과 정확히 일치하는 view 이름으로 된다.