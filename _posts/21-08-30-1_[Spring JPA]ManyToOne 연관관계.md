# N:1 연관관계

![https://raw.githubusercontent.com/hy6219/TIL/main/Spring/JPA/Entity/RelationShip/OneToOne.png](https://raw.githubusercontent.com/hy6219/TIL/main/Spring/JPA/Entity/RelationShip/OneToOne.png)

저번에는 User:UserHistory의 관계가 1:N이라는 점에 초점을 맞춰서 공부를 했었다

`@OneToMany`에서는 참조되는 값을 1에서 갖고 있지 않다! 1측에 해당하는 PK인 아이디를 `참조되는 측인 N측에서 FK로 가지고 있게 된다`

→따라서 N측에서 `@ManyToOne`이 조금 더 깔끔하게 엔티티를 구성할 수 있다

## 01. N측에 `@ManyToOne` 붙이기

UserHistory 측에 `@ManyToOne`을 붙여주자

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

    @Column(name="user_id")
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

    **@ManyToOne
    private User user;**
}
```

그리고, 저번시간에 테스트했던 내용을 UserHistoryRepositoryTest에 메서드 이름만 달리하여 실행해주면

```java
@Test
    public void manyToOne(){
        User user=new User();
        user.setName("David");
        user.setEmail("david@fastcampus.com");
        user.setGender(MALE);

        userRepository.save(user);
        user.setName("daniel");
        userRepository.save(user);//update
        user.setEmail("davidToDaniel@fastcampus.com");
        userRepository.save(user);//update

        List<UserHistory> list=userRepository.findByEmail("davidToDaniel@fastcampus.com").getUserHistories();
        System.out.println(list);
    }
```

```java
Caused by: javax.persistence.PersistenceException: 
[PersistenceUnit: default] Unable to build Hibernate 
SessionFactory; nested exception is org.hibernate.
MappingException: Repeated column in mapping for entity: 
com.example.jpa_entity.domain.UserHistory 
column: user_id (should be mapped with insert="false" update="false")
```

퍼시스턴스 예외가 발생한다. 메시지를 보니, user_id에 대해서 insert=false와 update=false를 설정해주자

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

    **@Column(name="user_id",insertable = false,updatable = false)**
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

    @ManyToOne
    private User user;
}
```

이번에는 정상동작을 하지만, user 데이터가 출력되지 않았다

그러면 히스토리 값들이라도 잘 나오는지 확인해보자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Gender;
import com.example.jpa_entity.domain.User;
import com.example.jpa_entity.domain.UserHistory;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

import static com.example.jpa_entity.domain.Gender.MALE;
import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class UserHistoryRepositoryTest {

    @Autowired
    private UserHistoryRepository userHistoryRepository;

    @Autowired
    private UserRepository userRepository;

    @Test
    public void manyToOne(){
        User user=new User();
        user.setName("David");
        user.setEmail("david@fastcampus.com");
        user.setGender(MALE);

        userRepository.save(user);
        user.setName("daniel");
        userRepository.save(user);//update
        user.setEmail("davidToDaniel@fastcampus.com");
        userRepository.save(user);//update

        userHistoryRepository.findAll().forEach(System.out::println);

        List<UserHistory> list=userRepository.findByEmail("davidToDaniel@fastcampus.com").getUserHistories();
        System.out.println(list);
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
    **insert 
    into
        user_history
        (id, created_at, updated_at, active, email, gender, name, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)**
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
**UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:09:33.230, updatedAt=2021-08-30T14:09:33.230), id=1, userId=null, name=David, email=david@fastcampus.com, active=false, gender=MALE, user=null)
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:09:33.320, updatedAt=2021-08-30T14:09:33.320), id=2, userId=null, name=daniel, email=david@fastcampus.com, active=false, gender=MALE, user=null)
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:09:33.327, updatedAt=2021-08-30T14:09:33.327), id=3, userId=null, name=daniel, email=davidToDaniel@fastcampus.com, active=false, gender=MALE, user=null)**
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
[]
```

그러면 히스토리 값들은 출력이 되지만, userId값이 null이고 user도 null로 되어 있음을 확인해볼 수 있다

그 이유는 우리가 앞서 퍼시스턴스 예외 메시지를 따라서 `userId에 대해서 insertable=false, updatable=false를 지정해주었기 때문`이다

## 02. 엔티티 리스너에서 1측의 객체 주입해주기

User 객체에 대한 엔티티 리스너 클래스인 UserEntityListener에서 setter로 UserHistory 객체에 User 객체를 주입해주자

```java
package com.example.jpa_entity.domain.listener;

