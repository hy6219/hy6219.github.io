# Server to Server 통신-RestTemplate01.GET요청

- 방법 1: RestTemplate
- 방법 2: WebClient
- 방법 3: Apache Client
- Etc...

서버 간 통신을 하는 것이기 때문에, 클라이언트와 서버로 구성될 필요가 있다!

먼저 클라이언트부터 만들어보자

- application.properties에서 클라이언트의 포트는 8089로 설정해주자

📍 RestTemplate 📍

[RestTemplate (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html)

- HTTP 메서드에 의한 공통적인 시나리오를 제공하는 템플릿
- 동기 클라이언트를 위한 http 요청에 대해 지원
- getForEntity : URL에서 GET을 통해서 URI 위치로 요청을 했을 때 응답을 연결 시도하는데, 응답은 ResponseEntity로 내려줌

```java
public <T> ResponseEntity<T> getForEntity(URI url,
                                          Class<T> responseType)
                                   throws RestClientException
```

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#getForEntity-java.net.URI-java.lang.Class-](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#getForEntity-java.net.URI-java.lang.Class-)

- getForObject: URL에서 GET을 통해서 URI 위치로 요청을 했을 때 응답을 연결시도하는데, 우리가 지정한 클래스로 응답을 내려줄 수 있음

-해당 메서드가 실행되자마자 클라이언트의 요청은 http연결을 통해서 서버에 연결됨

```java
@Nullable
public <T> T getForObject(URI url,
                                    Class<T> responseType)
                             throws RestClientException
```

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#getForObject-java.net.URI-java.lang.Class-](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#getForObject-java.net.URI-java.lang.Class-)

### 01. GET 요청- getForObject

먼저 서비스 부분을 다룰 것인데, uri를 "http://localhost/api/server/hello"로 만들고, RestTemplate을 통해 uri 로 요청을 했을 때 응답을 String으로 받도록 하자

```java
package com.example.client.service;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.net.URI;

@Slf4j
@Service
public class RestTemplateService {

    //http://localhost:9090/api/server/hello에 요청을 해서
    //응답을 받아올 것
    public String hello(){
        **//uri 만들기
        URI uri=
                UriComponentsBuilder
                        .fromUriString("http://localhost:9090")
                        .path("/api/server/hello")
                        .build()
                        .toUri();**

        log.info("uri: {}",uri);

        //RestTemplate만들기
        **RestTemplate restTemplate= new RestTemplate();**
        //restTemplate은 get,put,post,delete 등
        //요청 메서드를 모두 지원하는데
        //getForEntity은 ResponseEntity형태로 내려주어야 하지만
        //getForObject는 우리가 지정한 타입으로 응답을 내려줄 수 있다
        //getForObject가 실행되는 순간이 클라이언트에서 http로 서버에 접근하게 됨
        **String res = restTemplate.getForObject(uri,String.class);**
        log.info("RestTemplate uri: {}",res);
        //
        return res;
    }
}
```

그리고 GET 요청을 보내는 RestApiController에서는 이 서비스단의 hello 메서드를 내부적으로 호출하도록 하자

```java
package com.example.client.controller;

import com.example.client.service.RestTemplateService;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/client")
**@RequiredArgsConstructor**
public class RestApiController {

    **private final RestTemplateService restTemplateService;**

    @GetMapping("")
    public String getHello(){
        **return restTemplateService.hello();**
    }

}
```

---

이번에는 서버측을 만들어보자

서버의 포트는 9090으로 하고, 컨트롤러에는 클라이언트에서 서버의 uri로 요청이 들어왔을 때 plain text를 응답으로 내려주기로 하자

```java
package com.example.server.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/server")
public class ServerRestApiController {

    @GetMapping("/hello")
    public String hello(){
        return "hello server";
    }
}
```

이제 talend나 브라우저에서 get 요청으로 [http://localhost:8089/api/client](http://localhost:8089/api/client) 을 통해 클라이언트 서버측에 요청을 하면, 클라이언트 서버 측에서는 내부에서 서버측에 get 요청을 보내어 String 형태로 응답을 받아와서 아래처럼 "hello server"를 보여주게 된다!

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate%EC%9C%BC%EB%A1%9C%20%EC%84%9C%EB%B2%84%EA%B0%84%20%ED%86%B5%EC%8B%A0%ED%95%98%EA%B8%B0.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate%EC%9C%BC%EB%A1%9C%20%EC%84%9C%EB%B2%84%EA%B0%84%20%ED%86%B5%EC%8B%A0%ED%95%98%EA%B8%B0.gif?raw=true)

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate%EC%9C%BC%EB%A1%9C%20%EC%84%9C%EB%B2%84%EA%B0%84%20%ED%86%B5%EC%8B%A0%ED%95%98%EA%B8%B0-URI,%20UrIComponentsBuilder%EC%9D%B4%EC%9A%A9.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate%EC%9C%BC%EB%A1%9C%20%EC%84%9C%EB%B2%84%EA%B0%84%20%ED%86%B5%EC%8B%A0%ED%95%98%EA%B8%B0-URI,%20UrIComponentsBuilder%EC%9D%B4%EC%9A%A9.PNG?raw=true)

