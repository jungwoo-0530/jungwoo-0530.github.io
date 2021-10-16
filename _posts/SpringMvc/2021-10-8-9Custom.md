# 커스텀 애노테이션

#### @RequestMapping 애노테이션을 메타 애노테이션으로 사용하기

- @GetMapping 같은 커스텀한 애노테이션을 만들 수 있다.

#### 메타(Meta) 애노테이션

- 애노테이션에 사용할 수 있는 애노테이션
- 스프링이 제공하는 대부분의 애노테이션은 메타 애노테이션으로 사용할 수 있다.

#### 조합(Composed) 애노테이션

- 한개 혹은 여러 메타 애노테이션을 조합해서 만든 애노테이션
- 코드를 간결하게 줄일 수 있다.
- 보다 구체적인 의미를 부여할 수 있다.

#### @Retention

- 해당 애노테이션 정보를 언제까지 유지할 것인가.
- Source: 소스 코드까지만 유지. 즉, 컴파일 하면 해당 애노테이션 정보는 사라진다는 이야기.
- Class: 컴파인 한 .class 파일에도 유지. 즉 런타임 시, 클래스를 메모리로 읽어오면 해당 정보는 사라진다.
- Runtime: 클래스를 메모리에 읽어왔을 때까지 유지! 코드에서 이 정보를 바탕으로 특정 로직을 실행할 수 있다. 

#### @Target

- 해당 애노테이션을 어디에 사용할 수 있는지 결정한다. 

  

#### @Documented

- 해당 애노테이션을 사용한 코드의 문서에 그 애노테이션에 대한 정보를 표기할지 결정한다.
- 즉, 문서화할 때 사용.



#### 메타 애노테이션

● https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beansmeta-annotations

● https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/annotation/AliasFor.html







```java
package mvc.jungwoo.demowebmvc;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@RequestMapping(method = RequestMethod.GET, value = "/hello")//애노테이션 만들때는 GetMapping을 사용하지 못한다.
public @interface GetHelloMapping {
}
```

우리가 자주 사용하는 애노테이션을 이렇게 생성할 수 있다.

이렇게만 작성하고 

```java
    @GetHelloMapping
    @ResponseBody
    public String hello(){
        return "hello";
    }
```

 이렇게 실행한다면 될까? 안된다. 런타임시 @GetHelloMapping이 사라지게 된다.

그래서 저 @GetHelloMapping을 언제까지 유지할 것인지 @Retention을 사용하여 명시를 하여야 한다.

@Retention은 스프링 내용보다도 자바내용이며 자바에서 언제까지 유지할 것인지 명시할 수 있다. 



```java

@Retention(RetentionPolicy.CLASS)
@RequestMapping(method = RequestMethod.GET, value = "/hello")//애노테이션 만들때는 GetMapping을 사용하지 못한다.
public @interface GetHelloMapping {
}
```

RetentionPolicy.CLASS 이 말은 컴파일이 되어도 이 애노테이션 정보다 .CLASS에 정보가 들어있다는 것.



바이트코드를 클래스로더가 메모리로 읽어올때 애노테이션 기본값으로 모두 사라진다. 스프링은 런타임시에 애노테이션이 참고를 해야한다. 그래서 스프링은 리텐션이 반드시 런타임이여야한다.

```javascript
package mvc.jungwoo.demowebmvc;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
@RequestMapping(method = RequestMethod.GET, value = "/hello")//애노테이션 만들때는 GetMapping을 사용하지 못한다.
public @interface GetHelloMapping {
}

```

이런 식으로 설정해야 정상적으로 애노테이션이 작동된다.



```java
package mvc.jungwoo.demowebmvc;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@RequestMapping(method = RequestMethod.GET, value = "/hello")//애노테이션 만들때는 GetMapping을 사용하지 못한다.
public @interface GetHelloMapping {
}

```

@Target을 설정해서 메소드에 사용할 수 있도록 설정.

여러 곳을 원한다면 배열로 설정.



