# Configuration



### 1. 포매터 추가하기



https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#addFormatters-org.springframework.format.FormatterRegistry



```java
//SampleController
package boot.jungwoo.democonfiguration;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class SampleController {

    @GetMapping("/hello")
    public String hello()
    {
        return "hello";
    }
}

```



```java
//SampleControllerTest
package boot.jungwoo.democonfiguration;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.junit.jupiter.api.Assertions.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;

@WebMvcTest
class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        this.mockMvc.perform(get("/hello"))
                .andDo(print())
                .andExpect(content().string("hello"));
    }

}
```





Url path에 있는 값을 다음 처럼 가져올 수 있다.

```
package boot.jungwoo.democonfiguration;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class SampleController {

    @GetMapping("/hello/{name}")
    public String hello(@PathVariable String name)
    {
        return "hello" + name;
    }
}

```



#### Formatter

- Printer: 해당 객체를 (Locale 정보를 참고하여) 문자열로 어떻게 출력할 것인가
- Parser: 어떤 문자열을 (Locale 정보를 참고하여) 객체로 어떻게 변환할 것인가



포매터를 사용해서 문자열을 객체로 받아올 수 있다.



```java
//PersonFormatter
package boot.jungwoo.democonfiguration;

import org.springframework.format.Formatter;

import java.text.ParseException;
import java.util.Locale;

public class PersonFormatter implements Formatter<Person> {

    @Override
    public Person parse(String text, Locale locale) throws ParseException {
        Person person = new Person();
        person.setName(text);
        return person;
    }

    @Override
    public String print(Person object, Locale locale) {
        return object.toString();
    }
```



https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/format/Formatter.html

#### 포매터 추가하는 방법 1

- WebMvcConfigurer의 addFormatters(FormatterRegistry) 메소드 정의

#### 포매터 추가하는 방법 2 

- (스프링 부트 사용시에만 가능 함)

- 해당 포매터를 빈으로 등록



```java
//WebConfig.java
package boot.jungwoo.democonfiguration;

import org.springframework.format.FormatterRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addFormatter(new PersonFormatter);
        
    }
}

```

이런 식으로 포매터를 추가할 수 있다.

컨버터도 등록할 수 있는데, 컨버터는 포매터보다 좀 더 제너릭하다.





# 도메인 클래스 컨버터

이전에 살펴보았던 Formatter은 도메인 클래스에 name으로 맵핑을 했지만 보통은 id로 하게 된다.