import com.example.jpa_entity.domain.User;
import com.example.jpa_entity.domain.UserHistory;
import com.example.jpa_entity.repository.UserHistoryRepository;
import com.example.jpa_entity.support.BeanUtils;

import javax.persistence.PostPersist;
import javax.persistence.PostUpdate;

public class UserEntityListener {

    @PostPersist
    @PostUpdate
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
        **userHistory.setUser(user);**

        userHistoryRepository.save(userHistory);
    }

}
```

그러면 이번에는 toString으로 인한 순환참조로 `StackOverflowError가 발생하였다`

## 03. toString.Exclude로 순환참조 막아주기

그래서 toString으로 될 user를 제외시켜주자 (`@ToString.Exclude`)

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

    @Column(name="user_id",insertable = false,updatable = false)
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

    **@ManyToOne
    @ToString.Exclude
    private User user;**
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
    update
        user_history 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=?,
        user_id=? 
    where
        id=?
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
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_,
        userhistor1_.user_id as user_id8_4_1_,
        userhistor1_.id as id1_4_1_,
        userhistor1_.id as id1_4_2_,
        userhistor1_.created_at as created_2_4_2_,
        userhistor1_.updated_at as updated_3_4_2_,
        userhistor1_.active as active4_4_2_,
        userhistor1_.email as email5_4_2_,
        userhistor1_.gender as gender6_4_2_,
        userhistor1_.name as name7_4_2_,
        userhistor1_.user_id as user_id8_4_2_ 
    from
        user user0_ 
    left outer join
        user_history userhistor1_ 
            on user0_.id=userhistor1_.user_id 
    where
        user0_.id=?
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:18:37.048, updatedAt=2021-08-30T14:18:37.068), id=1, userId=6, name=David, email=david@fastcampus.com, active=false, gender=MALE)
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:18:37.126, updatedAt=2021-08-30T14:18:37.126), id=2, userId=6, name=daniel, email=david@fastcampus.com, active=false, gender=MALE)
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:18:37.135, updatedAt=2021-08-30T14:18:37.135), id=3, userId=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, gender=MALE)
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
[UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:18:37.048, updatedAt=2021-08-30T14:18:37.068), id=1, userId=6, name=David, email=david@fastcampus.com, active=false, gender=MALE), UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:18:37.126, updatedAt=2021-08-30T14:18:37.126), id=2, userId=6, name=daniel, email=david@fastcampus.com, active=false, gender=MALE), UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:18:37.135, updatedAt=2021-08-30T14:18:37.135), id=3, userId=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, gender=MALE)]
```

그러면 이번에는 userId값이 null이 아닌 값이 들어왔음을 확인해볼 수 있다

하지만 user값이 보이지 않는다(toString.Exclude로 제외시켰기 때문)

그리고 지금과 같이 1과 N측 각각에서 관계를 만들어주었기 때문에, 더이상 UserHistory안에 있는 userId는 없어도 된다

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

//    @Column(name="user_id",insertable = false,updatable = false)
//    private Long userId;

    private String name;

    private String email;

//    @CreatedDate
//    private LocalDateTime createdAt;
//    @LastModifiedDate
//    private LocalDateTime updatedAt;

    private boolean active;

    @Enumerated(EnumType.STRING)
    private Gender gender;

    @ManyToOne
    @ToString.Exclude
    private User user;
}
```

```java
package com.example.jpa_entity.domain.listener;

import com.example.jpa_entity.domain.User;
import com.example.jpa_entity.domain.UserHistory;
import com.example.jpa_entity.repository.UserHistoryRepository;
import com.example.jpa_entity.support.BeanUtils;

import javax.persistence.PostPersist;
import javax.persistence.PostUpdate;

public class UserEntityListener {

    @PostPersist
    @PostUpdate
    public void prePersist(Object o){
        UserHistoryRepository userHistoryRepository= BeanUtils.getBean(UserHistoryRepository.class);
        User user=(User)o;
        //userHistory는 insert
        UserHistory userHistory=new UserHistory();
     //   userHistory.setUserId(user.getId());
        userHistory.setName(user.getName());
        userHistory.setEmail(user.getEmail());
        userHistory.setCratedAt(user.getCratedAt());
        userHistory.setUpdatedAt(user.getUpdatedAt());
        userHistory.setActive(user.isActive());
        userHistory.setGender(user.getGender());
        userHistory.setUser(user);

        userHistoryRepository.save(userHistory);
    }

}
```

```java
create table user_history (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        active boolean not null,
        email varchar(255),
        gender varchar(255),
        name varchar(255),
        **user_id bigint,**
        primary key (id)
    )
