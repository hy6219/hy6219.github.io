# REST Client 설치하기

크롭 웹스토어- REST API Client 검색- "Talend API Tester - Free Edition "을 크롬에 추가- 퍼즐모양 클릭- Free Edition ▶️ 웹 애플리케이션이 실행됨

🌟 스프링에서 컨트롤러 등은 main-java-패지키명.어플리케이션명 밑에 작성하기!!

🌟 @RestController 어노테이션은 Rest컨트롤러로 작동할 것임을 명시

- REST 컨트롤러는 REST API를 처리하는 컨트롤러

🌟 @RequestMapping: 요청에 대한 URI를 매핑

⭐ @GetMapping : GET 요청에 따른 파라미터 위치를 매핑

📌 RequestMapping과 GetMapping의  뒤에 오는 괄호 안에는 주소가 string타입으로 와야 한다!

```java
package com.hello.helloworldapi.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api") //URI를 지정해주는 어노테이션
public class ApiController {

    @GetMapping("/hello") // http://localhost:포트번호/api/hello로 매핑
    public String Hello(){
        return "HELLO SPRING BOOT!";
    }
}
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/Overview/Hello%20world%20api_spring.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/Overview/Hello%20world%20api_spring.png?raw=true)

위와 같이 만약 RequestMapping을 uri로, GetMapping을 hello로 해준 후, 서버 포트를 8089로 요청했다면

응답은 http://localhost:8089/api/hello  에서 확인할 수 있게 된다

현재는 문자열로만 출력했기 때문에 plain text(text/plain)인 것을 헤더에서 확인해볼 수 있다

그리고 GET방식은 조회하는 기능이기 때문에 브라우저에서 확인또한 가능하다!(해당 주소를 주소창에 넣으면 잘 작동할 것이다!(HELLO SPRING BOOT!가 출력될 것이다)

위의 talwind api에서도 이를 확인해볼 수 있는데, 현재 상태코드가 200으로 잘 응답받았음을 확인해볼 수 있다