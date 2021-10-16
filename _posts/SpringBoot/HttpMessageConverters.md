# HttpMessageConverters



- https://docs.spring.io/spring/docs/5.0.7.RELEASE/spring-framework-reference/web.html#mvc-config-message-converters

#### HTTP 요청 본문을 객체로 변경하거나, 객체를 HTTP 응답 본문으로 변경할 때 사용. 

{“username”:”keesun”, “password”:”123”} <-> User

- @ReuqestBody

- @ResponseBody



#### Class에 @RestController가 붙어있다면 @ResponseBody를 생략해도된다.

- 만약에 @Controller인데 @ResponseBody를 안 붙였다면 ViewNameResolver를 사용해서 그 리턴에 해당하는 이름의 view를 찾으려고 할 것이다.

- @ResponseBody가 있다면 바로 messageConverter를 타서 응답 본문으로 내용이 들어가게 된다.

  

우리가 어떤 요청을 받았는지 어떤 응답을 보낼 것인지에 따라서 HttpMessageConverter가 달라진다.

요청에서 content-type이 json이고 json본문이 들어온 경우, json message converter가 사용된다.

```java
    @PostMapping("/user")
    public @ResponseBody User create(@RequestBody User user) {
        return null;
    }
```

@ResponseBody를 사용하여 User가 리턴되는데 User를 Convert해서 리턴한다. 이런 composition type인 경우에 기본적으로 json으로 리턴한다.

```java
  @PostMapping("/user")
    public @ResponseBody String create(@RequestBody User user) {
        return null;
    }
```

String일 경우에는 String message converter가 사용된다.



```java
    @PostMapping("/users/create")
    public User create(@RequestBody User user){
        return user;
    }
```

위와 같이 PostMapping을 하고 여기서는 UserController 컨트롤러가 @RestController이기 때문에, 리턴에 @ResponseBody를 생략하였다. 그래서 리턴으로 user 객체를 json으로 변환이 잘 되는지 확인하기 위해서 테스트 코드를 작성한다.

```java
@Test
    public void createUser_JSON() throws Exception {
        String userJson = "{\"username\":\"jungwoo\",\"password\":\"123\"}";
        mockMvc.perform(post("/users/create")
                .contentType(MediaType.APPLICATION_JSON_VALUE)
                .accept(MediaType.APPLICATION_JSON_VALUE)
                .content(userJson))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.username",
                        is(equalTo("jungwoo"))))
                .andExpect(jsonPath("$.password",
                        is(equalTo("123"))));

    }
```

테스트 코드로 contentType을 사용하여 content의 타입을 json으로, accept를 사용하여 요청 헤더 타입을 json으로 설정한다. 또한 content 본문을 userJson으로 설정한다. eqaulTo를 사용하여 jungwoo와 123이 같은지 요청을 보낸다. 