```

그리고 이렇게 지워주어도, DDL을 살펴보면 UserHistory측에 user_id가 FK로써 존재함을 확인해볼 수 있다

앞서 "하지만 user값이 보이지 않는다(toString.Exclude로 제외시켰기 때문)"이라고 말을 했는데,

이는, `양측에서 관계를 만들어주었기 때문에 양방향 관계가 되었기 때문에`, 비록 toString에서 확인은 어렵지만, `getUser`로 UserHistory측에서 확인이 가능하다!

```java
@Test
    public void manyToOne(){
        User user=new User();
        user.setName("David");
        user.setEmail("david@fastcampus.com");
        user.setGender(MALE);

        userRepository.save(user);
        user.setName("daniel");
        userRepository.save(user);//update
        user.setEmail("davidToDaniel@fastcampus.com");
        userRepository.save(user);//update

        userHistoryRepository.findAll().forEach(System.out::println);

        List<UserHistory> list=userRepository.findByEmail("davidToDaniel@fastcampus.com").getUserHistories();
        System.out.println(list);

        System.out.println("양방향: "+userHistoryRepository.findById(1L).orElseThrow(RuntimeException::new).getUser());
    }
```

```java
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
양방향: User(super=BaseEntity(cratedAt=2021-08-30T14:31:09.279, 
updatedAt=2021-08-30T14:31:09.445), id=6, name=daniel,
 email=davidToDaniel@fastcampus.com, active=false, testData=null, 
gender=MALE, userHistories=[UserHistory(super=BaseEntity
(cratedAt=2021-08-30T14:31:09.348, updatedAt=2021-08-30T14:31:09.369),
 id=1, name=David, email=david@fastcampus.com, active=false, 
gender=MALE), 
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:31:09.435,
 updatedAt=2021-08-30T14:31:09.435), id=2, name=daniel, 
email=david@fastcampus.com, active=false, gender=MALE), 
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:31:09.447,
 updatedAt=2021-08-30T14:31:09.447), id=3, name=daniel, 
email=davidToDaniel@fastcampus.com, active=false, gender=MALE)])
```

그런데, userHistories가 너무 길이가 긴데 확인하기도 복잡해서, 오히려 `UserHistory 클래스가 아닌 User 클래스 측에서 toString.Exclude를 해주는 것`이 보다 나을 것이다

그러면 UserHistory에서는 history 리스트를 확인할 수도 있고, 

User객체를 확인할 때에는 쿼리메서드와 getUser를 이용하면 history를 중복해서 확인하지 않아도 된다!(User측에서 toString에 List<UserHistory>를 반영하지 않겠다고 미리 명시해두었기 때문)

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

    **@OneToMany(fetch = FetchType.EAGER)
    @JoinColumn(name="user_id",insertable = false,updatable = false)
    @ToString.Exclude
    private List<UserHistory> userHistories=new ArrayList<>();**
}
```

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.*;

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

//    @Column(name="user_id",insertable = false,updatable = false)
//    private Long userId;

    private String name;

    private String email;

