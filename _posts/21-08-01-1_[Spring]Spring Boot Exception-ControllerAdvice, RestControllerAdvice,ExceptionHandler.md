# Spring Boot Exception 처리

📌 Web Application의 입장에서 바라보았을 때, 에러가 발생했을 대 이에 대응하기 위한 방법 📌

1. 에러페이지
2. 4XX Error 혹은 5XX에러 발생시키기
3. 클라이언트가 200외에 처리를 하지 못할 경우, 200을 내려주고 별도의 에러 메시지 전달

웹 어플리케이션은 한 곳에서 에러를 모아서 throws나 try-catch등으로 처리하는 것이 편하다! 이러한 방법들이 스프링에서 준비되어 있다!

🌟 **Exception 처리 방법** 🌟

[Exception 처리 방법](https://www.notion.so/0ad4f81f2e8c4761b84dfa23e19464d6)

📌 잠깐!  뷰 리졸버와 관련해서 스프링 MVC의 핵심 구성 요소를 살펴보자

![http://springcert.sourceforge.net/images/dispatchservlet.jpg](http://springcert.sourceforge.net/images/dispatchservlet.jpg)

source: [http://springcert.sourceforge.net/2.5/9-study-web-app.html](http://springcert.sourceforge.net/2.5/9-study-web-app.html)

0) DispatcherServlet이 모든 연결을 담당

1) 클라이언트(웹 브라우저)에서 요청이 들어오면, Handler Mapping 객체에게 적절한 컨트롤러를 검색할 것을 요청

2) Handler Mapping 객체는 적절한 컨트롤러를 검색하여 Dispatcher Servlet에게 전달

3) Dispatcher Servlet은 HandlerAdapter 객체에게 요청 처리를 위임

4) HandlerAdapter 객체는 컨트롤러의 적절한 메서드를 호출해서 요청을 처리하고, 반환 받은 결과를 ModelAndView 객체에 담아서 Dispatcher Servlet에 리턴

5) Dispatcher Servlet은 View Resolver 객체를 이용해서 응답으로 내려줄 뷰 객체를 찾거나 생성해서 Dispatcher Resolver에게 전달

6) Dispatcher Servlet은 전달받은 뷰 객체에게 응답 결과 생성을 요청

7) 뷰 객체는 클라이언트에게 전송할 응답 결과를 생성

참조: 

[https://tinkerbellbass.tistory.com/40](https://tinkerbellbass.tistory.com/40)

---

먼저,  사용자 객체를 만들 것인데, int는 null이 0이므로, Integer로 처리하기로 한다

사용자 객체에 필요한 필드는

- String 이름(size제약 : 최소 1, 최대 100, Not Empty 제약)
- Integer 나이(최솟값 제약:1 , NotNull 제약)

이다

```java
package com.example.exception.dto;

import javax.validation.constraints.Min;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

public class User {
    @NotEmpty//null,""불가
    @Size(min=1, max=100)
    private String name;
    @Min(value=1)
    @NotNull
    private Integer age;

    public User() {
    }

    public User(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

그리고 이를 이용해서 RestApiController에서는 일부러 get메서드에 대해서 예외를 만들기 위해서

🌹 get 메서드

- String name과 Integer age를 쿼리 파라미터로 받되
- required 옵션을 부여하고 그 값을 false로 부여하면, 값이 없더라도 정상동작은 가능하지만, null값이 들어가게 되도록 만들자
-required 옵션의 값이 true이면, 값이 없으면 exception을 띄워주게 된다
- 그리고 들어온 값을 user 객체에 넣어주고
- int a=age+10이라는 부분을 일부러 넣어서 null pointer exception을 만들어주자

위와 같이 상황을 만들고

post 메서드는 별다른 상황 없이 우선은, 에코처리해주도록 하자

```java
package com.example.exception.controller;

import com.example.exception.dto.User;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;

@RestController
@RequestMapping("/api")
public class RestApiController {

    //get이기 때문에 따로 메서드를 지정하지 않도록 할 것
    @RequestMapping("/user")
    public User get(@RequestParam(required=false) String name, @RequestParam(required=false) Integer age){
        User user=new User();
        user.setName(name);
        user.setAge(age);

        //required 옵션은 해당 파라미터값이 없어도 동작은 가능하나,
        //해당 인자의 값은 null이 될 것
        int a = 10+age;//age값이 들어오지 않으면 null값으로 뜨게 될 것

        return user;
    }

    @PostMapping("/")
    public User post(@Valid @RequestBody User user){
        System.out.println(user);
        return user;
    }

}
```

그리고 talend에서 요청을 [http://localhost:8089/api/user?name&age](http://localhost:8089/api/user?name=&age=)와 같이 값을 넣어주지 않으면

```java
2021-08-01 04:32:45.380 ERROR 1396 --- [nio-8089-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is java.lang.NullPointerException] with root cause

java.lang.NullPointerException: null
	at com.example.exception.controller.RestApiController.get(RestApiController.java:21) ~[main/:na]
(중략)

2021-08-01 04:32:54.657 ERROR 1396 --- [nio-8089-exec-3] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is java.lang.NullPointerException] with root cause

