# JUnit5 , 스프링 부트 테스트 내용 추가

## 01. `@SpringBootTest`

- 통합 테스트 지원
- 단점: 설정된 빈 모두를 로드해서 어플리케이션 규모가 클수록 속도가 느려짐
- SpringBoot 1버전까지는 `@RunWith(SpringRunner.class)`를 붙여야 사용가능했으나, 2버전부터는 `@SpringBootTest`만 붙여도 적용 가능하다!

### 01-1. `@SpringBootTest 파라미터`

ⓐ value : 테스트 실행 전 적용할 프로퍼티 주입 가능

- 기존의 프로퍼티를 오버라이드

ⓑ properties : 테스트가 실행되기 전에 {key=value} 형식으로 프로퍼티 추가 가능

ⓒ classes : 어플리케이션 컨텍스트에 로드할 클래스 지정 가능

- 따로 지정하지 않으면 `@SpringBootConfiguration`을 찾아서 로드

ⓓ webEnvironment : 어플리케이션이 실행될 때의 웹 환경 설정 가능

- 기본값 : Mock 서블릿을 로드
- Mock 서블릿 : 개발 환경에 따라 다른 Mock(가짜 객체 주입) 서블릿 ㅗ한경의 어플리케이션 컨텍스트를 선택하여 로드되도록 하는 설정값

🌟 몇 가지 주의 사항🌟

- 프로파일 환경(개발, 운영환경, QA[Quality Assurance;품질 보증])마다 다른 data source를 갖는다면 `@ActiveProfiles("local")`과 같은 방식으로 `원하는 프로파일 환경값을 부여`
- `테스트에서 @Transactional을 사용하면 테스트를 마치고 나서 수정된 데이터가 롤백되지만, 테스트가 서버의 다른 스레드에서 실행중이면, WebEnvironment의 RANDOM_PORT 나 DEFINED_PORT를 사용하면 롤백되지 않는다!`
- `@SpringBootTest`는 `@SpringBootApplication`이나 `@SpringBootConfiguration` 중 하나가 반드시 있어야 한다![기본적으로 이 두 어노테이션을 찾기 때문]

/코드

## 02. `@WebMvcTest`

- MVC를 위한 테스트
- 웹에서 테스트하기 힘든 컨트롤러를 테스트하는 데 적합
- 시큐리티, 필터까지 자동으로 테스트하며 수동으로 추가/삭제까지 가능
- `@Controller`, `@ControllerAdvice(에러핸들링)` , `@JsonComponent`, `Filter` , `WebMvcConfigurer`, `HandlerMethodArgumentResolver`만 로드 ▶️ `@SpringBootTest보다 가볍게 테스트 가능`

테스트를 위해서 Book 클래스를 만들어보자

```java
package com.example.yaml_setting.domain;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.time.LocalDateTime;

@Data
@NoArgsConstructor
public class Book {
    private Integer idx;//인덱스
    private String title;//제목
    private LocalDateTime publishedAt;//출판일

    @Builder
    public Book(String title, LocalDateTime publishedAt){
        this.title=title;
        this.publishedAt=publishedAt;
    }

}
```

이번에는 `/books` 로 GET요청 시 현재 BookService 클래스에 책 목록을 요청하여 `bookList`라는 기본값으로 데이터 값을 넘기는 컨트롤러를 만들자!

(반환되는 뷰 이름: book)

그리고 BookService는 인터페이스인데, 이의 구현체는 만들지 않도록 하겠다!

```java
package com.example.yaml_setting.service;

import com.example.yaml_setting.domain.Book;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public interface BookService {
    List<Book> getBookList();
}
```

```java
package com.example.yaml_setting.controller;

import com.example.yaml_setting.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class BookController {

    @Autowired
    private BookService bookService;

    @GetMapping("/books")
    public String getBookList(Model model){
        model.addAttribute("bookList",bookService.getBookList());
        return "book";
    }

}
```

