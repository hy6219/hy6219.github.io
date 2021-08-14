# 쿼리메소드 정의 및 실습1

계속해서 지난 시간의 프로젝트를 이어나가도록 하자!

이번에는 기본적으로  상속을 통해 제공되는 메서드들 외에 직접 커스텀해보도록 하자

🌟인텔리제이를 이용하게 되면 메서드명을 기본적인 컨벤션(CrudRepository등 참조)에 따라 지으면 추천되어 자동완성목록이 뜬다

## 01. name 속성에 따라 조회하는 쿼리 메서드 만들기

### 01-1. 도메인 리턴타입으로 된 findByName 메서드

먼저 name 필드에 대해서 조회하는 메서드인 `findByName` 메서드를 UserRepository에 만들어주자

이의 반환타입은 User로 해주자

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

public interface UserRepository extends JpaRepository<User,Long> {

    **User findByName(String name);**
}
```

이번에는 UserRepositoryTest에 가서 직접 테스트해보자

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Example;
import org.springframework.data.domain.ExampleMatcher;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

import java.util.List;

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.contains;
import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        User user=userRepository.findByName("steve");
        System.out.println(user);
    }

}
```

그러면 위와 같이 존재하지 않는 name값으로 검색하면 null이 표시되고, 쿼리는 where 조건에 name으로 확인하는 것을 볼 수 있다

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
        user0_.name=?
**null**
```

그리고 아래처럼 특정 name값 "martin"으로 된 행이 2개인데, 이 경우 조회를 시도하면, 쿼리는 당연히 동일 구조를 보이지만 , 지금은 martin이라는 값이 2개 행으로 조회되어 에러를 보인다

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Example;
import org.springframework.data.domain.ExampleMatcher;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

import java.util.List;

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.contains;
import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        User user=userRepository.findByName("martin");
        System.out.println(user);
    }

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
        user0_.name=?

query did not return a unique result: 2; nested exception is javax.persistence.NonUniqueResultException: query did not return a unique result: 2
org.springframework.dao.IncorrectResultSizeDataAccessException: query did not return a unique result: 2; nested exception is javax.persistence.NonUniqueResultException: query did not return a unique result: 2
(생략)
```

아래는 data.sql파일이다

```java
call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`) values (1, 'martin', 'martin@fastcampus.com', now(), now());

call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`) values (2, 'dennis', 'dennis@fastcampus.com', now(), now());

call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`) values (3, 'sophia', 'sophia@slowcampus.com', now(), now());

call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`) values (4, 'james', 'james@slowcampus.com', now(), now());

call next value for hibernate_sequence;
insert into user (`id`, `name`, `email`, `created_at`, `updated_at`) values (5, 'martin', 'martin@another.com', now(), now());
```

그리고 "james"처럼 name값에 대한 행이 단 1개인 경우 아래처럼 한 개행만 조회되는 것을 확인해볼 수 있다

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Example;
import org.springframework.data.domain.ExampleMatcher;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

import java.util.List;

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.contains;
import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        User user=userRepository.findByName("james");
        System.out.println(user);
    }

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
        user0_.name=?
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-14 19:39:09.943, updatedAt=2021-08-14 19:39:09.943)
```

### 01-2. List<도메인> 반환타입의 메서드 findByName

그러면 , 앞서 "martin"의 경우는 List로 받아주면 문제가 없겠다!

만들어보자!

생각해보면, User가 들어있는 List이므로 List<User>로 만들어주면 되겠다!

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

import java.util.List;

public interface UserRepository extends JpaRepository<User,Long> {

    //User findByName(String name);
    **List<User> findByName(String name);**
}
```

그러면 이제는 name값으로 조회되는 레코드 갯수가 여러개여도 에러도 안나고 확인할 수 있다

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
        user0_.name=?
[User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-14 19:43:02.317, updatedAt=2021-08-14 19:43:02.317), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-14 19:43:02.349, updatedAt=2021-08-14 19:43:02.349)]
```

### 01-3.findById처럼 Optional 반환타입으로 된 메서드 findByName

이번에는 findById처럼 null이더라도 에러를 발생시키지 않고 비어있다고 알려주는 문구를 띄워주는 Optional을 생각해보자

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Optional;

public interface UserRepository extends JpaRepository<User,Long> {

    //User findByName(String name);
   // List<User> findByName(String name);
    Optional<User> findByName(String name);
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
        user0_.name=?
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 19:57:46.432, updatedAt=2021-08-14 19:57:46.432)
```

