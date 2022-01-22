---
title:  "엔티티 매핑"
excerpt: ""

categories:
  - Jpa
tags:
  - [Jpa, Web, ORM]
 
date: 2021-10-23
last_modified_at: 2021-10-23
---



# 엔티티 매핑



#### 엔티티 매핑

- 객체와 테이블 매핑: **@Entity, @Table**
- 필드와 컬럼 매핑: **@Column**
- 기본 키 매핑: **@Id**
-  연관관계 매핑: **@ManyToOne,@JoinColumn**



#### @Entity

- @Entity가 붙은 클래스는 JPA가 관리, 엔티티라 한다.
- JPA를 사용해서 테이블과 매핑할 클래스는 **@Entity** 필수
- **주의**
  - *기본 생성자 필수**(파라미터가 없는 public 또는 protected 생성자) 
  - final 클래스, enum, interface, inner 클래스 사용X
  - 저장할 필드에 final 사용 X



#### @Entity 속성 정리

- 속성 : name
  - JPA에서 사용할 엔티티 이름을 지정한다.
  - 기본값: 클래스 이름을 그대로 사용(예: Member)
  - 같은 클래스 이름이 없으면 가급적 기본값을 사용한다.



#### @Table

- @Table은 엔티티와 매핑할 테이블 지정'

- | 속성                    | 기능                            | 기본값             |
  | ----------------------- | ------------------------------- | ------------------ |
  | name                    | 매핑할 테이블 이름              | 엔티티 이름을 사용 |
  | catalog                 | 데이터베이스 catalog 매핑       |                    |
  | schema                  | 데이터베이스 schema 매핑        |                    |
  | uniqueConstraints (DDL) | 생성 시에 유니크 제약 조건 생성 |                    |
