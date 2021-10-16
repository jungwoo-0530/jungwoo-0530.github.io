# 연습문제

다음 요청을 처리할 수 있는 핸들러 메소드를 맵핑하는 @RequestMapping (또는

@GetMapping, @PostMapping 등)을 정의하세요.

1. GET /events 

2.  GET /events/1,

   GET /events/2, 

   GET /events/3, ...

3. POST /events CONTENT-TYPE: application/json ACCEPT: application/json 

4. DELETE /events/1,

   DELETE /events/2,

   DELETE /events/3, ...

5. PUT /events/1 CONTENT-TYPE: application/json ACCEPT: application/json, 

   PUT /events/2 CONTENT-TYPE: application/json ACCEPT: application/json, ...



```java
package mvc.jungwoo.practice1webmvc;

import org.springframework.http.MediaType;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
public class EventHandler {

    @GetMapping("/events")
    @ResponseBody
    public String eventGet(){
        return "event";
    }

    @GetMapping("/events/{id}")
    @ResponseBody
    public String eventGet(@PathVariable String id){
        return "event";
    }

    @PostMapping(value = "/events",
            consumes = MediaType.APPLICATION_JSON_VALUE,
            produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public String eventCreate(){
        return "event";
    }

    @DeleteMapping("/events/{id}")
    @ResponseBody
    public String eventDelete(@PathVariable String id){
        return "event";
    }

    @PutMapping (value = "/events/{id}",
            consumes = MediaType.APPLICATION_JSON_VALUE,
            produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public String eventUpdate(@PathVariable String id){
        return "event";
    }



}
```