//    @CreatedDate
//    private LocalDateTime createdAt;
//    @LastModifiedDate
//    private LocalDateTime updatedAt;

    private boolean active;

    @Enumerated(EnumType.STRING)
    private Gender gender;

    @ManyToOne
    private User user;
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
    update
        user_history 
    set
        created_at=?,
        updated_at=?,
        active=?,
        email=?,
        gender=?,
        name=?,
        user_id=? 
    where
        id=?
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
Hibernate: 
    select
        user0_.id as id1_3_0_,
        user0_.created_at as created_2_3_0_,
        user0_.updated_at as updated_3_3_0_,
        user0_.active as active4_3_0_,
        user0_.email as email5_3_0_,
        user0_.gender as gender6_3_0_,
        user0_.name as name7_3_0_,
        userhistor1_.user_id as user_id8_4_1_,
        userhistor1_.id as id1_4_1_,
        userhistor1_.id as id1_4_2_,
        userhistor1_.created_at as created_2_4_2_,
        userhistor1_.updated_at as updated_3_4_2_,
        userhistor1_.active as active4_4_2_,
        userhistor1_.email as email5_4_2_,
        userhistor1_.gender as gender6_4_2_,
        userhistor1_.name as name7_4_2_,
        userhistor1_.user_id as user_id8_4_2_ 
    from
        user user0_ 
    left outer join
        user_history userhistor1_ 
            on user0_.id=userhistor1_.user_id 
    where
        user0_.id=?
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:38:13.403, updatedAt=2021-08-30T14:38:13.429), id=1, name=David, email=david@fastcampus.com, active=false, gender=MALE, user=User(super=BaseEntity(cratedAt=2021-08-30T14:38:13.314, updatedAt=2021-08-30T14:38:13.522), id=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, testData=null, gender=MALE))
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:38:13.513, updatedAt=2021-08-30T14:38:13.513), id=2, name=daniel, email=david@fastcampus.com, active=false, gender=MALE, user=User(super=BaseEntity(cratedAt=2021-08-30T14:38:13.314, updatedAt=2021-08-30T14:38:13.522), id=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, testData=null, gender=MALE))
UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:38:13.526, updatedAt=2021-08-30T14:38:13.526), id=3, name=daniel, email=davidToDaniel@fastcampus.com, active=false, gender=MALE, user=User(super=BaseEntity(cratedAt=2021-08-30T14:38:13.314, updatedAt=2021-08-30T14:38:13.522), id=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, testData=null, gender=MALE))
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
[UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:38:13.403, updatedAt=2021-08-30T14:38:13.429), id=1, name=David, email=david@fastcampus.com, active=false, gender=MALE, user=User(super=BaseEntity(cratedAt=2021-08-30T14:38:13.314, updatedAt=2021-08-30T14:38:13.522), id=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, testData=null, gender=MALE)), UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:38:13.513, updatedAt=2021-08-30T14:38:13.513), id=2, name=daniel, email=david@fastcampus.com, active=false, gender=MALE, user=User(super=BaseEntity(cratedAt=2021-08-30T14:38:13.314, updatedAt=2021-08-30T14:38:13.522), id=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, testData=null, gender=MALE)), UserHistory(super=BaseEntity(cratedAt=2021-08-30T14:38:13.526, updatedAt=2021-08-30T14:38:13.526), id=3, name=daniel, email=davidToDaniel@fastcampus.com, active=false, gender=MALE, user=User(super=BaseEntity(cratedAt=2021-08-30T14:38:13.314, updatedAt=2021-08-30T14:38:13.522), id=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, testData=null, gender=MALE))]
Hibernate: 
    select
        userhistor0_.id as id1_4_0_,
        userhistor0_.created_at as created_2_4_0_,
        userhistor0_.updated_at as updated_3_4_0_,
        userhistor0_.active as active4_4_0_,
        userhistor0_.email as email5_4_0_,
        userhistor0_.gender as gender6_4_0_,
        userhistor0_.name as name7_4_0_,
        userhistor0_.user_id as user_id8_4_0_,
        user1_.id as id1_3_1_,
        user1_.created_at as created_2_3_1_,
        user1_.updated_at as updated_3_3_1_,
        user1_.active as active4_3_1_,
        user1_.email as email5_3_1_,
        user1_.gender as gender6_3_1_,
        user1_.name as name7_3_1_ 
    from
        user_history userhistor0_ 
    left outer join
        user user1_ 
            on userhistor0_.user_id=user1_.id 
    where
        userhistor0_.id=?
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
양방향: User(super=BaseEntity(cratedAt=2021-08-30T14:38:13.314, updatedAt=2021-08-30T14:38:13.522), id=6, name=daniel, email=davidToDaniel@fastcampus.com, active=false, testData=null, gender=MALE)
```

→ jpa에서는 FK를 이용하기 보다, getter로 연결되는 값을 가져올 수 있다

## 연습하기-위의 ERD를 참고로 도메인을 만들고 연습하기

먼저 N:N을 배우지 않았지만, 직감적으로 비슷한 흐름으로 Author를 작성했으니 해당 클래스만 양해바란다

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.*;

@Data
@NoArgsConstructor
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper = true)
@Entity
public class Review extends BaseEntity{
    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String content;
    private double score;

    //book_id,user_id
    @ManyToOne
    @ToString.Exclude
    private User user;

    @ManyToOne
    @ToString.Exclude
    private Book book;
}
```

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Data
@NoArgsConstructor
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper = true)
@Entity
public class Publisher extends BaseEntity{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany
    @JoinColumn(name="publisher_id")
    @ToString.Exclude
    private List<Book> books=new ArrayList<>();
}
```

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Data
@NoArgsConstructor
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper = true)
@Entity
public class Author extends BaseEntity{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String country;

    @ManyToMany
    private List<Book> books=new ArrayList<>();
}
```

