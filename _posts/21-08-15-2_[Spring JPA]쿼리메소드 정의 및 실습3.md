# 쿼리메서드 기본실습3

## 1.Start to add bean value condition to where clause

이번에는 빈값에 대한 조회를 해보자

[Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation)

이 공식문서의 표를 보면,

- IsNull, Null : 속성값이 비어있는지에 대한 조건을 붙여줌
- IsNotNull, NotNull : 속성값이 비어있지 않는 조건을 붙여줌
- Like : LIKE 조건을  붙이는 것
- NotLike:  NOT LIKE 조건을 붙이는 것
- StartingWith : LIKE 입력% 조건을 붙이는 것
- EndingWith : LIKE %입력 조건을 붙이는 것
- Containing : LIKE %입력% 조건을 붙이는 것
- OrderBy: 정렬조건 `예: findByAgeOrderByLastnameDesc`
- Not: SQL에서 NOT의 기능! <>, ! =, ^=
- In : IN(?,?,?)조건
- NotIn : NOT IN 조건
- True : WHERE 속성=true
- False: WHERE 속성=false

이외에는 아래를 참조!

[쿼리메소드 정의 및 실습1](https://hy6219.github.io/TIL/Spring/JPA/JPA%20Repository/[Spring%20JPA]%EC%BF%BC%EB%A6%AC%EB%A9%94%EC%86%8C%EB%93%9C%20%EC%A0%95%EC%9D%98%20%EB%B0%8F%20%EC%8B%A4%EC%8A%B51.html#b51b01d8-febf-4b00-b7a9-5e104131b780)

### 01-1. IsNotNull

특정 속성에 대해서 Null이 아닌 경우를 조회

```java
List<User> findUsersByIdIsNotNull();
```

```java
@Test
    public void beanClause() {
        System.out.println("findUsersByIdIsNotNull: " + userRepository.findUsersByIdIsNotNull());
    }
```

```java
	Hibernate: 
    select
        user0_.id as id1_0_,
        user0_.created_at as created_2_0_,
        user0_.email as email3_0_,
        user0_.name as name4_0_,
        user0_.updated_at as updated_5_0_ 
    from
        user user0_ 
    where
        user0_.id is not null
findUsersByIdIsNotNull: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 15:58:30.197, updatedAt=2021-08-15 15:58:30.197), User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 15:58:30.213, updatedAt=2021-08-15 15:58:30.213), User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-15 15:58:30.213, updatedAt=2021-08-15 15:58:30.213), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 15:58:30.214, updatedAt=2021-08-15 15:58:30.214), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 15:58:30.214, updatedAt=2021-08-15 15:58:30.214)]
```

위와 같이, WHERE 절에 NotNull 속성이 붙는 것을 알 수 있고!

지금 아이디는 기본키이기 때문에 NOT NULL+UNIQUE 제약조건이 기본적으로 내장되어 있기 때문에 모든 레코드가 조회된다!

### 01-3. IsNotEmpty ▶️ 존재 하지 않는! WHERE Clause와 전혀 관련없는 키워드!

보통 자료구조에서 empty()등이 다루어 지기 때문에 isNotEmpty등을 떠올릴 수 있는데, 다음과 같이 간단하게 테스트해보면

```java
List<User> findUsersByIdIsNotEmpty();
```

```java
System.out.println("findUsersByIdIsNotEmpty: "+userRepository.findUsersByIdIsNotEmpty());
```

```java
Caused by: org.springframework.data.repository.query.QueryCreationException: Could not create query for public abstract java.util.List com.example.jpa_query_method.repository.UserRepository.findUsersByIdIsNotEmpty()! Reason: Failed to create query for method public abstract java.util.List com.example.jpa_query_method.repository.UserRepository.findUsersByIdIsNotEmpty()! IsEmpty / IsNotEmpty can only be used on collection properties!; nested exception is java.lang.IllegalArgumentException: Failed to create query for method public abstract java.util.List com.example.jpa_query_method.repository.UserRepository.findUsersByIdIsNotEmpty()! IsEmpty / IsNotEmpty can only be used on collection properties!
```

위와 같이 IsEmpty혹은 IsNotEmpty는 컬렉션 속성으로 사용되는 부분이라서 지금과 같은 경우에는 적용할 수 없다는 에러를 확인해볼 수 있다!

### 01-3-1. 컬렉션 속성으로 사용된다? IsNotEmpty?

Address 라는 도메인을 하나 만들어보자

Address는 id라는 필드 하나만 갖도록 하고

```java
package com.example.jpa_query_method.domain;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Address {
    @Id
    private Long id;
}
```

아직 배우진 않았지만 관계 중 1:N(일대다) 관계에 따르고, EAGER FETCH를 하는 List<Address> addresses 필드를 User 도메인에 추가해주자

```java
package com.example.jpa_query_method.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;
import java.util.List;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at")
    private Date createdAt;
    @Column(name="updated_at")
    private Date updatedAt;

    //IsNotEmpty 확인용
    @OneToMany(fetch=FetchType.EAGER)
    **private List<Address> addresses;**
}
```

그러면 지금 User 내부에 컬렉션 중 하나인 List 자료형을 갖는 필드가 존재하므로 이에 대해서는 IsNotEmpty 를 붙일 수 있다!

```java
List<User> findUsersByAddressesIsNotEmpty();
```

```java
System.out.println("findUsersByAddressesIsNotEmpty: "+userRepository.findUsersByAddressesIsNotEmpty());
```

```java
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.created_at as created_2_1_,
        user0_.email as email3_1_,
        user0_.name as name4_1_,
        user0_.updated_at as updated_5_1_ 
    from
        user user0_ 
    where
        **exists** (
            select
                address2_.id 
            from
                user_addresses addresses1_,
                address address2_ 
            where
                user0_.id=addresses1_.user_id 
                and addresses1_.addresses_id=address2_.id
        )
findUsersByAddressesIsNotEmpty: []
```

그러면 EXISTS로 존재한다면 모든 값을 보여주게 되는것을 확인해볼 수 있는데, 지금은 address의 id값이 null이기 때문에 조건을 맞추는 경우가 없어 비어있는 출력을 확인해볼 수 있다

이러한 IsNotEmpty 의 경우는 잘 사용되지 않는 구문이다

🌟 empty라는 것은 원래는 is not null ➕ is not "" 의 의미인데, 여기서는 그런 의미가 아닌, 컬렉션에서 해당 조건이 존재하는지 확인하면 보여주는 용도로 사용됨을 알 수 있다

### 01-4. True, False

먼저 불린값을 확인하기 위해서 User 도메인에 active라는 필드를 추가해주자

```java
package com.example.jpa_query_method.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;
import java.util.List;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at")
    private Date createdAt;
    @Column(name="updated_at")
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    **@Column(name="active")
    private boolean active;**
}
```

그리고 이에 걸맞게 data.sql도 아래처럼 수정해주자

```java
call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (1, 'martin', 'martin@fastcampus.com', now(), now(),true);

call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (2, 'dennis', 'dennis@fastcampus.com', now(), now(),true);

call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (3, 'sophia', 'sophia@slowcampus.com', now(), now(),false);

call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (4, 'james', 'james@slowcampus.com', now(), now(),false);

call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (5, 'martin', 'martin@another.com', now(), now(),true);
```

그러면 이에 맞게 메서드를 `~False/True` 로 만들어주자

```java
List<User> findUsersByActiveFalse();
    List<User> findUsersByActiveTrue();
```

```java
@Test
    public void beanClause() {
     //   System.out.println("findUsersByIdIsNotNull: " + userRepository.findUsersByIdIsNotNull());
      //  System.out.println("===");
        //System.out.println("findUsersByAddressesIsNotEmpty: "+userRepository.findUsersByAddressesIsNotEmpty());
        System.out.println("findUsersByActiveFalse: "+userRepository.findUsersByActiveFalse());
        System.out.println("findUsersByActiveTrue: "+userRepository.findUsersByActiveTrue());
    }
```

```java
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.updated_at as updated_6_1_ 
    from
        user user0_ 
    where
        user0_.active=0
findUsersByActiveFalse: [User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-15 16:35:44.708, updatedAt=2021-08-15 16:35:44.708, active=false), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 16:35:44.709, updatedAt=2021-08-15 16:35:44.709, active=false)]
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.updated_at as updated_6_1_ 
    from
        user user0_ 
    where
        user0_.active=1
findUsersByActiveTrue: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 16:35:44.695, updatedAt=2021-08-15 16:35:44.695, active=true), User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 16:35:44.708, updatedAt=2021-08-15 16:35:44.708, active=true), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 16:35:44.709, updatedAt=2021-08-15 16:35:44.709, active=true)]
```

우선, 이진법으로 생각하면 true는 1, false는 0이기 때문에

False의 경우에는 active=0인 조건으로 찾으려고 하고

True의 경우에는 active=1인 조건으로 찾으려고 하는 것을 확인해볼 수 있다

그렇기 때문에 False인 경우에는 아이디 3,4,5에 해당하는 sophia, james, martin이 출력되고

True인 경우에는 아이디 1,2에 해당되는 martin, dennis가 출력되는 것을 확인해볼 수 있다

### 01-5. In, NotIn

이번에는 IN이나 NOT IN은 1개 값에서도 찾을 수 있지만, 사용자가 지정한 특정 값들 중에 포함이 되는지를 확인해보는 것이 보다 매력있기 때문에 Iterable을 파라미터로 주어서 테스트해보도록 하겠다

```java
List<User> findUsersByIdIn(Iterable iterable);
    List<User> findUsersByIdNotIn(Iterable iterable);
```

```java
@Test
    public void inNotTest(){
        System.out.println("findUsersByIdIn: "+userRepository.findUsersByIdIn(Lists.newArrayList(1L,3L,5L)));
        System.out.println("findUsersByIdNotIn: "+userRepository.findUsersByIdNotIn(Lists.newArrayList(1L,3L,5L)));
    }
```

```java
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.updated_at as updated_6_1_ 
    from
        user user0_ 
    where
        **user0_.id in** (
            ? , ? , ?
        )
findUsersByIdIn: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 16:41:56.454, updatedAt=2021-08-15 16:41:56.454, active=true), User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-15 16:41:56.467, updatedAt=2021-08-15 16:41:56.467, active=false), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 16:41:56.468, updatedAt=2021-08-15 16:41:56.468, active=true)]
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.updated_at as updated_6_1_ 
    from
        user user0_ 
    where
        **user0_.id not in**  (
            ? , ? , ?
        )
findUsersByIdNotIn: [User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 16:41:56.467, updatedAt=2021-08-15 16:41:56.467, active=true), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 16:41:56.468, updatedAt=2021-08-15 16:41:56.468, active=false)]
```

그러면 In은 데이터가 아이디가 1L, 3L, 5L 중에 해당되는 레코드가 있다면 선택해서 보여주고

NotIn은 그 외의 경우, 즉 2L, 4L의 경우를 보여주는 것을 확인해볼 수 있다!

쿼리에서는 각각이 IN, NOT IN으로 확인되는 것을 볼 수 있다!

실제로는 다른 쿼리에서 리턴되는 값을 IN절을 위한 인자값으로 넣어주기 때문에 실제로는 얼마나 많은 데이터가 넘어가는지는 확인되기 어려운데, 너무 많은 데이터가 넘어가면 성능적 이슈가 있을 수 있기 때문에 사전에 미리 검토하고 들어가는 것이 좋다!

### 01-6. StartingWith(`LIKE 1%`), EndingWith(`LIKE %1`), Containing(`LIKE %1%` ▶️ Containing은 %로 둘러싸기 특성이 있어서 양방향 검색!!이라고 불릴 수 있다!)

[쿼리메소드 정의 및 실습1](https://hy6219.github.io/TIL/Spring/JPA/JPA%20Repository/[Spring%20JPA]%EC%BF%BC%EB%A6%AC%EB%A9%94%EC%86%8C%EB%93%9C%20%EC%A0%95%EC%9D%98%20%EB%B0%8F%20%EC%8B%A4%EC%8A%B51.html#6a5477fc-2cbf-45b0-927f-3af1afa9a605)

저번 자료를 확인해보도록 하자! 🙋🏻‍♂️

🌟 Containing도 되지만 Contains도 된다!

사실은 이런 키워드는 Like라는 키워드를 한번 더 랩핑한 것과 같다!

### 01-7. 위의 01-6의 원조격! Like

```java
List<User> findUsersByNameLike(String name);
```

```java
@Test
    public void likeMatcher(){
        //System.out.println("findAllByEmailEndingWith: "+userRepository.findAllByEmailEndingWith("slowcampus.com"));
        //System.out.println("findAllByNameStartingWith: "+userRepository.findAllByNameStartingWith("ma"));
       // System.out.println("findAllByEmailContaining: "+userRepository.findAllByEmailContaining("campus"));
        System.out.println("findUsersByNameLike-start: "+userRepository.findUsersByNameLike("mar%"));
        System.out.println("findUsersByNameLike-contain: "+userRepository.findUsersByNameLike("%art%"));
        System.out.println("findUsersByNameLike-end: "+userRepository.findUsersByNameLike("%tin"));
    }
```

```java
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.updated_at as updated_6_1_ 
    from
        user user0_ 
    where
        user0_.name like ? escape ?
findUsersByNameLike-start: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 16:59:46.133, updatedAt=2021-08-15 16:59:46.133, active=true), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 16:59:46.149, updatedAt=2021-08-15 16:59:46.149, active=true)]
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.updated_at as updated_6_1_ 
    from
        user user0_ 
    where
        user0_.name like ? escape ?
findUsersByNameLike-contain: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 16:59:46.133, updatedAt=2021-08-15 16:59:46.133, active=true), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 16:59:46.149, updatedAt=2021-08-15 16:59:46.149, active=true)]
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.updated_at as updated_6_1_ 
    from
        user user0_ 
    where
        user0_.name like ? escape ?
findUsersByNameLike-end: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 16:59:46.133, updatedAt=2021-08-15 16:59:46.133, active=true), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 16:59:46.149, updatedAt=2021-08-15 16:59:46.149, active=true)]
```

여기서 확인해볼 수 있는 것은

- Like는 01-6의 원초적인 모습을 %기호에서 확인해볼 수 있다는 점

이다!

우리가 `%1%` 로 인자값을 넘겨주면 `Containing`과 대응되고

`%1`로 넘겨주면 `EndingWith`와 대응되며

`1%`로 넘겨주면 `StartingWith`와 대응된다는 점이다!

이것은 SQL 문법에서 사용한 것 그대로를 문자열로 보는 것과 같다!

하지만 비슷한 점은 LIKE가 쿼리에 들어갔다는 점이라고 할 수 있겠다!

그리고 그 결과, 모든 결과에서 동일하게 두 martin이 출력되는 것을 볼 수 있다!

하지만, 우리가 실제로는 name에 대응되는 값이 들어온다면  `"%"+name+"%"` 로 문자열을 연결시켜주어야 하는데, 이는 가독성이 떨어진다!

따라서 StartingWith 등으로 매칭시켜주는 것이 효율적일 것이다!(Like보다!)

이런 면에서 Spring Data Jpa는 코드 가독성을 많이 신경쓴 라이브러리다!

### 01-8. Is,Equals

- "=" 이거면 설명 끝이다!!

먼저 이번에는 이름이 martin인 경우의 행 수를 카운트하는 메서드들을 Is, Equals로 동일한 목적으로 정의해보자

```java
long countUsersByNameIs(String name);
    long countUsersByNameEquals(String name);
```

```java
@Test
    public void isEquals(){
        System.out.println("countUsersByNameIs: "+userRepository.countUsersByNameIs("martin"));
        System.out.println("countUsersByNameEquals: "+userRepository.countUsersByNameEquals("martin"));
    }
```

```java
Hibernate: 
    select
        count(user0_.id) as col_0_0_ 
    from
        user user0_ 
    where
        user0_.name=?
countUsersByNameIs: 2
Hibernate: 
    select
        count(user0_.id) as col_0_0_ 
    from
        user user0_ 
    where
        user0_.name=?
countUsersByNameEquals: 2
```

그러면 Is와 Equals 모두 `=` 를 사용해서 조건절에서 name 속성값을 확인해서 count를 수행함을 알 수 있고, count는 `기본키로 고유한 값을 지닌 id 필드에 기인해서 카운트함`을 확인해볼 수 있다!

그런데 사실은 Is나 Equals가 없어도 충분히 동일한 쿼리가 꾸려지게 되어 동일 결과를 볼 수 있다

```java
long countUsersByName(String name);
    long countUsersByNameIs(String name);
    long countUsersByNameEquals(String name);
```

```java
@Test
    public void isEquals(){
        System.out.println("countUsersByName: "+userRepository.countUsersByName("martin"));
        System.out.println("countUsersByNameIs: "+userRepository.countUsersByNameIs("martin"));
        System.out.println("countUsersByNameEquals: "+userRepository.countUsersByNameEquals("martin"));
    }
```

```java
Hibernate: 
    select
        count(user0_.id) as col_0_0_ 
    from
        user user0_ 
    where
        user0_.name=?
countUsersByName: 2
Hibernate: 
    select
        count(user0_.id) as col_0_0_ 
    from
        user user0_ 
    where
        user0_.name=?
countUsersByNameIs: 2
Hibernate: 
    select
        count(user0_.id) as col_0_0_ 
    from
        user user0_ 
    where
        user0_.name=?
countUsersByNameEquals: 2
```

즉, Is나 Equals는 코드 가독성을 어떻게 하면 더 좋게 할 수 있는 지를 위해 제공되는 키워드라고 보면 좋다!