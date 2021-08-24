# Entity의 Listener 활용 1편!

## 00. Listener 란 무엇일까?

- 엔티티에 대한 `이벤트를 관찰하고 있다가 이벤트 발생 시 특정 동작을 진행`하는 것!

기존의 실습 프로젝트를 그대로 확장해서 사용해보자

[Entity 기본 속성(Annotation)](https://hy6219.github.io/TIL/Spring/JPA/Entity/[Spring%20JPA]%EC%97%94%ED%8B%B0%ED%8B%B0%EC%9D%98%20%EA%B8%B0%EB%B3%B8%20%ED%8A%B9%EC%A7%95.html)

## 01. JPA에서 제공하고 있는 이벤트의 종류에는 무엇이 있을까?

▶️ `@PrePersist` : `insert 메서드가 호출되기 전에 실행되는 메서드`

▶️ `@PreUpdate` : `merge 메서드 호출 전` 에 실행되는 메서드

▶️ `@PreRemove` : `delete 메서드 호출 전`에 실행되는 메서드

▶️ `@PostPersist` : `insert 메서드가 호출된 후 이후에 실행되는 메서드`

▶️ `@PostUpdate` : `merge메서드 호출 후`에 실행되는 메서드

▶️ `@PostRemove` : `delete 메서드 호출 후`에 실행되는 메서드

▶️ `@PostLoad` : `select 조회 호출 직후` 실행되는 메서드

## 02. 엔티티의 `insert 메서드 호출`을 감지하는 리스너- `@PrePersist과 @PostPersist`

먼저 아래와 같이 `@Prepersist`  및 `@PostPersist`어노테이션을 메서드 위에 달아주자 

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

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
    @Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;

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

    **@PrePersist
    public void prePersist(){
        String method=Thread.currentThread().getStackTrace()[0].getMethodName();
        System.out.println("PrePersist in method: "+method);
    }
    @PostPersist
    public void postPersist(){
        String method=Thread.currentThread().getStackTrace()[0].getMethodName();
        System.out.println("PostPersist in method: "+method);
    }**

}
```

그리고 다른 기존의 테스트는 확인하기에 중간에 양이 많아서 확인이 어려울 수 있어서 아래와 같이 insert를 실행하는 짧은 테스트를 구성해보자

```java
@Test
    public void insertListener(){
        User user=new User("kate","myKate@slowcampus.com");
        userRepository.save(user);
    }
```

테스트를 진행해보면, 아래처럼, 데이터 삽입 전후에 prePersist와 postPersist 문장이 확인된다

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/Pre_PostPersist.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/Pre_PostPersist.PNG?raw=true)

## 03. 엔티티의 `update 메서드 호출`을 감지하는 리스너- `@PreUpdate와 @PostUpdate`

먼저 User에 PreUpdate와 PostUpdate 어노테이션을 활용한 메서드를 만들어서 test로 간단하게 언제 이벤트를 감지하는지 확인해보자

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

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
    @Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;

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

    @PrePersist
    public void prePersist(){
        System.out.println("PrePersist");
    }
    @PostPersist
    public void postPersist(){
        System.out.println("PostPersist");
    }
    @PreUpdate
    public void preUpdate(){
        System.out.println("PreUpdate");
    }
    @PostUpdate
    public void postUpdate(){
        System.out.println("PostUpdate");
    }
}
```

```java
@Test
    public void updateEvent(){
        User user=userRepository.findById(3L).orElseThrow(RuntimeException::new);
        user.setActive(true);
        user.setGender(FEMALE);
        userRepository.save(user);
    }
```

```java
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
PostLoad
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
PostLoad
PreUpdate
Hibernate: 
    update
        user 
    set
        active=?,
        email=?,
        gender=?,
        name=?,
        updated_at=? 
    where
        id=?
PostUpdate
```

위의 테스트 결과를 통해서, PreUpdate와 PostUpdate는 `update문 실행 전과 후에 적용됨을 확인해볼 수 있다`

