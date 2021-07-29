# Spring Boot Validation-Basic

### ❓ What is "Validation" ❓

- Null Pointer Exception 발생 부분을 방지하기 위해 미리 검증을 하는 과정
- 가장 필요한 부분!!

자바에서는 Null Pointer 확인을 위해서 

- Objects.isNull 혹은 Objects.nonNull을 이용하거나
- 아래와 같이 ==null 혹은 ==0(숫자)을 통해서 확인

하는 방법을 이용

```java
if(password==null || age==0){
   return;
}
```

하지만 이렇게 해결하는 방식은 

- 코드양이 많아짐
- 비즈니스 로직에서 벗어난 코드가 작성되기도 함(많은 검증 과정으로 인해서)

과 같은 문제점을 야기시킬 수 있다

하지만, 이러한 validation이 없으면 파라미터를 1차적으로 걸러낼 수 없어서 이후 전체 서비스에 영향을 줄 수 있다

### 📌 Validation의 특징 📌

1. 검증해야 할 값이 많은 경우 코드의 길이가 길어짐
2. 구현에 따라 달라질 수 있지만, **서비스 로직과의 분리**가 필요
3. 흩어져 있는 경우 어디서 검증을 하는지 알기 어렵고, 재사용의 한계가 있음
4. 구현에 따라 달라질 수 있지만, 검증 로직이 변경되는 경우 테스트 코드 등과 같이 참조하는 클래스에서 로직이 변경되어야 하는 부분이 발생할 수 있음

ps. **일관적**이어야 좋음

