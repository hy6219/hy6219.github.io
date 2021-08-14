# Repository Interface 메서드 실습(2)

## flush()메서드

flush를 느끼기 위해서 먼저 User를 저장하고, flush 후 findAll을 해보자

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureTestEntityManager;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
      // User user =userRepository.getOne(1L);
        userRepository.save(new User("new Martin","newMartin@fastcampus.com"));
        **userRepository.flush();**
        userRepository.findAll().forEach(System.out::println);
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
        (created_at, email, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_0_,
        user0_.created_at as created_2_0_,
        user0_.email as email3_0_,
        user0_.name as name4_0_,
        user0_.updated_at as updated_5_0_ 
    from
        user user0_
User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-13 13:15:21.823, updatedAt=2021-08-13 13:15:21.823)
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-13 13:15:21.835, updatedAt=2021-08-13 13:15:21.835)
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-13 13:15:21.835, updatedAt=2021-08-13 13:15:21.835)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-13 13:15:21.835, updatedAt=2021-08-13 13:15:21.835)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-13 13:15:21.835, updatedAt=2021-08-13 13:15:21.835)
User(id=6, name=new Martin, email=newMartin@fastcampus.com, createdAt=null, updatedAt=null)
```

그러면 쿼리 상으로는 별다른 차이점은 없는 것처럼 보인다

## saveAndFlush() 메서드

- save+flush 메서드를 섞어둔 메서드

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureTestEntityManager;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
      // User user =userRepository.getOne(1L);
        **userRepository.saveAndFlush(new User("new Martin","newMartin@fastcampus.com"));**
        userRepository.findAll().forEach(System.out::println);
    }

}
```

이 역시도 결과는 위와 동일했다

▶️ 그러면 flush는 어떤 기능을 하는 것일까? 쿼리에 변화를 주는 것 같지도 않고?

👉 DB 반영 시점을 조절하는 것이기 때문에 쿼리에 변화가 없는 것처럼 보이는 것! 따라서 쿼리 변화가 없어 보이는 것이다!

## count()메서드

- long 형태로 반환

```java
SELECT COUNT(ID)
FROM   USER;
```

위와 같이 전체 레코드 수를 확인할 수 있는 것처럼 사용된다!

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureTestEntityManager;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
      // User user =userRepository.getOne(1L);
        **long cnt= userRepository.count();**
        System.out.println(cnt);
    }

}
```

지금은 data.sql에 5개 행만이 저장되어 있기 때문에 "5"가 출력되는 것을 확인해볼 수 있다

```java
Hibernate: 
    select
        count(*) as col_0_0_ 
    from
        user user0_
5
```

## existsById(ID값)

- boolean 형태로 리턴
- 고유값으로 된 레코드가 존재하는 지 확인

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureTestEntityManager;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
      // User user =userRepository.getOne(1L);
        **boolean flag= userRepository.existsById(1L);**
        System.out.println("id 1 exists?: "+flag);
    }

}
```

```java
Hibernate: 
    select
        count(*) as col_0_0_ 
    from
        user user0_ 
    **where
        user0_.id=?**
id 1 exists?: true
```

결과를 보니, COUNT함수 조회 결과가 1, 1개행이 있다고 판명되면 true를 반환시키는 것으로 파악된다(위의 count()와 다르게, 고유값에 대한 조건이 달린것이 특징이다)

## delete(Entity)

- 엔티티를 삭제

아이디가 2L인 엔티티를 삭제할 것인데, `userRepository.findById` 는 `optional`을 리턴해주기 때문에 `orElse(null)`을 붙여서 엔티티로 변환해줌과 동시에 null인 경우 null이 표시되도록 해주고, 이를 delete의 인자값으로 넣어주자

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureTestEntityManager;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
      // User user =userRepository.getOne(1L);
        **userRepository.delete(userRepository.findById(2L).orElse(null));**
        userRepository.findAll().forEach(System.out::println);
    }

}
```

그러면 아래와 같은 쿼리가 추가된 것을 확인해볼 수 있다

- findById는 📌 모양으로 쿼리를 확인
- delete에서 수행된 쿼리는 🌻 로 확인

이렇게, delete는 삭제 전 아이디를 탐색하는 부분이 한 번 실행하는 것을 확인해볼 수 있다

더불어 아이디가 2인 레코드가 제거된 것도 확인할 수 있다

🌟 하지만, delete 메서드 설명을 보면 아래처럼 null인 엔티티가 주어져서는 안된다고 보여주고 있다! 그래서 null이 들어가면 문제가 생길 것이다!

따라서 orElse보다 orElseThrow 를 이용해주는 것이 좋을 것 같다

> Deletes a given entity.
Params:
entity – must not be null.

Throws:
IllegalArgumentException – in case the given entity is null.

```java
DELETE FROM USER
WHERE ID=?
```

```java
📌 Hibernate: 
    select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?📌 
🌻**Hibernate: 
    select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?**
**Hibernate: 
    delete 
    from
        user 
    where
        id=?**🌻
Hibernate: 
    select
        user0_.id as id1_0_,
        user0_.created_at as created_2_0_,
        user0_.email as email3_0_,
        user0_.name as name4_0_,
        user0_.updated_at as updated_5_0_ 
    from
        user user0_
User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-13 13:34:49.544, updatedAt=2021-08-13 13:34:49.544)
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-13 13:34:49.558, updatedAt=2021-08-13 13:34:49.558)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-13 13:34:49.558, updatedAt=2021-08-13 13:34:49.558)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-13 13:34:49.558, updatedAt=2021-08-13 13:34:49.558)
```

그리고 앞서 말했던 것처럼 orElseThrow를 이용해서 진행해보자

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureTestEntityManager;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
      // User user =userRepository.getOne(1L);
        **userRepository.delete(userRepository.findById(2L).orElseThrow(()->new RuntimeException()));**
        //람다식을 도입하면 아래처럼도 가능!
        //userRepository.delete(userRepository.findById(2L).orElseThrow((RuntimeException::new));
        userRepository.findAll().forEach(System.out::println);
    }

}
```

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureTestEntityManager;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
      // User user =userRepository.getOne(1L);
        **userRepository.delete(userRepository.findById(7L).orElseThrow(()->new RuntimeException()));**
        //람다식을 도입하면 아래처럼도 가능!
        //**userRepository.delete(userRepository.findById(2L).orElseThrow((RuntimeException::new));**
        userRepository.findAll().forEach(System.out::println);
    }

}
```

먼저 첫번째처럼, 아이디가 1인 경우와 같이 `존재하는 레코드에 대해서는 orElse와 동일하게 에러없이 조회 후 삭제가 가능`하다 

하지만, 두번째처럼 아이디가 7인 경우와 같이 `존재하지 않는 레코드에 대해서는 orElse와 다!르!게! 매개변수로 전달해준 RuntimeException을 발생` 시키는 것을 확인해볼 수 있다!

그에 대한 확인은 아래처럼 id를 이용해서 select하는 쿼리는 실행되었으나, 그 결과가 orElseThrow로 에러가 발생했기 때문에 그 이후에 예외 부분이 띄워지는 것을 확인해볼 수 있다

```java
Hibernate: 
    select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?

