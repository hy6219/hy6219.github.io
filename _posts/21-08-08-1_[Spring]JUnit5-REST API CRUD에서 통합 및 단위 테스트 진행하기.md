# REST API CRUD 테스트 코드 작성하기

스프링 프로젝트를 기본적으로 lombok과 spring web을 체크 후 실행하면

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/spring%20test%20dependencies.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/spring%20test%20dependencies.PNG?raw=true)

위와 같이 JUnit5와 Mockito에 대한 의존성이 확인되는 것을 볼 수 있다!

(따로 maven repository에서 설정관련된 부분을 참조하지 않아도 된다!)

그런데 스프링 버전이 올라가면서 JUnit에 대해서 jupiter가 붙으면서 변경된 부분들이 있으므로 이 부분까지 포함하여 공부하자

[TIL/[Spring]JUnit5 맛보기 java with gradle!.md at main · hy6219/TIL](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/%5BSpring%5DJUnit5%20%EB%A7%9B%EB%B3%B4%EA%B8%B0%20java%20with%20gradle!.md)

먼저 저번 시간에 연습했던 calculator 예제를 재활용해보자

- DollarCalculator
- ICalculator
- MarketApi
- Calculator

만 재활용해주자!

그리고 이를 빈 객체로 활용하기 위해서 @Component 어노테이션을 붙여주자

그러면 지금 상황에서는 Calculator 내부에 ICalculator를 구현한 구현체가 DollarCalculator만 존재하므로 ICalculator 필드에 Autowired나 Qualifier를 붙이지 않아도 된다!

지금은 시세를 초기화하는 부분이 필요한데, 이 부분이 인터페이스에 없으므로(Calculator 클래스 내부 필드로 사용) 해당 부분을 추가해주자!

```java
package com.example.springcrud.component;

public interface ICalculator {
    int sum(int x, int y);
    int minus(int x, int y);
    void init();
}
```

```java
package com.example.springcrud.component;

import lombok.NoArgsConstructor;
import org.springframework.stereotype.Component;

@Component
@NoArgsConstructor
public class MarketApi {
    public int connect(){
        return 1100;
    }
}
```

```java
package com.example.springcrud.component;

import lombok.AllArgsConstructor;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Component;

@Component
@RequiredArgsConstructor
public class Calculator{
    //구현하지 않고
    //포함시켜서 진행
    private final ICalculator iCalculator;

 /*   public Calculator(ICalculator iCalculator){
        this.iCalculator=iCalculator;
    }*/

    public int sum(int x, int y){
        this.iCalculator.init();
        return this.iCalculator.sum(x,y);
    }

    public int minus(int x, int y){
        this.iCalculator.init();
        return this.iCalculator.minus(x,y);
    }

}
```

```java
package com.example.springcrud.component;

import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Component;

@Component
@RequiredArgsConstructor
public class DollarCalculator implements ICalculator {

    private int price =1;
    //통신을 통해서 시세를 가져와서 적용하기 위함
    //그런데 계산기의 목적과 "통신"은 부적절하기 때문에 분리할것
    private final MarketApi marketApi;

//    public DollarCalculator(MarketApi marketApi){
//        this.marketApi=marketApi;
//    }
    @Override
    public void init(){
        this.price= marketApi.connect();
    }

    @Override
    public int sum(int x, int y) {
        x*=price;
        y*=price;
        return x+y;
    }

    @Override
    public int minus(int x, int y) {
        x*=price;
        y*=price;
        return x-y;
    }
}
```

그리고 간단하게 get 요청을 하여 계산기가 작동되도록 컨트롤러를 만들어주자

이때, 쿼리파라미터는 x와 y라는 이름으로 integer 값을 받도록 하고

필드로 Calculator 객체를 놓고 해당 메서드를 실행하도록 해주자

```java
package com.example.springcrud.controller;

import com.example.springcrud.component.Calculator;
import com.example.springcrud.component.ICalculator;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
@RequiredArgsConstructor
public class CalculatorApiController {

    private final Calculator calculator;

    @GetMapping("/sum")
    public int sum(@RequestParam int x , @RequestParam int y){
        return calculator.sum(x,y);
    }

    @GetMapping("/minus")
    public int minus(@RequestParam int x , @RequestParam int y){
        return calculator.minus(x,y);
    }
}
```