### 02. GET 요청- getForEntity

지금까지 공부하고 있는 방향은 무엇인지 다시 정리하면,

지금까지 공부하고 있는 것은 바로 "클라이언트 서버에서 서버를 호출"하는 것이다! 그렇기 때문에 GET방식에서 서버 주소 [http://localhost:9090/api/server/hello](http://localhost:9090/api/server/hello) 를 바로 요청해도 동일할 것이다

이번에는 getForEntity로 응답을 내려보자

getForEntity는 ResponseEntity로 응답을 내려주기 때문에 클라이언트측 서비스와 컨트롤러 부분을 아래처럼 변경해줄 필요가 있다

더불어서, 정확한 body 내용을 위해서는 res.getBody()를 해줄 필요가 있다(getBody는 아래에서 확인 가능)

```java
package com.example.client.service;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.net.URI;

@Slf4j
@Service
public class RestTemplateService {

    //http://localhost:9090/api/server/hello에 요청을 해서
    //응답을 받아올 것
    public String hello(){
        //uri 만들기
        URI uri=
                UriComponentsBuilder
                        .fromUriString("http://localhost:9090")
                        .path("/api/server/hello")
                        .build()
                        .toUri();

        log.info("uri: {}",uri);

        //RestTemplate만들기
        RestTemplate restTemplate= new RestTemplate();
        //restTemplate은 get,put,post,delete 등
        //요청 메서드를 모두 지원하는데
        //getForEntity은 ResponseEntity형태로 내려주어야 하지만
        //getForObject는 우리가 지정한 타입으로 응답을 내려줄 수 있다
        //getForObject가 실행되는 순간이 클라이언트에서 http로 서버에 접근하게 됨
     //   String res = restTemplate.getForObject(uri,String.class);
        **ResponseEntity<String> res= restTemplate.getForEntity(uri,String.class);**
        log.info("RestTemplate uri: {}",res);
        //
        return res.getBody();
    }
}
```

ResponseEntity<String>으로 변경해준 이유는 String으로 내려줄 것인데 이를 ResponseEntity 타입으로 응답해줘야 하기 때문이다!

이번에도 클라이언트에서 [http://localhost:8089/api/client](http://localhost:8089/api/client) 로 요청을 받으면 내부에서 RestTemplate에 의해 서버측에 [http://localhost:9090/api/server/hello](http://localhost:9090/api/server/hello) 로 요청을 보내게 되어 아래와 같이 확인을 해볼 수 있다

- 💡아래의 클라이언트측은 서비스에서 return 값을 getBody적용 전에 응답을 확인겸 해서 ResponseEntity<String>으로 확인해보고자 임시방편으로 넣어준 것뿐이니, 감안해서 콘솔의 결과와 브라우저 결과를 확인해보자

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20getForEntity.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20getForEntity.gif?raw=true)

그러면 단순 클래스로 받아왔을 때와 다르게

- 상태코드
- Content-type 헤더 정보
- body부분의 컨텐트

등을 확인해볼 수 있음을 알 수 있었다 

조금 더 알아보기 위해서 getForEntity 부분에 브레이크 포인트를 걸고 디버깅을 해보도록 하자

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate.getForEntity%20%EB%94%94%EB%B2%84%EA%B9%85.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate.getForEntity%20%EB%94%94%EB%B2%84%EA%B9%85.PNG?raw=true)

그러면 크게 

- value
- headers
- body

로 구성되어 있고

headers부분에는 content-type, content-length 등이 들어가 있음을 확인해볼 수 있었다

