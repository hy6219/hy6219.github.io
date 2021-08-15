# 쿼리메서드 정의하기-페이징 처리

대체적으로 기본키와 같이 고유한 값으로 조회하는 것이 대부분이고, 리스트 형식으로 반환받는 경우가 많다! 이를 사이트에서는 접목해서 페이지로 보여주고 있다! 이를 연습해보자!

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

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;

/**
 * Extension of {@link CrudRepository} to provide additional methods to retrieve entities using the pagination and
 * sorting abstraction.
 *
 * @author Oliver Gierke
 * @see Sort
 * @see Pageable
 * @see Page
 */
@NoRepositoryBean
public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {

	/**
	 * Returns all entities sorted by the given options.
	 *
	 * @param sort
	 * @return all entities sorted by the given options
	 */
	Iterable<T> findAll(Sort sort);

	/**
	 * Returns a {@link Page} of entities meeting the paging restriction provided in the {@code Pageable} object.
	 *
	 * @param pageable
	 * @return a page of entities
	 */
	Page<T> findAll(Pageable pageable);
}
```

PagingAndSortingRepository 인터페이스를 참고해서 Pageable을 인자값으로 주는 메서드를 만들자

```java
package com.example.jpa_querymethod_paging.repository;

import com.example.jpa_querymethod_paging.domain.User;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Date;
import java.util.List;

public interface UserRepository extends JpaRepository<User,Long> {

    **Page<User> findByName(String name, Pageable pageable);**

}
```

그리고 Page를 추적하다보면, Slice라는 클래스가 나온다

Slice는 데이터 덩어리의 일부 묶음을 의미하는 클래스다!(현재 슬라이스의 값을 보여줌)

```java
/*
 * Copyright 2014-2021 the original author or authors.
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

import java.util.List;
import java.util.function.Function;

import org.springframework.core.convert.converter.Converter;
import org.springframework.data.util.Streamable;

/**
 * A slice of data that indicates whether there's a next or previous slice available. Allows to obtain a
 * {@link Pageable} to request a previous or next {@link Slice}.
 *
 * @author Oliver Gierke
 * @since 1.8
 */
public interface Slice<T> extends Streamable<T> {

	/**
	 * Returns the number of the current {@link Slice}. Is always non-negative.
	 *
	 * @return the number of the current {@link Slice}.
	 */
	int getNumber();

	/**
	 * Returns the size of the {@link Slice}.
	 *
	 * @return the size of the {@link Slice}.
	 */
	int getSize();

	/**
	 * Returns the number of elements currently on this {@link Slice}.
	 *
	 * @return the number of elements currently on this {@link Slice}.
	 */
	int getNumberOfElements();

	/**
	 * Returns the page content as {@link List}.
	 *
	 * @return
	 */
	List<T> getContent();

	/**
	 * Returns whether the {@link Slice} has content at all.
	 *
	 * @return
	 */
	boolean hasContent();

	/**
	 * Returns the sorting parameters for the {@link Slice}.
	 *
	 * @return
	 */
	Sort getSort();

	/**
	 * Returns whether the current {@link Slice} is the first one.
	 *
	 * @return
	 */
	boolean isFirst();

	/**
	 * Returns whether the current {@link Slice} is the last one.
	 *
	 * @return
	 */
	boolean isLast();

	/**
	 * Returns if there is a next {@link Slice}.
	 *
	 * @return if there is a next {@link Slice}.
	 */
	boolean hasNext();

	/**
	 * Returns if there is a previous {@link Slice}.
	 *
	 * @return if there is a previous {@link Slice}.
	 */
	boolean hasPrevious();

	/**
	 * Returns the {@link Pageable} that's been used to request the current {@link Slice}.
	 *
	 * @return
	 * @since 2.0
	 */
	default Pageable getPageable() {
		return PageRequest.of(getNumber(), getSize(), getSort());
	}

	/**
	 * Returns the {@link Pageable} to request the next {@link Slice}. Can be {@link Pageable#unpaged()} in case the
	 * current {@link Slice} is already the last one. Clients should check {@link #hasNext()} before calling this method.
	 *
	 * @return
	 * @see #nextOrLastPageable()
	 */
	Pageable nextPageable();

	/**
	 * Returns the {@link Pageable} to request the previous {@link Slice}. Can be {@link Pageable#unpaged()} in case the
	 * current {@link Slice} is already the first one. Clients should check {@link #hasPrevious()} before calling this
	 * method.
	 *
	 * @return
	 * @see #previousPageable()
	 */
	Pageable previousPageable();

