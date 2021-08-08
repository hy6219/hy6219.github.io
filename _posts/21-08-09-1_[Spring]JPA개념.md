# JPA 소개

## 01. JPA란 무엇인가요?

`#Java Persistence Api` `#ORM(Object Relational Mapping)` `#Transaction` `#JDBC` `#Hibernate` `#Repository` `#Entity` `#Query` `#Lock`

### 01-0. ORM

- Object Relational Mapping
- 객체와 데이터베이스 사이를 연결해주는 것
- 인터페이스로 구현되어 있음

![https://javabydeveloper.com/wp-content/uploads/2020/02/ORM-object-relational-mapping.png](https://javabydeveloper.com/wp-content/uploads/2020/02/ORM-object-relational-mapping.png)

source: [https://javabydeveloper.com/wp-content/uploads/2020/02/ORM-object-relational-mapping.png](https://javabydeveloper.com/wp-content/uploads/2020/02/ORM-object-relational-mapping.png)

### 01-1. JPA

- Java Persistence Api
- 현재 자바 진영의 표준 ORM으로 채택되어 있음!
- 데이터(persistence)에 접근하기 위한 API 규격을 정의해둔 것
- ORM이 전체적인 개념이라면, JPA는 조금 더 구체적으로 정의해둔 스펙
- 2019년부터 Jakarta Persistence로 명명

### 01-2.Hibernate

- JPA의 구현체
- 스프링은 기본적으로 하이버네이트를 이용

### 01-3. Spring Data JPA

- 스프링에서 하이버네이트를 간편하게 사용할 수 있도록 추상 객체를 한 번 더 감싸서 만들어 놓은 것
- EntityManager에 접근하지 않고도 데이터를 접근할 수 있음

![https://i1.wp.com/www.thistechnologylife.com/wp-content/uploads/2020/07/JPA.png?fit=839%2C278&ssl=1](https://i1.wp.com/www.thistechnologylife.com/wp-content/uploads/2020/07/JPA.png?fit=839%2C278&ssl=1)

source: [https://i1.wp.com/www.thistechnologylife.com/wp-content/uploads/2020/07/JPA.png?fit=839%2C278&ssl=1](https://i1.wp.com/www.thistechnologylife.com/wp-content/uploads/2020/07/JPA.png?fit=839%2C278&ssl=1)