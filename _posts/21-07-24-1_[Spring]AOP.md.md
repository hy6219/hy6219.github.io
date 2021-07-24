# AOP (Aspect Oriented Programming)- 관점 지향 프로그램

- 대부분 스프링 어플리케이션 (MVC웹 어플리케이션)

=Web Layer ➕ Business Layer ➕ Data Layer

1. Web Layer : REST API 제공! Client 중심의 로직 적용
-response를 내려주거나
-http status를 변경
2. Business Layer("서비스") : 내부 정책에 따른 로직 개발(개발 시에 주로 해당 로직 부분을 개발하게 될 것)
3. Data Layer: 데이터 베이스 및 외부와의 연동 처리

🌻 관심 분리(Separation of Concerns)

가. 횡단 관심 Crosscutting Concerns

반복되는 로깅, 예외, 트랜잭션 처리 같은 코드들을 한 곳으로 몰아서 코딩할 수 있도록 해줄 수 있는 AOP의 특징!!

나. 핵심 관심 Core Concerns

사용자의 요청에 따라 실제로 수행되는 핵심 비즈니스 로직

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/%EA%B4%80%EC%8B%AC%EB%B6%84%EB%A6%AC%20Separation%20of%20Concerns.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/%EA%B4%80%EC%8B%AC%EB%B6%84%EB%A6%AC%20Separation%20of%20Concerns.png?raw=true)

AOP는 로직 주입을 한다고 생각하면 된다!

🌟 주요 어노테이션

