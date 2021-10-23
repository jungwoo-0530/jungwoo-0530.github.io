# 기본 키 매핑

#### 기본 키 매핑 어노테이션

- @Id

- @GeneratedValue



#### 기본 키 매핑 방법

- 직접 할당: **@Id**만 사용

- 자동 생성(**@GeneratedValue**)
  - **IDENTITY**: 데이터베이스에 위임, MYSQL
  - **SEQUENCE**: 데이터베이스 시퀀스 오브젝트 사용, ORACLE
    - @SequenceGenerator 필요

- **TABLE**: 키 생성용 테이블 사용, 모든 DB에서 사용
  - @TableGenerator 필요
- **AUTO**: 방언에 따라 자동 지정, 기본값



#### 직접 할당

- **@Id** **사용**



#### IDENTITY 전략 - 특징

- 기본 키 생성을 데이터베이스에 위임

  - 기본 키를 우리가 insert하면 안된다.
  - DB에서 null로 insert쿼리가 날라오면 그 때 값을 세팅해준다.
  - 그래서 기본 키를 알기 위해서는 DB에 들어간 후에 알 수 있다.
    - 영속성 컨텍스트에 관리될려면 PK값이 무조건 있어야한다.
    - **그래서 예외적으로 .persist()를 한 시점에 insert 쿼리를 날린다.**

- 주로 MySQL, PostgreSQL, SQL Server, DB2에서 사용  

  (예: MySQL의 AUTO_ INCREMENT)

- JPA는 보통 트랜잭션 커밋 시점에 INSERT SQL 실행

- AUTO_ INCREMENT는 데이터베이스에 INSERT SQL을 실행한 이후에 ID 값을 알 수 있음

- IDENTITY 전략은 em.persist() 시점에 즉시 INSERT SQL 실행 하고 DB에서 식별자를 조회

```java
@Entity
public class Member{
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
}
```



#### SEQUENCE 전략 - 특징

- 데이터베이스 시퀀스는 유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트(예: 오라클 시퀀스)

- 오라클, PostgreSQL, DB2, H2 데이터베이스에서 사용

```java
@Entity
@SequenceGenerator(
			name = "MEMBER_SEQ_GENERATOR",
			sequenceName = "MEMBER_SEQ",
			initialValue = 1, allocationSize = 1)
public class Member{
  @Id
  @GeneratedValue(strategy = GenerationType.SEQUENCE,
                 generator = "MEMBER_SEQ_GENERATOR")
  private Long id;
}
```

sequenceName은 매핑할 데이터 베이스 시퀀스 이름

- int : primitive 자료형
- Integer : Wrapper 클래스(객체)
  - null 값 처리가 용이하기 때문에 SQL과 연동할 경우에 처리를 원할하게 할 수 있음.
- **Long 타입을 쓰는 것을 권장.**



#### SEQUENCE - @SequenceGenerator

- 주의: allocationSize 기본값 = 50 
- allocationSize가 50, initialValue 1로 설정해두면 처음 persist()할 때 1을하고 메모리에 50까지 확보하는 쿼리를 날려 메모리에 올려놓는다.
  - 그러면 다음 객체를 persist할 때는 메모리에 올라와져있는 50을 그대로 사용하여 2를 준다.
  - 50을 다 사용했다면 다음번에 100번까지 메모리에 확보를하는 쿼리를 날린다.

| 속성            | 설명                                                         | 기본값             |
| --------------- | ------------------------------------------------------------ | ------------------ |
| name            | 식별자 생성기 이름                                           | 필수               |
| sequenceName    | 데이터베이스에 등록되어 있는 시퀀스 이름                     | hibernate_sequence |
| initialValue    | DDL 생성 시에만 사용됨, 시퀀스 DDL을 생성할 때 처음 1 시작하는 수를 지정한다. | 1                  |
| allocationSize  | 시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용됨 **데이터베이스 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값 을 반드시** 1**로 설정해야 한다** | **50**             |
| catalog, schema | 데이터베이스 catalog, schema 이름                            |                    |





#### TABLE 전략

- 키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략

- 장점: 모든 데이터베이스에 적용 가능
- 단점: 성능

```java
create table MY_SEQUENCES (
	sequence_name varchar(255) not null,
	next_val bigint,
	primary key ( sequence_name )
)
```



```java
@Entity
@TableGenerator(
			name = "MEMBER_SEQ_GENERATOR",
			table = "MY_SEQUENCES"
			pkColumnValue = "MEMBER_SEQ",
			allocationSize = 1)
public class Member{
  @Id
  @GeneratedValue(strategy = GenerationType.TABLE,
                 generator = "MEMBER_SEQ_GENERATOR")
  private Long id;
}
```



#### @TableGenerator - 속성

| 속성                   | 설명                                                  | 기본값              |
| ---------------------- | ----------------------------------------------------- | ------------------- |
| name                   | 식별자 생성기 이름                                    | 필수                |
| table                  | 키생성 테이블명                                       | hibernate_sequences |
| pkColumnName           | 시퀀스 컬럼명                                         | sequence_name       |
| valueColumnNa          | 시퀀스 값 컬럼명                                      | next_val            |
| me pkColumnValue       | 키로 사용할 값 이름                                   | 엔티티 이름         |
| initialValue           | 초기 값, 마지막으로 생성된 값이 기준이다.             | 0                   |
| allocationSize         | 시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용됨) | **50**              |
| catalog, schema        | 데이터베이스 catalog, schema 이름                     |                     |
| uniqueConstraints(DDL) | 유니크 제약 조건을 지정할 수 있다.                    |                     |



#### 권장하는 식별자 전략

- **기본 키 제약 조건** : null 아님, 유일, **변하면 안된다**.
- 미래까지 이 조건을 만족하는 자연키는 찾기 어렵다. 대리키(대체키)를 사용하자.
  - 자연키 : 주민번호, 전화번호
  - 대리기(대체키) : 비지니스와 관련없는 키
- 예를 들어 주민등록번호도 기본 키로 적절하기 않다. 
- **권장: Long형 + 대체키 + 키 생성전략 사용**