[Validation을 위한 어노테이션](https://www.notion.so/bd53d4ecacb3492fb832393af46ea89c)

### 🌟 스프링 Validation을 위한 Setting 🌟

1. gradle dependencies 설정 추가

implementation("org.springframework.boot:spring-boot-starter-validation")

2.bean validation spec

[https://beanvalidation.org/2.0-jsr380/](https://beanvalidation.org/2.0-jsr380/) 

- "Hibernate Validator"
- 위의 어노테이션에 대한 사용을 확인해볼 수 있음

[http://hibernate.org/validator/](http://hibernate.org/validator/)

3.핸드폰번호 정규식

"^\\d{2,3}-\\d{3,4}-\\d{4}$"

010-1234-5678 처럼 차례대로 몇자리인지 나타내는 것!으로, 010-1234-5678이 아니면 받지 않겠다는 것!

---

먼저, validation을 하기 위해서 어떤 값들이 들어오는지 확인해볼 필요가 있다!

이 때 POST에서 확인하는 방식을 많이 채택!

먼저, 데이터를 주고 받기 위해서 이에 대한 user 객체를 만들자

user 객체는

- 이름
- 나이
- 이메일
- 핸드폰 번호

정보를 갖는다!

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    private String name;
    private int age;
    private String email;
    private String phoneNumber;

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                '}';
    }
}
```

이제 데이터를 http 메서드를 이용해서 주고받기 위해서 RestController를 만들어주자

 

```java
package com.validation.springvalidation.controller;

import com.validation.springvalidation.dto.User;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class ApiController {

    @PostMapping("/user")
    public User user(@RequestBody User user){
        System.out.println("user: "+user);
        return user;//echo
    }
}
```

talend tester에서 POST 방식으로 [http://localhost:8089/api/user](http://localhost:8089/api/user) url 위치에서

```java
{
  "name":"steve",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"010-1234-5678"
}
```

를 요청보내면

Response로 echo된 상태를 확인해볼 수 있고, 콘솔에서는

user: User{name='steve', age=20, email='steve@gmail.com', phoneNumber='010-1234-5678'}

위와 같이 어떤 값들이 들어오는지 확인해볼 수 있다

그런데, 만약 저렇게 @~형식의 이메일이나 - 가 들어간 핸드폰 번호 형태로 입력이 들어오지 않는다면

예)

```java
{
  "name":"steve",
  "age":20,
  "email":"steve",
  "phone_number":"01012345678"
}
```

response도 동일하게

```java
{
  "name":"steve",
  "age":20,
  "email":"steve",
  "phone_number":"01012345678"
}
```

와 같이 내려지는 것을 확인해볼 수 있고

콘솔에서는

user: User{name='steve', age=20, email='steve', phoneNumber='01012345678'}

위와 같이 출력되는 것을 확인해볼 수 있다

이런 부분에서 지금은 실생활에서 원하는 형태가 아니라는 것을 눈치챌 수 있다! 즉, 검증 Validation이 필요하다!

옛날 버전으로 검증을 한다면  아래와 같이 처리해볼 수 있을 것이다

 가장 간단하게 이메일은 at이 있는지 확인해보고

핸드폰은 -의 갯수가 2개인지 확인해보자

```java
package com.validation.springvalidation.controller;

import com.validation.springvalidation.dto.User;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class ApiController {

    @PostMapping("/user")
    public ResponseEntity user(@RequestBody User user){
        String email = user.getEmail();
        String phone = user.getPhoneNumber();
        boolean eFlag=false;
        boolean pFlag=false;

        int cnt = 0;

        for(int i = 0 ; i < phone.length(); i++){
            if(phone.charAt(i)=='-'){
                cnt++;
            }
        }

        if(!email.contains("@")){
            user.setEmail("잘못된 형식입니다");
            eFlag=true;
        }

        if(cnt!=2){
            user.setPhoneNumber("잘못된 형식입니다");
            pFlag=true;
        }

        System.out.println("user: "+user);
        if(eFlag==false && pFlag==false){
            return ResponseEntity.ok(user);
        }else{
            return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(user);
        }
    }
}
```

그러면 만약 사용자가

```java
{
  "name":"steve",
  "age":20,
  "email":"steve",
  "phone_number":"01012345678"
}
```

위와 같이 요청을 보내면 

```java
{
"name": "steve",
"age": 20,
"email": "잘못된 형식입니다",
"phone_number": "잘못된 형식입니다"
}
```

위와 같이 응답이 내려지는 것을 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation-Bad%20Request.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation-Bad%20Request.PNG?raw=true)

이번에는

```java
{
  "name":"steve",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"010-1234-5678"
}
```

위와 같이 요청을 해주면, Response는 위와 동일하게 확인됨을 볼 수 있다

그런데 만약, 회원수가 더 많아지고, 필드가 추가된다면 복잡해질 수 있다

이를 스프링 밸리데이션으로 바인딩해줄 수 있다

먼저 이메일에 대해서 validation을 진행해보자

그러면 @Email 어노테이션을 dto객체의 email 필드에 대해서 붙여주자

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import javax.validation.constraints.Email;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    private String name;
    private int age;
    **@Email**
    private String email;
    private String phoneNumber;

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                '}';
    }
}
```

🌟 Validation이 필요한 객체에 대해서는 @Valid 어노테이션을 붙여준다

따라서 ApiController의 user 메서드의 파라미터 앞에 해당 어노테이션을 추가해주자

```java
package com.validation.springvalidation.controller;

import com.validation.springvalidation.dto.User;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.validation.Valid;

@RestController
@RequestMapping("/api")
public class ApiController {

    @PostMapping("/user")
    public ResponseEntity user(**@Valid** @RequestBody User user){
        return ResponseEntity.status(HttpStatus.OK).body(user);
    }
}
```

이렇게 Valid 어노테이션이 붙여진 객체는 내부에 Email과 같은 어노테이션이 붙은 필드를 검사하고, 검증해서 맞지 않으면 Bad Request를 내보내게 된다

그리고 콘솔에서도 "올바른 형식의 이메일 주소여야 합니다"를 확인해볼 수 있게 된다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/%EC%8A%A4%ED%94%84%EB%A7%81%20%EB%B0%B8%EB%A6%AC%EB%8D%B0%EC%9D%B4%EC%85%98-%EC%9D%B4%EB%A9%94%EC%9D%BC.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/%EC%8A%A4%ED%94%84%EB%A7%81%20%EB%B0%B8%EB%A6%AC%EB%8D%B0%EC%9D%B4%EC%85%98-%EC%9D%B4%EB%A9%94%EC%9D%BC.gif?raw=true)

이번에는 핸드폰 번호에 대한 검증을 할 것인데

사이트에 따라 하이픈을 붙이기도, 안붙이기도 한다 

이런 부분은 정규식을 도입해서 접근해볼 수 있다

우선 하이픈이 있는 010-1234-5678을 생각해보면서 공부해보자!

