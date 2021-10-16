

# 외부 설정



외부 설정은 애플리케이션에서 사용되는 여러가지 설정값들을 안 또는 밖에서 설정하는 것.

흔히 볼 수 있는 가장 애플리케이션 설정파일은 application.properties이다.

외부 설정에서 사용할 때는 @Value("${}")를 사용하여 주입받는다.

### 1. 사용할 수 있는 외부 설정

- properties(application.properties)
- YAML
- 환경변수
- 커맨드 라인 아규먼트

### 2. 프로퍼티 우선 순위

1. 유저 홈 디렉토리에 있는 spring-boot-dev-tools.properties

2. 테스트에 있는 @TestPropertySource

   - @TestPropertiesSource(properties = "jungwoo.name=jungwoo")
   - @TestPropertiesSource(location="classpath:/test.properties")

3. @SpringBootTest 애노테이션의 properties 애트리뷰트

   - 메인에 있는 애플리케이션을 빌드하면 src 밑 부분만 빌드를 한다. 
   - 그러나 jar 하나로 패키징 후에 테스트 애플리케이션을 빌드하면 src, test 둘 다 빌드한다.

   - 소스를 빌드할 시점에 /src밑에 있는 부분이 전부 classpath로 들어가고 그 다음 테스트 코드(/test)를 빌드한다. 
   - 즉, 같은 application.properties가 있지만 test가 나중에 돌아가므로 test에 있는 properties가 src에 있는 properties파일을 오버라이딩한다.

4. 커맨드 라인 아규먼트

   - --args

5. SPRING_APPLICATION_JSON (환경 변수 또는 시스템 프로티) 에 들어있는프로퍼티

6. ServletConfig 파라미터

7. ServletContext 파라미터

8. java:comp/env JNDI 애트리뷰트

9. System.getProperties() 자바 시스템 프로퍼티

10. OS 환경 변수

11. RandomValuePropertySource

12. JAR 밖에 있는 특정 프로파일용 application properties

13. JAR 안에 있는 특정 프로파일용 application properties

14. JAR 밖에 있는 application properties

15. JAR 안에 있는 application properties

16. @PropertySource

17. 기본 프로퍼티 (SpringApplication.setDefaultProperties)


### 3. application.properties 우선 순위 (높은게 낮은걸 덮어 씁니다.) 

application.properties를 총 4곳에 넣을 수 있다.

1. file:./config/
   - 현재 프로젝트 루트에서 config 디렉토리를 만든 후 넣는 것.
2. file:./
   - 이 프로젝트 루트에 넣거나. jar파일이라면 jar 파일 실행하는 곳.
3. classpath:/config/
4. classpath:/



### 3. 랜덤값 설정하기

- ${random.*} 

  - ex) jungwoo.age = ${random.int}

  

### 4. 플레이스 홀더(재사용)

- name = jungwoo

- fullName = ${name} kim



### 5. 타입-세이프 프로퍼티 @ConfigurationProperties

- 여러 프로퍼티를 묶어서 읽어올 수 있음

  - @ConfigurationProperties("key값")을 사용

  - 인텔리J 자동 완성은 메타정보의 의해서 되기에 처음 사용하는 의존성은 따로 의존성을 추가해야함.

    ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-configuration-processor</artifactId>
                <optional>true</optional>
            </dependency>
    ```

    

- 빈으로등록해서다른빈에주입할수있음
  - @EnableConfigurationProperties
  - **@Component**
  - @Bean

-  융통성 있는 바인딩(Relexed binding)

  - context-path (케밥)

  - context_path (언드스코어)

  - contextPath (캐멀)

  - CONTEXTPATH

- 프로퍼티 타입 컨버전 

  프로퍼티에서 다 문자열이지만 들어갈때는 타입에 맞게 잘 들어간다.

  - @DurationUnit
    - 시간 정보를 받고 싶을 때

- 프로퍼티 값 검증
  - @Validated
  - JSR-303 (@NotNull, ...) 

- 메타 정보 생성

- @Value

  - SpEL 을 사용할 수 있지만...

  - 위에 있는 기능들은 전부 사용 못합니다.