java.lang.RuntimeException
(생략)
```

## deleteById(ID id)

- 아이디 값으로 레코드 삭제

```java
package com.example.jpa_repository_interface.repository;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
     **userRepository.deleteById(3L);**
    }

}
```

```java
Hibernate: 
    select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    delete 
    from
        user 
    where
        id=?
```

deleteById를 실행해봤을때, 기본적인 동작은 delete와 동일하게 한번 select 후 delete한다는 점이 확인되었다

하지만, delete는 파라미터가 엔티티로 들어오고, deleteById는 아이디값으로 접근한다는 점은 다르다

하지만! 아이디로 삭제한다는 점을 고려하면 오히려 deleteById가 작성에 용이할 수도 있다

```java
package com.example.jpa_repository_interface.repository;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
     userRepository.deleteById(10L);
    }

}
```

위와 같이 존재하지 않는 아이디를 넣어주면 어떨까?

```java
Hibernate: 
    select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?

No class com.example.jpa_repository_interface.domain.User entity with id 10 exists!
org.springframework.dao.EmptyResultDataAccessException: No class com.example.jpa_repository_interface.domain.User entity with id 10 exists!
(생략)
```

아래처럼 아이디가 10인 엔티티가 없다는 에러로 EmptyResultDataAccessException을 띄운다

🌟 delete와 deleteById에서 select가 실행되는 것은 실행 전에 그 레코드가 존재하는지 확인하는 차원에서 진행되는 것 같다

## deleteAll()과 deleteAllInBatch()

### deleteAll()

- 테이블 전체를 `레코드 하나씩 접근해서 삭제`
- findAll을 한번 수행해주고, 레코드를 하나씩 접근해서 삭제

처음에 직접 실행시켜보고, 데이터베이스 로그를 확인해보기 전에는 아래처럼

```java
DELETE FROM USER;
```

그냥 쿼리 한줄로 전체 삭제가 실행될 것이라고 예상했었다

하지만 예상과 다르게, 아래처럼 실행해봤을때 deleteAll은 기본키값마다 하나하나 접근해서 삭제하고 있었다

```java
DELETE FROM USER
WHERE ID=?
```

```java
package com.example.jpa_repository_interface.repository;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
     **userRepository.deleteAll();**
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
Hibernate: 
    delete 
    from
        user 
    where
        id=?
Hibernate: 
    delete 
    from
        user 
    where
        id=?
Hibernate: 
    delete 
    from
        user 
    where
        id=?
Hibernate: 
    delete 
    from
        user 
    where
        id=?
Hibernate: 
    delete 
    from
        user 
    where
        id=?
```

### deleteAll(Iterable)을 이용해서 `특정 고유값들`에 해당되는 레코드 삭제

`void deleteAll(Iterable<? extends T> entities)`

- iterable 인자값에 대응되는 레코드들만 삭제

인자값으로 findAllById(Iterable)을 넣어서 전달해보자

- findAllById ▶️ 🌷
- deleteAll ▶️ 🌹
- findAll ▶️ 🌺

그러면, 아래에서 알 수 있듯이, `deleteAll(Void)` 와 다르게 `deleteAll(Iterable)`은 `Iterable 길이 만큼 select 조회 수행 후 delete를 수행`하는 것을 확인해볼 수 있다!

```java
package com.example.jpa_repository_interface.repository;

import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
     userRepository.deleteAll(userRepository.findAllById(Lists.newArrayList(1L,3l,5l)));
     userRepository.findAll().forEach(System.out::println);
    }

}
```

```java
🌷Hibernate: 
    select
        user0_.id as id1_0_,
        user0_.created_at as created_2_0_,
        user0_.email as email3_0_,
        user0_.name as name4_0_,
        user0_.updated_at as updated_5_0_ 
    from
        user user0_ 
    where
        user0_.id in (
            ? , ? , ?
        )🌷
🌹Hibernate: 
    select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    delete 
    from
        user 
    where
        id=?
Hibernate: 
    delete 
    from
        user 
    where
        id=?
Hibernate: 
    delete 
    from
        user 
    where
        id=?🌹
🌺Hibernate: 
    select
        user0_.id as id1_0_,
        user0_.created_at as created_2_0_,
        user0_.email as email3_0_,
        user0_.name as name4_0_,
        user0_.updated_at as updated_5_0_ 
    from
        user user0_🌺
//findAll까지 실행된 결과
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-13 14:24:06.327, updatedAt=2021-08-13 14:24:06.327)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-13 14:24:06.329, updatedAt=2021-08-13 14:24:06.329)
```

- 이렇게 쿼리상으로 findAll을 불러오는 deleteAll은 부하가 많아질 수 있어서 실제로는 권장되지 않는다!

### deleteAllInBatch()

- `Batch` 라는 말의 뜻처럼 `일괄 삭제` 하는 의미가 강한 메서드!
- jsp servlet에서 executeBatch()처럼 `쿼리에 값을 대응시켜서 하나하나 적재시켜두었다가 한번에 실행` 하는 의미가 있는 삭제 메서드
- `deleteAll에서 발생 가능한 findAll 호출로 인한 과부하를 해소시키는 데에 도움을 줄 수 있는 메서드`

위에서 예상했던 `DELETE FROM USER` 가 이에 해당된다!

```java
package com.example.jpa_repository_interface.repository;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
     **userRepository.deleteAllInBatch();**
    }

}
```

```java
Hibernate: 
    delete 
    from
        user
```

### void deleteAllByIdInBatch(Iterable<ID> ids)

- delete 를 이용하면서 + where ~ in을 이용해서 한번에 특정 아이디값들에 해당되는 레코드를 삭제

```java
package com.example.jpa_repository_interface.repository;

import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        **userRepository.deleteAllByIdInBatch(Lists.newArrayList(1L,2L,3L));**
    }

}
```

```java
Hibernate: 
 **   delete 
    from
        user 
    where
        id in (
            ? , ? , ?
        )**
```

---

## 🌟페이징 Paging🌟

- JPA에서 지원하는 매우 편리한 기능!!😻
- Page<T> 클래스가 지원되고 있다!

**org.springframework.data.domain.Page** 패키지에서 지원된다!

1) Page<T> page= repository(about T).findAll(Pageable pageable)

- Pageable로 접근해서 가져올 수 있는 모든 결과를 조회

2) PageRequest(Pageable의 구현체).of(int page, int size)

- 페이지 번호는 0부터 시작
- page 에 해당되는 페이지를 가져옴
- size는 한 페이지 당 보여지는 게시글 갯수

이 외에도 다양한 메서드가 존재

- Sort 혹은 Sort.Direction으로 정렬 방향을 정해줄 수도 있고
- String...props를 통해 어떤 컬럼들을 보여줄 지 정해줄 수도 있음

[[# JPA] Pageable](https://catchdream.tistory.com/181)

```java
/*
 * Copyright 2008-2021 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
package org.springframework.data.domain;

import org.springframework.data.domain.Sort.Direction;
import org.springframework.lang.Nullable;
import org.springframework.util.Assert;

/**
 * Basic Java Bean implementation of {@link Pageable}.
 *
 * @author Oliver Gierke
 * @author Thomas Darimont
 * @author Anastasiia Smirnova
 * @author Mark Paluch
 */