그리고 인텔리제이에서 찾아보니, getHeaders는 HttpEntity클래스에서 제공되고, 결과는 HttpHeaders형태로 반환되는데, 

HttpHeaders는 MultiValueMap이라는 일종의 Map 자료구조 형태로 구성되어 있었다

그리고 아래와 같이 Map의 "V get(Object key)"를 오버라이딩하여 List<String>형태로 키에 대한 값을 내려주는 것을 보여주고 있다

```java
@Override
	@Nullable
	public List<String> get(Object key) {
		return this.headers.get(key);
	}
```

그러면 위에서 디버깅한 부분을 참조로 해서, 각각의 value들을 가져와서 로그로 남겨보자

```java
package com.example.client.service;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpHeaders;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.net.URI;

@Slf4j
@Service
public class RestTemplateService {

    //http://localhost:9090/api/server/hello에 요청을 해서
    //응답을 받아올 것
    public String hello(){
        //uri 만들기
        URI uri=
                UriComponentsBuilder
                        .fromUriString("http://localhost:9090")
                        .path("/api/server/hello")
                        .build()
                        .toUri();

        log.info("uri: {}",uri);

        //RestTemplate만들기
        RestTemplate restTemplate= new RestTemplate();
        //restTemplate은 get,put,post,delete 등
        //요청 메서드를 모두 지원하는데
        //getForEntity은 ResponseEntity형태로 내려주어야 하지만
        //getForObject는 우리가 지정한 타입으로 응답을 내려줄 수 있다
        //getForObject가 실행되는 순간이 클라이언트에서 http로 서버에 접근하게 됨
     //   String res = restTemplate.getForObject(uri,String.class);
        ResponseEntity<String> res= restTemplate.getForEntity(uri,String.class);
        **HttpHeaders headers= res.getHeaders();**
        log.info("RestTemplate uri: {}",res);
        log.info("RestTemplate getForEntity:status code-{}\n header:{}\nbody:{}",res.getStatusCode(),headers,res.getBody());
        **log.info("header info:content-type-{}\n " +
                        "content-length-{}\n" +
                        "Date-{}\n" +
                        "Keep-Alive-{}\n" +
                        "Connection-{}",headers.get("Content-type"),
                headers.get("Content-Length"),
                headers.get("Date"),
                headers.get("Keep-Alive"),
                headers.get("Connection"));**
        //
        return res.getBody();
    }
}
```

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate%EC%97%90%EC%84%9C%20header%20%EC%A0%95%EB%B3%B4%20%EC%B6%94%EC%B6%9C%ED%95%98%EA%B8%B0.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate%EC%97%90%EC%84%9C%20header%20%EC%A0%95%EB%B3%B4%20%EC%B6%94%EC%B6%9C%ED%95%98%EA%B8%B0.PNG?raw=true)

그러면 위와 같이 좀전에는 header에 대해서 , body에 대해서 구별하기 어려워서 어떤 정보가 헤더에 대한 정보인지 확인하기 어려웠지만

지금은 어떤 정보가 들어 있는지 확인해볼 수 있게 되었다

그리고 그 윗 부분에서 getHeaders로 헤더 정보를 가져올 수 있게 되었고, getBody로 response body를, getStatusCode로 상태 코드 정보를 가져올 수 있게 되었다

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/ResponseEntity-getHeader,%20getBody,%20getStatusCode.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/ResponseEntity-getHeader,%20getBody,%20getStatusCode.PNG?raw=true)

### 03. json 형태로의 응답 받기(서버 간 통신 결과로)

-다루어질 json 데이터 샘플 형태(Client 프로젝트 폴더-sample.json)

```java
{
  "name": "steve",
  "age": 10
}
```

위에 대응하는 dto를 만들자(UserResponse.java)

```java
package com.example.client.dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserResponse {
    private String name;
    private int age;
}
```

그런데 서비스단에서는 이에 따라서 클래스를 UserResponse에 맞춰주어야 하는데,

- getForObject 를 사용할 경우

UserResponse res=restTemplate.getForObject(uri,UserResponse.class);

위와 같이 바로 클래스를 적용시켜주면 된다

- getForEntity를 사용할 경우

ResponseEntity<UserResponse> res=restTemplate.getForEntity(uri,UserResponse.class);

위와 같이 ResponseEntity에 담아주고, class인자로 UserResponse.class를 담아주면 된다!

