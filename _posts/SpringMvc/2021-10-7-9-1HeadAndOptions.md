---
title:  "HEAD와 OPTIONS 요청 처리"
excerpt: ""

categories:
  - SpringMvc
tags:
  - [Spring, Web, Mvc]
 
date: 2021-10-07
last_modified_at: 2021-10-07
---



# HEAD와 OPTIONS 요청 처리



우리가 구현하지 않아도 스프링 웹 MVC에서 자동으로 처리하는 HTTP Method

- HEAD
- OPTIONS 

#### HEAD

- GET 요청과 동일하지만 응답 본문을 받아오지 않고 응답 헤더만 받아온다.

#### OPTIONS

- 사용할 수 있는 HTTP Method 제공
- 서버 또는 특정 리소스가 제공하는 기능을 확인할 수 있다.
- 서버는 Allow 응답 헤더에 사용할 수 있는 HTTP Method 목록을 제공해야 한다.

#### 참고

- https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html
- https://github.com/spring-projects/spring-framework/blob/master/spring-test/src/test/java/org/springframework/test/web/servlet/samples/standalone/resultmatchers/HeaderAssertionTests.java



```java
    @Test //junit은 public void로 선언해야한다.
    public void helloTest() throws Exception {
        mockMvc.perform(head("/hello")
                        .param("name", "jungwoo"))
                .andDo(print())
                .andExpect(status().isOk());

    }
```



Head로 요청을 보내면 

<img src="../img/image-20211007015033477.png" alt="image-20211007015033477" style="width:50%;" />

응답으로 Body는 빠진 상태로 응답이 온 것을 알 수 있다.







```java
@GetMapping("/hello")
@ResponseBody
public String hello(){
    return "hello";
}

@PostMapping("/hello")
@ResponseBody
public String helloPost(){
    return "hello";
}
```

이렇게 핸들러를 설정하면 Get, Post, Head, Options를 지원한다.



```java
@Test //junit은 public void로 선언해야한다.
    public void helloTest() throws Exception {
        mockMvc.perform(options("/hello"))
                .andDo(print())
                .andExpect(status().isOk());

    }
```



<img src="/assets/images/image-20211007015457267.png" alt="image-20211007015457267" style="width:55%;" />

Allow 응답 헤더에 사용할 수 있는 HTTP 메소드를 보여준다.



```java
    @Test //junit은 public void로 선언해야한다.
    public void helloTest() throws Exception {
        mockMvc.perform(options("/hello"))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(header().stringValues(HttpHeaders.ALLOW,
                        hasItems(containsString("GET"),
                                containsString("POST"),
                                containsString("HEAD"),
                                containsString("OPTIONS"))
                                ));

    }
}
```

헤더에 GET,POST,HEAD,OPTIONS가 있는지 확인 할 때.