java.lang.NullPointerException: null
(중략)
```

위와 같이 java.lang.NullPointerException이 get 메서드에서 발생해서 에러가 발생했음을 확인해볼 수 있다(500 상태코드 즉, 서버 오류 코드와 함께!◀️ int a=~부분에서 내부적으로 오류가 발생)

이렇게, 스프링은 500 상태를 띄우고 e.printStackTrace()와 같은 에러 표시를 하는  기본적인 예외처리를 해준다

이번에는 post 메서드를 이용해보자

[http://localhost:8089/api](http://localhost:8089/api) 로

```java
{
  "name":"null",
  "age":"null"
}
```

위와 같이 null값을 보내게 되면, 400 에러코드와 함께

```java
2021-08-01 04:46:19.042  WARN 47532 --- [nio-8089-exec-1] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.web.bind.MethodArgumentNotValidException: Validation failed for argument [0] in public com.example.exception.dto.User com.example.exception.controller.RestApiController.post(com.example.exception.dto.User): [Field error in object 'user' on field 'age': rejected value [null]; codes [NotNull.user.age,NotNull.age,NotNull.java.lang.Integer,NotNull]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [user.age,age]; arguments []; default message [age]]; default message [널이어서는 안됩니다]] ]
```

위와 같이 BadRequest임을 표시해준다(검증에 실패-문법상 오류)

이를 먼저 ControllerAdvice를 이용해서 접근해보자

**1.ControllerAdvice + ExceptionHandler**

📌 ControllerAdvice로 예외처리

- RestController에 적용할 것이면 @RestControllerAdvice를
- 페이지컨트롤러/뷰리졸버 에 적용할 것이면 @ControllerAdvice를 이용

지금은 RestController,에 대해서 적용할 것이고, 상태를 이용해서 처리를 해줄 것이기 때문에 ResponseEntity 타입을 반환해주는 메서드를 만드는데

- 모든 예외에 대해서(Exception.class)
- 500에러와 함께 body에는 ""(비어있는 값)을 실어주도록

할 것이다

그리고, 매개변수로 Exception을 받으면 이를 이용해서 처리를 해줄 수 있는데 우선 이를 이용해서 에러 메시지를 출력해주자

```java
package com.example.exception.advice;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalControllerAdvice {

    @ExceptionHandler(value=**Exception.class**)
    public ResponseEntity exception(Exception e){
				System.out.println("---");
        System.out.println(**e.getLocalizedMessage()**);
				System.out.println("---");
        return **ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("")**;
    }

}
```

그리고 이번에는 post 요청으로

```java
{
  "name":"null",
  "age":"null"
}
```

저번과 동일한 요청을 보내면 이번에는

500 상태코드가 발생하고, Body에는 No Content임을 확인해볼 수 있다

[Bad Request일 때에는 Body에 기본 메시지가 표시됐었다]

그리고 이번에는 내가 만든 ExceptionHandler를 통해서 에러 메시지를 콘솔에서 확인할 수 있다

```java
---
Validation failed for argument [0] in public com.example.exception.dto.User com.example.exception.controller.RestApiController.post(com.example.exception.dto.User): [Field error in object 'user' on field 'age': rejected value [null]; codes [NotNull.user.age,NotNull.age,NotNull.java.lang.Integer,NotNull]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [user.age,age]; arguments []; default message [age]]; default message [널이어서는 안됩니다]] 
---
```

이번에는 특정 예외에 대해서 처리를 해주기 위해서

예외 이름을 출력해주자

```java
package com.example.exception.advice;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
        **System.out.println(e.getClass().getSimpleName());**
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

}
```

요청도 저번과 동일하게 보내면, talend에서 확인되는 상태코드와 body 부분은 동일하지만, 콘솔에는 추가적으로

📌**MethodArgumentNotValidException**📌

이 출력되어, 검증과 관련된 에러가 메서드에서 발생했음을 확인해볼 수 있다

그리고 이에 대해 처리할 수 있는 메서드를 MethodArgumentNotValidException을 이용해서 만들어주자

```java
package com.example.exception.advice;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

    //MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e){

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }
}
```

그러면 이번에는 Bad Request이기 때문에 400에러와 함께 body부분에

```java
Validation failed for argument [0] in public com.example.exception.dto.User com.example.exception.controller.RestApiController.post(com.example.exception.dto.User): [Field error in object 'user' on field 'age': rejected value [null]; codes [NotNull.user.age,NotNull.age,NotNull.java.lang.Integer,NotNull]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [user.age,age]; arguments []; default message [age]]; default message [널이어서는 안됩니다]]
```

가 출력되는 것을 확인해볼 수 있다

이를 이용한다면 NullPointerException의 경우, e.getMessage()는 null이 출력된 것이기 때문에 특정 메시지를 출력하도록 해준다면

```java
package com.example.exception.advice;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

    //MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e){

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    //NullPointerException
    @ExceptionHandler(value=NullPointerException.class)
    public ResponseEntity nullPointerException(NullPointerException e){
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("인자값을 확인해주세요");
    }
}
```

위와 같이 대비할 수 있고, 그러면 400 에러와 함께 "인자값을 확인해주세요" 메시지를 확인해볼 수 있다!

그런데, 만약  global하게 대응하지 않고, 특정 패키지 하위 클래스에서 발생하는 예외를 잡고 싶다면 

RestControllerAdvice 어노테이션의 **basePackages**속성을 이용하면 지정이 가능하다!

```java
package com.example.exception.advice;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

