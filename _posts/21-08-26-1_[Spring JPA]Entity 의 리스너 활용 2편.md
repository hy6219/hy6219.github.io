# Entity의 Listener 활용 2편

## 01. 히스토리 데이터

- `히스토리 데이터는 DB에 특정 부분이 수정되면 해당 값의 복사본을 다른 테이블에 저장`해둠

`UserHistory` 라는 엔티티를 추가적으로 만들어보자

User 엔티티와 동일하게 진행하고, 거기에 userId라는 컬럼만 추가해보자

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
public class UserHistory {
    @Id
    @GeneratedValue
    private Long id;

    private Long userId;

    private String name;

    private String email;

    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

    private boolean active;
    
    @Enumerated(EnumType.STRING)
    private Gender gender;
}
```

이어서 레포지토리도 만들어보자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.UserHistory;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserHistoryRepository extends JpaRepository<UserHistory,Long> {
    
}
```

그리고 리스너를 만드는데 여기서는

- User엔티티의 값을 히스토리에 `저장 insert` 하는 것이 목적

으로 하는데, 갱신전에 진행되도록 해보자

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.repository.UserHistoryRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.persistence.PreUpdate;

@Component
public class UserEntityListener {
    //autowired를 위해서 listener를 스프링 빈으로 등록
    //field injection은 바람직하지 않음
    @Autowired
    private UserHistoryRepository userHistoryRepository;

    @PreUpdate
    public void preUpdate(Object o){
        User user=(User)o;
        //userHistory는 insert
        UserHistory userHistory=new UserHistory();
        userHistory.setUserId(user.getId());
        userHistory.setName(user.getName());
        userHistory.setEmail(user.getEmail());
        userHistory.setCreatedAt(user.getCreatedAt());
        userHistory.setUpdatedAt(user.getUpdatedAt());
        userHistory.setActive(user.isActive());
        userHistory.setGender(user.getGender());

        userHistoryRepository.save(userHistory);
    }
}
```

그리고  User 엔티티에 `@EntityListeners(value={})` 를 이용해서 지난번 리스너 외에 지금 만든 리스너를 등록해서 `여러개의 리스너를 만들어 등록`해보자

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
**@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})**
public class User implements Auditable{
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
    private LocalDateTime createdAt;
    @Column(name="updated_at")
    private LocalDateTime updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

}
```

이제 테스트를 해보도록 하자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.User;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class UserHistoryRepositoryTest {

    @Autowired
    private UserHistoryRepository userHistoryRepository;

    @Autowired
    private UserRepository userRepository;

    @Test
    public void preUpdateListenerTest(){
        User user=new User();
        user.setEmail("abcd@fastcampus.com");
        user.setName("abcd");
				user.setGender(Gender.MALE);

        userRepository.save(user);
        System.out.println("---insert완료");

        user.setName("abcde");
        userRepository.save(user);
        System.out.println("---update완료");

        userHistoryRepository.findAll().forEach(System.out::println);
    }

}
```

먼저 User 엔티티를 저장하고, 수정해서 반영한 후, userHistory에 반영이 되었는지 확인해보자

그런데 이를 실행해보면 아래와 같이 `리스너에서 NullPointerException`이 발생한다

```java
Caused by: java.lang.NullPointerException
	at com.example.jpa_entity.domain.UserEntityListener.preUpdate
```

🌟이는, 엔티티 리스너는 스프링빈을 주입받지 못하기 때문에 내부에서 `@Autowired`로 객체주입을 해줄 수 없다![MyEntityListener에서는 주입해준 적이 없었던 것을 비교해서 생각해보자!]

### 01-1. 엔티티 리스너의 의존성 주입 해결하기

위의 문제를 해결하기 위해서 스프링 빈을 가져올 수 있도록 특별한 클래스를 만들어보자

🌟 이전에 IoC/DI 부분에서 배웠던 ApplicationContextAware를 이용한 방법이다!

- ApplicationContextAware를 구현하는 클래스를 만들고
- 그 클래스는 객체 주입이 가능해지도록 `@Component`를 클래스 위에 붙여주자
- 그런데, 다만, 추가적으로 이번에는 제네릭스를 이용하고, 매개변수로 클래스를 받아서 `getBean`을 이용해서(`context.getBean(class)`) `클래스에 맞는 빈을 리턴`해주도록 하자!!
- 위의 부분☝️을 통해서 이전보다는 더 클래스에 맞도록 빈을 돌려주는 방법인 것 같다!
- 이전에는 context를 찾은 후 바로 그 context를 리턴하면 이를 이용해서 다른 클래스 getBean을 하는 방법이었다! 차이가 있다!

[TIL/[Spring]IoC and DI.md at main · hy6219/TIL](https://github.com/hy6219/TIL/blob/main/Spring/KeyPoint/%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84%EA%B3%BC%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%A3%BC%EC%9E%85/%5BSpring%5DIoC%20and%20DI.md)

```java
package com.example.jpa_entity.support;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