그러면 위와 같이 존재하면 해당 레코드를 가져오지만

martin과 같은 경우에는 2개 이상의 레코드인데 List<Optional<도메인>> 혹은 Optional<List<도메인>>등으로 변환하는 것은 불가능하기 때문에 2개 이상이 발견되었다는 문구를 확인해볼 수 있을 것이다

[Optional > for Empty List in Repository Layer Returns Optional.empty . How do I change it to return Optional[[]]](https://stackoverflow.com/questions/52185052/optionallistt-for-empty-list-in-repository-layer-returns-optional-empty-ho)

### 01-4. Set<도메인>으로 된 반환타입 메서드 findByName

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Optional;
import java.util.Set;

public interface UserRepository extends JpaRepository<User,Long> {

    //User findByName(String name);
   // List<User> findByName(String name);
  //  Optional<User> findByName(String name);
    **Set<User> findByName(String name);**
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
        user0_.name=?
[User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-14 20:02:49.3, updatedAt=2021-08-14 20:02:49.3), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-14 20:02:49.32, updatedAt=2021-08-14 20:02:49.32)]
```

그러면 Set은 집합과 같은 구조이기 때문에 중복되지 않은 두 martin에 대한 레코드 값들이 고스란히 담겨지는 것을 확인해볼 수 있다

🌟 즉, 쿼리메서드에서는 리턴타입을 고정시켜서 사용해야 하고!

🌟 레코드가 1개 혹은 N개 존재하는 지에 따라 개발자가 정해준 리턴타입에 맞춰서 반환해주는데 그 과정(런타임 중)에서 에러가 발생할 수도 있다(스프링 데이터 JPA에서 적절히 랩핑해주는 것)

### 🌟 스프링에서 제공되는 쿼리 메서드 네이밍 Supported query method subject keywords

[Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#appendix.query.method.subject)

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/JPA%20Repository/%EC%8A%A4%ED%94%84%EB%A7%81%EC%97%90%EC%84%9C%20%EC%A0%9C%EA%B3%B5%EB%90%98%EB%8A%94%20%EC%BF%BC%EB%A6%AC%EB%A9%94%EC%84%9C%EB%93%9C%20naming.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/JPA%20Repository/%EC%8A%A4%ED%94%84%EB%A7%81%EC%97%90%EC%84%9C%20%EC%A0%9C%EA%B3%B5%EB%90%98%EB%8A%94%20%EC%BF%BC%EB%A6%AC%EB%A9%94%EC%84%9C%EB%93%9C%20naming.PNG?raw=true)

공식문서에서 확인해보면,

- `find..By`, `read...By`, `get...By`, `query...By`, `search...By`, `stream...By` 의 경우는 전형적인 repository 타입, 컬렉션 혹은 Streamable 서브타입 혹은 Page와 같은 래퍼를 반환해주는 일반적인 쿼리 메서드로 사용[이중에서 일반적으로 `find...By` 를 가장 많이 사용한다]
- `exists...By` 는 프로젝션, 즉 열단위로 검색해서 존재하면 boolean 타입으로 반환
- `count...By` 는 열 단위로 카운트하여 numeric 결과를 반환
- `delete...By`, `remove...By` 는 void 타입이든지 delete된 갯수로 반환이든지 삭제 쿼리 메서드 결과로써 제공
- `...First<number>...`, `...Top<number>...` 는 처음 조회된 결과로 제한
- `...Distinct...` 는 중복되지 않도록 결과를 보여줌

이라고 되어 있는 것을 확인해볼 수 있다

find...By의 경우,  findUserBy 등 네이밍해주어도 되지만 지금 UserRepository를 보면

```java
public interface UserRepository extends **JpaRepository<User,Long>**
```

위와 같이 User 도메인을 명시해주었으므로 `findBy(User필드 관련-자동완성으로 확인가능)` 로 일반적으로 네이밍해주기도 한다!

🌟 List로 반환되는 목적일 경우에는 `findUsersByEmail` 과 같은 형태가 추천될 수 있다

🌟System.out.println은 인텔리제이에서 sout이라고 입력하면 나온다!

### 동일한 맥락에서 사용되는 `find,,,By`, `get...By` , `stream...By`, `read...By`, `query...By`, `search...By`

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Optional;
import java.util.Set;

public interface UserRepository extends JpaRepository<User,Long> {

    //List<User> findByName(String name);
    User findByEmail(String email);
    User getByEmail(String email);
    User readByEmail(String email);
    User queryByEmail(String email);
    User searchByEmail(String email);
    User streamByEmail(String email);
    List<User> findUsersByEmail(String email);
    List<User> findUsersByName(String name);
}
```

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Example;
import org.springframework.data.domain.ExampleMatcher;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