```java
package com.example.yaml_setting.controller;

import com.example.yaml_setting.domain.Book;
import com.example.yaml_setting.service.BookService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MockMvcBuilder;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultHandlers;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;

import java.time.LocalDateTime;
import java.util.Collections;

import static org.hamcrest.Matchers.contains;
import static org.mockito.BDDMockito.given;

@WebMvcTest(BookController.class)//1
class BookControllerTest {

    @Autowired
    private MockMvc mockMvc;//2

    @MockBean
    private BookService bookService;//3

    @Test
    public void bookMvcTest() throws Exception {
        Book book=new Book("Spring Boot Book",LocalDateTime.now());

        //4.메서드 반환값을 미리 정의
        given(bookService.getBookList()).willReturn(Collections.singletonList(book));

        //5.실행, 예측과 실제를 비교
        mockMvc.perform(
                MockMvcRequestBuilders.get("/books")
        ).andExpect(
                MockMvcResultMatchers.status().is2xxSuccessful()
        ).andExpect(
                MockMvcResultMatchers.view().name("book")
        ).andExpect(
                MockMvcResultMatchers.model().attributeDoesNotExist("bookList")
        ).andExpect(
                MockMvcResultMatchers.model().attribute("bookList",contains(book))
        ).andDo(
                MockMvcResultHandlers.print()
        );

    }
}
```

먼저 `given(bookService.getBookList()).willReturn(Collections.singletonList(book));` 를 확인해보자

- `given(가짜 결과를 만들 메서드).willReturn(반환값)` : `메서드의 결과값을 미리 정해둔 것`

-이때, given의 패키지는 `import static org.mockito.BDDMockito.given` 여야 한다!

- `MockMvcResultMatchers.view().name("book")` : 반환되는 뷰의 이름이 "book"인지 테스트
- `MockMvcResultMatchers.model().attributeDoesNotExist("bookList")` : 모델의 프로퍼티 중 "bookList"라는 프로퍼티가 존재하는지 테스트

▶️ 현재 bookList라는 프로퍼티가 가짜 객체를 통해 주입되었기 때문에, 이로 인한 경고가 확인될 것

- `MockMvcResultMatchers.model().attribute("bookList",contains(book))` : 모델의 프로퍼티 중 bookList라는 프로퍼티에 book 객체가 담겨져 있는지 테스트

-이때, contains의 패키지는 `import static org.hamcrest.Matchers.contains;` 여야 한다!

## 03. `@DataJpaTest`

- `JPA 관련 테스트 설정만 로드`
- 데이터 소스의 설정이 정상적인지 테스트
- JPA를 사용하여 데이터를 제대로 생성, 수정, 삭제하는지 등의 테스트가 가능
- 내장형 데이터베이스를 사용해서 실제 데이터베이스를 사용하지 않고 테스트 데이터베이스로 테스트 가능
- 기본적으로 `인메모리 임베디드 인터페이스`를 사용
- `@Entity` 클래스를 스캔해서 스프링 데이터 JPA 저장소(Spring Data JPA Repositories)를 구성
- 최적화한 별도의 데이터소스를 사용하고 싶다면 기본 설정된 데이터 소스를 사용하지 않도록 아래와 같이 `@AutoConfigureTestDatabase(replace=AutoConfigureTestDatabase.Replace.NONE)`을 적용해주면 된다!
- 테스트 데이터베이스는 `@AutoConfigureTestDatabae(connection=H2)` 와 같이 설정해서 사용가능하다!

```java
package com.example.yaml_setting.repository;

import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.test.context.ActiveProfiles;

import static org.junit.jupiter.api.Assertions.*;

@DataJpaTest
**@ActiveProfiles("...")
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)**
class BookRepositoryTest {

}
```

아니라면 application.yml에서 다음과 같이 변경해줘도 된다!

```yaml
spring:
  test:
    database:
      replace:
        NONE
```

기존의 Book을 엔티티로 만들어주자

```java
package com.example.yaml_setting.domain;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@Entity
@Table
public class Book {
    @Id
    @GeneratedValue
    private Integer idx;//인덱스
    @Column
    private String title;//제목
    @Column
    private LocalDateTime publishedAt;//출판일

    @Builder
    public Book(String title, LocalDateTime publishedAt){
        this.title=title;
        this.publishedAt=publishedAt;
    }

}
```

그리고 Dao에 해당하는 Repository로써 BookRepository를 만들어주자

```java
package com.example.yaml_setting.repository;

import com.example.yaml_setting.domain.Book;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookRepository extends JpaRepository<Book,Integer> {
}
```

### (부록)TestEntityManager

- DataJpaTest에서 EntityManager의 대체제로써 사용됨

[TestEntityManager (Spring Boot 2.5.3 API)](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/autoconfigure/orm/jpa/TestEntityManager.html)

ⓐ <E> E	persist(E entity)

- 인스턴스를 관리되고 지속적일 수 있도록 만들어줌

ⓑ void	flush()

- 지속성 컨텍스트를 기본 데이터베이스와 동기화