**@RestControllerAdvice(basePackages = "com.example.controller")**
public class GlobalControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

    //MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e){

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    //NullPointerException
    @ExceptionHandler(value=NullPointerException.class)
    public ResponseEntity nullPointerException(NullPointerException e){
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("인자값을 확인해주세요");
    }
}
```

**2.ExceptionHandler**

🌟 그런데, 컨트롤러가 RestApiController외에도 다양한 컨트롤러가 있는데, RestApiController에만 적용하고 싶다면 위의 방식(**@RestControllerAdvice(basePackages = "com.example.controller")**)도 좋지만,

해당 컨트롤러에 **ExceptionHandler**를 이용해서 아래와 같이 처리해주는 것을 추가해줘도 좋다!

특히, global과 따로 특정 클래스에 대한 핸들러를 만들어 지정해줄 필요가 있을 경우 이 방식이 유용할 것 같다!

```java
package com.example.exception.controller;

import com.example.exception.dto.User;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;

@RestController
@RequestMapping("/api")
public class **RestApiController** {

    //get이기 때문에 따로 메서드를 지정하지 않도록 할 것
    @RequestMapping("/user")
    public User get(@RequestParam(required=false) String name, @RequestParam(required=false) Integer age){
        User user=new User();
        user.setName(name);
        user.setAge(age);

        //required 옵션은 해당 파라미터값이 없어도 동작은 가능하나,
        //해당 인자의 값은 null이 될 것
        int a = 10+age;//age값이 들어오지 않으면 null값으로 뜨게 될 것

        return user;
    }

    @PostMapping("")
    public User post(@Valid @RequestBody User user){
        System.out.println(user);
        return user;
    }
    //MethodArgumentNotValidException에 대해서 에러 처리
    **@ExceptionHandler(value= MethodArgumentNotValidException.class)**
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e){
				System.out.println("rest api");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    //NullPointerException
    **@ExceptionHandler(value=NullPointerException.class)**
    public ResponseEntity nullPointerException(NullPointerException e){
        System.out.println("rest api");
				return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("인자값을 확인해주세요");
    }
}
```

그러면 지금은 GlobalControllerAdvice와 RestController 내 EventHandler 중 어떤 것이 우선시 될까?

Global부분을 아래처럼 수정해보자

```java
package com.example.exception.advice;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

    //MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e){
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    //NullPointerException
    @ExceptionHandler(value=NullPointerException.class)
    public ResponseEntity nullPointerException(NullPointerException e){
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("인자값을 확인해주세요");
    }
}
```

그러면 global보다 🌟 **RestApiController 내부에서 선언했던 핸들러가 우선시**🌟되어 동작하는 것을 확인해볼 수 있다!

더불어서 RestApiController 내부의 핸들러가 작동하게 되면

global로 선언해두었던 핸들러는 자연스럽게 작동하지 않게 됨을 확인해볼 수 있다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Exception/%EB%8B%A4%EC%96%91%ED%95%9C%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%20%EC%A4%91%20%ED%8A%B9%EC%A0%95%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%EC%97%90%20%EB%8C%80%ED%95%9C%20%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Exception/%EB%8B%A4%EC%96%91%ED%95%9C%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%20%EC%A4%91%20%ED%8A%B9%EC%A0%95%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%EC%97%90%20%EB%8C%80%ED%95%9C%20%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC.PNG?raw=true)

(UserController는 동일 내용이 들어가면 충돌될 수 있기 때문에 우선은 비워두었다. 물론 내용을 다르게 구성할 수 있지만 중요한 부분이 아니기 때문에 패스)