---
title:  "Spring Security 세팅"
excerpt: ""

categories:
  - Security
tags:
  - [Spring, Security, Setting]
 
date: 2021-11-04
last_modified_at: 2021-11-04

---



## 0. 키워드

**1. 접근 주체 ( Principal ) : 보호된 대상에 접근하는 클라이언트**

**2. 인증 ( Authentication ) : 현재 유저가 누구인지 확인 / 애플리케이션의 작업을 수행할 수 있는 주체임을 증명하는 과정**

**ex ) HTTP Basic Authentication 폼 로그인**

**3. 인가 ( Authorize ) : 현재 유저가 어떤 서비스, 페이지에 접근할 수 있는 권한이 있는지 검사**

**4. 권한 ( Authorization ) : 인증된 주체가 애플리케이션의 동작을 수행할 수 있도록 허락되있는지를 결정**

## 1. Build.gradle

```
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-security'
}
```

위와 같이 의존성을 추가한다.

참고로 시큐리티를 의존성에 추가만 하여도 다음과 같은 일이 발생한다.

1. 모든 요청은 인증을 거쳐야 자원에 접근 가능
2. 인증 방식은 폼 로그인 방식과 httpBasic로그인 방식 제공
3. 기본 로그인 페이지 제공
4. 기본 계정 한개 제공



## 2. Config

```java
@Configuration
@EnableWebSecurity
@AllArgsConstructor
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
}
```

우선 Config 클래스를 하나 생성해야한다. 각 어노테이션을 보면

1. `@EnableWebSecurity` : 웹보안 활성화를 위한 어노테이션으로 시큐리티를 적용하려면 필수.
2. `@Configuration` : 해당 클래스를 Configuration으로 빈으로 적용하기 위한 필수.



추상 클래스인 `WebSecurityConfigurerAdapter` 은 Security에 대한 필수적인 설정 메서드를 Override할 수 있게 해준다.



```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .authorizeRequests()//인가설정.
                .antMatchers("/admin/**").hasRole("ADMIN")//자동으로 ROLE_이 붙어서 ROLE_ADMIN으로 됨.
                .and()
                .formLogin()//form 로그인 방식 사용.
                .loginPage("/login")//
                .defaultSuccessUrl("/")
                .permitAll()
                .and()
                .logout()
                .logoutSuccessUrl("/")
                .invalidateHttpSession(true)
                .and()
                .exceptionHandling().accessDeniedPage("/users/denied");

    }
```



1. csrf() : csrf는 토큰을 사용하여 사이트간에 위조를 방지하기 위한 것이며 POST, PUT, DELETE 요청에 대해서 보호한다. 또한 rest api 서버라면 session 기반 인증과는 다르게 stateless하기 때문에 서버에 인증정보를 보관하지 않는다. 그래서 굳이 csrf를 작성할 필요가 없다. 
2. authorizeRequests() : 요청에 대한 권한 설정.
3. antMatchers() : 인자로 들어오는 url에 대해서 인증을 이루어져야 한다는 것.
   - .hasRole() : 인자로 들어오는 Role을 가지고 있어야함
   - .authenticated() : 인증이 되어야 한다는 것.
   - .permitAll() : 접근 전부 허용.
   - .denyAll() : 접근 전부 제한.
   - rememberMe() : 로그인한 사용자만 접근 가능. 리멤버 가능.

4. formLogin() : 폼로그인방식을 사용
5. loginPage() : 로그인 페이지 설정.
   - "/login" 사용시 시큐리티가 제공하는 기본 로그인화면으로 이동.
6. defaultSuccessUrl() : 로그인 성공시 이동할 페이지
7. logout() : 로그아웃 사용
8. logoutSuccessUrl() : 로그아웃 성공시 이동할 페이지
9. invalidateHttpSession () : 브라우저 종료시 로그아웃을 해서 로그인했던 모든 정보 삭제.(true)



참고 : 위처럼 HttpSecurity http를 메서드 체이닝을 사용해서 한번에 작성해도 되고 여러번 사용해도 됨.

