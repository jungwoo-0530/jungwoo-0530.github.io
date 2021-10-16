# HtmlUnit

#### Html을 테스트하기 위한 툴.

#### HTML 템플릿 뷰 테스트를 보다 전문적으로 하자.

- http://htmlunit.sourceforge.net/
- http://htmlunit.sourceforge.net/gettingStarted.html
- 의존성추가

```xml
<dependency>
   <groupId>org.seleniumhq.selenium</groupId>
   <artifactId>htmlunit-driver</artifactId>
   <scope>test</scope>
</dependency>
<dependency>
  <groupId>net.sourceforge.htmlunit</groupId>
  <artifactId>htmlunit</artifactId>
  <scope>test</scope>
</dependency>
```

- @Autowire WebClient



```java
@WebMvcTest(SampleController.class)
class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        // 요청 "/hello"
        // 응답
        // - 응답 모델 name : jungwoo
        // - 뷰 이름 : hello
        mockMvc.perform(get("/hello"))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(view().name("hello"))
                .andExpect(model().attribute("name", is("jungwoo")))
                .andExpect(content().string(containsString("jungwoo")));
    }

}
```

위와 같은 테스트를 아래와 같이 테스트를 만들 수 있다.

```java
@WebMvcTest(SampleController.class)
class SampleControllerTest {


    @Autowired
    WebClient webClient;

    @Test
    public void hello() throws IOException {
        HtmlPage page = webClient.getPage("/hello");
        HtmlHeading1 h1 = page.getFirstByXPath("//h1");
        assertThat(h1.getTextContent()).isEqualToIgnoringCase("jungwoo");
    }

}
```