다만, ResponseEntity는 상태코드 등 다양한 헤더 정보를 확인해볼 수 있기 때문에 지금은 getForEntity를 사용해보도록 하자

그리고, 그렇게 된다면, return이 body이므로 메서드 반환형태도 UserResponse로 바꿔주어야 할 것이다

➕query parameter를 전달받아서 서버에 넘겨주기 위해서 서비스와 컨트롤러에서 이를 활용하기로 하자!(보다 현실적으로!)

```java
package com.example.client.service;

import com.example.client.dto.UserResponse;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpHeaders;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.net.URI;

@Slf4j
@Service
public class RestTemplateService {

    //http://localhost:9090/api/server/hello에 요청을 해서
    //응답을 받아올 것
    public **UserResponse** hello(**String name, int age**){
        //uri 만들기
        URI uri=
                UriComponentsBuilder
                        .fromUriString("http://localhost:9090")
                        .path("/api/server/hello")
                        **.query("name="+name+"&age="+age)**
                        .build()
                        .toUri();

        log.info("uri: {}",uri);

        //RestTemplate만들기
        RestTemplate restTemplate= new RestTemplate();
        //restTemplate은 get,put,post,delete 등
        //요청 메서드를 모두 지원하는데
        //getForEntity은 ResponseEntity형태로 내려주어야 하지만
        //getForObject는 우리가 지정한 타입으로 응답을 내려줄 수 있다
        //getForObject가 실행되는 순간이 클라이언트에서 http로 서버에 접근하게 됨
     //01.   String res = restTemplate.getForObject(uri,String.class);
    // 02.   ResponseEntity<String> res= restTemplate.getForEntity(uri,String.class);
        //03.getForEntity-json
        **ResponseEntity<UserResponse> res=restTemplate.getForEntity(uri,UserResponse.class);**
        //03. getForObject-json
        //UserResponse res=restTemplate.getForObject(uri,UserResponse.class);
        HttpHeaders headers= res.getHeaders();
        log.info("RestTemplate uri: {}",res);
        log.info("RestTemplate getForEntity:status code-{}\n header:{}\nbody:{}",res.getStatusCode(),headers,res.getBody());
        log.info("header info:content-type-{}\n " +
                        "content-length-{}\n" +
                        "Date-{}\n" +
                        "Keep-Alive-{}\n" +
                        "Connection-{}",headers.get("Content-type"),
                headers.get("Content-Length"),
                headers.get("Date"),
                headers.get("Keep-Alive"),
                headers.get("Connection"));
        //
        return res.getBody();
    }
}
```

```java
package com.example.client.controller;

import com.example.client.dto.UserResponse;
import com.example.client.service.RestTemplateService;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/client")
@RequiredArgsConstructor
public class RestApiController {

    private final RestTemplateService restTemplateService;

    @GetMapping("")
    **public UserResponse getHello(@RequestParam String name, @RequestParam int age){
        return restTemplateService.hello(name,age);**
    }

}
```

그러면 이제는 서버도 이에 맞춰서 제공해주어야 하기 때문에 dto객체를 동일하게 만들어 주되, 의도에 맞게 User라고만 명명해주자

```java
package com.example.server.dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private int age;
}
```

그리고 그에 맞게, 컨트롤러 부분도 수정해줄 필요가 있다!

단, 클라이언트로부터 전달되는 쿼리 파라미터를 적용해서, User객체를 만들어서 에코시킬 필요가 있으니, RequestParam을 이용하자

```java
package com.example.server.controller;

import com.example.server.dto.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/api/server")
public class ServerRestApiController {

    @GetMapping("/hello")
    **public User hello(@RequestParam String name, @RequestParam int age)**{
        User user =new User();
        user.setName(name);
        user.setAge(age);
        log.info("request user : {}",user);
        return user;
    }
}
```

➕ UriComponentsBuilder에서 path는 pathVariable까지 포괄하하는 uri를 넣을 수 있다(더불어서 하이픈 - 으로 연결될 수도 있다)

```java
URI uri= UriComponenetsBuilder.fromUriString(http://서버ip:포트번호)
.path("/first-").path("value").path("/{pathVariable}")
.build(pathVariable값)
.toUri();
```