@Component
public class BeanUtils implements ApplicationContextAware {

    private static ApplicationContext context;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        BeanUtils.context=applicationContext;
    }

    public static ApplicationContext getContext(){
        return BeanUtils.context;
    }
}
```

```java
package com.example.jpa_entity.support;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

@Component
public class BeanUtils implements ApplicationContextAware {

    private static ApplicationContext context;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        BeanUtils.context=applicationContext;
    }

    **public static <T> T getBean(Class<T> clazz){
        return BeanUtils.context.getBean(clazz);
    }**
}
```

그리고 이를 이용해서 엔티티 리스너에서 `UserHistoryRepository의 빈을 찾으면 된다`

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.repository.UserHistoryRepository;
import com.example.jpa_entity.support.BeanUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.persistence.PreUpdate;

public class UserEntityListener {

    @PreUpdate
    public void preUpdate(Object o){
        **UserHistoryRepository userHistoryRepository= BeanUtils.getBean(UserHistoryRepository.class);**
        User user=(User)o;
        //userHistory는 insert
        UserHistory userHistory=new UserHistory();
        userHistory.setUserId(user.getId());
        userHistory.setName(user.getName());
        userHistory.setEmail(user.getEmail());
        userHistory.setCreatedAt(user.getCreatedAt());
        userHistory.setUpdatedAt(user.getUpdatedAt());
        userHistory.setActive(user.isActive());
        userHistory.setGender(user.getGender());

        userHistoryRepository.save(userHistory);
    }
}
```

```java
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user
        (active, created_at, email, gender, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?, ?, ?)
---insert완료
Hibernate: 
    select
        user0_.id as id1_2_0_,
        user0_.active as active2_2_0_,
        user0_.created_at as created_3_2_0_,
        user0_.email as email4_2_0_,
        user0_.gender as gender5_2_0_,
        user0_.name as name6_2_0_,
        user0_.updated_at as updated_7_2_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user_history
        (active, created_at, email, gender, name, updated_at, user_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    update
        user 
    set
        active=?,
        created_at=?,
        email=?,
        gender=?,
        name=?,
        updated_at=? 
    where
        id=?
---update완료
Hibernate: 
    select
        userhistor0_.id as id1_3_,
        userhistor0_.active as active2_3_,
        userhistor0_.created_at as created_3_3_,
        userhistor0_.email as email4_3_,
        userhistor0_.gender as gender5_3_,
        userhistor0_.name as name6_3_,
        userhistor0_.updated_at as updated_7_3_,
        userhistor0_.user_id as user_id8_3_ 
    from
        user_history userhistor0_
**UserHistory(id=7, userId=6, name=abcde, email=abcd@fastcampus.com, createdAt=2021-08-25T20:00:29.111, updatedAt=2021-08-25T20:00:29.226, active=false, gender=MALE)**
```

그러면 위와 같이 아이디가 6인 User가 update 전에 감지되어 히스토리에 저장된 것을 확인해볼 수 있다

### 📌01-2. insert 전에도 감지해서 히스토리에 저장하도록 하기

이번에는 삽입 전에도 감지해서 작동되는 리스너를 만들어보자

그러면 아래처럼 어노테이션을 하나 더 붙여주면 되고,

메서드명도 적절하게 변경해주면 된다!

🌟 이렇게 insert와 update 전에 엔티티 이벤트를 감지하면, 새로운 엔티티가 추가될 때마다, 그리고 엔티티가 수정될 때마다 히스토리에 데이터가 차곡차곡 쌓이게 된다!

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.repository.UserHistoryRepository;
import com.example.jpa_entity.support.BeanUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.persistence.PrePersist;
import javax.persistence.PreUpdate;

public class UserEntityListener {

