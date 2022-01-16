# QueryDsl 설정 방법

ㅣ

### 1. build.gradle

build.gradle로 설정을 한다.

```build.gradle
dependencies{
    // Querydsl
    implementation 'com.querydsl:querydsl-jpa'

    // Querydsl JPAAnnotationProcessor 사용 지정
    annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jpa"
    // java.lang.NoClassDefFoundError(javax.annotation.Entity) 발생 대응
    annotationProcessor "jakarta.persistence:jakarta.persistence-api"
    // java.lang.NoClassDefFoundError(javax.annotation.Generated) 발생 대응
    annotationProcessor "jakarta.annotation:jakarta.annotation-api"
}
```

Annotation Processor 방식을 사용. ``com.ewerk.gradle.plugins.querydsl`` 플로그인을 사용하지 않는 방법.



QClass가 생성되는 위치는 설정하는 방법에 따라서 두 곳으로 나뉜다.

**Intellij IDEA > Build > Build tools > Gradle > Build and run using**을 Gradle으로 설정하느냐 IntelliJ로 설정하느냐에 따라서 나뉜다.

1. Gradle로 설정

- build/generated/sources/annotationProcessor/java/main에 QClass 생성

2. IntelliJ로 설정

- src/main/generated에 QClass 생성



### 2. QuerydslConfig

```java
@EnableJpaAuditing
@Configuration
public class QuerydslConfig {

    @PersistenceContext
    private EntityManager entityManager;

    public QuerydslConfig() {
    }

    //JPAQueryFactory를 빈으로 등록함으로써 repository에서 바로 가져와서 사용.
    //JPAQueryFactory가 우리가 작성한 것을 토대로 entityManager를 통하여 질의.
    @Bean
    public JPAQueryFactory jpaQueryFactory(){
        return new JPAQueryFactory(entityManager);
    }

}
```

 `JPAQueryFactory`를 빈으로 설정하여 어느 곳이든 바로 가져와서 사용할 수 있게 빈으로 설정한다.



