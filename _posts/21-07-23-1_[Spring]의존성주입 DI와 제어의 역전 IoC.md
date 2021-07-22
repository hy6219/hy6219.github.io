# IoC/DI - 의존 관계 주입

🌻 **IoC(Inversion of Control) "제어의 역전"**

- 스프링에서는 객체를 new 생성자로 개발자가 관리하지 않고! **Spring Container**에 모두 맡기고, 싱글톤 패턴으로 관리가 이루어짐[프레임워크가 객체의 생명주기를 관리함]
- **객체 관리의 권한**이 **개발자 ▶️ 프레임워크**로 넘어감(**제어의 역전**)

🌻 **DI(Dependency Injection) "의존성 주입"**

- 개발자가 객체를 사용하기 위해서 주입을 받는 것

🌷 DI 장점

- 의존성으로부터 격리시킴으로써 **코드 테스트에 용이**
- DI를 통해서, 불가능한 상황을 Mock와 같은 기술을 통해서 안정적으로 테스트 가능[Mock : 외부와의 통신이 필요한 기술]
-Mock 기술을 수행하는 것을 Mocking이라고 함
-기댓값을 갖는 객체, 가짜 객체인 Mock 객체를 설정해주고 테스트가 가능
- 코드 확장 및 변경 시 영향을 최소화(추상화를 통해서 변경이 있더라도 내부 코드 변경이 최소화될 수 있는 것)
- 순환참조를 막을 수 있음
- 순환참조(Circular Reference) : 여러 객체들이 서로 이어진 상태로 , 돌고 돌아서 자기 자신을 참조하게 되는 상황

🌻 **DI를 간단한 예제를 통해서 익혀보자**

DI(의존성주입) 를 코드로 이해해보자

먼저, 아무런 별도의 개념 추가 없이 Base64인코딩을 하는 경우를 떠올린다면

```java
package com.company.ioc;

import java.util.Base64;

public class Encoder {

    public String encode(String msg){
        return Base64.getEncoder().encodeToString(msg.getBytes());
    }

}
```

위와 같이 인코딩을 처리해줄 메서드를 클래스 내부에 준비하고

```java
package com.company.ioc;

public class Main {
    public static void main(String[] args){
        String url = "http://www.naver.com/books/it?page=10&size=20&name=spring-boot";
        //인코딩을 할 것
        //Base-64 encoding
        Encoder encoder = new Encoder();
        String res =encoder.encode(url);
        System.out.println("Base64: "+res);
    }
}
```

실제 인코딩을 요청하는 Main에서는 msg의 인자로써 url을 넘겨주게 된다

그렇게 되면 콘솔에는

Base64: aHR0cDovL3d3dy5uYXZlci5jb20vYm9va3MvaXQ/cGFnZT0xMCZzaXplPTIwJm5hbWU9c3ByaW5nLWJvb3Q=

위와 같은 결과가 출력된다

이제 여기에 DI를 접목해보자!

-만약 URL 인코딩을 추가해야 하는 상황이 온다면 UrlEncoder 클래스를 아래와 같이 준비해주자

```java
package com.company.ioc;

import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;

public class UrlEncoding {
    public String encode(String msg){
        try {
            return URLEncoder.encode(msg, "UTF-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

다음은 Main클래스의 일부분이다

```java
UrlEncoder urlEncoder = new UrlEncoder();
String uRes = urlEncoder.encode(url);
System.out.println("URL Encoding: "+uRes);
```

그러면 콘솔에서 

URL Encoding: http%3A%2F%2Fwww.naver.com%2Fbooks%2Fit%3Fpage%3D10%26size%3D20%26name%3Dspring-boot

위와 같은 출력 결과를 확인해볼 수 있다

그런데 이와 같이 DI(의존성 주입)이 되지 않은 상황에서는 계속해서 클래스를 만들어서 Main에서 사용해야 한다는 번거로움과 비효율성이 발생한다

지금 UrlEncoder와 Encoder는 공통적으로 인코딩을 한다는 것(encode)을 수행한다 

▶️ 그래서 인코딩을 위한 인터페이스를 준비해볼 수 있다!

```java
package com.company.ioc;

public interface IEncoder {
    public String encode(String msg);
}
```

그렇게 된다면, 이 기능을 공통적으로 갖는 Encoder와 UrlEncoder는 아래와 같이 수정될 수 있다

```java
package com.company.ioc;

