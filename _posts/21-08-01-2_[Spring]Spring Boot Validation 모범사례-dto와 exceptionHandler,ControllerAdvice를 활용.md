# Spring Boot Validation을 통한 모범사례

- 클라이언트가 잘 활용할 수 있도록 접근!

위에서 연습했던 예제를 바탕으로 접근해보자

(RestApiController하단에 작성했던 ExceptionHandler부분은 지우고 시작하자)

전역처리 방식과 특정 api에서 적용하는 방식이 존재하는데, 두 방식이 모두 적용될 수도 있기 때문에 아래와 같이 GlobalControllerAdvice.java에서

**@RestControllerAdvice(basePackageClasses=RestApiController.class)**  로 수정해줌으로써 해당 클래스에만 적용할 수 있도록 변경해주자!

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

**@RestControllerAdvice(basePackageClasses = RestApiController.class)**
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

이제는 전역으로 작동하지 않고, RestApiController에서만 작동하는 ControllerAdvice가 되었다! 그렇기 때문에 의미에 맞게 ApiControllerAdvice로 Rename해주자

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class **ApiControllerAdvice** {

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

그리고 아래와 같이 전체 예외에 대한 확인을 진행하는 exception메서드에서 예외 이름만 확인하도록 변경해주자

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class ApiControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
/*        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");*/
        **System.out.println("error: "+e.getClass().getSimpleName());**
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

Post에 대해서만 validation을 계속 진행했었는데, get을 이용했을 때 validation을 하려면 어떻게 접근할까?

(get은 queryParameter를 받아서 진행하는데, dto객체로 따로 검증 기준을 미리 준비해둔 것이 아니라서, Valid를 붙여서 진행하기 어려웠다!)

▶️ 🌟 컨트롤러 단위에 **@Validated** 를 붙여주자!

그리고 **파라미터에 대해서 검증 기준 어노테이션**을 붙여주자!

- String name에 대해서는 길이가 최소 2
- Integer age에 대해서는 NotNull, 최솟값 1

제약을 걸어주자!

그리고 int a=age 부분도 지워서 충분히 validation에 집중해보자(이부분은 일부러 null pointer exception을 유발하기 위해 넣었던 부분이었다)

그러면 required=false로 에러를 발생시키는 부분을 없애도 좋다!(어차피 검증을 할 것이기 때문!)

```java
package com.example.exception.controller;

import com.example.exception.dto.User;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

@RestController
@RequestMapping("/api")
**@Validated**
public class RestApiController {

    //get이기 때문에 따로 메서드를 지정하지 않도록 할 것
    @RequestMapping("/user")
    public User get(
                    **@Size(min=2)**
                    @RequestParam String name,
                    **@NotNull
										@Min(value=1)**
                    @RequestParam Integer age){
        User user=new User();
        user.setName(name);
        user.setAge(age);

        //required 옵션은 해당 파라미터값이 없어도 동작은 가능하나,
        //해당 인자의 값은 null이 될 것
      //  int a = 10+age;//age값이 들어오지 않으면 null값으로 뜨게 될 것

        return user;
    }

    @PostMapping("")
    public User post(@Valid @RequestBody User user){
        System.out.println(user);
        return user;
    }

}
```

이제 [http://localhost:8089/api/user?name&age](http://localhost:8089/api/user?name&age) 로 요청을 보내보면

콘솔에서 ControllerAdivce에 의해서 에러가 "error: MissingServletRequestParameterException"임을 확인해볼 수 있다

이번에는 서버에 [http://localhost:8089/api/user?name=steve&age=20](http://localhost:8089/api/user?name=steve&age=20) 로 요청을 보내보면

```java
{
"name": "steve",
"age": 20
}
```

위와 같이 json 형태로 응답이 찍히는 것을 확인해볼 수 있다(with 200 OK! 🙆‍♂️)

그런데 아까 상황처럼 파라미터값이 들어오지 않아서 검증에 실패한 경우, 스프링에서 대응한 기본적인 예외 외에 예외 처리를 해주려면 어떻게 할까?

▶️ 가장 먼저 드는 생각은 ApiControllerAdvice에 MissingServletRequestParameterException 처리 메서드를 추가해주자

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.MissingServletRequestParameterException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class ApiControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
/*        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");*/
        System.out.println("error: "+e.getClass().getSimpleName());
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

    **@ExceptionHandler(value= MissingServletRequestParameterException.class)
    public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e)**{
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("파라미터로 전달될 값이 존재하지 않습니다. 확인부탁드립니다");
    }

}
```

그리고 이번에는 [http://localhost:8089/api/user?name&age](http://localhost:8089/api/user?name&age) 로 요청을 보내면 400 상태와 함께

예외 처리된 메시지를 확인해볼 수 있게 된다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/MissingServletException%EC%97%90%20%EB%8C%80%ED%95%9C%20%EC%B2%98%EB%A6%AC.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/MissingServletException%EC%97%90%20%EB%8C%80%ED%95%9C%20%EC%B2%98%EB%A6%AC.PNG?raw=true)

이번에는 [http://localhost:8089/api/user?name=0&age=1](http://localhost:8089/api/user?name=0&age=1) 로 요청을 보내보자

(정확한 예외이름을 확인하기 전에 드는 생각은, 검증기준에 위배되므로 서버 내부 에러로 500이 표시될 것이다라고 생각이 든다)

그러면 우리가 검증에 실패하였기 때문에, 검증은 Validator로 수행되었기 때문에 "error: ConstraintViolationException"를 콘솔에서 확인해볼 수 있게 된다

이 부분도 어떻게 예외처리를 해줄까? 당연히!! ControllerAdvice 부분에 ExceptionHandler를 추가등록해주어야 한다!

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.MissingServletRequestParameterException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.validation.ConstraintViolationException;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class ApiControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
/*        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");*/
        System.out.println("error: "+e.getClass().getSimpleName());
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

    @ExceptionHandler(value= MissingServletRequestParameterException.class)
    public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e){
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("파라미터로 전달될 값이 존재하지 않습니다. 확인부탁드립니다");
    }

    ***@ExceptionHandler(value=ConstraintViolationException.class)
    public ResponseEntity constraintViolationException(ConstraintViolationException e){***
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("ConstraintViolationException 발생");
    }

}
```

우선은 지금은 편의상 대부분 Bad Request로 내려주겠다!

그러면 더이상 500상태가 아닌 400 Bad Request 상태와 함께 "ConstraintViolationException 발생"이라는 표시가 body부분에 담겨져서 표현된다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/ConstraintViolationException%EC%97%90%20%EB%8C%80%ED%95%9C%20%EC%B2%98%EB%A6%AC.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/ConstraintViolationException%EC%97%90%20%EB%8C%80%ED%95%9C%20%EC%B2%98%EB%A6%AC.PNG?raw=true)