[UriComponentsBuilder (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/UriComponentsBuilder.html#path-java.lang.String-)

그리고 공식문서의 한 가지 예시를 더 확인해보면,

아래처럼 pathSegment로 두 path 분절 사이를 path()메서드 내부 구분자로 구분시켜줄 수도 있다

```java
builder.pathSegment("first-value", "second-value").path("/")

 // Results is "/first-value/second-value/"
```

그리고 위에서처럼 path().query()로 uri를 완성시켜줄 수도 있지만

```java
queryParam(name속성이름,name속성의 값)
```

으로 쿼리를 만들어 줄 수도 있다

더불어서, 물론 스프링은 기본적으로 UTF-8을 지원해주지만, 만약의 상황에 대비해서 인코딩을 지정해줄 수 있는 기능이 바로 encode()메서드이다

[UriComponentsBuilder (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/UriComponentsBuilder.html#encode--)

```java
public final UriComponentsBuilder encode() {
		return encode(StandardCharsets.UTF_8);
	}

	/**
	 * A variant of {@link #encode()} with a charset other than "UTF-8".
	 * @param charset the charset to use for encoding
	 * @since 5.0.8
	 */
	public UriComponentsBuilder encode(Charset charset) {
		this.encodeTemplate = true;
		this.charset = charset;
		return this;
	}
```

```java
package com.example.client.service;

import com.example.client.dto.UserResponse;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpHeaders;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.net.URI;

@Slf4j
@Service
public class RestTemplateService {

    //http://localhost:9090/api/server/hello에 요청을 해서
    //응답을 받아올 것
    public UserResponse hello(String name, int age){
        //uri 만들기
        URI uri=
                UriComponentsBuilder
                        .fromUriString("http://localhost:9090")
                        .path("/api/server/hello")
                        .query("name="+name+"&age="+age)
                        **.encode()**
                        .build()
                        .toUri();

        log.info("uri: {}",uri);

        //RestTemplate만들기
        RestTemplate restTemplate= new RestTemplate();
        //restTemplate은 get,put,post,delete 등
        //요청 메서드를 모두 지원하는데
        //getForEntity은 ResponseEntity형태로 내려주어야 하지만
        //getForObject는 우리가 지정한 타입으로 응답을 내려줄 수 있다
        //getForObject가 실행되는 순간이 클라이언트에서 http로 서버에 접근하게 됨
     //01.   String res = restTemplate.getForObject(uri,String.class);
    // 02.   ResponseEntity<String> res= restTemplate.getForEntity(uri,String.class);
        //03.getForEntity-json
        ResponseEntity<UserResponse> res=restTemplate.getForEntity(uri,UserResponse.class);
        //03. getForObject-json
        //UserResponse res=restTemplate.getForObject(uri,UserResponse.class);
        HttpHeaders headers= res.getHeaders();
        log.info("RestTemplate uri: {}",res);
        log.info("RestTemplate getForEntity:status code-{}\n header:{}\nbody:{}",res.getStatusCode(),headers,res.getBody());
        log.info("header info:content-type-{}\n " +
                        "content-length-{}\n" +
                        "Date-{}\n" +
                        "Keep-Alive-{}\n" +
                        "Connection-{}",headers.get("Content-type"),
                headers.get("Content-Length"),
                headers.get("Date"),
                headers.get("Keep-Alive"),
                headers.get("Connection"));
        //
        return res.getBody();
    }
}
```

이제 [http://localhost:8089/api/client?name=steve&age=20](http://localhost:8089/api/client?name=steve&age=20) 로 클라이언트에 요청을 보내보면

내부에서 서비스단에서의 hello 메서드를 호출하고, 그 내부에서 서버 uri의 특정 요청 메서드 uri에 매칭되어 json형태로 응답을 확인해볼 수 있게 된다!

(물론, ResponseEntity를 이용했기 때문에 헤더정보 또한 확인해볼 수 있다!)

더불어서 지금은 query() 메서드를 사용했기 때문에 쿼리 스트링까지 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20getForEntity-json%ED%98%95%ED%83%9C%EB%A1%9C%20%EC%9D%91%EB%8B%B5%EC%9D%84%20%EB%82%B4%EB%A0%A4%EC%A3%BC%EA%B8%B0.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20getForEntity-json%ED%98%95%ED%83%9C%EB%A1%9C%20%EC%9D%91%EB%8B%B5%EC%9D%84%20%EB%82%B4%EB%A0%A4%EC%A3%BC%EA%B8%B0.gif?raw=true)