## 04. 엔티티의 `delete 메서드 호출`을 감지하는 리스너- `@PreRemove와 @PostRemove`

이번에는 PreRemove와 PostRemove어노테이션을 붙인 메서드를 통해서 어느 시점에 감지하는지 간단한 출력문을 출력하도록 작성을 추가해보자

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

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
    @Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;

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

    @PrePersist
    public void prePersist(){
        System.out.println("PrePersist");
    }
    @PostPersist
    public void postPersist(){
        System.out.println("PostPersist");
    }
    @PreUpdate
    public void preUpdate(){
        System.out.println("PreUpdate");
    }
    @PostUpdate
    public void postUpdate(){
        System.out.println("PostUpdate");
    }
    @PreRemove
    public void preRemove(){
        System.out.println("PreRemove");
    }
    @PostRemove
    public void postRemove(){
        System.out.println("PostRemove");
    }

}
```

```java
@Test
    public void deleteEvent(){
        userRepository.deleteAll();
        System.out.println("---");
        userRepository.deleteAllInBatch();
    }
```

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/deleteAll-pre_postRemove.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/deleteAll-pre_postRemove.PNG?raw=true)

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/deleteAllInBatch-pre_postRemove.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/deleteAllInBatch-pre_postRemove.PNG?raw=true)

delete의 경우에는 특히 전체삭제에서 배치삭제 경우가 존재하기 때문에 다르게 감지될 수 있지 않을까? 라는 생각에 deleteAll과 deleteAllInBatch를 모두 실행해보았다

그 결과, `deletetAll일 경우, 삭제 전후로 PreRemove와 PostRemove`가 붙는 것을 확인해볼 수 있었지만

`deleteAllInBatch에서는 삭제 전후로 delete관련 리스너가 붙지 않는 것을 확인해볼 수 있었다`

## 05. 엔티티의 `select 메서드 호출`을 감지하는 리스너- `@PostLoad`

이번에는 select에 대한 리스너인 PostLoad 어노테이션을 이용해서 어느 시점에 작동하는지 확인해보자

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

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
    @Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;

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

    @PrePersist
    public void prePersist(){
        System.out.println("PrePersist");
    }
    @PostPersist
    public void postPersist(){
        System.out.println("PostPersist");
    }
    @PreUpdate
    public void preUpdate(){
        System.out.println("PreUpdate");
    }
    @PostUpdate
    public void postUpdate(){
        System.out.println("PostUpdate");
    }
    @PreRemove
    public void preRemove(){
        System.out.println("PreRemove");
    }
    @PostRemove
    public void postRemove(){
        System.out.println("PostRemove");
    }
    @PostLoad
    public void postLoad(){
        System.out.println("PostLoad");
    }

}
```

```java
@Test
    public void selectEvent(){
        User user=userRepository.findById(2L).orElseThrow(RuntimeException::new);
        System.out.println(user);
    }
```

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/PostLost-select%20%EC%A1%B0%ED%9A%8C.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/Listener/PostLost-select%20%EC%A1%B0%ED%9A%8C.PNG?raw=true)

위의 그림에서 알 수 있듯이, PostLoad는 `select 조회 후에 붙는 어노테이션임을 확인`해볼 수 있었다

일반적으로 PrePersist, PreUpdate가 가장 많이 사용된다

그리고 대부분 DB의 레코드에서는 생성일과 수정일을 함께 넣어서 생성하도록 되어 있다

## 06. 실제 리스너 사용시 생각해볼 부분 with DRY법칙

✴️ DRY 법칙 ❓

- Don't Repeat Yourself
- 개발 과정과 유지보수 비용 절감에 효과적

✴️ KISS 법칙 ❓

- Keep It Simple, Stupid
- 단순하게 작성하자

✴️ YANGNI 법칙 ❓

- You Ain't Gonna Need It
- 미리 함수나 코드를 작성하지 말고 지금 필요한 기능만 추가