1.먼저, 정규식을 표시하기 위해서 Pattern 어노테이션과 해당 어노테이션에 대해서 regexp속성 값으로 "^\\d{2,3}-\\d{3,4}-\\d{4}$"를 dto 객체 중 해당 필드에 붙여주자

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import javax.validation.constraints.Email;
import javax.validation.constraints.Pattern;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    private String name;
    private int age;
    @Email
    private String email;
    **@Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$")**
    private String phoneNumber;

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                '}';
    }
}
```

그러면 만약

```java
{
  "name":"steve",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"010-1234-5678"
}
```

혹은

```java
{
  "name":"steve",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"010-1234-5678"
}
```

와 같이 하이픈으로 연결된 핸드폰 번호를 요청으로 보내면 아래와 같이 200 OK 상태와 함께 그대로 에코되는 것을 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_Phone_Number_regexp_ok.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_Phone_Number_regexp_ok.PNG?raw=true)

하지만 아래처럼 정규식 형태가 아닌 요청을 보내게 되면

```java
{
  "name":"steve",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"0101235678"
}
```

BadRequest 상태와 함께

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_Phone_Number_regexp_badRequest.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_Phone_Number_regexp_badRequest.PNG?raw=true)

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_Phone_Number_regexp_badRequest_console.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_Phone_Number_regexp_badRequest_console.PNG?raw=true)

"(정규식) 와 일치해야 합니다"라는 메시지를 확인해볼 수 있다

그리고 스프링부트에서 활용할 수 있는 장점은 에러가 발생했을 때 에러에 대한 처리도 가능하다는 점이다!(이 부분은 추가적으로 조금 더 진행할 예정이다)

우선 지금 상태에서 Bad Request 등 에러에 대해서 처리해보도록 하자

1.BindingResult 객체를 api 컨트롤러 내 해당 메서드에 넣어준다!

```java
package com.validation.springvalidation.controller;

import com.validation.springvalidation.dto.User;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.validation.Valid;

@RestController
@RequestMapping("/api")
public class ApiController {

    @PostMapping("/user")
    public ResponseEntity user(@Valid @RequestBody User user,
														 **BindingResult bindingResult**){
        return ResponseEntity.status(HttpStatus.OK).body(user);
    }
}
```

그러면 바로 에러가 터지는 것이 아니라, validation 결과가 BindingResult에 상태값이 들어오게 된다

2-0.먼저 BindingResult에 담긴 "어떤 필드에, 어떤 에러가 담겼는지" 확인해보자

```java
package com.validation.springvalidation.controller;

import com.validation.springvalidation.dto.User;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.validation.Valid;

@RestController
@RequestMapping("/api")
public class ApiController {

    @PostMapping("/user")
    public ResponseEntity user(@Valid @RequestBody User user, BindingResult bindingResult){

        if(bindingResult.hasErrors()){
            StringBuilder sb = new StringBuilder();
            bindingResult.getAllErrors().forEach(objectError -> {
                **FieldError field= (FieldError)objectError;
                String msg= objectError.getDefaultMessage();
                System.out.println("field: "+field.getField());
                System.out.println("error: "+msg);**

            });
        }

        return ResponseEntity.status(HttpStatus.OK).body(user);
    }
}
```

그러기 위헤서는 먼저

- BindingResult 객체의 getAllErrors()메서드를 이용해서 모든 에러 정보에 접근
- 람다식을 이용해서 각 에러에 접근
- FieldError로 반복자를 이용해서 어떤 필드에서 에러가 발생했는지 확인

FieldError field=(FieldError)objectError;

- 메시지는 getDefaultMessage()를 이용해서 가져오기

그런데 메시지를 조금 다듬기 위해서 dto 객체 클래스에서 phoneNumber 필드에 대한 Pattern 어노테이션에 message 속성값을 다듬어주자

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import javax.validation.constraints.Email;
import javax.validation.constraints.Pattern;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    private String name;
    private int age;
    @Email
    private String email;
    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", **message="잘못된 핸드폰 번호 양식입니다")**
    private String phoneNumber;

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                '}';
    }
}
```

그리고 아래와 같이 요청을 보내면

```java
{
  "name":"steve",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"0101235678"
}
```

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation-Phone%20Number-BadRequest-BindingResult.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation-Phone%20Number-BadRequest-BindingResult.PNG?raw=true)

콘솔에서 phoneNumber 필드에서 검증에 실패했고, "잘못된 핸드폰 번호 양식입니다"라는 메시지가 발생했음을 확인해볼 수 있다

2-1. 2-0에서 어떻게 인식되는지 확인했다면, StringBuilder를 이용해서 필드와 메시지를 하나로 묶어주고, 이를 Bad Request일 때 body에 실어서 보내주도록 하자

