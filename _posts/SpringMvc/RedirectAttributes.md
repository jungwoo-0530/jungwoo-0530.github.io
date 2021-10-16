# RedirectAttributes

#### 리다이렉트 할 때 기본적으로 Model에 들어있는 primitive type 데이터는 URI 쿼리 매개변수에 추가된다.

- 스프링 부트에서는 이 기능이 기본적으로 비활성화 되어 있다.
- Ignore-default-model-on-redirect 프로퍼티를 사용해서 활성화 할 수 있다.

 

#### 원하는 값만 리다이렉트 할 때 전달하고 싶다면 RedirectAttributes에 명시적으로 추가할 수 있다.



#### 리다이렉트 요청을 처리하는 곳에서 쿼리 매개변수를 @RequestParam 또는 @ModelAttribute로 받을 수 있다.



#### RedirectAttributes는 Flash Attributes와는 다르게 URI에 쿼리 매개변수로 명시해야 하기에 문자열로 변환이 가능해야한다. 즉, 복합적인 객체를 전달하기에는 어렵다.



```java
   @PostMapping("/events/form/limit")
    public String eventFormLimitSubmit(@Validated @ModelAttribute Event event,
                                      BindingResult bindingResult,
                                       SessionStatus sessionStatus,
                                       RedirectAttributes attributes) {
        if (bindingResult.hasErrors()) {
            return "/events/form-limit";
        }
        sessionStatus.setComplete();
        attributes.addAttribute("name", event.getName());
        attributes.addAttribute("limit", event.getLimit());
        return "redirect:/events/list";
    }
```



리다이렉트할 때, model에 들어있는 데이터가 URI 쿼리 매개변수로 나오게 된다.

<img src="img/image-20211011120356978.png" alt="image-20211011120356978" style="width:50%;" />

전달을 하고 받는 쪽에서는 @PathVariable로 하나씩 받아도 되고 @ModelAttribute로도 받을 수 있다.

```java
@GetMapping("/events/list")
    public String getEvents(@PathVariable String name,
                            @PathVariable Integer limit,
                            Model model,
                            @SessionAttribute LocalDateTime visitTime) {
        System.out.println(visitTime);
        
        Event newEvent = new Event();
        newEvent.setName(name);
        newEvent.setLimit(limit);
        
        Event event = new Event();
        event.setName("spring");
        event.setLimit(10);

        List<Event> eventList = new ArrayList<>();
        eventList.add(event);
        eventList.add(newEvent)
        
        model.addAttribute(eventList);

        return "/events/list";
    }
```

```java
    @GetMapping("/events/list")
    public String getEvents(@ModelAttribute("newEvent") Event event,
                            Model model,
                            @SessionAttribute LocalDateTime visitTime) {
        System.out.println(visitTime);



        Event spring = new Event();
        spring.setName("spring");
        spring.setLimit(10);

        List<Event> eventList = new ArrayList<>();
        eventList.add(spring);
        eventList.add(event);


        model.addAttribute(eventList);

        return "/events/list";
    }
```

@ModelAttribute("newEvent") Event event 여기서 event에 쿼리 매개변수가 객체로 바인딩된다. newEvent는 무엇이냐면 @ModelAttribute Event event 이렇게 사용하면 기본적으로 @ModelAttribute("event") Event event와 같다.

그러면 클래스 애노테이션인 @SessionAttributes("event")때문에 세션에서 event를 먼저 찾으려고하기떄문에 에러가 나온다. 

그래서 newEvent로 이름을 변경하여 사용.

