# H2 In-Memory DB에 대해서

## 01. H2 데이터베이스 란

- 자바 기반의 경량화된 관계형 데이터베이스
- 파일로 저장해서 실제 db처럼 유지 가능
- 메모리 db로 사용해서 실제 인스턴스가 동작하는 시점에만 유지하기도 함

✴️ In-Memory 데이터베이스 ✴️

![https://assets-global.website-files.com/5debb9b4f88fbc3f702d579e/5e08f50b76f1c60b49b497e1_aa9152313a3347ecb378976e4731f705.png](https://assets-global.website-files.com/5debb9b4f88fbc3f702d579e/5e08f50b76f1c60b49b497e1_aa9152313a3347ecb378976e4731f705.png)

source: [https://assets-global.website-files.com/5debb9b4f88fbc3f702d579e/5e08f50b76f1c60b49b497e1_aa9152313a3347ecb378976e4731f705.png](https://assets-global.website-files.com/5debb9b4f88fbc3f702d579e/5e08f50b76f1c60b49b497e1_aa9152313a3347ecb378976e4731f705.png)

- 디스크가 아닌 메인 메모리에 설치되어 운영 ▶️ 응답 시간 최소화
- 메모리 상에서 인덱스 스캔방식(indexing)으로 빠르게 검색

[https://www.omnisci.com/technical-glossary/in-memory-database](https://www.omnisci.com/technical-glossary/in-memory-database)

[https://aws.amazon.com/ko/nosql/in-memory/](https://aws.amazon.com/ko/nosql/in-memory/)

---

🌟최근 스프링부트에서는 application.properties가 아닌, `application.yml` 을 주로 사용한다! application.properties를 지우고 생성해주자

(보다 계층적인 구조를 나타내고 있기 때문에 많이 사용되고 있음)

📌spring의 h2의 console에 대한 enabled를 true로 변경해주자

(`spring.h2.console.enabled=true`)

이는 H2 콘솔에 접근하기 위해서인데, 기본값이 false로 접근할수 없게 되어 있기 때문에 이렇게 바꿔주려는 것이다

이렇게 되면 h2 데이터베이스를 인메모리 데이터베이스로 바꿔줄 수 있게 된다!

[](https://www.baeldung.com/spring-boot-h2-database)

```yaml
server:
  port: 8089

spring:
  h2:
    console:
      enabled:true
```

더불어서, JPA사용을 위해서 User를 다음과 바꿔주자

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
/*@Getter
@Setter
@ToString
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@EqualsAndHashCode*/
@Data
@NoArgsConstructor
/*@Builder*/
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

그 이유는 기본 생성자가 필요한데, NoArgsConstructor가 붙으면 Builder 사용에 문제가 있기 때문이다

[Parameter 0 of constructor in required a bean of type 'java.lang.String' that could not be found](https://stackoverflow.com/questions/52841620/parameter-0-of-constructor-in-required-a-bean-of-type-java-lang-string-that-co)

```java
Parameter 0 of constructor in com.fastcampus.jpa.jpa_setting.domain.User required a bean of type 'java.lang.String' that could not be found
```

(객체 초기화가 필요)

그리고 실행해주면 아래처럼 H2 콘솔 접근이 가능해졌음을 확인해볼 수 있다

```java
2021-08-09 21:09:38.696  INFO 20132 --- [           main] o.s.b.a.h2.H2ConsoleAutoConfiguration    : H2 console available at '/h2-console'. Database available at 'jdbc:h2:mem:~'
```

이제 [http://localhost:8089/h2-console/](http://localhost:8089/h2-console/) 로 접근해보면 아래와 같은 화면을 마주할 수 있을 것이다

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/h2%20%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/h2%20%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95.PNG?raw=true)

여기서 JDBC URL 부분에 위에서 콘솔에서 확인했던 `Database available at 'jdbc:h2:mem:~'` 부분을 참좋해서 `jdbc:`부터 넣어주자!

그 뒤 connect를 눌러주면 아래와 같은 화면이 나타난다!

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/h2%20JDBC%20URL%EB%A1%9C%20%EC%A0%91%EA%B7%BC.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/h2%20JDBC%20URL%EB%A1%9C%20%EC%A0%91%EA%B7%BC.PNG?raw=true)

이것이 바로 하나의 DBMS라고 볼 수 있다

그리고 `SHOW DATABASES`를 입력해보면 아래와 같이 2개의 데이터베이스가 존재함을 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/SHOW%20DATABASES.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/SHOW%20DATABASES.PNG?raw=true)

### 인텔리제이에서 H2 콘솔 사용하기

1.우측 메뉴 중 `Database` 를 누르기

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B01.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B01.PNG?raw=true)

2.`Data Sources - H2` 를 누르기

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B02.png?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B02.png?raw=true)

3.초기 설치시, 하단에 아래처럼 다운로드 문구가 보이는데, 다운로드 해주기

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B03-%EC%B4%88%EA%B8%B0%EC%A0%91%EA%B7%BC%EC%8B%9C.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B03-%EC%B4%88%EA%B8%B0%EC%A0%91%EA%B7%BC%EC%8B%9C.PNG?raw=true)

4.URL부분에는 앞서 설정했던 JDBC URL을 넣어주기

5.그 후, test connection을 누르면 아래와 같이 연결에 성공했음을 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B04-h2%EB%93%9C%EB%9D%BC%EC%9D%B4%EB%B2%84%20%EC%84%A4%EC%B9%98%20%ED%9B%84%20JDBC%20URL%20%EC%9E%85%EB%A0%A5%20%ED%9B%84%20test%20connection.png?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B04-h2%EB%93%9C%EB%9D%BC%EC%9D%B4%EB%B2%84%20%EC%84%A4%EC%B9%98%20%ED%9B%84%20JDBC%20URL%20%EC%9E%85%EB%A0%A5%20%ED%9B%84%20test%20connection.png?raw=true)

```java
DBMS: H2 (ver. 1.4.200 (2019-10-14))
Case sensitivity: plain=upper, delimited=exact
Driver: H2 JDBC Driver (ver. 1.4.200 (2019-10-14), JDBC4.1)
Ping: 32 ms
```

6.connection type을 눌러보면 in memory, remote, embedded, url only가 있는데 이 중 in memory를 선택 후 ok를 누르자

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B05-connection%20type.png?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B05-connection%20type.png?raw=true)

그러면 아래처럼 H2 콘솔을 볼 수가 있는데(개인적으로 인텔리제이가 깔끔해서 웹에서 사용하는 것보다 이게 더 예쁜것 같다) 여기서 동일하게 show databases;를 명령하면 아래에서 동일한 결과를 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B0_%EC%99%84%EB%A3%8C.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/H2%20settings/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4%EC%97%90%EC%84%9C%20h2%20%EC%BD%98%EC%86%94%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B0_%EC%99%84%EB%A3%8C.PNG?raw=true)