그리고 생성한 세 개의 도메인에 대해서 레포지토리도 만들어두자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Review;
import org.springframework.data.jpa.repository.JpaRepository;

public interface ReviewRepository extends JpaRepository<Review,Long> {
}
```

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Publisher;
import org.springframework.data.jpa.repository.JpaRepository;

public interface PublisherRepository extends JpaRepository<Publisher,Long> {
}
```

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Author;
import org.springframework.data.jpa.repository.JpaRepository;

public interface AuthorRepository extends JpaRepository<Author,Long> {
}
```

그런데 Book과 Review는 1:N의 상황이기 때문에 Book측에서도 수정해줄 수 있다

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
import java.util.ArrayList;
import java.util.List;

@Data
@NoArgsConstructor
@Entity
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
///@EntityListeners(value=MyEntityListener.class)
//@EntityListeners(value= AuditingEntityListener.class)
public class Book extends BaseEntity/* implements Auditable*/ {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    //책 이름
    private String name;

    private String category;

    //책 저자
    private Long authorId;

    private Long publisherId;

    @OneToOne(mappedBy = "book")
    @ToString.Exclude
    private BookReviewInfo bookReviewInfo;

    **@OneToMany
    @JoinColumn(name="book_id")
    @ToString.Exclude**
    private List<Review> reviews=new ArrayList<>();
}
```

더불어 Book과 Publisher도 N:1의 관계이므로 Book에서도 아래처럼 관계를 명시해줄 수 있다

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
import java.util.ArrayList;
import java.util.List;

@Data
@NoArgsConstructor
@Entity
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
///@EntityListeners(value=MyEntityListener.class)
//@EntityListeners(value= AuditingEntityListener.class)
public class Book extends BaseEntity/* implements Auditable*/ {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    //책 이름
    private String name;

    private String category;

    //책 저자
    private Long authorId;

    private Long publisherId;

    @OneToOne(mappedBy = "book")
    @ToString.Exclude
    private BookReviewInfo bookReviewInfo;

    @OneToMany
    @JoinColumn(name="book_id")
    @ToString.Exclude
    private List<Review> reviews=new ArrayList<>();

    **@ManyToOne
    @ToString.Exclude
    private Publisher publisher;**
}
```

마찬가지로 User의 입장에서 Review 는 1:N의 관계라서 아래처럼 OneToMany로 이어줄 수 있다

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
    @JoinColumn(name="user_id",insertable = false,updatable = false)
    @ToString.Exclude
    private List<UserHistory> userHistories=new ArrayList<>();

    **@OneToMany
    @JoinColumn(name="user_id")
    @ToString.Exclude
    private List<Review> reviews=new ArrayList<>();**
}
```

이 상황에서 기존의 테스트를 돌려보면, 

```java
Caused by: org.hibernate.DuplicateMappingException: 
Table [book] contains physical column name [publisher_id] 
referred to by multiple logical column names: [publisher_id], 
[publisherId]
```

book내부에 이미 publisherId가 있어서 ManyToOne에 의해 연결되는 publisher_id로 인한 복제 매핑 충돌이 일어난다

따라서 기존의 long publisherId를 지워주자

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
import java.util.ArrayList;
import java.util.List;

@Data
@NoArgsConstructor
@Entity
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
///@EntityListeners(value=MyEntityListener.class)
//@EntityListeners(value= AuditingEntityListener.class)
public class Book extends BaseEntity/* implements Auditable*/ {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    //책 이름
    private String name;

    private String category;

    //책 저자
    private Long authorId;

//    private Long publisherId;

    @OneToOne(mappedBy = "book")
    @ToString.Exclude
    private BookReviewInfo bookReviewInfo;

    @OneToMany
    @JoinColumn(name="book_id")
    @ToString.Exclude
    private List<Review> reviews=new ArrayList<>();