public class PageRequest extends AbstractPageRequest {

	private static final long serialVersionUID = -4541509938956089562L;

	private final Sort sort;

	/**
	 * Creates a new {@link PageRequest} with sort parameters applied.
	 *
	 * @param page zero-based page index, must not be negative.
	 * @param size the size of the page to be returned, must be greater than 0.
	 * @param sort must not be {@literal null}, use {@link Sort#unsorted()} instead.
	 */
	protected PageRequest(int page, int size, Sort sort) {

		super(page, size);

		Assert.notNull(sort, "Sort must not be null!");

		this.sort = sort;
	}

	/**
	 * Creates a new unsorted {@link PageRequest}.
	 *
	 * @param page zero-based page index, must not be negative.
	 * @param size the size of the page to be returned, must be greater than 0.
	 * @since 2.0
	 */
	public static PageRequest of(int page, int size) {
		return of(page, size, Sort.unsorted());
	}

	/**
	 * Creates a new {@link PageRequest} with sort parameters applied.
	 *
	 * @param page zero-based page index.
	 * @param size the size of the page to be returned.
	 * @param sort must not be {@literal null}, use {@link Sort#unsorted()} instead.
	 * @since 2.0
	 */
	public static PageRequest of(int page, int size, Sort sort) {
		return new PageRequest(page, size, sort);
	}

	/**
	 * Creates a new {@link PageRequest} with sort direction and properties applied.
	 *
	 * @param page zero-based page index, must not be negative.
	 * @param size the size of the page to be returned, must be greater than 0.
	 * @param direction must not be {@literal null}.
	 * @param properties must not be {@literal null}.
	 * @since 2.0
	 */
	public static PageRequest of(int page, int size, Direction direction, String... properties) {
		return of(page, size, Sort.by(direction, properties));
	}

	/**
	 * Creates a new {@link PageRequest} for the first page (page number {@code 0}) given {@code pageSize} .
	 *
	 * @param pageSize the size of the page to be returned, must be greater than 0.
	 * @return a new {@link PageRequest}.
	 * @since 2.5
	 */
	public static PageRequest ofSize(int pageSize) {
		return PageRequest.of(0, pageSize);
	}

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.domain.Pageable#getSort()
	 */
	public Sort getSort() {
		return sort;
	}

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.domain.Pageable#next()
	 */
	@Override
	public PageRequest next() {
		return new PageRequest(getPageNumber() + 1, getPageSize(), getSort());
	}

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.domain.AbstractPageRequest#previous()
	 */
	@Override
	public PageRequest previous() {
		return getPageNumber() == 0 ? this : new PageRequest(getPageNumber() - 1, getPageSize(), getSort());
	}

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.domain.Pageable#first()
	 */
	@Override
	public PageRequest first() {
		return new PageRequest(0, getPageSize(), getSort());
	}

	/*
	 * (non-Javadoc)
	 * @see java.lang.Object#equals(java.lang.Object)
	 */
	@Override
	public boolean equals(@Nullable Object obj) {

		if (this == obj) {
			return true;
		}

		if (!(obj instanceof PageRequest)) {
			return false;
		}

		PageRequest that = (PageRequest) obj;

		return super.equals(that) && this.sort.equals(that.sort);
	}

	/**
	 * Creates a new {@link PageRequest} with {@code pageNumber} applied.
	 *
	 * @param pageNumber
	 * @return a new {@link PageRequest}.
	 * @since 2.5
	 */
	@Override
	public PageRequest withPage(int pageNumber) {
		return new PageRequest(pageNumber, getPageSize(), getSort());
	}

	/**
	 * Creates a new {@link PageRequest} with {@link Direction} and {@code properties} applied.
	 *
	 * @param direction must not be {@literal null}.
	 * @param properties must not be {@literal null}.
	 * @return a new {@link PageRequest}.
	 * @since 2.5
	 */
	public PageRequest withSort(Direction direction, String... properties) {
		return new PageRequest(getPageNumber(), getPageSize(), Sort.by(direction, properties));
	}

	/**
	 * Creates a new {@link PageRequest} with {@link Sort} applied.
	 *
	 * @param sort must not be {@literal null}.
	 * @return a new {@link PageRequest}.
	 * @since 2.5
	 */
	public PageRequest withSort(Sort sort) {
		return new PageRequest(getPageNumber(), getPageSize(), sort);
	}

	/*
	 * (non-Javadoc)
	 * @see java.lang.Object#hashCode()
	 */
	@Override
	public int hashCode() {
		return 31 * super.hashCode() + sort.hashCode();
	}

	/*
	 * (non-Javadoc)
	 * @see java.lang.Object#toString()
	 */
	@Override
	public String toString() {
		return String.format("Page request [number: %d, size %d, sort: %s]", getPageNumber(), getPageSize(), sort);
	}

}
```

먼저 간단하게 위에서 살펴본 Pageable의 구현체인 PageRequest의 of 메서드를 접근해서 출력해보자

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        Page<User> users=userRepository.findAll(PageRequest.of(1,3));
        users.forEach(System.out::println);
    }

}
```

```java
**Hibernate: 
    select
        user0_.id as id1_0_,
        user0_.created_at as created_2_0_,
        user0_.email as email3_0_,
        user0_.name as name4_0_,
        user0_.updated_at as updated_5_0_ 
    from
        user user0_ limit ? offset ?**
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-13 14:51:29.701, updatedAt=2021-08-13 14:51:29.701)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-13 14:51:29.701, updatedAt=2021-08-13 14:51:29.701)
```

그러면 위와 같이 LIMIT과 OFFSET이라는 부분이 들어가 있는 것을 확인해볼 수 있는데, 오라클 형식만 알고 있었던 나는 '페이징이니까 순번대로 줄을 세우고 그것을 끊어내는 것이겠지?' 라고 생각해보고 찾아보았더니 MySQL에서 사용하는 방법이라고 한다

그래서 LIMIT은 몇 개 행을 출력할 것인지를 나타내고, OFFSET은 몇 번째 row 부터 출력할 지를 나타내는 것이라고 한다

그러니 LIMIT은 size에 대응될 것같고 OFFSET은 size(page-1)+1에 대응될 것같다

(등차수열로 offset을 접근하였다)

LIMIT, OFFSET에 대한 부분은 아래에서 잠깐 볼 수 있다