import java.util.List;
import java.util.Set;

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.contains;
import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        User user1= new User("david","david@fastcampus.com");
        User user2=new User("simons","simons@fastcampus.com");
        User userInOne=userRepository.findById(1L).orElseThrow(RuntimeException::new);
        userInOne.setEmail("update_email_martin@gmail.com");
        List<User> res=userRepository.saveAll(Lists.newArrayList(user1,user2,userInOne));
        System.out.println("inserted&updated result: "+res);
    }

    @Test
    public void select(){
        System.out.println("===");
        System.out.println("findByEmail: "+userRepository.findByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("findUsersByEmail: "+userRepository.findUsersByEmail("martin@fastcampus.com"));
        System.out.println("===");
        System.out.println("findUsersByName: "+userRepository.findUsersByName("martin"));
        System.out.println("===");
        System.out.println("searchByEmail: "+userRepository.searchByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("streamByEmail: "+userRepository.streamByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("getByEmail: "+userRepository.getByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("queryByEmail: "+userRepository.queryByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("readByEmail: "+userRepository.readByEmail("dennis@fastcampus.com"));
        System.out.println("===");
    }

}
```

```java
===
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
        user0_.email=?
findByEmail: User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 22:40:33.335, updatedAt=2021-08-14 22:40:33.335)
===
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
        user0_.email=?
findUsersByEmail: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-14 22:40:33.323, updatedAt=2021-08-14 22:40:33.323)]
===
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
        user0_.name=?
findUsersByName: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-14 22:40:33.323, updatedAt=2021-08-14 22:40:33.323), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-14 22:40:33.336, updatedAt=2021-08-14 22:40:33.336)]
===
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
        user0_.email=?
searchByEmail: User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 22:40:33.335, updatedAt=2021-08-14 22:40:33.335)
===
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
        user0_.email=?
streamByEmail: User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 22:40:33.335, updatedAt=2021-08-14 22:40:33.335)
===
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
        user0_.email=?
getByEmail: User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 22:40:33.335, updatedAt=2021-08-14 22:40:33.335)
===
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
        user0_.email=?
queryByEmail: User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 22:40:33.335, updatedAt=2021-08-14 22:40:33.335)
===
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
        user0_.email=?