그러면 talend api에서 [http://localhost:8089/api/sum?x=10&y=10](http://localhost:8089/api/sum?x=10&y=10) 와 같이 요청을 보내면 body에 "22000"이라고 표시되는 것을 확인해볼 수 있을 것이다

이제는 , 이렇게 api tester에 가지 않고도 컨트롤러에 대해서 테스트할 수 있는 방법을 알아보자

🌟 먼저, 테스트를 진행할 때에 주의할 점은 , 아래 사진처럼 main과 test 폴더 내부에 동일한 이름의 패키지가 존재해야 한다는 점이다!(단순 com.example.springcrud 패키지 뿐 아니라, 하위 패키지를 포함한 모든 패키지까지 포함)

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%20%ED%85%8C%EC%8A%A4%ED%8A%B8_%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%20%ED%85%8C%EC%8A%A4%ED%8A%B8_%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD.PNG?raw=true)

## 01. 통합 테스트 `@SpringBootTest`

🌟 그리고 @SpringBootTest 어노테이션을 붙이면 실질적으로 스프링 컨테이너가 올라가면서, 전체적인 테스트가 가능해진다!

그리고 contextLoads 메서드를 실행해보면, 계속 실행되는 것은 아니고 단발적이기는 하지만, 스프링에서 Application.java를 실행했을 때처럼 스프링 마크가 찍혀지는 것을 볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EB%8B%A8%EC%9C%84%ED%85%8C%EC%8A%A4%ED%8A%B8_contextLoads.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EB%8B%A8%EC%9C%84%ED%85%8C%EC%8A%A4%ED%8A%B8_contextLoads.gif?raw=true)

🌟 패키지는 동일하게 가야 하기 때문에, DollarCalculator를 테스트하기 위해서 test 패키지 하위에 component 패키지를 만들자

그 다음은, "DollarCalculatorTest"라고 이름을 지정하여 자바 클래스를 하나 만들자

1.DollarCalculator는 MarketApi 클래스가 협력객체로써 존재하고 있고, DollarCalculator는 주력객체로 존재하고 있는데 init()메서드가 포함된 것은 Calculator이라는 점을 참고하자. 그리고 지금은 ICalculator의 구현체가 DollarCalculator 클래스뿐이라는 점을 고려해서 뒤이어서 나올 DI부분에서는 Autowired를 이용해줄 것이다

▶️ `@Import({MarketApi.class, Calculator.class})`로 불러와주기로 하자

[Calculator.class 대신 DollarCalculator.class를 넣어도 된다]

2.1에서  import한 MarketApi.class를 mocking처리해주기 위해서 어노테이션을 붙여야 하는데, "**스프링에서 빈 객체로 등록, 관리되고 있는 객체를 Mock처리**"하는 것이므로 `@MockBean`을 붙여주자

```java
package com.example.springcrud.component;

import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.context.annotation.Import;

//필요한 협력객체 주입
**@Import({MarketApi.class,Calculator.class})**
public class DollarCalculatorTest {

    **@MockBean**
    private MarketApi marketApi;//스프링에서 빈으로 등록된 객체를 mocking처리

}
```

3.Calculator의 행위를 검증하기 위해서, `@Autowired`를 붙여서 의존성을 주입해주자 (1의 설명을 참조하자)

4.테스트를 할 메서드 이름을 dollarCalculatorTest로 하고, `@Test` 어노테이션을 붙여서 테스트할 것임을 명시해주자

5.테스트할 메서드를 모두 명시해주고, 이전에 BeforeEach로 명시했던 부분을 내부에 포함시켜서, connect 메서드가 실행될 때 가짜 객체에 값으로 3000을 넣어 시뮬레이션해주자

```java
Mockito.when(marketApi.connect()).thenReturn(3000);
```

```java
package com.example.springcrud.component;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.context.annotation.Import;

//필요한 협력객체 주입
@Import({MarketApi.class,Calculator.class})
public class DollarCalculatorTest {

    @MockBean
    private MarketApi marketApi;//스프링에서 빈으로 등록된 객체를 mocking처리

    @Autowired
    private Calculator calculator;//DollarCalculator 주입

    **@Test**
    public void dollarCalculatorTest(){
        **Mockito.when(marketApi.connect()).thenReturn(3000);**

        int sum=calculator.sum(10,10);
        int minus=calculator.minus(10,10);

        **Assertions.assertEquals(60000,sum);**
        **Assertions.assertEquals(0,minus);**
    }

}
```

그런데 이대로 실행해주면 아래와 같이 NullPointerException이 표시된다