ⓒ <E> E	find(Class<E> entityClass, Object primaryKey)

- 기본키로 찾기

TestEntityManager를 이용해서 book 인스턴스를 관리하고 지속적일 수 있도록 설정해주자(persist)

```java
package com.example.yaml_setting.repository;

import com.example.yaml_setting.domain.Book;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;

import java.time.LocalDateTime;

import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.is;

@DataJpaTest
class BookRepositoryTest {

    private final static String BOOT_TEST_TITLE="Spring Boot Test Book";

    @Autowired
    private TestEntityManager testEntityManager;

    @Autowired
    private BookRepository bookRepository;

    @Test
    public void bookSaveTest(){
        Book book= Book.builder().title(BOOT_TEST_TITLE).publishedAt(LocalDateTime.now()).build();
        **testEntityManager.persist(book);**
        assertThat(bookRepository.getOne(book.getIdx()),is(book));
    }
}
```

이번에는 Book 인스턴스 3개를 저장하고, 그 갯수가 3개가 맞는지, 저장된 Book에 각 Book 객체가 모두 포함되어 있는지 테스트해보자

```java
package com.example.yaml_setting.repository;

import com.example.yaml_setting.domain.Book;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;

import java.time.LocalDateTime;
import java.util.List;

import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.*;

@DataJpaTest
class BookRepositoryTest {

    private final static String BOOT_TEST_TITLE="Spring Boot Test Book";

    @Autowired
    private TestEntityManager testEntityManager;

    @Autowired
    private BookRepository bookRepository;

    @Test
    public void bookSaveTest(){
        Book book= Book.builder().title(BOOT_TEST_TITLE).publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book);
        assertThat(bookRepository.getOne(book.getIdx()),is(book));
    }

    @Test
    **public void bookSaveSearchTest()**{
        Book book1= Book.builder().title(BOOT_TEST_TITLE+" 1").publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book1);
        Book book2= Book.builder().title(BOOT_TEST_TITLE+" 2").publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book2);
        Book book3= Book.builder().title(BOOT_TEST_TITLE+" 3").publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book3);

        **List<Book> bookList=bookRepository.findAll();
        assertThat(bookList,hasSize(3));**
        assertThat(bookList,contains(book1,book2,book3));
    }
}
```

이번에는 저장된 Book 중 2개가 제대로 삭제되는 지 테스트해보자

```java
package com.example.yaml_setting.repository;

import com.example.yaml_setting.domain.Book;
import org.assertj.core.util.Lists;
import org.hamcrest.collection.IsEmptyCollection;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;

import java.time.LocalDateTime;
import java.util.List;

import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.*;

@DataJpaTest
class BookRepositoryTest {

    private final static String BOOT_TEST_TITLE="Spring Boot Test Book";

    @Autowired
    private TestEntityManager testEntityManager;

    @Autowired
    private BookRepository bookRepository;

    @Test
    public void bookSaveTest(){
        Book book= Book.builder().title(BOOT_TEST_TITLE).publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book);
        assertThat(bookRepository.getOne(book.getIdx()),is(book));
    }

    @Test
    public void bookSaveSearchTest(){
        Book book1= Book.builder().title(BOOT_TEST_TITLE+" 1").publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book1);
        Book book2= Book.builder().title(BOOT_TEST_TITLE+" 2").publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book2);
        Book book3= Book.builder().title(BOOT_TEST_TITLE+" 3").publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book3);

        List<Book> bookList=bookRepository.findAll();
        assertThat(bookList,hasSize(3));
        assertThat(bookList,contains(book1,book2,book3));
    }

    @Test
    **public void bookListSaveAndDelete()**{
        Book book1= Book.builder().title(BOOT_TEST_TITLE+" 1").publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book1);
        Book book2= Book.builder().title(BOOT_TEST_TITLE+" 2").publishedAt(LocalDateTime.now()).build();
        testEntityManager.persist(book2);

        bookRepository.deleteAllInBatch();
        assertThat(bookRepository.findAll(),IsEmptyCollection.empty());
    }

}
```

## 04. `@RestClientTest`

- REST 관련 테스트를 도와줌
- REST 통신의 데이터형으로 사용되는 JSON 형식이 예상대로 응답을 반환하는지 등을 테스트 가능

먼저, REST 통신을 위해서 서비스단에서 URI를 만들어서 RestTemplate을 사용하자

