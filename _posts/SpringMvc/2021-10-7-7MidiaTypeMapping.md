# 미디어 타입 맵핑

#### 특정한 타입의 데이터를 담고 있는 요청만 처리하는 핸들러

- @RequestMapping(**consumes**=MediaType.APPLICATION_JSON_UTF8_VALUE)
- Content-Type 헤더로 필터링
- 매치 되는 않는 경우에 415 Unsupported Media Type 응답

#### 특정한 타입의 응답을 만드는 핸들러

- @RequestMapping(**produces**=”application/json”)
- Accept 헤더로 필터링 (하지만 살짝... 오묘함)
- 매치 되지 않는 경우에 406 Not Acceptable 응답'





#### 문자열을 입력하는 대신 MediaType을 사용하면 상수를 (IDE에서) 자동 완성으로 사용할 수 있다.



#### 클래스에 선언한 @RequestMapping에 사용한 것과 조합이 되지 않고 메소드에 사용한 @RequestMapping의 설정으로 덮어쓴다.



#### Not (!)을 사용해서 특정 미디어 타입이 아닌 경우로 맵핑 할 수도 있다.





```java
package mvc.jungwoo.demowebmvc;

import org.springframework.http.MediaType;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
public class SampleController {

    @GetMapping(value = "/hello", consumes = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public String hello(){
        return "hello";
    }
}
```

<img src="/assets/images/image-20211006125151887.png" alt="image-20211006125151887" style="width:80%;" />

이런 문자열이 컨텐츠 타입 헤더에 들어가 있는 경우에 이 핸들러를 사용하게 된다.

서버 쪽에 컨텐츠 타입을 알려주는 것. 저것만 사용할것이다라고.



```java
    @Test //junit은 public void로 선언해야한다.
    public void helloTest() throws Exception {
        mockMvc.perform(get("/hello")
                        .contentType(MediaType.APPLICATION_JSON_VALUE))
                .andDo(print())
                .andExpect(status().isOk());

    }
```



<img src="/assets/images/image-20211006125519046.png" alt="image-20211006125519046" style="width:60%;" />

<img src="/assets/images/image-20211006125538741.png" alt="image-20211006125538741" style="width:60%;" />



정상적으로 설정한 컨텐츠 타입이 헤더에 있는 것을 알 수가 있다.



```java
   @Test //junit은 public void로 선언해야한다.
    public void helloTest() throws Exception {
        mockMvc.perform(get("/hello")
                        .contentType(MediaType.APPLICATION_JSON_VALUE)
                        .accept(MediaType.APPLICATION_JSON_VALUE))
                .andDo(print())
                .andExpect(status().isOk());

    }
```

```java
@Controller
public class SampleController {

    @GetMapping(value = "/hello", 
            consumes = MediaType.APPLICATION_JSON_VALUE,
            produces = MediaType.TEXT_PLAIN_VALUE)
    @ResponseBody
    public String hello(){
        return "hello";
    }
}
```



보내는 쪽에서 accept를 줘서 필터링을 하고

요청은 json으로 하지만 응답으로는 Text를 받는 다고 특정하게 지정하였다.

약간 오묘하다..



```java
@Controller
@RequestMapping(consumes = MediaType.APPLICATION_XML_VALUE)
public class SampleController {

    @GetMapping(value = "/hello",
            consumes = MediaType.APPLICATION_JSON_VALUE,
            produces = MediaType.TEXT_PLAIN_VALUE)
    @ResponseBody
    public String hello(){
        return "hello";
    }
}

```

이렇게 클래스에서 xml만 요청만 처리한다고하고 핸들러는 JSON 요청을 처리한다고 했을 때는 핸들러가 클래스에 있는 설정을 오버라이딩해서 JSON 요청을 처리한다.



