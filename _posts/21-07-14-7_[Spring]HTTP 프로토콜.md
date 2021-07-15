# HTTP Protocol

"메시지를 주고(Request) 받는(Response) 형태의 통신 방법"

- .Hyper Text Transfer Protocol
- RFC2616에서 규정된 웹에서 데이터를 주고받는 프로토콜
- 이름에는 하이퍼텍스트 전송용 프로토콜로 정의되었으나, 실제로는 html , xml, json, 이미지, voice, video, javascript, pdf 등 다양한 파일을 모두 전송 가능
- TCP(Transmission Control Protocol-IP 프로토콜을 기반으로 연결형 서비스를 제공하는 프로토콜 ↔️ UDP)를 기반으로 한 REST 의 특징을 모두 구현하고 있는 웹 기반의 프로토콜

🌟 반드시 request도 있고 response도 있음!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/HTTP%20%ED%86%B5%EC%8B%A0%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C/http%20%ED%86%B5%EC%8B%A0%20%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/HTTP%20%ED%86%B5%EC%8B%A0%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C/http%20%ED%86%B5%EC%8B%A0%20%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C.png?raw=true)

HTTP 통신 프로토콜

HTTP 통신 프로토콜은 클라이언트에서 서버측으로 메시지를 이용해서 요청을 보낸 후 응답을 기다린다. 그런데 이때 응답 시간이 너무 길어지면 요청을 취소하고, 기다리는 동안에는 리드 타이머 혹은 컬렉션 타이머 등을 이용한다. 그리고 이러한 요청을 받은 서버에서는 로직을 수행하고 응답메시지를 클라이언트에게 보낸다. 그러면 클라이언트에서는 일정 시간 내에 응답을 받게 되면, 이 응답메시지를 해석해서 화면에 표시처리를 하게 된다.

🌟 HTTP 요청 메서드

- 멱등성 : 계속 실행 되더라도 문제가 없어야 하는 것! 같은 요청에 대해서 여러번 호출해도 동일한 응답을 받아야 하는 것!
- 안정성 : 데이터 접근으로 인한 데이터 변경 등이 없는 지와 관련된 속성! "위험성이 있는가?"가 그 척도!
- Data Body: 요청 메시지가 담기는 부분 - 리소스를 가져
- 오는 GET, HEAD, DELETE, OPTIONS는 본문이 필요없음!

[https://developer.mozilla.org/ko/docs/Web/HTTP/Messages](https://developer.mozilla.org/ko/docs/Web/HTTP/Messages)

[HTTP 요청 처리 메서드](https://www.notion.so/302ff883152541609a89bf5ad7a742d2)

- PUT이 멱등한 이유

-예를 들어서 어떤 레코드가 없으면 생성하게 될 것이고, 있다면 업데이트하게 될 것이므로!

-다만, 데이터가 수정될 수 있으므로 안정할 수는 없다!

- DELETE가 멱등한 이유

-삭제를 하고 난 이후에는 이미 삭제가 된 상태이기 때문!(없으면 삭제된 상태로 간주)

-하지만 삭제를 하고 난 후에는 데이터가 없는 상황이라서 안정하지는 않음

🌟 **HTTP 상태 코드** ⭐

### 1xx: 처리가 계속 되고 있는 상태

- 클라이언트 : 요청을 계속하거나, 서버의 지시에 따라 재요청

### 2xx : 대부분 요청에 대한 응답이 성공하였음을 의미

### 201 : 리다이렉트, 리소스가 다른 장소로 변경됨을 알림

- PUT을 사용할 때 발견될 수 있음

[HTTP 상태 코드-200번](https://www.notion.so/64de31cfb9024496983aeccd32806c07)

### 3xx : 리다이렉트 유도 - 다른 리소스로 리다이렉트

[HTTP 상태 코드-300번대](https://www.notion.so/91c90f3c4043493b8d2391ec1ace8bfe)

### 4xx : 클라이언트의 코드가 잘못된 경우

- 유효하지 않은 자원에 대한 요청 혹은 권한이 잘못된 경우 발생
- 개발자가 다시 한번 확인해야할 부분

[HTTP 상태 코드-400번대](https://www.notion.so/ee1fe863f9414079850d1955474214e6)

### 5xx : 서버 에러

- 서버에 리소스 요청을 재전송하여서 해결가능한 상황일 수도 있음
- 혹은 수정중인 상황일 수도 있음

    ### 500 에러 : 서버 내부 동작 에러

[HTTP 상태 코드-500번대](https://www.notion.so/9c490c62086f45af9895de4d041573a0)

정리에 참고한 사이트: [https://joshua1988.github.io/web-development/http-part1/](https://joshua1988.github.io/web-development/http-part1/)

[https://developer.mozilla.org/ko/docs/Web/HTTP/Status](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)