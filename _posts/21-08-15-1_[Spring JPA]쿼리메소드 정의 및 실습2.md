# 쿼리메소드 정의 및 실습2

### 00. 지난시간 학습한 내용을 한줄로 리뷰

find..by 등과 같은 형태에서 .

- find나 count 등은 쿼리를 어떻게 생성할 지
- ..에는 엔티티 관련 등의 이름이 올 수 있고
- by이하는 where 절을 형성

한다는 점을 알 수 있다

하지만 우리가 사용할 쿼리는 

- where 조건이 여러개일 수도 있고
- 문자열이 아닌 다양한 형태의 타입들을 사용할 수 있을 것

이다

조건과 관련된 키워드에 대해서는 아래를 잠깐 참고해보아도 좋다

[쿼리메소드 정의 및 실습1](https://hy6219.github.io/TIL/Spring/JPA/JPA%20Repository/[Spring%20JPA]%EC%BF%BC%EB%A6%AC%EB%A9%94%EC%86%8C%EB%93%9C%20%EC%A0%95%EC%9D%98%20%EB%B0%8F%20%EC%8B%A4%EC%8A%B51.html#b51b01d8-febf-4b00-b7a9-5e104131b780)

### 01. And를 이용한 조건 확인해보기

저번 시간에 잠깐 살펴봤지만, 복습 겸 이름과 이메일로 user를 찾도록 해보자

```java
User findByNameAndEmail(String name, String email);
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
        System.out.println("findFirst2UsersByName: "+userRepository.findFirst2UsersByName("martin"));
        System.out.println("---");
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
   //     System.out.println("---");
  //      System.out.println("findLast1ByName: "+userRepository.findLast1ByName("martin"));
        System.out.println("---");
		📌 **System.out.println("findByNameAndEmail: "+userRepository.findByNameAndEmail("martin","martin@fastcampus.com"));**📌
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
findByNameAndEmail: User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 07:32:08.462, updatedAt=2021-08-15 07:32:08.462)
```

그러면 쿼리의 조건절에서는 by 뒤에 붙여진 name과 email 조건으로 국한시켜서 `두 조건을 모두 만족시키는` 레코드를 추출해내는 것을 확인해볼 수 있다

### 02. Or를 이용한 조건 확인해보기

이름 혹은 이메일로 조건을 만들어서 해당되는 레코드들을 찾아보도록 하자

```java
List<User> findByNameOrEmail(String name, String email);
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
        System.out.println("findFirst2UsersByName: "+userRepository.findFirst2UsersByName("martin"));
        System.out.println("---");
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
   //     System.out.println("---");
  //      System.out.println("findLast1ByName: "+userRepository.findLast1ByName("martin"));
        System.out.println("---");
        System.out.println("findByNameAndEmail: "+userRepository.findByNameAndEmail("martin","martin@fastcampus.com"));
        System.out.println("---");
       📌 **System.out.println("findByNameOrEmail: "+userRepository.findByNameOrEmail("martin","martin@fastcampus.com"));**📌
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
        or user0_.email=?**
findByNameOrEmail: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 07:37:40.424, updatedAt=2021-08-15 07:37:40.424), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 07:37:40.448, updatedAt=2021-08-15 07:37:40.448)]
```

OR의 경우에는, 먼저 이름으로 찾은 경우와 이메일로 찾은 경우의 합집합이므로, `둘 중 한 조건만 충족해도` 결과에 포함시키는 것을 확인해볼 수 있다

### 03. After를 이용해서 특정 일자 이후일 때로 조건을 만들기

지난시간에는 count...By와 접목해서 사용했었는데, 이번에는 복습할 겸 find...By와 접목해서 사용해보자

CreatedAt의 값에 대한 조건을 2021년 8월 1일 이후로 좁혀보자

```java
List<User> findUsersByCreatedAtAfter(Date date);
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
        System.out.println("findFirst2UsersByName: "+userRepository.findFirst2UsersByName("martin"));
        System.out.println("---");
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
   //     System.out.println("---");
  //      System.out.println("findLast1ByName: "+userRepository.findLast1ByName("martin"));
        System.out.println("---");
        System.out.println("findByNameAndEmail: "+userRepository.findByNameAndEmail("martin","martin@fastcampus.com"));
        System.out.println("---");
        System.out.println("findByNameOrEmail: "+userRepository.findByNameOrEmail("martin","martin@fastcampus.com"));
        System.out.println("---");
        **System.out.println("findUsersByCreatedAtAfter: "+userRepository.findUsersByCreatedAtAfter(sf.parse(createdAt)));**
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
        user0_.created_at>?
findUsersByCreatedAtAfter: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 07:45:35.695, updatedAt=2021-08-15 07:45:35.695), User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 07:45:35.709, updatedAt=2021-08-15 07:45:35.709), User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-15 07:45:35.71, updatedAt=2021-08-15 07:45:35.71), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 07:45:35.71, updatedAt=2021-08-15 07:45:35.71), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 07:45:35.711, updatedAt=2021-08-15 07:45:35.711)]
```

그러면 where 절에서 `검색조건일자가 입력일자값보다 커야 이후를 검색해볼 수 있기 때문에` created_at>? 부분이 들어가는 것을 확인해볼 수 있다

### 04. Before를 이용해서 특정 기점 이전의 날짜로 조건을 만들기

이번에는 들어온 createdAt값 이전까지로 범위를 좁혀서 검색해보자

지난 시간에 공식문서에서 확인해본 것을 토대로 생각해본다면,

created_at<?를 이용해서 지정될 것이다

```java
List<User> findUsersByCreatedAtBefore(Date date);
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
        System.out.println("findFirst2UsersByName: "+userRepository.findFirst2UsersByName("martin"));
        System.out.println("---");
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
   //     System.out.println("---");
  //      System.out.println("findLast1ByName: "+userRepository.findLast1ByName("martin"));
        System.out.println("---");
        System.out.println("findByNameAndEmail: "+userRepository.findByNameAndEmail("martin","martin@fastcampus.com"));
        System.out.println("---");
        System.out.println("findByNameOrEmail: "+userRepository.findByNameOrEmail("martin","martin@fastcampus.com"));
        System.out.println("---");
        System.out.println("findUsersByCreatedAtAfter: "+userRepository.findUsersByCreatedAtAfter(sf.parse(createdAt)));
        System.out.println("---");
        **String newCr= "2021-08-15";
        System.out.println("findUsersByCreatedAtBefore: "+userRepository.findUsersByCreatedAtBefore(sf.parse(newCr)));**
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
        user0_.created_at<?
findUsersByCreatedAtBefore: []
```

역시 예상한 것처럼 created_at<?로 조건 범위를 좁혀서 검색했는데, 지금 8월 15일 이전에 작성된 글이 없기 때문에 빈 리스트를 확인할 수 있다

### 05. After나 Before를 이용해서 마치 GreaterThan이나 LessThan의 효과를 둔 조건을 걸어주기

입력된 아이디값보다 큰 경우에 대해서 찾아보고-After

입력된 이름값보다 작은 경우에 대해서 찾아보자-Before

```java
List<User> findUsersByIdAfter(Long id);
    List<User> findUsersByNameBefore(String name);
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
        System.out.println("findFirst2UsersByName: "+userRepository.findFirst2UsersByName("martin"));
        System.out.println("---");
        System.out.println("findTop1ByName: "+userRepository.findTop1ByName("martin"));
   //     System.out.println("---");
  //      System.out.println("findLast1ByName: "+userRepository.findLast1ByName("martin"));
        System.out.println("---");
        System.out.println("findByNameAndEmail: "+userRepository.findByNameAndEmail("martin","martin@fastcampus.com"));
        System.out.println("---");
        System.out.println("findByNameOrEmail: "+userRepository.findByNameOrEmail("martin","martin@fastcampus.com"));
        System.out.println("---");
        System.out.println("findUsersByCreatedAtAfter: "+userRepository.findUsersByCreatedAtAfter(sf.parse(createdAt)));
        System.out.println("---");
        String newCr= "2021-08-15";
        System.out.println("findUsersByCreatedAtBefore: "+userRepository.findUsersByCreatedAtBefore(sf.parse(newCr)));
        System.out.println("---");
        System.out.println("findUsersByCreatedAtIsBefore: "+userRepository.findUsersByCreatedAtIsBefore(sf.parse(newCr)));
        System.out.println("---");
        System.out.println("findUsersByCreatedAtIsAfter: "+userRepository.findUsersByCreatedAtIsAfter(sf.parse(newCr)));
        System.out.println("---");
        **System.out.println("findUsersByIdAfter: "+userRepository.findUsersByIdAfter(3L));
        System.out.println("---");
        System.out.println("findUsersByNameBefore: "+userRepository.findUsersByNameBefore("martin"));**
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
        user0_.id>?
findUsersByIdAfter: [User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 08:33:45.221, updatedAt=2021-08-15 08:33:45.221), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 08:33:45.221, updatedAt=2021-08-15 08:33:45.221)]
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
        user0_.name<?
findUsersByNameBefore: [User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 08:33:45.22, updatedAt=2021-08-15 08:33:45.22), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 08:33:45.221, updatedAt=2021-08-15 08:33:45.221)]
```

그러면, 그 결과, 아이디는 주어진 값보다 큰 아이디값을 가진 레코드를 조회하여 보여주고

이름은 알파벳을 기준으로 그 이전의 알파벳을 활용한 레코드를 조회하여 보여주는 것을 알 수 있다

🌟 이렇게 After와 Before는 다양하게 붙여줄수도 있지만 `가독성을 위해서 After와 Before는 날짜나 시간에 사용하는것이 적절`하다

### 06. GreaterThan, GreaterThanEqual, LessThan, LessThanEqual

먼저 왼쪽부터 차례대로 `필드>input`, `필드≥input`,`필드<input`, `필드≤input` 을 나타낸다

아이디를 기준으로 이 네가지를 적용해보자

```java
List<User> findUsersByIdGreaterThan(Long id);
    List<User> findUsersByIdGreaterThanEqual(Long id);
    List<User> findUsersByIdLessThan(Long id);
    List<User> findUsersByIdLessThanEqual(Long id);
    List<User> findUsersByCreatedAtLessThanEqual(Date date);
    List<User> findUsersByCreatedAtGreaterThanEqual(Date date);
```

```java
@Test
    public void greatLess() throws ParseException {
        SimpleDateFormat sf = new SimpleDateFormat("yyyy/MM/dd");
        String cr= "2021/08/15";
        System.out.println("findUsersByIdGreaterThan: "+userRepository.findUsersByIdGreaterThan(2L));
        System.out.println("findUsersByIdGreaterThanEqual: "+userRepository.findUsersByIdGreaterThanEqual(2L));
        System.out.println("findUsersByIdLessThan: "+userRepository.findUsersByIdLessThan(2L));
        System.out.println("findUsersByIdLessThanEqual: "+userRepository.findUsersByIdLessThanEqual(2L));
        System.out.println("findUsersByCreatedAtLessThanEqual: "+userRepository.findUsersByCreatedAtLessThanEqual(sf.parse(cr)));
        System.out.println("findUsersByCreatedAtGreaterThanEqual: "+userRepository.findUsersByCreatedAtGreaterThanEqual(sf.parse(cr)));
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
        user0_.id>?
findUsersByIdGreaterThan: [User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-15 12:00:15.901, updatedAt=2021-08-15 12:00:15.901), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 12:00:15.902, updatedAt=2021-08-15 12:00:15.902), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 12:00:15.902, updatedAt=2021-08-15 12:00:15.902)]
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
        user0_.id>=?
findUsersByIdGreaterThanEqual: [User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 12:00:15.901, updatedAt=2021-08-15 12:00:15.901), User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-15 12:00:15.901, updatedAt=2021-08-15 12:00:15.901), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 12:00:15.902, updatedAt=2021-08-15 12:00:15.902), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 12:00:15.902, updatedAt=2021-08-15 12:00:15.902)]
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
        user0_.id<?
findUsersByIdLessThan: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 12:00:15.886, updatedAt=2021-08-15 12:00:15.886)]
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
        user0_.id<=?
findUsersByIdLessThanEqual: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 12:00:15.886, updatedAt=2021-08-15 12:00:15.886), User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 12:00:15.901, updatedAt=2021-08-15 12:00:15.901)]
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
        user0_.created_at<=?
findUsersByCreatedAtLessThanEqual: []
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
        user0_.created_at>=?
findUsersByCreatedAtGreaterThanEqual: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 12:00:15.886, updatedAt=2021-08-15 12:00:15.886), User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 12:00:15.901, updatedAt=2021-08-15 12:00:15.901), User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-15 12:00:15.901, updatedAt=2021-08-15 12:00:15.901), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 12:00:15.902, updatedAt=2021-08-15 12:00:15.902), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 12:00:15.902, updatedAt=2021-08-15 12:00:15.902)]
```

그러면 쿼리를 집중해서 잘 살펴보면 `GreaterThan에서는 조건에 >`, `GreaterThanEqual에서는 >=`, `LessThan에서는 <`, `LessThanEqual에서는 <=` 이 붙어서 조회가 된 것을 살펴볼 수 있다

특히, After 및 Before와 다른 점은 `범용적으로 날짜, 숫자 등 다양한 타입에 적용할 수 있다` 는 점이다!

그렇기에, 지금 위에서는 After나 Before는 이후 혹은 이전만 쉽게 확인해볼 수 있었는데 LessThanEqual 이라던지 GreaterThanEqual로 해당 일자 8월 15일을 포함하고 그 이전 혹은 이후도 검색해볼 수 있었다

### 07. 숫자와 날짜에 대한 구간에 대한 조건을 세우는 BETWEEN으로 조건 만들기

쿼리문에서 BETWEEN A AND B를 통해 특정 구간을 끊어내는 것과 동일하다!

먼저 CreatedAt 필드를 접목해서 날짜 구간으로 조회해보고

숫자로는 아이디값을 이용해서 구간으로 조회해보자

```java
List<User> findUsersByCreatedAtBetween(Date date1, Date date2);
    List<User> findUsersByIdBetween(Long from, Long to);
```

```java
@Test
public void betweenTest() throws ParseException {
        SimpleDateFormat sf = new SimpleDateFormat("yyyy/MM/dd");
        Date date1=sf.parse("2021/08/01");
        Date date2=sf.parse("2021/08/16");

        System.out.println("findUsersByCreatedAtBetween: "+userRepository.findUsersByCreatedAtBetween(date1,date2));
        System.out.println("findUsersByIdBetween: "+userRepository.findUsersByIdBetween(1L,4L));
    }
}
```

살펴본다면, BETWEEN A AND B가 x≥A && x≤B 의 의미이기 때문에 지금 입력에 해당되는 해당일자는 8월 15일이기 때문에 구간에 걸리는 레코드는 모든 레코드가 잡히게 될 것이다

다만, 날짜의 경우, 보통은 데이터베이스에 시간을 미지정시 0시 0분 0초로 들어가기 때문에 24시까지 포함해서 조회하고자 한다면, 시간을 미지정한 채 위와 같이 다음날을 넣어주어도 좋다

비슷한 개념에서 살펴보면, 아이디가 1L인 경우와 4L인 경우의 사이에 해당되는 레코드는 아이디가 1~4L인 경우가 해당될 것이다

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
        user0_.created_at between ? and ?
findUsersByCreatedAtBetween: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 12:14:20.855, updatedAt=2021-08-15 12:14:20.855), User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 12:14:20.865, updatedAt=2021-08-15 12:14:20.865), User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-15 12:14:20.866, updatedAt=2021-08-15 12:14:20.866), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 12:14:20.866, updatedAt=2021-08-15 12:14:20.866), User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 12:14:20.866, updatedAt=2021-08-15 12:14:20.866)]
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
        user0_.id between ? and ?