```java
package com.example.yaml_setting.service;

import com.example.yaml_setting.domain.Book;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.net.URI;

@Service
public class BookRestService {

		private final RestTemplate restTemplate;

		public BookRestService(RestTemplateBuilder builder){
			this.restTemplate=builder.rootUri("/rest/test").build();
   }

    public Book getRestBook(){

        return restTemplate.getForObject("/rest/test",Book.class);
    }
}
```

이런 서비스단을 기반으로 REST 컨트롤러를 만들어보자

```java
package com.example.yaml_setting.controller;

import com.example.yaml_setting.domain.Book;
import com.example.yaml_setting.service.BookRestService;
import org.springframework.beans.factory.annotation.Autowired;
**import org.springframework.http.MediaType;**
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class BookRestController {

    @Autowired
    private BookRestService bookRestService;

    @GetMapping(value="/rest/test", **produces = {MediaType.APPLICATION_JSON_VALUE}**)
    public Book getRestBooks(){
        return bookRestService.getRestBook();
    }
}
```

```java
{
  "idx": null,
  "title": "테스트",
  "publishedAt": null
}
```

-위는 test용 json 데이터 test.json이다

서비스에 대해서 "/rest/test"에 요청을 던졌을 때,  응답이 test.json일 것이라고 예측하고

title값이 "테스트"인지 확인해보자

```java
package com.example.yaml_setting.service;

import com.example.yaml_setting.domain.Book;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.internal.junit.JUnitRule;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.client.RestClientTest;
import org.springframework.core.io.ClassPathResource;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.test.web.client.MockRestServiceServer;
import org.springframework.test.web.client.match.MockRestRequestMatchers;
import org.springframework.test.web.client.response.MockRestResponseCreators;

import static org.assertj.core.api.Assertions.assertThat;

@RestClientTest(BookRestService.class)
class BookRestServiceTest {

    @Autowired
    private BookRestService bookRestService;

    **@Autowired
    private MockRestServiceServer server;**

    @Test
    public void restTest(){
        server.expect(
                MockRestRequestMatchers.requestTo("/rest/test")
        ).andRespond(
                MockRestResponseCreators.withSuccess(new ClassPathResource("/test.json",getClass()), MediaType.APPLICATION_JSON)
        );

        Book book = this.bookRestService.getRestBook();
        assertThat(book.getTitle()).isEqualTo("테스트");
    }

}
```

먼저 JUnit4에서는 `@Rule`을 이용하는 방식이 있는데 JUnit5는 해당 방식을 사용하지 않고 위와 같이 진행이 가능하다!

[Rule로 지정한 필드값은 하나의 테스트 메서드가 끝날 때마다 정의한 값으로 초기화해줌]

ⓐ MockRestServiceServer : 클라이언트-서버 간 REST 테스트를 위한 객체

ⓑ expect: mockMvc의 perform과 유사한 기능!

ⓒ andRespond: 응답하기

`MockRestResponseCreators.withSuccess(new ClassPathResource("/test.json",getClass()), MediaType.APPLICATION_JSON)` - 성공 시, test.json을 가져와서 json 형태로 응답

ⓓ `assertThat(book.getTitle()).isEqualTo("테스트");` : c의 응답 중 title 값이 "테스트"와 일치하는지 비교

이번에는 에러를 발생시키고 이 에러가 HttpServerErrorException과 같은지 비교하도록 해보자

🌟 JUnit5에서는 4버전과 다르게, ExpectedException이 지원되지 않아, `assertThrows`를 이용하자

