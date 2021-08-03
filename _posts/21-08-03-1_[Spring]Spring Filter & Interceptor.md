# Spring Boot Filter와 Interceptor

![https://postfiles.pstatic.net/20140627_149/platinasnow_1403836406321rAl0L_PNG/Untitled-1.png?type=w2](https://postfiles.pstatic.net/20140627_149/platinasnow_1403836406321rAl0L_PNG/Untitled-1.png?type=w2)

출처: [https://blog.naver.com/platinasnow/220035316135](https://blog.naver.com/platinasnow/220035316135) 

🌟 스프링에서

- Filter
- Interceptor
- AOP

는 **어떤 기준 전 및 후 등에 어떠한 공통된 행동을 할 때 사용되는 기능들**이다!

학습 전, 구글링하여 내용을 정리해보았습니다! 출처는 두 군데 입니다!

- [https://blog.naver.com/platinasnow/220035316135](https://blog.naver.com/platinasnow/220035316135)  심해펭귄의 심해도서관 님 블로그
- [https://goddaehee.tistory.com/154](https://goddaehee.tistory.com/154) 갓대희 님 블로그

[Interceptor vs Filter vs AOP](https://www.notion.so/1ceafe109a3747299db992adada41cee)

- Interceptor와 Filter는 서블릿 단위에서 실행되지만, AOP는 메서드 앞에 프록시 패턴의 형태로 실행
- 요청이 들어올 경우, Filter ➡️ Interceptor ➡️ AOP ➡️ Interceptor ➡️ Filter 순으로 거치게 됨

📌 요청이 들어오는 순서에 따른 메서드 실행 순서 📌

1.서버 실행

2.서블릿이 올라오는 동안 init 실행

3.doFilter 실행

4.컨트롤러에 들어가기 전 preHandler 실행

5.컨트롤러에서 나와서 postHandler, afterCompletion, doFilter 순으로 메서드 실행

6.서블릿 종료 혹은 내용 변경 시 destory 실행

이제부터는 구글링 외 공부한 내용을 정리한 부분을 아래에 기록할 것이다!

---

## 1. Filter(필터)

1. **요청 및 응답의 정보를 변경**하거나
2. 스프링에 의해 **데이터가 변환되기 전의 순수한 클라이언트의 요청/응답 값을 확인**

할 수 있는 웹 어플리케이션에서 관리되는 영역

🌟 특징 🌟

1. 유일하게 ServletRequest, ServletResponse 의 객체를 변환 가능
2. 스프링 부트 프레임워크에서 클라이언트로부터 오는 요청이나 응답에 대해서 최초 혹은 최종 단계 위치에 존재[요청일때에는 최초, 응답일 때에는 최종]
3. 주로 요청/응답의 logging 용도로 활용
4. 3 외에도, 인증과 관련된 로직들을 필터에서 처리하여 선/후 처리됨으로써 서비스 비즈니스 로직과 분리

먼저 공부에 앞서서, 아직 롬복(lombok)을 학습하지는 않았지만 가볍게 살펴보기로 한다!

롬복을 사용하면 기존의 getter, setter, constructor를 편하게 만들 수 있다!

먼저 이름과 나이를 필드로 갖는 User dto를 롬복을 이용해서 만들어보면

아래와 같다

```java
package com.example.filter.dto;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

**@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor**
public class User {
    private String name;
    private int age;

}
```

@Getter는 클래스의 getter 메서드를

@Setter는 클래스의 setter 메서드,

@NoArgsConstructor는 기본 생성자,

@AllArgsConstructor는 모든 필드를 파라미터로 갖는 생성자

를 의미한다!

이렇게 롬복을 적용해주고, structure를 확인해보면

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/lombok%20%EB%A7%9B%EB%B3%B4%EA%B8%B0.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/lombok%20%EB%A7%9B%EB%B3%B4%EA%B8%B0.PNG?raw=true)

아래와 같이 기존에 일일히 적용해주었던 getter와 setter, 기본 생성자, 필드 생성자가 확인되는 것을 볼 수 있다

그리고 Data 어노테이션은 

- 기본 생성자
- getter
- setter
- equals
- hashCode
- toString

까지 모두 생성해준다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/Data%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/Data%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98.PNG?raw=true)

다만, Data 어노테이션과 AllArgsConstructor를 같이 사용하게 된다면 생성자는 필드 생성자만 남게 되므로, 기본 생성자도 같이 사용하고 싶다면 NoArgsConstructor도 같이 넣어주자

```java
package com.example.filter.dto;

import lombok.*;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private int age;

}
```

이제 롬복을 사용함으로써 우리는 필드만 만들어주면 된다!

🌟 중요한 점은, 롬복을 initializer에서 추가하지 않았다면, dependencies에

```java
compileOnly 'org.projectlombok:lombok'
**annotationProcessor 'org.projectlombok:lombok'**
```

를 붙여주면 되는데, 중요한 것은 annotationProcessor 부분을 꼭 붙여주어야 한다(컴파일될때 gradle에서 annotationProcessor가 같이 동작할 수 있도록)

**롬복은 컴파일할때만 적용된다!**

🌟 기본적으로 ,스프링에서는 **Slf4j** 라는 **로깅 관련 라이브러리**를 제공한다!

이는 롬복을 사용할 경우,접근할 수 있다

해당 어노테이션을 RestController에 붙여주도록 하자(Slf4j에서 j는 java를 의미한다)

간단하게, RestController에서는 User 객체를 body로 받아서 처리하도록 post 메서드를 만들어주자

```java
package com.example.filter.controller;

import com.example.filter.dto.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

**@Slf4j**
@RestController
@RequestMapping("/api")
public class RestApiController {

    @PostMapping("/user")
    public User post(@RequestBody User user){

    }

}
```

위와 같이 Slf4j를 이용하면, 앞으로는

log라는 객체를 이용해서 로깅을 할 수 있는데

이를 출력할 때에, log.info를 이용하면 콘솔에서 로그확인이 가능하다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%EC%8A%A4%ED%94%84%EB%A7%81%20%EB%A1%9C%EA%B9%85-Slf4j.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%EC%8A%A4%ED%94%84%EB%A7%81%20%EB%A1%9C%EA%B9%85-Slf4j.PNG?raw=true)

log.info는 위의 그림과 같이 

```java
log.info("format string",value);
```

편의상 앞부분을 "format string"이라고 명시하겠다!

그 이유는 마치 c나 자바에서의 printf와 유사한 쓰임새를 보여서 이렇게 편의상 부르겠다

format string에서는 중괄호를 이용하는데, 이 중괄호가 여럿일 경우 컴마를 이용해서 구분해주자

그리고 format string 이후에는 앞에서부터 순서대로 어떤 중괄호에 어떤 값이 들어갈 지 매칭시켜주자

그러면 위와 같이 로그가 콘솔에 찍히는 것을 확인해볼 수 있다

✴️나중에 현업에서는 이 로그를 json형태와 접목하여 사용할 수 있다고 한다. 이부분은 기회가 된다면, 찾아보고 공부해보자!!

## 1. 필터 🌻

```java
package com.example.filter.controller;

import com.example.filter.dto.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/api")
public class RestApiController {

    @PostMapping("/user")
    public User post(@RequestBody User user){
        **log.info("User: {} [name: {}, age: {}]",user,user.getName(),user.getAge());**
        return user;
    }

}
```

이번에는 필터를 만들어보자

먼저 중요한 부분은!!

- Filter라는 인터페이스(java.servlet 하위 패키지)를 구현
- 자바 빈 객체로 등록하여 관리될 수 있도록 해야 함

의 두 파트가 중요하다!

우리는 이번에 Filter의 메서드 중 doFilter만 오버라이딩하기로 하자

```java
package com.example.filter.filter;

import org.springframework.stereotype.Component;

import javax.servlet.*;
import java.io.IOException;

**@Component**
public class GlobalFilter implements **Filter** {
    @Override
    public void **doFilter**(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

    }
}
```

📌 FilterChain 📌

![https://docs.spring.io/spring-security/site/docs/current/reference/html5/images/servlet/architecture/multi-securityfilterchain.png](https://docs.spring.io/spring-security/site/docs/current/reference/html5/images/servlet/architecture/multi-securityfilterchain.png)

source: [https://docs.spring.io/spring-security/site/docs/current/reference/html5/](https://docs.spring.io/spring-security/site/docs/current/reference/html5/)

클라이언트 요청 ▶️ 컨테이너에서 FilterChain을 생성(Filter들과 서블릿[요청 uri 경로를 기반으로 HttpServletRequest를 진행해야만 하는 서블릿]을 포함)

[밑에서 확인한 문서를 참조한다면, 이 컨테이너는 서블릿 컨테이너일 것으로 추측된다]

- 스프링에서 서블릿은 DispatcherServlet 인스턴스이고, 하나의 서블릿은 하나의 HttpServletRequest와 HttpServletResponse를 갖는다

➕ FilterChain의 doFilter메서드가 궁금하여 인텔리제이에서 추적해서 보았는데, javax.servlet패키지라서, oracle api를 참조하였다

[https://docs.oracle.com/javaee/7/api/javax/servlet/FilterChain.html](https://docs.oracle.com/javaee/7/api/javax/servlet/FilterChain.html)

FilterChain인스턴스.doFilter메서드는

- ServletRequest
- ServletResponse

를 파라미터로 요구하고

IOException과 ServletException을 throws한다

그리고 해당 메서드의 설명을 보니, 

> 1.chain 내부에 있는 필터들 중 **다음 필터의 실행을 유발**시키거나,
2.호출되는 필터가 체인 내부의 필터들 중 **마지막 필터**인 경우
▶️ 체인 끝에 있는 리소스가 호출되도록 한다

Causes the next filter in the chain to be invoked, or if the calling filter is the last filter in the chain, causes the resource at the end of the chain to be invoked.

라고 되어 있다!

그러면, 이

chain.doFilter(request,response);

를 기준으로 전에는 전처리, 후에는 후처리가 가능할 것이다!

로그를 간단하게 찍어볼 것인데, 요청 uri를 확인해볼 것이다

그런데 uri를 가져오려면 HttpServletRequest가 필요한데 우리는 지금 ServletRequest를 사용하고 있다

HttpServletRequest.java를 잘 보면, ServletRequest를 상속받은 것을 알 수 있다

즉, 우리는 부모클래스의 객체를 자식 클래스로 down-casting해줄 필요가 있다

따라서 명시적으로 캐스팅을 표시해주어야 한다!

```java
public interface HttpServletRequest extends **ServletRequest**
```

그런데, BufferedReader는 커서를 기반으로 넘겨가면서 읽게 되는데,

```java
Servlet.service() for servlet [dispatcherServlet] in context 
with path [] threw exception [Request processing failed; 
nested exception is java.lang.IllegalStateException: getReader() 
has already been called for this request] with root cause
```

위와 같이 에러가 발생한 것은, 커서가 끝까지 이동해서 그 이후에는 읽을 것이 없어서 발생하는 것이다 이때에는 

기존은 HttpServletRequest가 아닌 ContentCachingRequestWrapper를 이용해주자

마찬가지로 HttpServletResponse를 ContentCachingResponseWrapper로 변경해주자

추가 참조 : [https://leeyongjin.tistory.com/entry/request-response-logging](https://leeyongjin.tistory.com/entry/request-response-logging)

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/ContentCachingRequestWrapper.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/ContentCachingRequestWrapper.html)

🌹 **ContentCachingRequestWrapper** 🌹

- HttpServletRequestWrapper 를 상속받은 클래스
- 따라가다 보면, ServletRequest가 조상격임을 확인 가능
- 입력 스트림과 리더로부터 모든 컨텐츠를 읽어온 데이터를 임시저장하고, 이 컨텐츠는 바이트 배열로 얻어오게 된다

이렇게 ContentCachingRequestWrapper로 변환해주면 내용을 읽고 끝나도 계속 읽을 수 있게 된다!

```java
package com.example.filter.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import org.springframework.web.util.ContentCachingRequestWrapper;
import org.springframework.web.util.ContentCachingResponseWrapper;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.BufferedReader;
import java.io.IOException;

@Slf4j
@Component
public class GlobalFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        //전처리
        **ContentCachingRequestWrapper httpServletRequest= (ContentCachingRequestWrapper) request;**
        **ContentCachingResponseWrapper httpServletResponse=(ContentCachingResponseWrapper)response;**

        String uri= httpServletRequest.getRequestURI();
        //body읽어오기
        BufferedReader br=httpServletRequest.getReader();

        br.lines().forEach(line->{
            log.info("uri: {}, read content: {}",uri,line);
        });

      chain.doFilter(httpServletRequest, httpServletResponse);
        //후처리

    }
}
```

그런데 ClassCastException이 발생한다!

그 이유는 해당 경우에는 new 생성자로 생성시켜주어야 하기 때문이다!

아래를 참조해서 

- HttpServletRequest로 캐스팅 후 -a
- a를 ContentCachingRequestWrapper로 변환

해주도록 하고, 이를 동일하게 response에도 적용해주자!!

```java
public ContentCachingRequestWrapper(HttpServletRequest request) {
		super(request);
		int contentLength = request.getContentLength();
		this.cachedContent = new ByteArrayOutputStream(contentLength >= 0 ? contentLength : 1024);
		this.contentCacheLimit = null;
	}
```

그러면 이번에는 아래처럼 HttpMessageNotReadableException: Required request body is missing 에러가 발생한다!

```java
2021-08-02 15:48:05.536  INFO 40684 --- [nio-8089-exec-1] com.example.filter.filter.GlobalFilter   : uri: /api/user, read content: {
2021-08-02 15:48:05.537  INFO 40684 --- [nio-8089-exec-1] com.example.filter.filter.GlobalFilter   : uri: /api/user, read content:   "name":"김길동",
2021-08-02 15:48:05.537  INFO 40684 --- [nio-8089-exec-1] com.example.filter.filter.GlobalFilter   : uri: /api/user, read content:   "age":20
2021-08-02 15:48:05.537  INFO 40684 --- [nio-8089-exec-1] com.example.filter.filter.GlobalFilter   : uri: /api/user, read content: }
2021-08-02 15:48:05.608  WARN 40684 --- [nio-8089-exec-1] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.http.converter.HttpMessageNotReadableException: Required request body is missing: public com.example.filter.dto.User com.example.filter.controller.RestApiController.post(com.example.filter.dto.User)]
```

즉, body에 실어진 값이 없기 때문이다!!

이 상황에서 해결할 수 있는 방법은 chain.doFilter 이후에 다뤄질 "후처리"에서 접근이 가능하다!

이 후처리를 위해서 response에 값을 넣어주기 위해서 reader의 커서가 맨 뒤로 옮겨져 있을 경우, response가 읽을 값이 없기 때문에 getReader부분을 주석처리해주자

```java
package com.example.filter.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import org.springframework.web.util.ContentCachingRequestWrapper;
import org.springframework.web.util.ContentCachingResponseWrapper;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.BufferedReader;
import java.io.IOException;

@Slf4j
@Component
public class GlobalFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        //전처리
        ContentCachingRequestWrapper httpServletRequest= new ContentCachingRequestWrapper((HttpServletRequest) request);//길이만 초기화
        ContentCachingResponseWrapper httpServletResponse=new ContentCachingResponseWrapper((HttpServletResponse)response);

//        //body읽어오기
//        BufferedReader br=httpServletRequest.getReader();
//
//        br.lines().forEach(line->{
//            log.info("uri: {}, read content: {}",uri,line);
//        });

        chain.doFilter(httpServletRequest, httpServletResponse);
        //후처리
        String uri= httpServletRequest.getRequestURI();
        String reqContent=new String(httpServletRequest.getContentAsByteArray());

        log.info("요청 uri:{}, reqeust body:{}",uri,reqContent);

        String resContent=new String(httpServletResponse.getContentAsByteArray());
        int httpStatusCode=httpServletResponse.getStatus();

        log.info("response status:{}, response body:{}",httpStatusCode,resContent);
    }
}
```

그리고 [http://localhost:8089/api/user](http://localhost:8089/api/user) 로 body에

```java
{
  "name":"김길동",
  "age":20
}
```

위와 같은 application/json content-type으로 요청을 보내면

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%EB%A1%9C%EA%B9%85%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20filter%EC%97%90%EC%84%9C%EC%9D%98%20%EC%A0%84%EC%B2%98%EB%A6%AC,%20%ED%9B%84%EC%B2%98%EB%A6%AC.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%EB%A1%9C%EA%B9%85%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20filter%EC%97%90%EC%84%9C%EC%9D%98%20%EC%A0%84%EC%B2%98%EB%A6%AC,%20%ED%9B%84%EC%B2%98%EB%A6%AC.PNG?raw=true)

위와 같이 로그로 요청 uri와 요청body, 응답 상태코드, 응답 body를 확인해볼 수 있다!

그런데 talend에서 확인해봤을 때 200 OK는 떴지만, Body는 여전히 비어져 있는 것을 확인해볼 수 있는데, 이는 지금 httpResponse에 접근을 해서 getContentByteArray로 한번 읽었기 때문이다

이는 아래의 방법처럼 한번 복사해둘 필요가 있는 것 같다!

[ContentCachingResponseWrapper Produces Empty Response](https://stackoverflow.com/questions/39935190/contentcachingresponsewrapper-produces-empty-response)

따라서 copyBodyToResponse() 메서드를 사용해주자!!

java파일을 확인해보면 해당 메서드는 void 타입으로 copyBodyToResponse(boolean)이라는 오버로딩된 메서드를 부르게 된다!

```java
public void copyBodyToResponse() throws IOException {
		copyBodyToResponse(true);
	}
```

```java
protected void copyBodyToResponse(boolean complete) throws IOException {
		if (this.content.size() > 0) {
			HttpServletResponse rawResponse = (HttpServletResponse) getResponse();
			if ((complete || this.contentLength != null) && !rawResponse.isCommitted()) {
				if (rawResponse.getHeader(HttpHeaders.TRANSFER_ENCODING) == null) {
					rawResponse.setContentLength(complete ? this.content.size() : this.contentLength);
				}
				this.contentLength = null;
			}
			this.content.writeTo(rawResponse.getOutputStream());
			this.content.reset();
			if (complete) {
				super.flushBuffer();
			}
		}
	}
```

오버로딩된 메서드를 살펴보니, writeTo메서드를 내부적으로 호출해서 content 필드에 복사해놓는 것으로 파악된다

```java
package com.example.filter.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import org.springframework.web.util.ContentCachingRequestWrapper;
import org.springframework.web.util.ContentCachingResponseWrapper;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.BufferedReader;
import java.io.IOException;

@Slf4j
@Component
public class GlobalFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        //전처리
        ContentCachingRequestWrapper httpServletRequest= new ContentCachingRequestWrapper((HttpServletRequest) request);//길이만 초기화
        ContentCachingResponseWrapper httpServletResponse=new ContentCachingResponseWrapper((HttpServletResponse)response);

//        //body읽어오기
//        BufferedReader br=httpServletRequest.getReader();
//
//        br.lines().forEach(line->{
//            log.info("uri: {}, read content: {}",uri,line);
//        });

        chain.doFilter(httpServletRequest, httpServletResponse);
        //후처리
        String uri= httpServletRequest.getRequestURI();
        String reqContent=new String(httpServletRequest.getContentAsByteArray());

        log.info("요청 uri:{}, reqeust body:{}",uri,reqContent);

        String resContent=new String(httpServletResponse.getContentAsByteArray());
        int httpStatusCode=httpServletResponse.getStatus();
        **httpServletResponse.copyBodyToResponse();**
        log.info("response status:{}, response body:{}",httpStatusCode,resContent);
    }
}
```

본론으로 돌아와서, httpServletResponse.copyBodyToResponse(); 를 httpServletResponse에 대해서 적용해주면(위치는 위와 같이 response에 대한 write 접근하는 부분이 모두 끝난 후에 적용해주자) response가 복사되어 이번에는 talend에서 200 OK와 함께 

```java
{
  "name":"김길동",
  "age":20
}
```

위와 같은 json 형태 응답이 확인된다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/Filter%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EC%A0%84%EC%B2%98%EB%A6%AC,%20%ED%9B%84%EC%B2%98%EB%A6%AC%20%EB%B0%8F%20%EC%9D%B4%EB%A5%BC%20%ED%86%B5%ED%95%9C%20response%20body%20%EB%82%B4%EB%A0%A4%EC%A3%BC%EA%B8%B0.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/Filter%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EC%A0%84%EC%B2%98%EB%A6%AC,%20%ED%9B%84%EC%B2%98%EB%A6%AC%20%EB%B0%8F%20%EC%9D%B4%EB%A5%BC%20%ED%86%B5%ED%95%9C%20response%20body%20%EB%82%B4%EB%A0%A4%EC%A3%BC%EA%B8%B0.PNG?raw=true)

🌟 내가 Body내용을 이용할 필요가 있다! ▶️  ContentCachingRequestWrapper,  ContentCachingResponseWrapper 를 이용!

🌟 &&  ContentCachingRequestWrapper,  ContentCachingResponseWrapper를 사용할 때, BufferedReader, getReader(), getContentAsByteArray() 등을 사용해서 response나 request를 한 번 읽고 난 후에, response에 body에 읽고 쓰는 작업이 필요하다면, **response.copyBodyToResponse()**;를 해주자!

필터에서는 jsp 서블릿에서 적용했던 세션 정보를 활용한 로그인 로그아웃 로직에 응용해볼 수도 있고, 로깅에도 이용해볼 수 있다

필터에 적용할지, 인터셉터에 적용할지, aop에 적용할지(특히 필터와 인터셉터 사이 고민 중에는) 상황에 따라 적용하기!

(수강중인 강의 팁!!💡)인증의 경우에는 인터셉터에 넣는 것도 괜찮을 듯..!

---

### 특정 구역에 필터 적용하기

특정 구역에 필터를 적용하기 위해서 RestApiController를 복사하자

그리고 post mapping부분만 temp로 수정해주자

```java
package com.example.filter.controller;

import com.example.filter.dto.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/api")
public class ApiUserController {

    @PostMapping("/temp")
    public User post(@RequestBody User user){
        log.info("User: {} [name: {}, age: {}]",user,user.getName(),user.getAge());
        return user;
    }

}
```

우리는 RestApiController.java부분에만 필터를 적용해보자

먼저 [Application.java](http://application.java) 에서 ServletComponentScan 어노테이션을 달아주자

```java
package com.example.filter;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

@SpringBootApplication
**@ServletComponentScan**
public class FilterApplication {

    public static void main(String[] args) {
        SpringApplication.run(FilterApplication.class, args);
    }

}
```

그리고 GlobalFilter.java에서는 더이상 Component가 아닌 WebFilter를 넣어서 사용해주자

더불어서 WebFilter의 urlPatterns값으로 RestApiController에 대응되는 url 주소를 매칭시켜주자

```java
package com.example.filter.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import org.springframework.web.util.ContentCachingRequestWrapper;
import org.springframework.web.util.ContentCachingResponseWrapper;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.BufferedReader;
import java.io.IOException;

@Slf4j
//@Component
**@WebFilter(urlPatterns = "/api/user")**
public class GlobalFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        //전처리
        ContentCachingRequestWrapper httpServletRequest= new ContentCachingRequestWrapper((HttpServletRequest) request);//길이만 초기화
        ContentCachingResponseWrapper httpServletResponse=new ContentCachingResponseWrapper((HttpServletResponse)response);

//        //body읽어오기
//        BufferedReader br=httpServletRequest.getReader();
//
//        br.lines().forEach(line->{
//            log.info("uri: {}, read content: {}",uri,line);
//        });

        chain.doFilter(httpServletRequest, httpServletResponse);
        //후처리
        String uri= httpServletRequest.getRequestURI();
        String reqContent=new String(httpServletRequest.getContentAsByteArray());

        log.info("요청 uri:{}, reqeust body:{}",uri,reqContent);

        
        String resContent=new String(httpServletResponse.getContentAsByteArray());
        int httpStatusCode=httpServletResponse.getStatus();
				httpServletResponse.copyBodyToResponse();
        log.info("response status:{}, response body:{}",httpStatusCode,resContent);
    }
}
```

요청은 저번과 동일하게  두 컨트롤러 모두에 적용해주면 이번에는 /api/user에 대응되는 컨트롤러를 통했을 때에만 request와 response에 대한 내용을 찍는 것을 확인해볼 수 있다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%ED%8A%B9%EC%A0%95%20RestController%EC%97%90%20Filter%20%EC%A0%81%EC%9A%A9%ED%95%B4%EC%A3%BC%EA%B8%B0.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%ED%8A%B9%EC%A0%95%20RestController%EC%97%90%20Filter%20%EC%A0%81%EC%9A%A9%ED%95%B4%EC%A3%BC%EA%B8%B0.gif?raw=true)

urlPatterns의 값으로 string 배열값을 넣어서 여러 컨트롤러 중 특정 컨트롤러들에게 한정시킬 수도 있다!!

## 2. Interceptor

- 필터와 다르게, Spring context에 등록됨!(따라서 어떤 메서드를 사용하는지, handlerMapping의 결과로 어떤 컨트롤러에 매핑이 되었는지 등을 사용할 수 있음)
- **주로 인증 단계**를 처리하거나, 로깅하는 데에 사용
- 선/후처리 과정을 통해서 서비스 비즈니스 로직과 분리

먼저 인터셉터의 경우, "인증"을 처리하는데에 주로 사용될 수 있으므로

이러한 사용목적을 기반으로, 먼저 권한을 갖고 있지 않은 사용자까지 모두 포함하여 사용할 수 있도록 하는 public 컨트롤러를 아래와 같이 간단하게 "public hello"를 출력하도록 해주자

```java
package com.example.interceptor.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

//권한이 없는 사용자까지 모두 들어올 수 있도록 하기
@RestController
@RequestMapping("/api/public")
public class PublicController {

    @GetMapping("/hello")
    public String hello(){
        return "public Hello";
    }

}
```

그리고 인증이 허가된 사용자(인증, 허가에 대한 공부 필요할 듯!) 가 접근할 수 있는 private controller도 만들자! 이 역시도 비슷하게 "private hello"를 출력하도록 하자

```java
package com.example.interceptor.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/private")
public class PrivateController {

    @GetMapping("/hello")
    public String hello(){
        return "private Hello";
    }

}
```

그리고 권한에 대한 차이를 주기 위해서 Auth라는 어노테이션을 만들기로 하자

- javadoc에 어노테이션을 등록
- 메서드 선언시 / 클래스나 인터페이스 혹은 열거형 등 선언 시 사용 가능
- 런타임동안 적용

Auth는 위와 같이 사용될 수 있도록 하고, privatecontroller에서만 붙여서 적용해주자

```java
package com.example.interceptor.annotation;

import java.lang.annotation.*;

@Documented
@Target({ElementType.TYPE,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Auth {
}
```

```java
package com.example.interceptor.controller;

import com.example.interceptor.annotation.Auth;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/private")
**@Auth**
public class PrivateController {

    @GetMapping("/hello")
    public String hello(){
        return "private Hello";
    }

}
```

이를 이용해서 인터셉터에서는 어노테이션이 붙어있으면 통과, 그렇지 않으면 통과시키지 않도록 하자

- HandlerInterceptor를 구현하고
- preHandle이라는 메서드를 오버라이딩해주자

✴️ 스프링에서는 인터셉터를 사용하기 위해서 HandlerInterceptor 인터페이스와 HandlerInterceptorAdaptor 추상클래스 를 지원

[](https://www.baeldung.com/spring-mvc-handlerinterceptor)

- preHandle() : 컨트롤러로 요청이 들어가기 전에 수행

-요청이 핸들러에 의해서 추가로 처리되어야 하는지(true) 아니면 처리되지 않아야 하는지(false)를 스프링에게 알려줌

- postHandle() :HandlerAdaptor에서 요청을 처리한 직후 뷰를 생성하기 전에 호출
- afterCompletion() : 요청 처리가 완료된 후, 즉 뷰를 렌더링한 후에 진행

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerInterceptor.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerInterceptor.html)

[AsyncHandlerInterceptor (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/AsyncHandlerInterceptor.html)

afterCompletion의 경우에는, 비동기 처리 시에 AsyncHandlerInterceptor를 참고해야 한다

- AsyncHandlerInterceptor 인터페이스의 afterConcurrentHandlingStarted() 메서드

-poshHandle() 및 afterCompletion() 핸들러가 동시에 수행될 때, 스레드 로컬 변수를 정리

그리고 저번 필터때 처럼, request와 response에 대해서 읽는 작업이 발생하면 커서의 이동으로 내용 손실이 있는 것 같은 상황이 발생한다(실제로는 커서이동으로 인한 것)

하지만 이때 전역 필터에서( 동일 패키지 내에서) ContentCachingRequestWrapper로 형변환해주었다면,  인터셉터에서도 해당 클래스로 request(HttpServletRequest)를 형변환하여 body에 대한 읽는 작업을 수행할 수 있다

- 그 이유는 필터에서 외부에서 들어온 ServletRequest를 이용해서 전처리 및 후처리된 내용이 그대로 넘어가야 하기 때문!
- 그렇지 않고 필터에서 ContentCachingRequestWrapper 처리를 해주지 않는다면, 외부에서의 ServletRequest에 대한 처리 부분 없이 바로 HttpServletRequest가 들어가게 되어 문제가 된다!

![https://justforchangesake.files.wordpress.com/2014/05/spring-request-lifecycle.jpg](https://justforchangesake.files.wordpress.com/2014/05/spring-request-lifecycle.jpg)

source: [https://justforchangesake.files.wordpress.com/2014/05/spring-request-lifecycle.jpg](https://justforchangesake.files.wordpress.com/2014/05/spring-request-lifecycle.jpg)

📌 스프링 MVC 요청 생명 주기(요청 순서) 📌

1. HandlerMapping을 통해 실행될 핸들러 및 인터셉터 전달
2. 인터셉터의 preHandle 실행
3. HandlerAdaptor에 Handler를 전달하여 해당 컨트롤러의 argument 매핑 및 handler의 메서드 유발([https://www.baeldung.com/spring-mvc-handler-adapters](https://www.baeldung.com/spring-mvc-handler-adapters)-handlerInterceptor와 관련있는 것으로 파악)
4. Interceptor의 postHandle 실행
5. View Resolver를 통해 실제 보여줄 뷰를 렌더링하여 HttpServletResponse에 write하여 dispatcher servlet, filter를 거쳐 화면에 뷰를 보여주기
6. interceptor의 afterCompletion 실행

[Spring MVC 생명주기](https://jistol.github.io/spring/2018/03/17/spring-mvc-structure/)

🌟 Handler

- 타입이 어떤 것으로 변환되는지, 검증에 대한 처리, formatting(형식화) 에 대한 정보를 갖고 있음
- 어떤 컨트롤러에 매핑되었는지 정보를 가지고 있음

HandlerMethod : 메서드와 빈으로 구성된 핸들러에 대한 정보를 다루는 클래스로, Object 클래스를 상속받음

[HandlerMethod (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/method/HandlerMethod.html)

```java
package com.example.interceptor.interceptor;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.resource.ResourceHttpRequestHandler;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Slf4j
@Component
public class AuthInterceptor **implements HandlerInterceptor**{

    @Override
    public boolean **preHandle**(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        String uri = request.getRequestURI();
        log.info("request uri: {}",uri);
        return false;
    }

    //어노테이션이 달려있는지 볼 것
    //clazz는 class가 예약어이기 때문에 자주 대체재로써 사용되는 이름
    //Class로 어노테이션 확인
    private boolean checkAnnotation(Object handler,
                                    Class clazz){
        //리소스(html,js 등)에 대한 요청은 무조건 통과
        if(handler instanceof **ResourceHttpRequestHandler**){
            return true;
        }

        //어노테이션이 달려있는지
        **HandlerMethod handlerMethod=(HandlerMethod) handler;**

        **if(handlerMethod.getMethodAnnotation(clazz)!=null ||
           handlerMethod.getBeanType().getAnnotation(clazz)!=null)**{
            //어노테이션을 갖고 있는지 체크
            return true;//auth가 있을 때
        }
        return false;//모든 조건에 부합한 경우

    }
}
```

먼저 위와 같이 HandlerInterceptor를 구현한 인터셉터를 만들 것인데

- 리소스(js, html 등-ResourceHttpRequestHandler)는 그냥 통과시킬 것이고
- 어노테이션이 달려있는지에 따라 통과시킬지를 가른다

이때, 핸들러에 있는 메서드나 빈으로 구성된 정보를 가져오기 위해서 **HandlerMethod**를 사용하고, **Auth 어노테이션(Class)가 들어있는지** 확인한다

그리고, 그 다음은 인터셉터를 등록하기 위한 config 파일을 하나 만들어보자

- 이때, config를 위한 파일에서는 WebMvcConfigurer 를 구현한다!
- addInterceptors()메서드를 오버라이딩

```java
@Override
    public void addInterceptors(InterceptorRegistry registry) {
        InterceptorRegistry
    }
```

- 이때, 인터셉터를 빈으로 등록하되, 하나의 클래스 내 메서드에서 진행하기 위해서 Configuration 어노테이션을 사용해주자
- 위에서 만든 인증에 대한 인터셉터를 다루기 위해서 필드로

private final AuthInterceptor authInterceptor;

를 두어 값 변경 제한을 주자

그리고, 해당 authInterceptor에 대해서 RequiredArgsConstructor 어노테이션으로 생성자 주입을 해주자!

아래처럼 AutoWired로 해줘도 되지만, 순환참조가 일어날 수도 있다! 따라서 요즘에는 RequiredArgsConstructor로 많이 사용하는 추세라고 한다!

```java
@AutoWired
private final AuthInterceptor authInterceptor;
```

📌RequiredArgsConstructor

final 혹은 @NotNull이 붙은 필드의 생성자를 생성하는 생성자 주입

[[Spring] @RequiredArgsConstructor 어노테이션을 사용한 "생성자 주입"](https://velog.io/@developerjun0615/Spring-RequiredArgsConstructor-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%9C-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%A3%BC%EC%9E%85)

- 매개변수로 받은 InterceptorRegistry를 이용해서 addInterceptor(authInterceptor)를 통해 인터셉터를 등록해준다!

```java
package com.example.interceptor.config;

import com.example.interceptor.interceptor.AuthInterceptor;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration //하나의 클래스에서 여러 개의 빈 등록, 관리 or 하나의 클래스 안에서 메서드 등으로 빈 등록
@RequiredArgsConstructor
public class MvcConfig implements WebMvcConfigurer {

    private final AuthInterceptor authInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
       **registry.addInterceptor(authInterceptor);**
    }
}
```

📌 InterceptorRegistry 📌

- 매핑된 인터셉터들의 리스트를 등록하는데에 도움을 주는 역할

메서드1) public InterceptorRegistration addInterceptor(HandlerInterceptor interceptor)

- 제공된 HandlerInterceptor 추가

메서드 2) public InterceptorRegistration addWebRequestInterceptor(WebRequestInterceptor interceptor)

- 제공된 WebRequestInterceptor 추가

메서드 3) protected List<Object> getInterceptors()

- 모든 등록된 인터셉터들을 반환

[InterceptorRegistry (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/InterceptorRegistry.html)

[http://localhost:8089/api/private/hello](http://localhost:8089/api/private/hello) 로 요청을 보내보자(get 요청)

이렇게 인터셉터를 등록하면, 지금은 preHandle에서 return false처리가 되어 response body에 넘어오는 값이 없어보인다(뷰로 넘어오지 않음) 따라서 아래처럼 로그만 확인할 수 있다

```java
2021-08-03 11:29:55.768  INFO 32844 --- [nio-8089-exec-1] 
c.e.i.interceptor.AuthInterceptor        : 
request uri: /api/private/hello
```

아래처럼 private controller에 로그를 찍고 인터셉터에서 컨트롤러로 들어오는지 확인해보자

```java
package com.example.interceptor.controller;

import com.example.interceptor.annotation.Auth;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/private")
@Auth
@Slf4j
public class PrivateController {

    @GetMapping("/hello")
    public String hello(){
        log.info("private hello controller");
        return "private Hello";
    }

}
```

그러면,  위의 "private hello controller"가 로그로 찍히지 않음을 알 수 있다

이로써 위의 생명주기와 비교한다면, false로 인해서 컨트롤러로 연결되지 않고 

리턴되어버린 것을 알 수 있다

따라서 preHandle 부분에서 return true로 변경해보자

```java
package com.example.interceptor.interceptor;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.resource.ResourceHttpRequestHandler;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Slf4j
@Component
public class AuthInterceptor implements HandlerInterceptor{

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        String uri = request.getRequestURI();
        log.info("request uri: {}",uri);
        **return true;**
    }

    //어노테이션이 달려있는지 볼 것
    //clazz는 class가 예약어이기 때문에 자주 대체재로써 사용되는 이름
    //Class로 어노테이션 확인
    private boolean checkAnnotation(Object handler,
                                    Class clazz){
        //리소스(html,js 등)에 대한 요청은 무조건 통과
        if(handler instanceof ResourceHttpRequestHandler){
            return true;
        }

        //어노테이션이 달려있는지
        HandlerMethod handlerMethod=(HandlerMethod) handler;

        if(handlerMethod.getMethodAnnotation(clazz)!=null ||
           handlerMethod.getBeanType().getAnnotation(clazz)!=null){
            //어노테이션을 갖고 있는지 체크
            return true;//auth가 있을 때
        }
        return false;//모든 조건에 부합한 경우

    }
}
```

이렇게 수정해주고나서, [http://localhost:8089/api/private/hello](http://localhost:8089/api/private/hello) 로 요청을 다시 보내보면 이번에는 response body에 "private Hello"가 뜨고, response status code로 200 OK가 확인된다

그리고 콘솔에서는 아래와 같이 미리 설정해주었던 로그를 확인해볼 수 있다

```java
2021-08-03 11:40:04.944  INFO 14944 --- 
[nio-8089-exec-1] c.e.i.interceptor.AuthInterceptor        : 
**request uri: /api/private/hello**
2021-08-03 11:40:04.952  INFO 14944 --- 
[nio-8089-exec-1] c.e.i.controller.PrivateController       : 
**private hello controller**
```

즉, true일 때에 비로소 내부 로직을 탈 수 있음을 확인해볼 수 있다

그러면, 이런 생각이  들 수 있다  '이건 아직 인증이 다 안된 것 깉은데요..? 그냥 preHandle에서 무조건 통과시켜주는데요?'

그래서 플래그값처럼 preHandle 메서드 내부에 어노테이션을 확인하는 checkAnnotation메서드 결과를 넣어주자

그리고 검증 로직은 uri를 동적으로 가져와서 쿼리에 name=steve가 들어있으면 인증된 것으로 하고, 전체적으로 public으로 하되 해당 부분만 private으로 처리하도록 하자

```java
package com.example.interceptor.interceptor;

import com.example.interceptor.annotation.Auth;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.resource.ResourceHttpRequestHandler;
import org.springframework.web.util.UriComponentsBuilder;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.net.URI;

@Slf4j
@Component
public class AuthInterceptor implements HandlerInterceptor{

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        String uri = request.getRequestURI();
        boolean hasAnnotation=checkAnnotation(handler, Auth.class);

        **URI u = UriComponentsBuilder.fromUriString(uri)
        .query(request.getQueryString())
        .build()
        .toUri();**

        log.info("request uri: {}",uri);
        log.info("uri info: {}",u);
        if(hasAnnotation){
            //권한 검증
            String query=u.getQuery();
            log.info("query: {}",query);
            if(query.equals("name=steve")){

                return true;
            }
            return false;
        }
        //기본은 public
        return true;
    }

    //어노테이션이 달려있는지 볼 것
    //clazz는 class가 예약어이기 때문에 자주 대체재로써 사용되는 이름
    //Class로 어노테이션 확인
    private boolean checkAnnotation(Object handler,
                                    Class clazz){
        //리소스(html,js 등)에 대한 요청은 무조건 통과
        if(handler instanceof ResourceHttpRequestHandler){
            return true;
        }

        //어노테이션이 달려있는지
        HandlerMethod handlerMethod=(HandlerMethod) handler;

        if(handlerMethod.getMethodAnnotation(clazz)!=null ||
           handlerMethod.getBeanType().getAnnotation(clazz)!=null){
            //어노테이션을 갖고 있는지 체크
            return true;//auth가 있을 때
        }
        return false;//모든 조건에 부합한 경우

    }
}
```

[UriComponentsBuilder (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/UriComponentsBuilder.html)

📍 UriComponentsBuilder 📍

- Object 상속
- UriBuilder, Cloneable 구현

-HttpServletRequest의 getRequestURI()과 다르게, 쿼리 등 다양한 Uri의 요소들을 동적으로 생성하여 문자열로 보여주는 클래스

🌻 다양한 쓰임새 

- fromPath(String) 이나 fromUri(String)과 같은 static factory 메서드로 UriComponentsBuilder 생성
- scheme(String)이나 userInfo(String). host(String) 등의 메서드를 통해서 다양한 URI 요소들을 설정
- build()메서드를 통해서 UriComponents 인스턴스를 만들기

-public static UriComponentsBuilder fromUriString(String uri) :

주어진 URI에 대한 string을 이용해서 UriComponenetsBuilder를 생성

[UriComponentsBuilder (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/UriComponentsBuilder.html#fromUriString-java.lang.String-)

- public UriComponentsBuilder query(@Nullable String query)

:쿼리 스트링 부분까지 가져와서 UriComponentsBuilder를 생성

-이와 반대로 queryParam은 특정 파라미터의 값을 설정하는 것 같다

- public UriComponents build()

:  builder에 포함된 다양한 요소들로부터 UrIComponents 생성

[UriComponents (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/UriComponents.html#toUri--)

- UriComponents 클래스의 toUri() 메서드 public abstract URI toUri()

: toUriString()에 의해서 전체 URI가 인코딩된 상태라면, 파라미터가 1개만 요구되는 그값을 URI 생성자에 전달[아래의 경우가 해당될 듯]

```java
public URI(String str) throws URISyntaxException {
        new Parser(str).parse(false);
    }
```

: 위와 다르게 인코딩된 상태가 아니라면, 각각의 URI 요소값들을 다양한 파라미터들을 요구하는 URI 생성자에게 전달

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%EC%9D%B8%EC%A6%9D%EA%B3%BC%EC%A0%95%EC%97%90%20%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94%20%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%EC%9D%B8%EC%A6%9D%EA%B3%BC%EC%A0%95%EC%97%90%20%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94%20%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0.gif?raw=true)

그리고 요청을 [http://localhost:8089/api/private/hello?name=steve](http://localhost:8089/api/private/hello?name=steve) 로 보내면 이젠 body에 private Hello가 뜨고, 잘 작동하지만

[http://localhost:8089/api/private/hello?name=alice](http://localhost:8089/api/private/hello?name=alice) 로 요청하면 false로 되어서 body에 어떤 내용도 들어오지 않는다(내부 로직까지 도달할 사이가 없이 false가 리턴되기 때문)

[http://localhost:8089/api/private/hello](http://localhost:8089/api/private/hello) 로 요청하게 되면, query가 null이기 때문에 null pointer exception이 발생해서 서버 내부 에러 500이 뜨고 

```java
2021-08-03 14:08:53.684  INFO 28956 --- [nio-8089-exec-9] c.e.i.interceptor.AuthInterceptor        : request uri: /api/private/hello
2021-08-03 14:08:53.684  INFO 28956 --- [nio-8089-exec-9] c.e.i.interceptor.AuthInterceptor        : uri info: /api/private/hello
2021-08-03 14:08:53.684  INFO 28956 --- [nio-8089-exec-9] c.e.i.interceptor.AuthInterceptor        : query: null
2021-08-03 14:08:53.685 ERROR 28956 --- [nio-8089-exec-9] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is java.lang.NullPointerException] with root cause

java.lang.NullPointerException: null
	at com.example.interceptor.interceptor.AuthInterceptor.preHandle(AuthInterceptor.java:36) ~[main/:na]
	at org.springframework.web.servlet.HandlerExecutionChain.applyPreHandle(HandlerExecutionChain.java:148) ~[spring-webmvc-5.3.9.jar:5.3.9]
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1059) ~[spring-webmvc-5.3.9.jar:5.3.9]
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:963) ~[spring-webmvc-5.3.9.jar:5.3.9]
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006) ~[spring-webmvc-5.3.9.jar:5.3.9]
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:898) ~[spring-webmvc-5.3.9.jar:5.3.9]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:655) ~[tomcat-embed-core-9.0.50.jar:4.0.FR]
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883) ~[spring-webmvc-5.3.9.jar:5.3.9]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:764) ~[tomcat-embed-core-9.0.50.jar:4.0.FR]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:228) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:163) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53) ~[tomcat-embed-websocket-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:190) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:163) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100) ~[spring-web-5.3.9.jar:5.3.9]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119) ~[spring-web-5.3.9.jar:5.3.9]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:190) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:163) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93) ~[spring-web-5.3.9.jar:5.3.9]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119) ~[spring-web-5.3.9.jar:5.3.9]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:190) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:163) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201) ~[spring-web-5.3.9.jar:5.3.9]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119) ~[spring-web-5.3.9.jar:5.3.9]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:190) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:163) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:202) ~[tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:97) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:542) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:143) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:78) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:357) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:382) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:893) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1723) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [na:1.8.0_282]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [na:1.8.0_282]
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61) [tomcat-embed-core-9.0.50.jar:9.0.50]
	at java.lang.Thread.run(Thread.java:748) [na:1.8.0_282]

2021-08-03 14:08:53.686  INFO 28956 --- [nio-8089-exec-9] c.e.i.interceptor.AuthInterceptor        : request uri: /error
2021-08-03 14:08:53.686  INFO 28956 --- [nio-8089-exec-9] c.e.i.interceptor.AuthInterceptor        : uri info: /error
```

콘솔에는 위와 같이 로그와 에러 부분이 찍힌다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%ED%8A%B9%EC%A0%95%20url%EC%97%90%20%EB%8C%80%ED%95%B4%EC%84%9C%20%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0%20%EC%A0%81%EC%9A%A9%20%EC%98%88%EC%99%B8%EB%A5%BC%20%EB%91%90%EA%B8%B0%20excludePathPatterns.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%ED%8A%B9%EC%A0%95%20url%EC%97%90%20%EB%8C%80%ED%95%B4%EC%84%9C%20%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0%20%EC%A0%81%EC%9A%A9%20%EC%98%88%EC%99%B8%EB%A5%BC%20%EB%91%90%EA%B8%B0%20excludePathPatterns.gif?raw=true)

특정 Url을 빼고 싶은 경우에는, 인터셉터를 등록했던 MvcConfig 클래스에서 

InterceptorRegistry의 메서드인 addInterceptor의 반환형이 InterceptorRegistration이었음을 참조해서

여기서 제공되는 **public InterceptorRegistration excludePathPatterns(String... patterns) 메서드**를 통해 적용이 가능하다!

[InterceptorRegistration (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/InterceptorRegistration.html#excludePathPatterns-java.lang.String...-)

```java
package com.example.interceptor.config;

import com.example.interceptor.interceptor.AuthInterceptor;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration //하나의 클래스에서 여러 개의 빈 등록, 관리 or 하나의 클래스 안에서 메서드 등으로 빈 등록
@RequiredArgsConstructor
public class MvcConfig implements WebMvcConfigurer {

    private final AuthInterceptor authInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
       registry.addInterceptor(authInterceptor).**excludePathPatterns("/api/private/*");**
    }
}
```

위의 경우는 /api/private하위의 모든 url에 대해서 제외를 하기로 한 것이다

그러면 이제는 아래처럼 요청을 private 컨트롤러에게 보내도 인터셉터가 작동하지 않을 것이다

하지만, 역으로 addPathPatterns를 이용한다면, 지정 패턴의 url에 대해서만 인터셉터를 적용할 수 있다!

이는 즉, 어노테이션이 없어도 인증을 처리할 수 있게 된 것이다!

```java
package com.example.interceptor.config;

import com.example.interceptor.interceptor.AuthInterceptor;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration //하나의 클래스에서 여러 개의 빈 등록, 관리 or 하나의 클래스 안에서 메서드 등으로 빈 등록
@RequiredArgsConstructor
public class MvcConfig implements WebMvcConfigurer {

    private final AuthInterceptor authInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
       registry.addInterceptor(authInterceptor).**addPathPatterns("/api/private/*");**
    }
}
```

이제는 private에만 인터셉터가 작동되어서 public 에 대해서 요청([http://localhost:8089/api/public/hello](http://localhost:8089/api/public/hello))을 보내면 200OK여도 인터셉터가 작동되지 않음을 확인할 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%ED%8A%B9%EC%A0%95%20url%EC%97%90%20%EB%8C%80%ED%95%B4%EC%84%9C%20%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0%20%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0%20addPathPatterns.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%ED%8A%B9%EC%A0%95%20url%EC%97%90%20%EB%8C%80%ED%95%B4%EC%84%9C%20%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0%20%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0%20addPathPatterns.gif?raw=true)

뿐만 아니라, addPathPatterns로 지정해서 특정 컨트롤러로 집중할 수 있도록 했다면, 더이상 클래스로 구별하지 않아도 되므로 Auth 어노테이션을 메서드에 붙여주어도 된다. 하지만 일관성이 떨어지고 유지보수가 힘들다(다른 곳에서도 사용할 수 있는데 그때에는 클래스 등에도 적용할 수도 있기 때문) 따라서 특정 url로 addPathPatterns를 걸어주고, 어노테이션도 클래스 선언 시에 걸어주는 것이 좋다!

```java
package com.example.interceptor.controller;

import com.example.interceptor.annotation.Auth;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/private")
@Slf4j
public class PrivateController {
		**@Auth**
    @GetMapping("/hello")
    public String hello(){
        log.info("private hello controller");
        return "private Hello";
    }

}
```

🌟 위의 addPathPatterns나 execludePathPatterns는 단 하나의 패턴만 넣을 수 있는 것이 아니라, 

addPathPatterns("/api/private/*","/rapi/private/*")

처럼 comma로 구분하여 여러개를 넣고 지정해줄 수 있다

## 2-1. 인터셉터에서 예외만들고 관리하기

인터셉터 내부에서 세션, 쿠키 등에 대해서 관리해볼 수 있지만

이번에는 예외에 대해서 공부해보자

인증 exception이 있다고 가정하고 아래와 같이 runtime exception을 상속받아 만들자

```java
package com.example.interceptor.exception;

public class AuthException extends RuntimeException{

}
```

그리고 인증에 실패하면 return false를 시키지 말고 예외 책임 상황을 고의로 만들어주자

```java
package com.example.interceptor.interceptor;

import com.example.interceptor.annotation.Auth;
import com.example.interceptor.exception.AuthException;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.resource.ResourceHttpRequestHandler;
import org.springframework.web.util.UriComponentsBuilder;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.net.URI;

@Slf4j
@Component
public class AuthInterceptor implements HandlerInterceptor{

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        String uri = request.getRequestURI();
        boolean hasAnnotation=checkAnnotation(handler, Auth.class);

        URI u = UriComponentsBuilder.fromUriString(uri)
        .query(request.getQueryString())
        .build()
        .toUri();

        log.info("request uri: {}",uri);
        log.info("uri info: {}",u);
        if(hasAnnotation){
            //권한 검증
            String query=u.getQuery();
            log.info("query: {}",query);
            if(query.equals("name=steve")){

                return true;
            }
          //  return false;
          📌  **throw new AuthException();**📌
        }
        //기본은 public
        return true;
    }

    //어노테이션이 달려있는지 볼 것
    //clazz는 class가 예약어이기 때문에 자주 대체재로써 사용되는 이름
    //Class로 어노테이션 확인
    private boolean checkAnnotation(Object handler,
                                    Class clazz){
        //리소스(html,js 등)에 대한 요청은 무조건 통과
        if(handler instanceof ResourceHttpRequestHandler){
            return true;
        }

        //어노테이션이 달려있는지
        HandlerMethod handlerMethod=(HandlerMethod) handler;

        if(handlerMethod.getMethodAnnotation(clazz)!=null ||
           handlerMethod.getBeanType().getAnnotation(clazz)!=null){
            //어노테이션을 갖고 있는지 체크
            return true;//auth가 있을 때
        }
        return false;//모든 조건에 부합한 경우

    }
}
```

그리고 이를 지금 rest api controller에 대해서 예외 처리가 되는 것이기 때문에 RestControllerAdvice와 ExceptionHandler를 이용해주자

그리고 응답은 UNAUTHORIZED 상태와 함께 no body로 내어주기 위해서

build()를 이용하겠다(build()메서드는 응답을 body없이 내려주는 것이다)

```java
package com.example.interceptor.exceptionHandler;

import com.example.interceptor.exception.AuthException;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(value= AuthException.class)
    public ResponseEntity authException(){
        //build: body없이 리턴하기
     📌   **return ResponseEntity.status(HttpStatus.UNAUTHORIZED).build();**📌
    }
}
```

이번에 [http://localhost:8089/api/private/hello?name=alice](http://localhost:8089/api/private/hello?name=alice) 로 steve가 아닌 사람으로 요청을 하게 되면 UNAUTHORIZED 상태코드를 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0%EC%97%90%EC%84%9C%20%EC%9D%B8%EC%A6%9D%EA%B3%BC%EC%A0%95%20%EC%A4%91%20%EC%98%88%EC%99%B8%EB%A5%BC%20%EB%B0%9C%EC%83%9D%EC%8B%9C%ED%82%A4%EA%B3%A0,%20%EC%9D%B4%EC%97%90%20%EB%8C%80%ED%95%B4%EC%84%9C%20%EC%98%88%EC%99%B8%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Interceptor,%20Filter/%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0%EC%97%90%EC%84%9C%20%EC%9D%B8%EC%A6%9D%EA%B3%BC%EC%A0%95%20%EC%A4%91%20%EC%98%88%EC%99%B8%EB%A5%BC%20%EB%B0%9C%EC%83%9D%EC%8B%9C%ED%82%A4%EA%B3%A0,%20%EC%9D%B4%EC%97%90%20%EB%8C%80%ED%95%B4%EC%84%9C%20%EC%98%88%EC%99%B8%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0.gif?raw=true)

🌟 이와 같이 인터셉터는 필터와 다르게 어노테이션 및 클래스를 이용해서 컨텍스트 정보를 활용할 수 있다!(그렇기 때문에 HandlerMethod로 부터 정보도 획득 가능했다)

🌟 인터셉터는 등록 순서가 있다!! ▶️ 따라서 인증의 과정에 대해서 depth를 두고 적용이 가능하다!

그래서 위의 config에서 추가로

```java
package com.example.interceptor.config;

import com.example.interceptor.interceptor.AuthInterceptor;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration //하나의 클래스에서 여러 개의 빈 등록, 관리 or 하나의 클래스 안에서 메서드 등으로 빈 등록
@RequiredArgsConstructor
public class MvcConfig implements WebMvcConfigurer {

    private final AuthInterceptor authInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
       **registry.addInterceptor(authInterceptor).addPathPatterns("/api/private/*"); //a
       registry.addInterceptor(snsInterceptor).addPathPatterns("/api/private/*"); //b**
    }
}
```

위와 같이 인터셉터를 등록했다면

a가 우선순위가 높아서 a가 먼저 동작 후에 b가 동작하게 된다!