[페이징 시 쿼리 LIMIT, OFFSET (my-sql)](https://itnewvom.tistory.com/21)

그리고 우리가 3개씩 보이도록 했기 때문에 지금은 두 번째 페이지(zero-based이기 때문)이므로 아이디가 4, 5에 해당되는 레코드가 확인되는 것을 볼 수 있다(나중에 뷰에서 보여질 때에는 +1을 해서 보여주어야 겠죠?)

💐Page<T>에서 지원되는 메서드

- getTotalElements : 모든 페이지를 통틀어서 존재하는 레코드 수 반환
- getTotalPages: size로 끊어지는 전체 페이지 수 반환
- getNumberOfElements: 현재 페이지의 레코드 수 반환
- getSort: 정렬 정보(정렬 방향 등)에 대해서 반환
- getSize : 한 페이지에 보여지는 컨텐츠 수를 반환
- getContent: 해당 페이지의 모든 컨텐츠에 접근

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        Page<User> users=userRepository.findAll(PageRequest.of(1,3));
        users.forEach(System.out::println);
        System.out.println("totalElements: "+users.getTotalElements());
        System.out.println("totalPages: "+users.getTotalPages());
        System.out.println("numberOfElements: "+users.getNumberOfElements());
        System.out.println("sort: "+users.getSort());
        System.out.println("size: "+ users.getSize());
        System.out.println("getContents--");

        users.getContent().forEach(System.out::println);
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
        user user0_ limit ? offset ?
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-13 15:20:01.915, updatedAt=2021-08-13 15:20:01.915)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-13 15:20:01.915, updatedAt=2021-08-13 15:20:01.915)
**totalElements: 5
totalPages: 2
numberOfElements: 2
sort: UNSORTED
size: 3
getContents--
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-13 15:20:01.915, updatedAt=2021-08-13 15:20:01.915)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-13 15:20:01.915, updatedAt=2021-08-13 15:20:01.915)**
```

---

## 📌JPA Repository를 상속받는 또다른 인터페이스인 `QueryByExampleExecutor`

- 흔히 "QBE"로 부르기도 함!
- 엔티티를 Example로 만들고, ExampleMatcher를 만들어줌으로써 필요한 쿼리들을 만드는 방법

### 📌Example=Probe+ExampleMatcher

[Example (Spring Data Core 2.5.4 API)](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/Example.html)

[](https://freedeveloper.tistory.com/158)

[Spring Data JPA - Query By Example](https://minkukjo.github.io/framework/2020/07/31/Spring-138/)

- Probe: 필드에 어떤 값들이 담기는 지를 의미하는, 즉 "도메인 객체"를 의미

(가짜 객체를 의미함)

- ExampleMatcher는 "일치 옵션 및 유형 안전성에 대해서 조정할 수 있도록" 한다고 나와 있는데,  이는 "Probe" 즉, **도메인 객체와 동일한 것만 가져올 수 잇도록 하는 것**을 의미한다

- of(T probe) : 기본적으로 null이 아닌 모든 속성을 포함하여 새로운 Example 생성[probe는 non null 이어야 함]
- of(T probe, ExampleMatcher matcher) : 주어진 ExampleMatcher에 대응되는 Example 생성[probe, matcher 모두 non null이어야 함]

```
static <T>Example<T> of(T probe,
ExampleMatcher matcher)
```

- T getProbe() : 사용된 Example 가져오기[반환형은 null일수없음]
- ExampleMatcher getMatcher() : 사용된 ExampleMatcher 가져오기[반환형은 null일수가 없음]
- default Class<T> getProbeType() : 사용된 probe의 실제 타입을 가져오기

[ProxyUtils.getUserClass(Class) 와 관련]

---

### 📌 ExampleMatcher

- 도메인 객체와 동일한 것만(ex: 속성)  가져오는 클래스

[ExampleMatcher (Spring Data Core 2.5.4 API)](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/ExampleMatcher.html)

- static ExampleMatcher matching() : null이 아닌 모든 속성들을 대상으로 쿼리를 생성
- ExampleMatcher withIgnorePaths(String...IgnoredPaths) : 명시된 속성(속성경로들)만 무시하고 구성된 ExampleMatcher를 복사하여 반환
- ExampleMatcher withMatcher(String propertyPath, ExampleMatcher.GenericPropertyMatcher genericPropertyMatcher): 속성에 대해 명시된 genericPropertyMatcher로 구성된 ExampleMatcher를 반환
- default ExampleMatcher	withMatcher(String propertyPath, ExampleMatcher.MatcherConfigurer<ExampleMatcher.GenericPropertyMatcher> matcherConfigurer)

: 위와 동일

:  ExampleMatcher에 포함시킬 것을 명시

:GenericPropertyMatcher-ENDING 은 끝나는 것, CONTAINS는 포함시키는 것

---

먼저 ExampleMatcher를 만들되, name을 제외시키고, email은 포함시키는 ExampleMatcher를 만들고, null이 아닌 모든 속성들을 대상으로 쿼리를 생성하도록 한다

```java
package com.example.jpa_repository_interface.repository;

import com.example.jpa_repository_interface.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.ExampleMatcher;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        **ExampleMatcher matcher=ExampleMatcher.matching()
                .withIgnorePaths("name")
                .withMatcher("email",endsWith());**
    }

}
```

그리고 Example을 만드는데, Example은 제네릭으로 되어있고, 우리는 User라는 도메인이 있기 때문에 제네릭으로는 User를 넣어주자

➕Example.of() 내부에는 Probe, 즉 가짜 객체가 들어가야 하는데(물론 우리가 알기로는 User임이 명확하지만) new User(name,email)로 넣어주자

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

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        ExampleMatcher matcher=ExampleMatcher.matching()
                .withIgnorePaths("name")
                .withMatcher("email",endsWith());

        **Example<User> example = Example.of(new User("jisoo","fastcampus.com"),matcher);**
    }

}
```

그러면 이제는 `findAll(Example)`로 전체조회에 대해서 접근할 수 있다

`<S extends T> List<S> findAll(Example<S> example);`

`<S extends T> List<S> findAll(Example<S> example, Sort sort);`

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

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        ExampleMatcher matcher=ExampleMatcher.matching()
                .withIgnorePaths("name")
                .withMatcher("email",endsWith());

        Example<User> example = Example.of(new User("jisoo","fastcampus.com"),matcher);

        **userRepository.findAll(example).forEach(System.out::println);**
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
        **user0_.email like ? escape ?**
User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-14 12:10:23.318, updatedAt=2021-08-14 12:10:23.318)
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 12:10:23.335, updatedAt=2021-08-14 12:10:23.335)
```

잠깐 data.sql을 보면서 생각해보자

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

분명히 여기에서는 "jisoo"로 시작되거나 해당 문자열이 포함된 케이스가 없었다!

하지만 **fastcampus.com로 끝나는 경우(`endsWith`)**는 "martin"과 "dennis"가 존재했다

그리고 결과를 통해서 `LIKE` 검색을 통해 📌 `name 속성은 매칭조건에서 제외(withIgnorePaths)시키고, email 속성은 매칭조건에 포함(withMatcher)시켜서 조회` 📌했음을 확인해볼 수 있었다!

즉, 어떻게 본다면 JPA와 그의 구현체 하이버네이트의 입장에서는 이렇게 해석하였던 것이다

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

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        ExampleMatcher matcher="matcher야 나는 required 속성으로 생성시키는
                                생성자 속성이 2개가 있지만, 
                                그 중 email만 이용해서 검사하도록 해줘";

        Example<User> example = Example.of(new User("jisoo","fastcampus.com"),matcher);

        userRepository.findAll(example).forEach(System.out::println);
    }

}
```