그리고 이렇게 get방식으로 접근하면 조회를 하게 되어서, post나 put처럼 body를 요구하지 않기 때문에, api tester가 아닌 페이지에서 white label이 뜨지 않고 아래처럼 내용이 표시된다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/validated%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20validation_get%20method%EB%A1%9C%20%EC%A0%91%EA%B7%BC_ControllerAdvice.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/validated%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20validation_get%20method%EB%A1%9C%20%EC%A0%91%EA%B7%BC_ControllerAdvice.PNG?raw=true)

지금까지는 단순히 validation을 하고 이러한 과정 중 발생하는 예외에 대한 처리를 해주는 일반적인 부분으로, 이전 학습내용과 동일하다

📌추가해줄 부분은 아래와 같다! 집중하자!📌

🌹 ConstraintViolationException 부분🌹

먼저, 해당 예외클래스에서 제공되는 메서드인 getConstraintViolations()를 통해서 에러를 확인해보자!

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.MissingServletRequestParameterException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.validation.ConstraintViolationException;
import java.lang.reflect.Field;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class ApiControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
/*        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");*/
        System.out.println("error: "+e.getClass().getSimpleName());
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

    @ExceptionHandler(value= MissingServletRequestParameterException.class)
    public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e){
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("파라미터로 전달될 값이 존재하지 않습니다. 확인부탁드립니다");
    }

    @ExceptionHandler(value=ConstraintViolationException.class)
    public ResponseEntity constraintViolationException(ConstraintViolationException e){
        **e.getConstraintViolations().forEach(error->{
            System.out.println(error);
        });**
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("ConstraintViolationException 발생");
    }

}
```

[https://docs.oracle.com/javaee/7/api/javax/validation/ConstraintViolationException.html](https://docs.oracle.com/javaee/7/api/javax/validation/ConstraintViolationException.html)

getConstraintViolations() 메서드는 오라클 API를 확인해본 결과, 검증 과정에서 보고된 제약집합을 Set<ConstraintViolation<>>으로 반환해주는 메서드이다

(궁금해서 찾아보게 되었다!)

이번에는 [http://localhost:8089/api/user?name=0&age=0](http://localhost:8089/api/user?name=0&age=0) 로 요청을 보내서 두 파라미터에 대해서 모두 위배되도록 해보자

그러면 콘솔에는

```java
ConstraintViolationImpl{interpolatedMessage='크기가 2에서 2147483647 사이여야 합니다', propertyPath=get.name, rootBeanClass=class com.example.exception.controller.RestApiController, messageTemplate='{javax.validation.constraints.Size.message}'}
ConstraintViolationImpl{interpolatedMessage='1 이상이어야 합니다', propertyPath=get.age, rootBeanClass=class com.example.exception.controller.RestApiController, messageTemplate='{javax.validation.constraints.Min.message}'}
```

위와 같이 size와 min에 대한 위배 사항이 출력됨을 확인해볼 수 있다

return 부분에 브레이크 포인트를 걸고 e에 담긴 값을 확인해보자

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/ConstraintViolationException%20%EB%B0%9C%EC%83%9D%EC%8B%9C%20%ED%99%95%EC%9D%B8%ED%95%B4%EB%B3%BC%EC%88%98%20%EC%9E%88%EB%8A%94%20%EA%B0%92%20%ED%99%95%EC%9D%B8-%EB%94%94%EB%B2%84%EA%B9%85.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/ConstraintViolationException%20%EB%B0%9C%EC%83%9D%EC%8B%9C%20%ED%99%95%EC%9D%B8%ED%95%B4%EB%B3%BC%EC%88%98%20%EC%9E%88%EB%8A%94%20%EA%B0%92%20%ED%99%95%EC%9D%B8-%EB%94%94%EB%B2%84%EA%B9%85.PNG?raw=true)

그러면 여러가지 값들이 있는데 그중에서도 위의 콘솔에서 확인된 값이 담긴 constraintViolations를 확인해보면, 메시지는 interpolatedMessage라는 곳에 담겨있고, 잘못된 값(value)는 0이고, propertyPath는 값을 얻는 경로인 것 같은데 이는 get.Age이며, 메시지 템플릿은 javax.validation.constraints.Min.message에 기인한다는 것을 확인해볼 수 있다[이부분은 defaultMessage와 관련이 있을 것 같다]

name도 비슷한 구성의 에러를 확인해볼 수 있다

그리고 추가로 get.Age가 담긴 propertyPath를 펼쳐보면 

```java
[0]:""
[1]:"get"
[2]:"age"
```

과 같이 담겨있는 것으로 보아, 잘못된 필드를 확인하기 위해서는 맨 마지막을 참조하면 될 듯 싶다

(그 외에도 currentLeafNode에도 age가 담겨있어서 이를 활용해도 괜찮을 것 같다)

그런데, getPropertyPath는 Path를 반환해주는데, 이를 Path.Node로 받아서 iterator를 이용해서 필드값을 가져와줄 수 있다

[https://stackoverflow.com/questions/36555057/get-field-name-when-javax-validation-constraintviolationexception-is-thrown](https://stackoverflow.com/questions/36555057/get-field-name-when-javax-validation-constraintviolationexception-is-thrown)

그리고 leafNode로 필드값을 접근하려면

조금더 간단한데

String field=error.getLeafBean().toString();

와 같이 처리해줄 수 있다

하지만, 이방식은 public interface ConstraintViolation<T>을 참조해보면 getLeafBean이  Object타입인데 이를 오버라이딩해주지 않았으므로

주소값이 출력된다

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.MissingServletRequestParameterException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.validation.ConstraintViolationException;
import javax.validation.Path;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class ApiControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
/*        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");*/
        System.out.println("error: "+e.getClass().getSimpleName());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

    //MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e){
        BindingResult bindingResult=e.getBindingResult();
        bindingResult.getAllErrors().forEach(error->{
            FieldError fieldError=(FieldError)error;
            String field=fieldError.getField();
            String msg = fieldError.getDefaultMessage();
            String value=fieldError.getRejectedValue().toString();//어떤 값들이 잘못 들어갔는지 확인

            System.out.println("필드: "+field);
            System.out.println("메시지: "+msg);
            System.out.println("잘못 들어간 값: "+value);
            System.out.println("---");
        });
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    //NullPointerException
    @ExceptionHandler(value=NullPointerException.class)
    public ResponseEntity nullPointerException(NullPointerException e){
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("인자값을 확인해주세요");
    }

    @ExceptionHandler(value= MissingServletRequestParameterException.class)
    public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e){

        String fieldName=e.getParameterName();
        String fieldType=e.getParameterType();
        String msg=e.getMessage();

        System.out.println("fieldName: "+fieldName);
        System.out.println("fieldType: "+fieldType);
        System.out.println("error message: "+msg);
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    @ExceptionHandler(value=ConstraintViolationException.class)
    public ResponseEntity constraintViolationException(ConstraintViolationException e){
        **System.out.println("error: "+e.getClass().getSimpleName());
        e.getConstraintViolations().forEach(error->{
           //https://stackoverflow.com/questions/36555057/get-field-name-when-javax-validation-constraintviolationexception-is-thrown
            Path path= error.getPropertyPath();
            String field=null;

            for(Path.Node node:path){
                field=node.getName();//맨 마지막 값이 남게 될것
            }
            //String field=error.getLeafBean().toString();로 접근할 수도 있음-leafBean오르 접근시
            String msg=error.getMessage();
            String msgTemplate = error.getMessageTemplate();

            String invalidValue=error.getInvalidValue().toString();//잘못된 값

            System.out.println("field: "+field);
            System.out.println("msg: "+msg);
            System.out.println("message Template: "+msgTemplate);
            System.out.println("invalid value: "+invalidValue);
            System.out.println("---");
        });**
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("ConstraintViolationException 발생");
    }

}
```