    @ManyToOne
    @ToString.Exclude
    private Publisher publisher;
}
```

그러면 Book과 관련된 RepositoryTest에 존재하는 테스트 메서드 내부의 setter가 문제된다(필드가 존재하지 않아서)

깔끔하게 setPublisherId를 주석처리하자

(예시)-BookRepositoryTest

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Book;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class BookRepositoryTest {
    @Autowired
    private BookRepository bookRepository;

    @BeforeEach
    public void init(){
        System.out.println("insert");
        Book book=new Book();
        book.setName("테스트");
        book.setAuthorId(1L);
      //  book.setPublisherId(1L);
        bookRepository.save(book);
        System.out.println("insert 끝");
    }

    @Test
    public void bookTest(){
        Book book=new Book();
        book.setName("jpa 초격차 패키지");
        book.setAuthorId(2L);
        book.setPublisherId(2L);

        bookRepository.save(book);

        bookRepository.findAll().forEach(System.out::println);
    }

    @Test
    public void listenerTest(){
        bookRepository.findAll().forEach(System.out::println);
//Book(id=6, name=테스트, author=테스트, createdAt=2021-08-24T15:10:22.153, updatedAt=2021-08-24T15:10:22.153)
        System.out.println("update");
        Book book2=bookRepository.findById(6L).orElseThrow(RuntimeException::new);
        book2.setName("수정");
        bookRepository.save(book2);
        System.out.println("update 끝");
        bookRepository.findAll().forEach(System.out::println);
    }
}
```

그리고 Book, User, Publisher,Review DDL을 살펴보면

N측에 해당되는 Book과 Review테이블에 FK로써 pushlisher_id, book_id, user_id가 생성되었음을 확인해볼 수있다

```java
Hibernate: 
    
    create table book (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        author_id bigint,
        category varchar(255),
        name varchar(255),
        **publisher_id bigint**,
        primary key (id)
    )

Hibernate: 
    
    create table publisher (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        name varchar(255),
        primary key (id)
    )
Hibernate: 
    
    create table review (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        content varchar(255),
        score double not null,
        title varchar(255),
        **book_id bigint,
        user_id bigint,**
        primary key (id)
    )
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
```

먼저 Author를 제외한 나머지 관계들이 Book과 관계되어 있어서 BookRepositoryTest에서 데이터를 미리 저장하도록 한 후, 접근하도록 테스트해보자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Book;
import com.example.jpa_entity.domain.Publisher;
import com.example.jpa_entity.domain.Review;
import com.example.jpa_entity.domain.User;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class BookRepositoryTest {
    @Autowired
    private BookRepository bookRepository;

    @Autowired
    private PublisherRepository publisherRepository;

    @Autowired
    private ReviewRepository reviewRepository;

    @Autowired
    private UserRepository userRepository;

    private User givenUser(){
        return userRepository.findByEmail("martin@fastcampus.com");
    }

    private Publisher givenPublisher(){
        Publisher publisher=new Publisher();

        publisher.setName("테스트 publisher");
        publisherRepository.save(publisher);

        return publisher;
    }

    private Book givenBook(Publisher publisher) {
        Book book=new Book();
        book.setName("책입니당");
        book.setPublisher(publisher);

        bookRepository.save(book);

        return book;
    }

    private void givenReview(User user, Book book) {
        Review review= new Review();

        review.setTitle("테스트 인 부트");
        review.setContent("잼잼!");
        review.setScore(5.0f);
        review.setUser(user);
        review.setBook(book);

        reviewRepository.save(review);
    }

    private void givenBookAndReview() {
        User user=givenUser();
        Publisher publisher=givenPublisher();
        Book book=givenBook(publisher);
        givenReview(user,book);
    }

    @Test
    public void bookRelationTest(){
        //Book정보와 Review 정보를 저장
        givenBookAndReview();
        User user=userRepository.findByEmail("martin@fastcampus.com");
        System.out.println("User : (userRepository)-"+user);
        System.out.println("Review<-User: "+user.getReviews());
        System.out.println("Book<-Review<-User: "+user.getReviews().get(0).getBook());
        System.out.println("Publisher<-Book<-Review<-User: "+user.getReviews().get(0).getBook().getPublisher());
    }

}
```

그러면 아래처럼 저번에 보았던 LazyInitializationException이 발생하였다

```java
failed to lazily initialize a collection of role: com.example.jpa_entity.domain.User.reviews, could not initialize proxy - no Session
```

그래서 메세지 내용 그대로, User에 대해서 Review 관계부분에 FetchType.EAGER를 적어주자

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
    @JoinColumn(name="user_id",insertable = false,updatable = false)
    @ToString.Exclude
    private List<UserHistory> userHistories=new ArrayList<>();

    @OneToMany(fetch = FetchType.EAGER)
    @JoinColumn(name="user_id")
    @ToString.Exclude
    private List<Review> reviews=new ArrayList<>();
}
```