[JUnit 5 Expected Exception - assertThrows() Example](https://howtodoinjava.com/junit5/expected-exception-example/)

```java
package com.example.yaml_setting.service;

import com.example.yaml_setting.domain.Book;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.client.RestClientTest;
import org.springframework.core.io.ClassPathResource;
import org.springframework.http.MediaType;
import org.springframework.test.web.client.MockRestServiceServer;
import org.springframework.test.web.client.match.MockRestRequestMatchers;
import org.springframework.test.web.client.response.MockRestResponseCreators;
import org.springframework.web.client.HttpServerErrorException;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

@RestClientTest(BookRestService.class)
class BookRestServiceTest {

    @Autowired
    private BookRestService bookRestService;

    @Autowired
    private MockRestServiceServer server;

    @Test
    public void restTest(){
        server.expect(
                MockRestRequestMatchers.requestTo("/rest/test")
        ).andRespond(
                MockRestResponseCreators.withSuccess(new ClassPathResource("/test.json",getClass()), MediaType.APPLICATION_JSON)
        );

        Book book = this.bookRestService.getRestBook();
        assertThat(book.getTitle()).isEqualTo("테스트");
    }

    @Test
    public void restErrorTest(){
        server.expect(
                MockRestRequestMatchers.requestTo("/rest/test")
        ).andRespond(
                **MockRestResponseCreators.withServerError()**
        );

        **assertThrows(HttpServerErrorException.class,()->{
            bookRestService.getRestBook();
        });
    }**

}
```

- MockRestResponseCreators.withServerError() : 500 상태로 응답을 내려줌
- MockRestResponseCreators.withUnauthorizedRequest() : 401(UNAUTHORIZED) 상태로 응답을 내려줌
- MockRestResponseCreators.withBadRequest(): 400(BAD_REQUEST) 상태로 응답을 내려줌
- MockRestResponseCreators.withSuccess(Resource body, @Nullable MediaType contentType) : 200 상태로 Resource 기반 바디로 응답을 내려줌
- MockRestResponseCreators.withCreatedEntity(URI location) : 201(CREATED) 상태로 응답을 내려줌

[MockRestResponseCreators (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/client/response/MockRestResponseCreators.html#withServerError--)

## 05. `@JsonTest`

- json 테스트를 지원
- Gson과 Jackson의 테스트를 제공(각각 GsonTester와 JacksonTester를 사용)
- JSON 테스트 종류

(1) 문자열로 나열된 JSON 데이터 ▶️ 객체로 변환[역직렬화]

(2) 객체 ▶️ 문자열로 나열된 JSON 데이터[직렬화]

다시 한번! test.json

```java
{
  "idx": null,
  "title": "테스트",
  "publishedAt": null
}
```

```java
package com.example.yaml_setting;

import com.example.yaml_setting.domain.Book;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.json.JsonTest;
import org.springframework.boot.test.json.JacksonTester;

import java.io.IOException;

import static org.assertj.core.api.Assertions.assertThat;

@JsonTest
public class BookJsonTest {

    @Autowired
    private JacksonTester<Book> json;

    @Test
    public void jsonTest() throws IOException {
        Book book= Book.builder().title("테스트").build();//인자가 2개거나 없거나 해야할텐데 그래서 에러날것(cuz 생성자)

        String content="{\"title\":\"테스트\"}";

        assertThat(this.json.parseObject(content).getTitle()).isEqualTo(book.getTitle());
        assertThat(this.json.parseObject(content).getPublishedAt()).isNull();

        //각 필드를 변환한 문자열이 test.json파일에 정의한 내용과 일치하는지 테스트
        assertThat(this.json.write(book)).isEqualTo("/test.json");
        //title값이 있는지 확인
        assertThat(this.json.write(book)).hasJsonPathStringValue("title");
        //title값이 일치하는지 확인
        assertThat(this.json.write(book)).extractingJsonPathStringValue("title").isEqualTo("테스트");

    }
}
```

🌟public T parseObject(byte[] jsonBytes) : JSON 문자열 데이터 ▶️ 자바 객체(역직렬화)

🌟public JsonContent<T> write(T value) : 자바 객체 ▶️ JSON 문자열 데이터

[AbstractJsonMarshalTester (Spring Boot 2.5.3 API)](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/json/AbstractJsonMarshalTester.html#write-T-)

가. 여기는 역직렬화 결과를 이용

ⓐ `assertThat(this.json.parseObject(content).getTitle()).isEqualTo(book.getTitle());`

- 자바 객체로 바뀐 json 데이터로부터 title값을 가져와서 book 인스턴스의 title값과 같은지 확인

ⓑ `assertThat(this.json.parseObject(content).getPublishedAt()).isNull();`

- json 데이터로부터 자바 객체로 바뀐 결과에서 publishedAt값이 비어있는지 확인

나. 여기는 직렬화 결과를 이용

ⓒ `assertThat(this.json.write(book)).isEqualTo("/test.json");`

- 객체를 json 데이터로 바뀐 결과가 test.json파일에 정의한 내용과 일치하는지 테스트

ⓓ `assertThat(this.json.write(book)).hasJsonPathStringValue("title");`

- 객체를 json 데이터로 바뀐 결과에 title 필드가 있는지 확인

ⓔ `assertThat(this.json.write(book)).extractingJsonPathStringValue("title").isEqualTo("테스트");`

- 객체를 json 데이터로 바뀐 결과의 title 필드값을 추출하고, 그 값이 "테스트"와 일치하는지 확인