    @PrePersist
    @PreUpdate
    public void prePersistAndUpdate(Object o){
        UserHistoryRepository userHistoryRepository= BeanUtils.getBean(UserHistoryRepository.class);
        User user=(User)o;
        //userHistory는 insert
        UserHistory userHistory=new UserHistory();
        userHistory.setUserId(user.getId());
        userHistory.setName(user.getName());
        userHistory.setEmail(user.getEmail());
        userHistory.setCreatedAt(user.getCreatedAt());
        userHistory.setUpdatedAt(user.getUpdatedAt());
        userHistory.setActive(user.isActive());
        userHistory.setGender(user.getGender());

        userHistoryRepository.save(userHistory);
    }
}
```

이에 걸맞게 테스트하는 메서드의 이름도 변경해주자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Gender;
import com.example.jpa_entity.domain.User;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class UserHistoryRepositoryTest {

    @Autowired
    private UserHistoryRepository userHistoryRepository;

    @Autowired
    private UserRepository userRepository;

    @Test
    public void prePersistAndUpdateListenerTest(){
        User user=new User();
        user.setEmail("abcd@fastcampus.com");
        user.setName("abcd");
        user.setGender(Gender.MALE);

        userRepository.save(user);
        System.out.println("---insert완료");

        user.setName("abcde");
        userRepository.save(user);
        System.out.println("---update완료");

        userHistoryRepository.findAll().forEach(System.out::println);
    }

}
```

```java
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user_history
        (active, created_at, email, gender, name, updated_at, user_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user
        (active, created_at, email, gender, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?, ?, ?)
---insert완료
Hibernate: 
    select
        user0_.id as id1_2_0_,
        user0_.active as active2_2_0_,
        user0_.created_at as created_3_2_0_,
        user0_.email as email4_2_0_,
        user0_.gender as gender5_2_0_,
        user0_.name as name6_2_0_,
        user0_.updated_at as updated_7_2_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user_history
        (active, created_at, email, gender, name, updated_at, user_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    update
        user 
    set
        active=?,
        created_at=?,
        email=?,
        gender=?,
        name=?,
        updated_at=? 
    where
        id=?
---update완료
Hibernate: 
    select
        userhistor0_.id as id1_3_,
        userhistor0_.active as active2_3_,
        userhistor0_.created_at as created_3_3_,
        userhistor0_.email as email4_3_,
        userhistor0_.gender as gender5_3_,
        userhistor0_.name as name6_3_,
        userhistor0_.updated_at as updated_7_3_,
        userhistor0_.user_id as user_id8_3_ 
    from
        user_history userhistor0_
UserHistory(id=6, userId=null, name=abcd, email=abcd@fastcampus.com, createdAt=2021-08-25T20:08:51.218, updatedAt=2021-08-25T20:08:51.218, active=false, gender=MALE)
UserHistory(id=8, userId=7, name=abcde, email=abcd@fastcampus.com, createdAt=2021-08-25T20:08:51.218, updatedAt=2021-08-25T20:08:51.339, active=false, gender=MALE)
```

그러면 이번에는 insert 전에 가져와서 정보를 저장한 데이터와 update 전에 가져와서 정보를 저장한 데이터를 확인해볼 수 있는데

insert 전에 가져온 데이터에서는 말 그대로 삽입 전에 가져왔기 때문에 자동으로 카운트되도록 된 User의 PK인 id값이 세팅도 되기 전에 가져온 것이므로 userId가 null로 되어 있는 것을 확인해볼 수 있다

추가적으로, 만약의 경우에 insert와 update 전의 값들을 가져오지 못하거나, 이전에 설정했던 createdAt이나 updatedAt이 null이라면 아래처럼 리스너를 연결해주고,

MyEntityListener에서 사용하는 인터페이스인 Auditable을 붙여주자

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
@EntityListeners(value={MyEntityListener.class})
public class UserHistory implements Auditable{
    @Id
    @GeneratedValue
    private Long id;

    private Long userId;

    private String name;

    private String email;

    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

    private boolean active;

    @Enumerated(EnumType.STRING)
    private Gender gender;
}
```

## 02. 스프링에서 제공되는 기본 리스너 `@EnableJpaAuditing`, `AuditingEntityListener.class, @CreatedDate, @LastModifiedDate`

🌟먼저, `~Application.java`에 들어가서 `@EnableJpaAuditing`이라는 어노테이션을 추가해주자!

```java
package com.example.jpa_entity;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@SpringBootApplication
@EnableJpaAuditing
public class JpaEntityApplication {

