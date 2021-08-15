# 쿼리 메서드 활용-정렬

이번시간에는 OrderBy를 활용할 것인데, 이에 대해서 확인하고자 한다면 저번 기록을 참고해도 좋다!

[쿼리메서드 기본실습3](https://hy6219.github.io/TIL/Spring/JPA/JPA%20Repository/[Spring%20JPA]%EC%BF%BC%EB%A6%AC%EB%A9%94%EC%86%8C%EB%93%9C%20%EC%A0%95%EC%9D%98%20%EB%B0%8F%20%EC%8B%A4%EC%8A%B53.html#021ec601-5c20-4ab4-b718-0e525c5853fa)

## 01. (복습)Top<number>

- 앞에서 number번째까지 가져오기

```java
package com.example.jpa_sort_practice.repository;

import com.example.jpa_sort_practice.domain.User;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Date;
import java.util.List;

public interface UserRepository extends JpaRepository<User,Long> {

    List<User> findTop1ByName(String name);
}
```

```java
package com.example.jpa_sort_practice.repository;

import com.example.jpa_sort_practice.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void top(){
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
    }

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
        user0_.name=? limit ?
findTop1ByName: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 20:23:56.551, updatedAt=2021-08-15 20:23:56.551, active=true)]
```

위와 같이 원래 data.sql를 보면 martin은 총 2명인데,  그중 가장 먼저 조회된 아이디가 1인 martin이 limit을 통해서 조회된 것을 볼 수 있다!

## 02. `...Top<number>By"A"OrderBy"B"Desc/Asc`

- A에 대해서 인자값으로 들어온 값과 비교해서 조회한 결과를 이용해서
- B에 대해서 내림차순(역순;Desc) 혹은 오름차순(정순;Asc)으로 정렬한 후
- 그 결과에 대해서 위에서 number번째까지 포함하는 결과를 반환

```java
List<User> findTop1ByNameOrderByIdDesc(String name);
```

```java
System.out.println("findTop1ByNameOrderByIdDesc: "+userRepository.findTop1ByNameOrderByIdDesc("martin"));
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
        user0_.name=? 
    **order by
        user0_.id desc limit ?**
findTop1ByNameOrderByIdDesc: [User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 20:30:19.721, updatedAt=2021-08-15 20:30:19.721, active=true)]
```

이번에는 ORDER BY 절이 삽입되고, 아이디에 대해서 내림차순 정렬을 하는 것을 확인해볼 수 있는데 

SQL은 `FROM` > `WHERE` > `GROUP BY` > `HAVING` > `SELECT` > `ORDER BY` 순이기 때문에 `이름에 대한 조회 후` `아이디에 대해서 내림차순 정렬` 된 결과 중 첫 번째 결과를 확인해볼 수 있다

🌟Top이나 First 뒤에 number를 생략하면 기본값으로 1이 들어가는 것으로 간주된다!

## 03. 정렬에 기준 추가하기

이번에는 `ORDER BY A DESC, B ASC` 처럼 정렬 기준을 추가하는 것을 확인해보자! 해당 경우에는 A에 대해서 먼저 정렬하고, A값이 같으면 B에 대해서 정렬하는 것을 의미한다!

이 경우에는 `List<User> findFirstByNameOrderByIdDescEmailAsc(String name);`와 같이, `And로 잇지 않고 바로 이어서 적어주도록` 한다!

```java
List<User> findFirstByName**OrderByIdDescEmailAsc**(String name);
```

```java
package com.example.jpa_sort_practice.repository;

import com.example.jpa_sort_practice.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void top(){
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
        System.out.println("findTop1ByNameOrderByIdDesc: "+userRepository.findTop1ByNameOrderByIdDesc("martin"));
        **System.out.println("findFirstByNameOrderByIdDescEmailAsc: "+userRepository.findFirstByNameOrderByIdDescEmailAsc("martin"));**
    }

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
    **where
        user0_.name=? 
    order by
        user0_.id desc,
        user0_.email asc limit ?**
findFirstByNameOrderByIdDescEmailAsc: [User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 20:44:06.373, updatedAt=2021-08-15 20:44:06.373, active=true)]
```

그 결과, WHERE절에서는 name으로 값을 찾고 ORDER BY절에서는 아이디와 이메일에 대한 정렬이 이루어지는 것을 확인해볼 수 있다

더불어 limit으로 정렬까지 마무리된 결과를 대상으로 몇 개까지 자를지를 보여주고 있다

해당 경우에는 martin이 두명인데, 아이디상으로 5번 마틴이 역순으로 가장 앞에 와있기 때문에 아이디 5번인 마틴이 조회된다!

🌟name이 겹쳐지는 경우, 위와 같이 정렬조건을 적절하게 조합하여 사용하는 것도 방법이다!

## 04. 페이징 메서드들처럼 Sort 파라미터를 받아서 처리하기(OrderBy이용 🚫)

Sort의 패키지: org.springframework.data.domain.Sort

아래의 Sort.by와 Order.desc/asc/by 조합으로 진행

```java
Sort.by(Order.desc(string으로 속성명 적어주기))
```

```java
List<User> findFirstByName(String name, Sort sort);
```

```java
ㅍpackage com.example.jpa_sort_practice.repository;

import com.example.jpa_sort_practice.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void top(){
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
        System.out.println("findTop1ByNameOrderByIdDesc: "+userRepository.findTop1ByNameOrderByIdDesc("martin"));
        System.out.println("findFirstByNameOrderByIdDescEmailAsc: "+userRepository.findFirstByNameOrderByIdDescEmailAsc("martin"));
        System.out.println("findFirstByName-desc id: "+userRepository.findFirstByName("martin", Sort.by(Sort.Order.desc("id"))));
        System.out.println("findFirstByName-asc id: "+userRepository.findFirstByName("martin", Sort.by(Sort.Order.asc("id"))));
        System.out.println("findFirstByName-by id: "+userRepository.findFirstByName("martin", Sort.by(Sort.Order.by("id"))));
    }

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
        user0_.name=? 
    **order by
        user0_.id desc limit ?**
findFirstByName-desc id: [User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 20:56:09.531, updatedAt=2021-08-15 20:56:09.531, active=true)]
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
        user0_.name=? 
    **order by
        user0_.id asc limit ?**
findFirstByName-asc id: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 20:56:09.519, updatedAt=2021-08-15 20:56:09.519, active=true)]
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
        user0_.name=? 
    **order by
        user0_.id asc limit ?**
findFirstByName-by id: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 20:56:09.519, updatedAt=2021-08-15 20:56:09.519, active=true)]
```

쿼리를 살펴보면 Order.by와 Order.asc는 모두 오름차순을 지원하고,

Order.desc는 내림차순을 지원하는 것을 살펴볼 수 있다

그렇기 때문에 Order.by와 Order.asc로 조회를 시도하면 아이디가 1번인 마틴이 조회되고

Order.desc로 조회하면 아이디가 5번인 마틴이 조회되는 것을 살펴볼 수 있다

### 🌟04-1. OrderBy를 사용하지 않고 여러 조건을 중첩시켜서 정렬시켜보기!

Sort 클래스 확인

```java
public static Sort by(List<Order> orders) {

		Assert.notNull(orders, "Orders must not be null!");

		return orders.isEmpty() ? Sort.unsorted() : new Sort(orders);
	}

	/**
	 * Creates a new {@link Sort} for the given {@link Order}s.
	 *
	 * @param orders must not be {@literal null}.
	 * @return
	 */
	public static Sort by(Order... orders) {

		Assert.notNull(orders, "Orders must not be null!");

		return new Sort(Arrays.asList(orders));
	}
```

위와 같이 Order를 리스트로 만든 형태를 집어넣어줘도 되고!

Orders를 계속 넣어줘도 된다(가변인자)

```java
package com.example.jpa_sort_practice.repository;

import com.example.jpa_sort_practice.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void top(){
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
        System.out.println("findTop1ByNameOrderByIdDesc: "+userRepository.findTop1ByNameOrderByIdDesc("martin"));
        System.out.println("findFirstByNameOrderByIdDescEmailAsc: "+userRepository.findFirstByNameOrderByIdDescEmailAsc("martin"));
        System.out.println("findFirstByName-desc id: "+userRepository.findFirstByName("martin", Sort.by(Sort.Order.desc("id"))));
        System.out.println("findFirstByName-asc id: "+userRepository.findFirstByName("martin", Sort.by(Sort.Order.asc("id"))));
        System.out.println("findFirstByName-by id: "+userRepository.findFirstByName("martin", Sort.by(Sort.Order.by("id"))));

        **System.out.println("여러 정렬조건을 OrderBy없이- List<Order> :"+userRepository.findFirstByName("martin",Sort.by(Lists.newArrayList(Sort.Order.desc("id"),Sort.Order.by("email")))));
        System.out.println("여러 정렬조건을 OrderBy없이-가변인자 이용: "+userRepository.findFirstByName("martin",Sort.by(Sort.Order.desc("id"),Sort.Order.asc("email"))));**
    }

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
        user0_.name=? 
    order by
        user0_.id desc,
        user0_.email asc limit ?
여러 정렬조건을 OrderBy없이- List<Order> :[User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 21:04:31.858, updatedAt=2021-08-15 21:04:31.858, active=true)]
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
        user0_.name=? 
    order by
        user0_.id desc,
        user0_.email asc limit ?
여러 정렬조건을 OrderBy없이-가변인자 이용: [User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 21:04:31.858, updatedAt=2021-08-15 21:04:31.858, active=true)]
```

그러면 위의 쿼리도 그렇고, 결과도 그렇고, 동일했다!

List<Order>방식과 Order..orders 방식, 즉 리스트에 담는 방식과 가변인자에 담는 방식 모두 여러 조건을 조합해서 사용할 수 있음을 확인해볼 수 있었다!

이러한 방식은 기존의 네이밍 방식으로 인한 자유도 저하와 OrderBy 갯수가 많아지는 것 등을 해결해줄 수 있다! 🧸 이렇게 되면, 메서드 이름 길이도 줄어들게 만들 수 있어서 코드 가독성을 높일 수 있다!

즉, 이 방식을 통해서 인터페이스에 특정 메서드만 두고서, 내부 정렬 방식은 외부의 요청에 따라 다르게 적용될 수 있게 함으로써 그 자유도를 높여줄 수 있다!

### 04-2. 04-1의 확장! 코드 가독성 증대를 위해서 Sort 형메서드를 만들어서 관리하기

```java
package com.example.jpa_sort_practice.repository;

import com.example.jpa_sort_practice.domain.User;
import org.assertj.core.util.Lists;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Sort;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

//
@SpringBootTest
//@Transactional
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void top(){
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
        System.out.println("findTop1ByNameOrderByIdDesc: "+userRepository.findTop1ByNameOrderByIdDesc("martin"));
        System.out.println("findFirstByNameOrderByIdDescEmailAsc: "+userRepository.findFirstByNameOrderByIdDescEmailAsc("martin"));
        System.out.println("findFirstByName-desc id: "+userRepository.findFirstByName("martin", Sort.by(Sort.Order.desc("id"))));
        System.out.println("findFirstByName-asc id: "+userRepository.findFirstByName("martin", Sort.by(Sort.Order.asc("id"))));
        System.out.println("findFirstByName-by id: "+userRepository.findFirstByName("martin", Sort.by(Sort.Order.by("id"))));

        System.out.println("여러 정렬조건을 OrderBy없이- List<Order> :"+userRepository.findFirstByName("martin",Sort.by(Lists.newArrayList(Sort.Order.desc("id"),Sort.Order.by("email")))));
        System.out.println("여러 정렬조건을 OrderBy없이-가변인자 이용: "+userRepository.findFirstByName("martin",Sort.by(Sort.Order.desc("id"),Sort.Order.asc("email"))));
        **System.out.println("Sort형 메서드를 만들어서 정렬해서 가독성 높이기: "+userRepository.findFirstByName("martin",getSort()));**
    }

    **private Sort getSort(){
        return Sort.by(
          Sort.Order.desc("id"),
          Sort.Order.asc("email").ignoreCase(),
          Sort.Order.desc("createdAt")
        );
    }**
}
```

- Order ignoreCase() : 키워드 ignoreCase처럼 대소문자를 무시(모두 대문자로 변환하여 비교할 수 있도록 지원)

위와 같이 정렬 조건이 더 길어지면 Sort형을 반환하는 메서드를 만들어두고, Sort 자리에 인자값으로 넣어주면 복잡한 조합의 정렬 적용이 가능하다!

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
        user0_.name=? 
    order by
        user0_.id desc,
        lower(user0_.email) asc,
        user0_.created_at desc limit ?
Sort형 메서드를 만들어서 정렬해서 가독성 높이기: [User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 21:13:20.521, updatedAt=2021-08-15 21:13:20.521, active=true)]
```

- 자유도가 떨어지는 것이 단점만은 아닌데, Sort 인자를 관리해야 하는 부분에 대해서는 불편함을 줄여줄 수는 있다(하지만 코드 가독성은 여전히 더 악화시킨다)

[이것은 개발자가 직접 고민해서 선택할 문제다]