readByEmail: User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 22:40:33.335, updatedAt=2021-08-14 22:40:33.335)
===
```

먼저 Users나 User가 끼는 findUsersByEmail이나 findUsersByName의 경우는 기본적으로 List로 반환한다는 점을 확인해볼 수 있다

그리고 문서에서 소개한 것처럼 나머지 `find,,,By`, `get...By` , `stream...By`, `read...By`, `query...By`, `search...By` 는 동일하게 where절 등을 이용해서 조회하는 기능을 지원해주는 것을 확인해볼 수 있었다

따라서 단순 조회가 목적이라면, 이 중에서 코드 가독성이 높다고 판단한 메서드 명을 채택해서 사용하면 된다

그리고 아래와 같이 findSomethingByEmail처럼 find와 By 사이에 엔티티와 관련없는 이름이 들어가더라도 User에 대한 레포지토리이고, 반환형과 파라미터값이 User 와 관련있으므로, 전혀 문제없이 동일하게 작동한다

즉, find...By 형태에 맞기만 한다면, Something과 같은 부분은 구현체에서 무시가 되는 것이다! 이는 자유도가 제공되는 것을 의미하는데 , 이러한 만큼 잘못된 정보를 네이밍에 이용하지 않아야 한다는 경각심을 일깨워준다

```java
User findSomethingByEmail(String email);
```

```java
@Test
    public void somethingSelect(){
        System.out.println("findSomethingByEmail: "+userRepository.findSomethingByEmail("dennis@fastcampus.com"));
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
        user0_.email=?
findSomethingByEmail: User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 22:51:02.821, updatedAt=2021-08-14 22:51:02.821)
```

만약 `findByByName`이라고 실수로 메서드 명을 짓는다면 런타임 오류가 발생할 수 있다 ! 따라서 이러한 부분을 미연에 방지하기 위해서는 `사전에 이러한 쿼리 메서드들에 대해서는 사전 테스트를 단위/통합 테스트로 진행해야 좋다`

### `count...By` 와 함께 키워드들을 조합해서 사용해보기

[Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation)

이 문서는 쿼리에서 사용되는 예약어를 기반으로 작성된 키워드들을 나타낸 것이다(이음매라고 생각)

- Distinct : 중복 x
- And: 쿼리에서 AND
- Or : 쿼리에서 OR
- Is, Equals : =
- BETWEEN: BETWEEN A AND B
- LessThan: <
- LessThanEqual: ≤
- ...생략
- After: 날짜가 뒤의 날짜일때 !  x > ?
- Before: 날짜가 앞의 날짜일때 ! x < ?
- StartingWith : ~로 시작 (LIKE이용)
- EndsWith: ~로 끝(LIKE이용)
- Containing : ~를 포함(LIKE이용)
- IgnoreCase: 대소문자 구분 x (대문자로 모두 변형해서 비교) 
-where UPPER(x.firstname) = UPPER(?1)

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

import java.util.Date;
import java.util.List;
import java.util.Optional;
import java.util.Set;

public interface UserRepository extends JpaRepository<User,Long> {

    //List<User> findByName(String name);
    User findByEmail(String email);
    User getByEmail(String email);
    User readByEmail(String email);
    User queryByEmail(String email);
    User searchByEmail(String email);
    User streamByEmail(String email);
    List<User> findUsersByEmail(String email);
    List<User> findUsersByName(String name);
    User findSomethingByEmail(String email);

    **long countByName(String name);
    long countByNameAndEmail(String name, String email);
    long countByCreatedAtAfter(Date createdAt);**

}
```

문서에서 예시에 나온것과 유사하게 네이밍해서 사용해보았다

먼저 이름으로 조회되는 행 수를 카운트하고

이름과 이메일로 조회된 결과의 행수, 특정 일자 이후의 행 수를 카운트하였다

```java
@Test
    public void selectWithKeywords() throws ParseException {
        SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd");
        String createdAt="2021-08-01";
        System.out.println("---");
        System.out.println("countUsersByName: "+userRepository.countByName("martin"));
        System.out.println("---");
        System.out.println("countByCreatedAtAfter: "+userRepository.countByCreatedAtAfter(sf.parse(createdAt)));
        System.out.println("---");
        System.out.println("countByNameAndEmail: "+userRepository.countByNameAndEmail("martin","martin@fastcampus.com"));

    }
```

```java
---
Hibernate: 
    select
        count(user0_.id) as col_0_0_ 
    from
        user user0_ 
    where
        user0_.name=?
countUsersByName: 2
---
Hibernate: 
    select
        count(user0_.id) as col_0_0_ 
    from
        user user0_ 
    where
        user0_.created_at>?
countByCreatedAtAfter: 5
---
Hibernate: 
    select
        count(user0_.id) as col_0_0_ 
    from
        user user0_ 
    where
        user0_.name=? 
        and user0_.email=?
countByNameAndEmail: 1
```

그 결과, 위와 같은 쿼리에서 각 조건이 적절하게 조합되고, COUNT 함수로 조건에 맞는 행수가 확인된 것을 볼 수 있다

### `delete..By`, `remove...By` 네이밍

- 일반적으로 JpaRepository인터페이스에서 제공되는 삭제 메서드를 많이 이용

`...First<숫자>...`와  `...Top<숫자>...`

둘 다 동일하게 처음 발견한 것 포함 몇개까지 보여줄지를 결정하는 것이다!

다만, 선택은 각 팀별 선택의 자율성에 맡긴 네이밍!

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

import java.util.Date;
import java.util.List;
import java.util.Optional;
import java.util.Set;

public interface UserRepository extends JpaRepository<User,Long> {

    //List<User> findByName(String name);
    User findByEmail(String email);
    User getByEmail(String email);
    User readByEmail(String email);
    User queryByEmail(String email);
    User searchByEmail(String email);
    User streamByEmail(String email);
    List<User> findUsersByEmail(String email);
    List<User> findUsersByName(String name);
    User findSomethingByEmail(String email);

    long countByName(String name);
    long countByNameAndEmail(String name, String email);
    long countByCreatedAtAfter(Date createdAt);

