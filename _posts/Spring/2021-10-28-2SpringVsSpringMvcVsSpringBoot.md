---
title:  "Spring Vs SpringMvc Vs SpringBoot"
excerpt: ""

categories:
  - Spring
tags:
  - [Spring, Web, Mvc, Boot]
 
date: 2021-10-28
last_modified_at: 2021-10-28
---



우선 Spring Vs Spring Mvc를 비교하기 전에 간단한 상식.

Web Server

- 예) 아파치
- 정적인 파일을 사용

Web Application Server

- 예) 톰캣
- 동적인 파일을 사용



----

### Spring



Servlet 인터페이스, Servlet을 구현한 구현체가 HttpServlet

요청이 들어올 때마다 쓰레드를 생성해서 Servlet 구현체(HttpServlet)와 연결.

```java
public class HelloServlet extends HttpServlet
```

서블릿 인터페이스에 init, service, destory가 정의되어있음.

init은 말 그대로 서블릿 인스턴스를 생성

실제 기능이 실행되는 것이 service

HttpServlet에 doGet(),doPost(),doPut(),doDelete()가 정의 되어있음. Http 메서드에 따라서 웹 컨테이너가 호출해줌. 그 내용은 우리가 개발.

static,Member 변수들은 쓰레드끼리 공유.

Local 변수는 독립적.



요청이 들어오면 Web.xml을 참조하여 그에 맞는 servlet과 매핑시켜준다.

```xml
<Web-app>
  <servlet>
    <servlet-name>member</servlet-name>
    <servlet-class>servlets.MemberServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>member</servlet-name>
    <url-pattern>/member</url-pattern>
  </servlet-mapping>
</Web-app>
```

위와 같이 /member 리퀘스트가 들어오면 이름이 같은 servlet인 member를 찾아서 그 class인 MemberServlet으로 매핑해준다.

```java
public class MemberServlet extends HttpServlet{
  protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
    String username = req.;
  }
}
```



즉, 정리하면 URL마다 web.xml에 매핑을 시켜준다. 그러면 URL마다 요청이 각각의 클라이언트로 부터 들어온다.

/memeber라는 요청이 처음으로 들어온다면 member 서블릿 구현체로 매핑시켜주고 비니지스 로직을 처리한 후 리스폰해준다.



1. web.xml에 URL마다 서블릿 구현체를 매핑시켜준다.
2. 어떤 클라이언트로부터 요청이 들어온다면 웹 컨테이너(톰켓)는 web.xml을 바탕으로 해당 클라이언의 HttpServletRequest, HttpServletResponse 객체를 알맞은 서블릿 구현체로 연결시켜준다.
3. 해당 서블릿 구현체가 처음 실행된 것이라면 init후에 service(비지니스 로직)이 실행되고 처음이 아니라면 바로 service()로 실행.
4. 비지니스 로직을 한 후에 바로 view를 생성하여 클라이언트에게 response해준다.



--------------

### spring mvc

mvc에서는 서블릿을 DispatcherServlet로 사용한다.

mvc가 아닌 

Web.xml에 각각의 요청에 대한 알맞는 서블릿을 매핑을 시켜주지 않고 

모든 요청을 Dispatcher Servlet으로 매핑시켜준다.

```xml
<servlet>
  <servlet-name>dispatcher</servlet-name>
  <serlvet-class>org.springframework.web.servlet.DispatcherServlet</serlvet-class>
  <load-on-startup>1</load-on-startup>
</servlet>
<servelt-mapping>
  <servlet-name>dispatcher</servlet-name>
  <url-pattern>/</url-pattern>
</servelt-mapping>
```

모든 요청이 들어오면 dispatcherservlet으로 보낸다.

이 때, web.xml에서 servlet으로 등록을 하면 /WEB-INF/서블릿이름-servlet.xml으로 파일이 자동으로 생성된다.

즉, 해당 Servlet 설정파일이 만들어진다. 위에서는 dispatcher-servlet.xml이 만들어진다.

이 xml에서 여러 설정을 하면되는데 여기서 HandlerMapping 방식을 정해준다.



처음으로 HandlerMapping으로 컨트롤러 클래스를 찾아준다.

컨트롤러를 찾는 방법은 spring framework가 4가지 방법을 제공한다

1. BeanNameHandlerMapping

   - Bean 이름과 Url을 Mapping하는 방식

   - ```xml
     <beans>
       <bean name="/employee" class="com.easycompany.controller.EmployeeController"
             ...
     </beans>
     ```

     

   - /employee로 접근하면 위 class(Controller)로 mapping

2. ControllerClassNameHandlerMapping

3. SimpleUrlHandlerMapping

4. DefaultAnnotationHandlerMapping

   - 우리가 익숙하게 사용하는 방법인 애노테이션을 사용하는 방법
   - class에 @Controller를 사용하고 메소드에 @RequestMapping("/Employee")를 사용하여 매핑

이 4가지 중에 하나를 골라 사용한다



컨트롤러를 클래스 찾았으면 컨트롤러 호출(메서드 호출)은 HandlerAdapter가 호출.

즉, HandlerMapping이 클래스를 찾아주고 그 클래스에서 HandlerAdapter가 메서드를 찾아준다.

HandlerAdapter에서 ModelAndView형태로 바꿔준다. 보통 view를 String타입으로 주기 때문에

ViewResolver가 그 이름을 가지고 실제 view를 찾아줌.

컨트롤러에서 만든 데이터인 모델을 view에 심어줌

그 다음에는 클라이언트에게 response



즉, dispatcher servlet -> HandlerMapping -> HandlerAdapter -> ViewResolve -> 해당 view를 클라이언트에게 response

HandlerMapping, HandlerAdapter, ViewResolve는 모두 dispatcher-servlet.xml에 정의되어 있다.

아래는 초밥 개발자님 블로그에서 퍼온 dispatcher-servlet.xml이다.

이러한 dispatcher-servlet.xml을 WebConfig 자바 파일로도 할 수 있다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
                           http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">

    <context:component-scan base-package="me.bosuksh.springmvc" />
    <mvc:annotation-driven />
    <bean class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping" />
    <bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter" />

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```



추가로 @RequsetMapping, @GetMapping, @PostMapping ...의 차이.

- Spring 4.3 버전부터 Spring MVC 컨트롤 메소드를 위한 새로운 어노테이션 5개 추가

  - @PostMapping
  - @GetMapping
  - @PutMapping
  - @DeleteMapping
  - @PatchMapping
  - 새롭게 추가됨으로써 HttpMethods에 맵핑된다.

- ```java
  @RequestMapping(value = "/member", method = {RequsetMethod.POST})
  ```

- ```java
  @PostMapping("/member")
  ```

- 위 두개는 같은 것. 짧게 줄여줄 수 있어서 명확한 코드.



Spring과 Spring Mvc에는 우리에게 큰 이득을 준다.

- Spring에서는 요청마다 서블릿을 만들어서 사용했다면 Spring Mvc에서는 dispatcher servlet인 servlet하나로 1개이다.
- 또한 Spring에서는 서블릿마다 Web.xml에 작성하여 servlet을 관리하였지만 Mvc에서는 Web.xml에 dispatcher하나만 있기 때문에 관리가 편하다고 볼 수 있다.
- 추가로 Spring의 Servlet에서 View로 보내 주는 것까지 직접 다 만들었지만 Spring Mvc에서는 View를 분리시키는 효과를 보았기에 효율적으로 개발을 할 수 있다.



-----------------------

### Spring Boot



