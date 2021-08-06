# RestTemplate-네이버 지역 검색 API 연동해보기

[지역 - Search API](https://developers.naver.com/docs/serviceapi/search/local/local.md#%EC%A7%80%EC%97%AD)

> ***비로그인 오픈 API***이므로 **GET으로 호출**할 때 **HTTP Header**에 애플리케이션 등록 시 발급받은 **Client ID와 Client Secret 값을 같이 전송**해 주시면 활용 가능합니다.

먼저, 로그인 후, api 사용신청을 하게 되면,

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/%EB%84%A4%EC%9D%B4%EB%B2%84%20%EA%B2%80%EC%83%89%20api%20%EC%82%AC%EC%9A%A9%20%EC%8B%A0%EC%B2%AD.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/%EB%84%A4%EC%9D%B4%EB%B2%84%20%EA%B2%80%EC%83%89%20api%20%EC%82%AC%EC%9A%A9%20%EC%8B%A0%EC%B2%AD.PNG?raw=true)

위와 같이 입력 양식이 보이게 되는데, 이때 사용할 api를 선택하고, url은 나의 경우 따로 호스팅 중인 사이트가 없기 때문에 로컬호스트를 사용하기로 해주었다

그러면 이제는 클라이언트 아이디와 클라이언트 secret이 표시되는데 이를 복사해두자!

그리고 사용 전에 제대로 데이터가 오는지, 어떻게 uri가 구성되는지를 확인해보기 위해서 우리의 talend tester를 이용해서 

- 떡볶이
- 5개 검색(최대 5개 검색지원 가능하다고 한다)
- 블로그 등 리뷰 개수 순

으로 검색하자

그리고 start는 어차피 기본 값 1로, 정렬기준으로 인덱스 1부터 보여주게 되어 있으므로 굳이 명시하지 않아도 될 듯하여 생략하자

그리고 헤더에는 X-Naver-Client-ID와 X-Naver-Client-Secret이라는 커스텀 헤더에 각각 Client ID, Client-secret 값을 실어주자(이 값은 비밀!이다😄 각자 부여받은 번호값을 넣어주면 된다)

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/%EB%84%A4%EC%9D%B4%EB%B2%84%20%EA%B2%80%EC%83%89%20api%20test.png?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/%EB%84%A4%EC%9D%B4%EB%B2%84%20%EA%B2%80%EC%83%89%20api%20test.png?raw=true)

그러면 위와 같이 떡볶이에 대해서 데이터를 확인해볼 수 있다

- title 은 업체나 기관명을
- link는 업체나 기관의 사이트 및 sns 주소를
- category는 분류 정보
- description은 업체나 기관의 설명 정보를

나타낸다

다른 필드들에 대해서도 위의 api 사이트에서 확인해볼 수 있다!

그리고 자세히 보면, query 값으로 utf-8인코딩을 한다고 되어 있는데

몇 가지 인코딩 변환 사이트를 알아보다가

[Online URL encoding ... percent-escape | Coder's Toolbox](http://coderstoolbox.net/string/#!encoding=url&action=encode&charset=utf_8)

아래의 사이트가 괜찮은 것 같았다. 몇 곳은 인코딩이 잘 안되는 것 같았다(위와 동일한 결과가 나오지 않았다)

URL에 사용할 것이고 utf-8을 사용하여 인코딩할 것이므로 각각을 체크해주면

"떡볶이"에 대해서 인코딩된 결과가 나오고 , 이를 query의 값으로 넣어주면

위와 동일한 결과를 확인해볼 수 있다

먼저 저번처럼 헤더값을 변경해주어야 하기 때문에 exchange 메서드를 이용하자

```java
package com.example.naversearchapi.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.io.UnsupportedEncodingException;
import java.net.URI;
import java.nio.charset.StandardCharsets;

@RestController
@RequestMapping("/api")
@Slf4j
public class ServerApiController {

    @GetMapping("/naver")
    public String naver(@RequestParam String search) throws UnsupportedEncodingException {
        //https://openapi.naver.com/v1/search/local.json?query=%EB%96%A1%EB%B3%B6%EC%9D%B4&display=5&sort=comment
        //로 요청을 보낼 것
        URI uri= UriComponentsBuilder.fromUriString("https://openapi.naver.com")
                .path("/v1/search/local.json")
                .queryParam("query",search)
                .queryParam("display","5")
                .queryParam("sort","comment")
                .encode()
                .build()
                .toUri();

        RestTemplate restTemplate =new RestTemplate();

        log.info("uri: {}",uri);

        RequestEntity<Void> requestEntity=
                RequestEntity.get(uri)
                             .header("X-Naver-Client-ID","your client id")
                             .header("X-Naver-Client-Secret","your client secret")
                             .build();

        //헤더값을 채워서 넣기 위해서 exchange이용
        ResponseEntity<String> res = restTemplate.exchange(requestEntity,String.class);

        return res.getBody();
    }

}
```

그리고 나서, 우리는 편안하게 검색할 키워드만 바꿔주면 Query Parameter로 사용될 search에 값이 들어가고, encode()로 인해서 utf-8로 인코딩될 것이다!

다양하게 김밥, 떡볶이에 대해서 검색을 해보자

[http://localhost:8089/api/naver?search=김밥](http://localhost:8089/api/naver?search=%EA%B9%80%EB%B0%A5)

[http://localhost:8089/api/naver?search=떡볶이](http://localhost:8089/api/naver?search=%EA%B9%80%EB%B0%A5)

![https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20%EB%84%A4%EC%9D%B4%EB%B2%84%20%EA%B2%80%EC%83%89%20api%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/RestTemplate/RestTemplate-%20%EB%84%A4%EC%9D%B4%EB%B2%84%20%EA%B2%80%EC%83%89%20api%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0.gif?raw=true)

그러면 위와 같이 데이터 5개가 확인된다!

그리고 encode()부분을

```java
.encode(Charset.forName("UTF-8"))
```

로 명시해줘도 좋다!

그리고 지금은 편의상 String으로 응답을 받았는데, api 센터에서 설명된 필드에 맞춰서 dto 객체 등을 만들어 적용해줘도 좋다