    **List<User> findFirst2UsersByName(String name);
    List<User> findTop1ByName(String name);**
}
```

```java
@Test
    public void selectWithKeywords() throws ParseException {
        SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd");
        String createdAt="2021-08-01";
        System.out.println("---");
        System.out.println("countUsersByName: "+userRepository.countByName("martin"));
        System.out.println("---");
        System.out.println("countByCreatedAtAfter: "+userRepository.countByCreatedAtAfter(sf.parse(createdAt)));
        System.out.println("---");
        System.out.println("countByNameAndEmail: "+userRepository.countByNameAndEmail("martin","martin@fastcampus.com"));
        System.out.println("---");
        **System.out.println("findFirst2UsersByName: "+userRepository.findFirst2UsersByName("martin"));
        System.out.println("---");
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));**

    }
```

```java
(생략)
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
        user0_.name=? limit ?
findFirst2UsersByName: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-14 23:29:50.413, updatedAt=2021-08-14 23:29:50.413), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-14 23:29:50.435, updatedAt=2021-08-14 23:29:50.435)]
---
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
        user0_.name=? limit ?
findTop1ByName: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-14 23:29:50.413, updatedAt=2021-08-14 23:29:50.413)]
```

그 결과 먼저 첫번째는 martin이 2개 행을 갖는데 2개를 모두 가져와서 보여지게 되고, 두번째는 그 두개 중 한개만을 택해서 가져와서 보여주는 것을 확인할 수 있다

그리고 만약 Top1과 같이 한개만 가져온다면, List보다 User로 받는 것이 보다 더 나은 선택일 수 있다

다만 First2와 같이 2개이상을 가져오면 List로 받는 것이 보다 바람직하다

### `EndingWith`, `StartingWith`, `Containing` 패턴

- 위에서 언급한 것과 같이 ~로 끝나거나/시작하거나/포함시키는 패턴!

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

import java.util.Date;
import java.util.List;
import java.util.Optional;
import java.util.Set;

public interface UserRepository extends JpaRepository<User,Long> {

    //List<User> findByName(String name);
    User findByEmail(String email);
    User getByEmail(String email);
    User readByEmail(String email);
    User queryByEmail(String email);
    User searchByEmail(String email);
    User streamByEmail(String email);
    List<User> findUsersByEmail(String email);
    List<User> findUsersByName(String name);
    User findSomethingByEmail(String email);

    long countByName(String name);
    long countByNameAndEmail(String name, String email);
    long countByCreatedAtAfter(Date createdAt);

    List<User> findFirst2UsersByName(String name);
    List<User> findTop1ByName(String name);

    **List<User> findAllByEmailEndingWith(String pattern);
    List<User> findAllByNameStartingWith(String pattern);
    List<User> findAllByEmailContaining(String pattern);**
}
```

```java
@Test
    public void likeMatcher(){
        System.out.println("findAllByEmailEndingWith: "+userRepository.findAllByEmailEndingWith("slowcampus.com"));
        System.out.println("findAllByNameStartingWith: "+userRepository.findAllByNameStartingWith("ma"));
        System.out.println("findAllByEmailContaining: "+userRepository.findAllByEmailContaining("campus"));
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
        user0_.email like ? escape ?
findAllByEmailEndingWith: [User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-14 23:40:21.619, updatedAt=2021-08-14 23:40:21.619), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-14 23:40:21.623, updatedAt=2021-08-14 23:40:21.623)]
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
        user0_.name like ? escape ?
findAllByNameStartingWith: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-14 23:40:21.601, updatedAt=2021-08-14 23:40:21.601), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-14 23:40:21.624, updatedAt=2021-08-14 23:40:21.624)]
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
        user0_.email like ? escape ?
findAllByEmailContaining: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-14 23:40:21.601, updatedAt=2021-08-14 23:40:21.601), User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 23:40:21.618, updatedAt=2021-08-14 23:40:21.618), User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-14 23:40:21.619, updatedAt=2021-08-14 23:40:21.619), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-14 23:40:21.623, updatedAt=2021-08-14 23:40:21.623)]
```

먼저 EndingWith의 경우 ExampleMatcher에서처럼, slowcampus.com으로 끝나는 이메일이 포함된 모든 행을 찾아서 보여주고

StartingWith는 ma로 시작하는 이름이 포함된 모든 행을 보여준다

그리고 Containing은 campus가 포함된 이메일이 존재하는 모든 행을 찾아서 보여줌을 확인해볼 수 있다

