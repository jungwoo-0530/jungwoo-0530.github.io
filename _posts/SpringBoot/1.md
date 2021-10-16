# Spring-Boot

스프링 부트 프로젝트 생성기 :  https://start.spring.io



## 의존성 관리



## 자동설정



#### 1. 덮어쓰기 방지

- @ConditionalOnMissingBean을 starter 빈에다 설정.

#### 2. 빈 재정의 안하고 설정.

- 1. @ConfigurationProperties("class name")을 strater에 property에 설정
  2. @EnableConfigurationProperties("property 이름.class")을 Configuration class에 설정
  3. 자동설정 사용하는 프로젝트에서 프로퍼티 키값을 작성 후 실행 -> 자동 완성

```java
@Autowired

```



