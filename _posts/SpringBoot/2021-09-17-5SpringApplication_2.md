---
title:  "SpringApplication2 "
excerpt: "ApplicationEvent,WebApplicationType 설정, ApplicationArguments, ApplicationRunner"

categories:
  - Spring-Boot
tags:
  - [Spring-Boot, Web, Mvc]
 
date: 2021-09-17
last_modified_at: 2021-09-17
---



# SpringApplication-2



### 1. ApplicationEvent





```java
//EventListener class
package boot.jungwoo.application;

import org.springframework.boot.context.event.ApplicationStartingEvent;
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;

@Component
public class SampleListener implements ApplicationListener<ApplicationStartingEvent> {

    @Override
    public void onApplicationEvent(ApplicationStartingEvent event) {
        System.out.println("=======================");
        System.out.println("Application is starting");
        System.out.println("=======================");
    }
}

```



**Event Listener가 빈이라면 ApplicationContext가 만들어진 다음에 발생하는 이벤트들은 가능하나, ApplicationContext가  만들어지기 이전에 발생한 이벤트들은 Listener가 동작을 안한다.** 

-> 이런 경우에는 직접 빈을 등록해줘야한다.

 

```java
package boot.jungwoo.application;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(Application.class);
        app.addListeners(new SampleListener());
        app.run(args);
    }

}
```



그 결과 정상적으로 출력이 된다.

<img src="/assets/images/image-20211002092554933.png" alt="image-20211002092554933" style="width:30%;" />



리스터를 만들 때, 시점을 정하는 여러가지 제너릭 파라미터가 존재한다. 아래와 같은 여러가지 제너릭 파라미터가 존재한다.

Application events are sent in the following order, as your application runs:

1. An `ApplicationStartingEvent` is sent at the start of a run but before any processing, except for the registration of listeners and initializers.
2. An `ApplicationEnvironmentPreparedEvent` is sent when the `Environment` to be used in the context is known but before the context is created.
3. An `ApplicationContextInitializedEvent` is sent when the `ApplicationContext` is prepared and ApplicationContextInitializers have been called but before any bean definitions are loaded.
4. An `ApplicationPreparedEvent` is sent just before the refresh is started but after bean definitions have been loaded.
5. An `ApplicationStartedEvent` is sent after the context has been refreshed but before any application and command-line runners have been called.
6. An `AvailabilityChangeEvent` is sent right after with `LivenessState.CORRECT` to indicate that the application is considered as live.
7. An `ApplicationReadyEvent` is sent after any [application and command-line runners](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.spring-application.command-line-runner) have been called.
8. An `AvailabilityChangeEvent` is sent right after with `ReadinessState.ACCEPTING_TRAFFIC` to indicate that the application is ready to service requests.
9. An `ApplicationFailedEvent` is sent if there is an exception on startup.





### 2. WebApplicationType 설정

`SERVLET`이 없고 `REACTIVE`로 동작하지만 `SERVLET`이 있으면 무조건 `SERVLET`로 동작함
Spring MVC가 들어있으면 기본적으로 `SERVLET`로 동작함
Spring Webfulx가 들어있으면 기본적으로 `REACTIVE`로 동작함
`SERVLET`, `REACTIVE`둘다 없으면 NONE로 동작



```java
package boot.jungwoo.application;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.WebApplicationType;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(Application.class);
        app.setWebApplicationType(WebApplicationType.SERVLET);
        app.run(args);
    }
}	
```



### 3. ApplicationArguments

아규먼트를 주는 방법은 두가지가 존재한다.

<img src="/assets/images/image-20211002094931329.png" alt="image-20211002094931329" style="width:30%;" />

위와 같이 project Configuration에서 주는 방법

또한 터미널에서 주는 방법은 아래와 같다.



<img src="/assets/images/image-20211002095650247.png" alt="image-20211002095650247" style="width:50%;" />

<img src="/assets/images/image-20211002095711386.png" alt="image-20211002095711386" sstyle="width:30%;" />



mvn을 패키징하고 --jungwoo 아규먼트를 주고 실행한다.



```java
package boot.jungwoo.application;

import org.springframework.boot.ApplicationArguments;
import org.springframework.stereotype.Component;


@Component
public class SampleListener{

    public SampleListener(ApplicationArguments arguments)
    {
        System.out.println("args로 들어온 값 확인.");
        System.out.println("jungwoo: " + arguments.containsOption("jungwoo"));
    }
}

```



<img src="/assets/images/image-20211002095307672.png" alt="image-20211002095307672" style="width:20%;" />

정상적으로 들어온 것을 알 수 있다.



## 4. ApplicationRunner

애플리케이션 실행한 뒤 뭔가 실행하고 싶을 때, ApplicationRunner(추천), CommandLineRunner를 사용할 수 있다.

```java
package boot.jungwoo.application;

import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;


@Component
public class SampleListener implements ApplicationRunner
{
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("args로 들어온 값 확인.");
        System.out.println("jungwoo: " + args.containsOption("jungwoo"));
    }
}
```



<img src="/assets/images/image-20211002100134140.png" alt="image-20211002100134140" style="width:30%;" />



ApplicationRunner는 JVM option을 아예 받지 못하고 아규먼트만 받을 수 있다.

또한 ApplicationRunner가 여러 개라면 @Order(숫자)를 사용하여 ApplicationRunner의 순서를 정할 수 있다. 참고로 낮은 숫자가 우선순위가 높다.