[소프트웨어 개발의 3개의 KEY 원칙 : KISS,YAGNI,DRY](https://hongjinhyeon.tistory.com/136)

ref : [https://hongjinhyeon.tistory.com/136](https://hongjinhyeon.tistory.com/136)

---

예를 들어서, 새로운 사용자를 추가하고, 이메일로 찾도록 해보자

```java
@Test
    public void prePersistTest(){
        User user= new User();
        user.setEmail("abc@fastcampus.com");
        user.setName("abc");
        user.setCreatedAt(LocalDateTime.now());
        user.setUpdatedAt(LocalDateTime.now());

        userRepository.save(user);//insert

        System.out.println(userRepository.findByEmail(user.getEmail()));
    }
```

```java
PrePersist
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user
        (active, created_at, email, gender, name, id) 
    values
        (?, ?, ?, ?, ?, ?)
PostPersist
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.gender as gender5_1_,
        user0_.name as name6_1_,
        user0_.updated_at as updated_7_1_ 
    from
        user user0_ 
    where
        user0_.email=?
PostLoad
User(id=6, name=abc, email=abc@fastcampus.com, createdAt=2021-08-23T15:07:44.905, updatedAt=null, active=false, testData=null, gender=null)
```

그러면 insert 전 후에, 정확히 말하자면, prePersist는 시퀀스를 부르기 전에 부터 시작되고, postPersist는 insert 후에 감지되는 것을 확인해볼 수 있다

그리고, 이메일로 조회한 후에 postLoad가 감지됨을 확인해볼 수 있다

하지만, setCreatedAt이나 setUpdatedAt과 같은 setter들을 계속해서 반복해주는 것은 DRY 법칙에 어긋나고, 실수로 지금 언급된 setter 중 하나이상을 넣지 않음으로써 발생가능한 데이터 정확성의 문제가 발생가능하다

이를 위해서 엔티티 자체에 prePersist를 사용해서 값을 set하는 방식을 사용해줌이 바람직하다

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

    **@PrePersist
    public void prePersist(){
       this.createdAt=LocalDateTime.now();
       this.updatedAt=LocalDateTime.now();
    }**
    @PostPersist
    public void postPersist(){
        System.out.println("PostPersist");
    }

}
```

```java
@Test
    public void prePersistTest(){
        User user= new User();
        user.setEmail("abc@fastcampus.com");
        user.setName("abc");
     
//        user.setCreatedAt(LocalDateTime.now());
//        user.setUpdatedAt(LocalDateTime.now());

        userRepository.save(user);//insert

        System.out.println(userRepository.findByEmail(user.getEmail()));
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
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.gender as gender5_1_,
        user0_.name as name6_1_,
        user0_.updated_at as updated_7_1_ 
    from
        user user0_ 
    where
        user0_.email=?
User(id=6, name=abc, email=abc@fastcampus.com, createdAt=2021-08-24T14:12:54.864, updatedAt=2021-08-24T14:12:54.864, active=false, testData=null, gender=null)
```

그러면 이제는 굳이 setter로 유사 작업을 반복하지 않고도, 생성일자와 수정일자에 대해서 반영할 수 있음을 확인해볼 수 있다

 

비슷한 맥락에서 preUpdate에 대해서도, 반복되는 것을 줄일 수 있다

update는 createdAt과는 맥락이 맞지 않으므로, updatedAt만 수정해주는 것으로 하자

```java
@PreUpdate
    public void preUpdate(){
        this.updatedAt=LocalDateTime.now();
    }
```

```java
@Test
    public void updateEvent(){
        User user=userRepository.findById(3L).orElseThrow(RuntimeException::new);
        user.setGender(FEMALE);
        userRepository.save(user);
        userRepository.findAll().forEach(System.out::println);
    }
```

```java
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
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
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.gender as gender5_1_,
        user0_.name as name6_1_,
        user0_.updated_at as updated_7_1_ 
    from
        user user0_
User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-24T14:16:05.417, updatedAt=2021-08-24T14:16:05.417, active=true, testData=null, gender=null)
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-24T14:16:05.437, updatedAt=2021-08-24T14:16:05.437, active=true, testData=null, gender=null)
**User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-24T14:16:05.438, updatedAt=2021-08-24T14:16:05.888, active=false, testData=null, gender=FEMALE)**
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-24T14:16:05.438, updatedAt=2021-08-24T14:16:05.438, active=false, testData=null, gender=null)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-24T14:16:05.439, updatedAt=2021-08-24T14:16:05.439, active=true, testData=null, gender=null)
```

그러면 알아서 update 전 시점에 이벤트를 감지해서 미리 updatedAt시점을 수정해서 반영하는 것을 확인해볼 수 있다

[위에서 user 인스턴스를 출력에 사용하지 않은 이유는 이전에 조회된 값, 즉 변경 전의 값이 담겨져있기 때문이다]

## 07. 복습해보기 with Book 엔티티

Book이라는 엔티티를 만들어보자

그런데 앞에서 다룬 것처럼 DRY법칙에 의해서 생성일과 수정일에 대해서 리스너가 감지하여 반복을 줄이도록 하자

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import java.time.LocalDate;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
public class Book {
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

    @PrePersist
    public void preProcessing(){
        this.createdAt=LocalDateTime.now();
        this.updatedAt=LocalDateTime.now();
    }

    @PreUpdate
    public void preUpdate(){
        this.updatedAt=LocalDateTime.now();
    }
}
```

