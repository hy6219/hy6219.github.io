# Server to Server 통신-RestTemplate02.POST요청

예제는 아래에서 이용했던 것을 기반으로 진행하도록 한다

📍expand는 매개변수로 가변인자가 Object로 들어가 있는데, 

```xml
.expand()
.expand()
```

와 같이 잘못된 형태로 pathVariable을 매치시켜주게 되면 java.lang.IllegalArgumentException: Not enough variable values available to expand 와 같은 에러가 발생하는데, 

 

```xml
.expand(값1,값2)
```

와 같은 형태로 인자값을 넣어주어야 한다!

그리고 찾아보니 동일 에러 상황이 나타날 수 있는 또다른 상황은 GET 요청시 json 데이터를 보내려고 할 때에도 발생할 수 있는 것 같다

[Spring RestTemplate Exception: "Not enough variables available to expand" | Baeldung](https://www.baeldung.com/spring-not-enough-variables-available)

먼저, 다른것은 get과 유사하지만, post의 경우, body가 존재하기 때문에 body로부터 들어오는 값을 서버측에 전달해주어야 한다는 점이 다르다!

그렇기 때문에 요청값을 담은 UserRequest 객체를 만들자

```java
package com.example.client.dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserRequest {
    private String name;
    private int age;
}
```

이번에는 pathVariable을 복습겸 같이 활용해보자

클라이언트에서는 서버측으로 [http://localhost:9090/api/server/userId/{userId}/name/{name}](http://localhost:9090/api/server/userId/{userId}/name/{name}) 으로 요청을 보낼 것이다

그러면 expand를 이용해서 pathVariable을 매치시켜줄 수 있다

```java
package com.example.client.service;

import com.example.client.dto.UserRequest;
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
                        .encode()
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

    //post방식
    public UserResponse post(UserRequest userRequest,long userId, String name){
        //예제일 뿐, 실제로 주소를 이렇게 사용하지는 않을것
        //http://localhost:9090/api/server/userId/{userId}/name/{name}로 통신
        URI uri=UriComponentsBuilder.fromUriString("http://localhost:9090")
                .**path("/api/server/userId/{userId}/name/{name}")**
                .encode()
                .build()
                **.expand(userId,name)**
                .toUri();
        //.build뒤에 .expand(값)을 순차적으로 매칭시켜서
        //path variable을 넣어주어도 됨
        log.info("request: {}",uri);

        //post는 http body가 있어야 하는데
        //클라이언트는 object로 보내면 object mapper가
        //json으로 바꿔서, RestTemplate에 넣어주고
        //그러면 RestTemplate은 이를 response를 json으로 내려주게 됨

        RestTemplate restTemplate=new RestTemplate();
        **ResponseEntity<UserResponse> res= restTemplate.postForEntity(uri,userRequest,UserResponse.class);**
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

그러면, 이에 맞게 컨트롤러도 RequestBody와 PathVariable을 이용할 수 있도록 바꿔주자

```java
package com.example.client.controller;

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

//    @GetMapping("")
//    public UserResponse getHello(@RequestParam String name, @RequestParam int age){
//        return restTemplateService.hello(name,age);
//    }

    @PostMapping("/userId/{userId}/name/{name}")
    public UserResponse post(@RequestBody UserRequest user,@PathVariable long userId, @PathVariable  String name){
        return restTemplateService.post(user,userId,name);
    }
}
```

서버측 컨트롤러에서도 비슷하게 대응해주자

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
    public User hello(@RequestBody User user,@PathVariable long userId, @PathVariable String name){
        log.info("request user : {}",user);
        return user;
    }

}
```

이제 [http://localhost:8089/api/client/userId/100/name/steve](http://localhost:8089/api/client/userId/100/name/steve) 로

```java
{
  "name":"steve",
  "age":20
}
```

위와 같이 요청을 보내면 아래처럼 콘솔에는 header, body, status 정보를 확인해볼 수 있고

talend 에서는 json 데이터를 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20postForEntity-json%ED%98%95%ED%83%9C%EB%A1%9C%20%EC%9D%91%EB%8B%B5%EC%9D%84%20%EB%82%B4%EB%A0%A4%EC%A3%BC%EA%B8%B0.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20postForEntity-json%ED%98%95%ED%83%9C%EB%A1%9C%20%EC%9D%91%EB%8B%B5%EC%9D%84%20%EB%82%B4%EB%A0%A4%EC%A3%BC%EA%B8%B0.gif?raw=true)

🌟만약 어떤 형태로 응답이 내려올지 모르겠다면, 개발 시에 RestTemplateService에서 메서드 리턴타입을 void로 해두고, ResponseEntity<UserResponse>부분을 ResponseEntity<String>으로 바꿔서 어떤 데이터가 오고 가는지 확인해보는 것도 하나의 전략이다! 이를 통해서 어떤 부분(예:notation)이 잘못되었는지 확인해봐도 좋다!