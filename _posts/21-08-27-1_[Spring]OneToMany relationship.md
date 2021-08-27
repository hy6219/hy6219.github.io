# 1 : N 연관관계 알아보기

🌟🌟🌟핵심🌟🌟🌟

1. `@OneToMany(fetch=FetchType.EAGER)`
2. `@JoinColumn(name=~)`
3. N측에 `@Column(name=~)`
4. 1측에 준비된 연결 필드는 `컬렉션 자료구조`

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/RelationShip/OneToOne.png?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/RelationShip/OneToOne.png?raw=true)

User엔티티와 UserHistory는 1:N의 연관관계를 갖는다

다시 한 번 복습 겸, User 객체를 하나 저장하고, 이를 history에서 확인해보자

```java
@Test
    public void userRelationTest(){
        User user=new User();
        user.setName("David");
        user.setEmail("david@fastcampus.com");
        user.setGender(MALE);

        userRepository.save(user);

        userHistoryRepository.findAll().forEach(System.out::println);
    }
```

```java
UserHistory(super=BaseEntity(cratedAt=2021-08-27T10:56:43.874, updatedAt=2021-08-27T10:56:43.874), id=1, userId=null, name=David, email=david@fastcampus.com, active=false, gender=MALE)
UserHistory(super=BaseEntity(cratedAt=2021-08-27T10:56:43.976, updatedAt=2021-08-27T10:56:43.976), id=2, userId=6, name=David, email=modified-david@fastcampus.com, active=false, gender=MALE)
```

그러면 PrePersist로 삽입 전에 가져왔기 때문에 첫번째 데이터는 userId가 null인 것을 알 수 있다

따라서 우리는 보다 나은 적용을 위해서 리스너 시점을 삽입 후, 수정 후로 변경해주자

```java
package com.example.jpa_entity.domain.listener;

import com.example.jpa_entity.domain.User;
import com.example.jpa_entity.domain.UserHistory;
import com.example.jpa_entity.repository.UserHistoryRepository;
import com.example.jpa_entity.support.BeanUtils;

import javax.persistence.PostPersist;
import javax.persistence.PostUpdate;

public class UserEntityListener {

    **@PostPersist
    @PostUpdate**
    public void prePersist(Object o){
        UserHistoryRepository userHistoryRepository= BeanUtils.getBean(UserHistoryRepository.class);
        User user=(User)o;
        //userHistory는 insert
        UserHistory userHistory=new UserHistory();
        userHistory.setUserId(user.getId());
        userHistory.setName(user.getName());
        userHistory.setEmail(user.getEmail());
        userHistory.setCratedAt(user.getCratedAt());
        userHistory.setUpdatedAt(user.getUpdatedAt());
        userHistory.setActive(user.isActive());
        userHistory.setGender(user.getGender());

        userHistoryRepository.save(userHistory);
    }

}
```

```java
Hibernate: 
    insert 
    into
        user
        (id, created_at, updated_at, active, email, gender, name) 
    values
        (null, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        userhistor0_.id as id1_4_,
        userhistor0_.created_at as created_2_4_,
        userhistor0_.updated_at as updated_3_4_,
        userhistor0_.active as active4_4_,
        userhistor0_.email as email5_4_,
        userhistor0_.gender as gender6_4_,
        userhistor0_.name as name7_4_,
        userhistor0_.user_id as user_id8_4_ 
    from
        user_history userhistor0_
UserHistory(super=BaseEntity(cratedAt=2021-08-27T11:04:34.222, updatedAt=2021-08-27T11:04:34.222), id=1, userId=6, name=David, email=david@fastcampus.com, active=false, gender=MALE)
```

그러면 이제는 History 내부에는 삽입 후에 저장이 되고, 그 후 추가 수정이 없었지만 그래도 이전과 다르게 userId값이 존재하였기 때문에 하나의 레코드만 조회될 수 있게 된다

▶️ DB에 저장한 후에 바로 UserHistory 값을 저장하도록 처리한다는 의미

이번에는 같은 user에 대해서 히스토리를 쌓도록 해보자

- 먼저 이름을 daniel로 변경해주고(기존:david)
- 그 다음에는 이메일을 davidToDaniel@fastcampus.com 으로 변경해주자

