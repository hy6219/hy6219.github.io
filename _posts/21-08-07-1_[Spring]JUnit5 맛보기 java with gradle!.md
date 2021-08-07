# JUnit이란?

## 00. TDD 개발방식

- Test-driven Development
- 테스트 주도 개발에서 사용
- 코드의 유지보수 및 운영환경에서의 에러를 미리 방지하기 위해서 단위 별로 검증하는 테스트 프레임워크

📍 단위 테스트 📍

- 작성한 코드가 기대하는 대로 동작을 하는지 검증하는 절차
- 언어별로 단위테스트를 지원하는 프레임워크가 존재

## 01. JUnit

- 자바 기반의 단위테스트를 위한 프레임워크
- 어노테이션 기반으로 테스트 지원 ▶️ 테스트가 용이
- Assert(`예상값`, `실제값`)를 통해 검증

## 02. Let's practice JUnit

먼저 JUnit 방식에 익숙해지기 위해서 java gradle 프로젝트를 시작하자

그러면 아레처럼 `주피터 엔진`과 `junit` , `useJUnitPlatform()`이 있어야 한다!

```java
plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    **testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'**
    **testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0'**
}

test {
    useJUnitPlatform()
}
```

계산기를 만들어보면서 익혀보자

먼저 더하기와 빼기를 하는 계산기 인터페이스 ICalculator를 만들어보자

```java
public interface ICalculator {
    int sum(int x, int y);
    int minus(int x, int y);
}
```

그리고 이를 구현하지 않고, 멤버변수로 두고 사용하는 클래스 Calculator를 만들어보자

```java
public class Calculator{
    //구현하지 않고
    //포함시켜서 진행
    private ICalculator iCalculator;

    public Calculator(ICalculator iCalculator){
        this.iCalculator=iCalculator;
    }

    public int sum(int x, int y){
        return this.iCalculator.sum(x,y);
    }

    public int minus(int x, int y){
        return this.iCalculator.minus(x,y);
    }

}
```

그리고 Calculator의 생성자에 보면, 구현체를 넣어주어야 하는데 구현체가 없기 때문에,  ICalculator를 구현한 구현체 KrwCalculator를 만들어보자

```java
public class KrwCalculator implements ICalculator {

    private int price=1;

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

이를 이용해서 KrwCalculator를 Calculator에 전달해주고, 이를 기반으로 sum메서드를 진행하는데, 그 인자값으로 x와 y값으로 10을 넣어주자

```java
public class Main {
    public static void main(String[] args){
        System.out.println("hello JUnit");

        Calculator c=new Calculator(new KrwCalculator());

        System.out.println(c.sum(10,10));

    }
}
```

그러면 아래처럼 더한 결과가 잘 나오는 것을 확인해볼 수 있다

```java
hello JUnit
20
```

그리고 이번에는 "해외 시세"에 따른 금액 변동이 있는 Dollar의 경우를 생각해보자

이 경우에는 시세를 "외부"에서 가져와야 하기 때문에 일종의 '통신'이 필요하다

그런데 이는 계산기 목적과 상이하기 때문에,  시세를 의미하는 MarketApi로 분리하고 간단하게 1100원이 시세라고 가정하자

```java
public class MarketApi {
    public int connect(){
        return 1100;
    }
}
```

이를 이용해서 DollarCalculator에서는 생성자를 이용해서 MarketApi를 주입해주고, price에 이 값을 넣어 초기화해주자

```java
public class DollarCalculator implements ICalculator {

    private int price =1;
    //통신을 통해서 시세를 가져와서 적용하기 위함
    //그런데 계산기의 목적과 "통신"은 부적절하기 때문에 분리할것
    private MarketApi marketApi;