`만약 withIgnorePath가 없다면?`

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

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        ExampleMatcher matcher=ExampleMatcher.matching()
            //    .withIgnorePaths("name")
                .withMatcher("email",endsWith());

        Example<User> example = Example.of(new User("jisoo","fastcampus.com"),matcher);

        userRepository.findAll(example).forEach(System.out::println);
    }

}
```

실행 전 예상을 해본다면, 이제는 required constructor로 접근을 하는데, 제외시킬 속성이 없는데 jisoo가 들어간 경우는 없기 때문에 쿼리 외에는 빈 문자열만 확인될 것이다

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
        (
            user0_.email like ? escape ?
        ) 
        and user0_.name=?
```

예상과 동일했고, 이번에는 name조건(정확한 매칭 exact matching을 하게 됨! 그 이유는 endsWith등을 붙이지 않았기 때문!)에 대한 부분도 확인해볼 수 있었다

- endsWith 외에도 `startsWith`, `exact` 등이 있다

startsWith는 시작하는 것이고, exact는 정확하게 그 문자열이어야 하는 경우를 의미하고 있다

```java
/*
 * Copyright 2016-2021 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
package org.springframework.data.domain;

import java.util.Collection;
import java.util.LinkedHashMap;
import java.util.Map;
import java.util.Optional;
import java.util.Set;
import java.util.function.Function;

import org.springframework.lang.Nullable;
import org.springframework.util.Assert;
import org.springframework.util.ObjectUtils;

/**
 * Specification for property path matching to use in query by example (QBE). An {@link ExampleMatcher} can be created
 * for a {@link Class object type}. Instances of {@link ExampleMatcher} can be either {@link #matchingAll()} or
 * {@link #matchingAny()} and settings can be tuned {@code with...} methods in a fluent style. {@code with...} methods
 * return a copy of the {@link ExampleMatcher} instance with the specified setting. Null-handling defaults to
 * {@link NullHandler#IGNORE} and case-sensitive {@link StringMatcher#DEFAULT} string matching.
 * <p>
 * This class is immutable.
 *
 * @author Christoph Strobl
 * @author Mark Paluch
 * @author Oliver Gierke
 * @author Jens Schauder
 * @since 1.12
 */
public interface ExampleMatcher {

	/**
	 * Create a new {@link ExampleMatcher} including all non-null properties by default matching <strong>all</strong>
	 * predicates derived from the example.
	 *
	 * @return new instance of {@link ExampleMatcher}.
	 * @see #matchingAll()
	 */
	static ExampleMatcher matching() {
		return matchingAll();
	}

	/**
	 * Create a new {@link ExampleMatcher} including all non-null properties by default matching <strong>any</strong>
	 * predicate derived from the example.
	 *
	 * @return new instance of {@link ExampleMatcher}.
	 */
	static ExampleMatcher matchingAny() {
		return new TypedExampleMatcher().withMode(MatchMode.ANY);
	}

	/**
	 * Create a new {@link ExampleMatcher} including all non-null properties by default matching <strong>all</strong>
	 * predicates derived from the example.
	 *
	 * @return new instance of {@link ExampleMatcher}.
	 */
	static ExampleMatcher matchingAll() {
		return new TypedExampleMatcher().withMode(MatchMode.ALL);
	}

	/**
	 * Returns a copy of this {@link ExampleMatcher} with the specified {@code propertyPaths}. This instance is immutable
	 * and unaffected by this method call.
	 *
	 * @param ignoredPaths must not be {@literal null} and not empty.
	 * @return new instance of {@link ExampleMatcher}.
	 */
	ExampleMatcher withIgnorePaths(String... ignoredPaths);

	/**
	 * Returns a copy of this {@link ExampleMatcher} with the specified string matching of {@code defaultStringMatcher}.
	 * This instance is immutable and unaffected by this method call.
	 *
	 * @param defaultStringMatcher must not be {@literal null}.
	 * @return new instance of {@link ExampleMatcher}.
	 */
	ExampleMatcher withStringMatcher(StringMatcher defaultStringMatcher);

	/**
	 * Returns a copy of this {@link ExampleMatcher} with ignoring case sensitivity by default. This instance is immutable
	 * and unaffected by this method call.
	 *
	 * @return new instance of {@link ExampleMatcher}.
	 */
	default ExampleMatcher withIgnoreCase() {
		return withIgnoreCase(true);
	}

	/**
	 * Returns a copy of this {@link ExampleMatcher} with {@code defaultIgnoreCase}. This instance is immutable and
	 * unaffected by this method call.
	 *
	 * @param defaultIgnoreCase
	 * @return new instance of {@link ExampleMatcher}.
	 */
	ExampleMatcher withIgnoreCase(boolean defaultIgnoreCase);

	/**
	 * Returns a copy of this {@link ExampleMatcher} with the specified {@code GenericPropertyMatcher} for the
	 * {@code propertyPath}. This instance is immutable and unaffected by this method call.
	 *
	 * @param propertyPath must not be {@literal null}.
	 * @param matcherConfigurer callback to configure a {@link GenericPropertyMatcher}, must not be {@literal null}.
	 * @return new instance of {@link ExampleMatcher}.
	 */
	default ExampleMatcher withMatcher(String propertyPath, MatcherConfigurer<GenericPropertyMatcher> matcherConfigurer) {

		Assert.hasText(propertyPath, "PropertyPath must not be empty!");
		Assert.notNull(matcherConfigurer, "MatcherConfigurer must not be empty!");

		GenericPropertyMatcher genericPropertyMatcher = new GenericPropertyMatcher();
		matcherConfigurer.configureMatcher(genericPropertyMatcher);

		return withMatcher(propertyPath, genericPropertyMatcher);
	}

	/**
	 * Returns a copy of this {@link ExampleMatcher} with the specified {@code GenericPropertyMatcher} for the
	 * {@code propertyPath}. This instance is immutable and unaffected by this method call.
	 *
	 * @param propertyPath must not be {@literal null}.
	 * @param genericPropertyMatcher callback to configure a {@link GenericPropertyMatcher}, must not be {@literal null}.
	 * @return new instance of {@link ExampleMatcher}.
	 */
	ExampleMatcher withMatcher(String propertyPath, GenericPropertyMatcher genericPropertyMatcher);

	/**
	 * Returns a copy of this {@link ExampleMatcher} with the specified {@code PropertyValueTransformer} for the
	 * {@code propertyPath}.
	 *
	 * @param propertyPath must not be {@literal null}.
	 * @param propertyValueTransformer must not be {@literal null}.
	 * @return new instance of {@link ExampleMatcher}.
	 */
	ExampleMatcher withTransformer(String propertyPath, PropertyValueTransformer propertyValueTransformer);

	/**
	 * Returns a copy of this {@link ExampleMatcher} with ignore case sensitivity for the {@code propertyPaths}. This
	 * instance is immutable and unaffected by this method call.
	 *
	 * @param propertyPaths must not be {@literal null} and not empty.
	 * @return new instance of {@link ExampleMatcher}.
	 */
	ExampleMatcher withIgnoreCase(String... propertyPaths);

	/**
	 * Returns a copy of this {@link ExampleMatcher} with treatment for {@literal null} values of
	 * {@link NullHandler#INCLUDE} . This instance is immutable and unaffected by this method call.
	 *
	 * @return new instance of {@link ExampleMatcher}.
	 */
	default ExampleMatcher withIncludeNullValues() {
		return withNullHandler(NullHandler.INCLUDE);
	}

	/**
	 * Returns a copy of this {@link ExampleMatcher} with treatment for {@literal null} values of
	 * {@link NullHandler#IGNORE}. This instance is immutable and unaffected by this method call.
	 *
	 * @return new instance of {@link ExampleMatcher}.
	 */
	default ExampleMatcher withIgnoreNullValues() {
		return withNullHandler(NullHandler.IGNORE);
	}

	/**
	 * Returns a copy of this {@link ExampleMatcher} with the specified {@code nullHandler}. This instance is immutable
	 * and unaffected by this method call.
	 *
	 * @param nullHandler must not be {@literal null}.
	 * @return new instance of {@link ExampleMatcher}.
	 */
	ExampleMatcher withNullHandler(NullHandler nullHandler);

	/**
	 * Get defined null handling.
	 *
	 * @return never {@literal null}
	 */
	NullHandler getNullHandler();

	/**
	 * Get defined {@link ExampleMatcher.StringMatcher}.
	 *
	 * @return never {@literal null}.
	 */
	StringMatcher getDefaultStringMatcher();

	/**
	 * @return {@literal true} if {@link String} should be matched with ignore case option.
	 */
	boolean isIgnoreCaseEnabled();

	/**
	 * @param path must not be {@literal null}.
	 * @return return {@literal true} if path was set to be ignored.
	 */
	default boolean isIgnoredPath(String path) {
		return getIgnoredPaths().contains(path);
	}

	/**
	 * @return unmodifiable {@link Set} of ignored paths.
	 */
	Set<String> getIgnoredPaths();

	/**
	 * @return the {@link PropertySpecifiers} within the {@link ExampleMatcher}.
	 */
	PropertySpecifiers getPropertySpecifiers();

	/**
	 * Returns whether all of the predicates of the {@link Example} are supposed to match. If {@literal false} is
	 * returned, it's sufficient if any of the predicates derived from the {@link Example} match.
	 *
	 * @return whether all of the predicates of the {@link Example} are supposed to match or any of them is sufficient.
	 */
	default boolean isAllMatching() {
		return getMatchMode().equals(MatchMode.ALL);
	}

	/**
	 * Returns whether it's sufficient that any of the predicates of the {@link Example} match. If {@literal false} is
	 * returned, all predicates derived from the example need to match to produce results.
	 *
	 * @return whether it's sufficient that any of the predicates of the {@link Example} match or all need to match.
	 */
	default boolean isAnyMatching() {
		return getMatchMode().equals(MatchMode.ANY);
	}

	/**
	 * Get the match mode of the {@link ExampleMatcher}.
	 *
	 * @return never {@literal null}.
	 * @since 2.0
	 */
	MatchMode getMatchMode();

	/**
	 * Null handling for creating criterion out of an {@link Example}.
	 *
	 * @author Christoph Strobl
	 */
	enum NullHandler {

		INCLUDE, IGNORE
	}

	/**
	 * Callback to configure a matcher.
	 *
	 * @author Mark Paluch
	 * @param <T>
	 */
	interface MatcherConfigurer<T> {
		void configureMatcher(T matcher);
	}

	/**
	 * A generic property matcher that specifies {@link StringMatcher string matching} and case sensitivity.
	 *
	 * @author Mark Paluch
	 */
	class GenericPropertyMatcher {

		@Nullable StringMatcher stringMatcher = null;
		@Nullable Boolean ignoreCase = null;
		PropertyValueTransformer valueTransformer = NoOpPropertyValueTransformer.INSTANCE;

		/**
		 * Creates an unconfigured {@link GenericPropertyMatcher}.
		 */
		public GenericPropertyMatcher() {}

		/**
		 * Creates a new {@link GenericPropertyMatcher} with a {@link StringMatcher} and {@code ignoreCase}.
		 *
		 * @param stringMatcher must not be {@literal null}.
		 * @param ignoreCase
		 * @return
		 */
		public static GenericPropertyMatcher of(StringMatcher stringMatcher, boolean ignoreCase) {
			return new GenericPropertyMatcher().stringMatcher(stringMatcher).ignoreCase(ignoreCase);
		}

		/**
		 * Creates a new {@link GenericPropertyMatcher} with a {@link StringMatcher} and {@code ignoreCase}.
		 *
		 * @param stringMatcher must not be {@literal null}.
		 * @return
		 */
		public static GenericPropertyMatcher of(StringMatcher stringMatcher) {
			return new GenericPropertyMatcher().stringMatcher(stringMatcher);
		}

		/**
		 * Sets ignores case to {@literal true}.
		 *
		 * @return
		 */
		public GenericPropertyMatcher ignoreCase() {

			this.ignoreCase = true;
			return this;
		}

		/**
		 * Sets ignores case to {@code ignoreCase}.
		 *
		 * @param ignoreCase
		 * @return
		 */
		public GenericPropertyMatcher ignoreCase(boolean ignoreCase) {

			this.ignoreCase = ignoreCase;
			return this;
		}

		/**
		 * Sets ignores case to {@literal false}.
		 *
		 * @return
		 */
		public GenericPropertyMatcher caseSensitive() {

			this.ignoreCase = false;
			return this;
		}

		/**
		 * Sets string matcher to {@link StringMatcher#CONTAINING}.
		 *
		 * @return
		 */
		public GenericPropertyMatcher contains() {

			this.stringMatcher = StringMatcher.CONTAINING;
			return this;
		}

		/**
		 * Sets string matcher to {@link StringMatcher#ENDING}.
		 *
		 * @return
		 */
		public GenericPropertyMatcher endsWith() {

			this.stringMatcher = StringMatcher.ENDING;
			return this;
		}

		/**
		 * Sets string matcher to {@link StringMatcher#STARTING}.
		 *
		 * @return
		 */
		public GenericPropertyMatcher startsWith() {

			this.stringMatcher = StringMatcher.STARTING;
			return this;
		}

		/**
		 * Sets string matcher to {@link StringMatcher#EXACT}.
		 *
		 * @return
		 */
		public GenericPropertyMatcher exact() {

			this.stringMatcher = StringMatcher.EXACT;
			return this;
		}

		/**
		 * Sets string matcher to {@link StringMatcher#DEFAULT}.
		 *
		 * @return
		 */
		public GenericPropertyMatcher storeDefaultMatching() {

			this.stringMatcher = StringMatcher.DEFAULT;
			return this;
		}

		/**
		 * Sets string matcher to {@link StringMatcher#REGEX}.
		 *
		 * @return
		 */
		public GenericPropertyMatcher regex() {

			this.stringMatcher = StringMatcher.REGEX;
			return this;
		}

		/**
		 * Sets string matcher to {@code stringMatcher}.
		 *
		 * @param stringMatcher must not be {@literal null}.
		 * @return
		 */
		public GenericPropertyMatcher stringMatcher(StringMatcher stringMatcher) {

			Assert.notNull(stringMatcher, "StringMatcher must not be null!");
			this.stringMatcher = stringMatcher;
			return this;
		}

		/**
		 * Sets the {@link PropertyValueTransformer} to {@code propertyValueTransformer}.
		 *
		 * @param propertyValueTransformer must not be {@literal null}.
		 * @return
		 */
		public GenericPropertyMatcher transform(PropertyValueTransformer propertyValueTransformer) {

			Assert.notNull(propertyValueTransformer, "PropertyValueTransformer must not be null!");
			this.valueTransformer = propertyValueTransformer;
			return this;
		}

		protected boolean canEqual(final Object other) {
			return other instanceof GenericPropertyMatcher;
		}

		/*
		 * (non-Javadoc)
		 * @see java.lang.Object#equals(java.lang.Object)
		 */
		@Override
		public boolean equals(Object o) {

			if (this == o) {
				return true;
			}

			if (!(o instanceof GenericPropertyMatcher)) {
				return false;
			}

			GenericPropertyMatcher that = (GenericPropertyMatcher) o;

			if (stringMatcher != that.stringMatcher)
				return false;

			if (!ObjectUtils.nullSafeEquals(ignoreCase, that.ignoreCase)) {
				return false;
			}

			return ObjectUtils.nullSafeEquals(valueTransformer, that.valueTransformer);
		}

		/*
		 * (non-Javadoc)
		 * @see java.lang.Object#hashCode()
		 */
		@Override
		public int hashCode() {
			int result = ObjectUtils.nullSafeHashCode(stringMatcher);
			result = 31 * result + ObjectUtils.nullSafeHashCode(ignoreCase);
			result = 31 * result + ObjectUtils.nullSafeHashCode(valueTransformer);
			return result;
		}
	}

	/**
	 * Predefined property matchers to create a {@link GenericPropertyMatcher}.
	 *
	 * @author Mark Paluch
	 */
	class GenericPropertyMatchers {

		/**
		 * Creates a {@link GenericPropertyMatcher} that matches string case insensitive.
		 *
		 * @return
		 */
		public static GenericPropertyMatcher ignoreCase() {
			return new GenericPropertyMatcher().ignoreCase();
		}

		/**
		 * Creates a {@link GenericPropertyMatcher} that matches string case sensitive.
		 *
		 * @return
		 */
		public static GenericPropertyMatcher caseSensitive() {
			return new GenericPropertyMatcher().caseSensitive();
		}

		/**
		 * Creates a {@link GenericPropertyMatcher} that matches string using {@link StringMatcher#CONTAINING}.
		 *
		 * @return
		 */
		public static GenericPropertyMatcher contains() {
			return new GenericPropertyMatcher().contains();
		}

		/**
		 * Creates a {@link GenericPropertyMatcher} that matches string using {@link StringMatcher#ENDING}.
		 *
		 * @return
		 */
		public static GenericPropertyMatcher endsWith() {
			return new GenericPropertyMatcher().endsWith();

		}

		/**
		 * Creates a {@link GenericPropertyMatcher} that matches string using {@link StringMatcher#STARTING}.
		 *
		 * @return
		 */
		public static GenericPropertyMatcher startsWith() {
			return new GenericPropertyMatcher().startsWith();
		}

		/**
		 * Creates a {@link GenericPropertyMatcher} that matches string using {@link StringMatcher#EXACT}.
		 *
		 * @return
		 */
		public static GenericPropertyMatcher exact() {
			return new GenericPropertyMatcher().exact();
		}

		/**
		 * Creates a {@link GenericPropertyMatcher} that matches string using {@link StringMatcher#DEFAULT}.
		 *
		 * @return
		 */
		public static GenericPropertyMatcher storeDefaultMatching() {
			return new GenericPropertyMatcher().storeDefaultMatching();
		}

		/**
		 * Creates a {@link GenericPropertyMatcher} that matches string using {@link StringMatcher#REGEX}.
		 *
		 * @return
		 */
		public static GenericPropertyMatcher regex() {
			return new GenericPropertyMatcher().regex();
		}
	}

	/**
	 * Match modes for treatment of {@link String} values.
	 *
	 * @author Christoph Strobl
	 * @author Jens Schauder
	 */
	enum StringMatcher {

		/**
		 * Store specific default.
		 */
		DEFAULT,
		/**
		 * Matches the exact string
		 */
		EXACT,
		/**
		 * Matches string starting with pattern
		 */
		STARTING,
		/**
		 * Matches string ending with pattern
		 */
		ENDING,
		/**
		 * Matches string containing pattern
		 */
		CONTAINING,
		/**
		 * Treats strings as regular expression patterns
		 */
		REGEX;
	}

	/**
	 * Allows to transform the property value before it is used in the query.
	 */
	interface PropertyValueTransformer extends Function<Optional<Object>, Optional<Object>> {}

	/**
	 * @author Christoph Strobl
	 * @author Oliver Gierke
	 * @since 1.12
	 */
	enum NoOpPropertyValueTransformer implements ExampleMatcher.PropertyValueTransformer {

		INSTANCE;

		/*
		 * (non-Javadoc)
		 * @see java.util.function.Function#apply(java.lang.Object)
		 */
		@Override
		@SuppressWarnings("null")
		public Optional<Object> apply(Optional<Object> source) {
			return source;
		}
	}

	/**
	 * Define specific property handling for a Dot-Path.
	 *
	 * @author Christoph Strobl
	 * @author Mark Paluch
	 * @since 1.12
	 */
	class PropertySpecifier {

		private final String path;
		private final @Nullable StringMatcher stringMatcher;
		private final @Nullable Boolean ignoreCase;
		private final PropertyValueTransformer valueTransformer;

		/**
		 * Creates new {@link PropertySpecifier} for given path.
		 *
		 * @param path Dot-Path to the property. Must not be {@literal null}.
		 */
		PropertySpecifier(String path) {

			Assert.hasText(path, "Path must not be null/empty!");
			this.path = path;

			this.stringMatcher = null;
			this.ignoreCase = null;
			this.valueTransformer = NoOpPropertyValueTransformer.INSTANCE;
		}

		private PropertySpecifier(String path, @Nullable StringMatcher stringMatcher, @Nullable Boolean ignoreCase,
				PropertyValueTransformer valueTransformer) {
			this.path = path;
			this.stringMatcher = stringMatcher;
			this.ignoreCase = ignoreCase;
			this.valueTransformer = valueTransformer;
		}

		/**
		 * Creates a new {@link PropertySpecifier} containing all values from the current instance and sets
		 * {@link StringMatcher} in the returned instance.
		 *
		 * @param stringMatcher must not be {@literal null}.
		 * @return
		 */
		public PropertySpecifier withStringMatcher(StringMatcher stringMatcher) {

			Assert.notNull(stringMatcher, "StringMatcher must not be null!");
			return new PropertySpecifier(this.path, stringMatcher, this.ignoreCase, this.valueTransformer);
		}

		/**
		 * Creates a new {@link PropertySpecifier} containing all values from the current instance and sets
		 * {@code ignoreCase}.
		 *
		 * @param ignoreCase must not be {@literal null}.
		 * @return
		 */
		public PropertySpecifier withIgnoreCase(boolean ignoreCase) {
			return new PropertySpecifier(this.path, this.stringMatcher, ignoreCase, this.valueTransformer);
		}

		/**
		 * Creates a new {@link PropertySpecifier} containing all values from the current instance and sets
		 * {@link PropertyValueTransformer} in the returned instance.
		 *
		 * @param valueTransformer must not be {@literal null}.
		 * @return
		 */
		public PropertySpecifier withValueTransformer(PropertyValueTransformer valueTransformer) {

			Assert.notNull(valueTransformer, "PropertyValueTransformer must not be null!");
			return new PropertySpecifier(this.path, this.stringMatcher, this.ignoreCase, valueTransformer);
		}

		/**
		 * Get the properties Dot-Path.
		 *
		 * @return never {@literal null}.
		 */
		public String getPath() {
			return path;
		}

		/**
		 * Get the {@link StringMatcher}.
		 *
		 * @return can be {@literal null}.
		 */
		@Nullable
		public StringMatcher getStringMatcher() {
			return stringMatcher;
		}

		/**
		 * @return {@literal null} if not set.
		 */
		@Nullable
		public Boolean getIgnoreCase() {
			return ignoreCase;
		}

		/**
		 * Get the property transformer to be applied.
		 *
		 * @return never {@literal null}.
		 */
		public PropertyValueTransformer getPropertyValueTransformer() {
			return valueTransformer == null ? NoOpPropertyValueTransformer.INSTANCE : valueTransformer;
		}

		/**
		 * Transforms a given source using the {@link PropertyValueTransformer}.
		 *
		 * @param source
		 * @return
		 */
		public Optional<Object> transformValue(Optional<Object> source) {
			return getPropertyValueTransformer().apply(source);
		}

		/*
		 * (non-Javadoc)
		 * @see java.lang.Object#equals(java.lang.Object)
		 */
		@Override
		public boolean equals(Object o) {

			if (this == o) {
				return true;
			}

			if (!(o instanceof PropertySpecifier)) {
				return false;
			}

			PropertySpecifier that = (PropertySpecifier) o;

			if (!ObjectUtils.nullSafeEquals(path, that.path)) {
				return false;
			}

			if (stringMatcher != that.stringMatcher)
				return false;

			if (!ObjectUtils.nullSafeEquals(ignoreCase, that.ignoreCase)) {
				return false;
			}

			return ObjectUtils.nullSafeEquals(valueTransformer, that.valueTransformer);
		}

		/*
		 * (non-Javadoc)
		 * @see java.lang.Object#hashCode()
		 */
		@Override
		public int hashCode() {
			int result = ObjectUtils.nullSafeHashCode(path);
			result = 31 * result + ObjectUtils.nullSafeHashCode(stringMatcher);
			result = 31 * result + ObjectUtils.nullSafeHashCode(ignoreCase);
			result = 31 * result + ObjectUtils.nullSafeHashCode(valueTransformer);
			return result;
		}

		protected boolean canEqual(final Object other) {
			return other instanceof PropertySpecifier;
		}

	}

	/**
	 * Define specific property handling for Dot-Paths.
	 *
	 * @author Christoph Strobl
	 * @author Mark Paluch
	 * @since 1.12
	 */
	class PropertySpecifiers {

		private final Map<String, PropertySpecifier> propertySpecifiers = new LinkedHashMap<>();

		PropertySpecifiers() {}

		PropertySpecifiers(PropertySpecifiers propertySpecifiers) {
			this.propertySpecifiers.putAll(propertySpecifiers.propertySpecifiers);
		}

		public void add(PropertySpecifier specifier) {

			Assert.notNull(specifier, "PropertySpecifier must not be null!");
			propertySpecifiers.put(specifier.getPath(), specifier);
		}

		public boolean hasSpecifierForPath(String path) {
			return propertySpecifiers.containsKey(path);
		}

		public PropertySpecifier getForPath(String path) {
			return propertySpecifiers.get(path);
		}

		public boolean hasValues() {
			return !propertySpecifiers.isEmpty();
		}

		public Collection<PropertySpecifier> getSpecifiers() {
			return propertySpecifiers.values();
		}

		/*
		 * (non-Javadoc)
		 * @see java.lang.Object#equals(java.lang.Object)
		 */
		@Override
		public boolean equals(Object o) {

			if (this == o) {
				return true;
			}

			if (!(o instanceof PropertySpecifiers)) {
				return false;
			}

			PropertySpecifiers that = (PropertySpecifiers) o;
			return ObjectUtils.nullSafeEquals(propertySpecifiers, that.propertySpecifiers);
		}

		/*
		 * (non-Javadoc)
		 * @see java.lang.Object#hashCode()
		 */
		@Override
		public int hashCode() {
			return ObjectUtils.nullSafeHashCode(propertySpecifiers);
		}
	}

	/**
	 * The match modes to expose so that clients can find about how to concatenate the predicates.
	 *
	 * @author Oliver Gierke
	 * @since 1.13
	 * @see ExampleMatcher#isAllMatching()
	 */
	enum MatchMode {
		ALL, ANY;
	}
}
```

