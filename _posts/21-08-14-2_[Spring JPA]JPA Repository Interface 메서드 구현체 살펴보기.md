# SimpleJpaRepository 분석

## 📍UPDATE관련 해서 save메서드 확인해보기📍

아직까지 살펴본 쿼리관련 메서드에서 UPDATE와 관련된 메서드를 확인해보지는 못했다

UPDATE쿼리는 어떻게 만드는지 살펴보자

`setter로 객체 인스턴스 값을 변경시켜주면 어떨까?`

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
      User userIdOne=userRepository.findById(1L).orElseThrow(RuntimeException::new);
      System.out.println("--");
      userIdOne.setEmail("martin-updated@fastcampus.com");
      userRepository.save(userIdOne);
      System.out.println("--");
      userRepository.findAll().forEach(System.out::println);
    }

}
```

위와 같이 setter를 이용해보면

- 🌹 : findById(ID id)
- 🌻 : save(Entity)
- 💐 : findAll()

```java
Hibernate: 
  🌹  select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?🌹
--
Hibernate: 
   🌻 select
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
    update
        user 
    set
        created_at=?,
        email=?,
        name=?,
        updated_at=? 
    where
        id=?🌻
--
Hibernate: 
   💐 select
        user0_.id as id1_0_,
        user0_.created_at as created_2_0_,
        user0_.email as email3_0_,
        user0_.name as name4_0_,
        user0_.updated_at as updated_5_0_ 
    from
        user user0_💐
User(id=1, name=martin, email=martin-updated@fastcampus.com, createdAt=2021-08-14 15:00:24.835, updatedAt=2021-08-14 15:00:24.835)
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-14 15:00:24.851, updatedAt=2021-08-14 15:00:24.851)
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-14 15:00:24.852, updatedAt=2021-08-14 15:00:24.852)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-14 15:00:24.852, updatedAt=2021-08-14 15:00:24.852)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-14 15:00:24.853, updatedAt=2021-08-14 15:00:24.853)
```

1. 기존의 데이터 중 아이디가 1L인 레코드를 가져오는데, orElseThrow로 예외처리를 떠넘겨주고 ▶️ findById🌹
2. 이렇게 찾아온 데이터를 setEmail로 이메일 값을 setter로 변경해준 후
3. save해주면 `update`되는데, 이때 📌`select로 확인 후 update되는 것을 확인해볼 수 있다` 📌▶️ save 🌻
4. 그리고 결과를 확인하기 위해서 findAll을 해주면, 아이디가 1인 martin레코드의 이메일이 수정된것을 확인해볼 수 있다 ▶️ findAll 💐

그러면 어떨 때에는 update, 어떨 때에는 insert되는 이러한 설정이 어떻게 되어있는지 확인해보기 이해서 `shift키를 연속 두번 눌러서 확인해보자`

`SimpleJpaRepository` 를 검색해보자!

```java
@Transactional
	@Override
	public <S extends T> S save(S entity) {

		Assert.notNull(entity, "Entity must not be null.");

		if (entityInformation.isNew(entity)) {
			em.persist(entity);
			return entity;
		} else {
			return em.merge(entity);
		}
	}
```

그리고 그중 이 save 메서드를 잘 보면, 먼저, null인 경우에는 IllegalArgumentException 에러와 함께 "Entity must not be null" 메시지를 띄우고

엔티티가 기존의 엔티티라면 병합(merge, `update`)시키고, 

새로운 엔티티라면 새롭게 만들어 영구적으로 만들게 하는 것(persist(entity))을 확인해볼 수 있다

그리고 @Transactional이 붙어있는데, @Transactional이 존재하지 않으면 save메서드에서 자체적으로 Tranasactional을 감싸서 실행한다

`ctrl+T` 를 눌러서(설정의 키맵에서 확인가능하다) isNew메서드가 구현된 구현체를 확인해보자

구현체를 타고 타고 따라가다보면 `AbstractPersistable` 클래스를 확인해볼 수 있고 여기서 isNew를 볼수 있다

```java
@Transient // DATAJPA-622
	@Override
	public boolean isNew() {
		return null == getId();
	}