import java.util.Base64;

public class Encoder implements IEncoder{

    @Override
    public String encode(String msg) {
        return Base64.getEncoder().encodeToString(msg.getBytes());
    }
}
```

```java
package com.company.ioc;

import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;

public class UrlEncoder implements IEncoder{

    @Override
    public String encode(String msg) {
        try {
            return URLEncoder.encode(msg,"UTF-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

그러면 다형성이 적용되어 인터페이스를 이용한 인스턴스 생성이 가능해졌다는 점 외에는 스프링의 특징인 추상화의 면에서는 변화된 것은 없다

```java
package com.company.ioc;

public class Main {
    public static void main(String[] args){
        String url = "http://www.naver.com/books/it?page=10&size=20&name=spring-boot";
        //인코딩을 할 것
        //Base-64 encoding
        IEncoder encoder = new Encoder();
        String res =encoder.encode(url);
        System.out.println("Base64: "+res);

        //urlEncoder
        IEncoder urlEncoder = new UrlEncoder();
        String uRes = urlEncoder.encode(url);
        System.out.println("URL Encoding: "+uRes);

    }
}
```

지금부터 DI를 접목해보자

- 기존의 Encoder 클래스를 "Base64Encoder"로 변경해주고

기존의 Encoder 클래스는

- 인터페이스를 필드로 갖도록 하고
- 기본 생성자에

```java
this.인터페이스_필드=new Base64Encoder();
```

로 Base64Encoder를 생성시키도록 해주자

- 또한, 인터페이스의 encode 메서드를 아래와 같이 내부에서 불러오도록 하는 encode 메서드를 작성해주자!

```java
public String encode(String msg){
  return 인터페이스_필드.encode(msg);
}
```

그러면 정리를 해보면 Base64 인코딩을 해주는 인코더 클래스는 아래와 같이 정리되고(이전에는 이름이 "Encoder"였다)

```java
.package com.company.ioc;

import java.util.Base64;

public class Base64Encoder implements IEncoder{

    @Override
    public String encode(String msg) {
        return Base64.getEncoder().encodeToString(msg.getBytes());
    }
}
```

중간 다리 가교 역할을 해주는 Encoder 클래스는 아래와 같이 정리될 수 있다

```java
package com.company.ioc;

public class Encoder {
    private IEncoder iEncoder;

    public Encoder(){
        this.iEncoder=new Base64Encoder();
    }
    public String encode(String msg){
        return iEncoder.encode(msg);
    }
}
```

그런데 이 때, Encoder 클래스의 기본 생성자의 치명적인 실수가 있다

지금은 Base64만 필요한데, 만약에 UrlEncoder가 필요하면

계속 생성자부분을 수정해주어야만 한다

본질의 클래스를 건드리면서 비효율적이다!

DI란, 외부에서 내가 사용할 객체를 주입해주는 것이다!

바로, Encoder라는 클래스에서 Base64Encoder와 UrlEncoder를 다형성을 적용해서 생성 및 접근해주는 것이다!

```java
package com.company.ioc;

public class Encoder {
    private IEncoder iEncoder;

    public Encoder(**IEncoder iEncoder**){
        this.iEncoder=iEncoder;
    }
    public String encode(String msg){
        **return iEncoder.encode(msg);**
    }
}
```

그렇게 되면 외부(Main)에서는 Encoder 클래스에 객체를 주입시켜주면 된다!

바로 아래처럼 Encoder를 건드리지 않더라도 생성자 내부 인자값만 교체해주면 된다!

```java
package com.company.ioc;

public class Main {
    public static void main(String[] args){
        String url = "http://www.naver.com/books/it?page=10&size=20&name=spring-boot";
        //인코딩을 할 것
        //Base-64 encoding
        Encoder encoder = new Encoder(**new Base64Encoder()**);
       //Base64
        String result = encoder.encode(url);
        System.out.println("base 64 based on encoder class: "+result);

        encoder = new Encoder(new UrlEncoder());
        result = encoder.encode(url);
        System.out.println("url encoding: "+result);

    }
}
```

이 때, Encoder 생성자 내부인자를 **"주입 객체"**라고 부르도록 하자!

그리고 IEncoder라는 "**의존적인 객체**(다형성에 의해서 그 형태가 다르게 잡혀지기 때문에 의존적이라고 볼 수 있음)를 **외부에서 주입**받았다"라고 일컬을 수 있고, 이를 "**DI 의존성 주입**"이라고 할 수 있다!

그리고 위의 의존성 주입으로 코드가 보다 간결해지고, 코드 관리 및 재사용성이 보다 간편해졌다!

간단하게 예를 덧붙이면, 만약 Base32 Encoder를 추가하고 싶다면 별다른 복잡함 없이 다형성 적용을 통한 의존성을 활용하기 위해서

인터페이스 IEncoder를 구현하는 클래스 Base32Encoder.java만 작성해주면 된다! 간단하다!

🌻 **IoC(제어의 역전)을 예제로 익혀보자!**

Base64Encoder, UrlEncoder, Encoder, IEncoder를 위에서 작성한 것을 그대로 이용해서 스프링 부트 프로젝트에서 이용해보자

잠깐 위에서 DI 부분 중 Main 클래스를 살펴보면, Encoder의 내부에 new Base64Encoder()와 같이 개발자가 주입을 해주고 있다

하지만 위에서 언급했듯이

🌟 스프링에서 IoC는 스프링 컨테이너가 객체 생명주기라던지 생성 등에 대해서 모두 다 가져가기 때문에 직접 주입하는 부분이 줄어든다!

그러면 어떻게 스프링 컨테이너에게 이를 맡길까?

▶️ **@Component** 를 이용하자!

먼저 UrlEncoder에 한번 해당 어노테이션을 달아보자

```java
package com.ioc_di.ioc_di.encoder;

import org.springframework.stereotype.Component;

import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;

**@Component**
public class UrlEncoder implements IEncoder{
    @Override
    public String encode(String msg) {
        try {
            return URLEncoder.encode(msg,"UTF-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84%EA%B3%BC%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%A3%BC%EC%9E%85/%5BIoC%5D%EC%8A%A4%ED%94%84%EB%A7%81%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%97%90%EA%B2%8C%20%EA%B0%9D%EC%B2%B4%20%EA%B4%80%EB%A6%AC%20%EB%A7%A1%EA%B8%B0%EA%B8%B0_component.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84%EA%B3%BC%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%A3%BC%EC%9E%85/%5BIoC%5D%EC%8A%A4%ED%94%84%EB%A7%81%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%97%90%EA%B2%8C%20%EA%B0%9D%EC%B2%B4%20%EA%B4%80%EB%A6%AC%20%EB%A7%A1%EA%B8%B0%EA%B8%B0_component.PNG?raw=true)

Component 어노테이션을 붙여주면, 위와 같이 클래스 왼쪽에 스프링 마크가 생기는 것을 확인해볼 수 있다

이는 🌟 **스프링 빈으로 등록**된다는 것을 표시하는 것이다!

📌main에 있는 Application.java에 있는 @SpringBootApplication은 스프링 부트로 동작할 수 있게 해주는 것을 의미한다!

그 어노테이션 왼쪽에 돋보기 모양을 누르면 아래와 같이 등록된 빈으로써 UrlEncoder를 확인해볼 수 있다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84%EA%B3%BC%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%A3%BC%EC%9E%85/%5BIoC%5D%EB%93%B1%EB%A1%9D%EB%90%9C%20%EC%9E%90%EB%B0%94%EB%B9%88%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84%EA%B3%BC%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%A3%BC%EC%9E%85/%5BIoC%5D%EB%93%B1%EB%A1%9D%EB%90%9C%20%EC%9E%90%EB%B0%94%EB%B9%88%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.png?raw=true)

눈치 챘겠지만, JSP 서블릿에서 불렸던 "자바 빈"을 떠올려보면

**스프링에서의 빈**은 IoC개념과 접목해서 정리하면 **"IoC 컨테이너가 관리하는 자바 객체"**라고 정리해볼 수 있다!

그렇기 때문에 Application과 함께 urlEncoder 클래스를 확인해볼 수 있는 것이다!

그러면 SpringBootApplication 어노테이션을 눌러서 Component를 어떻게 관리하는지 살짝 눈으로만 확인해보자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84%EA%B3%BC%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%A3%BC%EC%9E%85/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%EC%9D%98%20Component%EA%B4%80%EB%A6%AC.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84%EA%B3%BC%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%A3%BC%EC%9E%85/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%EC%9D%98%20Component%EA%B4%80%EB%A6%AC.png?raw=true)

i.인텔리제이에서 ctrl키를 누른 채 SpringBootApplication을 누르면, [SpringBootApplication.java](http://springbootapplication.java) 파일을 확인해볼 수 있다(윗줄 왼쪽)

우리는 여기에서 먼저 ComponentScan이라는 어노테이션을 통해서 '아! 스프링부트가 Component라고 붙여진 객체들에 대해서 관리하는 구나!'라는 직감이 온다!!

ii. 그리고 SpringBootConfiguration 어노테이션을 눌러보자(윗줄 오른쪽)

그러면 Configuration 어노테이션이 보이는데, 이를 클릭하면

iii. [Configuration.java](http://configuration.java) 파일이 열린다

이 파일을 보면 다른 어노테이션들 중에서도 "Component" 어노테이션이 단연 돋보인다!!

즉,  이를 통해서 스프링부트는 개발자가 관리를 객체를 주입하는 수고스러움이 줄어들고, 이에 대한 책임을 프레임워크인 스프링부트가 짊어짐을 확인해볼 수 있다!

📌즉, **Component 어노테이션**은, 정리해보면, **이 클래스(객체)는 스프링부트 너가 좀 맡아서 관리해줘❤️** 라는 의미다! 그리고 더 덧붙이면, **스프링(부트)는 이 객체를 스캔해서 싱글톤 객체로 스프링 컨테이너에서 관리하게 된다!**

마찬가지로 Base64Encoder도 빈으로 등록해주자!

그러면 이렇게 컨테이너에서 관리되는 객체를 어떻게 쓸 수 있을까?

▶️ **SpringApplicationContext**를 통해서 접근하여 객체를 가져올 수 있다

해당 클래스를 한 번 작성해보자

먼저

- ApplicationContextAware 인터페이스를 구현해야 하고
- 빈 객체로써 등록하기 위해서 Component 어노테이션을 붙여주어야 한다

그렇게 되면 ApplicationContextAware의 메서드를 implemented하게 되어 아래와 같은

```java
package com.ioc_di.ioc_di;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

@Component
public class ApplicationContextProvider implements ApplicationContextAware {
    @Override
    public void setApplicationContext**(ApplicationContext applicationContext)** throws BeansException {

    }
}
```

setApplicationContext 메서드가 존재하게 된다.

그리고 파라미터를 보면 ApplicationContext가 주입되는 것을 볼 수 있는데

이 때 주입하는 것은 당연히 스프링이 할 일이다

그리고 부가적으로 할 일은

- 위의 메서드로 인해 스프링이 주입할 수 있도록 도와줄, "ApplicationContext"를 필드로 추가—-a
- setApplicationContext메서드에서 주입이 일어나면 context 필드에 applicationContext를 할당—-b
- context에 대한 getter 메서드 —-c

해주는 것이 필요하다

그런데 이때, 싱글톤으로 관리되기 때문에 

a는 private static, c는 public static으로 접근을 제한시켜줄 필요가 있다

```java
package com.ioc_di.ioc_di;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

@Component
public class ApplicationContextProvider implements ApplicationContextAware {
//a
    private static ApplicationContext context;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        context=applicationContext;//b
    }
//c
    public static ApplicationContext getContext(){
        return context;
    }

}
```

 🌟 ApplicationContext : 빈에 대한 IoC 컨테이너

그리고 Application 파일에서 IoC를 확인해볼 것인데

(1) 빈에 대한 주입을 스프링에게 맡기기 위해서 빈에 대한 IoC컨테이너인 ApplicationContext를 생성해주고

(2) DI는 해줄 것이지만, IoC(객체를 관리)는 new로 진행하지는 않을 것이다. 

(3) 그러면 예를 들어서 Base64Encoder의 경우, ApplicationContext 객체를 이용해서 getBean으로 접근할 수 있게 된다

```java
ApplicationContext context= ApplicationContextProvider.getContext();

**Base64Encoder base64Encoder= context.getBean(Base64Encoder.class);**
```

📌그리고!! 객체를 접근하는 방법은 

- 클래스로 접근
- 이름으로 접근

하는 방법이 있는데, "이름"이라 함은, 빈으로 등록된 명칭을 가리킨다!

그리고 웬만하면 객체가 한 개인 경우에는 클래스로 접근하는 것도 괜찮다

그렇게 되면 Main에서는 다음과 같이 테스트 해볼 수 있을 것이다!

```java
package com.ioc_di.ioc_di;

import com.ioc_di.ioc_di.encoder.Base64Encoder;
import com.ioc_di.ioc_di.encoder.Encoder;
import com.ioc_di.ioc_di.encoder.UrlEncoder;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class IoCDiApplication {

    public static void main(String[] args) {
        SpringApplication.run(IoCDiApplication.class, args);

        //스프링 application이 실행되고 나면
        //빈에 대한 주입을 스프링에게 맡김
        ApplicationContext context = ApplicationContextProvider.getContext();
        //빈은 1.이름 2. 클래스 타입으로 찾을 수 있음
        //객체는 주입해줄 것이지만 IoC(객체 관리)는 new로 해주지 않을 것
        //클래스
        Base64Encoder base64Encoder = context.getBean(Base64Encoder.class);
        //이름
        UrlEncoder    urlEncoder    = (UrlEncoder) context.getBean("urlEncoder");

        Encoder encoder= new Encoder(base64Encoder);
        String  url   = "http://www.naver.com/books/it?page=10&size=20&name=spring-boot";
        System.out.println("IoC container--base64: "+encoder.encode(url));

        encoder = new Encoder(urlEncoder);
        System.out.println("IoC container--urlEncoder: "+encoder.encode(url));
    }

}
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84%EA%B3%BC%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%A3%BC%EC%9E%85/%EC%8A%A4%ED%94%84%EB%A7%81_%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/KeyPoint/%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84%EA%B3%BC%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%A3%BC%EC%9E%85/%EC%8A%A4%ED%94%84%EB%A7%81_%EC%A0%9C%EC%96%B4%EC%9D%98%20%EC%97%AD%EC%A0%84.PNG?raw=true)

🌟🌟 스프링에서 빈을 주입받을 수 있는 장소 🌟⭐

- 변수
- 생성자
- setter

그런데 위에서 encoder=new Encoder를 통해서 부품을 바꿔주는 것보다 위에서 언급한 것처럼, setter를 이용해서 빈을 주입시켜보자

그렇다면, 중간 가교역할을 하는 Encoder 클래스에서 부품을 교체시켜주자

```java
package com.ioc_di.ioc_di.encoder;

public class Encoder {
    private IEncoder iEncoder;

    🌟**public Encoder(IEncoder iEncoder)**{//생성자에서 의존성 주입
        this.iEncoder=iEncoder;
    }

    **public void setEncoder(IEncoder iEncoder){**
        this.iEncoder=iEncoder;
    }

    public String encode(String msg){
        return iEncoder.encode(msg);
    }

}
```

그러면 Application.java는 new Encoder(부품)을 아래처럼 기존의 encoder 객체에서 setEncoder(부품)으로 대체할 수 있게 되어, 의존성을 주입하게 된다

```java
package com.ioc_di.ioc_di;

import com.ioc_di.ioc_di.encoder.Base64Encoder;
import com.ioc_di.ioc_di.encoder.Encoder;
import com.ioc_di.ioc_di.encoder.UrlEncoder;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class IoCDiApplication {

    public static void main(String[] args) {
        SpringApplication.run(IoCDiApplication.class, args);

        //스프링 application이 실행되고 나면
        //빈에 대한 주입을 스프링에게 맡김
        ApplicationContext context = ApplicationContextProvider.getContext();
        //빈은 1.이름 2. 클래스 타입으로 찾을 수 있음
        //객체는 주입해줄 것이지만 IoC(객체 관리)는 new로 해주지 않을 것
        //클래스
        Base64Encoder base64Encoder = context.getBean(Base64Encoder.class);
        //이름
        UrlEncoder    urlEncoder    = (UrlEncoder) context.getBean("urlEncoder");

        Encoder encoder= new Encoder(base64Encoder);
        String  url   = "http://www.naver.com/books/it?page=10&size=20&name=spring-boot";
        System.out.println("IoC container--base64: "+encoder.encode(url));

      🌟**encoder.setEncoder(urlEncoder);//setter에서 의존성 주입**
        System.out.println("IoC container--urlEncoder: "+encoder.encode(url));
    }

}
```

가교 역할을 하는 Encoder에서도 의존성 역전을 만들 수 있다!

하지만 간과해서는 안될 부분은

🌟 스프링은 빈이 2개 이상인 경우(위의 경우는 다형성으로 인해서), 매칭이 잘 되지 않는다!

이때에는 Qualifier 어노테이션을 이용해서 "**@Qualifier("매칭시켜줄 빈 객체의 이름")**"을 붙여주면 된다!(아래는 urlEncoder를 이용하고자 할 경우이다!)

```java
package com.ioc_di.ioc_di.encoder;

import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class Encoder {
    private IEncoder iEncoder;

    public Encoder(@Qualifier("urlEncoder") IEncoder iEncoder){
        this.iEncoder=iEncoder;
    }

    public void setEncoder(IEncoder iEncoder){
        this.iEncoder=iEncoder;
    }

    public String encode(String msg){
        return iEncoder.encode(msg);
    }

}
```

---

📌 빈 객체 이름은  변수에 대한 자바 컨벤션처럼 소문자로 시작하는 camel case로 되어 있다! 하지만 이를 변경해주고 싶다면 **@Component("변경하고 싶은 이름")** 으로 지정해줄 수 있다

```java
package com.ioc_di.ioc_di.encoder;

import org.springframework.stereotype.Component;

import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;

@Component("myUrlEncoder")
public class UrlEncoder implements IEncoder{
    @Override
    public String encode(String msg) {
        try {
            return URLEncoder.encode(msg,"UTF-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

---

다시 본론으로 돌아와서, Qualifier를 통해서 Encoder에 urlEncoder 객체에 대해서만 할당을 했기 때문에 Application.java도 아래와 같이 변경될 수 있을 것이다!

```java
package com.ioc_di.ioc_di;

import com.ioc_di.ioc_di.encoder.Base64Encoder;
import com.ioc_di.ioc_di.encoder.Encoder;
import com.ioc_di.ioc_di.encoder.UrlEncoder;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class IoCDiApplication {

    public static void main(String[] args) {
        SpringApplication.run(IoCDiApplication.class, args);

        Encoder encoder = context.getBean(Encoder.class);
        String  url   = "http://www.naver.com/books/it?page=10&size=20&name=spring-boot";
        System.out.println("IoC container--url: "+encoder.encode(url));

    }

}
```

지금의 모습을 보면 절대 new 생성자를 그 어디에서도 살펴볼 수가 없다!

즉, 모든 권한이 스프링에게 넘어갔다!

그런데 두 개 인코더를 모두 사용하고 싶다면⁉️

▶️ Component로 두지 않고 , 빈으로 직접 등록해보자

Application.java에서 또 하나의 클래스를 만들어보자

그리고 **Configuration 어노테이션**을 사용해서 **두 개 이상의 빈 객체를 관리**할 수 있도록 하자

더불어서, 직접 빈으로써 등록하기 위해서 Bean 어노테이션을 붙여주자

그런데 지금은 개발자 입장에서는 오버로딩을 하여 두 인코더를 식별한다고 생각할 수 있지만, 스프링은 혼란스러울 수 있다

따라서 위에서 Qualifier처럼, Bean("식별할 수 있는 이름")으로 어노테이션을 명명해주고, 기존 빈 객체와 충돌되지 않도록 이름을 지어주도록 하자

그러면, **getBean("빈 객체 이름", Encoder.class)**로 빈 객체를 매칭시켜줄 수 있다!

```java
package com.ioc_di.ioc_di;

import com.ioc_di.ioc_di.encoder.Base64Encoder;
import com.ioc_di.ioc_di.encoder.Encoder;
import com.ioc_di.ioc_di.encoder.UrlEncoder;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@SpringBootApplication
public class IoCDiApplication {

    public static void main(String[] args) {
        SpringApplication.run(IoCDiApplication.class, args);

        //스프링 application이 실행되고 나면
        //빈에 대한 주입을 스프링에게 맡김
        ApplicationContext context = ApplicationContextProvider.getContext();

        Encoder encoder = context.getBean("base64Encode",Encoder.class);
        String  url   = "http://www.naver.com/books/it?page=10&size=20&name=spring-boot";
        System.out.println("IoC container--base64: "+encoder.encode(url));

        encoder = context.getBean("urlEncode",Encoder.class);
        System.out.println("IoC container-url: "+encoder.encode(url));

    }

}
//두 개 이상의 빈 객체를 관리하자
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

실제로는 ApplicationContext를 이용하는 것보다 어노테이션 등을 사용!(예: AutoWired)