`만약, matcher가 아예 없다면?`

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

import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.endsWith;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void crud(){
        ExampleMatcher matcher=ExampleMatcher.matching()
            //    .withIgnorePaths("name")
                .withMatcher("email",endsWith());

        **Example<User> example = Example.of(new User("jisoo","fastcampus.com"));**

        userRepository.findAll(example).forEach(System.out::println);
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
        **user0_.name=? 
        and user0_.email=?**
```

ExampleMatcher가 없다면, 위와 같이 name과 email 모두에 대해서 `exact matching`을 하게 되는 것을 확인해볼 수 있다

따라서 위의 경우에는 jisoo, fastcampus.com인 경우가 없기 때문에 확인되는 결과가 쿼리 외에는 없다!

하지만, 실제로는 ignore시키는 경우보다 `기본 생성자를 만들고` `setter로 값을 채워준 후 Example에 matcher와 함께 전달` 하여 탐색하는 방식을 많이 사용한다

그리고 아직 이후에 학습할 부분과 관련되어 있지만 contains는 양방향 검색을 지원한다고 한다

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
        User user = new User();
        user.setEmail("slow");

        //contains는 양방향 LIKE검색
        ExampleMatcher matcher=ExampleMatcher.matching().withMatcher("email",contains());
        Example<User> example=Example.of(user,matcher);
        userRepository.findAll(example).forEach(System.out::println);
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
        user0_.email like ? escape ?
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-14 12:52:40.979, updatedAt=2021-08-14 12:52:40.979)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-14 12:52:40.979, updatedAt=2021-08-14 12:52:40.979)
```

위의 경우에는 contains로 slow가 `포함`된 경우만 조회하여 보여주는 것을 확인해볼 수 있다

➕검색이 필요한 인자를 Example 내부에 추가하여 생성해줄 수 있다! withIgnorePaths나 withMatcher를 사용하지 않고!

하지만 이 Example을 이용하는 방식은 String에 관련된 부분에만 적용할 수 있다는 점과 같은 한계가 있어서, 복잡한 쿼리를 만들 경우에는 QueryDSL 과 같은 별도의 방식을 구현하게 된다!