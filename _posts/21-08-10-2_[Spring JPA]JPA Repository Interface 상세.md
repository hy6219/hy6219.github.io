# JPA Repository Interface 상세

(전제조건)JPA를 사용하기 위해서는 아래와 같은 의존성을 추가해주었다는 전제 하에 진행하도록 하자

```java
plugins {
    id 'org.springframework.boot' version '2.5.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
}

group = 'com.fastcampus'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    **implementation 'org.springframework.boot:spring-boot-starter-data-jpa'**
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
		runtimeOnly 'com.h2database:h2'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
    useJUnitPlatform()
}
```

🌟 ORM은 `자바 객체`와 `데이터베이스 필드`를 연결해주는 것인데 여기서 `자바객체`는 `엔티티`를 의미한다!

---

지난시간의 코드를 그대로 이용해서 진행해보자

🌟먼저, 객체를 엔티티(데이터베이스에서 객체의 의미)로 만들어주기 위해서는  `@Entity` 를 클래스에 붙여주면 된다!

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import java.time.LocalDateTime;

@Component
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
**@Entity**
public class User {
    @NonNull
    private String name;
    @NonNull
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

그러면 친절한 인텔리제이는 `영속성 엔티티 User는 기본키를 가져야 한다(Persistent entity 'User' should have primary key)` 라는 문구와 함께 빨간펜을 쭉 그어준다!

그래서 ! 기본키로 작동시켜줄 필드를 따로 만들어보자(이름이나 다른 값은 같을 수 있기 때문이다)

그 필드로는 아이디값을 이용해보기로 하고

`@Id` 로 기본키를 명시해주자

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import java.time.LocalDateTime;

@Component
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
public class User {
    **@Id**
    private Long id;
    @NonNull
    private String name;
    @NonNull
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

### 📌 Spring Data JPA 기본키 매핑 방법 📌

1. 직접 할당 : 기본키를 어플리케이션에서 직접 할당
2. 자동 생성 : DB가 자동으로 기본키를 할당(오라클의 시퀀스 같은 것)

`@GeneratedValue` 를 사용

(1) IDENTITY :기본키 생성을 데이터베이스에 위임(DB에 의존적)

(2) SEQUENCE: 데이터베이스 시퀀스를 사용해서 기본키를 할당(DB에 의존적)

- `@SequenceGenerator` 로 시퀀스 생성기 등록 후 시퀀스 이름 지정 필요

(3) TABLE : 키 생성 테이블 사용

(4) AUTO : 데이터베이스가 오라클일 경우 SEQUENCE를  자동선택하여 사용(DB에 의존적 x) 

[Spring Data JPA 기본키 매핑하는 방법](https://ithub.tistory.com/24)

---

기본키에 대해서는, 자동생성전략을 세워서, 시퀀스처럼 자동 증가되도록 `@GeneratedValue` 를 붙여주자

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import java.time.LocalDateTime;

@Component
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
public class User {
    **@Id
    @GeneratedValue**
    private Long id;
    @NonNull
    private String name;
    @NonNull
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

이러한 엔티티는 과연 어떻게 조회/저장/삭제/수정이 가능할까?

▶️ 바로 `레포지토리 Repository` 로 인해 가능한 것이다!

먼저, JpaRepository를 상속받는 인터페이스를 만들어주자

그리고 이때 JpaRepository를 참고하면 `JpaRepository<T, ID>` 인 점을 참조해서 `User클래스가 T(엔티티 타입)` 에 매칭될 것이고 , `Id는 기본키인 long id` 에 매칭될 것임을 정리해볼 수 있다

```java
package com.fastcampus.jpa_layer.repository;

import domain.User;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository **extends JpaRepository<User,Long>** {
}
```

그리고 간단하게 통합테스트를 해보자

```java
package com.fastcampus.jpa.jpa_setting.repository;

import com.fastcampus.jpa.jpa_setting.domain.User;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void test(){
        userRepository.save(new User());
        System.out.println(userRepository.findAll());
    }

}
```

save를 통해서는 저장을 할 것이고, findAll은 selectAll과 같은 의미로 보면 될 것이다

그러면 아래처럼 기본생성자인데, 기본키에 대해서는 시퀀스나 auto increment처럼 작동할  것이기 때문에 1이 되는 것을 확인해볼 수 있다

```java
[User(id=1, name=null, email=null, createdAt=null, updatedAt=null)]
```

그리고 이렇게 확인해본 것을 통해서, 데이터베이스 내에 User라는 테이블 내부에 id값이 1인 레코드가 존재함을 확인해볼 수 있다

---

먼저, JpaRepository의 구조를 살펴보자

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/JPA%20Repository/UserRepository.png?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/JPA%20Repository/UserRepository.png?raw=true)

위는 인텔리제이에서 다이어그램으로 그린 것인데, 지금 우리가 사용한 `save` 메서드라던지 `findAll` 메서드를 추적해보면, 각각은 `CrudRepository` 인터페이스와 `JpaRepository` 인터페이스에서 찾아볼 수 있었다

먼저 JpaRepository 인터페이스의 추상 메서드들을 살펴보자

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
package org.springframework.data.jpa.repository;

import java.util.List;

import javax.persistence.EntityManager;

import org.springframework.data.domain.Example;
import org.springframework.data.domain.Sort;
import org.springframework.data.repository.NoRepositoryBean;
import org.springframework.data.repository.PagingAndSortingRepository;
import org.springframework.data.repository.query.QueryByExampleExecutor;

/**
 * JPA specific extension of {@link org.springframework.data.repository.Repository}.
 *
 * @author Oliver Gierke
 * @author Christoph Strobl
 * @author Mark Paluch
 * @author Sander Krabbenborg
 * @author Jesse Wouters
 */
@NoRepositoryBean
public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID>, QueryByExampleExecutor<T> {

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.repository.CrudRepository#findAll()
	 */
	@Override
	List<T> findAll();

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.repository.PagingAndSortingRepository#findAll(org.springframework.data.domain.Sort)
	 */
	@Override
	List<T> findAll(Sort sort);

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.repository.CrudRepository#findAll(java.lang.Iterable)
	 */
	@Override
	List<T> findAllById(Iterable<ID> ids);

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.repository.CrudRepository#save(java.lang.Iterable)
	 */
	@Override
	<S extends T> List<S> saveAll(Iterable<S> entities);

	/**
	 * Flushes all pending changes to the database.
	 */
	void flush();

	/**
	 * Saves an entity and flushes changes instantly.
	 *
	 * @param entity entity to be saved. Must not be {@literal null}.
	 * @return the saved entity
	 */
	<S extends T> S saveAndFlush(S entity);

	/**
	 * Saves all entities and flushes changes instantly.
	 *
	 * @param entities entities to be deleted. Must not be {@literal null}.
	 * @return the saved entities
	 * @since 2.5
	 */
	<S extends T> List<S> saveAllAndFlush(Iterable<S> entities);

	/**
	 * Deletes the given entities in a batch which means it will create a single query. This kind of operation leaves JPAs
	 * first level cache and the database out of sync. Consider flushing the {@link EntityManager} before calling this
	 * method.
	 *
	 * @param entities entities to be deleted. Must not be {@literal null}.
	 * @deprecated Use {@link #deleteAllInBatch(Iterable)} instead.
	 */
	@Deprecated
	default void deleteInBatch(Iterable<T> entities){deleteAllInBatch(entities);}

	/**
	 * Deletes the given entities in a batch which means it will create a single query. This kind of operation leaves JPAs
	 * first level cache and the database out of sync. Consider flushing the {@link EntityManager} before calling this
	 * method.
	 *
	 * @param entities entities to be deleted. Must not be {@literal null}.
	 * @since 2.5
	 */
	void deleteAllInBatch(Iterable<T> entities);

	/**
	 * Deletes the entities identified by the given ids using a single query. This kind of operation leaves JPAs first
	 * level cache and the database out of sync. Consider flushing the {@link EntityManager} before calling this method.
	 *
	 * @param ids the ids of the entities to be deleted. Must not be {@literal null}.
	 * @since 2.5
	 */
	void deleteAllByIdInBatch(Iterable<ID> ids); **//아이디값이 리스트로 들어감**

	/**
	 * Deletes all entities in a batch call.
	 */
	void deleteAllInBatch();

	/**
	 * Returns a reference to the entity with the given identifier. Depending on how the JPA persistence provider is
	 * implemented this is very likely to always return an instance and throw an
	 * {@link javax.persistence.EntityNotFoundException} on first access. Some of them will reject invalid identifiers
	 * immediately.
	 *
	 * @param id must not be {@literal null}.
	 * @return a reference to the entity with the given identifier.
	 * @see EntityManager#getReference(Class, Object) for details on when an exception is thrown.
	 * @deprecated use {@link JpaRepository#getById(ID)} instead.
	 */
	@Deprecated
	T getOne(ID id);

	/**
	 * Returns a reference to the entity with the given identifier. Depending on how the JPA persistence provider is
	 * implemented this is very likely to always return an instance and throw an
	 * {@link javax.persistence.EntityNotFoundException} on first access. Some of them will reject invalid identifiers
	 * immediately.
	 *
	 * @param id must not be {@literal null}.
	 * @return a reference to the entity with the given identifier.
	 * @see EntityManager#getReference(Class, Object) for details on when an exception is thrown.
	 * @since 2.5
	 */
	T getById(ID id);

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.repository.query.QueryByExampleExecutor#findAll(org.springframework.data.domain.Example)
	 */
	@Override
	<S extends T> List<S> findAll(Example<S> example);

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.repository.query.QueryByExampleExecutor#findAll(org.springframework.data.domain.Example, org.springframework.data.domain.Sort)
	 */
	@Override
	<S extends T> List<S> findAll(Example<S> example, Sort sort);
}
```

해당 인터페이스에서는 전체에 대해서 리스트로 조회하는 메서드들이 존재하고 있고, 추가로 아이디로 특정 레코드만 조회할 수도 있었다. 뿐만아니라 삭제 저장도 지원하고 있었다

(~)flush()메서드: 현재 JPA 컨텍스트에서 갖고 있는 db값을 db에 반영하도록 지시

deleteInBatch(Iterable<T> entities) :iterable(list의 상위개념)로 받아서 한꺼번에 삭제

deleteAllInBatch() : 조건없이 해당 테이블에 대한 삭제

➕JpaRepository가 상속받은 PagingSortingRepository는 페이징 처리를 도와주는 인터페이스다

그다음은 CrudRepository 인터페이스를 살펴보자

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
package org.springframework.data.repository;

import java.util.Optional;

/**
 * Interface for generic CRUD operations on a repository for a specific type.
 *
 * @author Oliver Gierke
 * @author Eberhard Wolff
 * @author Jens Schauder
 */
@NoRepositoryBean
public interface CrudRepository<T, ID> extends Repository<T, ID> {

	/**
	 * Saves a given entity. Use the returned instance for further operations as the save operation might have changed the
	 * entity instance completely.
	 *
	 * @param entity must not be {@literal null}.
	 * @return the saved entity; will never be {@literal null}.
	 * @throws IllegalArgumentException in case the given {@literal entity} is {@literal null}.
	 */
	<S extends T> S save(S entity);

	/**
	 * Saves all given entities.
	 *
	 * @param entities must not be {@literal null} nor must it contain {@literal null}.
	 * @return the saved entities; will never be {@literal null}. The returned {@literal Iterable} will have the same size
	 *         as the {@literal Iterable} passed as an argument.
	 * @throws IllegalArgumentException in case the given {@link Iterable entities} or one of its entities is
	 *           {@literal null}.
	 */
	<S extends T> Iterable<S> saveAll(Iterable<S> entities);

	/**
	 * Retrieves an entity by its id.
	 *
	 * @param id must not be {@literal null}.
	 * @return the entity with the given id or {@literal Optional#empty()} if none found.
	 * @throws IllegalArgumentException if {@literal id} is {@literal null}.
	 */
	Optional<T> findById(ID id);

	/**
	 * Returns whether an entity with the given id exists.
	 *
	 * @param id must not be {@literal null}.
	 * @return {@literal true} if an entity with the given id exists, {@literal false} otherwise.
	 * @throws IllegalArgumentException if {@literal id} is {@literal null}.
	 */
	boolean existsById(ID id);

	/**
	 * Returns all instances of the type.
	 *
	 * @return all entities
	 */
	Iterable<T> findAll();

	/**
	 * Returns all instances of the type {@code T} with the given IDs.
	 * <p>
	 * If some or all ids are not found, no entities are returned for these IDs.
	 * <p>
	 * Note that the order of elements in the result is not guaranteed.
	 *
	 * @param ids must not be {@literal null} nor contain any {@literal null} values.
	 * @return guaranteed to be not {@literal null}. The size can be equal or less than the number of given
	 *         {@literal ids}.
	 * @throws IllegalArgumentException in case the given {@link Iterable ids} or one of its items is {@literal null}.
	 */
	Iterable<T> findAllById(Iterable<ID> ids);

	/**
	 * Returns the number of entities available.
	 *
	 * @return the number of entities.
	 */
	long count();

	/**
	 * Deletes the entity with the given id.
	 *
	 * @param id must not be {@literal null}.
	 * @throws IllegalArgumentException in case the given {@literal id} is {@literal null}
	 */
	void deleteById(ID id);

	/**
	 * Deletes a given entity.
	 *
	 * @param entity must not be {@literal null}.
	 * @throws IllegalArgumentException in case the given entity is {@literal null}.
	 */
	void delete(T entity);

	/**
	 * Deletes all instances of the type {@code T} with the given IDs.
	 *
	 * @param ids must not be {@literal null}. Must not contain {@literal null} elements.
	 * @throws IllegalArgumentException in case the given {@literal ids} or one of its elements is {@literal null}.
	 * @since 2.5
	 */
	void deleteAllById(Iterable<? extends ID> ids);

	/**
	 * Deletes the given entities.
	 *
	 * @param entities must not be {@literal null}. Must not contain {@literal null} elements.
	 * @throws IllegalArgumentException in case the given {@literal entities} or one of its entities is {@literal null}.
	 */
	void deleteAll(Iterable<? extends T> entities);

	/**
	 * Deletes all entities managed by the repository.
	 */
	void deleteAll();
}
```

CrudRepository 인터페이스에서는 저장, 전체저장, 삭제, 전체삭제,  조회, 전체 조회를 포함해서 아이디로 진행할 수 있는 crud를 지원해주는 것을 확인해볼 수 있다

🌟 findAll은 실제 서비스에서는 성능 이슈가 있어서 잘 사용하지는 않음

(힙에 데이터를 저장하면서 Out-of-memory가 될 수도 있음)

🌟	Optional<T> findById(ID id); 은 getOne과는 동작이 다름!(나중에 살펴볼것)

CrudRepository 인터페이스의 count() 메서드는 페이징 처리에 필요한 글의 갯수를 세는 것과 관련이 있을 것이다!

- Repository 인터페이스는 최상위 레포지토리임을 나타내주기 위한 인터페이스이고, 실제로 메서드에 대한 정의는 존재하지 않음