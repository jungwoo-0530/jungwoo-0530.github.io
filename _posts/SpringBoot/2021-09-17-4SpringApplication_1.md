---
title:  "SpringApplication - 로그레벨설정, 배너설정"
excerpt: ""

categories:
  - Spring-Boot
tags:
  - [Spring-Boot, Web, Mvc]
 
date: 2021-09-17
last_modified_at: 2021-09-17
---



# SpringApplication-1





```java
SpringApplication.run(class명.class, args);
```

보통 main class에서 SpringApplication을 실행 할 때 위 한 줄을 추가한다.

위와 같이 사용한다면 SpringApplication이 제공하는 다양한 기능을 커스터마이징해서 사용하기 어렵다.

```java
SpringApplication app = new SpringApplication(class명.class);
app.run(args);
```

따라서 SpringApplication의 여러 기능들을 입맛에 맞게 사용하고자 할 때는 위와 같이 Spring Application **인스턴스를 생성**해 실행하는 편이 좋다.



### 1. 로그 레벨 설정

기본적인 로그 레벨은 INFO레벨이다.

레벨 설정하는 방법은 

<img src="/assets/images/image-20211002065547245.png" alt="image-20211002065547245" style="width:33%;" />

실행하는 옵션에서 Configurations를 들어가 VM options에 -Ddebug를 설정한다면 디버그 레벨로 설정이 된다.

실행하면 아래와 같이 

<img src="/assets/images/image-20211002065802665.png" alt="image-20211002065802665" style="width:45%;" /> 

디버그 레벨로 찍히는 것을 알 수 있다.

또한 디버그 레벨로 실행하면 어떤 자동설정이 적용이 되고 어떠한 설정이 적용안됐는지 알 수가 있다.



### 2. 배너

https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.spring-application

<img src="/assets/images/image-20211002070251090.png" alt="image-20211002070251090" style="width:30%;" />

111

배너는 어플리케이션 실행시 나오는 배너를 말한다. 

**배너 설정 방법은 총 4가지 존재한다.**



변경하는 방법은 리소스에 **banner.txt | gif | jpg | png** 파일을 만든 후 띄우고 싶은 배너를 작성 후 실행하면 배너가 적용이 된다.

<img src="/assets/images/image-20211002070448167.png" alt="image-20211002070448167" style="width:30%;" />

만약 다른 위치에 Banner를 만들고싶으면 **application.properties** 파일에

```null
spring.banner.location=classpath:디렉토리\banner.txt
```



또한 banner.txt에 쓸 수 있는 변수들이 있다. 스프링 버전, 어플리케이션 버전 등등을 출력할 수 있다.

| Variable                                                     | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `${application.version}`                                     | The version number of your application, as declared in `MANIFEST.MF`. For example, `Implementation-Version: 1.0` is printed as `1.0`. |
| `${application.formatted-version}`                           | The version number of your application, as declared in `MANIFEST.MF` and formatted for display (surrounded with brackets and prefixed with `v`). For example `(v1.0)`. |
| `${spring-boot.version}`                                     | The Spring Boot version that you are using. For example `2.5.5`. |
| `${spring-boot.formatted-version}`                           | The Spring Boot version that you are using, formatted for display (surrounded with brackets and prefixed with `v`). For example `(v2.5.5)`. |
| `${Ansi.NAME}` (or `${AnsiColor.NAME}`, `${AnsiBackground.NAME}`, `${AnsiStyle.NAME}`) | Where `NAME` is the name of an ANSI escape code. See [`AnsiPropertySource`](https://github.com/spring-projects/spring-boot/tree/v2.5.5/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/ansi/AnsiPropertySource.java) for details. |
| `${application.title}`                                       | The title of your application, as declared in `MANIFEST.MF`. For example `Implementation-Title: MyApp` is printed as `MyApp`. |



**class로 배너를 구현** 할 수도 있다.

```java
@SpringBootApplication
public class SpringApplication1Application {
    public static void main(String[] args) {
        SpringApplication app 
            = new SpringApplication(SpringApplication1Application.class);

        app.setBanner(((environment, sourceClass, out) -> {
            out.println("====================");
            out.println("spring boot jungwoo)");
            out.println("====================");
        }));
        app.run(args);
    }
}
```



**SpringApplicationBuilder**로 빌더 패던 사용으로 만들 수 있다.