findUsersByIdBetween: [User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-15 12:14:20.855, updatedAt=2021-08-15 12:14:20.855), User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-15 12:14:20.865, updatedAt=2021-08-15 12:14:20.865), User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-15 12:14:20.866, updatedAt=2021-08-15 12:14:20.866), User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-15 12:14:20.866, updatedAt=2021-08-15 12:14:20.866)]
```

🌟BETWEEN과 LessThan,LessThanEquals,GreaterThan,GreaterThanEquals는 모두 경계값을 포함하고 After 및 Before는 경계값을 포함시키지 않는다는 특징을 지닌다!

다만! BETWEEN은   LessThan,LessThanEquals,GreaterThan,GreaterThanEquals와 다르게 하한과 상항이 존재하고, 그 둘을 모두 포용한다는 차이가있다!

➕Between을 풀어서 쓴다면 예를 들어서, 위의 `findUsersByIdBetween` 를 예로 든다면 아래와 같이 풀어쓸 수 있을 것이다

```java
List<User> findUsersByIdGreaterThanEqualsAndIdLessThanEqual(Long from, Long to);
```

🌟 쿼리상의 논리적 오류는 개발툴에서조차도 찾아줄 수 없기 때문에 키워드를 기반으로 하는 쿼리메서드 네이밍에서는 조심해야 할 것!!