    public DollarCalculator(MarketApi marketApi){
        this.marketApi=marketApi;
    }
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

그러면 이를 메인에서 똑같이 x와 y에 값을 10으로 넣으면 이제는 MarketApi로 인해서 시세가 적용되어, 22000원이 계산되었음을 확인해볼 수 있다

```java
public class Main {
    public static void main(String[] args){
        System.out.println("hello JUnit");

//        Calculator c=new Calculator(new KrwCalculator());
//
//        System.out.println(c.sum(10,10));

        MarketApi marketApi=new MarketApi();
        DollarCalculator d= new DollarCalculator(marketApi);
        d.init();

        Calculator c2=new Calculator(d);
        System.out.println(c2.sum(10,10));

    }
}
```

```java
hello JUnit
22000
```

그런데, 만약 DollarCalculator에서 sum메서드가 return 0이라면 언제까지 ctrl을 누르고 이 원인을 파악하러 몇 개의 클래스를 건널 것인가?

▶️ **test 폴더**를 이용하자!! 🌟🌟🌟

✴️ 잠깐! 테스트 중에 이런 표시를 보셨다구요? 겁내지 마십시오! 

```java
0 containers and 1 tests were Method or class mismatch
```

가장 빠른 방법은 '무시'입니다!(허허)

[https://velog.io/@wiswis3434/Day-01-오류모음](https://velog.io/@wiswis3434/Day-01-%EC%98%A4%EB%A5%98%EB%AA%A8%EC%9D%8C)

원인은 "메서드가 여러개인데 1개만 테스트했기 때문"이라고 합니다

[https://www.inflearn.com/questions/157200](https://www.inflearn.com/questions/157200)

하지만, 그래도 테스트가 문제가 있다(진행에)

1. Settings-Build,Execution,Deployment-Build Tools-Gradle에서 `Build and run using` 과 `Run tests using` 속성을 Intellij IDEA로 변경[[https://www.inflearn.com/questions/157200](https://www.inflearn.com/questions/157200)]
2. [https://intellij-support.jetbrains.com/hc/en-us/community/posts/360004383639-How-to-add-Gradle-options](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360004383639-How-to-add-Gradle-options) 를 참고해서 Run-Edit Configurations-Environment variables 수정

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/0%20containers%20and%201%20tests%20were%20Method%20or%20class%20mismatch.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/0%20containers%20and%201%20tests%20were%20Method%20or%20class%20mismatch.PNG?raw=true)

나는 문구가 떴지만, 그냥 테스트 결과가 확인되어서 문제가 없었다!

---

다시 컴백 투 본론!

삽질 끝에 문제가 없는 것 같아서 쭉 진행하기로 했다

위의 DollarController에서 sum과 minus에서 리턴되는 값을 0으로 해두자(일부러)

그리고 단위테스트를 하기 위해서는 

- Test 어노테이션
- Assertions.~ 메서드를 통해서 값 비교를 해주어야 한다!(배열관련 비교 메서드도 있어서 ~로 적었다)

```java
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;

public class DollarCalculatorTest {
    @Test
    public void testHello(){
        System.out.println("hello");
    }

  📌  **@Test**
    public void dollarTest(){
        MarketApi marketApi=new MarketApi();
        DollarCalculator d= new DollarCalculator(marketApi);
        d.init();

        Calculator c2=new Calculator(d);
        System.out.println(c2.sum(10,10));

       📌 **Assertions.assertEquals(22000,c2.sum(10,10));**

    }
}
```

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EB%8B%A8%EC%9C%84%ED%85%8C%EC%8A%A4%ED%8A%B8.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EB%8B%A8%EC%9C%84%ED%85%8C%EC%8A%A4%ED%8A%B8.gif?raw=true)

그리고 dollarTest() 메서드에 대해서 왼쪽에 뜨는 실행 버튼을 눌러주면 예측값이 22000인데, 실제로 테스트해봤을 때 0이 나왔고, 관련된 부분을 같이 띄워준다

그러면 우리는 '엇! 그러면 여기서 잘못되었던 거구나!' 라고 생각해서 해당 부분을 수정해주면 될 것이다

그런데, 조금만 더 상황을 가정해서 생각해보자

지금 sum메서드와 minus 메서드의 차이점은 `+를 할지 -를 할 지` 이지 않은가?

그런데 만약, 돈이 걸려있었던 플젝인데 복붙만 하고 수정을 하지 않았다면..?

큰 위기가 있을 것인데, 이를 이전에 방지하는데에 이러한 테스트코드가 위대한 역할을 해줄 수 있을 것이다! 뿐만 아니라, 다른 팀원이 수정 후 코드 확인을 할 때에도 도움이 될 수 있을 것이다!

---

## 03. Mockito

- 가짜(Mock) 객체의 의존성 주입을 해서 예상하는 값을 반환하도록 상황을 모사해주는 것
- 예) User라는 객체가 있는데, 이를 사용중이라면 Mock 객체를 만들어 테스트
- Mockito core와 Mockito JUnit Jupiter 에 대한 의존성을 추가해주어야 한다
- test 클래스 위에 `@ExtendWith(MockitoExtension.class)` 를 추가해주어야 한다!

```java
plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    **// https://mvnrepository.com/artifact/org.mockito/mockito-core
    testImplementation group: 'org.mockito', name: 'mockito-core', version: '3.6.0'
    // https://mvnrepository.com/artifact/org.mockito/mockito-junit-jupiter
    testImplementation group: 'org.mockito', name: 'mockito-junit-jupiter', version: '3.6.0'**
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0

test {
    useJUnitPlatform()
}
```

`위: build.gradle`

```java
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;

**@ExtendWith(MockitoExtension.class)**
public class DollarCalculatorTest {
    @Test
    public void testHello(){
        System.out.println("hello");
    }

    @Test
    public void dollarTest(){
        MarketApi marketApi=new MarketApi();
        DollarCalculator d= new DollarCalculator(marketApi);
        d.init();

        Calculator c2=new Calculator(d);
        System.out.println(c2.sum(10,10));

        Assertions.assertEquals(22000,c2.sum(10,10));

    }
}
```

MarketApi를 가짜 객체(Mock Object)로 만들어 사용해보자!

🌹 Mockito.lenient(), MockSettings.lenient()

- Mockito 2부터 도입
- 더 편리한 테스트와 향상된 생산성을 유도
- 엄격한 stubbing은 불필요한 stub들을 보고함으로써 테스트를 조금더 dry하게 만드는데, lenient 는 이에 대한 절충안을 제시

[Mockito (Mockito 3.11.2 API)](https://javadoc.io/static/org.mockito/mockito-core/3.11.2/org/mockito/Mockito.html#46)

(외계어인 것인가.. 어려워😭)

### 03-1. TDD의 원초적 개념부터 접근, What is Mocking / Stubbing?

아래의 해외 블로그를 참고해서 TDD 부분을 정리해보았다😢

[How to test software, part I: mocking, stubbing, and contract testing](https://circleci.com/blog/how-to-test-software-part-i-mocking-stubbing-and-contract-testing/)

![https://production-cci-com.imgix.net/blog/media/Testing_pyramid.jpg?ixlib=rb-3.2.1&auto=format&fit=max&q=60&ch=DPR%2CWidth%2CViewport-Width%2CSave-Data&w=898](https://production-cci-com.imgix.net/blog/media/Testing_pyramid.jpg?ixlib=rb-3.2.1&auto=format&fit=max&q=60&ch=DPR%2CWidth%2CViewport-Width%2CSave-Data&w=898)

source: [https://production-cci-com.imgix.net/blog/media/Testing_pyramid.jpg?ixlib=rb-3.2.1&auto=format&fit=max&q=60&ch=DPR%2CWidth%2CViewport-Width%2CSave-Data&w=898](https://production-cci-com.imgix.net/blog/media/Testing_pyramid.jpg?ixlib=rb-3.2.1&auto=format&fit=max&q=60&ch=DPR%2CWidth%2CViewport-Width%2CSave-Data&w=898)

위의 삼각형은 사이트에서 "테스트 피라미드"로 소개하고 있다

- 가장 빠르고 저렴한 방법으로 테스트할 수 있는 것은 단위/요소 테스트
- 통합 테스트는 이러한 단위테스트보다는 조금 더 느리고 기회비용이 비싸다
- 그리고 UI 계층에서 테스트 하는것이 이 세가지 중 가장 느리고 기회비용이 가장 비싸다

🌻 What is `Mocking` ? 🌻

실제 서비스를 대신할 수 있는 가짜 버전의 외부/내부 서비스를 만들어 테스트를 보다 빠르고 안정적으로 실행하는 것

- 객체의 속성과 상호작용할 때 모의 객체를 사용할 수 있음(객체의 기능이나 동작과 관련된 작용이 아니라!)

🌻 What is `Stubbing` ? 🌻

Mocking처럼 `대리인(stand-in)` 을 만들지만, `전체 객체가 아닌, 행위(행동;behavior)를 모의 테스트` 하는 것

궁금한 것은 둘의 차이가 행위인지 속성인지라는 점에서 차이가 있다는 점이다! 그래서 mock과 stub의 차이라던지 관련 개념을 설명해둔 곳이 필요했다

그 결과 멋진! 블로그를 발견했다!(감사합니다!!)

[https://joont92.github.io/tdd/상태검증과-행위검증-stub과-mock-차이/](https://joont92.github.io/tdd/%EC%83%81%ED%83%9C%EA%B2%80%EC%A6%9D%EA%B3%BC-%ED%96%89%EC%9C%84%EA%B2%80%EC%A6%9D-stub%EA%B3%BC-mock-%EC%B0%A8%EC%9D%B4/)

[https://minslovey.tistory.com/97](https://minslovey.tistory.com/97)

[https://testing.jabberstory.net/](https://testing.jabberstory.net/)

이 블로그를 기반으로 Mock과 Stub, 검증 등에 대해서 추가로 정리해보면서 공부해보도록 할 것이다

### 03-1-1. 관련 용어 정리

🌹 SUT ( System Under Test) : 주요 객체(primary object)! 테스트 대상

🌹 협력객체(collaborator) :  부차적 객체(secondary objects)

🌹 테스트 더블(Test Double) : 테스팅을 목적으로 진짜 객체 대신 사용되는 모든 종류의 위장 객체

- Dummy, Fake Object, Stub, Mock
- 관련해서는 나중에 더 공부해보자(아래)

[What's the difference between faking, mocking, and stubbing?](https://stackoverflow.com/questions/346372/whats-the-difference-between-faking-mocking-and-stubbing)

### 03-1-2. 상태검증과 행위검증의 차이

🌹 상태검증 : 메서드가 수행된 후 주요객체(SUT) 및 협력객체(collaborator)의 `상태`를 살펴봄으로써 올바르게 동작했는지 판단

🌹 행위검증 : 주요객체(SUT)가 협력객체(collaborator)의 특정 메서드가 호출되었는지 등의 `행위`를 검사함으로써 올바르게 동작했는지 판단

### 03-1-3. stub vs mock

🌹 stub

- 호출이 되면, `미리 준비된 답변으로 응답` 하는 것
- 테스트 시, `프로그램된 것 이외에는 응답 🚫`
- 협력 객체의 특정 부분이 테스트하기 어려울 경우, stub를 사용하면 수월
- 일반적으로 우리가 mock으로 잘못 알고 있음

🌹 mock

- 다른 테스트 더블과는 다르게, `행위검증 사용을 추구`
- 행위를 기록하는 식의 로직이 들어가 있을 것

## 03-2. Mockito를 이용해서 주요 객체 SUT의 행위를 검증하기

먼저, 우리는 위의 예제를 이어서 진행해볼 예정이다

이때, `협력객체`가 `MarketApi` 이다!

그리고 **lanient**를 이용해서 **조금 느슨**하게 **connect 메서드가 실행되었을 때(when)** **3000을 리턴**하도록 해주자**(mocking 처리)(thenReturn)**

 

`@BeforeEach`

- 각각의 test 메서드 실행 전에 노출되어 처리

`@AfterEach`

- 각각의 test 메서드 실행 후에 노출되어 처리

`@BeforeAll`

- 클래스에 존재하는 모든 메서드 시작 전 실행

`@AfterAll`

- 클래스에 존재하는 모든 메서드 시작 후 실행

우리는 협력객체인 MarketApi를 필드로 만들어 두었으므로, mocking을 확인해볼 메서드인 mockTest() 내부에서는 새로이 객체를 만들 필요가 없다

그리고, 협력객체는 주력 객체의 실행 전에 init 메서드를 실행한 상황이어야 하므로 `@BeforeEach` 를 이용해서 먼저 모의 객체를 만들어서 값을 주입해주자

🌟 Mocking 작업 대상이 될 mock 객체인 MarketApi에 대해서는 **@Mock 어노테이션**을 붙이자

```java
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;

**@ExtendWith(MockitoExtension.class)**
public class DollarCalculatorTest {
    **@Mock
    public MarketApi marketApi;**

    **@BeforeEach**
    public void init(){
        **Mockito.lenient().when(marketApi.connect()).thenReturn(3000);**
    }

    @Test
    public void testHello(){
        System.out.println("hello");
    }

    @Test
    public void dollarTest(){
				MarketApi marketApi=new MarketApi();
        DollarCalculator d= new DollarCalculator(marketApi);
        d.init();

        Calculator c2=new Calculator(d);
        System.out.println(c2.sum(10,10));

        Assertions.assertEquals(22000,c2.sum(10,10));

    }

    **@Test**
    public void mockTest(){
     //   MarketApi marketApi=new MarketApi();
        DollarCalculator d= new DollarCalculator(marketApi);
        d.init();

        Calculator c2=new Calculator(d);
        System.out.println(c2.sum(10,10));

        Assertions.assertEquals(60000,c2.sum(10,10));

    }

}
```

그러면 아래와 같이 우리가 지금 위에서는 60000으로 예측했는데, 처음 테스트시에는 22000으로 예측했었다.

이럴 경우에는 테스트에 실패했음을 통보받고, 예측값과 실제값을 확인해볼 수 있고

중요한 것은 이제 더이상 init으로 1100원이 시세가 아니라 , 위에서 지정했던 3000이 시세가 되었음이라는 점이다!

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EB%8B%A8%EC%9C%84%ED%85%8C%EC%8A%A4%ED%8A%B8_Mockito.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5_%EB%8B%A8%EC%9C%84%ED%85%8C%EC%8A%A4%ED%8A%B8_Mockito.gif?raw=true)

그리고 테스트 결과를 아래처럼 확인해봄으로써 우리가 실행한 테스트가 모두 통과되었는지 점검함으로써 발생가능한 위기상황을 어느정도 잡아줄 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%ED%86%B5%EA%B3%BC%20%ED%99%95%EC%9D%B8%20%EB%B0%A9%EB%B2%95.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/JUnit5-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%ED%86%B5%EA%B3%BC%20%ED%99%95%EC%9D%B8%20%EB%B0%A9%EB%B2%95.PNG?raw=true)