### `Last<number>` 도 될까? ▶️ 존재하지 않는다!!

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

import java.util.Date;
import java.util.List;
import java.util.Optional;
import java.util.Set;

public interface UserRepository extends JpaRepository<User,Long> {

    //List<User> findByName(String name);
    User findByEmail(String email);
    User getByEmail(String email);
    User readByEmail(String email);
    User queryByEmail(String email);
    User searchByEmail(String email);
    User streamByEmail(String email);
    List<User> findUsersByEmail(String email);
    List<User> findUsersByName(String name);
    User findSomethingByEmail(String email);

    long countByName(String name);
    long countByNameAndEmail(String name, String email);
    long countByCreatedAtAfter(Date createdAt);

    List<User> findFirst2UsersByName(String name);
    List<User> findTop1ByName(String name);

    List<User> findAllByEmailEndingWith(String pattern);
    List<User> findAllByNameStartingWith(String pattern);
    List<User> findAllByEmailContaining(String pattern);

    **User findLast1ByName(String name);**
}
```

끝에서부터 찾는 경우도 가능할까?

Last를 넣어서 시도해보았다

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Example;
import org.springframework.data.domain.ExampleMatcher;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.List;
import java.util.Set;

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.contains;
import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        User user1= new User("david","david@fastcampus.com");
        User user2=new User("simons","simons@fastcampus.com");
        User userInOne=userRepository.findById(1L).orElseThrow(RuntimeException::new);
        userInOne.setEmail("update_email_martin@gmail.com");
        List<User> res=userRepository.saveAll(Lists.newArrayList(user1,user2,userInOne));
        System.out.println("inserted&updated result: "+res);
    }

    @Test
    public void select(){
        System.out.println("===");
        System.out.println("findByEmail: "+userRepository.findByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("findUsersByEmail: "+userRepository.findUsersByEmail("martin@fastcampus.com"));
        System.out.println("===");
        System.out.println("findUsersByName: "+userRepository.findUsersByName("martin"));
        System.out.println("===");
        System.out.println("searchByEmail: "+userRepository.searchByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("streamByEmail: "+userRepository.streamByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("getByEmail: "+userRepository.getByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("queryByEmail: "+userRepository.queryByEmail("dennis@fastcampus.com"));
        System.out.println("===");
        System.out.println("readByEmail: "+userRepository.readByEmail("dennis@fastcampus.com"));
        System.out.println("===");
    }

    @Test
    public void somethingSelect(){
        System.out.println("findSomethingByEmail: "+userRepository.findSomethingByEmail("dennis@fastcampus.com"));
    }

    @Test
    public void selectWithKeywords() throws ParseException {
        SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd");
        String createdAt="2021-08-01";
        System.out.println("---");
        System.out.println("countUsersByName: "+userRepository.countByName("martin"));
        System.out.println("---");
        System.out.println("countByCreatedAtAfter: "+userRepository.countByCreatedAtAfter(sf.parse(createdAt)));
        System.out.println("---");
        System.out.println("countByNameAndEmail: "+userRepository.countByNameAndEmail("martin","martin@fastcampus.com"));
        System.out.println("---");
        System.out.println("findFirst2UsersByName: "+userRepository.findFirst2UsersByName("martin"));
        System.out.println("---");
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
        System.out.println("---");
        System.out.println("findLast1ByName: "+userRepository.findLast1ByName("martin"));

    }

    @Test
    public void likeMatcher(){
        System.out.println("findAllByEmailEndingWith: "+userRepository.findAllByEmailEndingWith("slowcampus.com"));
        System.out.println("findAllByNameStartingWith: "+userRepository.findAllByNameStartingWith("ma"));
        System.out.println("findAllByEmailContaining: "+userRepository.findAllByEmailContaining("campus"));
    }
```

그 결과 아래와 같이 2개의 행이 조회된다는 오류가 나온다

즉, Last라는 키워드는 존재하지 않음을 확인해볼 수 있다

그리고 이는 쿼리에서 limit이 없는 것으로도 확인해볼 수 있다

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
        user0_.name=?
query did not return a unique result: 2; nested exception is javax.persistence.NonUniqueResultException: query did not return a unique result: 2
```

이런 Last 키워드는 존재하지 않지만, 나중에 ORDER BY와 접목해서 역순으로 정렬 후 First혹은 Top으로 접근할 수 있다