![img](https://blog.kakaocdn.net/dn/mOk0S/btqDISpvF8q/YpLDklpm3ldO0wPc9eV2r1/img.png)



 

그럼 이제 id를 받아서 person으로 변경을 한 후에 이름을 출력한다던가 하는 일을 해보자.

 

이 경우 이전과 마찬가지로 Formatter나 Converter를 이용하게 되지만 이번에는 만들지 않고 JPA의 도움을 받아서 만들

 

어보자. 스프링 데이터 JPA가 지원해주는 기능 중에 **도메인 클래스 컨버터 기능**이 있다. 

 

이 기능은 아이디에 해당하는 도메인 클래스로 변환을 해주는 컨버터가 자동으로 등록이 된다.

 



![img](https://blog.kakaocdn.net/dn/balxZ7/btqDJfdEIWj/Dnkl358vWdJGhXe7xKHCjK/img.png)



 

의존성에 h2와 jpa만 추가해준 후 Person 클래스에 @Entity를 붙여준다. 그리고 PersonRepository를 만든다. Id값에서 

 

Entity로 컨버팅 할때 리포지토리를 사용하므로 있어야 한다.

 

 



![img](https://blog.kakaocdn.net/dn/GgbMP/btqDGIVG74S/YskOI9HU3ScM2V20QcFKk1/img.png)



 

------

 



![img](https://blog.kakaocdn.net/dn/c8qVck/btqDF1g66W4/y75i4lxvg7xUP3KfWNOcY1/img.png)



이제 간단한 테스트코드를 만들어서 확인을 해보자. 과연 컨버팅이 되었을까?

 

이 ID에 해당하는 Entity를 못 찾았기 때문이다. 이 Repository에는 findById라는 메서드를 통해 가져오게 되는데 

 



![img](https://blog.kakaocdn.net/dn/b4wE2W/btqDHsrvv4p/8yMCpp3jKmqQvcLOKtxFXk/img.png)



 

이 1에 해당하는 Entity가 없기 때문에 NullPointException이 발생한다. 

 



![img](https://blog.kakaocdn.net/dn/b4JWZk/btqDEZc1eO4/yCUrrSUg2LzEVEQWK9qeI0/img.png)



Person 객체를 하나 만들어서 리포지토리에 저장해주자.

 

그리고 이 savedPerson의 ID를 넣어서 해당 본문이 나오는지 확인해 보자. JPA에 의해서 컨버팅이 되었다면

 

 

 



![img](https://blog.kakaocdn.net/dn/mOk0S/btqDISpvF8q/YpLDklpm3ldO0wPc9eV2r1/img.png)



이와 같은 응답은 "hello seungyeol" 이 나와야 할 것이다.

 

 

 



![img](https://blog.kakaocdn.net/dn/lrAY1/btqDE0bTe4J/XK1YRzUeu8Xx4ID2ipgpD1/img.png)



테스트 케이스가 통과되었으니 별도의 formatter나 converter 없이도 JPA에 의해서 컨버팅이 되는 것을 확인할 수 있었다.

 

 

이처럼 도메인 클래스의 경우 컨버터를 스프링 데이터 JPA가 자동으로 등록해주기 때문에 도메인 클래스용 

 

Formatter 나 Converter를 만들 일은 적다고 보면 된다.



# 핸들러 인터셉터

```java
public interface HandlerInterceptor {
 
    boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception;
 
    void postHandle(
            HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)
            throws Exception;
 
    void afterCompletion(
            HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
            throws Exception;
 
}

```

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#addInterceptors-org.springframework.web.servlet.config.annotation.InterceptorRegistry

1.prehandler

2.요청처리

3.posthandler

4.뷰 렌더링

5.afterCompletion

#### HandlerInterceptor

- 핸들러 맵핑에 설정할 수 있는 인터셉터
- 핸들러를 실행하기 전, 후(아직 랜더링 전) 그리고 완료(랜더링까지 끝난 이후) 시점에 부가 작업을 하고 싶은 경우에 사용할 수 있다.
- 여러 핸들러에서 반복적으로 사용하는 코드를 줄이고 싶을 때 사용할 수 있다.
- 로깅, 인증 체크, Locale 변경 등...

#### boolean preHandle(request, response, **handler**)

- 핸들러 실행하기 전에 호출 됨
- “핸들러"에 대한 정보를 사용할 수 있기 때문에 서블릿 필터에 비해 보다 세밀한 로직을 구현할 수 있다.
- 리턴값으로 계속 다음 인터셉터 또는 핸들러로 요청,응답을 전달할지(true) 응답 처리가 이곳에서 끝났는지(false) 알린다.

#### void postHandle(request, response, **modelAndView**)

- 핸들러 실행이 끝나고 아직 뷰를 랜더링 하기 이전에 호출 됨
- “뷰"에 전달할 추가적이거나 여러 핸들러에 공통적인 모델 정보를 담는데 사용할 수도 있다.
- 이 메소드는 인터셉터 역순으로 호출된다.
- 비동기적인 요청 처리 시에는 호출되지 않는다.

#### void afterCompletion(request, response, handler, ex)

- 요청 처리가 완전히 끝난 뒤(뷰 랜더링 끝난 뒤)에 호출 됨
- preHandler에서 true를 리턴한 경우에만 호출 됨
- 이 메소드는 인터셉터 역순으로 호출된다.
- 비동기적인 요청 처리 시에는 호출되지 않는다.

#### vs 서블릿 필터

- 서블릿 보다 구체적인 처리가 가능하다.
- 서블릿은 보다 일반적인 용도의 기능을 구현하는데 사용하는게 좋다.

#### 참고:

-  https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerInterceptor.html
- https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/AsyncHandlerInterceptor.html
- http://forum.spring.io/forum/spring-projects/web/20146-what-is-the-difference-between-using-a-filter-and-interceptor (스프링 개발자 Mark Fisher의 서블릿 필터와의 차이점에 대한 답변 참고)

# 리소스 핸들러



# HTTP 메시지 컨버터

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#configureMessageConverters-java.util.List

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#extendMessageConverters-java.util.List

#### HTTP 메시지 컨버터

 ● 요청 본문에서 메시지를 읽어들이거나(@RequestBody), 응답 본문에 메시지를 작성할 때(@ResponseBody) 사용한다.

#### 기본 HTTP 메시지 컨버터(어떤 컨버터를 쓸지는 요청 헤더를 봐서 선택된다.)

- 바이트 배열 컨버터 

- 문자열 컨버터
- Resource 컨버터
- Form 컨버터 (폼 데이터 to/from MultiValueMap<String, String>)

여기까지는 기본으로 등록되는 메시지 컨버터, 이 다음부터는 의존성에 추가해야함.

- (JAXB2 컨버터)
- (Jackson2 컨버터)
- (Jackson 컨버터)
- (Gson 컨버터)
- (Atom 컨버터)
- (RSS 컨버터)
-  ...

#### 설정 방법

- 기본으로 등록해주는 컨버터에 새로운 컨버터 추가하기: extendMessageConverters
- 기본으로 등록해주는 컨버터는 다 무시하고 새로 컨버터 설정하기: configureMessageConverters 
- 의존성 추가로 컨버터 등록하기 **(**추천**)**
  - 메이븐 또는 그래들 설정에 의존성을 추가하면 그에 따른 컨버터가 자동으로 등록 된다**. **
  - **WebMvcConfigurationSupport**
  - (이 기능 자체는 스프링 프레임워크의 기능임, 스프링 부트 아님.)



```java
//sampleController

    @GetMapping("/message")
    public String message(@RequestBody String body) {
        return body;
    }
```



```java
//Test
@Test
    public void stringMessage() throws Exception {
        this.mockMvc.perform(get("/message")// message 쪽으로 get요청을 보내고
                .content("hello"))//본문에 hello라고 보낸다.
                .andDo(print())//화면에 출력
                .andExpect(status().isOk())//status가 ok인지 확인
                .andExpect(content().string("hello"));//본문이 hello라고 나오는지 확인
    }
```





참고

● https://www.baeldung.com/spring-httpmessageconverter-rest



## HTTP 메시지 컨버터 JSON

#### 스프링 부트를 사용하지 않는 경우

- 사용하고 싶은 JSON 라이브러리를 의존성으로 추가
- GSON
- JacksonJSON
- JacksonJSON 2

#### 스프링 부트를 사용하는 경우

- 기본적으로 JacksonJSON 2가 의존성에 들어있다.
-  즉**, JSON**용 **HTTP** 메시지 컨버터가 기본으로 등록되어 있다**.**



```java
@GetMapping("/jsonMessage")
    public Person jsonMessage(@RequestBody Person person) {
        return person;
    }
```



```java
    @Test
    public void jsonMessage() throws Exception {
        Person person = new Person();
        person.setId(2021l);
        person.setName("jungwoo");

        String jsonString = objectMapper.writeValueAsString(person);

        this.mockMvc.perform(get("/jsonMessage")
                    .contentType(MediaType.APPLICATION_JSON_UTF8)//서버에 json으로 주고(헤더).
                    .accept(MediaType.APPLICATION_JSON_UTF8)//어떤 json으로 받기를 원한다.
                    .content(jsonString))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.id").value(2019))
                .andExpect(jsonPath("$.name").value("keesun"))
        ;
    }
```



#### 참고

 ● JSON path 문법
 ● https://github.com/json-path/JsonPath ● http://jsonpath.com/



## HTTP 메시지 컨버터 XML



#### OXM(Object-XML Mapper) 라이브러리 중에 스프링이 지원하는 의존성 추가

- JacksonXML
- JAXB



#### 스프링 부트를 사용하는 경우

- 기본으로 **XML** 의존성 추가해주지 않음**.**



#### JAXB 의존성 추가

```xml
        <dependency>
            <groupId>javax.xml.bind</groupId>
            <artifactId>jaxb-api</artifactId>
        </dependency>
        <dependency>
            <groupId>org.glassfish.jaxb</groupId>
            <artifactId>jaxb-runtime</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-oxm</artifactId>
            <version>${spring-framework.version}</version>
        </dependency>
```

jaxb-api : jaxb 인터페이스

jaxb-runtime : jaxb 구현체

spring-oxm : xml을 객체로(언마샬링), 객체를 xml로(마샬링) 변환하는 것을 마샬링 언마샬링이라하는데 그것을 추상화해 놓은 api를 스프링이 제공해주는 것.

#### Marshaller 등록

```java
@Bean
public Jaxb2Marshaller marshaller() {
    Jaxb2Marshaller jaxb2Marshaller = new Jaxb2Marshaller(); jaxb2Marshaller.setPackagesToScan(Event.class.getPackageName()); return jaxb2Marshaller;
}
```

setPackagestoScan : @XmlRootElement가 있어야 jaxb가 스캔을 할 수가 있다. 

```java
package me.whiteship.demobootweb;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement
@Entity
public class Person {

    @Id @GeneratedValue
    private Long id;

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }
}

```



```java
@Test
    public void xmlMessage() throws Exception {
        Person person = new Person();
        person.setId(2019l);
        person.setName("keesun");

      //위 문자를 xml String으로 바꿔야함.
     
      
        StringWriter stringWriter = new StringWriter();
        Result result = new StreamResult(stringWriter);
        marshaller.marshal(person, result);
        String xmlString = stringWriter.toString();

        this.mockMvc.perform(get("/jsonMessage")
                .contentType(MediaType.APPLICATION_XML)
                .accept(MediaType.APPLICATION_XML)
                .content(xmlString))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(xpath("person/name").string("keesun"))
                .andExpect(xpath("person/id").string("2019"))
        ;
    }
```



#### 도메인 클래스에 @XmlRootElement 애노테이션 추가

참고

- Xpath 문법
- https://www.w3schools.com/xml/xpath_syntax.asp
- https://www.freeformatter.com/xpath-tester.html





# 그 밖에 WebMvcConfigurer 설정

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html

#### CORS 설정

- Cross Origin 요청 처리 설정
- 같은 도메인에서 온 요청이 아니더라도 처리를 허용하고 싶다면 설정한다.



#### 리턴 값 핸들러 설정

- 스프링 MVC가 제공하는 기본 리턴 값 핸들러 이외에 리턴 핸들러를 추가하고 싶을 때 설정한다.

#### 아큐먼트 리졸버 설정

- 스프링 MVC가 제공하는 기본 아규먼트 리졸버 이외에 커스텀한 아규먼트 리졸버를 추가하고 싶을 때 설정한다. 뷰 컨트롤러
- 단순하게 요청 URL을 특정 뷰로 연결하고 싶을 때 사용할 수 있다. 비동기 설정
- 비동기 요청 처리에 사용할 타임아웃이나 TaskExecutor를 설정할 수 있다. 뷰 리졸버 설정
- 핸들러에서 리턴하는 뷰 이름에 해당하는 문자열을 View 인스턴스로 바꿔줄 뷰 리졸버를 설정한다.

#### Content Negotiation 설정

- 요청 본문 또는 응답 본문을 어떤 (MIME) 타입으로 보내야 하는지 결정하는 전략을 설정한다.



# 스프링 **MVC** 설정 마무리



#### 스프링 MVC 설정은 즉 DispatcherServlet이 사용할 여러 빈 설정. - HandlerMapper

- HandlerAdapter
- ViewResolver
- ExceptionResolver 

- LocaleResolver
- ...

일일히 등록하려니 너무 많고, 해당 빈들이 참조하는 또 다른 객체들까지 설정하려면... 설정할게 너무 많다.

#### @EnableWebMvc

 \- 애노테이션 기반의 스프링 MVC 설정 간편화
 \- WebMvcConfigurer가 제공하는 메소드를 구현하여 커스터마이징할 수 있다.

#### 스프링 부트

- 스프링 부트 자동 설정을 통해 다양한 스프링 **MVC** 기능을 아무런 설정 파일을 만들지 않아도 제공한다.

- WebMvcConfigurer가 제공하는 메소드를 구현하여 커스터마이징할 수 있다.
- @EnableWebMvc를 사용하면 스프링 부트 자동 설정을 사용하지 못한다.

#### 스프링 MVC 설정 방법

- 스프링 부트를 사용하는 경우에는 application.properties 부터 시작.
- WebMvcConfigurer로 시작
-  @Bean으로 MVC 구성 요소 직접 등록

