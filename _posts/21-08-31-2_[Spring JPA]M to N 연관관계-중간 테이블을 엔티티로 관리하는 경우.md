# M:N 연관관계-중간 테이블을 엔티티로 관리하는 경우

![https://raw.githubusercontent.com/hy6219/TIL/main/Spring/JPA/Entity/RelationShip/OneToOne.png](https://raw.githubusercontent.com/hy6219/TIL/main/Spring/JPA/Entity/RelationShip/OneToOne.png)

[TIL/[Spring JPA]M to N 연관관계-중간 테이블을 엔티티로 관리하지 않는 경우.md at main · hy6219/TIL](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Entity/RelationShip/ManyToMany_RelationShip/%5BSpring%20JPA%5DM%20to%20N%20%EC%97%B0%EA%B4%80%EA%B4%80%EA%B3%84-%EC%A4%91%EA%B0%84%20%ED%85%8C%EC%9D%B4%EB%B8%94%EC%9D%84%20%EC%97%94%ED%8B%B0%ED%8B%B0%EB%A1%9C%20%EA%B4%80%EB%A6%AC%ED%95%98%EC%A7%80%20%EC%95%8A%EB%8A%94%20%EA%B2%BD%EC%9A%B0.md)

지난 시간에 살짝 언급했듯이, "ManyToMany"에서는 중간 테이블을 JoinColumn 등으로 해소할 수가 없다

이번 시간에 해보려고 하는 것을 예로 들자면 "주문 테이블"이 그 사례가 될 수 있을 것 같다

주문에서 ManyToMany관계에 해당되는 것은 바로 "사용자"와 "상품"간의 관계인데

이렇게 되면 저번 시간에 살펴본 것처럼 user_product라는 중간테이블이 생겨나게 될 것이다

이 때, 이 "user_product"라는 테이블을 따로 "order"라는 엔티티 클래스로 관리하기도 하는데 , 이번에는 이렇게 `중간 테이블을 클래스로 관리하는 방식`을 접해보도록 할 것이다

## 01. 중간 테이블을 위한 클래스 "BookAndAuthor" 만들기

- BookAndAuthor: Book=N: 1
- BookAndAuthor: Author=N:1

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
@EqualsAndHashCode(callSuper = true)
public class BookAndAuthor extends BaseEntity{

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    private Book book;

    @ManyToOne
    private Author author;

}
```

## 02. 01 과정에 따라서, Book과 BookAndAuthor, BookAndAuthor 와 Author관계 성립시켜주기

이제는 중간 브릿지로써 "BookAndAuthor"가 존재해서

- Book:BookAndAuthor=1:N
- Author:BookAndAuthor=1:N

으로 양측을 정리해줄 필요가 있다

그렇게 되면 `@JoinColumn(name="book-id")` 로 중간테이블과 연결될 수 있다!

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
import java.util.Collection;
import java.util.Collections;
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

    //@ManyToMany
    **@OneToMany
    @JoinColumn(name="book_id")
    @ToString.Exclude
    private List<BookAndAuthor> bookAndAuthors=new ArrayList<>();**

//    public void addAuthor(Author...author){
//        Collections.addAll(this.authors,author);
//    }

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
import java.util.Collection;
import java.util.Collections;
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

  //  @ManyToMany
    **@OneToMany
    @JoinColumn(name="author_id")
    @ToString.Exclude
    private List<BookAndAuthor> bookAndAuthors=new ArrayList<>();**

//    public void addBook(Book...book){
//        Collections.addAll(this.books,book);
//    }

}
```

## 03. BookAndAuthor(중간 테이블)에 대한 레포지토리 생성

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.BookAndAuthor;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookAndAuthorRepository extends JpaRepository<BookAndAuthor,Long> {
}
```

## 04. 테스트 코드에서 연관관계를 맺는 부분을 수정

03까지 진행되었다면, 연관관계를 맺는 부분을 수정해줄 필요가 있다

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Author;
import com.example.jpa_entity.domain.Book;
import com.example.jpa_entity.domain.BookAndAuthor;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import javax.transaction.Transactional;
import java.util.ArrayList;
import java.util.Arrays;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class AuthorRepositoryTest {

    @Autowired
    private AuthorRepository authorRepository;

    @Autowired
    private BookRepository bookRepository;

    @Autowired
    private BookAndAuthorRepository bookAndAuthorRepository;

    private Book givenBook(String name){
        Book book=new Book();
        book.setName(name);

        return bookRepository.save(book);
    }

    private Author givenAuthor(String name){
        Author author=new Author();

        author.setName(name);
        return authorRepository.save(author);
    }

    private BookAndAuthor givenBookAndAuthor(Book book, Author author){
        BookAndAuthor ba=new BookAndAuthor();
        ba.setBook(book);
        ba.setAuthor(author);

        return bookAndAuthorRepository.save(ba);
    }

    @Test
    @Transactional
    public void manyToManyTest(){
        Book book1=givenBook("책1");
        Book book2=givenBook("책2");
        Book book3=givenBook("개발책1");
        Book book4=givenBook("개발책2");

        Author author1=givenAuthor("martin");
        Author author2=givenAuthor("steve");

        //중간테이블 엔티티
        BookAndAuthor ba1=givenBookAndAuthor(book1,author1);
        BookAndAuthor ba2=givenBookAndAuthor(book2,author2);
        BookAndAuthor ba3=givenBookAndAuthor(book3,author1);
        BookAndAuthor ba4=givenBookAndAuthor(book3,author2);
        BookAndAuthor ba5=givenBookAndAuthor(book4,author1);
        BookAndAuthor ba6=givenBookAndAuthor(book4,author2);

        book1.setBookAndAuthors(Lists.newArrayList(ba1));
        book2.setBookAndAuthors(Lists.newArrayList(ba2));
        book3.setBookAndAuthors(Lists.newArrayList(ba3,ba4));
        book4.setBookAndAuthors(Lists.newArrayList(ba5,ba6));

        author1.setBookAndAuthors(Lists.newArrayList(ba1,ba3,ba5));
        author2.setBookAndAuthors(Lists.newArrayList(ba2,ba4,ba6));

        bookRepository.saveAll(Lists.newArrayList(book1,book2,book3,book4));
        authorRepository.saveAll(Lists.newArrayList(author1,author2));

        //연관관계 넣어주기
//        book1.addAuthor(author1);
//        book2.addAuthor(author2);
//        book3.addAuthor(author1,author2);
//        book4.addAuthor(author1,author2);
//
//        author1.addBook(book1,book3,book4);
//        author2.addBook(book2,book3,book4);

       // System.out.println("authors through book: "+bookRepository.findAll().get(2).getAuthors());
     //   System.out.println("books through author: "+authorRepository.findAll().get(0).getBooks());

        /*
        * 주문 테이블에서는 User와 Product가 M:N관계가 될 수 있을것
        * ->따라서 User,Product 테이블 외에도
        * 중간 테이블로써 user_products가 ddl에서 보이게 될 것
        * 그런데 이 중간 테이블을 또다른 엔티티인 "order테이블"로써
        * 따로 엔티티를 만들어줄 수도 있을 것
        * */

    }
}
```

이렇게 Book 객체와 Author 객체를 인자로 받아서 객체를 만들고 모두 저장을 해준 후에 확인을 해도 좋지만 보다 좋은 방식을 위해서 5의 방식을 도입해보자

## 05. 보다 현업에서 사용되는 방식으로 중간 엔티티 객체 관리하기

저번에 addXXX로 데이터를 추가하는 것에 대한 관리방법으로 중간 엔티티도 동일하게 접근해보자

```java
package com.example.jpa_entity.domain;

import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.Collection;
import java.util.Collections;
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

  //  @ManyToMany
    @OneToMany
    @JoinColumn(name="author_id")
    @ToString.Exclude
    private List<BookAndAuthor> bookAndAuthors=new ArrayList<>();

//    public void addBook(Book...book){
//        Collections.addAll(this.books,book);
//    }

    **public void addBookAndAuthor(BookAndAuthor...bookAndAuthor){
        Collections.addAll(this.bookAndAuthors,bookAndAuthor);
    }**
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
import java.util.ArrayList;
import java.util.Collection;
import java.util.Collections;
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

    //@ManyToMany
    @OneToMany
    @JoinColumn(name="book_id")
    @ToString.Exclude
    private List<BookAndAuthor> bookAndAuthors=new ArrayList<>();

//    public void addAuthor(Author...author){
//        Collections.addAll(this.authors,author);
//    }

    **public void addBookAndAuthor(BookAndAuthor...bookAndAuthor){
        Collections.addAll(this.bookAndAuthors,bookAndAuthor);
    }**

}
```

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Author;
import com.example.jpa_entity.domain.Book;
import com.example.jpa_entity.domain.BookAndAuthor;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import javax.transaction.Transactional;
import java.util.ArrayList;
import java.util.Arrays;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class AuthorRepositoryTest {

    @Autowired
    private AuthorRepository authorRepository;

    @Autowired
    private BookRepository bookRepository;

    @Autowired
    private BookAndAuthorRepository bookAndAuthorRepository;

    private Book givenBook(String name){
        Book book=new Book();
        book.setName(name);

        return bookRepository.save(book);
    }

    private Author givenAuthor(String name){
        Author author=new Author();

        author.setName(name);
        return authorRepository.save(author);
    }

    private BookAndAuthor givenBookAndAuthor(Book book, Author author){
        BookAndAuthor ba=new BookAndAuthor();
        ba.setBook(book);
        ba.setAuthor(author);

        return bookAndAuthorRepository.save(ba);
    }

    @Test
    @Transactional
    public void manyToManyTest(){
        Book book1=givenBook("책1");
        Book book2=givenBook("책2");
        Book book3=givenBook("개발책1");
        Book book4=givenBook("개발책2");

        Author author1=givenAuthor("martin");
        Author author2=givenAuthor("steve");

        //중간테이블 엔티티
        BookAndAuthor ba1=givenBookAndAuthor(book1,author1);
        BookAndAuthor ba2=givenBookAndAuthor(book2,author2);
        BookAndAuthor ba3=givenBookAndAuthor(book3,author1);
        BookAndAuthor ba4=givenBookAndAuthor(book3,author2);
        BookAndAuthor ba5=givenBookAndAuthor(book4,author1);
        BookAndAuthor ba6=givenBookAndAuthor(book4,author2);

        **book1.addBookAndAuthor(ba1);
        book2.addBookAndAuthor(ba2);
        book3.addBookAndAuthor(ba3,ba4);
        book4.addBookAndAuthor(ba5,ba6);

        author1.addBookAndAuthor(ba1,ba3,ba5);
        author2.addBookAndAuthor(ba2,ba4,ba6);**
//        book1.setBookAndAuthors(Lists.newArrayList(ba1));
//        book2.setBookAndAuthors(Lists.newArrayList(ba2));
//        book3.setBookAndAuthors(Lists.newArrayList(ba3,ba4));
//        book4.setBookAndAuthors(Lists.newArrayList(ba5,ba6));
//
//        author1.setBookAndAuthors(Lists.newArrayList(ba1,ba3,ba5));
//        author2.setBookAndAuthors(Lists.newArrayList(ba2,ba4,ba6));

        bookRepository.saveAll(Lists.newArrayList(book1,book2,book3,book4));
        authorRepository.saveAll(Lists.newArrayList(author1,author2));

				System.out.printf("get book and author info from book table--\n");
        bookRepository.findAll().get(2).getBookAndAuthors().forEach(item->{
            System.out.printf("book: %s, author: %s\n",item.getBook(),item.getAuthor());
        });

        //연관관계 넣어주기
//        book1.addAuthor(author1);
//        book2.addAuthor(author2);
//        book3.addAuthor(author1,author2);
//        book4.addAuthor(author1,author2);
//
//        author1.addBook(book1,book3,book4);
//        author2.addBook(book2,book3,book4);

       // System.out.println("authors through book: "+bookRepository.findAll().get(2).getAuthors());
     //   System.out.println("books through author: "+authorRepository.findAll().get(0).getBooks());

        /*
        * 주문 테이블에서는 User와 Product가 M:N관계가 될 수 있을것
        * ->따라서 User,Product 테이블 외에도
        * 중간 테이블로써 user_products가 ddl에서 보이게 될 것
        * 그런데 이 중간 테이블을 또다른 엔티티인 "order테이블"로써
        * 따로 엔티티를 만들어줄 수도 있을 것
        * */

    }
}
```

그러면 테스트코드는 더이상 setter로 일일히 추가해줄 필요가 없다

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
        book
        (id, created_at, updated_at, author_id, category, name, publisher_id) 
    values
        (null, ?, ?, ?, ?, ?, ?)
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
        book
        (id, created_at, updated_at, author_id, category, name, publisher_id) 
    values
        (null, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        author
        (id, created_at, updated_at, country, name) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        author
        (id, created_at, updated_at, country, name) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
get book and author info from book_and_author table--
Hibernate: 
    select
        book0_.id as id1_2_,
        book0_.created_at as created_2_2_,
        book0_.updated_at as updated_3_2_,
        book0_.author_id as author_i4_2_,
        book0_.category as category5_2_,
        book0_.name as name6_2_,
        book0_.publisher_id as publishe7_2_ 
    from
        book book0_
book: Book(super=BaseEntity(cratedAt=2021-08-31T16:45:33.133, updatedAt=2021-08-31T16:45:33.133), id=3, name=개발책1, category=null, authorId=null), author: Author(super=BaseEntity(cratedAt=2021-08-31T16:45:33.144, updatedAt=2021-08-31T16:45:33.144), id=1, name=martin, country=null)
book: Book(super=BaseEntity(cratedAt=2021-08-31T16:45:33.133, updatedAt=2021-08-31T16:45:33.133), id=3, name=개발책1, category=null, authorId=null), author: Author(super=BaseEntity(cratedAt=2021-08-31T16:45:33.150, updatedAt=2021-08-31T16:45:33.150), id=2, name=steve, country=null)
```

지금은 book3에 대해서 접근하기 때문에, 책 이름은 당연히 같고

저자는 지금 author1과 author2가 들어가 있는 것을 확인해볼 수 있다

마찬가지로, Author 테이블에서도 동일과정으로 작업을 진행해볼 수 있다

이번에는 author1로 접근해서, 저자는 같지만 book1,book3,book4의 정보를 확인해볼 수 있게 된다

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.Author;
import com.example.jpa_entity.domain.Book;
import com.example.jpa_entity.domain.BookAndAuthor;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import javax.transaction.Transactional;
import java.util.ArrayList;
import java.util.Arrays;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class AuthorRepositoryTest {

    @Autowired
    private AuthorRepository authorRepository;

    @Autowired
    private BookRepository bookRepository;

    @Autowired
    private BookAndAuthorRepository bookAndAuthorRepository;

    private Book givenBook(String name){
        Book book=new Book();
        book.setName(name);

        return bookRepository.save(book);
    }

    private Author givenAuthor(String name){
        Author author=new Author();

        author.setName(name);
        return authorRepository.save(author);
    }

    private BookAndAuthor givenBookAndAuthor(Book book, Author author){
        BookAndAuthor ba=new BookAndAuthor();
        ba.setBook(book);
        ba.setAuthor(author);

        return bookAndAuthorRepository.save(ba);
    }

    @Test
    @Transactional
    public void manyToManyTest(){
        Book book1=givenBook("책1");
        Book book2=givenBook("책2");
        Book book3=givenBook("개발책1");
        Book book4=givenBook("개발책2");

        Author author1=givenAuthor("martin");
        Author author2=givenAuthor("steve");

        //중간테이블 엔티티
        BookAndAuthor ba1=givenBookAndAuthor(book1,author1);
        BookAndAuthor ba2=givenBookAndAuthor(book2,author2);
        BookAndAuthor ba3=givenBookAndAuthor(book3,author1);
        BookAndAuthor ba4=givenBookAndAuthor(book3,author2);
        BookAndAuthor ba5=givenBookAndAuthor(book4,author1);
        BookAndAuthor ba6=givenBookAndAuthor(book4,author2);

        book1.addBookAndAuthor(ba1);
        book2.addBookAndAuthor(ba2);
        book3.addBookAndAuthor(ba3,ba4);
        book4.addBookAndAuthor(ba5,ba6);

        author1.addBookAndAuthor(ba1,ba3,ba5);
        author2.addBookAndAuthor(ba2,ba4,ba6);
//        book1.setBookAndAuthors(Lists.newArrayList(ba1));
//        book2.setBookAndAuthors(Lists.newArrayList(ba2));
//        book3.setBookAndAuthors(Lists.newArrayList(ba3,ba4));
//        book4.setBookAndAuthors(Lists.newArrayList(ba5,ba6));
//
//        author1.setBookAndAuthors(Lists.newArrayList(ba1,ba3,ba5));
//        author2.setBookAndAuthors(Lists.newArrayList(ba2,ba4,ba6));

        bookRepository.saveAll(Lists.newArrayList(book1,book2,book3,book4));
        authorRepository.saveAll(Lists.newArrayList(author1,author2));

        System.out.printf("get book and author info from book table--\n");
        bookRepository.findAll().get(2).getBookAndAuthors().forEach(item->{
            System.out.printf("book: %s, author: %s\n",item.getBook(),item.getAuthor());
        });

        System.out.printf("get book and author info from author table--\n");
        **authorRepository.findAll().get(0).getBookAndAuthors().forEach(item->{
            System.out.printf("book: %s, author: %s\n",item.getBook(),item.getAuthor());
        });**

        //연관관계 넣어주기
//        book1.addAuthor(author1);
//        book2.addAuthor(author2);
//        book3.addAuthor(author1,author2);
//        book4.addAuthor(author1,author2);
//
//        author1.addBook(book1,book3,book4);
//        author2.addBook(book2,book3,book4);

       // System.out.println("authors through book: "+bookRepository.findAll().get(2).getAuthors());
     //   System.out.println("books through author: "+authorRepository.findAll().get(0).getBooks());

        /*
        * 주문 테이블에서는 User와 Product가 M:N관계가 될 수 있을것
        * ->따라서 User,Product 테이블 외에도
        * 중간 테이블로써 user_products가 ddl에서 보이게 될 것
        * 그런데 이 중간 테이블을 또다른 엔티티인 "order테이블"로써
        * 따로 엔티티를 만들어줄 수도 있을 것
        * */

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
        book
        (id, created_at, updated_at, author_id, category, name, publisher_id) 
    values
        (null, ?, ?, ?, ?, ?, ?)
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
        book
        (id, created_at, updated_at, author_id, category, name, publisher_id) 
    values
        (null, ?, ?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        author
        (id, created_at, updated_at, country, name) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        author
        (id, created_at, updated_at, country, name) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        book_and_author
        (id, created_at, updated_at, author_id, book_id) 
    values
        (null, ?, ?, ?, ?)
get book and author info from book table--
Hibernate: 
    select
        book0_.id as id1_2_,
        book0_.created_at as created_2_2_,
        book0_.updated_at as updated_3_2_,
        book0_.author_id as author_i4_2_,
        book0_.category as category5_2_,
        book0_.name as name6_2_,
        book0_.publisher_id as publishe7_2_ 
    from
        book book0_
book: Book(super=BaseEntity(cratedAt=2021-08-31T16:50:51.854, updatedAt=2021-08-31T16:50:51.854), id=3, name=개발책1, category=null, authorId=null), author: Author(super=BaseEntity(cratedAt=2021-08-31T16:50:51.864, updatedAt=2021-08-31T16:50:51.864), id=1, name=martin, country=null)
book: Book(super=BaseEntity(cratedAt=2021-08-31T16:50:51.854, updatedAt=2021-08-31T16:50:51.854), id=3, name=개발책1, category=null, authorId=null), author: Author(super=BaseEntity(cratedAt=2021-08-31T16:50:51.872, updatedAt=2021-08-31T16:50:51.872), id=2, name=steve, country=null)
get book and author info from author table--
Hibernate: 
    select
        author0_.id as id1_1_,
        author0_.created_at as created_2_1_,
        author0_.updated_at as updated_3_1_,
        author0_.country as country4_1_,
        author0_.name as name5_1_ 
    from
        author author0_
**book: Book(super=BaseEntity(cratedAt=2021-08-31T16:50:51.762, updatedAt=2021-08-31T16:50:51.762), id=1, name=책1, category=null, authorId=null), author: Author(super=BaseEntity(cratedAt=2021-08-31T16:50:51.864, updatedAt=2021-08-31T16:50:51.864), id=1, name=martin, country=null)
book: Book(super=BaseEntity(cratedAt=2021-08-31T16:50:51.854, updatedAt=2021-08-31T16:50:51.854), id=3, name=개발책1, category=null, authorId=null), author: Author(super=BaseEntity(cratedAt=2021-08-31T16:50:51.864, updatedAt=2021-08-31T16:50:51.864), id=1, name=martin, country=null)
book: Book(super=BaseEntity(cratedAt=2021-08-31T16:50:51.856, updatedAt=2021-08-31T16:50:51.856), id=4, name=개발책2, category=null, authorId=null), author: Author(super=BaseEntity(cratedAt=2021-08-31T16:50:51.864, updatedAt=2021-08-31T16:50:51.864), id=1, name=martin, country=null)**
```

그리고 DDL을 살펴보면, 중간 테이블에 이전에 author_id와 book_id에 붙었던 not null이 없어졌고,

created_at, updated_at, pk가 보이는 것을 확인해볼 수 있었다

```java
Hibernate: 
    
    create table author (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        country varchar(255),
        name varchar(255),
        primary key (id)
    )
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
    
    **create table book_and_author (
       id bigint generated by default as identity,
        created_at timestamp,
        updated_at timestamp,
        author_id bigint,
        book_id bigint,
        primary key (id)
    )**
```