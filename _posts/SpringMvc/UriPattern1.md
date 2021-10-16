# URI 패턴 맵핑 



#### URI, URL, URN 햇갈린다

 ● https://stackoverflow.com/questions/176264/what-is-the-difference-between-a-uri-a-url-and-a-urn

#### 요청 식별자로 맵핑하기

- @RequestMapping은 다음의 패턴을 지원합니다.
- ?: 한 글자 (“/author/???” => “/author/123”)
-  * : 여러 글자 (“/author/*” => “/author/keesun”)
- ** : 여러 패스 (“/author/** => “/author/keesun/book”)

#### 클래스에 선언한 @RequestMapping과 조합

- 클래스에 선언한 URI 패턴뒤에 이어 붙여서 맵핑합니다.

#### 정규 표현식으로 맵핑할 수도 있습니다. 

- /{name:정규식}

#### 패턴이 중복되는 경우에는?

 ● 가장 구체적으로 맵핑되는 핸들러를 선택합니다.

#### URI 확장자 맵핑 지원

- 이 기능은 권장하지 않습니다. (스프링 부트에서는 기본으로 이 기능을 사용하지 않도록 설정 해 줌) 
  - 보안 이슈 (RFD Attack)
  - URI 변수, Path 매개변수, URI 인코딩을 사용할 때 할 때 불명확 함.

#### RFD Attack

 ● https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/reflected-file-download-a-new-web-attack-vector/
 ● https://www.owasp.org/index.php/Reflected_File_Download

 ● https://pivotal.io/security/cve-2015-5211





```java
 @GetMapping("/hello", "/hi")
```

한가지로 맵핑 뿐만 아니라 여러가지로 맵핑이 가능하다.



```java
 @GetMapping("/hello?")
```

이런식으로 작성하면 /hello는 실패, 무조건 한글자가 뒤에 붙어야한다. 즉, /helloo는 성공.

```java
 @GetMapping("/hello??")
```

뒤에 두글자가 무조건 와야한다.

```java
 @GetMapping("/hello/?")
```

중간에 path가 들어있다면 이렇게.



```java
@Controller
@RequestMapping("/hello")
public class SampleController {

    @GetMapping("/**")
    @ResponseBody
    public String hello(){
        return "hello";
    }
}
```

이런식으로 조합하여 작성 가능 -> /hello/**으로 맵핑



```java
@Controller
@RequestMapping("/hello")
public class SampleController {

    @GetMapping("/{name:[a-z]+}")
    @ResponseBody
    public String hello(@PathVariable String name){
        return "hello " + name;
    }
}
```

```java
@Test //junit은 public void로 선언해야한다.
    public void helloTest() throws Exception {
        mockMvc.perform(get("/hello/jungwoo"))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(content().string("hello jungwoo"));
```

위는 정규식을 사용한 것. 정규식 사용에 hello에 @PathVariable을 사용하여 name을 받는다.



```java
@Controller
@RequestMapping("/hello")
public class SampleController {

    @GetMapping("/jungwoo")
    @ResponseBody
    public String hello(@PathVariable String name){
        return "hello jungwoo";
    }
    @GetMapping("/**")
    @ResponseBody
    public String hello(@PathVariable String name){
        return "hello";
    }
}
```

패턴이 중복되도 가장 구체적으로 맵핑되는 핸들러를 자동으로 사용하게 된다. 즉, 여기서는 /hello/jungwoo가 맵핑되어 hello jungwoo가 바디에 나온다.

```java
    @Test //junit은 public void로 선언해야한다.
    public void helloTest() throws Exception {
        mockMvc.perform(get("/hello/jungwoo"))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(content().string("hello jungwoo"))
                .andExpect(handler().handlerType(SampleController.class))
                .andExpect(handler().methodName("hellojungwoo"));

    }
```

이런 식으로 어떤 핸들러를 사용했는지 알 수 있다.



```java
@GetMapping({"/jungwoo", "/jungwoo.*"})
```

우리가 /jungwoo만 맵핑을 하여도 암묵적으로 /jungwoo.*도 맵핑을 해준다. 예를 들어 /hello/jungwoo.json이런 것도 맵핑을 해준다.

스프링 부트에는 이 기능을 사용하지 않도록 설정이 되어있따. 보안이슈와 불명확하므로