엔티티를 만들었다면 다음은 월 만들어야죠? → `레포지토리`!! dao를 만들어야죠!

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Book;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookRepository extends JpaRepository<Book,Long> {

}
```

그러면 이제 BookRepository에 대한 테스트를 만들어주자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Book;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class BookRepositoryTest {
    @Autowired
    private BookRepository bookRepository;

    @Test
    public void bookTest(){
        Book book=new Book();
        book.setName("jpa 초격차 패키지");
        book.setAuthor("패스트 캠퍼스");

        bookRepository.save(book);

        bookRepository.findAll().forEach(System.out::println);
    }
}
```

위의 bookTest메서드를 통해 insert후 Book 엔티티에 존재하는 레코드들을 조회해보면 아래와 같이 prePersist가 시작되어 사전에 DEFAULT값처럼 당시 시간이 저장된 것을 확인해볼 수 있다

```java
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        book
        (author, created_at, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?)
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.author as author2_1_,
        book0_.created_at as created_3_1_,
        book0_.name as name4_1_,
        book0_.updated_at as updated_5_1_ 
    from
        book book0_
Book(id=6, name=jpa 초격차 패키지, author=패스트 캠퍼스, createdAt=2021-08-24T14:31:56.505, updatedAt=2021-08-24T14:31:56.505)
```

▶️ 하지만 이러한 메서드는 각 엔티티마다 반복해서 만들어줘야 할 것이다!

이럴때 엔티티 리스너를 이용해서 지정해주는 방법이 있다!

## 08. 엔티티 리스너 만들기

우선, 도메인 패키지에 "MyEntityListener"클래스를 만들고

```java
package com.example.jpa_entity.domain;

public class MyEntityListener {
}
```

Book과 User 엔티티에서 `@PrePersist`나 `@PreUpdate` 가 붙여진 메서드를 지우고