그리고 interpolatedMessage이름으로 된 메서드는 없어서 getMessage()를 이용해보고, TemplateMessage도 확인해보기 위해서 getMessageTemplate()메서드를 이용해보자

그리고 요청은 저번과 동일하게 [http://localhost:8089/api/user?name=0&age=0](http://localhost:8089/api/user?name=0&age=0) 으로 get요청을 보내면 당연히 값은 있지만 검증에 실패했기 때문에 ConstraintViolationException이 발생하고 콘솔에는

```java
error: ConstraintViolationException
field: name
msg: 크기가 2에서 2147483647 사이여야 합니다
message Template: {javax.validation.constraints.Size.message}
invalid value: 0
---
field: age
msg: 1 이상이어야 합니다
message Template: {javax.validation.constraints.Min.message}
invalid value: 0
---
```

위와 같이 출력되는 것을 확인해볼 수 있다

그리고 message에서는 inpolatedMessage 내용이 확인되고, getMessageTemplate에서는 기본메시지가 참조하는 패키지 정보가 포함되어 있음을 확인할 수 있다

➕ 🐻 또 다른 PropertyPath()처리 방법은

- Stream<Path.Node>를 이용
- StreamSupport.stream(error.getPropertyPath().spliterator(),false)를 이용
- 이렇게 받은 스트림을 list 객체로 변환하여 2번째 요소에 접근([get, age]만 들어오게 될 것)

---

[https://docs.oracle.com/javase/8/docs/api/java/util/stream/StreamSupport.html#stream-java.util.Spliterator-boolean-](https://docs.oracle.com/javase/8/docs/api/java/util/stream/StreamSupport.html#stream-java.util.Spliterator-boolean-)

StreamSupport 클래스의 stream메서드는 Spliterator로부터 새로운 sequential이나 parallel stream을 만들어내는 메서드로 쓰임새는 아래와 같다

그리고 pararell 부분은 true일 경우, 병렬 스트림으로 false인 경우 sequential(직렬)스트림으로 반환하게 된다고 한다!

그래서 반환하는 것은 직렬 혹은 병렬 스트림이라고 한다!

public static <T> Stream<T> stream(Spliterator<T> spliterator,
                                   boolean parallel)

<T> Stream<T> 부분은 약간 이상하긴 한데, 찾아보니, T 타입으로 메서드를 사용하고자 정의된 것이라고 한다!

[Stream - don't understand this return-Type](https://stackoverflow.com/questions/52575623/t-streamt-dont-understand-this-return-type)

📌Spliterator - Stream, Collection, IO채널 등에서 특정 소스의 객체를 순회하거나 파티셔닝 하기 위한 인터페이스

[https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)

병렬 처리라는 것은, 하나의 작업을 서브 작업으로 나누고, 서브 작업들을 분리된 스레드에서 병렬적으로 처리하는 것(직렬은 순서대로)

▶️ 마치 동적분할이 생각나는 것 같다

---

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.MissingServletRequestParameterException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.validation.ConstraintViolationException;
import javax.validation.Path;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;
import java.util.stream.StreamSupport;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class ApiControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
/*        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");*/
        System.out.println("error: "+e.getClass().getSimpleName());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

    //MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e){
        BindingResult bindingResult=e.getBindingResult();
        bindingResult.getAllErrors().forEach(error->{
            FieldError fieldError=(FieldError)error;
            String field=fieldError.getField();
            String msg = fieldError.getDefaultMessage();
            String value=fieldError.getRejectedValue().toString();//어떤 값들이 잘못 들어갔는지 확인

            System.out.println("필드: "+field);
            System.out.println("메시지: "+msg);
            System.out.println("잘못 들어간 값: "+value);
            System.out.println("---");
        });
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    //NullPointerException
    @ExceptionHandler(value=NullPointerException.class)
    public ResponseEntity nullPointerException(NullPointerException e){
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("인자값을 확인해주세요");
    }

    @ExceptionHandler(value= MissingServletRequestParameterException.class)
    public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e){

        String fieldName=e.getParameterName();
        String fieldType=e.getParameterType();
        String msg=e.getMessage();

        System.out.println("fieldName: "+fieldName);
        System.out.println("fieldType: "+fieldType);
        System.out.println("error message: "+msg);
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    @ExceptionHandler(value=ConstraintViolationException.class)
    public ResponseEntity constraintViolationException(ConstraintViolationException e){
        System.out.println("error: "+e.getClass().getSimpleName());
        e.getConstraintViolations().forEach(error->{
           //https://stackoverflow.com/questions/36555057/get-field-name-when-javax-validation-constraintviolationexception-is-thrown
//            Path path= error.getPropertyPath();//처음에 접근했던 방법
//            String field=null;
//
//            for(Path.Node node:path){
//                field=node.getName();//맨 마지막 값이 남게 될것
//            }

            **Stream<Path.Node> stream = StreamSupport.stream(error.getPropertyPath().spliterator(),false);
            List<Path.Node> list= stream.collect(Collectors.toList());**
           // System.out.println(list);[get, age]
            **String field=list.get(list.size()-1).getName();**

            //String field=error.getLeafBean().toString();로 접근할 수도 있음-leafBean오르 접근시
            String msg=error.getMessage();
            String msgTemplate = error.getMessageTemplate();

            String invalidValue=error.getInvalidValue().toString();//잘못된 값

            System.out.println("field: "+field);
            System.out.println("msg: "+msg);
            System.out.println("message Template: "+msgTemplate);
            System.out.println("invalid value: "+invalidValue);
            System.out.println("---");
        });
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("ConstraintViolationException 발생");
    }

}
```

그리고 요청도 위에서 시도한 것과 동일하게 접근하면, 결과도 동일하게 나온다

```java
error: ConstraintViolationException
field: name
msg: 크기가 2에서 2147483647 사이여야 합니다
message Template: {javax.validation.constraints.Size.message}
invalid value: 0
---
field: age
msg: 1 이상이어야 합니다
message Template: {javax.validation.constraints.Min.message}
invalid value: 0
---
```

---

🌹MethodArgumentNotValidException에 대한 처리🌹

BindingResult를 이용하기 위해 이를 가져오는 getBindingResult를 사용할 수 있도록 접근하기 위해서 MethodArgumentNotValidException을 예로 접근해보자

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.MissingServletRequestParameterException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.validation.ConstraintViolationException;
import java.lang.reflect.Field;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class ApiControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
/*        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");*/
        System.out.println("error: "+e.getClass().getSimpleName());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

    //MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e){
        **BindingResult bindingResult=e.getBindingResult();
        bindingResult.getAllErrors().forEach(error->{
            FieldError fieldError=(FieldError)error;
            String field=fieldError.getField();
            String msg = fieldError.getDefaultMessage();
            String value=fieldError.getRejectedValue().toString();//어떤 값들이 잘못 들어갔는지 확인

            System.out.println("필드: "+field);
            System.out.println("메시지: "+msg);
            System.out.println("잘못 들어간 값: "+value);
            System.out.println("---");
        });**
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    //NullPointerException
    @ExceptionHandler(value=NullPointerException.class)
    public ResponseEntity nullPointerException(NullPointerException e){
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("인자값을 확인해주세요");
    }

    @ExceptionHandler(value= MissingServletRequestParameterException.class)
    public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e){
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("파라미터로 전달될 값이 존재하지 않습니다. 확인부탁드립니다");
    }

    @ExceptionHandler(value=ConstraintViolationException.class)
    public ResponseEntity constraintViolationException(ConstraintViolationException e){
        e.getConstraintViolations().forEach(error->{
            System.out.println(error);
        });
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("ConstraintViolationException 발생");
    }

}
```

위에서처럼 bindingResult를 가져오고, getAllErrors()로 바인딩된 모든 에러들에 대해서 reflection(추후 정리예정)을 이용해서 필드에러로 각 에러를 변환해주고, 에러필드가 어디인지 메시지는 무엇인지, 잘못된 값은 무엇인지(getRejectedValue)를 확인해주자

post방식으로 [http://localhost:8089/api](http://localhost:8089/api) 에

```java
{
  "name":"",
  "age":"0"
}
```

요청을 보내면 

```java
필드: name
메시지: 비어 있을 수 없습니다
잘못 들어간 값: 
---
필드: name
메시지: 크기가 1에서 100 사이여야 합니다
잘못 들어간 값: 
---
필드: age
메시지: 1 이상이어야 합니다
잘못 들어간 값: 0
---
global
```

위와 같이 MethodArgumentNotValidException 가 발생하였고, 

name의 경우에는 null이 들어갔고 그 길이가 기준에 부합하였고

age는 최솟값이 1이었는데 0이 들어갔기 때문에 잘못되었음을 확인해볼 수 있다

-각 필드에 부여된 검증기준

- name: NotEmpty,Size
- age: NotNull, Min

🌹 MissingServletRequestParameterException 에 대한 부분 🌹

- get방식에서와 같이, 파라미터 값이 들어오지 않았을 때 발생

 

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/MissingServletRequestParameterException.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/MissingServletRequestParameterException.html)

서버에 다시한번 [http://localhost:8089/api/user?name&age](http://localhost:8089/api/user?name&age) 로 요청을 보내면

아래와 같이 MssingServletRequestParameterException이 발생한다

그런데 상세 내용을 확인해보고자, return 부분에 브레이크포인트를 걸고, 디버깅을 해보았다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/MissingServletException%EC%97%90%20%EB%8B%B4%EA%B2%A8%EC%9E%88%EB%8A%94%20%EA%B0%92%20%ED%99%95%EC%9D%B8-%EB%94%94%EB%B2%84%EA%B9%85.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/MissingServletException%EC%97%90%20%EB%8B%B4%EA%B2%A8%EC%9E%88%EB%8A%94%20%EA%B0%92%20%ED%99%95%EC%9D%B8-%EB%94%94%EB%B2%84%EA%B9%85.PNG?raw=true)

그 결과, 파라미터이름은 age, 데이터형은 Integer임은 확인되었지만, detailMessage는 비어있음을 확인하였다

 그러면 이 디버깅 내용을 기반으로

- ParameterName
- ParameterType
- message

를 찍어보자

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.MissingServletRequestParameterException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.validation.ConstraintViolationException;
import java.lang.reflect.Field;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class ApiControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
/*        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");*/
        System.out.println("error: "+e.getClass().getSimpleName());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

    //MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e){
        BindingResult bindingResult=e.getBindingResult();
        bindingResult.getAllErrors().forEach(error->{
            FieldError fieldError=(FieldError)error;
            String field=fieldError.getField();
            String msg = fieldError.getDefaultMessage();
            String value=fieldError.getRejectedValue().toString();//어떤 값들이 잘못 들어갔는지 확인

            System.out.println("필드: "+field);
            System.out.println("메시지: "+msg);
            System.out.println("잘못 들어간 값: "+value);
            System.out.println("---");
        });
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    //NullPointerException
    @ExceptionHandler(value=NullPointerException.class)
    public ResponseEntity nullPointerException(NullPointerException e){
        System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("인자값을 확인해주세요");
    }

    @ExceptionHandler(value= MissingServletRequestParameterException.class)
    public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e){

        **String fieldName=e.getParameterName();
        String fieldType=e.getParameterType();
        String msg=e.getMessage();

        System.out.println("fieldName: "+fieldName);
        System.out.println("fieldType: "+fieldType);
        System.out.println("error message: "+msg);**
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    @ExceptionHandler(value=ConstraintViolationException.class)
    public ResponseEntity constraintViolationException(ConstraintViolationException e){
        e.getConstraintViolations().forEach(error->{
            System.out.println(error);
        });
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("ConstraintViolationException 발생");
    }

}
```

그러면 콘솔에 아래와 같이 어떤 필드에서 잘못되었고, 그 데이터형과 메시지를 같이 확인해볼 수 있다

```java
fieldName: age
fieldType: Integer
error message: Required request parameter 'age' for method parameter type Integer is present but converted to null
```

---

---

### 클라이언트에게 보기 좋은 형태로 검증 결과에 대한 정보를 전달해주기

지금까지는 개발자 입장에서 클라이언트에게 보기 좋은 형태로 보여주기 위한 중간 과정을 다루었다

어떤 필드에서 문제가 있었고, 어떤 값으로 인해서 오류가 났는지 , 메시지는 무엇이었는지 등에 대해서를 다루었다

ErrorResponse 라는 dto 객체를 만들어보자

- 상태코드
- 요청url
- 에러코드
- 에러메시지
- 결과코드
- 에러리스트(어떤 에러들이 담겨있는지)

그리고 에러가 담길 에러리스트에 관련된 Error 객체도 만들자

이는 위에서 접근했던 field, error message, invalid value로 구성하자

```java
package com.example.exception.dto;

public class Error {
    private String field;
    private String message;
    private String invalidValue;

    public Error() {
    }

    public Error(String field, String message, String invalidValue) {
        this.field = field;
        this.message = message;
        this.invalidValue = invalidValue;
    }

    public String getField() {
        return field;
    }

    public void setField(String field) {
        this.field = field;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public String getInvalidValue() {
        return invalidValue;
    }

    public void setInvalidValue(String invalidValue) {
        this.invalidValue = invalidValue;
    }

    @Override
    public String toString() {
        return "Error{" +
                "field='" + field + '\'' +
                ", message='" + message + '\'' +
                ", invalidValue='" + invalidValue + '\'' +
                '}';
    }
}
```

```java
package com.example.exception.dto;

import java.util.List;

public class ErrorResponse {
    private String statusCode;//상태코드
    private String requestUrl;//요청url
    private String code;//에러코드
    private String message;//에러메시지
    private String resultCode;//결과코드
    private List<Error> errorList;//에러리스트

    public ErrorResponse() {
    }

    public ErrorResponse(String statusCode, String requestUrl, String code, String message, String resultCode, List<Error> errorList) {
        this.statusCode = statusCode;
        this.requestUrl = requestUrl;
        this.code = code;
        this.message = message;
        this.resultCode = resultCode;
        this.errorList = errorList;
    }

    public String getStatusCode() {
        return statusCode;
    }

    public void setStatusCode(String statusCode) {
        this.statusCode = statusCode;
    }

    public String getRequestUrl() {
        return requestUrl;
    }

    public void setRequestUrl(String requestUrl) {
        this.requestUrl = requestUrl;
    }

    public String getCode() {
        return code;
    }

    public void setCode(String code) {
        this.code = code;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public String getResultCode() {
        return resultCode;
    }

    public void setResultCode(String resultCode) {
        this.resultCode = resultCode;
    }

    public List<Error> getErrorList() {
        return errorList;
    }

    public void setErrorList(List<Error> errorList) {
        this.errorList = errorList;
    }

    @Override
    public String toString() {
        return "ErrorResponse{" +
                "statusCode='" + statusCode + '\'' +
                ", requestUrl='" + requestUrl + '\'' +
                ", code='" + code + '\'' +
                ", message='" + message + '\'' +
                ", resultCode='" + resultCode + '\'' +
                ", errorList=" + errorList +
                '}';
    }
}
```

그 다음에는 ApiControllerAdvice부분을 수정해주면 될 것 같다

(dto객체로 에러를 보이는 부분이 관리되고 있기 때문)

먼저 methodArgumentNotValidException 부분부터 수정해주자

- List<Error> list

를 추가히주고

람다식으로 접근한 bindingResult.getAllErrors()로 각 에러에 접근하여 담기 위해서

Error myError = new Error();

를 지역변수처럼 넣어서 사용해주자

그다음에는 setter를 이용해서 field, msg, value를 차곡차곡 넣어주자

그다음에는 리스트에 넣어주면 될 것 같다!

이렇게 반복해서 getAllErrors를 모두 접근하면, ErrorResponse의 필드의 값을 차근차근 채워나가면 될 것 같다

그런데 requestUrl은 서블릿 request가 필요하기 때문에 파라미터로 HttpServletRequest를 넣어주자

```java
//MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e, HttpServletRequest httpServletRequest){

        **List<Error> errorList=new ArrayList<>();**

        BindingResult bindingResult=e.getBindingResult();
        bindingResult.getAllErrors().forEach(error->{
            FieldError fieldError=(FieldError)error;
            String field=fieldError.getField();
            String msg = fieldError.getDefaultMessage();
            String value=fieldError.getRejectedValue().toString();//어떤 값들이 잘못 들어갔는지 확인

            System.out.println("필드: "+field);
            System.out.println("메시지: "+msg);
            System.out.println("잘못 들어간 값: "+value);
            System.out.println("---");

            Error myError= new Error();
            myError.setField(field);
            myError.setMessage(msg);
            myError.setInvalidValue(value);
            errorList.add(myError);

        });

        **ErrorResponse errorResponse = new ErrorResponse();
        errorResponse.setErrorList(errorList);
        errorResponse.setMessage(e.getMessage());
        errorResponse.setRequestUrl(httpServletRequest.getRequestURL().toString());//request필요
        errorResponse.setResultCode("FAIL");
        errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());
        //System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);**
    }
```

다른 부분들도 동일하게 적용해주자

```java
package com.example.exception.advice;

import com.example.exception.controller.RestApiController;
import com.example.exception.dto.Error;
import com.example.exception.dto.ErrorResponse;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.MissingServletRequestParameterException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.servlet.http.HttpServletRequest;
import javax.validation.ConstraintViolationException;
import javax.validation.Path;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;
import java.util.stream.StreamSupport;

@RestControllerAdvice(basePackageClasses = RestApiController.class)
public class ApiControllerAdvice {

    @ExceptionHandler(value=Exception.class)
    public ResponseEntity exception(Exception e){
/*        System.out.println(e.getClass().getSimpleName());
        System.out.println("---");
        System.out.println(e.getLocalizedMessage());
        System.out.println("---");
        System.out.println("global");*/
        System.out.println("error: "+e.getClass().getSimpleName());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
    }

    //MethodArgumentNotValidException에 대해서 에러 처리
    @ExceptionHandler(value= MethodArgumentNotValidException.class)
    public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e, HttpServletRequest httpServletRequest){

        List<Error> errorList=new ArrayList<>();

        BindingResult bindingResult=e.getBindingResult();
        bindingResult.getAllErrors().forEach(error->{
            FieldError fieldError=(FieldError)error;
            String field=fieldError.getField();
            String msg = fieldError.getDefaultMessage();
            String value=fieldError.getRejectedValue().toString();//어떤 값들이 잘못 들어갔는지 확인

            System.out.println("필드: "+field);
            System.out.println("메시지: "+msg);
            System.out.println("잘못 들어간 값: "+value);
            System.out.println("---");

            Error myError= new Error();
            myError.setField(field);
            myError.setMessage(msg);
            myError.setInvalidValue(value);
            errorList.add(myError);

        });

        ErrorResponse errorResponse = new ErrorResponse();
        errorResponse.setErrorList(errorList);
        errorResponse.setMessage(e.getMessage());
        errorResponse.setRequestUrl(httpServletRequest.getRequestURL().toString());//request필요
        errorResponse.setResultCode("FAIL");
        errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());
        //System.out.println("global");
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
    }

    //NullPointerException-이제는 MissingServletRequestParameterException 등이 대체하게 되었음