```

위와 같이 isNew는 고유값이 널인지 체크하는 것을 확인해볼 수 있다

더불어 isNew라는 플래그를 통해서 select +(insert혹은 update)를 실행하는 것을 확인해볼 수 있다

즉, 고유값이 null이면 insert, 아니라면 update를 실행하는데, 이를 위해서 select가 선행됨을 확인해볼 수 있다

이렇게 확인해봄으로써 JpaRepository의 메서드들은 SimpleJpaRepository에서 구현된 메서드들이 제공되고 있음을 확인해볼 수 있었다

## saveAndFlush

```java
@Transactional
	@Override
	public <S extends T> S saveAndFlush(S entity) {

		S result = save(entity);
		flush();

		return result;
	}
```

saveAndFlush는 정말 직관적으로 save를 진행한 후, flush를 진행함을 확인해볼 수 있었다

## saveAll(Iterable)

```java
@Transactional
	@Override
	public <S extends T> List<S> saveAll(Iterable<S> entities) {

		Assert.notNull(entities, "Entities must not be null!");

		**List<S> result = new ArrayList<S>();**

		for (S entity : entities) {
			**result.add(save(entity));**
		}

		return result;
	}
```

위의 내용을 확인해보면 내부에는 저장할 모든 객체들의 정보가 담긴 List를 ArrayList로 구현한 "result" 객체가 마련되어 있고

파라미터로 들어온 iterable의 사이즈만큼 향상된 for문을 사용해서 리스트에는 매번 save 결과를 담아내는 것을 확인해볼 수 있다

그리고 result라는 List 객체를 반환시키는 것을 확인해볼 수 있다

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
        User user1= new User("david","david@fastcampus.com");
        User user2=new User("simons","simons@fastcampus.com");
        List<User> res=userRepository.saveAll(Lists.newArrayList(user1,user2));
        System.out.println("inserted result: "+res);
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
        user
        (created_at, email, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user
        (created_at, email, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?)
**inserted result: [User(id=6, name=david, email=david@fastcampus.com, createdAt=null, updatedAt=null), User(id=7, name=simons, email=simons@fastcampus.com, createdAt=null, updatedAt=null)]**
```

이렇게 삽입되는 모든 객체들이 리스트에 담겨진 것을 확인해볼 수 있다

이번에는 update와 insert를 모두 테스트해봐도 재밌을 것 같다

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
        User user1= new User("david","david@fastcampus.com");
        User user2=new User("simons","simons@fastcampus.com");
        User userInOne=userRepository.findById(1L).orElseThrow(RuntimeException::new);
        userInOne.setEmail("update_email_martin@gmail.com");
        List<User> res=userRepository.saveAll(Lists.newArrayList(user1,user2,userInOne));
        System.out.println("inserted&updated result: "+res);
    }

}
```

```java
--findById
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
    call next value for hibernate_sequence
Hibernate: 
    call next value for hibernate_sequence
--save전 isNew를 통한 select
Hibernate: 
    **select
        user0_.id as id1_0_0_,
        user0_.created_at as created_2_0_0_,
        user0_.email as email3_0_0_,
        user0_.name as name4_0_0_,
        user0_.updated_at as updated_5_0_0_ 
    from
        user user0_ 
    where
        user0_.id=?**
Hibernate: 
    insert 
    into
        user
        (created_at, email, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?)
Hibernate: 
    insert 
    into
        user
        (created_at, email, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?)
**Hibernate: 
    update
        user 
    set
        created_at=?,
        email=?,
        name=?,
        updated_at=? 
    where
        id=?**
