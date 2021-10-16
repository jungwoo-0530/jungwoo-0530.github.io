# 멀티 폼 서브밋

#### 세션을 사용해서 여러 폼에 걸쳐 데이터를 나눠 입력 받고 저장하기 ● 이벤트 이름 입력받고

- 이벤트 제한 인원 입력받고
- 서브밋 -> 이벤트 목록으로! 

#### 완료된 경우에 세션에서 모델 객체 제거하기

- SessionStatus



```java
    @GetMapping("/events/form/name")
    public String eventsFormName(Model model) {
        model.addAttribute("event", new Event());
        return "/events/form-name";
    }
```

eventFormName메소드에서 event 이름으로 모델에 속성을 추가했으므로 @SessionAttributes("event")에 의해서 세션에 저장된다. model 객체를 /event/form-name에서 사용할 것이다.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <title>Create New Event</title>
</head>
<body>
<form action="#" th:action="@{/events/form/name}" method="post" th:object="${event}">
  <p th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Incorrect date</p>
  Name: <input type="text" title="name" th:field="*{name}"/>
  <input type="submit" value="Create"/>
</form>

</body>
</html>
```

이 뷰에서 object="${event}"이 객체가 결국은 세션에 저장되는 것. 이름을 입력받는다. form 태그에 action 속성은 폼 데이터를 서버로 보낼 때, 해당 데이터가 도착할 URL을 명시한다. 즉, 서버에  /events/form/name을 보내면 핸들러에서 /events/form/name에 맵핑한다.

```java

    @PostMapping("/events/form/name")
    public String eventFormNameSubmit(@Validated @ModelAttribute Event event,
                                      BindingResult bindingResult) {
        if (bindingResult.hasErrors()) {
            return "/events/form-name";
        }
        return "redirect:/events/form/limit";
    }
```

eventFormNameSubmit에 @ModelAttribute Event event에 위에서 입력받은 동일한 객체를 사용한다. ModelAttribute가 세션에 있는 정보도 바인딩을 받기 떄문에.

이름을 받은 객체가 문제가 있으면 /events/form-name 뷰를 다시 보여줄 것이고 이상이 없다면 /events/form/limit로 리다이렉트 한다.

```java
    @GetMapping("/events/form/limit")
    public String eventsFormLimit(@ModelAttribute Event event, Model model) {
        model.addAttribute("event", event);
        return "/events/form-limit";
    }
```

/events/form/limit로 리다이렉트 되었기에 GetMapping된다. 위와 똑같이 @ModelAttribute를 사용하여 세션에 저장된 동일한 event객체를 사용한다. 객체를 모델에 넣고 /events에 있는 form-limit.html form으로 이동.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Create New Event</title>
</head>
<body>
<form action="#" th:action="@{/events/form/limit}" method="post" th:object="${event}">
    <p th:if="${#fields.hasErrors('limit')}" th:errors="*{limit}">Incorrect date</p>
    Limit: <input type="text" title="limit" th:field="*{limit}"/>
    <input type="submit" value="Create"/>
</form>

</body>
</html>
```

여기서는 limit을 입력받고 액션으로 /events/form/limit을 URL에 명시.

```java
    @PostMapping("/events/form/limit")
    public String eventFormLimitSubmit(@Validated @ModelAttribute Event event,
                                      BindingResult bindingResult,
                                       SessionStatus sessionStatus) {
        if (bindingResult.hasErrors()) {
            return "/events/form-limit";
        }
        sessionStatus.setComplete();
        return "redirect:/events/list";
    }

```

나머지는 위와 동일한 절차를 진행하고 모든 입력이 완료되었다면 세션을 종료시킨다. 그리고 마지막으로 리다이렉트시켜서 끝낸다.