```java
java.lang.NullPointerException
	at com.example.springcrud.component.DollarCalculatorTest.dollarCalculatorTest(DollarCalculatorTest.java:22)
gine.extension.TimeoutExtension.intercept(TimeoutExtension.java:149)
```

그 이유는 @SpringBootTest가 붙여지지 않았기 때문이다!

붙여주자

그리고, 사실상 SpringBootTest가 올라가는 순간, 모든 빈 객체가 올라가게 되므로[이를 `통합테스트`라고 한다!], `@Import`를 하지 않아도 된다!

```java
package com.example.springcrud.component;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.context.annotation.Import;

**@SpringBootTest**
//필요한 협력객체 주입
@Import({MarketApi.class,Calculator.class})
public class DollarCalculatorTest {

    @MockBean
    private MarketApi marketApi;//스프링에서 빈으로 등록된 객체를 mocking처리

    @Autowired
    private Calculator calculator;//DollarCalculator 주입

    @Test
    public void dollarCalculatorTest(){
        Mockito.when(marketApi.connect()).thenReturn(3000);

        int sum=calculator.sum(10,10);
        int minus=calculator.minus(10,10);

        Assertions.assertEquals(60000,sum);
        Assertions.assertEquals(0,minus);
    }

}
```

위의 dollarCalculatorTest를 실행해보면, 테스트 결과가 통과되는 것을 확인해볼 수 있다(Tests passed:1)

## 02. 단위 테스트, `@WebMvcTest(abc.class)`

그러면 `단위 테스트` 는 어떻게 할까?

지금은 Calculator에 대해서 진행했기 때문에, controller에 대해서 진행해보자

`@WebMvcTest`를 통해서 웹에 필요한 것들만 로딩시켜 테스트해주자

```java
package com.example.springcrud.controller;

import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;

//웹에 필요한 것들만 로딩시킴
**@WebMvcTest(CalculatorApiController.class)**
public class CalculatorApiControllerTest {
}
```

지금은 CalculatorApiController.class만 필요하기 때문에 값으로 해당 클래스를 넣어주자

앞서서, SpringBootTest는 통합테스트를 지원하기 때문에, 자원소비가 많다는 점에서 WebMvcTest가 자원소비가 적어서 효과적일 수 있다

그리고, `@AutoConfigureWebMvc` 를 통해서 MVC 빈과 관련된 빈을 자동으로 등록되도록 해주자

```java
package com.example.springcrud.controller;

import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureWebMvc;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;

//웹에 필요한 것들만 로딩시킴
**@WebMvcTest(CalculatorApiController.class)
@AutoConfigureWebMvc**
public class CalculatorApiControllerTest {
}
```

그 다음에는, 실제로 CalculatorApiController에서는 Calculator 객체가 존재하고,

Calculator클래스에서는 ICalculator 객체가 존재하고 있기 때문에 `@Import`로 DollarCalculator와 Calculator를 import해주자

```java
package com.example.springcrud.controller;

import com.example.springcrud.component.Calculator;
import com.example.springcrud.component.ICalculator;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureWebMvc;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.context.annotation.Import;

//웹에 필요한 것들만 로딩시킴
@WebMvcTest(CalculatorApiController.class)
@AutoConfigureWebMvc
//calculator 주입
//calculator클래스에서는 ICalculator를 주입
**@Import({DollarCalculator.class, Calculator.class})**
public class CalculatorApiControllerTest {
}
```

🌟 MockMvc

- 웹 애플리케이션을 서버에 배포하지 않고도 스프링 MVC의 동작을 재현할 수 있는 클래스

```java
public ResultActions perform(RequestBuilder requestBuilder)
                      throws Exception
```

- 요청을 수행하고, 예상값과 같은 미래의 행위(further actions)의 연쇄를 허용하는 타입을 반환