**inserted&updated result: [User(id=6, name=david, email=david@fastcampus.com, createdAt=null, updatedAt=null), User(id=7, name=simons, email=simons@fastcampus.com, createdAt=null, updatedAt=null), User(id=1, name=martin, email=update_email_martin@gmail.com, createdAt=2021-08-14 15:43:36.754, updatedAt=2021-08-14 15:43:36.754)]**
```

확인해보니 `isNew를 통한 select가 처음에 확인되고`

그 이후 `insert, update`가 확인되었으며

리스트에는 새로 담겨지는 데이터와 기존 데이터가 수정된 결과까지 담기게 되었음을 확인해볼 수 있었다

## findById와 getOne

```java
@Override
	public Optional<T> findById(ID id) {

		Assert.notNull(id, ID_MUST_NOT_BE_NULL);

		Class<T> domainType = getDomainClass();

		if (metadata == null) {
			return Optional.ofNullable(em.find(domainType, id));
		}

		LockModeType type = metadata.getLockModeType();

		Map<String, Object> hints = new HashMap<>();
		getQueryHints().withFetchGraphs(em).forEach(hints::put);

		return Optional.ofNullable(type == null ? em.find(domainType, id, hints) : em.find(domainType, id, type, hints));
	}
```

```java
@Deprecated
	@Override
	public T getOne(ID id) {

		Assert.notNull(id, ID_MUST_NOT_BE_NULL);
		return em.getReference(getDomainClass(), id);
	}
```

findById는 find를 통해 직접 찾는 EAGER FETCH

getOne은 getReference를 통해 찾는 LAZY FETCH 의 양상을 보인다

## existsById

메서드 구현 부분 중 

```java
String existsQuery = QueryUtils.getExistsQueryString(entityName, placeholder, idAttributeNames);
```

이 부분을 통해 getExistsQueryString 메서드를 추적해보면(QueryUtils의 static 메서드)

```java
public static String getExistsQueryString(String entityName, String countQueryPlaceHolder,
			Iterable<String> idAttributes) {

		String whereClause = Streamable.of(idAttributes).stream() //
				.map(idAttribute -> String.format(EQUALS_CONDITION_STRING, "x", idAttribute, idAttribute)) //
				.collect(Collectors.joining(" AND ", " WHERE ", ""));

		return String.format(COUNT_QUERY_STRING, countQueryPlaceHolder, entityName) + whereClause;
	}
```

```java
public static final String COUNT_QUERY_STRING = "select count(%s) from %s x";
```

COUNT_QUERY_STRING에 기반한다는 것을 확인해볼 수 있고, 이를 통해서 저번시간에 확인해봤던 existsById 실행 시 COUNT 함수 등장 이유를 확인해볼 수 있었다

## deleteAll vs deleteAllInBatch

```java
@Override
	@Transactional
	public void deleteAll() {

		for (T element : findAll()) {
			delete(element);
		}
	}
```

먼저 deleteAll의 경우에는 findAll을 통해 가져온 결과를 활용해서 반복문을 돌면서 delete를 반복한다는 것을 확인해볼 수 있다

```java
@Override
	@Transactional
	public void deleteAllInBatch() {
		em.createQuery(getDeleteAllQueryStringgetDeleteAllQueryString()).executeUpdate();
	}
```

```java
private String getDeleteAllQueryString() {
		return getQueryString(DELETE_ALL_QUERY_STRING, entityInformation.getEntityName());
	}
```

```java
public static final String DELETE_ALL_QUERY_STRING = "delete from %s x";
```

```java
//AbstractProducedQuery.java
@Override
	public int executeUpdate() throws HibernateException {
		getProducer().checkTransactionNeededForUpdateOperation( "Executing an update/delete query" );

		beforeQuery();
		try {
			return doExecuteUpdate();
		}
		catch ( QueryExecutionRequestException e) {
			throw new IllegalStateException( e );
		}
		catch( TypeMismatchException e ) {
			throw new IllegalArgumentException( e );
		}
		catch ( HibernateException e) {
			throw getExceptionConverter().convert( e );
		}
		finally {
			afterQuery();
		}
	}
```

그에 반해 deleteAllInBatch는 getDeleteAllQueryString() 메서드를 이용함으로써 전체를 삭제하는 쿼리인 `DELETE * FROM 테이블` 로 쿼리를 만들고(createQuery), 마지막으로 한꺼번에 executeUpdate를 실행하여 작동하였음을 확인해볼 수 있었다! 즉, 쿼리는 deleteAll과 다르게 단 하나로 진행되었고, 수행도 단 한 번으로 진행되었음을 확인해볼 수 있었다