//    @ExceptionHandler(value=NullPointerException.class)
//    public ResponseEntity nullPointerException(NullPointerException e){
//        System.out.println("global");
//        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("인자값을 확인해주세요");
//    }

    @ExceptionHandler(value= MissingServletRequestParameterException.class)
    public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e,HttpServletRequest httpServletRequest){

        String fieldName=e.getParameterName();
        String fieldType=e.getParameterType();
        String msg=e.getMessage();

        System.out.println("fieldName: "+fieldName);
        System.out.println("fieldType: "+fieldType);
        System.out.println("error message: "+msg);

        List<Error> errorList = new ArrayList<>();
        Error error=new Error();
        error.setField(fieldName);
        error.setInvalidValue(fieldType);
        error.setMessage(msg);

        errorList.add(error);

        ErrorResponse errorResponse = new ErrorResponse();
        errorResponse.setErrorList(errorList);
        errorResponse.setRequestUrl(httpServletRequest.getRequestURL().toString());
        errorResponse.setMessage(e.getMessage());
        errorResponse.setResultCode("FAIL");
        errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
    }

    @ExceptionHandler(value=ConstraintViolationException.class)
    public ResponseEntity constraintViolationException(ConstraintViolationException e,HttpServletRequest httpServletRequest){
        List<Error> errorList = new ArrayList<>();
        ErrorResponse errorResponse=new ErrorResponse();

        System.out.println("error: "+e.getClass().getSimpleName());
        e.getConstraintViolations().forEach(error->{
           //https://stackoverflow.com/questions/36555057/get-field-name-when-javax-validation-constraintviolationexception-is-thrown
//            Path path= error.getPropertyPath();
//            String field=null;
//
//            for(Path.Node node:path){
//                field=node.getName();//맨 마지막 값이 남게 될것
//            }

            Stream<Path.Node> stream = StreamSupport.stream(error.getPropertyPath().spliterator(),false);
            List<Path.Node> list= stream.collect(Collectors.toList());
           // System.out.println(list);[get, age]
            String field=list.get(list.size()-1).getName();

            //String field=error.getLeafBean().toString();로 접근할 수도 있음-leafBean오르 접근시
            String msg=error.getMessage();
            String msgTemplate = error.getMessageTemplate();

            String invalidValue=error.getInvalidValue().toString();//잘못된 값

            System.out.println("field: "+field);
            System.out.println("msg: "+msg);
            System.out.println("message Template: "+msgTemplate);
            System.out.println("invalid value: "+invalidValue);
            System.out.println("---");

            Error error1=new Error();
            error1.setField(field);
            error1.setMessage(msg);
            error1.setInvalidValue(invalidValue);

            errorList.add(error1);

        });

        errorResponse.setErrorList(errorList);
        errorResponse.setMessage(e.getMessage());
        errorResponse.setRequestUrl(httpServletRequest.getRequestURL().toString());
        errorResponse.setResultCode("FAIL");
        errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
    }

}
```

[http://localhost:8089/api/user?name=0&age=0](http://localhost:8089/api/user?name=0&age=0) 로 요청을 보내보자

(브라우저에서 보면, 예쁘게 보이지 않고 일렬로 보이는 형태라서, talend에서 확인해보자)

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/errorList%20dto%20%EA%B0%9D%EC%B2%B4%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8%EC%97%90%EA%B2%8C%20%EB%B3%B4%EA%B8%B0%20%EC%A2%8B%EC%9D%80%20%ED%98%95%ED%83%9C%EB%A1%9C%20%EC%97%90%EB%9F%AC%20%EC%83%81%ED%99%A9%EC%9D%84%20%ED%91%9C%EC%8B%9C%ED%95%98%EA%B8%B0.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Validation%20%EB%AA%A8%EB%B2%94%EC%82%AC%EB%A1%80/errorList%20dto%20%EA%B0%9D%EC%B2%B4%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8%EC%97%90%EA%B2%8C%20%EB%B3%B4%EA%B8%B0%20%EC%A2%8B%EC%9D%80%20%ED%98%95%ED%83%9C%EB%A1%9C%20%EC%97%90%EB%9F%AC%20%EC%83%81%ED%99%A9%EC%9D%84%20%ED%91%9C%EC%8B%9C%ED%95%98%EA%B8%B0.PNG?raw=true)

그러면 json 형태로 에러에 대한 정보를 한눈에 파악해볼 수 있게 된다

이렇게 정리해두면 엔터프레이즈 프레임워크인 스프링의 목적에 맞게 일괄적용도 가능해서 편리하다!

더불어, 로직을 숨기거나 메시지를 직접 만들어 보내게 됨으로써 보다 안전한 정보 제공이 가능하다!