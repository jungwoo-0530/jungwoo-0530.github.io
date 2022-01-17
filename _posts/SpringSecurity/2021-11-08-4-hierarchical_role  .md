---
title:  "hierarchical Role(계층형 권한)"
excerpt: ""

categories:
  - Security

tags:
  - [Spring, Security, hierarchical, role]
 
date: 2021-11-08
last_modified_at: 2021-11-08

---





계층형 권한이 필요한 이유 ?

- admin, provider, member
- 계층형 권한이 있으면 provider가 member의 권한을 가지므로 member가 할 수 있는 것들을 모두 인가 받을 수 있음.
- 중복적인 코드가 필요없다.



## 1. RoleHierarchy



직접 계층형 권한을 구현할 필요가 없다.

이미 `RoleHierarchy`라는 인터페이스가 존재하고, `RoleHierarchyImpl`라는 `RoleHierarchy`인터페이스 구현체 클래스도 존재한다. 우리는 단지 끌어와서 사용만 하면된다.



WebSecurityConfig.java

```java
    @Bean
    RoleHierarchy roleHierarchy() {
        RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
        roleHierarchy.setHierarchy("ROLE_ADMIN > ROLE_PROVIDER > ROLE_MEMBER");
        return roleHierarchy;
    }
```

위와 같이 구현체를 가져와서 빈으로 등록만 해주면 알아서 계층형 권한을 만들어준다.

`RolehierarchyImpl`가 제공하는 메소드인 `setHierarchy()`를 사용하면 된다.