	/**
	 * Returns a new {@link Slice} with the content of the current one mapped by the given {@link Converter}.
	 *
	 * @param converter must not be {@literal null}.
	 * @return a new {@link Slice} with the content of the current one mapped by the given {@link Converter}.
	 * @since 1.10
	 */
	<U> Slice<U> map(Function<? super T, ? extends U> converter);

	/**
	 * Returns the {@link Pageable} describing the next slice or the one describing the current slice in case it's the
	 * last one.
	 *
	 * @return
	 * @since 2.2
	 */
	default Pageable nextOrLastPageable() {
		return hasNext() ? nextPageable() : getPageable();
	}

	/**
	 * Returns the {@link Pageable} describing the previous slice or the one describing the current slice in case it's the
	 * first one.
	 *
	 * @return
	 * @since 2.2
	 */
	default Pageable previousOrFirstPageable() {
		return hasPrevious() ? previousPageable() : getPageable();
	}
}
```

위의 Slice 인터페이스를 살펴보면 부분집합에 대한 정보를 제공해줌을 알 수 있다

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

import java.util.Collections;
import java.util.function.Function;

/**
 * A page is a sublist of a list of objects. It allows gain information about the position of it in the containing
 * entire list.
 *
 * @param <T>
 * @author Oliver Gierke
 */
public interface Page<T> extends Slice<T> {

	/**
	 * Creates a new empty {@link Page}.
	 *
	 * @return
	 * @since 2.0
	 */
	static <T> Page<T> empty() {
		return empty(Pageable.unpaged());
	}

	/**
	 * Creates a new empty {@link Page} for the given {@link Pageable}.
	 *
	 * @param pageable must not be {@literal null}.
	 * @return
	 * @since 2.0
	 */
	static <T> Page<T> empty(Pageable pageable) {
		return new PageImpl<>(Collections.emptyList(), pageable, 0);
	}

	/**
	 * Returns the number of total pages.
	 *
	 * @return the number of total pages
	 */
	int getTotalPages();

	/**
	 * Returns the total amount of elements.
	 *
	 * @return the total amount of elements
	 */
	long getTotalElements();

	/**
	 * Returns a new {@link Page} with the content of the current one mapped by the given {@link Function}.
	 *
	 * @param converter must not be {@literal null}.
	 * @return a new {@link Page} with the content of the current one mapped by the given {@link Function}.
	 * @since 1.10
	 */
	<U> Page<U> map(Function<? super T, ? extends U> converter);
}
```

그리고 Page 인터페이스를 보면 부분집합에 대한 정보 뿐 아니라 전체 페이지 수 등과 같은 전체적인 정보를 제공해주고 있음을 알 수 있다

➕ Pageable은 페이지에 대한 요청값! Page는 페이지에 대한 응답값!

```java
package com.example.jpa_querymethod_paging.repository;

import com.example.jpa_querymethod_paging.domain.User;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void pagingTest(){
        Page<User> page =userRepository.findByName("martin",PageRequest.of(0,1,Sort.by(Sort.Order.desc("id"))));
        System.out.println("content: "+page.getContent());
        System.out.println("totalPages: "+page.getTotalPages());
    }

}
```

[TIL/Repository Interface 메서드 실습(2).md at main · hy6219/TIL](https://github.com/hy6219/TIL/blob/main/Spring/JPA/JPA%20Repository/Repository%20Interface%20%EB%A9%94%EC%84%9C%EB%93%9C%20%EC%8B%A4%EC%8A%B5(2).md)

지난 기록에서 페이징 처리 관련 메서드인 of 를 정리해본 것을 토대로 했을 때, 우선 id에 대해서 내림차순 정렬한 데이터를 기반으로, 한 페이지에 1개 게시글만 오도록(size)하였을 때 첫번째 페이지를 요청한 결과를 findByName 메서드에서 담당하고 있음을 보여주고 있다! 

```java
Hibernate: 
    select
        user0_.id as id1_0_,
        user0_.active as active2_0_,
        user0_.created_at as created_3_0_,
        user0_.email as email4_0_,
        user0_.name as name5_0_,
        user0_.updated_at as updated_6_0_ 
    from
        user user0_ 
    where
        user0_.name=? 
    order by
        user0_.id desc limit ?
Hibernate: 
    select
        **count(user0_.id)** as col_0_0_ 
    from
        user user0_ 
    where
        user0_.name=?
content: [User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-15 23:05:00.55, updatedAt=2021-08-15 23:05:00.55, active=true)]
totalPages: 2
```

이를 기반으로 ORDER BY절과 limit이 붙었음을 확인해볼 수 있었고

getTotalPages메서드와 관련해서 아래의 COUNT 함수가 한 번 조회되었음을 확인해볼 수 있었다(getTotalElements도 유사하다)

즉, 위의 경우에는 5번 마틴을 확인해볼 수 있다