```java
@Test
    public void userRelationTest(){
        User user=new User();
        user.setName("David");
        user.setEmail("david@fastcampus.com");
        user.setGender(MALE);

        userRepository.save(user);
        **user.setName("daniel");
        userRepository.save(user);//update
        user.setEmail("davidToDaniel@fastcampus.com");
        userRepository.save(user);//update**
        userHistoryRepository.findAll().forEach(System.out::println);
    }
```

그러면 setter로 데이터에 대한 업데이트가 발생하여, 동일한 userId를 갖는 데이터에 대해서 여러번 접근한 기록이 저장된다!(이 때, userId는 null이 아니다)

```java
Hibernate: 
    insert 
    into
        user
        (id, created_at, updated_at, active, email, gender, name) 
    values
        (null, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        userhistor0_.id as id1_4_,
        userhistor0_.created_at as created_2_4_,
        userhistor0_.updated_at as updated_3_4_,
        userhistor0_.active as active4_4_,
        userhistor0_.email as email5_4_,
        userhistor0_.gender as gender6_4_,
        userhistor0_.name as name7_4_,
        userhistor0_.user_id as user_id8_4_ 
    from
        user_history userhistor0_
**UserHistory(super=BaseEntity(cratedAt=2021-08-27T14:12:39.286, updatedAt=2021-08-27T14:12:39.286), id=1, userId=6, name=David, email=david@fastcampus.com, active=false, gender=MALE)
UserHistory(super=BaseEntity(cratedAt=2021-08-27T14:12:39.459, updatedAt=2021-08-27T14:12:39.459), id=2, userId=6, name=daniel, email=david@fastcampus.com, active=false, gender=MALE)
UserHistory(super=BaseEntity(cratedAt=2021-08-27T14:12:39.468, updatedAt=2021-08-27T14:12:39.468), id=3, userId=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, gender=MALE)**
```

▶️ 그리고 이러한 테스트를 통해서, 지난 번에 학습했던 리스너 설정으로 인해서 히스토리 저장을 자동화하는 데에 매우 효과적임을 확인해볼 수 있다!

그런데 유저 히스토리에 쌓인 값이 많은 경우에는 findAll보다는 userId별로 추출해내오는 것이 보다 나을 수 있다!

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.UserHistory;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;

public interface UserHistoryRepository extends JpaRepository<UserHistory,Long> {

    **List<UserHistory> findByUserId(Long userId);**
}
```

```java
@Test
    public void userRelationTest(){
        User user=new User();
        user.setName("David");
        user.setEmail("david@fastcampus.com");
        user.setGender(MALE);

        userRepository.save(user);
        user.setName("daniel");
        userRepository.save(user);//update
        user.setEmail("davidToDaniel@fastcampus.com");
        userRepository.save(user);//update
        //userHistoryRepository.findAll().forEach(System.out::println);
        **System.out.println(userHistoryRepository.findByUserId(6L));**
    }