```java
package com.validation.springvalidation.controller;

import com.validation.springvalidation.dto.User;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.validation.Valid;

@RestController
@RequestMapping("/api")
public class ApiController {

    @PostMapping("/user")
    public ResponseEntity user(@Valid @RequestBody User user, BindingResult bindingResult){

        if(bindingResult.hasErrors()){
            StringBuilder sb = new StringBuilder();
            bindingResult.getAllErrors().forEach(objectError -> {
                FieldError field= (FieldError)objectError;
                String msg= objectError.getDefaultMessage();
                System.out.println("field: "+field.getField());
                System.out.println("error: "+msg);

                **sb.append("field: "+field.getField()).append(",\t message: "+msg);**
            });

          💛  **return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(sb.toString());**
        }

        return ResponseEntity.status(HttpStatus.OK).body(user);
    }
}
```

그리고 나서 

```java
{
  "name":"steve",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"0101235678"
}
```

위와 같은 요청을 보내면

응답으로는 아래와 같이 phoneNumber필드에서 어떤 에러 메시지를 갖는다는 것을 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation-Phone%20Number-BadRequest-BindingResult-%EA%B2%80%EC%A6%9D%EC%8B%A4%ED%8C%A8.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation-Phone%20Number-BadRequest-BindingResult-%EA%B2%80%EC%A6%9D%EC%8B%A4%ED%8C%A8.PNG?raw=true)

더불어서 콘솔에는

```java
field: phoneNumber
error: 잘못된 핸드폰 번호 양식입니다
```

가 출력된다!

그리고 이번에는 검증 양식에 맞추어서 아래와 같은 요청을 보내면

```java
{
  "name":"steve",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"010-123-5678"
}
```

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation-Phone%20Number-BadRequest-BindingResult-%EA%B2%80%EC%A6%9D%EC%84%B1%EA%B3%B5.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation-Phone%20Number-BadRequest-BindingResult-%EA%B2%80%EC%A6%9D%EC%84%B1%EA%B3%B5.PNG?raw=true)

위와 같이 검증된 결과가 그대로 에코되어 확인된다!

추가로, 더 좋은 메시지를 생각해본다면

"잘못된 연락처 형식입니다. 아래와 같은 형식을 참고해주세요: 010-123(4)-5678"

을 생각해볼 수 있을 것이다

그러면 사용자도 어떤 부분이 잘못되었고, 어떻게 수정해야할지 알 수 있을 것이다

🌟 BindingResult가 없을 경우, 이후에 공부할 예외처리와 함께 처리할 수 있다!

🌟 검증이 잘 되었다면 검증 성공 혹은 실패에 따른 로직이 실행되게 된다!

➕ 몇 가지 검증을 더 해보자

- 이름에는 공백이 없었으면 해(null, "", " "불가!)
- 나이는 최소 0살 이상, 100살 이하

그러면 아래와 같이 NotBlank 어노테이션과 Min, Max 어노테이션을 해당 필드에 붙여주면 된다!

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import javax.validation.constraints.*;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    **@NotBlank(message="이름에는 공백이 허용되지 않습니다")**
    private String name;
    
    **@Min(value=0, message="최소 0살 이상")
    @Max(value=100,message="최대 100살 이하")**
    private int age;
    @Email
    private String email;
    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", message="잘못된 핸드폰 번호 양식입니다")
    private String phoneNumber;

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                '}';
    }
}
```

그러면 이름은 

- null이거나 "", " "이 아닐 때

나이는

- [0,100]살 범위에 있을 때

에 200 OK가 뜬다

하지만 나이가 200살 인경우 혹은 이름이 " "로 들어갔을 때에는 아래처럼 Bad Request와 함께 설정한 메시지를 확인해볼 수 있게 된다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_max_badRequest.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_max_badRequest.PNG?raw=true)

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_not%20blank.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_not%20blank.PNG?raw=true)

음.. 한가지 궁금해진 점은

"여러 필드에서 검증이 실패하면 어떻게 될까? 잘 인식이 될까?"인데

아래와 같이 이름은 공백, 나이는 -1로 요청을 보낸다고 생각해보자

```java
{
  "name":" ",
  "age":-1,
  "email":"steve@gmail.com",
  "phone_number":"010-123-5678"
}
```

그러면 아래처럼 name과 age필드에서 검증에 실패했음을 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_%EA%B2%80%EC%A6%9D%20%EC%8B%A4%ED%8C%A8%EB%90%9C%20%EB%91%90%20%ED%95%84%EB%93%9C.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20Boot%20Validation_%EA%B2%80%EC%A6%9D%20%EC%8B%A4%ED%8C%A8%EB%90%9C%20%EB%91%90%20%ED%95%84%EB%93%9C.PNG?raw=true)