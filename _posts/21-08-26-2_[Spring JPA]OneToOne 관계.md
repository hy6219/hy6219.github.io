# 1:1 연관관계 살펴보기

[앞으로 구현할 연관관계에 참고할 ERD](저번 시간에 그린 것!)

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/RelationShip/User.png?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/RelationShip/User.png?raw=true)

결제나 주문 테이블과 같이 트래픽이 발생하는 경우는 1:1 테이블을 계속해서 추가하는 경우가 존재

지금 우리가 만든 ERD에는 1:1 관계를 갖는 테이블이 존재하지 않아서, 하나만 더 만들어 보자!

Book테이블과 Review 테이블이 합쳐진 "BookReviewInfo" 테이블을 만들어보자 

이 테이블은 DB 연산을 줄이기 위해서 주기적으로 review에 대한 정보를 저장해두고 Book 테이블과 1:1조인을 해서 사용할 수 있도록 하자

- average_review_score: 평균 점수
- review_count: 리뷰가 그 책에 대해서 몇 개가 달려있는지

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/RelationShip/OneToOne.png?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/RelationShip/OneToOne.png?raw=true)

그런데 저번에 만들었던 Auditable을 살리기 위해서 진행중에

`cannot resolve column name` 이라던지 `cannot resolve table 'User'` 가 떴다..😥 그래서 찾아보니 너무 엄격하게 인텔리제이가 검사해서 그런것이라서, inspection에서 JPA-Unresolved database references annotations를 체크해제해주면 된다고 한다