그런데 `org.hibernate.loader.MultipleBagFetchException` 이라는 예외가 발생했는데

이는, Bag이라는 구조가 있는데 이 구조가 List처럼 중복을 허용하는 자료구조라고 하는데 , 여러 BagType을 동시에 fetch해올 때 발생하는 에러라고 한다

[(Troubleshooting) Hibernate MultipleBagFetchException 정복하기](https://perfectacle.github.io/2019/05/01/hibernate-multiple-bag-fetch-exception/)

우리는 대신에, 테스트 메서드 위에 `@Transactional`을 붙여주자

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
    @JoinColumn(name="user_id",insertable = false,updatable = false)
    @ToString.Exclude
    private List<UserHistory> userHistories=new ArrayList<>();

    @OneToMany
    @JoinColumn(name="user_id")
    @ToString.Exclude
    private List<Review> reviews=new ArrayList<>();
}
```

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Book;
import com.example.jpa_entity.domain.Publisher;
import com.example.jpa_entity.domain.Review;
import com.example.jpa_entity.domain.User;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import javax.transaction.Transactional;

@SpringBootTest
class BookRepositoryTest {
    @Autowired
    private BookRepository bookRepository;

    @Autowired
    private PublisherRepository publisherRepository;

    @Autowired
    private ReviewRepository reviewRepository;

    @Autowired
    private UserRepository userRepository;

    @BeforeEach
    public void init(){
        System.out.println("insert");
        Book book=new Book();
        book.setName("테스트");
        book.setAuthorId(1L);
      //  book.setPublisherId(1L);
        bookRepository.save(book);
        System.out.println("insert 끝");
    }

    @Test
    public void bookTest(){
        Book book=new Book();
        book.setName("jpa 초격차 패키지");
        book.setAuthorId(2L);
      //  book.setPublisherId(2L);

        bookRepository.save(book);

        bookRepository.findAll().forEach(System.out::println);
    }

    @Test
    public void listenerTest(){
        bookRepository.findAll().forEach(System.out::println);
//Book(id=6, name=테스트, author=테스트, createdAt=2021-08-24T15:10:22.153, updatedAt=2021-08-24T15:10:22.153)
        System.out.println("update");
        Book book2=bookRepository.findById(6L).orElseThrow(RuntimeException::new);
        book2.setName("수정");
        bookRepository.save(book2);
        System.out.println("update 끝");
        bookRepository.findAll().forEach(System.out::println);
    }

    private User givenUser(){
        return userRepository.findByEmail("martin@fastcampus.com");
    }

    private Publisher givenPublisher(){
        Publisher publisher=new Publisher();

        publisher.setName("테스트 publisher");
        publisherRepository.save(publisher);

        return publisher;
    }

    private Book givenBook(Publisher publisher) {
        Book book=new Book();
        book.setName("책입니당");
        book.setPublisher(publisher);

        bookRepository.save(book);

        return book;
    }

    private void givenReview(User user, Book book) {
        Review review= new Review();

        review.setTitle("테스트 인 부트");
        review.setContent("잼잼!");
        review.setScore(5.0f);
        review.setUser(user);
        review.setBook(book);

        reviewRepository.save(review);
    }

    private void givenBookAndReview() {
        User user=givenUser();
        Publisher publisher=givenPublisher();
        Book book=givenBook(publisher);
        givenReview(user,book);
    }

    @Test
    @Transactional
    public void bookRelationTest(){
        //Book정보와 Review 정보를 저장
        givenBookAndReview();
        User user=userRepository.findByEmail("martin@fastcampus.com");
        System.out.println("User : (userRepository)-"+user);
        System.out.println("Review<-User: "+user.getReviews());
        System.out.println("Book<-Review<-User: "+user.getReviews().get(0).getBook());
        System.out.println("Publisher<-Book<-Review<-User: "+user.getReviews().get(0).getBook().getPublisher());
    }

}
```

