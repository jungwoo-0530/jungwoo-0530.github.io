# 헤더와 매개변수



#### 특정한 헤더가 있는 요청을 처리하고 싶은 경우

- @RequestMapping(**headers** = “key”) 특정한 헤더가 없는 요청을 처리하고 싶은 경우

- @RequestMapping(**headers** = “**!**key”)



####  특정한 헤더 키/값이 있는 요청을 처리하고 싶은 경우

- @RequestMapping(**headers** = “key=value”)



####  특정한 요청 매개변수 키를 가지고 있는 요청을 처리하고 싶은 경우 

- @RequestMapping(**params** = “a”) 특정한 요청 매개변수가 없는 요청을 처리하고 싶은 경우
- @RequestMapping(**params** = “**!**a”)



#### 특정한 요청 매개변수 키/값을 가지고 있는 요청을 처리하고 싶은 경우 ● @RequestMapping(**params** = “a=b”)





```java
@WebMvcTest
class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test //junit은 public void로 선언해야한다.
    public void helloTest() throws Exception {
        mockMvc.perform(get("/hello")
                .header(HttpHeaders.FROM,"localhost"))
                .andDo(print())
                .andExpect(status().isOk());

    }
}
```

 test에서 요청을 from으로 줬다.



```java

@Controller
public class SampleController {

    @GetMapping(value = "/hello", headers = HttpHeaders.FROM)
    @ResponseBody
    public String hello(){
        return "hello";
    }
}

```

이 컨트롤러가 이 헤더만 들어가있는 요청만 처리하겠다는 것.



```java
@GetMapping(value = "/hello", headers = "!" + HttpHeaders.FROM)
```

not을 붙여서 FROM헤더를 제외하고 요청을 처리.



```java
 @GetMapping(value = "/hello", headers = HttpHeaders.FROM + "=" + "localhost")
```

헤더가 정확한 값인 localhost로 들어올 때 처리



```java
// /hello?name=jungwoo 이런식으로 name이라는 param 들어와야한다

@GetMapping(value = "/hello", params = "name")
```



```java
@Test //junit은 public void로 선언해야한다.
public void helloTest() throws Exception {
    mockMvc.perform(get("/hello")
                    .param("name", "jungwoo"))
            .andDo(print())
            .andExpect(status().isOk());

}
```

param 설정이 정상적으로 되는 것으 알 수 있다.





```java
@GetMapping(value = "/hello", params = "name=kimjungwoo")
```

이번에는 param값이 정확한 값으로 들어올 때 사용하는 것.

즉 /hello?name=kimjungwoo 로 들어와야 요청 처리