```

```java
Hibernate: 
    insert 
    into
        user
        (id, created_at, updated_at, active, email, gender, name) 
    values
        (null, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        userhistor0_.id as id1_4_,
        userhistor0_.created_at as created_2_4_,
        userhistor0_.updated_at as updated_3_4_,
        userhistor0_.active as active4_4_,
        userhistor0_.email as email5_4_,
        userhistor0_.gender as gender6_4_,
        userhistor0_.name as name7_4_,
        userhistor0_.user_id as user_id8_4_ 
    from
        user_history userhistor0_ 
    where
        userhistor0_.user_id=?
**[UserHistory(super=BaseEntity(cratedAt=2021-08-27T14:21:33.181, updatedAt=2021-08-27T14:21:33.181), id=1, userId=6, name=David, email=david@fastcampus.com, active=false, gender=MALE), UserHistory(super=BaseEntity(cratedAt=2021-08-27T14:21:33.240, updatedAt=2021-08-27T14:21:33.240), id=2, userId=6, name=daniel, email=david@fastcampus.com, active=false, gender=MALE), UserHistory(super=BaseEntity(cratedAt=2021-08-27T14:21:33.246, updatedAt=2021-08-27T14:21:33.246), id=3, userId=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, gender=MALE)]**
```

그러면 userId가 6인 경우에 대해서 히스토리에 쌓인 결과를 리스트 형태로 확인해볼 수 있음을 알 수 있다

그리고 더 나아가서, 이는 수정 등의 과정에 따라 달라진 값을 추적하기도 용이하다 ! 결과적으로는 위와 동일하지만 엄연히 "어떤 값으로 userId를 가져온다는 점"이 차이라고 할 수 있다

아래처럼 수정된 이메일 값이었던 "davidToDaniel@fastcampus.com"이었던 기록으로 userId값을 찾고, 이를 파라미터로 넣어서 수정 전후 과정을 살펴보자

```java
@Test
    public void userRelationTest(){
        User user=new User();
        user.setName("David");
        user.setEmail("david@fastcampus.com");
        user.setGender(MALE);

        userRepository.save(user);
        user.setName("daniel");
        userRepository.save(user);//update
        user.setEmail("davidToDaniel@fastcampus.com");
        userRepository.save(user);//update
        **List<UserHistory> list=userHistoryRepository.findByUserId(
                userRepository.findByEmail("davidToDaniel@fastcampus.com").getId()
        );**
        System.out.println(list);
    }
```

먼저! 지금은 user에 david@fastcampus.com으로 검색해볼 수 없어서 저장된 값인 davidToDaniel@fastcampus.com으로 userId를 찾도록 하였다

그 결과 해당 userId에 대한 변화 사항은 아래와 같이, 먼저 이름이 바뀌었고, 그 후에 이메일이 변경되었음을 확인해볼 수 있었다

더불어 findByUserId로 인해서 WHERE 절의 조건에 user_id=?가 추가되었음을 확인해볼 수 있다

```java
Hibernate: 
    insert 
    into
        user
        (id, created_at, updated_at, active, email, gender, name) 
    values
        (null, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_,
        user0_.created_at as created_2_3_,
        user0_.updated_at as updated_3_3_,
        user0_.active as active4_3_,
        user0_.email as email5_3_,
        user0_.gender as gender6_3_,
        user0_.name as name7_3_ 
    from
        user user0_ 
    where
        user0_.email=?
Hibernate: 
    select
        userhistor0_.id as id1_4_,
        userhistor0_.created_at as created_2_4_,
        userhistor0_.updated_at as updated_3_4_,
        userhistor0_.active as active4_4_,
        userhistor0_.email as email5_4_,
        userhistor0_.gender as gender6_4_,
        userhistor0_.name as name7_4_,
        userhistor0_.user_id as user_id8_4_ 
    from
        user_history userhistor0_ 
    **where
        userhistor0_.user_id=?**
[UserHistory(super=BaseEntity(cratedAt=2021-08-27T14:28:32.954, updatedAt=2021-08-27T14:28:32.954), id=1, userId=6, name=David, email=david@fastcampus.com, active=false, gender=MALE), UserHistory(super=BaseEntity(cratedAt=2021-08-27T14:28:33.016, updatedAt=2021-08-27T14:28:33.016), id=2, userId=6, name=daniel, email=david@fastcampus.com, active=false, gender=MALE), UserHistory(super=BaseEntity(cratedAt=2021-08-27T14:28:33.021, updatedAt=2021-08-27T14:28:33.021), id=3, userId=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, gender=MALE)]
```

하지만 지난 시간처럼 이 방식은 JPA에서 제공하는 관계에 대한 방식이 아니고 기존의 기능을 이용한 것이라, 지난 시간처럼 JPA에서 제공하는 관계에 대한 어노테이션을 배워보도록 하자

## `@OneToMany` - 1:N의 연관관계 지원

User에 관련된 UserHistory는 n개이기 때문에 `리스트 등과 같은 자바 컬렉션이 지원`될 것이다

1:N에서 `@OneToMany`의 핵심인 `One`은 User측이므로, User 엔티티에서 적용해보도록 하자

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.UserEntityListener;
import lombok.*;

import javax.persistence.*;
import java.util.List;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
@Table(name="user")
//@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class, UserEntityListener.class})
@EntityListeners(value={UserEntityListener.class})
public class User extends BaseEntity /*implements Auditable*/{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

 ****🌟🌟🌟   **@OneToMany**
 🌟🌟🌟   **private List<UserHistory> userHistories;**
}
```

그리고 인텔리제이 내부에서 자체적으로 변수명을 추천해주는데, ~List로 끝나는 것보다는, ~(e)s와 같은 복수형으로 끝나는 네이밍을 선택하는 것이 최근 트렌드라고 한다

📌 그리고! NullPointerException이 발생하지 않도록 new 생성자로 List에 대해서 생성되도록 해주자

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.UserEntityListener;
import lombok.*;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
@Table(name="user")
//@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class, UserEntityListener.class})
@EntityListeners(value={UserEntityListener.class})
public class User extends BaseEntity /*implements Auditable*/{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

    **@OneToMany
    private List<UserHistory> userHistories=new ArrayList<>();**
}
```

물론 몇 번의 테스트를 통해서 JPA에서는 null인 경우 빈 리스트를 출력하는 것을 확인했지만 JPA에서 Persist 전에 NULL이라서 insert 시에 문제가 발생할 수도 있다! 그래서 위처럼 기본적으로 객체를 생성시켜주는 것이 좋다

이번에는 UserId를 가져와서 UserHistory를 조회하는 부분을 위로 인해서 변경해줄 수 있을 것이다

다만, 개념적으로 나눠서 생각을 더 해주기 위해서, 메서드명을 oneToManyTest로 짓고 새로이 만들어주자

```java
//1:n적용후
    @Test
    public void oneToManyTest(){
        User user=new User();
        user.setName("David");
        user.setEmail("david@fastcampus.com");
        user.setGender(MALE);

        userRepository.save(user);
        user.setName("daniel");
        userRepository.save(user);//update
        user.setEmail("davidToDaniel@fastcampus.com");
        userRepository.save(user);//update

        **List<UserHistory> list=userRepository.findByEmail("davidToDaniel@fastcampus.com").getUserHistories();
        System.out.println(list);**
    }
```

이대로 실행해보면 아래처럼 LazyInitializationException이 발생한다

```java
org.hibernate.LazyInitializationException
```

그래서 User에서 userHistories에 대해 사용되었던 OneToMany의 속성 중 fetch 속성의 값을 `FetchType.EAGER로 변경해주자

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.UserEntityListener;
import lombok.*;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
@Table(name="user")
//@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class, UserEntityListener.class})
@EntityListeners(value={UserEntityListener.class})
public class User extends BaseEntity /*implements Auditable*/{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

    **@OneToMany(fetch = FetchType.EAGER)**
    private List<UserHistory> userHistories=new ArrayList<>();
}
```

테스트를 실행해보고 DDL부터 살펴보자

```java
Hibernate: 
    
    create table user (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        active boolean,
        email varchar(255),
        gender varchar(255),
        name varchar(255),
        primary key (id)
    )
Hibernate: 
    
    **create table user_user_histories (
       user_id bigint not null,
        user_histories_id bigint not null
    )**
Hibernate: 
    
    create table user_history (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        active boolean not null,
        email varchar(255),
        gender varchar(255),
        name varchar(255),
        user_id bigint,
        primary key (id)
    )
Hibernate: 
    
    **alter table user_user_histories 
       add constraint UK_s2x6bx7omau9g3vxibgmcgkct unique (user_histories_id)**
Hibernate: 
    
    alter table book_review_info 
       add constraint FKp5fhkokpbtoxmc3mxo8ay9e5l 
       foreign key (book_id) 
       references book
**Hibernate: 
    
    alter table user_user_histories 
       add constraint FKf8g37jnpc3f9trg0i893oqnyx 
       foreign key (user_histories_id) 
       references user_history**
**Hibernate: 
    
    alter table user_user_histories 
       add constraint FKop961c3sakkxvcadq7djc11kt 
       foreign key (user_id) 
       references user**
```

그러면 List<UserHistory> 객체에 대한 테이블이 생성되는데,

user_histories로 생성되고 user_histories_id는 고유키이자 외래키로 설정되며

user_id는 외래키로 설정된 것을 확인해볼 수 있다

```java
Hibernate: 
    insert 
    into
        user
        (id, created_at, updated_at, active, email, gender, name) 
    values
        (null, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        userhistor0_.user_id as user_id1_4_0_,
        userhistor0_.user_histories_id as user_his2_4_0_,
        userhistor1_.id as id1_5_1_,
        userhistor1_.created_at as created_2_5_1_,
        userhistor1_.updated_at as updated_3_5_1_,
        userhistor1_.active as active4_5_1_,
        userhistor1_.email as email5_5_1_,
        userhistor1_.gender as gender6_5_1_,
        userhistor1_.name as name7_5_1_,
        userhistor1_.user_id as user_id8_5_1_ 
    from
        user_user_histories userhistor0_ 
    inner join
        user_history userhistor1_ 
            on userhistor0_.user_histories_id=userhistor1_.id 
    where
        userhistor0_.user_id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        userhistor0_.user_id as user_id1_4_0_,
        userhistor0_.user_histories_id as user_his2_4_0_,
        userhistor1_.id as id1_5_1_,
        userhistor1_.created_at as created_2_5_1_,
        userhistor1_.updated_at as updated_3_5_1_,
        userhistor1_.active as active4_5_1_,
        userhistor1_.email as email5_5_1_,
        userhistor1_.gender as gender6_5_1_,
        userhistor1_.name as name7_5_1_,
        userhistor1_.user_id as user_id8_5_1_ 
    from
        user_user_histories userhistor0_ 
    inner join
        user_history userhistor1_ 
            on userhistor0_.user_histories_id=userhistor1_.id 
    where
        userhistor0_.user_id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_,
        user0_.created_at as created_2_3_,
        user0_.updated_at as updated_3_3_,
        user0_.active as active4_3_,
        user0_.email as email5_3_,
        user0_.gender as gender6_3_,
        user0_.name as name7_3_ 
    from
        user user0_ 
    where
        user0_.email=?
Hibernate: 
    select
        userhistor0_.user_id as user_id1_4_0_,
        userhistor0_.user_histories_id as user_his2_4_0_,
        userhistor1_.id as id1_5_1_,
        userhistor1_.created_at as created_2_5_1_,
        userhistor1_.updated_at as updated_3_5_1_,
        userhistor1_.active as active4_5_1_,
        userhistor1_.email as email5_5_1_,
        userhistor1_.gender as gender6_5_1_,
        userhistor1_.name as name7_5_1_,
        userhistor1_.user_id as user_id8_5_1_ 
    from
        user_user_histories userhistor0_ 
    inner join
        user_history userhistor1_ 
            on userhistor0_.user_histories_id=userhistor1_.id 
    where
        userhistor0_.user_id=?
[]
```

그리고 DQL, DML을 살펴보면 ANSI 문법을 이용해서 innser join을 on을 이용해서 userHistory 테이블 내에서 먼저 select 후 반환 혹은 업데이트를 하는 모습을 확인해볼 수 있다

## 🌟🌟🌟`@JoinColumn(name=*연결되는 n측에서의 관련 필드명*)` - 엔티티가 어떤 컬럼으로 조인될 지 지정🌟🌟🌟

위의 innser join의 번거로움을 `@JoinColumn`으로 줄여보도록 하자

우리는 UserHistory테이블의 user_id와 연결되므로 `@JoinColumn(name=user_id)`

로 진행해주자

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.UserEntityListener;
import lombok.*;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
@Table(name="user")
//@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class, UserEntityListener.class})
@EntityListeners(value={UserEntityListener.class})
public class User extends BaseEntity /*implements Auditable*/{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

    @OneToMany(fetch = FetchType.EAGER)
    **@JoinColumn(name="user_id")**
    private List<UserHistory> userHistories=new ArrayList<>();
}
```

그러면 아래처럼 컬럼이름인 user_id로 해야할지, 자바에서의 이름인 userId로 해야할 지 모호하다는 에러가 발생한다

```java
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Invocation of init method failed; nested exception is org.hibernate.DuplicateMappingException: Table [user_history] contains physical column name [user_id] referred to by multiple logical column names: [user_id], [userId]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory
```

이를 UserHistory에서 `@Column(name="user_id")`로 명시해주자

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.Auditable;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
//@EntityListeners(value={MyEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class})
public class UserHistory extends BaseEntity/*implements Auditable*/ {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    **@Column(name="user_id")**
    private Long userId;

    private String name;

    private String email;

//    @CreatedDate
//    private LocalDateTime createdAt;
//    @LastModifiedDate
//    private LocalDateTime updatedAt;

    private boolean active;

    @Enumerated(EnumType.STRING)
    private Gender gender;
}
```

```java

Hibernate: 
    
    create table user (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        active boolean,
        email varchar(255),
        gender varchar(255),
        name varchar(255),
        primary key (id)
    )
Hibernate: 
    
    create table user_history (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        active boolean not null,
        email varchar(255),
        gender varchar(255),
        name varchar(255),
        user_id bigint,
        primary key (id)
    )
Hibernate: 
    
    alter table book_review_info 
       add constraint FKp5fhkokpbtoxmc3mxo8ay9e5l 
       foreign key (book_id) 
       references book
Hibernate: 
    
    alter table user_history 
       add constraint FKaa6ilb6iqih95bntoeyysb2pc 
       foreign key (user_id) 
       references user
Hibernate: 
    insert 
    into
        user
        (id, created_at, updated_at, active, email, gender, name) 
    values
        (null, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        userhistor0_.user_id as user_id8_4_0_,
        userhistor0_.id as id1_4_0_,
        userhistor0_.id as id1_4_1_,
        userhistor0_.created_at as created_2_4_1_,
        userhistor0_.updated_at as updated_3_4_1_,
        userhistor0_.active as active4_4_1_,
        userhistor0_.email as email5_4_1_,
        userhistor0_.gender as gender6_4_1_,
        userhistor0_.name as name7_4_1_,
        userhistor0_.user_id as user_id8_4_1_ 
    from
        user_history userhistor0_ 
    where
        userhistor0_.user_id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        userhistor0_.user_id as user_id8_4_0_,
        userhistor0_.id as id1_4_0_,
        userhistor0_.id as id1_4_1_,
        userhistor0_.created_at as created_2_4_1_,
        userhistor0_.updated_at as updated_3_4_1_,
        userhistor0_.active as active4_4_1_,
        userhistor0_.email as email5_4_1_,
        userhistor0_.gender as gender6_4_1_,
        userhistor0_.name as name7_4_1_,
        userhistor0_.user_id as user_id8_4_1_ 
    from
        user_history userhistor0_ 
    where
        userhistor0_.user_id=?
Hibernate: 
    update
        user 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=? 
    where
        id=?
Hibernate: 
    insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_3_,
        user0_.created_at as created_2_3_,
        user0_.updated_at as updated_3_3_,
        user0_.active as active4_3_,
        user0_.email as email5_3_,
        user0_.gender as gender6_3_,
        user0_.name as name7_3_ 
    from
        user user0_ 
    where
        user0_.email=?
Hibernate: 
    select
        userhistor0_.user_id as user_id8_4_0_,
        userhistor0_.id as id1_4_0_,
        userhistor0_.id as id1_4_1_,
        userhistor0_.created_at as created_2_4_1_,
        userhistor0_.updated_at as updated_3_4_1_,
        userhistor0_.active as active4_4_1_,
        userhistor0_.email as email5_4_1_,
        userhistor0_.gender as gender6_4_1_,
        userhistor0_.name as name7_4_1_,
        userhistor0_.user_id as user_id8_4_1_ 
    from
        user_history userhistor0_ 
    where
        userhistor0_.user_id=?
[UserHistory(super=BaseEntity(cratedAt=2021-08-27T15:03:38.196, updatedAt=2021-08-27T15:03:38.196), id=1, userId=6, name=David, email=david@fastcampus.com, active=false, gender=MALE), UserHistory(super=BaseEntity(cratedAt=2021-08-27T15:03:38.352, updatedAt=2021-08-27T15:03:38.352), id=2, userId=6, name=daniel, email=david@fastcampus.com, active=false, gender=MALE), UserHistory(super=BaseEntity(cratedAt=2021-08-27T15:03:38.361, updatedAt=2021-08-27T15:03:38.361), id=3, userId=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, gender=MALE)]
```

그러면 이전과 다르게, user_histories라는 별도 테이블이 보이지 않고, user_histories_id가 남고, innser join도 보이지 않는다

그리고!! 우리가 연관관계를 JPA 어노테이션을 이용하지 않았을 때와 동일한 결과를 확인해볼 수 있다!

📌여기에서 history라는 값은 user엔티티에서 수정하면 안되는 값이다!! 즉, READ-ONLY로 되어야 한다!

## 1측에 존재하는 필드(매개체)에 @JoinColumn(`insertable=false, updatable=false`)로 설정하여 이 연결체에 접근하지 못하도록 하기

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.UserEntityListener;
import lombok.*;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
@Table(name="user")
//@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class, UserEntityListener.class})
@EntityListeners(value={UserEntityListener.class})
public class User extends BaseEntity /*implements Auditable*/{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

    @OneToMany(fetch = FetchType.EAGER)
    **@JoinColumn(name="user_id",insertable = false,updatable = false)**
    private List<UserHistory> userHistories=new ArrayList<>();
}
```

이렇게 되면 이 매개체에 대해서 User는 함부로 건드리지 못하게 되어, 미연의 상황을 방지할 수 있게 된다!→ 나중에 트랜잭션에서 관리하는 것과 연결해서 공부할 필요가 있음!

이렇게 read-only로 만든 일은 jpa의 자동 설정으로 인해 발생할 수 있는 상황을 미연에 막기 위함이다!