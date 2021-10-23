# 필드와 컬럼 매핑



#### 요구사항 추가

1. 회원은 일반 회원과관리자로 구분해야 한다.

2. 회원 가입일과 수정일이 있어야 한다.

3. 회원을 설명할 수 있는필드가 있어야 한다.이 필드는 길이제한이 없다.



```java
package hellojpa;

import javax.persistence.*;
import javax.persistence.criteria.CriteriaBuilder;
import java.util.Date;

@Entity
public class Member {

    @Id//PK 맵핑
    private long id;

    @Column(name = "name")//객체는 username , DB에는 name
    private String uesrname;

    private Integer age;

    //DB에는 EnumType이 없음, 그래서 Enumerated 애노테이션을 사용하면된다.
    @Enumerated(EnumType.STRING)
    private RoleType roleType;
    //DB에는 DATE,TIME, TIMESTAMP를 구분해서 사용.
    @Temporal(TemporalType.TIMESTAMP)//
    private Date createDate;

    @Temporal(TemporalType.TIMESTAMP)
    private Date lastModifiedDate;
    //varchar를 넘어서 큰 컨텐츠를 넣고 싶으면 Lob을 사용.
    @Lob
    private String description;

    public Member(){

    }

}

```

<img src="img/image-20211024022851661.png" alt="image-20211024022851661" style="width:50%;" />

String, @Lob을 사용하면 clob으로 매핑.



#### 매핑 어노테이션 정리

hibernate.hbm2ddl.auto

| 어노테이션  | 설명                                                         |
| ----------- | ------------------------------------------------------------ |
| @Column     | 컬럼 매핑                                                    |
| @Temporal   | 날짜 타입 매핑                                               |
| @Enumerated | enum 타입 매핑                                               |
| @Lob        | BLOB, CLOB 매핑                                              |
| @Transient  | 특정 필드를 컬럼에 매핑하지 않음(매핑 무시), DB와 관계없이 메모리에서만 사용하고 싶을 때 |



#### @Column

| 속성                   | 설명                                                         | 기본값                                                   |
| ---------------------- | ------------------------------------------------------------ | -------------------------------------------------------- |
| name                   | 필드와 매핑할 테이블의 컬럼 이름                             | 객체의 필드 이름                                         |
| insertable, updatable  | 등록, 변경 가능 여부                                         | TRUE(변경가능)                                           |
| **nullable(DDL)**      | null 값의 허용 여부를 설정한다. false로 설정하면 DDL 생성 시에 not null 제약조건이 붙는다. |                                                          |
| unique(DDL)            | @Table의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제약조건을 걸 때 사용한다. |                                                          |
| columnDefinition (DDL) | 데이터베이스 컬럼 정보를 직접 줄 수 있다.   ex) varchar(100) default ‘EMPTY' | 필드의 자바 타입과 방언 정보를 사용해서 적절한 컬럼 타입 |
| length(DDL)            | 문자 길이 제약조건, String 타입에만 사용한다.                | 255                                                      |
| precision, scale(DDL)  | BigDecimal 타입에서 사용한다(BigInteger도 사용할 수 있다). precision은 소수점을 포함한 전체 자릿수를, scale은 소수의 자릿수 다. 참고로 double, float 타입에는 적용되지 않는다. 아주 큰 숫자나 정밀한 소수를 다루어야 할 때만 사용한다. | precision=19, scale=2                                    |



#### **@Enumerated**

**자바** **enum** **타입을 매핑할 때 사용**

**주의! ORDINAL 사용X**

| 속성  | 설명                                                         | 기본값           |
| ----- | :----------------------------------------------------------- | ---------------- |
| value | EnumType.ORDINAL: enum 순서를 데이터베이스에 저장<br />EnumType.STRING: enum 이름을 데이터베이스에 저장 | EnumType.ORDINAL |

EnumType.ORDINAL을 사용할 경우 순서가 저장되므로 integer로 db에 매핑된다. 그러므로 enum타입을 사용할 때는 항상 STRING을 사용.



#### **@Temporal**

**날짜 타입(java.util.Date, java.util.Calendar)을 매핑할 때 사용**
**참고: LocalDate, LocalDateTime을 사용할 때는 생략 가능(최신 하이버네이트 지원)**

| 속성  | 설명                                                         | 기본값 |
| ----- | ------------------------------------------------------------ | ------ |
| value | **TemporalType.DATE**: 날짜, 데이터베이스 date 타입과 매핑  <br />(예: 2013-10-11)<br />**TemporalType.TIME**: 시간, 데이터베이스 time 타입과 매핑  <br />•  (예: 11:11:11) <br />**TemporalType.TIMESTAMP**: 날짜와 시간, 데이터베이스 timestamp 타입과 매핑<br />•(예: 2013–10–11 11:11:11) |        |



#### **@Lob**

- **데이터베이스** **BLOB, CLOB** **타입과 매핑**

- **@Lob에는 지정할 수 있는 속성이 없다.**

- **매핑하는 필드 타입이 문자면** CLOB매핑, 나머지는 BLOB 매핑
  - CLOB: String, char[], java.sql.CLOB
  - BLOB: byte[], java.sql. BLOB

#### **@Transient**

- 필드 매핑X
- 데이터베이스에 저장X,조회X
- 주로 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용

- @Transient
  private Integer temp;

