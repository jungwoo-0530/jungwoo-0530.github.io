# 데이터베이스 스키마 자동 생성

- DDL을 애플리케이션 실행 시점에 자동 생성
  - 실제 운영에서는 안 쓰지만 local이나 테스트할 때 좋음
  - 원래는 table을 db에서 다 생성하고 객체를 만들지만
  - 객체를 만들면 알아서 table을 만들어줌.
- 테이블중심->객체중심
- 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL 생성
- 이렇게 **생성된** **DDL은 개발 장비에서만 사용**
- 생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬 은 후 사용



#### 데이터베이스 스키마 자동 생성 - 속성

```xml
<property name="hibernate.hbm2ddl.auto" value="" />
```

| 옵션        | 설명                                                         |
| ----------- | ------------------------------------------------------------ |
| create      | 기존테이블 삭제 후 다시 생성 (DROP + CREATE)                 |
| create-drop | create와 같으나 종료시점에 테이블 DROP                       |
| update      | 변경분만 반영(운영DB에는 사용하면 안됨), 예로 col 추가는 가능하나 지우는 것은 불가능. |
| validate    | 엔티티와 테이블이 정상 매핑되었는지만 확인                   |
| none        | 사용하지 않음                                                |



#### 데이터베이스 스키마 자동 생성 - 주의

- **운영 장비에는 절대** **create, create-drop, update** **사용하면 안된다.**

- 개발 초기 단계는 create 또는 update
- 테스트 서버는 update 또는 validate
  - 테스트 서버는 여러 명에서 테스트로 사용하는 서버를 생각.
  - 결론은 local서버에서만 사용하고 사용안하는 것이 맞다.
- 스테이징과 운영 서버는 validate 또는 none



#### DDL 생성 기능

- 제약조건 추가: 회원 이름은 **필수**, 10자 초과X 
  - **@Column(nullable = false, length = 10)**
- 유니크 제약조건 추가
  - **@Table(uniqueConstraints = {@UniqueConstraint( name = "NAME_AGE_UNIQUE",**  **columnNames = {"NAME", "AGE"} )})**

- DDL 생성 기능은 DDL을 자동 생성할 때만 사용되고 JPA의 실행 로직에는 영향을 주지 않는다.