    public static void main(String[] args) {
        SpringApplication.run(JpaEntityApplication.class, args);
    }

}
```

그리고, User엔티티와 Book엔티티, UserHistory엔티티에서 MyEntityListener가 아닌 `AuditingEntityListener를 연결해주자`

```java
package com.example.jpa_entity.domain;

import lombok.*;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
//@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})
@EntityListeners(value={**AuditingEntityListener.class**,UserEntityListener.class})
public class User implements Auditable{
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
    private LocalDateTime createdAt;
    @Column(name="updated_at")
    private LocalDateTime updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

}
```

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDate;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
///@EntityListeners(value=MyEntityListener.class)
**@EntityListeners(value= AuditingEntityListener.class)**
public class Book implements Auditable{
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    //책 이름
    private String name;
    //책 저자
    private String author;
    //생성일
    private LocalDateTime createdAt;
    //수정일
    private LocalDateTime updatedAt;

}
```

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
//@EntityListeners(value={MyEntityListener.class})
**@EntityListeners(value={AuditingEntityListener.class})**
public class UserHistory implements Auditable{
    @Id
    @GeneratedValue
    private Long id;

    private Long userId;

    private String name;

    private String email;

    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

    private boolean active;

    @Enumerated(EnumType.STRING)
    private Gender gender;
}
```

그리고 `Auditing으로 지정해야 할 데이터 중 createdDate에 @CreatedDate` ,

`Auditing으로 지정해야 할 데이터 중 updatedAt에 @LastModifiedDate`를 붙여주자

```java
package com.example.jpa_entity.domain;

import lombok.*;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
//@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})
@EntityListeners(value={AuditingEntityListener.class,UserEntityListener.class})
public class User implements Auditable{
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
    @CreatedDate
    private LocalDateTime createdAt;
    @Column(name="updated_at")
    @LastModifiedDate
    private LocalDateTime updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

}
```

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDate;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
///@EntityListeners(value=MyEntityListener.class)
@EntityListeners(value= AuditingEntityListener.class)
public class Book implements Auditable{
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    //책 이름
    private String name;
    //책 저자
    private String author;
    //생성일
    @CreatedDate
    private LocalDateTime createdAt;
    //수정일
    @LastModifiedDate
    private LocalDateTime updatedAt;

}
```

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
//@EntityListeners(value={MyEntityListener.class})
@EntityListeners(value={AuditingEntityListener.class})
public class UserHistory implements Auditable{
    @Id
    @GeneratedValue
    private Long id;

    private Long userId;

    private String name;

    private String email;

    @CreatedDate
    private LocalDateTime createdAt;
    @LastModifiedDate
    private LocalDateTime updatedAt;

    private boolean active;