[https://log-laboratory.tistory.com/285](https://log-laboratory.tistory.com/285)

그 후에 `Class 'BaseEntity' must either be declared abstract or implement abstract method 'getCreatedAt()' in 'Auditable`

와 같은 에러가 났는데, 아마도 createdAt에 대한 getter에서 충돌이 있었나보다

그래서 해당 필드만 getter, setter를 오버라이딩해주었다

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
@MappedSuperclass
@NoArgsConstructor
@EntityListeners(value=AuditingEntityListener.class)
public class BaseEntity implements Auditable{
    @CreatedDate
    @Column(name="created_at")
    private LocalDateTime cratedAt;

    @LastModifiedDate
    @Column(name="updated_at")
    private LocalDateTime updatedAt;
    
    @Override
    public LocalDateTime getCreatedAt() {
        return this.cratedAt;
    }

    @Override
    public void setCreatedAt(LocalDateTime createdAt) {
        this.cratedAt=createdAt;
    }
}
```

아무튼 이렇게 되면, 각 엔티티에 만약, Auditable 인터페이스를 이용하고 싶은 경우에 반복해서 구현하지 않아도 된다!

이번에는 BookReviewInfo 도메인을 만들어주자

averageReviewScore나 reviewCount는 기본값을 0으로 생각하게 하여 null이어도 기본적으로 0으로 인식될 수 있도록 Wrapper 클래스가 아닌 Primitive 타입을 이용하도록 하자

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.BaseEntity;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@NoArgsConstructor
@Data
public class BookReviewInfo extends BaseEntity {

    @Id
    @GeneratedValue
    private Long id;//boorReviewInfo 의 PK

    private Long bookId;

    //Wrapper class는 null을 허용할지 안할지가 중요한데
    //averageReviewScore나 reviewCount는 null도 포함시켜서
    //생각해볼 수 있기 때문에 small float, small int를 이용(null이 0이므로)

    private float averageReviewScore;

    private int reviewCount;

}
```

그리고 이에 대한 레포지토리도 만들어주자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.listener.BookReviewInfo;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookReviewInfoRepository extends JpaRepository<BookReviewInfo,Long> {

}
```

일단, 잘 돌아가는지 확인하기 위해서 이에 대한 통합테스트를 진행해보자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.listener.BookReviewInfo;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class BookReviewInfoRepositoryTest {

    @Autowired
    private BookReviewInfoRepository bookReviewInfoRepository;

    @Test
    public void test(){
        BookReviewInfo info=new BookReviewInfo();

        info.setBookId(1L);
        info.setAverageReviewScore(3f);
        info.setReviewCount(5);

        bookReviewInfoRepository.save(info);
        bookReviewInfoRepository.findAll().forEach(System.out::println);
    }

}
```

다른 것은 천천히 살펴보도록 하고 DDL 부분 먼저 확인해보도록 하자

```java
create table book_review_info (
       id bigint not null,
        created_at timestamp,
        updated_at timestamp,
        **average_review_score float not null,**
        book_id bigint,
        **review_count integer not null,**
        primary key (id)
    )
```

그러면, primitive를 이용했던 부분에 대해서 not null 제약조건이 붙어 있는 것을 확인해볼 수 있다

그런데 아래 내용을 보면, created_at과 updated_at이 보이지 않는다!

vo객체를 물려받기 위한 ToString, EqualsAndHashCode 어노테이션 속성값을 설정해보도록 하자

```java
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        book_review_info
        (created_at, updated_at, average_review_score, book_id, review_count, id) 
    values
        (?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        bookreview0_.id as id1_2_,
        bookreview0_.created_at as created_2_2_,
        bookreview0_.updated_at as updated_3_2_,
        bookreview0_.average_review_score as average_4_2_,
        bookreview0_.book_id as book_id5_2_,
        bookreview0_.review_count as review_c6_2_ 
    from
        book_review_info bookreview0_
BookReviewInfo(id=6, bookId=1, averageReviewScore=3.0, reviewCount=5)
```

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.BaseEntity;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@NoArgsConstructor
**@ToString(callSuper = true)
@EqualsAndHashCode(callSuper = true)**
@Data
public class BookReviewInfo extends BaseEntity {

    @Id
    @GeneratedValue
    private Long id;//boorReviewInfo 의 PK

    private Long bookId;

    //Wrapper class는 null을 허용할지 안할지가 중요한데
    //averageReviewScore나 reviewCount는 null도 포함시켜서
    //생각해볼 수 있기 때문에 small float, small int를 이용(null이 0이므로)

    private float averageReviewScore;

    private int reviewCount;

}
```

```java
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        book_review_info
        (created_at, updated_at, average_review_score, book_id, review_count, id) 
    values
        (?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        bookreview0_.id as id1_2_,
        bookreview0_.created_at as created_2_2_,
        bookreview0_.updated_at as updated_3_2_,
        bookreview0_.average_review_score as average_4_2_,
        bookreview0_.book_id as book_id5_2_,
        bookreview0_.review_count as review_c6_2_ 
    from
        book_review_info bookreview0_
BookReviewInfo(super=BaseEntity(cratedAt=2021-08-26T16:08:11.642, updatedAt=2021-08-26T16:08:11.642), id=6, bookId=1, averageReviewScore=3.0, reviewCount=5)
```

그러면 이번에는 created_at과 updated_at이 잘 보이는 것을 확인해볼 수 있다

[이전에는 두 어노테이션의 해당 속성의 기본값이 false였기 때문에 안보인 것이다]

이번에는 지금 새롭게 설계함으로써 변경된 내용을 차근차근 반영해보자

먼저 Book을 수정해보자

🌟 아직 클린코드를 공부하지는 않았지만, 주석으로 된 코드는 "나쁜 코드 anti-code"라고 한다

-요즘에는 VCS 중 하나인 깃을 예로 들면, 이를 통해 관리하고 있기 때문에 코드를 굳이 주석처리하지 않아도 해당 히스토리가 존재하므로,  주석처리보다는 지우는 것이 좋다

-하지만 학습시에는 무방하다!

---

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
///@EntityListeners(value=MyEntityListener.class)
//@EntityListeners(value= AuditingEntityListener.class)
public class Book extends BaseEntity/* implements Auditable*/ {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    //책 이름
    private String name;

    private String category;

    //책 저자
    private Long authorId;

    private Long publisherId;

}
```

먼저 ERD에서 설계된 대로, Book 엔티티를 수정해보면, 위와 같이 반영할 수 있는데 이대로 이를 이용해서 진행된 테스트코드도 수정해보도록 하겠다

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
        **book.setAuthorId(1L);
        book.setPublisherId(1L);**
        bookRepository.save(book);
        System.out.println("insert 끝");
    }

    @Test
    public void bookTest(){
        Book book=new Book();
        book.setName("jpa 초격차 패키지");
        **book.setAuthorId(2L);
        book.setPublisherId(2L);**

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

publisherId는 새로이 만든 속성이기도 하고, 외래키에 NULL이 있는 것은 상상도 해볼 수 없으므로 (지금은 별다른 추가를 하지는 않았지만) 추가해보았다

```java
Hibernate: 
    
    drop table if exists address CASCADE 
Hibernate: 
    
    drop table if exists book CASCADE 
Hibernate: 
    
    drop table if exists book_review_info CASCADE 
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
        **created_at timestamp,
        updated_at timestamp,**
        **author_id bigint,
        category varchar(255),**
        name varchar(255),
        **publisher_id bigint,**
        primary key (id)
    )
Hibernate: 
    
    create table book_review_info (
       id bigint not null,
        created_at timestamp,
        updated_at timestamp,
        average_review_score float not null,
        book_id bigint,
        review_count integer not null,
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
insert
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        book
        (created_at, updated_at, author_id, category, name, publisher_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?)
insert 끝
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        book
        (created_at, updated_at, author_id, category, name, publisher_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.created_at as created_2_1_,
        book0_.updated_at as updated_3_1_,
        book0_.author_id as author_i4_1_,
        book0_.category as category5_1_,
        book0_.name as name6_1_,
        book0_.publisher_id as publishe7_1_ 
    from
        book book0_
**Book(super=BaseEntity(cratedAt=2021-08-26T16:16:46.499, updatedAt=2021-08-26T16:16:46.499), id=6, name=테스트, category=null, authorId=1, publisherId=1)
Book(super=BaseEntity(cratedAt=2021-08-26T16:16:46.577, updatedAt=2021-08-26T16:16:46.577), id=7, name=jpa 초격차 패키지, category=null, authorId=2, publisherId=2)**
```

그러면 위와 같이, 물려받은 속성에는 created_at과 updated_at이 표시되고, 설정한 값들이 잘 보여지는 것을 확인해볼 수 있다

이번에는 Book과 BookReviewInfo의 관계를 맺어주도록 해보자

## 🌟 Test에서 Book과 BookReviewInfo 사이의 연관관계 연습해보기

먼저 위에서 말한 것처럼 Book과 BookReviewInfo의 관계를 맺어주기 위해서 BookRepository 객체를 주입해주자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.BookReviewInfo;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class BookReviewInfoRepositoryTest {

    @Autowired
    private BookReviewInfoRepository bookReviewInfoRepository;

    @Autowired
    private BookRepository bookRepository;

    @Test
    public void test(){
        BookReviewInfo info=new BookReviewInfo();

        info.setBookId(1L);
        info.setAverageReviewScore(3f);
        info.setReviewCount(5);

        bookReviewInfoRepository.save(info);
        bookReviewInfoRepository.findAll().forEach(System.out::println);
    }

}
```

그리고 진행할 내용은 아래와 같다

- Book 객체를 만들어서 name과 author id, publisher id 값을 setter로 채워넣어주자
- 그 후 위에서 만든 Book 객체를 저장해주자
- BookReviewInfo 객체를 만들어서 setter를 이용해서 bookId와 averageReviewScore,reviewCount를 채워넣어주자
- Book과 BookReviewInfo는 BaseEntity 내부에 있는 AuditingEntityListener로 created_at과 updated_at이 만들어지고 관리되므로 따로 만들 필요는 없다
- BookReviewInfo 객체도 저장해주자
- Book과 BookReviewInfo 엔티티로 저장된 모든 레코드들을 출력

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Book;
import com.example.jpa_entity.domain.BookReviewInfo;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class BookReviewInfoRepositoryTest {

    @Autowired
    private BookReviewInfoRepository bookReviewInfoRepository;

    @Autowired
    private BookRepository bookRepository;

    @Test
    public void test(){
        BookReviewInfo info=new BookReviewInfo();

        info.setBookId(1L);
        info.setAverageReviewScore(3f);
        info.setReviewCount(5);

        bookReviewInfoRepository.save(info);
        bookReviewInfoRepository.findAll().forEach(System.out::println);
    }

    **@Test
    public void relationTest(){
        Book book= new Book();
        book.setName("Jpa 초격차 패키지");
        book.setAuthorId(1L);
        book.setPublisherId(1L);

        bookRepository.save(book);

        BookReviewInfo info=new BookReviewInfo();
        info.setBookId(1L);
        info.setAverageReviewScore(4.5f);
        info.setReviewCount(2);

        bookReviewInfoRepository.save(info);

        bookRepository.findAll().forEach(System.out::println);
        bookReviewInfoRepository.findAll().forEach(System.out::println);
    }**
}
```

```java
Hibernate: 
    
    drop table if exists address CASCADE 
Hibernate: 
    
    drop table if exists book CASCADE 
Hibernate: 
    
    drop table if exists book_review_info CASCADE 
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
        author_id bigint,
        category varchar(255),
        name varchar(255),
        publisher_id bigint,
        primary key (id)
    )
Hibernate: 
    
    create table book_review_info (
       id bigint not null,
        created_at timestamp,
        updated_at timestamp,
        average_review_score float not null,
        book_id bigint,
        review_count integer not null,
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
    insert 
    into
        book
        (created_at, updated_at, author_id, category, name, publisher_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        book_review_info
        (created_at, updated_at, average_review_score, book_id, review_count, id) 
    values
        (?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.created_at as created_2_1_,
        book0_.updated_at as updated_3_1_,
        book0_.author_id as author_i4_1_,
        book0_.category as category5_1_,
        book0_.name as name6_1_,
        book0_.publisher_id as publishe7_1_ 
    from
        book book0_
**Book(super=BaseEntity(cratedAt=2021-08-26T16:47:10.671, updatedAt=2021-08-26T16:47:10.671), id=6, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)**
Hibernate: 
    select
        bookreview0_.id as id1_2_,
        bookreview0_.created_at as created_2_2_,
        bookreview0_.updated_at as updated_3_2_,
        bookreview0_.average_review_score as average_4_2_,
        bookreview0_.book_id as book_id5_2_,
        bookreview0_.review_count as review_c6_2_ 
    from
        book_review_info bookreview0_
**BookReviewInfo(super=BaseEntity(cratedAt=2021-08-26T16:47:10.790, updatedAt=2021-08-26T16:47:10.790), id=7, bookId=1, averageReviewScore=4.5, reviewCount=2)**
```

그러면 위와 같이 Book과 BookReviewInfo에 대한 저장 정보를 모두 확인해볼 수 있다!

🌟 이번에는 연관관계를 표시해보기 위해서 , `BookRepository를 이용해서 쿼리 메서드 findById를 이용해서 우리가 지정한 BookId로 저장이 잘 되었는지 확인부터 해주자

```java
public void relationTest(){
        Book book= new Book();
        book.setName("Jpa 초격차 패키지");
        book.setAuthorId(1L);
        book.setPublisherId(1L);

        bookRepository.save(book);

        BookReviewInfo info=new BookReviewInfo();
        info.setBookId(1L);
        info.setAverageReviewScore(4.5f);
        info.setReviewCount(2);

        bookReviewInfoRepository.save(info);

        bookRepository.findAll().forEach(System.out::println);
        bookReviewInfoRepository.findAll().forEach(System.out::println);

        Long bookId=bookReviewInfoRepository.findById(7L).orElseThrow(RuntimeException::new).getBookId();
        System.out.println("bookId: "+bookId);
        **Book book1=bookRepository.findById(bookId).orElseThrow(RuntimeException::new);**
        System.out.println(book1);
    }
```

💢 당근 지금 연관관계 설정을 해주지도 않았고, Book의 id값이 1이 아닌 6인 경우만 존해하고 있었기 때문에 이런 데이터는 없어서 RuntimeException이 발생한다~!! (에러로 공부해서 좋지만 싫어~~ 초록초록만 볼래..)

그러면! Book의 id도 존재하는 값인 6으로 넣어주면 될 것이다! 그러면 에러는 없을것이다!

```java
@Test
    public void relationTest(){
        Book book= new Book();
        book.setName("Jpa 초격차 패키지");
        book.setAuthorId(1L);
        book.setPublisherId(1L);

        bookRepository.save(book);

        BookReviewInfo info=new BookReviewInfo();
        **info.setBookId(6L);**
        info.setAverageReviewScore(4.5f);
        info.setReviewCount(2);

        bookReviewInfoRepository.save(info);

        bookRepository.findAll().forEach(System.out::println);
        bookReviewInfoRepository.findAll().forEach(System.out::println);

        Long bookId=bookReviewInfoRepository.findById(7L).orElseThrow(RuntimeException::new).getBookId();
        System.out.println("bookId: "+bookId);
        Book book1=bookRepository.findById(bookId).orElseThrow(RuntimeException::new);
        System.out.println(book1);
    }
```

```java
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        book
        (created_at, updated_at, author_id, category, name, publisher_id, id) 
    values
        (?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        book_review_info
        (created_at, updated_at, average_review_score, book_id, review_count, id) 
    values
        (?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.created_at as created_2_1_,
        book0_.updated_at as updated_3_1_,
        book0_.author_id as author_i4_1_,
        book0_.category as category5_1_,
        book0_.name as name6_1_,
        book0_.publisher_id as publishe7_1_ 
    from
        book book0_
Book(super=BaseEntity(cratedAt=2021-08-26T17:34:06.953, updatedAt=2021-08-26T17:34:06.953), id=6, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)
Hibernate: 
    select
        bookreview0_.id as id1_2_,
        bookreview0_.created_at as created_2_2_,
        bookreview0_.updated_at as updated_3_2_,
        bookreview0_.average_review_score as average_4_2_,
        bookreview0_.book_id as book_id5_2_,
        bookreview0_.review_count as review_c6_2_ 
    from
        book_review_info bookreview0_
BookReviewInfo(super=BaseEntity(cratedAt=2021-08-26T17:34:07.066, updatedAt=2021-08-26T17:34:07.066), id=7, bookId=6, averageReviewScore=4.5, reviewCount=2)
Hibernate: 
    select
        bookreview0_.id as id1_2_0_,
        bookreview0_.created_at as created_2_2_0_,
        bookreview0_.updated_at as updated_3_2_0_,
        bookreview0_.average_review_score as average_4_2_0_,
        bookreview0_.book_id as book_id5_2_0_,
        bookreview0_.review_count as review_c6_2_0_ 
    from
        book_review_info bookreview0_ 
    where
        bookreview0_.id=?
bookId: 6
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.created_at as created_2_1_0_,
        book0_.updated_at as updated_3_1_0_,
        book0_.author_id as author_i4_1_0_,
        book0_.category as category5_1_0_,
        book0_.name as name6_1_0_,
        book0_.publisher_id as publishe7_1_0_ 
    from
        book book0_ 
    where
        book0_.id=?
**Book(super=BaseEntity(cratedAt=2021-08-26T17:34:06.953, updatedAt=2021-08-26T17:34:06.953), id=6, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)**
```

그러면 위에서 저장했던 Book, 즉 아이디가 6인 레코드가 조회되는 것을 확인해볼 수 있다

전에도 잠깐 살펴봤듯이, GenerationType의 기본값 AUTO는 시퀀스로 관리되기 때문에 우리가 테스트할 때에는 "지금 테스트하는 데이터가 처음이군. 시퀀스값이 1이지..?"라고 생각할 수 있지만 그것은 오산이다!!

전에도 hibernate_sequence라는 공용 시퀀스(data.sql에서 명시)가 공유되면서 사용되어 사용된 적이 있을 수 있어서 꼭! 그 시퀀스 값을 확인하고 사용하기를 바란다!

이러한 복잡함을 해결하기 위해서 GenerationType의 IDENTITY 전략을 이용해보자

- MySQL이나 MariaDB의 기본 전략
- 테이블마다 해당 아이디값을 auto_increment값으로 지정하여 지속적으로 값을 증가시켜줌

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
///@EntityListeners(value=MyEntityListener.class)
//@EntityListeners(value= AuditingEntityListener.class)
public class Book extends BaseEntity/* implements Auditable*/ {
    @Id
    **@GeneratedValue(strategy = GenerationType.IDENTITY)**
    private Long id;
    //책 이름
    private String name;

    private String category;

    //책 저자
    private Long authorId;

    private Long publisherId;

}
```

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.BaseEntity;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
@NoArgsConstructor
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper = true)
@Data
public class BookReviewInfo extends BaseEntity {

    @Id
    **@GeneratedValue(strategy=GenerationType.IDENTITY)**
    private Long id;//boorReviewInfo 의 PK

    private Long bookId;

    //Wrapper class는 null을 허용할지 안할지가 중요한데
    //averageReviewScore나 reviewCount는 null도 포함시켜서
    //생각해볼 수 있기 때문에 small float, small int를 이용(null이 0이므로)

    private float averageReviewScore;

    private int reviewCount;

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
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper=true)
//@EntityListeners(value={MyEntityListener.class})
//@EntityListeners(value={AuditingEntityListener.class})
public class UserHistory extends BaseEntity/*implements Auditable*/ {
    @Id
    **@GeneratedValue(strategy = GenerationType.IDENTITY)**
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
@Test
    public void relationTest(){
        Book book= new Book();
        book.setName("Jpa 초격차 패키지");
        book.setAuthorId(1L);
        book.setPublisherId(1L);

        bookRepository.save(book);

        BookReviewInfo info=new BookReviewInfo();
        **info.setBookId(1L);**
        info.setAverageReviewScore(4.5f);
        info.setReviewCount(2);

        bookReviewInfoRepository.save(info);

        bookRepository.findAll().forEach(System.out::println);
        bookReviewInfoRepository.findAll().forEach(System.out::println);

        **Long bookId=bookReviewInfoRepository.findById(1L).orElseThrow(RuntimeException::new).getBookId();**
        System.out.println("bookId: "+bookId);
        Book book1=bookRepository.findById(bookId).orElseThrow(RuntimeException::new);
        System.out.println(book1);
    }
```

그리고 테스트에도 값을 1L로 변경해서 진행해주면

이번에는 6이나 7값으로 들어가지 않고, 1로 값이 들어가는 것을 확인해볼 수 있다

```java
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
        book_review_info
        (id, created_at, updated_at, average_review_score, book_id, review_count) 
    values
        (null, ?, ?, ?, ?, ?)
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.created_at as created_2_1_,
        book0_.updated_at as updated_3_1_,
        book0_.author_id as author_i4_1_,
        book0_.category as category5_1_,
        book0_.name as name6_1_,
        book0_.publisher_id as publishe7_1_ 
    from
        book book0_
Book(super=BaseEntity(cratedAt=2021-08-26T17:46:30.047, updatedAt=2021-08-26T17:46:30.047), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)
Hibernate: 
    select
        bookreview0_.id as id1_2_,
        bookreview0_.created_at as created_2_2_,
        bookreview0_.updated_at as updated_3_2_,
        bookreview0_.average_review_score as average_4_2_,
        bookreview0_.book_id as book_id5_2_,
        bookreview0_.review_count as review_c6_2_ 
    from
        book_review_info bookreview0_
BookReviewInfo(super=BaseEntity(cratedAt=2021-08-26T17:46:30.127, updatedAt=2021-08-26T17:46:30.127), id=1, bookId=1, averageReviewScore=4.5, reviewCount=2)
Hibernate: 
    select
        bookreview0_.id as id1_2_0_,
        bookreview0_.created_at as created_2_2_0_,
        bookreview0_.updated_at as updated_3_2_0_,
        bookreview0_.average_review_score as average_4_2_0_,
        bookreview0_.book_id as book_id5_2_0_,
        bookreview0_.review_count as review_c6_2_0_ 
    from
        book_review_info bookreview0_ 
    where
        bookreview0_.id=?
bookId: 1
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.created_at as created_2_1_0_,
        book0_.updated_at as updated_3_1_0_,
        book0_.author_id as author_i4_1_0_,
        book0_.category as category5_1_0_,
        book0_.name as name6_1_0_,
        book0_.publisher_id as publishe7_1_0_ 
    from
        book book0_ 
    where
        book0_.id=?
Book(super=BaseEntity(cratedAt=2021-08-26T17:46:30.047, updatedAt=2021-08-26T17:46:30.047), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)
```

그런데 추후에 시퀀스에 대해서 "org.h2.jdbc.JdbcSQLSyntaxErrorException:Sequence 'HIBERNATE_SEQUENCE' not found" 라고 에러가 나올 수도 있으므로, data.sql에서 call for로 시퀀스를 부르는 부분을 지워주자

```java
--call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (1, 'martin', 'martin@fastcampus.com', now(), now(),true);

--call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (2, 'dennis', 'dennis@fastcampus.com', now(), now(),true);

--call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (3, 'sophia', 'sophia@slowcampus.com', now(), now(),false);

--call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (4, 'james', 'james@slowcampus.com', now(), now(),false);

--call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`,`active`) values (5, 'martin', 'martin@another.com', now(), now(),true);
```

User도 그러면 IDENTITY전략으로 바꿔주자

(앞으로는 시퀀스를 사용하지 않으므로)

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.listener.UserEntityListener;
import lombok.*;

import javax.persistence.*;

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
    **@GeneratedValue(strategy = GenerationType.IDENTITY)**
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

}
```

그리고 DDL로 확인해보면 이전에는 NOT NULL이 붙지 않고, `generated by default as identity` 가 붙는 것을 확인해볼 수 있다!

바로 H2에서 IDENTITY 전략이 붙은 것이다!

```java
Hibernate: 
    
    create table book (
       **id bigint generated by default as identity,**
        created_at timestamp,
        updated_at timestamp,
        author_id bigint,
        category varchar(255),
        name varchar(255),
        publisher_id bigint,
        primary key (id)
    )
Hibernate: 
    
    create table book_review_info (
       **id bigint generated by default as identity,**
        created_at timestamp,
        updated_at timestamp,
        average_review_score float not null,
        book_id bigint,
        review_count integer not null,
        primary key (id)
    )
Hibernate: 
    
    create table user (
       **id bigint generated by default as identity,**
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
       **id bigint generated by default as identity,**
        created_at timestamp,
        updated_at timestamp,
        active boolean not null,
        email varchar(255),
        gender varchar(255),
        name varchar(255),
        user_id bigint,
        primary key (id)
    )
```

이러한 점을 통해서 RDB의 관계에서 FK를 이용하는 방식을 확인해볼 수 있었다

하지만, JPA에서는 이를 보다 간편하게 만들 수 있다(이방법은 우리는 "음~그렇구나"할 수 있는 정도지만, 매번 우리가 이런 방식으로 select와 같은 조회 작업으로만 확인해볼 수는 없지 않은가!!)

우선, 테스트를 조금 더 깔끔하게 만들기 위해서 BeforeEach를 붙여서 데이터 저장을 사전에 진행하도록 해주고, 기존의 relationTest라는 테스트를 진행하는 메서드는 select 조회만 남겨두자

```java
@BeforeEach
    public void givenBook(){
        Book book= new Book();
        book.setName("Jpa 초격차 패키지");
        book.setAuthorId(1L);
        book.setPublisherId(1L);

        bookRepository.save(book);
    }

    @BeforeEach
    public void givenBookReview(){
        BookReviewInfo info=new BookReviewInfo();
        info.setBookId(1L);
        info.setAverageReviewScore(4.5f);
        info.setReviewCount(2);

        bookReviewInfoRepository.save(info);

    }

    @Test
    public void relationTest(){
        bookRepository.findAll().forEach(System.out::println);
        bookReviewInfoRepository.findAll().forEach(System.out::println);

        Long bookId=bookReviewInfoRepository.findById(1L).orElseThrow(RuntimeException::new).getBookId();
        System.out.println("bookId: "+bookId);
        Book book1=bookRepository.findById(bookId).orElseThrow(RuntimeException::new);
        System.out.println(book1);
    }
```

기존의 test라는 메서드는 겹칠 수 있으니 아래처럼 값만 바꿔주자

```java
@Test
    public void test(){
        BookReviewInfo info=new BookReviewInfo();

        **info.setBookId(2L);**
        info.setAverageReviewScore(3f);
        info.setReviewCount(5);

        bookReviewInfoRepository.save(info);
        bookReviewInfoRepository.findAll().forEach(System.out::println);
    }
```

그리고 이어서 아래 단계를 진행해주자

## BookReviewInfo 클래스 내부에 bookId 대신 Book 객체를 넣기! ==다른 테이블을 참조하는 테이블 측에 `@OneToOne`

그냥 `private Book book`만 해주면 "'Basic' attribute type should not be 'Persistence Entity'"라고 경고가 뜰 것이다. 일반 속성은 영속성 객체가 될 수 없다는 뜻인데, 하나의 엔티티 내부에 다른 엔티티를 넣으면 관계가 필요하기 때문에 이런 경고가 뜬 것 같다

우리는 지금 `1:1관계`이기 때문에 `@OneToOne`을 위에 붙여주자!

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.BaseEntity;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.*;

@Entity
@NoArgsConstructor
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper = true)
@Data
public class BookReviewInfo extends BaseEntity {

    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private Long id;//boorReviewInfo 의 PK

    //private Long bookId;
    **@OneToOne
    private Book book;**

    //Wrapper class는 null을 허용할지 안할지가 중요한데
    //averageReviewScore나 reviewCount는 null도 포함시켜서
    //생각해볼 수 있기 때문에 small float, small int를 이용(null이 0이므로)

    private float averageReviewScore;

    private int reviewCount;

}
```

테이블에는 BookId가 존재하지만, 지금은 엔티티로 접근하는데 jpa에서

`엔티티로 set/get을 하게 되면 자동으로 관계에 의해 매칭된다`

그러면 테스트는 먼저 Book 객체로 정보가 오고 가야 하기 때문에,

givenBook이나 givenBookReview 메서드는 BeforeEach에서 해제시켜주고(givenBook이 실행된다 하더라도, 이를 givenBookReview의 파라미터로 주입시키려면 BeforeEach에서 해제시키고 각 메서드에서 호출하는 것이 바람직할 것이다)

givenBook의 리턴타입은 Book으로 시정해주자

더불어, 위에서 언급한 것처럼 엔티티로 set/get을 하기위해서 setBook과 getBook을 이용하는 것으로 접근하자!

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Book;
import com.example.jpa_entity.domain.BookReviewInfo;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class BookReviewInfoRepositoryTest {

    @Autowired
    private BookReviewInfoRepository bookReviewInfoRepository;

    @Autowired
    private BookRepository bookRepository;

    @Test
    public void test(){
        Book book=givenBook();
        BookReviewInfo info=new BookReviewInfo();

        info.setBook(book);
        info.setAverageReviewScore(3f);
        info.setReviewCount(5);

        bookReviewInfoRepository.save(info);
        bookReviewInfoRepository.findAll().forEach(System.out::println);
    }

    public Book givenBook(){
        Book book= new Book();
        book.setName("Jpa 초격차 패키지");
        book.setAuthorId(1L);
        book.setPublisherId(1L);

        Book book1=bookRepository.save(book);
        return book1;
    }

    public void givenBookReview(Book book){
        BookReviewInfo info=new BookReviewInfo();
        info.setBook(book);
        info.setAverageReviewScore(4.5f);
        info.setReviewCount(2);

        bookReviewInfoRepository.save(info);

    }

    @Test
    public void relationTest(){
        Book book=givenBook();
        givenBookReview(book);

        bookRepository.findAll().forEach(System.out::println);
        bookReviewInfoRepository.findAll().forEach(System.out::println);

        **Book book1=bookReviewInfoRepository.findById(1L).orElseThrow(RuntimeException::new).getBook();**
        System.out.println(book1);
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
Hibernate: 
    insert 
    into
        book_review_info
        (id, created_at, updated_at, average_review_score, book_id, review_count) 
    values
        (null, ?, ?, ?, ?, ?)
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.created_at as created_2_1_,
        book0_.updated_at as updated_3_1_,
        book0_.author_id as author_i4_1_,
        book0_.category as category5_1_,
        book0_.name as name6_1_,
        book0_.publisher_id as publishe7_1_ 
    from
        book book0_
Book(super=BaseEntity(cratedAt=2021-08-26T19:26:52.155, updatedAt=2021-08-26T19:26:52.155), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)
Hibernate: 
    select
        bookreview0_.id as id1_2_,
        bookreview0_.created_at as created_2_2_,
        bookreview0_.updated_at as updated_3_2_,
        bookreview0_.average_review_score as average_4_2_,
        bookreview0_.book_id as book_id6_2_,
        bookreview0_.review_count as review_c5_2_ 
    from
        book_review_info bookreview0_
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.created_at as created_2_1_0_,
        book0_.updated_at as updated_3_1_0_,
        book0_.author_id as author_i4_1_0_,
        book0_.category as category5_1_0_,
        book0_.name as name6_1_0_,
        book0_.publisher_id as publishe7_1_0_ 
    from
        book book0_ 
    where
        book0_.id=?
**BookReviewInfo(super=BaseEntity(cratedAt=2021-08-26T19:26:52.241, updatedAt=2021-08-26T19:26:52.241), id=1, book=Book(super=BaseEntity(cratedAt=2021-08-26T19:26:52.155, updatedAt=2021-08-26T19:26:52.155), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1), averageReviewScore=4.5, reviewCount=2)**
Hibernate: 
    select
        bookreview0_.id as id1_2_0_,
        bookreview0_.created_at as created_2_2_0_,
        bookreview0_.updated_at as updated_3_2_0_,
        bookreview0_.average_review_score as average_4_2_0_,
        bookreview0_.book_id as book_id6_2_0_,
        bookreview0_.review_count as review_c5_2_0_,
        book1_.id as id1_1_1_,
        book1_.created_at as created_2_1_1_,
        book1_.updated_at as updated_3_1_1_,
        book1_.author_id as author_i4_1_1_,
        book1_.category as category5_1_1_,
        book1_.name as name6_1_1_,
        book1_.publisher_id as publishe7_1_1_ 
    from
        book_review_info bookreview0_ 
    **left outer join**
        book book1_ 
            on bookreview0_.book_id=book1_.id 
    where
        bookreview0_.id=?
bookId: 1
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.created_at as created_2_1_0_,
        book0_.updated_at as updated_3_1_0_,
        book0_.author_id as author_i4_1_0_,
        book0_.category as category5_1_0_,
        book0_.name as name6_1_0_,
        book0_.publisher_id as publishe7_1_0_ 
    from
        book book0_ 
    where
        book0_.id=?
Book(super=BaseEntity(cratedAt=2021-08-26T19:26:52.155, updatedAt=2021-08-26T19:26:52.155), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)
```

그러면 위와 같이, 아이디값과, Book값이 잘 매칭되어 조회되는 것을 확인해볼 수 있다!

더불어서, BookReviewInfo에 Book 엔티티 레코드 객체가 1:1관계로 매칭되어 확인된 것을 살펴볼 수 있다

++더불어, BookId가 아닌 , 이제는 바로 BookReviewInfo의 id로 Book에 접근할 수도 있다!

```java
 **Book book1=bookReviewInfoRepository.findById(1L).orElseThrow(RuntimeException::new).getBook();**
```

DDL을 살펴보면, 이전에 bookId가 있던 것과 유사하게 되어 있는 것을 볼 수 있지만, 차이점은 `이제는 Book 객체의 id값을 이용해서 참조해온다는 점, 즉 Lazy Fetch로 데이터를 다루고 있다`는 점을 확인해볼 수 있다

```java
create table book (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        author_id bigint,
        category varchar(255),
        name varchar(255),
        publisher_id bigint,
        primary key (id)
    )
Hibernate: 
    
    create table book_review_info (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        average_review_score float not null,
        review_count integer not null,
        **book_id bigint,**
        primary key (id)
    )
```

그리고 아래의 출력문에서 볼 수 있듯이 BookReviewInfo와 Book 엔티티가 LEFT OUTER JOIN 연산을 통해 조회하였음을 확인해볼 수 있다

즉, BookReviewInfo에 있는 것은 모두 넣어서 조회한 것이다

![https://www.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Left-Join-with-Where.png](https://www.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Left-Join-with-Where.png)

source: [https://www.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Left-Join-with-Where.png](https://www.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Join-Left-Join-with-Where.png)

```java
Hibernate: 
    select
        bookreview0_.id as id1_2_0_,
        bookreview0_.created_at as created_2_2_0_,
        bookreview0_.updated_at as updated_3_2_0_,
        bookreview0_.average_review_score as average_4_2_0_,
        bookreview0_.book_id as book_id6_2_0_,
        bookreview0_.review_count as review_c5_2_0_,
        book1_.id as id1_1_1_,
        book1_.created_at as created_2_1_1_,
        book1_.updated_at as updated_3_1_1_,
        book1_.author_id as author_i4_1_1_,
        book1_.category as category5_1_1_,
        book1_.name as name6_1_1_,
        book1_.publisher_id as publishe7_1_1_ 
    from
        book_review_info bookreview0_ 
    **left outer join**
        book book1_ 
            on bookreview0_.book_id=book1_.id 
    where
        bookreview0_.id=?
bookId: 1
```

그런데 LEFT OUTER JOIN을 한 이유는 OneToOne 내부에는

아래처럼 optional이라는 값의 기본값이 true로 되어 있어서

`해당 대상이 존재할 수도 없을 수도 있다`라는 의미라서, `완전한,교집합 외의 Book 부분은 제외되는 것 같은 LEFT OUTER JOIN`이 진행된 것이다

보통 LEFT OUTER JOIN을 기본적으로 outer join이라고 본다는 점에서 그냥 outer join되었다고 보면 될 것이다

```java
/*
 * Copyright (c) 2008, 2019 Oracle and/or its affiliates. All rights reserved.
 *
 * This program and the accompanying materials are made available under the
 * terms of the Eclipse Public License v. 2.0 which is available at
 * http://www.eclipse.org/legal/epl-2.0,
 * or the Eclipse Distribution License v. 1.0 which is available at
 * http://www.eclipse.org/org/documents/edl-v10.php.
 *
 * SPDX-License-Identifier: EPL-2.0 OR BSD-3-Clause
 */

// Contributors:
//     Linda DeMichiel - 2.1
//     Linda DeMichiel - 2.0

package javax.persistence;

import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import javax.persistence.CascadeType;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.RetentionPolicy.RUNTIME;
import static javax.persistence.FetchType.EAGER;

/**
 * Specifies a single-valued association to another entity that has
 * one-to-one multiplicity. It is not normally necessary to specify
 * the associated target entity explicitly since it can usually be
 * inferred from the type of the object being referenced.  If the relationship is
 * bidirectional, the non-owning side must use the <code>mappedBy</code> element of
 * the <code>OneToOne</code> annotation to specify the relationship field or
 * property of the owning side.
 *
 * <p> The <code>OneToOne</code> annotation may be used within an
 * embeddable class to specify a relationship from the embeddable
 * class to an entity class. If the relationship is bidirectional and
 * the entity containing the embeddable class is on the owning side of
 * the relationship, the non-owning side must use the
 * <code>mappedBy</code> element of the <code>OneToOne</code>
 * annotation to specify the relationship field or property of the
 * embeddable class. The dot (".") notation syntax must be used in the
 * <code>mappedBy</code> element to indicate the relationship attribute within the
 * embedded attribute.  The value of each identifier used with the dot
 * notation is the name of the respective embedded field or property.
 * 
 * <pre>
 *    Example 1: One-to-one association that maps a foreign key column
 *
 *    // On Customer class:
 *
 *    &#064;OneToOne(optional=false)
 *    &#064;JoinColumn(
 *    	name="CUSTREC_ID", unique=true, nullable=false, updatable=false)
 *    public CustomerRecord getCustomerRecord() { return customerRecord; }
 *
 *    // On CustomerRecord class:
 *
 *    &#064;OneToOne(optional=false, mappedBy="customerRecord")
 *    public Customer getCustomer() { return customer; }
 *
 *
 *    Example 2: One-to-one association that assumes both the source and target share the same primary key values. 
 *
 *    // On Employee class:
 *
 *    &#064;Entity
 *    public class Employee {
 *    	&#064;Id Integer id;
 *    
 *    	&#064;OneToOne &#064;MapsId
 *    	EmployeeInfo info;
 *    	...
 *    }
 *
 *    // On EmployeeInfo class:
 *
 *    &#064;Entity
 *    public class EmployeeInfo {
 *    	&#064;Id Integer id;
 *    	...
 *    }
 *
 *
 *    Example 3: One-to-one association from an embeddable class to another entity.
 *
 *    &#064;Entity
 *    public class Employee {
 *       &#064;Id int id;
 *       &#064;Embedded LocationDetails location;
 *       ...
 *    }
 *
 *    &#064;Embeddable
 *    public class LocationDetails {
 *       int officeNumber;
 *       &#064;OneToOne ParkingSpot parkingSpot;
 *       ...
 *    }
 *
 *    &#064;Entity
 *    public class ParkingSpot {
 *       &#064;Id int id;
 *       String garage;
 *       &#064;OneToOne(mappedBy="location.parkingSpot") Employee assignedTo;
 *        ... 
 *    } 
 *
 * </pre>
 *
 * @since 1.0
 */
@Target({METHOD, FIELD}) 
@Retention(RUNTIME)

public @interface OneToOne {

    /** 
     * (Optional) The entity class that is the target of 
     * the association. 
     *
     * <p> Defaults to the type of the field or property 
     * that stores the association. 
     */
    Class targetEntity() default void.class;

    /**
     * (Optional) The operations that must be cascaded to 
     * the target of the association.
     *
     * <p> By default no operations are cascaded.
     */
    CascadeType[] cascade() default {};

    /** 
     * (Optional) Whether the association should be lazily 
     * loaded or must be eagerly fetched. The EAGER 
     * strategy is a requirement on the persistence provider runtime that 
     * the associated entity must be eagerly fetched. The LAZY 
     * strategy is a hint to the persistence provider runtime.
     */
    FetchType fetch() default EAGER;

    /** 
     * (Optional) Whether the association is optional. If set 
     * to false then a non-null relationship must always exist.
     */
    boolean optional() default true;

    /** (Optional) The field that owns the relationship. This 
      * element is only specified on the inverse (non-owning) 
      * side of the association.
     */
    String mappedBy() default "";

    /**
     * (Optional) Whether to apply the remove operation to entities that have
     * been removed from the relationship and to cascade the remove operation to
     * those entities.
     * @since 2.0
     */
    boolean orphanRemoval() default false;
}
```

이를 역이용해서 이번에는 optional=false로 진행해보자

```java
package com.example.jpa_entity.domain;

import com.example.jpa_entity.domain.BaseEntity;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.*;

@Entity
@NoArgsConstructor
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper = true)
@Data
public class BookReviewInfo extends BaseEntity {

    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private Long id;//boorReviewInfo 의 PK

    //private Long bookId;
    @OneToOne(optional = false)
    private Book book;

    //Wrapper class는 null을 허용할지 안할지가 중요한데
    //averageReviewScore나 reviewCount는 null도 포함시켜서
    //생각해볼 수 있기 때문에 small float, small int를 이용(null이 0이므로)

    private float averageReviewScore;

    private int reviewCount;

}
```

그러면 동일한 테스트를 진행해보면(relationTest), 아래처럼 이번에는 inner join이 실행된다

```java
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
        book_review_info
        (id, created_at, updated_at, average_review_score, book_id, review_count) 
    values
        (null, ?, ?, ?, ?, ?)
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.created_at as created_2_1_,
        book0_.updated_at as updated_3_1_,
        book0_.author_id as author_i4_1_,
        book0_.category as category5_1_,
        book0_.name as name6_1_,
        book0_.publisher_id as publishe7_1_ 
    from
        book book0_
Book(super=BaseEntity(cratedAt=2021-08-26T19:44:48.344, updatedAt=2021-08-26T19:44:48.344), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)
Hibernate: 
    select
        bookreview0_.id as id1_2_,
        bookreview0_.created_at as created_2_2_,
        bookreview0_.updated_at as updated_3_2_,
        bookreview0_.average_review_score as average_4_2_,
        bookreview0_.book_id as book_id6_2_,
        bookreview0_.review_count as review_c5_2_ 
    from
        book_review_info bookreview0_
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.created_at as created_2_1_0_,
        book0_.updated_at as updated_3_1_0_,
        book0_.author_id as author_i4_1_0_,
        book0_.category as category5_1_0_,
        book0_.name as name6_1_0_,
        book0_.publisher_id as publishe7_1_0_ 
    from
        book book0_ 
    where
        book0_.id=?
BookReviewInfo(super=BaseEntity(cratedAt=2021-08-26T19:44:48.424, updatedAt=2021-08-26T19:44:48.424), id=1, book=Book(super=BaseEntity(cratedAt=2021-08-26T19:44:48.344, updatedAt=2021-08-26T19:44:48.344), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1), averageReviewScore=4.5, reviewCount=2)
Hibernate: 
    select
        bookreview0_.id as id1_2_0_,
        bookreview0_.created_at as created_2_2_0_,
        bookreview0_.updated_at as updated_3_2_0_,
        bookreview0_.average_review_score as average_4_2_0_,
        bookreview0_.book_id as book_id6_2_0_,
        bookreview0_.review_count as review_c5_2_0_,
        book1_.id as id1_1_1_,
        book1_.created_at as created_2_1_1_,
        book1_.updated_at as updated_3_1_1_,
        book1_.author_id as author_i4_1_1_,
        book1_.category as category5_1_1_,
        book1_.name as name6_1_1_,
        book1_.publisher_id as publishe7_1_1_ 
    from
        book_review_info bookreview0_ 
    **inner join**
        book book1_ 
            on bookreview0_.book_id=book1_.id 
    where
        bookreview0_.id=?
Book(super=BaseEntity(cratedAt=2021-08-26T19:44:48.344, updatedAt=2021-08-26T19:44:48.344), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)
```

이렇게 `optional=false`가 되면, `교집합을 찾는 것이 되어서, Book은 절대로! 값이 없을 수가 없다!`

그래서, 이전에는 BookReviewInfo 테이블의 book_id에 NOT NULL이 없었는데, 이번에는 NOT NULL이 붙은 것을 확인해볼 수 있다

```java
Hibernate: 
    
    create table book_review_info (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        average_review_score float not null,
        review_count integer not null,
        **book_id bigint not null,**
        primary key (id)
    )
```

이처럼 자바 객체의 속성에 따라 최적의 쿼리와 DDL을 만들어서 실행할 수 있도록 해주는 것이 바로 JPA의 역할이다!

'그런데 만약 Book 객체에서 BookReviewInfo 정보를 가져오려면 어떻게 해야할까?'

[테스트가 끝났으므로, optional값은 돌려두자 true로 ]

## `참조를 당한 테이블` Book에서 `참조를 주도한 테이블` BookReviewInfo를 확인하기 `@OneToOne` ➕ `mappedBy와 @ToString.Exclude를 이용한 순환참조 막기`

동일하게 Book 내부에 BookReviewInfo를 넣고 `@OneToOne`을 붙여주면 될 것이다

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

    **@OneToOne
    private BookReviewInfo bookReviewInfo;**
}
```

그러면 기존의 테스트를 해보고 Book 내부에서 BookReviewInfo를 확인해보자

그러면 우선 DDL은 OneToOne의 optional 값이 지금은 true라서 book_review_info_id, book_id에  not null이 붙지 않았다

```java
Hibernate: 
    
    create table book (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        author_id bigint,
        category varchar(255),
        name varchar(255),
        publisher_id bigint,
        book_review_info_id bigint,
        primary key (id)
    )
Hibernate: 
    
    create table book_review_info (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        average_review_score float not null,
        review_count integer not null,
        book_id bigint,
        primary key (id)
    )
```

```java
Hibernate: 
    insert 
    into
        book
        (id, created_at, updated_at, author_id, book_review_info_id, category, name, publisher_id) 
    values
        (null, ?, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_review_info
        (id, created_at, updated_at, average_review_score, book_id, review_count) 
    values
        (null, ?, ?, ?, ?, ?)
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.created_at as created_2_1_,
        book0_.updated_at as updated_3_1_,
        book0_.author_id as author_i4_1_,
        book0_.book_review_info_id as book_rev8_1_,
        book0_.category as category5_1_,
        book0_.name as name6_1_,
        book0_.publisher_id as publishe7_1_ 
    from
        book book0_
Book(super=BaseEntity(cratedAt=2021-08-26T20:09:50.438, updatedAt=2021-08-26T20:09:50.438), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1, bookReviewInfo=null)
Hibernate: 
    select
        bookreview0_.id as id1_2_,
        bookreview0_.created_at as created_2_2_,
        bookreview0_.updated_at as updated_3_2_,
        bookreview0_.average_review_score as average_4_2_,
        bookreview0_.book_id as book_id6_2_,
        bookreview0_.review_count as review_c5_2_ 
    from
        book_review_info bookreview0_
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.created_at as created_2_1_0_,
        book0_.updated_at as updated_3_1_0_,
        book0_.author_id as author_i4_1_0_,
        book0_.book_review_info_id as book_rev8_1_0_,
        book0_.category as category5_1_0_,
        book0_.name as name6_1_0_,
        book0_.publisher_id as publishe7_1_0_,
        bookreview1_.id as id1_2_1_,
        bookreview1_.created_at as created_2_2_1_,
        bookreview1_.updated_at as updated_3_2_1_,
        bookreview1_.average_review_score as average_4_2_1_,
        bookreview1_.book_id as book_id6_2_1_,
        bookreview1_.review_count as review_c5_2_1_,
        book2_.id as id1_1_2_,
        book2_.created_at as created_2_1_2_,
        book2_.updated_at as updated_3_1_2_,
        book2_.author_id as author_i4_1_2_,
        book2_.book_review_info_id as book_rev8_1_2_,
        book2_.category as category5_1_2_,
        book2_.name as name6_1_2_,
        book2_.publisher_id as publishe7_1_2_ 
    from
        book book0_ 
    **left outer join
        book_review_info bookreview1_ 
            on book0_.book_review_info_id=bookreview1_.id 
    left outer join
        book book2_ 
            on bookreview1_.book_id=book2_.id** 
    where
        book0_.id=?
BookReviewInfo(super=BaseEntity(cratedAt=2021-08-26T20:09:50.518, updatedAt=2021-08-26T20:09:50.518), id=1, book=Book(super=BaseEntity(cratedAt=2021-08-26T20:09:50.438, updatedAt=2021-08-26T20:09:50.438), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1, bookReviewInfo=null), averageReviewScore=4.5, reviewCount=2)
Hibernate: 
    select
        bookreview0_.id as id1_2_0_,
        bookreview0_.created_at as created_2_2_0_,
        bookreview0_.updated_at as updated_3_2_0_,
        bookreview0_.average_review_score as average_4_2_0_,
        bookreview0_.book_id as book_id6_2_0_,
        bookreview0_.review_count as review_c5_2_0_,
        book1_.id as id1_1_1_,
        book1_.created_at as created_2_1_1_,
        book1_.updated_at as updated_3_1_1_,
        book1_.author_id as author_i4_1_1_,
        book1_.book_review_info_id as book_rev8_1_1_,
        book1_.category as category5_1_1_,
        book1_.name as name6_1_1_,
        book1_.publisher_id as publishe7_1_1_,
        bookreview2_.id as id1_2_2_,
        bookreview2_.created_at as created_2_2_2_,
        bookreview2_.updated_at as updated_3_2_2_,
        bookreview2_.average_review_score as average_4_2_2_,
        bookreview2_.book_id as book_id6_2_2_,
        bookreview2_.review_count as review_c5_2_2_ 
    from
        book_review_info bookreview0_ 
    **left outer join
        book book1_ 
            on bookreview0_.book_id=book1_.id 
    left outer join
        book_review_info bookreview2_ 
            on book1_.book_review_info_id=bookreview2_.id** 
    where
        bookreview0_.id=?
Book(super=BaseEntity(cratedAt=2021-08-26T20:09:50.438, updatedAt=2021-08-26T20:09:50.438), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1, bookReviewInfo=null)
```

그런데 DDL 이후를 살펴보면 LEFT OUTER JOIN이 여러번 반복된다.

그리고 최하단에서 BookReviewInfo가 null이 뜬 것은 지금 참조 방식을 변경해주어야 하기 때문에, 이를 제외한 조인만 확인하자

### (1) `@OneToOne(mappedBy=)`와 `@ToString.Exclude`를 이용한 순환참조 끊기

➕`OneToOne의 mappedBy 옵션`을 이용하면, `연관 키를 해당 옵션이 설정된 테이블 내에서 삭제될 수 있도록 한다`

이러한 mappedBy 속성을 이용해보자(일종의 테스트)

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

    **@OneToOne(mappedBy = "book")**
    private BookReviewInfo bookReviewInfo;
}
```

그러면 테스트를 진행해보면 아래와 같은 StackOverFlowError가 발생한다

```java
java.lang.StackOverflowError
	at java.lang.AbstractStringBuilder.append
```

`엔티티 관계를 이용할 때, toString에서 순환참조가 일어나게 된다`

따라서 `관계는 특별한 관계가 아니라면 단방향으로 걸거나, toString에서 제외하고(@ToString.Exclude) 진행해주어야 한다`

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
    **@ToString.Exclude**
    private BookReviewInfo bookReviewInfo;
}
```

---

그리고 이제, 테스트에서 Book이 아닌, `BookReviewInfo를 bookRepository를 이용해서 접근해보자`

즉, 이전에 Book은 bookReviewInfoRepository로 접근을 시도했는데 이번에는 bookRepository로 접근하는 것이다!

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Book;
import com.example.jpa_entity.domain.BookReviewInfo;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class BookReviewInfoRepositoryTest {

    @Autowired
    private BookReviewInfoRepository bookReviewInfoRepository;

    @Autowired
    private BookRepository bookRepository;

    @Test
    public void test(){
        Book book=givenBook();
        BookReviewInfo info=new BookReviewInfo();

        info.setBook(book);
        info.setAverageReviewScore(3f);
        info.setReviewCount(5);

        bookReviewInfoRepository.save(info);
        bookReviewInfoRepository.findAll().forEach(System.out::println);
    }

    public Book givenBook(){
        Book book= new Book();
        book.setName("Jpa 초격차 패키지");
        book.setAuthorId(1L);
        book.setPublisherId(1L);

        Book book1=bookRepository.save(book);
        return book1;
    }

    public void givenBookReview(Book book){
        BookReviewInfo info=new BookReviewInfo();
        info.setBook(book);
        info.setAverageReviewScore(4.5f);
        info.setReviewCount(2);

        bookReviewInfoRepository.save(info);

    }

    @Test
    public void relationTest(){
        Book book=givenBook();
        givenBookReview(book);

        bookRepository.findAll().forEach(System.out::println);
        bookReviewInfoRepository.findAll().forEach(System.out::println);

        Book book1=bookReviewInfoRepository.findById(1L).orElseThrow(RuntimeException::new).getBook();
        System.out.println(book1);

        ***BookReviewInfo info=bookRepository.findById(1L).orElseThrow(RuntimeException::new).getBookReviewInfo();
        System.out.println(info);***
    }

}
```

그러면 이번에 실행된 DDL부터 모든 쿼리를 살펴보면

```java
Hibernate: 
    
    create table book (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        author_id bigint,
        category varchar(255),
        name varchar(255),
        publisher_id bigint,
        primary key (id)
    )
Hibernate: 
    
    create table book_review_info (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        average_review_score float not null,
        review_count integer not null,
        book_id bigint,
        primary key (id)
    )
(생략)
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
        book_review_info
        (id, created_at, updated_at, average_review_score, book_id, review_count) 
    values
        (null, ?, ?, ?, ?, ?)
Hibernate: 
    select
        book0_.id as id1_1_,
        book0_.created_at as created_2_1_,
        book0_.updated_at as updated_3_1_,
        book0_.author_id as author_i4_1_,
        book0_.category as category5_1_,
        book0_.name as name6_1_,
        book0_.publisher_id as publishe7_1_ 
    from
        book book0_
Hibernate: 
    select
        bookreview0_.id as id1_2_1_,
        bookreview0_.created_at as created_2_2_1_,
        bookreview0_.updated_at as updated_3_2_1_,
        bookreview0_.average_review_score as average_4_2_1_,
        bookreview0_.book_id as book_id6_2_1_,
        bookreview0_.review_count as review_c5_2_1_,
        book1_.id as id1_1_0_,
        book1_.created_at as created_2_1_0_,
        book1_.updated_at as updated_3_1_0_,
        book1_.author_id as author_i4_1_0_,
        book1_.category as category5_1_0_,
        book1_.name as name6_1_0_,
        book1_.publisher_id as publishe7_1_0_ 
    from
        book_review_info bookreview0_ 
    left outer join
        book book1_ 
            on bookreview0_.book_id=book1_.id 
    where
        bookreview0_.book_id=?
Book(super=BaseEntity(cratedAt=2021-08-26T20:24:21.503, updatedAt=2021-08-26T20:24:21.503), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)
Hibernate: 
    select
        bookreview0_.id as id1_2_,
        bookreview0_.created_at as created_2_2_,
        bookreview0_.updated_at as updated_3_2_,
        bookreview0_.average_review_score as average_4_2_,
        bookreview0_.book_id as book_id6_2_,
        bookreview0_.review_count as review_c5_2_ 
    from
        book_review_info bookreview0_
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.created_at as created_2_1_0_,
        book0_.updated_at as updated_3_1_0_,
        book0_.author_id as author_i4_1_0_,
        book0_.category as category5_1_0_,
        book0_.name as name6_1_0_,
        book0_.publisher_id as publishe7_1_0_,
        bookreview1_.id as id1_2_1_,
        bookreview1_.created_at as created_2_2_1_,
        bookreview1_.updated_at as updated_3_2_1_,
        bookreview1_.average_review_score as average_4_2_1_,
        bookreview1_.book_id as book_id6_2_1_,
        bookreview1_.review_count as review_c5_2_1_ 
    from
        book book0_ 
    left outer join
        book_review_info bookreview1_ 
            on book0_.id=bookreview1_.book_id 
    where
        book0_.id=?
Hibernate: 
    select
        bookreview0_.id as id1_2_1_,
        bookreview0_.created_at as created_2_2_1_,
        bookreview0_.updated_at as updated_3_2_1_,
        bookreview0_.average_review_score as average_4_2_1_,
        bookreview0_.book_id as book_id6_2_1_,
        bookreview0_.review_count as review_c5_2_1_,
        book1_.id as id1_1_0_,
        book1_.created_at as created_2_1_0_,
        book1_.updated_at as updated_3_1_0_,
        book1_.author_id as author_i4_1_0_,
        book1_.category as category5_1_0_,
        book1_.name as name6_1_0_,
        book1_.publisher_id as publishe7_1_0_ 
    from
        book_review_info bookreview0_ 
    left outer join
        book book1_ 
            on bookreview0_.book_id=book1_.id 
    where
        bookreview0_.book_id=?
BookReviewInfo(super=BaseEntity(cratedAt=2021-08-26T20:24:21.590, updatedAt=2021-08-26T20:24:21.590), id=1, book=Book(super=BaseEntity(cratedAt=2021-08-26T20:24:21.503, updatedAt=2021-08-26T20:24:21.503), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1), averageReviewScore=4.5, reviewCount=2)
Hibernate: 
    select
        bookreview0_.id as id1_2_0_,
        bookreview0_.created_at as created_2_2_0_,
        bookreview0_.updated_at as updated_3_2_0_,
        bookreview0_.average_review_score as average_4_2_0_,
        bookreview0_.book_id as book_id6_2_0_,
        bookreview0_.review_count as review_c5_2_0_,
        book1_.id as id1_1_1_,
        book1_.created_at as created_2_1_1_,
        book1_.updated_at as updated_3_1_1_,
        book1_.author_id as author_i4_1_1_,
        book1_.category as category5_1_1_,
        book1_.name as name6_1_1_,
        book1_.publisher_id as publishe7_1_1_ 
    from
        book_review_info bookreview0_ 
    left outer join
        book book1_ 
            on bookreview0_.book_id=book1_.id 
    where
        bookreview0_.id=?
Book(super=BaseEntity(cratedAt=2021-08-26T20:24:21.503, updatedAt=2021-08-26T20:24:21.503), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1)
Hibernate: 
    select
        book0_.id as id1_1_0_,
        book0_.created_at as created_2_1_0_,
        book0_.updated_at as updated_3_1_0_,
        book0_.author_id as author_i4_1_0_,
        book0_.category as category5_1_0_,
        book0_.name as name6_1_0_,
        book0_.publisher_id as publishe7_1_0_,
        bookreview1_.id as id1_2_1_,
        bookreview1_.created_at as created_2_2_1_,
        bookreview1_.updated_at as updated_3_2_1_,
        bookreview1_.average_review_score as average_4_2_1_,
        bookreview1_.book_id as book_id6_2_1_,
        bookreview1_.review_count as review_c5_2_1_ 
    from
        book book0_ 
    left outer join
        book_review_info bookreview1_ 
            on book0_.id=bookreview1_.book_id 
    where
        book0_.id=?
**BookReviewInfo(super=BaseEntity(cratedAt=2021-08-26T20:24:21.590, updatedAt=2021-08-26T20:24:21.590), id=1, book=Book(super=BaseEntity(cratedAt=2021-08-26T20:24:21.503, updatedAt=2021-08-26T20:24:21.503), id=1, name=Jpa 초격차 패키지, category=null, authorId=1, publisherId=1), averageReviewScore=4.5, reviewCount=2)**
```

DDL에서 Book에서는 book_review_info_id가 보이지 않지만, BookReviewInfo에서는 book_id가 보이고, left outer join도 2번에서 1번으로 줄었다

더불어서, Book에서 BookReviewInfo가 toString에서 제외되었고, BookReviewInfo를 Book으로부터 추출해낼 수 있음을 확인해볼 수 있다