[Aspect Oriented Programming 주요 어노테이션](https://www.notion.so/e360f9971369480d99ba50234596d948)

예제로 접해보도록 하자!

먼저, 메서드 파라미터 및 실행시간에 대한 로그를 남기는 경우를 생각해보자

그런데 먼저 AOP를 사용하기 위해서는 dependency를 추가해야 한다!

build.gradle에서

```java
plugins {
    id 'org.springframework.boot' version '2.5.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
}

group = 'com.aop'
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
    **implementation 'org.springframework.boot:spring-boot-starter-aop'**
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
    useJUnitPlatform()
}
```

위와 같이 implementation 'org.springframework.boot:spring-boot-starter-**aop**'를 추가해주자!

추가한 다음에는 화면에 표시되는 공룡모양의 새로고침 버튼을 누르거나 우측 gradle 메뉴의 메뉴바 중 'reload'를 눌러 새로고침해주자

BUILD SUCCESSFUL in 5s

BUILD SUCCESSFUL이 표시되면 성공이다!!

이어서, 사용자라는 객체를 

- 아이디
- 비밀번호
- 이메일

이라는 필드만 이용해서 만들어주자

```java
package com.aop.aop.dto;

public class User {
    private String id;
    private String pw;
    private String email;

    public User() {
    }

    public User(String id, String pw, String email) {
        this.id = id;
        this.pw = pw;
        this.email = email;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getPw() {
        return pw;
    }

    public void setPw(String pw) {
        this.pw = pw;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Override
    public String toString() {
        return "User{" +
                "id='" + id + '\'' +
                ", pw='" + pw + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}
```

그리고 아래와 같이 응답을 json으로 내려주는 경우를 각각 get과 post로 생각해보자

- get() : PathVariable로 id를, Query Parameter로 name을 이용한 text json을 응답으로 내려주기
- post(): Data Body를 통해서 데이터를 생성하고 응답으로 text json을 내려주기

```java
package com.aop.aop.controller;

import com.aop.aop.dto.User;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
public class RestApiController {

    @GetMapping("/get/{id}")
    public String get(@PathVariable long id, @RequestParam String name){
        StringBuilder sb = new StringBuilder();

        sb.append("[id= "+id+", name="+name+"]\n");

        System.out.println(sb);

        return sb.toString();
    }

    @PostMapping("/post")
    public User post(@RequestBody User user){
        System.out.println(user);
        return user;

    }

}
```

여기서 생각해봐야 할 점은, 이런 api가 과연 두 개만으로 끝날까?

실무에서는 엔드포인트가 많을 것!

이렇게 많은 엔드포인트에 대해서 AOP를 이용해서 로그를 찍어볼 것!

🌻 Endpoint:  통신 채널의 한 쪽 끝

- api가 서버에서 리소스에 접근가능하게 해주는 URL

[https://smartbear.com/learn/performance-monitoring/api-endpoints/](https://smartbear.com/learn/performance-monitoring/api-endpoints/)

먼저 AOP를 위해서 관련 클래스를 만들고

- Aspect 어노테이션으로 AOP로 사용될 것임을 명시하고
- Component 어노테이션으로 스프링에서 해당 객체가 관리될 것임을 명시해주자

```java
package com.aop.aop;

import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

**@Aspect
@Component**
public class ParameterAop {

}
```

그리고 aop를 적용할 위치를 지정하기 위해 PointCut을 이용할 것인데 잠시 이에 대해서 공부해보자

---

![https://www.javainuse.com/boot-27_1.jpg](https://www.javainuse.com/boot-27_1.jpg)

참조: [https://www.javainuse.com/spring/spring-boot-aop](https://www.javainuse.com/spring/spring-boot-aop)

🌟🌟 AOP 용어 정리

1. Joinpoint 조인 포인트
- 클라이언트가 호출하는 모든 비즈니스 메서드
- 비즈니스 클래스(서비스 클래스)의 모든 메서드가 해당됨
- "포인트컷 대상" "포인트컷 후보"

1. Pointcut 포인트컷
- 필터링된 조인포인트
- 우리에게 필요한 특정 메서드에서만 횡단 관심에 해당하는 공통 기능을 수행시키기 위해서 필요
- 메서드가 포함된 클래스, 패키지를 정확하게 지정 가능
- 메서드 시그니처(=메서드 명, 파라미터 순서, 타입, 갯수)까지 정확하게 지정 가능

📌 Pointcut expression 📌

A. PCD(pointcut designator;포인트컷 지시자) 

🌺 execution 

- 메서드 실행 조인포인트를 위한 기본적인 PCD

🌺 within

- 특정 유형의 조인 포인트들로 매칭시키는 한계를 정의

🌺 target

- 대상 객체가 주어진 유형의 인스턴스인 경우 조인 포인트를 매칭시키는 한계

🌺 this

- 빈 참조가 주어진 유형일 경우 조인 포인트를 매칭시키는 한곝

🌺 args

- 인자들이 주어진 유형일 경우 조인 포인트를 매칭시키는 한계

etc..

[https://docs.spring.io/spring-framework/docs/2.0.x/reference/aop.html](https://docs.spring.io/spring-framework/docs/2.0.x/reference/aop.html)

```java
@Pointcut("execution(public * *(..))")
    private void anyPublicOperation() {}
    
    @Pointcut("within(com.xyz.someapp.trading..*")
    private void inTrading() {}
    
    @Pointcut("anyPublicOperation() && inTrading()")
    private void tradingOperation() {}
```

위의 경우는 공식문서에서 설명하고 있는 예시인데,

만약 메서드명을 "anyPublicOperation"이라고 하고,

Pointcut을 지정해주었다면 **메서드명이 Pointcut의 name값이 되어서 사용될 수** 있다!

그리고, 위와 같이 && 뿐아니라,  ||, ! 를 이용해서 표현식을 연결해줄 수도 있다!

B. Pointcut expression

- execution([리턴타입]  [접근제어자]  패키지경로  클래스명 메서드명(메서드 파라미터))

-리턴타입

[Pointcut expression-리턴타입](https://www.notion.so/b22714bf44704febada223de13411aea)

-(..) : 0 개 이상의 파라미터에 매칭

-* : 모든 값을 표현

ex) execution(public * *(..)) 

-어떤 public 메서드에 대한 실행

execution(* set*(..)) 

-set으로 시작하는 모든 메서드 실행

execution(* com.xyz.service.AccountService.*(..))

-AccountService에 존재하는 모든 메서드 실행

### execution(* com.xyz.service.**.**(..))

-service패키지에 존재하는 모든 메서드 실행

### execution(* com.xyz.service..**.**(..))

-service 패키지 내부 혹은 하위 패키지의 모든 메서드 실행

1. Advice 어드바이스
- 횡단관심에 해당되는 공통 기능의 코드
- 독립된 클래스의 메서드로 작성됨
- 위에서 살펴본 before, after, after-returning, after-throwing, around 등은 어드바이스의 동작 시점을 나타냄

1. Aspect 혹은 Advisor     애스팩트 또는 어드바이저
- =포인트컷+ 어드바이스
- 어떤 포인트컷 메서드에 대해서 어떤 어드바이스 메서드를 실행할 지 결정

1. Weaving 위빙 ("엮기")
- 포인트컷으로 지정한 핵심 관심 메서드가 호출될 때, 어드바이스에 해당하는 횡단 관심 메서드가 삽입되는 과정
- 컴파일타임 위빙, 로딩타임 위빙, 런타임 위빙으로 분류
- 스프링에서는 런타임 위빙 방식만 지원

---

### Before, AfterReturning을 이용한 흐름 제어

이어서, Pointcut expression을 이용해서 "com.aop.aop.controller 패키지 및 서브패키지의 모든 메서드를 활용할 것"임을 명시해보자

```java
package com.aop.aop;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class ParameterAop {
    //pointcut : 어떤 부분에 aop를 적용할 것인지 명시
    //pointcut expression을 이용해서 com.aop.aop.controller 패키지 및 서브패키지의 모든 메서드를 실행하도록 지정
    **@Pointcut("execution(* com.aop.aop.controller..*.*(..))")**
    private void cut(){
        
    }
}
```

우리는 여기에서 

"메서드 실행 전 어떤 인자가 들어가는지 확인해보고, 그리고 실행 후 어떤 값이 리턴되는지 확인해볼 것이다"

그래서 우리는 어드바이스 동작시점(횡단관심의 동작시점)을 

"메서드 실행 전 어떤 인자가 들어가는지 확인" ▶️ Before

"실행 후 어떤 값이 리턴되는지 확인" ▶️ AfterReturning

로 나누어 생각해볼 수 있을 것이다!

그러면 지금 포인트컷을 , 즉 AOP 동작위치에 대한 부분을 cut()메서드가 담당하고 있기 때문에, **Before 혹은 AfterReturning 어노테이션을 이용시에 cut()메서드를 넘겨주면 된다**

그런데, **AfterReturning**은 인자로써 반환하는 값이 있어야 하므로, **파라미터로 객체를 추가**해주어야 한다

그리고! 그 **객체의 이름과 어노테이션에 추가되는 returning 속성의 값은 일치해야** 한다!

더불어서 Before와 AfterReturning은 JoinPoint를 파라미터로 갖는다!(포인트컷이 조인포인트을 모아둔 것과 관련된 것이 아닐까 추측해본다!)

```java
package com.aop.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class ParameterAop {
    //pointcut : 어떤 부분에 aop를 적용할 것인지 명시
    //pointcut expression을 이용해서 com.aop.aop.controller 패키지 및 서브패키지의 모든 메서드를 실행하도록 지정
    @Pointcut("execution(* com.aop.aop.controller..*.*(..))")
    private void cut(){
        //메서드 실행 전 어떤 인자를 확인할 것이고
        //실행 후에 어떤 값이 리턴되는지 확인해볼 것
    }

    **@Before("cut()")**
    public void arguments(JoinPoint joinPoint){

    }

    **@AfterReturning(value="cut()", returning="returnObj")**
    public void returning(JoinPoint joinPoint, **Object returnObj**){

    }
}
```

그리고 **Before에서는 인자를 가져올 텐데, 이를 조인포인트의 getArgs()를 이용해서 받아올 수 있다**

```java
package com.aop.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class ParameterAop {
    //pointcut : 어떤 부분에 aop를 적용할 것인지 명시
    //pointcut expression을 이용해서 com.aop.aop.controller 패키지 및 서브패키지의 모든 메서드를 실행하도록 지정
    @Pointcut("execution(* com.aop.aop.controller..*.*(..))")
    private void cut(){
        //메서드 실행 전 어떤 인자를 확인할 것이고
        //실행 후에 어떤 값이 리턴되는지 확인해볼 것
    }

    @Before("cut()")
    public void arguments(JoinPoint joinPoint){
        //arguments를 가져올 것
        Object[] args = joinPoint.getArgs();

        for(Object obj:args){
            System.out.println("type: "+obj.getClass().getSimpleName());
            System.out.println("value: "+obj);
        }
    }

    @AfterReturning(value="cut()", returning="returnObj")
    public void returning(JoinPoint joinPoint, Object returnObj){
        System.out.println("return obj:"+returnObj);
    }
}
```

그러면, 콘솔로 결과출력을 확인해보기 전에 상기해보면,

- (1) cut메서드는 진입점을 controller 패키지 및 서브패키지의 모든 메서드에 두고 있다
- (2) Before는 Pointcut 위치 이전에 실행된다
- (3) AfterReturning은 Pointcut 위치 이후에 메서드 호출 성공시에 (not throws) 실행된다

라는 점을 상기해보면

Before 부분이 먼저 출력 ▶️ 요청메서드 결과 ▶️ AfterReturning 부분 출력

순으로 콘솔에서 확인할 수 있을 것이다

POST 매핑된 부분을 먼저 확인해보자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/%ED%8F%AC%EC%9D%B8%ED%8A%B8%EC%BB%B7%EA%B3%BC%20%EC%96%B4%EB%93%9C%EB%B0%94%EC%9D%B4%EC%8A%A4%20%EC%A7%84%ED%96%89%EC%8B%9C%EC%A0%90.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/%ED%8F%AC%EC%9D%B8%ED%8A%B8%EC%BB%B7%EA%B3%BC%20%EC%96%B4%EB%93%9C%EB%B0%94%EC%9D%B4%EC%8A%A4%20%EC%A7%84%ED%96%89%EC%8B%9C%EC%A0%90.png?raw=true)

그러면 결과는 예측한 것과 같은 형태로 확인된다!

이는 물론, GET mapping한 부분에서도 마찬가지다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/%ED%8F%AC%EC%9D%B8%ED%8A%B8%EC%BB%B7%EA%B3%BC%20%EC%96%B4%EB%93%9C%EB%B0%94%EC%9D%B4%EC%8A%A4%20%EC%A7%84%ED%96%89%EC%8B%9C%EC%A0%90_get.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/%ED%8F%AC%EC%9D%B8%ED%8A%B8%EC%BB%B7%EA%B3%BC%20%EC%96%B4%EB%93%9C%EB%B0%94%EC%9D%B4%EC%8A%A4%20%EC%A7%84%ED%96%89%EC%8B%9C%EC%A0%90_get.PNG?raw=true)

즉, 위에서 설정한 Pointcut으로 인해서 PostMapping된 메서드 혹은 GetMapping된 메서드가 횡단관심으로 설정되고, 흐름의 기준으로써 설정된 것이다

이러한 기준과 그를 기준으로한 흐름을 이용할 수 있다는 점에서 AOP를 이용해서 로그를 남길 수 있다!

그리고 위에서 포인트컷을 이용하면 메서드 시그니처까지 알 수 있다라고 언급했는데

이 점을 상기시킨다면

"작동한 메서드 이름"도 가져올 수 있다!

그것도 스프링에서 제공되는 AspectJ 패키지에서의 Signature와 함께 java.lang.reflect.Method 패키지와의 조화를 통해서!!

```java
MethodSignature methodSignature=(MethodSignature)jointPoint.getSignature();
Method method=methodSignature.getMethod();//메서드를 알아내기
String name = method.getName();//메서드 명 알아내기
```

```java
package com.aop.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;

@Aspect
@Component
public class ParameterAop {
    //pointcut : 어떤 부분에 aop를 적용할 것인지 명시
    //pointcut expression을 이용해서 com.aop.aop.controller 패키지 및 서브패키지의 모든 메서드를 실행하도록 지정
    @Pointcut("execution(* com.aop.aop.controller..*.*(..))")
    private void cut(){
        //메서드 실행 전 어떤 인자를 확인할 것이고
        //실행 후에 어떤 값이 리턴되는지 확인해볼 것
    }

    @Before("cut()")
    public void arguments(JoinPoint joinPoint){
        //arguments를 가져올 것
        Object[] args = joinPoint.getArgs();
        //메서드이름
        MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
        Method method= methodSignature.getMethod();

        System.out.println("method name: "+method.getName());
        for(Object obj:args){
            System.out.println("type: "+obj.getClass().getSimpleName());
            System.out.println("value: "+obj);
        }
    }

    @AfterReturning(value="cut()", returning="returnObj")
    public void returning(JoinPoint joinPoint, Object returnObj){
        //메서드이름
        MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
        Method method= methodSignature.getMethod();

        System.out.println("method name: "+method.getName());
        System.out.println("return obj:"+returnObj);
    }
}
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/%ED%8F%AC%EC%9D%B8%ED%8A%B8%EC%BB%B7%EC%9D%84%20%ED%86%B5%ED%95%9C%20%EB%A9%94%EC%84%9C%EB%93%9C%20%EC%8B%9C%EA%B7%B8%EB%8B%88%EC%B2%98%20%EC%A0%91%EA%B7%BC.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/%ED%8F%AC%EC%9D%B8%ED%8A%B8%EC%BB%B7%EC%9D%84%20%ED%86%B5%ED%95%9C%20%EB%A9%94%EC%84%9C%EB%93%9C%20%EC%8B%9C%EA%B7%B8%EB%8B%88%EC%B2%98%20%EC%A0%91%EA%B7%BC.PNG?raw=true)

그러면 위와 같이 메서드이름을 확인해볼 수 있다! 지금은 컨트롤러의 post메서드가 작동되어 해당 이름이 출력되었다!

이렇게 포인트컷을 이용하게 됨으로써 컨트롤러 외부에서도 메서드를 바라볼 수 있다는 점은 , 지금은 체감하지 못하지만, 작업시에 매우 편리할 것 같다!(AOP를 이용해서 로그를 남기고, 이를 이용해서 디버깅을 할  때 어디서 잘못 되었는지 확인해볼 수 있다!)

---

### Around를 이용한 흐름 제어

🌹 **메서드의 실행 시간**을 이용해서 서버의 현재 상태 및 부하를 로그로 남길 수 있다! 이를 AOP를 이용해서 적용해보자!

먼저 AOP를 만들어보자

⚠️ 잠깐 ⚠️

Bean vs Component

Bean 은 클래스 단위로 객체를 등록할 수 없고

Bean은 메서드 단위에서 객체를 등록할 수 있다!

참고-IoC/DI 에서의 예제

```java
@Configuration
class AppConfig{

    @Bean("base64Encode")
    public Encoder encoder(Base64Encoder base64Encoder){
        return new Encoder(base64Encoder);
    }

    @Bean("urlEncode")
    public Encoder encoder(UrlEncoder urlEncoder){
        return new Encoder(urlEncoder);
    }
}
```

그런데 커스텀 어노테이션을 하나 만들어 줄 것이다!

[https://mangkyu.tistory.com/130](https://mangkyu.tistory.com/130)

해당 어노테이션은

- 타입과 메서드 선언 시 사용될 수 있도록 @Target({ElementType.TYPE, ElementType.METHOD})
- 어노테이션 정보는 런타임 동안 유지될 수 있도록 @Retention(RetentionPolicy.RUNTIME)

하자

```java
package com.aop.aop.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Timer {

}
```

그리고 컨트롤러 부분에는 2초 정도 소요되는 delete 과정이 있다고 가정해보고 해당 메서드를 마련해보자

```java
@Timer
    @DeleteMapping("/delete")
    public void delete(@RequestParam long id){
        //db logic 처리 시간이 1~2초 정도 소요된다고 가정하기
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```

그리고 AOP를 만들어보자! 이번에는 타이머 어노테이션에 대해서 제약을 걸어주어 타이머로써 기능적으로 동작할 수 있도록 하기 위해서 

@annotation PCD를 이용할 것이다!

```java
package com.aop.aop;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

//특정메서드의 실행시간을 찍을 것
@Aspect
@Component
public class TimerAop {

    @Pointcut("execution(* com.aop.aop.controller..*.*(..))")
    private void cut(){

    }
//타이머로써 동작할 수 있도록 annotation에 대한 제약을 걸어둘것
//@annotation(패키지명.클래스명)으로 짚어주었음
    **@Pointcut("@annotation(com.aop.aop.annotation.Timer)")**
    private void enableTimer(){

    }
}
```

PCD 내부 expression은 동일 패턴으로 구성되었지만 이번에는 정확히 1개로 짚어주었다

그리고 시간을 측정할 것이기 때문에 메서드 실행 전후 흐름 차이를 이용할 필요가 있다!

따라서 전과 후를 이용한 과정이 필요하므로 Around 흐름처리가 필요하다!

🌟Around는 ProceedingJoinPoint 객체로 조인포인트를 이용할 수 있다!

[ProceedingJointPoint 인터페이스의 메서드](https://www.notion.so/e6f0759d38144a998f2e478086a61f76)

우리는 지금 시간을 측정할 것이므로 proceed()로 조인포인트를 실행해주고, 그 전과 후에 스탑워치로 측정 시작과 끝을 수행시켜주자! 그러면 전체 시간을 구할 수 있다

더불어서, 타이머 기능을 위한 AOP에서의 해당 메서드는 메서드와 어노테이션 실행 시 인식을 위해서 

&&를 이용해서 두 포인트컷을 모두 사용하기로 명시해주자

```java
package com.aop.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;
import org.springframework.util.StopWatch;

//특정메서드의 실행시간을 찍을 것
@Aspect
@Component
public class TimerAop {

    @Pointcut("execution(* com.aop.aop.controller..*.*(..))")
    private void cut(){

    }
//타이머로써 동작할 수 있도록 annotation에 대한 제약을 걸어둘것
    @Pointcut("@annotation(com.aop.aop.annotation.Timer)")
    private void enableTimer(){

    }

    **@Around("cut() && enableTimer()")**
    public void timerAround(ProceedingJoinPoint jointPoint){
        try {
            //실행전
            StopWatch stopWatch = new StopWatch();
            stopWatch.start();

            //proceed에서 실행되므로 이 부분 위가 전, 아래가 후
            Object result = jointPoint.proceed();//조인포인트 호출
            //return 타입이 있으면 object로, void면 비어있게 될 것

            //실행후
            stopWatch.stop();

            System.out.println("proceed time:"+stopWatch.getTotalTimeSeconds()+"s");
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
    }
}
```

만약, AOP가 없었다면, 컨트롤러의 메서드 get,post,delete 내부에서 모두 스탑워치와 관련된 코드를 작성해주어야 한다!

뿐만아니라, 실질적인 비즈니스(서비스; dao가 받아온 데이터 가공) 관점이 아니지만, 실질적 로직이 들어가야 할 부분에 부가적인 부분이 계속 반복되는 문제가 있다!

하지만 AOP가 존재함으로써, 공통된 기능들은 AOP에서 명시해주면서 별도 기능만 추가해주면 된다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/AOP_around%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%ED%9D%90%EB%A6%84%EC%A0%9C%EC%96%B4_ProceedJoinPoint.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/AOP_around%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%ED%9D%90%EB%A6%84%EC%A0%9C%EC%96%B4_ProceedJoinPoint.PNG?raw=true)

위와 같이 서버에서 처리되는데 소요된 시간을 포인트컷과 그 흐름을 제어하는 Advice 흐름 제어관련 메서드로 확인해볼 수 있다!

그리고 위에서 return obj는 ParameterAop가 존재해서 발생된 것이다!

위와 같이 AOP를 이용해서 서버 모니터링에 적용해볼 수 있게 될 것이다!

---

### [값 변환]Before를 이용해서 미리 메서드 실행 전에 특정 값을 변경해보기(비즈니스 로직에 조금 더 집중할 수 있도록!)

- 암호화된 값에 대한 복호화 등이 필요할 때 유용
- 외부 값을 AOP단에서 이미 복호화된 상태에서 들어오게 할 수 있음
- 그리고 이에 대한 값 변경은 AOP단 내부에서 변경해주고 거래처에 제공해줄 수 있음

복호화하는 예제로 생각해보자

먼저 Decode라는 커스텀 어노테이션을 위에서와 동일하게 만들어주자

```java
package com.aop.aop.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Decode {

}
```

그리고 컨트롤러부분에 put api를 추가해보자

다만, 이 api는 Decode 어노테이션을 적용할 것이므로 Decode 어노테이션을 붙여주자

```java
		@Decode
    @PutMapping("/put")
    public ResponseEntity<User> put(@RequestBody User user){
        System.out.println("put");
        System.out.println(user);
        return ResponseEntity.status(HttpStatus.CREATED).body(user);
    }
```

그리고 AOP를 만들어보자

- "전" : 디코딩하여 db에 저장할 것
- "후" : 인코딩하여 db에 저장할 것

```java
package com.aop.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class DecodeAop {

    //포인트컷
    @Pointcut("execution(* com.aop.aop.controller..*.*(..))")
    private void cut(){

    }

    @Pointcut("@annotation(com.aop.aop.annotation.Decode)")
    private void decodeCut(){

    }

    @Before("cut() && decodeCut()")
    public void before(JoinPoint joinPoint){

    }

    @AfterReturning(value="cut() && decodeCut()", returning="returnObj")
    public void afterReturn(JoinPoint joinPoint, Object returnObj){

    }
}
```

먼저, Application에서 이메일 주소를 암호화하고, 이를 이용해서 api에서 확인해보자

```java
package com.aop.aop;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import java.util.Base64;

@SpringBootApplication
public class AopApplication {

    public static void main(String[] args) {
        SpringApplication.run(AopApplication.class, args);
        System.out.println(Base64.getEncoder().encodeToString("steve@gmail.com".getBytes()));
    }

}
```

그러면 콘솔에 "c3RldmVAZ21haWwuY29t"와 같이 표시되는데 이를 복사해서

talend에서 put 값으로 넣어주자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/Before%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%B4%EC%84%9C%20%EA%B0%92%EC%9D%84%20%EB%B3%80%EA%B2%BD%ED%95%B4%EC%A3%BC%EA%B8%B0-%EC%95%94%ED%98%B8%ED%99%94,%20%EB%B3%B5%ED%98%B8%ED%99%94.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/AOP/Before%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%B4%EC%84%9C%20%EA%B0%92%EC%9D%84%20%EB%B3%80%EA%B2%BD%ED%95%B4%EC%A3%BC%EA%B8%B0-%EC%95%94%ED%98%B8%ED%99%94,%20%EB%B3%B5%ED%98%B8%ED%99%94.PNG?raw=true)

암호화된 값을 넣어주면,  처음에는 디코딩하여 저장되기 때문에 "[steve@gmail.com](mailto:steve@gmail.com)"로 저장되고, AfterReturning에서 returnObj는 현재 "[steve@gmail.com](mailto:steve@gmail.com)" 이고, 이를 인코딩하였기 때문에 처음에 입력했던 암호화된 값을 response에서 보게 된다!

파트너사에서 특정 추가 변경값을 요구하면 선처리 혹은 후처리를 해줄 때 AOP를 활용하면 유용!!