    @Enumerated(EnumType.STRING)
    private Gender gender;
}
```

동일한 테스트를 진행해보자!

```java
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user_history
        (active, created_at, email, gender, name, updated_at, user_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user
        (active, created_at, email, gender, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?, ?, ?)
---insert완료
Hibernate: 
    select
        user0_.id as id1_2_0_,
        user0_.active as active2_2_0_,
        user0_.created_at as created_3_2_0_,
        user0_.email as email4_2_0_,
        user0_.gender as gender5_2_0_,
        user0_.name as name6_2_0_,
        user0_.updated_at as updated_7_2_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user_history
        (active, created_at, email, gender, name, updated_at, user_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    update
        user 
    set
        active=?,
        created_at=?,
        email=?,
        gender=?,
        name=?,
        updated_at=? 
    where
        id=?
---update완료
Hibernate: 
    select
        userhistor0_.id as id1_3_,
        userhistor0_.active as active2_3_,
        userhistor0_.created_at as created_3_3_,
        userhistor0_.email as email4_3_,
        userhistor0_.gender as gender5_3_,
        userhistor0_.name as name6_3_,
        userhistor0_.updated_at as updated_7_3_,
        userhistor0_.user_id as user_id8_3_ 
    from
        user_history userhistor0_
UserHistory(id=6, userId=null, name=abcd, email=abcd@fastcampus.com, createdAt=2021-08-25T20:25:17.779, updatedAt=2021-08-25T20:25:17.779, active=false, gender=MALE)
UserHistory(id=8, userId=7, name=abcde, email=abcd@fastcampus.com, createdAt=2021-08-25T20:25:17.899, updatedAt=2021-08-25T20:25:17.899, active=false, gender=MALE)
```

그러면 앞에서 살펴본 결과와 동일하게 insert 및 update 전에 가져온 데이터로 저장된 모습을 확인해볼 수 있다!

즉, 자동으로 적용될 수 있다는 점이다!

그리고 위에서 살펴본 `@CreatedDate`나 `@LastModifiedDate`외에도 org.springframework.data:spring-data-commons:2.5.3을 살펴보면

AccessType, createdBy 등 다양한 어노테이션이 있는 것을 알 수 있다!

특히 createdBy나 LastModifiedBy는 스프링 시큐리티와 연동하면 더 쉽다고 하다! 나중에 스프링시큐리티를 공부하면서 생각해봐야겠다!

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/Spring%20%EA%B8%B0%EB%B3%B8%20%EB%A6%AC%EC%8A%A4%EB%84%88%EB%A1%9C%20%EC%A0%91%EA%B7%BC%EA%B0%80%EB%8A%A5%ED%95%9C%20%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98%EB%93%A4.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/Spring%20%EA%B8%B0%EB%B3%B8%20%EB%A6%AC%EC%8A%A4%EB%84%88%EB%A1%9C%20%EC%A0%91%EA%B7%BC%EA%B0%80%EB%8A%A5%ED%95%9C%20%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98%EB%93%A4.PNG?raw=true)

## 📌📌03. 실제로 사용되는 리스너의 모습 더 알아보기! `@MappedSuperClass를 이용한 공통 부분이 모여진 클래스를 만들어서 리스너와 연결해서 사용하기`

먼저, 클래스 구조가 다소 복잡하기 때문에, 리스너라는 패키지를 도메인 패키지 하위에 두고, 기존의 Auditable 인터페이스와 다른 리스너들을 이동시켜주자

그런데 계속해서 Book에도, User에도, UserHistory에도 `@CreatedDate`나 `@LastModifiedDate를 반복해서 넣어주고 있는 불편함이 보인다`

이를 차근차근 더 보기 좋게 변경해보자

### 03-1. BaseEntity 클래스 만들기

- 공통적으로 필요한 createdAt, updatedAt, AuditingEntityListener를 정리해서 보관하는 개념!
- `@MappedSuperclass`로 필드를  위의 Book, User,UserHistory 엔티티의 컬럼으로 상속시켜주기
- `@MappedSuperclass`:해당 클래스를 상속받는 자식 클래스에 대해서  해당 클래스의 필드를 자식 클래스의 컬럼으로 포함시켜줌

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.Auditable;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.Column;
import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
**@MappedSuperclass**
@EntityListeners(value= AuditingEntityListener.class)
public class BaseEntity {
    @CreatedDate
    **@Column(name="created_at")**
    private LocalDateTime cratedAt;
    @LastModifiedDate
    **@Column(name="updated_at")**
    private LocalDateTime updatedAt;
}
```

그리고 처음에는 `@MappedSuperClass만 붙였다가`  처음에는 

```java
warning: Generating equals/hashCode implementation but without 
a call to superclass, even though this class does not 
extend java.lang.Object. If this is intentional, add 
'@EqualsAndHashCode(callSuper=false)' to your type.
```

이 경고를 보고서, `그러면 권장받은 대로 @EqualsAndHashCode(callSuper=false)를 해서 domain에 별도로 구현하는 value object가 없을 경우를 나타내도록` 하였는데[처음에는 공통적인 부분을 모두 지우고 BaseEntity로 관리하도록 했기 때문에 이렇게 접근하는 것이 맞는 줄 알았지만.. 아래를 보면 알겠지만 아니었다😢]

[[Spring] Generating equals/hashCode implementation but without a call to superclass, even though this class does not extend java.lang.Object 오류 해결](https://chati.tistory.com/168)

``org.h2.jdbc.JdbcSQLSyntaxErrorException: Column "CREATED_AT" not found` 에러도 발생했었다. 그래서 혹여 name 속성을 넣어줘야 하나 싶어서, 위와 같이 `@Column(name=~)`을 추가해주었다! 이부분을 꼭! 잊지 말자!

그리고 이제는 더이상 Auditable 인터페이스가 필요없기 때문에(스프링에서 제공되는 AuditingEntityListener가 있기 때문), UserEntityListener를 이용하도록 나머지 엔티티들을 조절해주자

아래는 UserEntityListener를 다시 확인해보기 위한 것이다!

```java
package com.example.jpa_entity.domain.listener;

import com.example.jpa_entity.domain.User;
import com.example.jpa_entity.domain.UserHistory;
import com.example.jpa_entity.repository.UserHistoryRepository;
import com.example.jpa_entity.support.BeanUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.persistence.PrePersist;
import javax.persistence.PreUpdate;

public class UserEntityListener {

    @PrePersist
    @PreUpdate
    public void prePersistAndUpdate(Object o){
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
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.Auditable;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
**@EqualsAndHashCode(callSuper=false)**
///@EntityListeners(value=MyEntityListener.class)
//@EntityListeners(value= AuditingEntityListener.class)
public class Book extends BaseEntity/* implements Auditable*/ {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    //책 이름
    private String name;
    //책 저자
    private String author;
    //생성일
//    @CreatedDate
//    private LocalDateTime createdAt;
//    //수정일
//    @LastModifiedDate
//    private LocalDateTime updatedAt;

}
```

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.Auditable;
import com.example.jpa_entity.domain.listener.UserEntityListener;
import lombok.*;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
**@EqualsAndHashCode(callSuper=false)**
@Table(name="user")
//@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class, UserEntityListener.class})
@EntityListeners(value={UserEntityListener.class})
public class User extends BaseEntity /*implements Auditable*/{
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
//    @Column(name="created_at")
//    @CreatedDate
//    private LocalDateTime createdAt;
//    @Column(name="updated_at")
//    @LastModifiedDate
//    private LocalDateTime updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

}
```

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.Auditable;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
**@EqualsAndHashCode(callSuper=false)**
//@EntityListeners(value={MyEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class})
public class UserHistory extends BaseEntity/*implements Auditable*/ {
    @Id
    @GeneratedValue
    private Long id;

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

그런데 아래처럼 테스트해보면, created_at과 updated_at이 ddl이나 dql, dml에서는 잘 확인되지만, 정작 전체결과에서는 보이질 않는다

```java
Hibernate: 
    
    drop table if exists address CASCADE 
Hibernate: 
    
    drop table if exists book CASCADE 
Hibernate: 
    
    drop table if exists user CASCADE 
Hibernate: 
    
    drop table if exists user_history CASCADE 
Hibernate: 
    
    drop sequence if exists hibernate_sequence
Hibernate: create sequence hibernate_sequence start with 1 increment by 1
Hibernate: 
    
    create table address (
       id bigint not null,
        primary key (id)
    )
Hibernate: 
    
    create table book (
       id bigint not null,
        created_at timestamp,
        updated_at timestamp,
        author varchar(255),
        name varchar(255),
        primary key (id)
    )
Hibernate: 
    
    create table user (
       id bigint not null,
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
       id bigint not null,
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
    call next value for hibernate_sequence
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user_history
        (created_at, updated_at, active, email, gender, name, user_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user
        (created_at, updated_at, active, email, gender, name, id) 
    values
        (?, ?, ?, ?, ?, ?, ?)
---insert완료
Hibernate: 
    select
        user0_.id as id1_2_0_,
        user0_.created_at as created_2_2_0_,
        user0_.updated_at as updated_3_2_0_,
        user0_.active as active4_2_0_,
        user0_.email as email5_2_0_,
        user0_.gender as gender6_2_0_,
        user0_.name as name7_2_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user_history
        (created_at, updated_at, active, email, gender, name, user_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
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
---update완료
Hibernate: 
    select
        userhistor0_.id as id1_3_,
        userhistor0_.created_at as created_2_3_,
        userhistor0_.updated_at as updated_3_3_,
        userhistor0_.active as active4_3_,
        userhistor0_.email as email5_3_,
        userhistor0_.gender as gender6_3_,
        userhistor0_.name as name7_3_,
        userhistor0_.user_id as user_id8_3_ 
    from
        user_history userhistor0_
UserHistory(id=6, userId=null, name=abcd, email=abcd@fastcampus.com, active=false, gender=MALE)
UserHistory(id=8, userId=7, name=abcde, email=abcd@fastcampus.com, active=false, gender=MALE)
```

그래서 조금 더 나아가서 생각해볼 때, created_at과 updated_at을 상속받은 부분이 잘 처리되지 않았다는 점에서

```java
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
```

를 각 엔티티에 적용해주었다

처음에는 자손 클래스 내부에 직접적으로 value object가 보이지 않아서 false로 해주어야 하나 생각했는데 , 생각해보니 equals와 ToString, HashCode는 삼총사마냥 동등성과 동일성 비교시에 따라다니니 이 점을 고려해서 상속받은 정보를 처리해주어야 했던 것 같다😭

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.Auditable;
import com.example.jpa_entity.domain.listener.UserEntityListener;
import lombok.*;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
**@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)**
@Table(name="user")
//@EntityListeners(value={MyEntityListener.class,UserEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class, UserEntityListener.class})
@EntityListeners(value={UserEntityListener.class})
public class User extends BaseEntity /*implements Auditable*/{
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
//    @Column(name="created_at")
//    @CreatedDate
//    private LocalDateTime createdAt;
//    @Column(name="updated_at")
//    @LastModifiedDate
//    private LocalDateTime updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    @Enumerated(EnumType.STRING)
    private Gender gender;

}
```

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
**@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)**
///@EntityListeners(value=MyEntityListener.class)
//@EntityListeners(value= AuditingEntityListener.class)
public class Book extends BaseEntity/* implements Auditable*/ {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    //책 이름
    private String name;
    //책 저자
    private String author;
    //생성일
//    @CreatedDate
//    private LocalDateTime createdAt;
//    //수정일
//    @LastModifiedDate
//    private LocalDateTime updatedAt;

}
```

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
**@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)**
//@EntityListeners(value={MyEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class})
public class UserHistory extends BaseEntity/*implements Auditable*/ {
    @Id
    @GeneratedValue
    private Long id;

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
    
    drop table if exists address CASCADE 
Hibernate: 
    
    drop table if exists book CASCADE 
Hibernate: 
    
    drop table if exists user CASCADE 
Hibernate: 
    
    drop table if exists user_history CASCADE 
Hibernate: 
    
    drop sequence if exists hibernate_sequence
Hibernate: create sequence hibernate_sequence start with 1 increment by 1
Hibernate: 
    
    create table address (
       id bigint not null,
        primary key (id)
    )
Hibernate: 
    
    create table book (
       id bigint not null,
        created_at timestamp,
        updated_at timestamp,
        author varchar(255),
        name varchar(255),
        primary key (id)
    )
Hibernate: 
    
    create table user (
       id bigint not null,
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
       id bigint not null,
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
    call next value for hibernate_sequence
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user_history
        (created_at, updated_at, active, email, gender, name, user_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user
        (created_at, updated_at, active, email, gender, name, id) 
    values
        (?, ?, ?, ?, ?, ?, ?)
---insert완료
Hibernate: 
    select
        user0_.id as id1_2_0_,
        user0_.created_at as created_2_2_0_,
        user0_.updated_at as updated_3_2_0_,
        user0_.active as active4_2_0_,
        user0_.email as email5_2_0_,
        user0_.gender as gender6_2_0_,
        user0_.name as name7_2_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user_history
        (created_at, updated_at, active, email, gender, name, user_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
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
---update완료
Hibernate: 
    select
        userhistor0_.id as id1_3_,
        userhistor0_.created_at as created_2_3_,
        userhistor0_.updated_at as updated_3_3_,
        userhistor0_.active as active4_3_,
        userhistor0_.email as email5_3_,
        userhistor0_.gender as gender6_3_,
        userhistor0_.name as name7_3_,
        userhistor0_.user_id as user_id8_3_ 
    from
        user_history userhistor0_
UserHistory(super=BaseEntity(cratedAt=2021-08-26T12:31:13.541, updatedAt=2021-08-26T12:31:13.541), id=6, userId=null, name=abcd, email=abcd@fastcampus.com, active=false, gender=MALE)
UserHistory(super=BaseEntity(cratedAt=2021-08-26T12:31:13.641, updatedAt=2021-08-26T12:31:13.641), id=8, userId=7, name=abcde, email=abcd@fastcampus.com, active=false, gender=MALE)
```

그러면 이번에는 위와 같이 created_at과 updated_at이 표시되는 것을 확인해볼 수 있다