[MockMvc (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html)

🌟 Interface ResultActions

- 만들어진 요청의 결과에 대해서 예상값과 같은 적용된 행위들을 허용
- MockMvcResultMatchers 와 MockMvcResultHandlers의 static factory methods를 참조해야 할 필요가 있음

[ResultActions (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultActions.html)

1. andExpect

```java
ResultActions andExpect(ResultMatcher matcher)
                 throws Exception
```

- 예측을 수행

(Example)

```java
static imports: MockMvcRequestBuilders.*, MockMvcResultMatchers.*

 mockMvc.perform(get("/person/1"))
   .andExpect(status().isOk())
   .andExpect(content().contentType(MediaType.APPLICATION_JSON))
   .andExpect(jsonPath("$.person.name").value("Jason"));
```

"/person/1" uri에 대해서 status는 200OK, 컨텐츠는 json, name 속성 값은 Jason으로 예측

2.andDo 메서드

- 일반적인 행위를 수행

```java
ResultActions andDo(ResultHandler handler)
             throws Exception
```

3.andReturn 메서드

- 결과에 대한 직접적인 접근을 위한 만들어진 요청의 결과를 반환

```java
MvcResult andReturn()
```

🌟 Class MockMvcResultMatchers

[MockMvcResultMatchers (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/result/MockMvcResultMatchers.html)

몇 가지만 살펴보자

1.content 메서드

```java
static ContentResultMatchers	content()
```

- response body 검증에 접근

2.status 메서드

```java
static StatusResultMatchers	status()
```

- response status 검증에 접근

🌟 Class MockMvcResultHandlers

[MockMvcResultHandlers (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/result/MockMvcResultHandlers.html)

1.log메서드

- Apache Commons Logging를 통해서 디버그 로그 메시지를 출력
- MvcResult에 대해서 로깅

```java
static ResultHandler	log()
```

2.print메서드

- 표준 출력 스트림/OutputSstream/Writer 에 대한 MvcResult 상세를 출력

```java
static ResultHandler	print()

static ResultHandler	print(OutputStream stream)

static ResultHandler	print(Writer writer)
```

🌟 Class MockMvcRequestBuilders

[MockMvcRequestBuilders (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/MockMvcRequestBuilders.html)

RequestBuilders를 위한 static factory methods 사용을 위한 클래스

1.delete(uri)

```java
static MockHttpServletRequestBuilder	delete(URI uri)
```

- delete 요청에 대한 MockHttpServletRequestBuilder 생성

2.get(uri)

```java
static MockHttpServletRequestBuilder	get(URI uri)
```

- get 요청에 대한 MockHttpServletRequestBuilder 생성

3.put(uri)

```java
static MockHttpServletRequestBuilder	put(URI uri)
```

- put 요청에 대한 MockHttpServletRequestBuilder 생성

4.post(uri)

```java
static MockHttpServletRequestBuilder	post(URI uri)
```

- post요청에 대한 MockHttpServletRequestBuilder 생성

🌟 MockHttpServletRequestBuilder 클래스

- 쿼리파라미터의 경우, 여러 필드가 있을 시 queryParam메서드를 통해서 Map 형태로 파라미터를 제공해주는 것도 좋을 것

1.queryParam메서드

```java
MockHttpServletRequestBuilder	queryParam(String name, String... values)

MockHttpServletRequestBuilder	queryParams(MultiValueMap<String,String> params)
```

- 쿼리스트링에 추가한 후 request parameters map에 추가

[MockHttpServletRequestBuilder (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/MockHttpServletRequestBuilder.html#queryParam-java.lang.String-java.lang.String...-)

---

우리는 [http://localhost:8089/api/sum](http://localhost:8089/api/sum) 에 queryParameter로 x와 y값을 넣어서 get 요청(MockMvcRequestBuilders.get, MockHttpServletRequestBuilder.param)을 보낼것이고 `일련의 연쇄작업`을 통해서 예측의 결과를 확인할 것(perform)이다

그리고 그 이전에 MarketApi를 mock 객체로 만든 후

MockMvc 객체를 주입해줘서 서버 배포와 무관하게 테스트할 수 있도록 해주자

그 이후, `일련의 작업은 크게 아래와 같은 2단계로 구성된다`

Step 1. "나는 `예측`할 것이다"

- `MockMvcResultMatchers`를 이용

(1)상태코드는 200 OK일 것이고(`MockMvcResultMatchers.status().isOk()`)

(2)컨텐츠는 string형태의 60000값일 것이다(`MockMvcResultMatchers.content().string("60000")`)

Step 2. "일반적인 행위를 `수행`할 것이다"

- `MockMvcResultHandlers` 를 이용

(1) 결과를 출력할 것 `MockMvcResultHandlers.print()`

```java
package com.example.springcrud.controller;

import com.example.springcrud.component.Calculator;
import com.example.springcrud.component.DollarCalculator;
import com.example.springcrud.component.ICalculator;
import com.example.springcrud.component.MarketApi;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureWebMvc;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.context.annotation.Import;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultHandlers;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;

//웹에 필요한 것들만 로딩시킴
@WebMvcTest(CalculatorApiController.class)
@AutoConfigureWebMvc
//calculator 주입
//calculator클래스에서는 ICalculator를 주입
@Import({DollarCalculator.class, Calculator.class})
public class CalculatorApiControllerTest {
    //MarketApi Mocking처리
    **@MockBean
    private MarketApi marketApi;**

    //mvc를 mocking으로 테스트
    **@Autowired
    private MockMvc mockMvc;**

    @BeforeEach
    public void init(){
        Mockito.when(marketApi.connect()).thenReturn(3000);
    }

    @Test
    public void sumTest() throws Exception {
        //http://localhost:8089/api/sum?x=~&y=~
        **mockMvc.perform(
                MockMvcRequestBuilders.get("http://localhost:8089/api/sum")
                .queryParam("x","10")
                .queryParam("y","10")
        ).andExpect(
                //기대
                MockMvcResultMatchers.status().isOk()
        ).andExpect(
                MockMvcResultMatchers.content().string("60000")
        ).andDo(
                MockMvcResultHandlers.print()
        );**
    }

}
```

이제 테스트해보자

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EB%8B%A8%EC%9C%84%ED%85%8C%EC%8A%A4%ED%8A%B8_MockMvc.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EB%8B%A8%EC%9C%84%ED%85%8C%EC%8A%A4%ED%8A%B8_MockMvc.gif?raw=true)

그러면 마치 talend 에서 테스트한 것처럼 요청과 응답을 확인해볼 수 있다! 만약 예상값과 실제값이 다르면 위와 같이 Expected, Actual로 표시해준다

## 02-1 . POST 방식에 대한 단위테스트

컨트롤러의 minus 부분에 대해서 post 방식으로 변경해주자

```java
package com.example.springcrud.controller;

import com.example.springcrud.component.Calculator;
import com.example.springcrud.component.ICalculator;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
@RequiredArgsConstructor
public class CalculatorApiController {

    private final Calculator calculator;

    @GetMapping("/sum")
    public int sum(@RequestParam int x , @RequestParam int y){
        return calculator.sum(x,y);
    }

    **@PostMapping("/minus")**
    public int minus(@RequestParam int x , @RequestParam int y){
        return calculator.minus(x,y);
    }
}
```

그리고 우선 요청,응답을 body로 주고받아야 하므로 dto객체를 만들자

```java
package com.example.springcrud.dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Req {
    private int x;
    private int y;
}
```

응답은 json에 깊이를 주기 위해서 body부분에 대한 내부 클래스를 만들어주자

```java
package com.example.springcrud.dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Res {
    private int result;
    private Body response;

    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public static class Body{
        private String resultCode="OK";
    }
}
```

그리고 이를 이용해서 json 형태로 결과를 내려주도록 하되

Res의 result에는 minus 결과를 넣어주도록 하자

```java
package com.example.springcrud.controller;

import com.example.springcrud.component.Calculator;
import com.example.springcrud.component.ICalculator;
import com.example.springcrud.dto.Req;
import com.example.springcrud.dto.Res;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
@RequiredArgsConstructor
public class CalculatorApiController {

    private final Calculator calculator;

    @GetMapping("/sum")
    public int sum(@RequestParam int x , @RequestParam int y){
        return calculator.sum(x, y);
    }

    @PostMapping("/minus")
    public Res minus(@RequestBody Req req){
        int res= calculator.minus(req.getX(),req.getY());
        Res response = new Res();

        response.setResult(res);
				response.setResponse(new Res.Body());
        return response;
    }
}
```

이제 테스트를 진행해보기 시작할 것인데, 위에서 메서드 몇가지(get,post, delete, put)를 보고 왔기 때문에 한결 수월하다

Req객체를 만들고, 이를 setter로 값을 주입해준 후, object mapper로 이를 json 객체로 만들어주자

그리고 이것을 이용해서 요청을 보내자

우선 결과부터 확인해보기 위해서 상태만 예측 후 출력해보자

```java
@Test
    public void minusTest() throws Exception {
        Req request = new Req();

        request.setX(30);
        request.setY(10);

        ObjectMapper objectMapper=new ObjectMapper();
        String json=objectMapper.writeValueAsString(request);

        mockMvc.perform(
                MockMvcRequestBuilders.post("http://localhost:8089/api/minus")
                .contentType(MediaType.APPLICATION_JSON)
                .content(json)
        ).andExpect(
                MockMvcResultMatchers.status().is2xxSuccessful()
        ).andDo(
                MockMvcResultHandlers.print()
        );

    }
```

그러면 아래와 같이 상태는 200이고, body에 result로 60000이고 response가 "OK"임을 확인해볼 수 있다

```java
MockHttpServletResponse:
           Status = 200
    Error message = null
          Headers = [Content-Type:"application/json"]
     Content type = application/json
             Body = {"result":60000,"response":{"resultCode":"OK"}}
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```

이렇게 응답이 json으로 내려오는 것도, 그 구조도 확인했으니, 이제 상태와 json 값을 기대해보자

앞서 json값에 대해서 기대할 때에는 jsonPath의 값에 $와 dot으로 점차적으로 접근하는 것을 확인했기 때문에 그대로 적용해주자

```java
package com.example.springcrud.controller;

import com.example.springcrud.component.Calculator;
import com.example.springcrud.component.DollarCalculator;
import com.example.springcrud.component.ICalculator;
import com.example.springcrud.component.MarketApi;
import com.example.springcrud.dto.Req;
import com.example.springcrud.dto.Res;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureWebMvc;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.context.annotation.Import;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultHandlers;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;

//웹에 필요한 것들만 로딩시킴
@WebMvcTest(CalculatorApiController.class)
@AutoConfigureWebMvc
//calculator 주입
//calculator클래스에서는 ICalculator를 주입
@Import({DollarCalculator.class, Calculator.class})
public class CalculatorApiControllerTest {
    //MarketApi Mocking처리
    @MockBean
    private MarketApi marketApi;

    //mvc를 mocking으로 테스트
    @Autowired
    private MockMvc mockMvc;

    @BeforeEach
    public void init(){
        Mockito.when(marketApi.connect()).thenReturn(3000);
    }

    @Test
    public void sumTest() throws Exception {
        //http://localhost:8089/api/sum?x=~&y=~
        mockMvc.perform(
                MockMvcRequestBuilders.get("http://localhost:8089/api/sum")
                .queryParam("x","10")
                .queryParam("y","10")
        ).andExpect(
                //기대
                MockMvcResultMatchers.status().isOk()
        ).andExpect(
                MockMvcResultMatchers.content().string("60000")
        ).andDo(
                MockMvcResultHandlers.print()
        );
    }

    @Test
    public void minusTest() throws Exception {
        Req request = new Req();

        request.setX(30);
        request.setY(10);

        ObjectMapper objectMapper=new ObjectMapper();
        String json=objectMapper.writeValueAsString(request);

        mockMvc.perform(
                MockMvcRequestBuilders.post("http://localhost:8089/api/minus")
                .contentType(MediaType.APPLICATION_JSON)
                .content(json)
        )**.andExpect(
                MockMvcResultMatchers.status().is2xxSuccessful()
        ).andExpect(
                MockMvcResultMatchers.jsonPath("$.result")
                .value("60000")
        ).andExpect(
                MockMvcResultMatchers.jsonPath("$.response.resultCode")
                .value("OK")
        )**.andDo(
                MockMvcResultHandlers.print()
        );

    }

}
```

이렇게 이상태로 테스트를 진행해보면 아래와 같이 응답을 확인해볼 수 있고, 테스트에 통과했음을 확인해볼 수 있다! 이렇게 하면, 행위검증 mocking을 Mockito로 수행해볼 수 있다!

처음에 접근했던 방식은 Mockito를 이용해서 가짜객체를 만드는 작업이 없었다는 점을 떠올려서 비교해보자

```java
MockHttpServletResponse:
           Status = 200
    Error message = null
          Headers = [Content-Type:"application/json"]
     Content type = application/json
             Body = {"result":60000,"response":{"resultCode":"OK"}}
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```

📌Depth 가 있는 json 데이터에 접근하기 📌

위에서 Res 객체를 잘 살펴보면 내부 클래스를 이용해서 깊이를 둔 것을 확인해볼 수 있다

이때 이에 대해서 접근하기 위해서는 바깥부터 천천히 접근하면 된다

```java
**$.response.resultCode**
```

$는 응답 body를 의미한다!

---

나머지 put은 post와 유사한 성질을 이용하고

delete는 get과 유사한 성질을 이용하면 될 것이다!