윗부분에 `@EntityListeners(value=MyEntityListener.class)` 를 붙여주자

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import java.time.LocalDate;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
**@EntityListeners(value=MyEntityListener.class)**
public class Book {
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
**@EntityListeners(value=MyEntityListener.class)**
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

그리고 createdAt과 updatedAt에 대한 존재를 MyEntityListener에서도 알고 있어야 하므로, jpa와는 별개로 인터페이스를 두는 것이 좋다(setter, getter 만들기)

```java
package com.example.jpa_entity.domain;

import java.time.LocalDateTime;

public interface Auditable {
    **LocalDateTime getCreatedAt();
    LocalDateTime getUpdatedAt();

    public void setCreatedAt(LocalDateTime createdAt);
    public void setUpdatedAt(LocalDateTime updatedAt);**
    

}
```

그러면 Book과 User가 이 인터페이스를 구현하는 것으로 해줘도 문제가 없을 것이다!

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import java.time.LocalDate;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
@EntityListeners(value=MyEntityListener.class)
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
@EntityListeners(value=MyEntityListener.class)
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

🌟 단, 중요한 점은, 엔티티 내에서는 this로 어떤 엔티티인지 구분이 갔기 때문에 매개변수가 필요없었다! 다만, 이렇게 엔티티 외에서 리스너로 감지하고자 할 경우에는 엔티티 객체를 감지해야 하는데, 어떤 타입인지 분간할 수 없으므로 파라미터의 타입이 Object로 강제되어, 파라미터 사용에 대해서 강제성이 붙는다

그 점을 고려하면 아래와 같이 insert전과 update 전에 이벤트를 감지하여 날짜를 저장 및 변경할 수 있도록 할 수 있다

```java
package com.example.jpa_entity.domain;

import javax.persistence.PrePersist;
import javax.persistence.PreUpdate;
import java.time.LocalDateTime;

public class MyEntityListener {
    //해당 엔티티를 받아서 리스너에서 처리해야 해서,
    //전과 다르게 매개변수가 있어야!
    //단지, 그 인자값 타입이 무엇인지 알 수 없어서
    //Object 타입으로 강제
    @PrePersist
    public void prePersist(Object obj){
        if(obj instanceof Auditable){
            Auditable a=(Auditable) obj;
            a.setCreatedAt(LocalDateTime.now());
            a.setUpdatedAt(LocalDateTime.now());
        }
    }

    @PreUpdate
    public void preUpdate(Object obj){
        if(obj instanceof Auditable){
            Auditable a=(Auditable) obj;
            a.setUpdatedAt(LocalDateTime.now());
        }
    }

}
```

그리고 간단하게 insert는 BeforeEach로 테스트 전에 케이스로 넣어주도록 하고,

update는 그 데이터를 기준으로 실행되도록 테스트해보자

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
        book.setAuthor("테스트");
        bookRepository.save(book);
        System.out.println("insert 끝");
    }

    @Test
    public void bookTest(){
        Book book=new Book();
        book.setName("jpa 초격차 패키지");
        book.setAuthor("패스트 캠퍼스");

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

```java
insert
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        book
        (author, created_at, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?)
insert 끝
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.author as author2_1_,
        book0_.created_at as created_3_1_,
        book0_.name as name4_1_,
        book0_.updated_at as updated_5_1_ 
    from
        book book0_
**Book(id=6, name=테스트, author=테스트, createdAt=2021-08-24T15:12:05.959, updatedAt=2021-08-24T15:12:05.959)**
update
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.author as author2_1_0_,
        book0_.created_at as created_3_1_0_,
        book0_.name as name4_1_0_,
        book0_.updated_at as updated_5_1_0_ 
    from
        book book0_ 
    where
        book0_.id=?
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.author as author2_1_0_,
        book0_.created_at as created_3_1_0_,
        book0_.name as name4_1_0_,
        book0_.updated_at as updated_5_1_0_ 
    from
        book book0_ 
    where
        book0_.id=?
Hibernate: 
    update
        book 
    set
        author=?,
        created_at=?,
        name=?,
        updated_at=? 
    where
        id=?
update 끝
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.author as author2_1_,
        book0_.created_at as created_3_1_,
        book0_.name as name4_1_,
        book0_.updated_at as updated_5_1_ 
    from
        book book0_
**Book(id=6, name=수정, author=테스트, createdAt=2021-08-24T15:12:05.959, updatedAt=2021-08-24T15:12:06.245)**
```

그러면 이전에 우리가 엔티티에서 `@PrePersist`와 `@PreUpdate`를 감지해서 메서드로 일괄 값 수정을 해준것과 동일하게 적용되는 것을 확인해볼 수 있다

뿐만아니라, User 엔티티에 대해서도 이 리스너를 활용할 수 있기 때문에 반복적인 코딩을 줄일 수 있게 된다!