```java
Hibernate: 
    insert 
    into
        book
        (id, created_at, updated_at, author_id, category, name, publisher_id) 
    values
        (null, ?, ?, ?, ?, ?, ?)
insert 끝
Hibernate: 
    select
        user0_.id as id1_7_,
        user0_.created_at as created_2_7_,
        user0_.updated_at as updated_3_7_,
        user0_.active as active4_7_,
        user0_.email as email5_7_,
        user0_.gender as gender6_7_,
        user0_.name as name7_7_ 
    from
        user user0_ 
    where
        user0_.email=?
Hibernate: 
    select
        userhistor0_.user_id as user_id8_8_0_,
        userhistor0_.id as id1_8_0_,
        userhistor0_.id as id1_8_1_,
        userhistor0_.created_at as created_2_8_1_,
        userhistor0_.updated_at as updated_3_8_1_,
        userhistor0_.active as active4_8_1_,
        userhistor0_.email as email5_8_1_,
        userhistor0_.gender as gender6_8_1_,
        userhistor0_.name as name7_8_1_,
        userhistor0_.user_id as user_id8_8_1_ 
    from
        user_history userhistor0_ 
    where
        userhistor0_.user_id=?
Hibernate: 
    insert 
    into
        publisher
        (id, created_at, updated_at, name) 
    values
        (null, ?, ?, ?)
Hibernate: 
    insert 
    into
        book
        (id, created_at, updated_at, author_id, category, name, publisher_id) 
    values
        (null, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        review
        (id, created_at, updated_at, book_id, content, score, title, user_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_7_,
        user0_.created_at as created_2_7_,
        user0_.updated_at as updated_3_7_,
        user0_.active as active4_7_,
        user0_.email as email5_7_,
        user0_.gender as gender6_7_,
        user0_.name as name7_7_ 
    from
        user user0_ 
    where
        user0_.email=?
User : (userRepository)-User(super=BaseEntity(cratedAt=2021-08-30T16:35:10.650, updatedAt=2021-08-30T16:35:10.650), id=1, name=martin, email=martin@fastcampus.com, active=true, testData=null, gender=null)
Hibernate: 
    select
        reviews0_.user_id as user_id8_6_0_,
        reviews0_.id as id1_6_0_,
        reviews0_.id as id1_6_1_,
        reviews0_.created_at as created_2_6_1_,
        reviews0_.updated_at as updated_3_6_1_,
        reviews0_.book_id as book_id7_6_1_,
        reviews0_.content as content4_6_1_,
        reviews0_.score as score5_6_1_,
        reviews0_.title as title6_6_1_,
        reviews0_.user_id as user_id8_6_1_,
        book1_.id as id1_3_2_,
        book1_.created_at as created_2_3_2_,
        book1_.updated_at as updated_3_3_2_,
        book1_.author_id as author_i4_3_2_,
        book1_.category as category5_3_2_,
        book1_.name as name6_3_2_,
        book1_.publisher_id as publishe7_3_2_,
        publisher2_.id as id1_5_3_,
        publisher2_.created_at as created_2_5_3_,
        publisher2_.updated_at as updated_3_5_3_,
        publisher2_.name as name4_5_3_,
        bookreview3_.id as id1_4_4_,
        bookreview3_.created_at as created_2_4_4_,
        bookreview3_.updated_at as updated_3_4_4_,
        bookreview3_.average_review_score as average_4_4_4_,
        bookreview3_.book_id as book_id6_4_4_,
        bookreview3_.review_count as review_c5_4_4_ 
    from
        review reviews0_ 
    left outer join
        book book1_ 
            on reviews0_.book_id=book1_.id 
    left outer join
        publisher publisher2_ 
            on book1_.publisher_id=publisher2_.id 
    left outer join
        book_review_info bookreview3_ 
            on book1_.id=bookreview3_.book_id 
    where
        reviews0_.user_id=?
Review<-User: [Review(super=BaseEntity(cratedAt=2021-08-30T16:35:11.233, updatedAt=2021-08-30T16:35:11.233), id=1, title=테스트 인 부트, content=잼잼!, score=5.0)]
Book<-Review<-User: Book(super=BaseEntity(cratedAt=2021-08-30T16:35:11.221, updatedAt=2021-08-30T16:35:11.221), id=2, name=책입니당, category=null, authorId=null)
Publisher<-Book<-Review<-User: Publisher(super=BaseEntity(cratedAt=2021-08-30T16:35:11.214, updatedAt=2021-08-30T16:35:11.214), id=1, name=테스트 publisher)
```

테스트를 실행해보면, 결과적으로는 지금은 Author에 대해서 별다른 설정을 해주지 않았으므로 null인 것은 당연하고

🌟getter로 접근해서 데이터를 가져올 수 있음을 확인해볼 수 있다(jpa의 장점)