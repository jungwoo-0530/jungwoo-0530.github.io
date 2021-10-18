---
title:  "인메모리 데이터베이스"
excerpt: ""

categories:
  - Spring-Boot
tags:
  - [Spring-Boot, Web, Mvc]
 
date: 2021-10-18
last_modified_at: 2021-10-18
---

# 인메모리 데이터베이스

- 인메모리 데이터베이스(In-memory Database)란?
  - 데이터 스토리지의 메인 메모리에 설치되어 운영되는 방식의 데이터베이스 관리 시스템이다. 디스크에 설치되는 방식에 비해 처리 속도가 빠르다.
  - 디스크가 아닌 주 메모리에 모든 데이터를 보유하고 있는 데이터베이스. 디스크 검색보다 자료 접근이 훨씬 빠른 것이 가장 큰 장점이다. 데이터 양의 빠른 증가로 데이터베이스 응답 속도가 떨어지는 문제를 해결할 수 있는 대안이 인 메모리 데이터베이스이다. 전형적인 디스크 방식은 디스크에 저장된 데이터를 대상으로 쿼리를 수행하지만, 인 메모리 방식은 메모리상에 색인을 넣어 필요한 모든 정보를 메모리상의 색인을 통해 빠르게 검색할 수 있다
  - 휘발성이기에 보통 Test용으로 사용.

#### 지원하는 인-메모리 데이터베이스

- **H2 (**추천**,** 콘솔 때문에**...)**

- HSQL

- Derby

  

#### Spring-JDBC가 클래스패스에 있으면 자동 설정이 필요한 빈을 설정 해줍니다.

- DataSource
- JdbcTemplate

#### 인-메모리 데이터베이스 기본 연결 정보 확인하는 방법

- URL: “testdb”

- username: “sa”
- password: “”



#### H2 콘솔 사용하는 방법

- spring-boot-devtools를 추가하거나...
- spring.h2.console.enabled=true 만 추가.
- /h2-console로 접속 (이 path도 바꿀 수 있음)

#### 실습 코드

- CREATE TABLE USER (ID INTEGER NOT NULL, name VARCHAR(255), PRIMARY KEY (id))
- INSERT INTO USER VALUES (1, ‘jungwoo’)

---



#### 3개 의존성 추가

- Web, H2 Database, JDBC API



```java
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.Statement;

@Component
public class H2Runner implements ApplicationRunner {

    @Autowired
    DataSource dataSource;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        try(Connection connection = dataSource.getConnection()){
            System.out.println(connection.getMetaData().getURL());
            System.out.println(connection.getMetaData().getUserName());

            Statement statement = connection.createStatement();
            String sql = "CREATE TABLE USER(" +
                    "ID INTEGER NOT NULL," +
                    " name VARCHAR(255)," +
                    " PRIMARY KEY (id))";
            statement.executeUpdate(sql);
        }
    }
}
```

기본적으로 DataSource가 빈으로 설정이 되어있어서 주입받아서 사용할 수 있다.

Connection에 메타 정보가 들어있다.

간단한 테이블을 만드는 sql을 만든 후, 실행하면

jdbc:h2:mem:3af7fad6-a461-4001-83bf-bca70db11971
SA

위와 같은 결과가 나온다.

스프링 부트 2.3부터 변경된 내용중 하나인데 스프링 부트 기본 인메모리 DB 설정이 매번 새로운 이름으로 만들어 지도록 2.3부터 변경되서 그렇습니다. testdb로 고정하고 싶으시다면 다음 설정을 application.properties에 추가해야한다.

- spring.datasource.generate-unique-name=false

 

H2콘솔을 사용하는 방법은 

//application.properties

```
spring.h2.console.enabled=true
```

설정하고 localhost:8080/h2-console을 접속하면 아래와 같이 나온다.

<img src="/assets/images/image-20211017095432520.png" alt="image-20211017095432520" style="zoom:50%;" />

.getURL()한 값을 JDBC URL에 넣어주고 UserName을 확인한다.

그런 다음 Connect를 눌러준다.

<img src="/assets/images/image-20211017095534580.png" alt="image-20211017095534580" style="zoom:50%;" />

정상적으로 접속이 된다. 아까 생성한 USER Table을 확인할 수 있다.

```java
@Component
public class H2Runner implements ApplicationRunner {

    @Autowired
    DataSource dataSource;

    @Autowired
    JdbcTemplate jdbcTemplate;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        try(Connection connection = dataSource.getConnection()){
            System.out.println(connection.getMetaData().getURL());
            System.out.println(connection.getMetaData().getUserName());

            Statement statement = connection.createStatement();
            String sql = "CREATE TABLE USER(" +
                    "ID INTEGER NOT NULL," +
                    " name VARCHAR(255)," +
                    " PRIMARY KEY (id))";
            statement.executeUpdate(sql);
        }
        jdbcTemplate.execute("INSERT INTO USER VALUES (1, 'jungwoo')");
    }
}
```

jdbcTemplate도 주입받을 수 있다. 주입받은 후에 테이블에 INSERT한다.

jdbctemplate를 사용하면 좀 더 간결하게 sql을 던질 수 있다. 예외를 던질 때, 좀 더 가독성이 높은 에러 메시지를 볼 수 있다. 기본 jdbc api를 사용하는 것보다 좋다.

