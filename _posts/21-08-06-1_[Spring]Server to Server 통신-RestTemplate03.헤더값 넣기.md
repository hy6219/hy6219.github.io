# Server to Server 통신-RestTemplate03.헤더값 넣기

[TIL/[Spring]Server to Server 통신-RestTemplate02.POST요청.md at main · hy6219/TIL](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/%5BSpring%5DServer%20to%20Server%20%ED%86%B5%EC%8B%A0-RestTemplate02.POST%EC%9A%94%EC%B2%AD.md)

이번에는 이전에는 헤더의 contents부분이 비어져 있는 경우가 있었는데, 바로 앞에서 POST방식에서 주로 볼 수 있었다

이번에는 헤더의 값을 채워넣어주자

예제는 앞에서 이용한 예제를 확장해서 진행하도록 하자

---

## RequestEntity

- HttpEntity의 확장된 개념
- HTTP 메서드와 대상 url을 노출
- RestTemplate에서 @Controller에 있는 메서드의 요청 입력에 대해서 준비하기 위해 필요한 객체
- GET, POST, PUT 요청과 관련된 메서드 등이 존재

🌹public static RequestEntity.BodyBuilder post(URI url)

주어진 url을 이용해서 HTTP POST 요청을 만들어냄

[RequestEntity (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/RequestEntity.html#post-java.net.URI-)

🌹 RequestEntity.BodyBuilder contentType(MediaType contentType)

body의 media type을 지정(content-type헤더에)

🌹 <T> RequestEntity<T> body(T body)

- body 내용을 설정

🌹 B header(String headerName, String... headerValues)

- 주어진 단일 헤더값(headerValues)를 주어진 헤더이름(headerName)에 넣기

---

**(클라이언트)**

먼저,  지난 POST 방식에서 설정한 uri는 그대로 갖는 것으로 하고, 

📌헤더값을 변경하기 위해서는 **RestTemplate의 exchange(RequestEntity, 응답형태와 관련된 클래스)**를 이용해주어야 한다

그런데 보통 json 데이터를 처리할 경우에는 요청과 응답에도 POST를 사용하는 것이 보편적이고, 편리하므로 POST 방식을 이용해서 요청을 하기 위해서 

- RequestEntity를 생성할 때, post메서드를 이용하자(post(uri))

그리고 해더의 경우 

- Content-type 헤더는 미디어타입을 json으로 하고
- 커스텀헤더는 x-authorization과 custom-header라는 이름으로 해서 실어보내자

그리고 body에는 userRequest라는 user정보 관련 객체를 담아주자

```java
package com.example.client.service;

import com.example.client.dto.UserRequest;
import com.example.client.dto.UserResponse;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.net.URI;

@Slf4j
@Service
public class RestTemplateService {

    public UserResponse exchangeHeader(UserRequest userRequest,long userId, String name){
        //주소 동일하게 사용
        URI uri=UriComponentsBuilder.fromUriString("http://localhost:9090")
                .path("/api/server/userId/{userId}/name/{name}")
                .encode()
                .build()
                .expand(userId,name)
                .toUri();
        log.info("request: {}",uri);

        RestTemplate restTemplate=new RestTemplate();

        **RequestEntity<UserRequest> req=RequestEntity.post(uri)
                                                    .contentType(MediaType.APPLICATION_JSON)
                                                    .header("x-authorization","abcd")
                                                    .header("custom-header","aaa")
                                                    .body(userRequest);**

        ResponseEntity<UserResponse> res= restTemplate.exchange(req,UserResponse.class);
        HttpHeaders headers= res.getHeaders();

        log.info("status code: {}",res.getStatusCode());
        log.info("status code value:{}",res.getStatusCodeValue());
        log.info("header info:content-type-{}\n " +
                        "content-length-{}\n" +
                        "Date-{}\n" +
                        "Keep-Alive-{}\n" +
                        "Connection-{}",headers.get("Content-type"),
                headers.get("Content-Length"),
                headers.get("Date"),
                headers.get("Keep-Alive"),
                headers.get("Connection"));
        log.info("body:{}",res.getBody());

        return res.getBody();
    }

}
```

(서버)

서버측에서 요청측에서 보낸 헤더값을 확인해보기 위해서 "@RequestHeader(name="헤더명")"을 이용해서 확인해보자

```java
package com.example.server.controller;

import com.example.server.dto.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;

@Slf4j
@RestController
@RequestMapping("/api/server")
public class ServerRestApiController {

//    @GetMapping("/hello")
//    public User hello(@RequestParam String name, @RequestParam int age){
//        User user =new User();
//        user.setName(name);
//        user.setAge(age);
//        log.info("request user : {}",user);
//        return user;
//    }
    @PostMapping("/userId/{userId}/name/{name}")
    public User hello(@RequestBody User user,
                      @PathVariable long userId,
                      @PathVariable String name,
                      **@RequestHeader(name="x-authorization") String xAuth,
                      @RequestHeader(name="custom-header") String custom**){
        log.info("request user : {}",user);
        log.info("x-authorization header: {}",xAuth);
        log.info("custom header: {}",custom);
        return user;
    }

}
```

그리고 요청은 POST 방식으로, 아래와 같은 json 요청을 보내면,  서버측에서 클라이언트 서버에서 보낸 헤더값을 확인해볼 수 있게 된다

```java
{
  "name":"steve",
  "age":20
}
```

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20%ED%97%A4%EB%8D%94%EA%B0%92%20%EB%B3%80%EA%B2%BD-RequestEntity.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20%ED%97%A4%EB%8D%94%EA%B0%92%20%EB%B3%80%EA%B2%BD-RequestEntity.gif?raw=true)

---

## 🌟🌟🌟body 내부에 header와 body가 들어가고, 그 내용이 변경되는 경우🌟🌟🌟 -재사용성에 탁월!

지금 공부에 참고하고 있는 강의에서, 핀테크 산업 등의 경우에 아래처럼

body 내부에 header와 body가 들어가고, 그 내부 값이 변경되는 경우가 있다고 한다.

```java
{
  "header": {
    "response_code": "200"
  },
  **"body"**: {
    "name": "steve",
    "age": 10
  }
}
```

**(클라이언트 & 서버)**

이 경우, **body 내부의 body 부분(위에서 굵게 표시된 부분)은 내부 값 및 형태가 변경될 수 있으므로 제네릭스를 이용해서 필드로 만들어주자**

```java
package com.example.client.dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Req<T> {

    private Header header;
    **private T body;**

		@Data
    @NoArgsConstructor
    @AllArgsConstructor
    public static class Header{
        private String responseCode;
    }

}
```

```java
package com.example.server.dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Req<T> {

    private Header header;
    **private T body;**

    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public static class Header{
        private String responseCode;
    }
}
```

**(클라이언트)**

먼저, 요청을 보낼때에는 response code는 없기 때문에  아래처럼 header의 response_body 부분은 비워진 채로 보내져야 한다

```java
{
  "header": {
    **"response_code": ""**
  },
  "body": {
    "name": "steve",
    "age": 10
  }
}
```

🌟 클라이언트와 서버측에서 요청과 응답에 관련된 dto의 필드명은 맞춰주어야 한다! 그렇지 않으면 값이 null로 들어오거나 에러가 발생할 수 있다!

그런데, 제네릭스는 class를 사용할 수 없기 때문에 restTemplate에서 제공되는 parameterizedTypeReference를 이용해서

ResponseEntity<Req<UserResponse>> response=restTemplate.exchange(requestEntity,**new ParameterizedTypeReference<Req<UserResponse>>(){}**);

위와 같이 사용할 수 있다

그리고 body에 들어가는 클래스가 바뀔때마다 전체를 건드리지 않고 내부 클래스만 바꿔주기 위해서 리턴타입을 UserResponse에서 Req<UserResponse>로 변경해주자

```java
public **Req<UserResponse>** genericExchange(UserRequest userRequest,long userId, String name){
        URI uri=UriComponentsBuilder.fromUriString("http://localhost:9090")
                .path("/api/server/userId/{userId}/name/{name}")
                .encode()
                .build()
                .expand(userId,name)
                .toUri();
        log.info("request: {}",uri);

        Req<UserRequest> req= new Req();

        req.setHeader(
                new Req.Header()
        );

        req.setBody(
            userRequest
        );

        RequestEntity<Req<UserRequest>> requestEntity=RequestEntity.post(uri)
                .contentType(MediaType.APPLICATION_JSON)
                .header("x-authorization","abcd")
                .header("custom-header","aaa")
                .body(req);

        RestTemplate restTemplate= new RestTemplate();

        //제네릭스는 class를 붙일 수 없음
        //ParameterizedType을 이용하여 제네릭스를 하나의 타입으로 만들어주기
        ResponseEntity<Req<UserResponse>> response=restTemplate.exchange(requestEntity,new ParameterizedTypeReference<Req<UserResponse>>(){});

        return **response.getBody()**;
    }
```

그러면 컨트롤러부분에서도 이 메서드를 호출시켜주고, 리턴타입을 맞춰주어야 한다

```java
package com.example.client.controller;

import com.example.client.dto.Req;
import com.example.client.dto.UserRequest;
import com.example.client.dto.UserResponse;
import com.example.client.service.RestTemplateService;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/client")
@RequiredArgsConstructor
public class RestApiController {

    private final RestTemplateService restTemplateService;

    @PostMapping("/userId/{userId}/name/{name}")
    public Req<UserResponse> exchangeHeader(@RequestBody UserRequest user, @PathVariable long userId, @PathVariable  String name){
        return restTemplateService.genericExchange(user,userId,name);
    }
}
```

(서버)

서버측에서는 이제

```java
{
  "header": {
    "response_code": "200"
  },
  "body": {
    "name": "steve",
    "age": 10
  }
}
```

위와 같이 상태코드도 같이 내려주어야 하는데, 이를 간단하게

요청으로 전달된 객체값이 null이면 400, 아니면 200을 내려주도록 해보자

```java
package com.example.server.controller;

import com.example.server.dto.Req;
import com.example.server.dto.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@Slf4j
@RestController
@RequestMapping("/api/server")
public class ServerRestApiController {

    @PostMapping("/userId/{userId}/name/{name}")
    public **Req<User>** hello(
                 @RequestBody Req<User> user,
                  @PathVariable long userId,
                  @PathVariable String name,
                  @RequestHeader(name="x-authorization") String xAuth,
                  @RequestHeader(name="custom-header") String custom){
            log.info("request user : {}",user);
            log.info("x-authorization header: {}",xAuth);
            log.info("custom header: {}",custom);

            Req<User> response=
                    new Req<>();

            **Req.Header header =new Req.Header();
            User body=user.getBody();

            if(body==null){
                header.setResponseCode("400");
            }else{
                header.setResponseCode("200");
            }

            response.setHeader(
                   header
            );

            response.setBody(
                    body
            );**

            return response;
}

}
```

사실 int가 null에 대해서 명확히 처리하기 위해서는 래퍼 클래스인 Integer를 사용함이 보다 맞겠지만 이해를 위해서 빠르게 진행해보자

요청을 [http://localhost:8089/api/client/userId/100/name/steve](http://localhost:8089/api/client/userId/100/name/steve) 로 POST 요청을 아래와 같이 보내면,

```java
{
  "name":"steve",
  "age":20
}
```

이번에는 위에서 설계한 것처럼 body 내부에 header와 body가 들어있는 형태로 확인해볼 수 있게 될 것이다!

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20%EC%9E%AC%EC%82%AC%EC%9A%A9%EC%84%B1%20%EC%A6%9D%EB%8C%80%EB%A5%BC%20%EC%9C%84%ED%95%9C%20json%20%EB%94%94%EC%9E%90%EC%9D%B8.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20%EC%9E%AC%EC%82%AC%EC%9A%A9%EC%84%B1%20%EC%A6%9D%EB%8C%80%EB%A5%BC%20%EC%9C%84%ED%95%9C%20json%20%EB%94%94%EC%9E%90%EC%9D%B8.gif?raw=true)