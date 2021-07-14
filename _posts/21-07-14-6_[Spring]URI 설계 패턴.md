# URI 설계 패턴

1. What is URI?
- Uniform Resource Identifier
- 인터넷에서 특정 자원을 나타내는 주소값
- 이 값은 유일함!
- 응답은 달라질 수 있음

예)
요청: https://www.fastcampus.co.kr/resource/sample/1
응답: fastcampus.pdf, fastcampus.docx

2. What is URL?

- Uniform Resource Locator
- 인터넷 상에서의 자원, 특정파일이 어디에 위치하는지 식별하는 주소

예) https://www.fastcampus.co.kr/fastcampus.pdf

- URL은 URI의 하위 개념
- URI는 서버에 있는 리소스 정보로 대체될 수 있지만 URL은 변경될 수 없음(정확히 지정된 것)

📌 URI 설계원칙 RFC-3986

- / (슬래시 구분자)는 계층 관계를 나타내는 데 사용

예) https://fastcampus.co.kr/classes/java/curriculums/web-master

- URI 마지막 문자로 / 는 포함하지 않음

예) [https://fastcampus.co.kr/classes/java/curriculums/web-master**/**](https://fastcampus.co.kr/classes/java/curriculums/web-master/) 는 🚫

- -(하이픈)은 URI 가독성을 높이는데 사용

예) https://fastcampus.co.kr/classes/java/curriculums/web-master

- _(밑줄; 언더라인) 은 사용하지 않음

예) https://fastcampus.co.kr/classes/java/curriculums/web**_**master 는 🚫

- URI 경로에는 소문자가 적합!

예) https://fastcampus.co.kr/classes/JAVA/curriculums/web-master 🚫

예) https://fastcampus.co.kr/classes/java/curriculums/web-master 🆗

- 파일 확장자는 URI에 포함시키지 않도록 하기

예) [https://fastcampus.co.kr/classes/java/curriculums/web-master.jsp](https://fastcampus.co.kr/classes/java/curriculums/web-master.jsp) 🚫

- 프로그래밍 언어에 의존적인 확장자를 사용하지 않기

예) https://fastcampus.co.kr/classes/java/curriculums/web-master.do 🚫

-보안에 취약할 수 있기 때문

- 구현에 의존적인 경로를 사용하지 않기

예) https://fastcampus.co.kr/servlet/classes/java/curriculums/web-master 🚫

-보안에 취약할 수 있기 때문

- 세션 아이디를 포함시키지 않기

예) https://fastcampus.co.kr/classes/java/curriculums/web-master?session-id=abcdef   🚫

- 프로그래밍 언어의 메서드명을 이용하지 않기

예) https://fastcampus.co.kr/classes/java/curriculums/web-master?action=intro  🚫

- 명사에 단수형보다는 복수형을 사용해야 한다! 컬렉션에 대한 표현은 복수로 사용

예) https://fastcampus.co.kr/classes/java/curriculums/web-master

예) 컬렉션— users/jisoo

- 컨트롤러 이름으로는 동사나 동사구를 사용

예) https://fastcampus.co.kr/classes/java/curriculums/web-master/re-order

- 경로 부분 중 변하는 부분은 유일한 값으로 대체

예) lesson-id, user-id가 유일한 값일때

lessons/2/users/100으로 lesson-id가 2, user-id가 100인 경우에 접근

- CRUD 기능을 나타내는 것은 URI에 사용하지 않음

예) GET의 경우

.../web-master/lessons/2/users/100/READ  🚫

DELETE의 경우

.../web-master/lessons/2/users/100 🆗

- URI Query Parameter 디자인

-컬렉션 결과에 대해서 필터링

예) /web-master?chapter=2

- URI 쿼리는 컬렉션의 결과를 페이지로 구분하여 나타내는데 사용

예) /web-master?chapter=2&page=0&size=10&sort=asc

(➡️페이징 처리 등)

- API에 있어서 서브 도메인은 일관성 있게 사용해야 함

[https://fastcampus.co.kr](https://fastcampus.co.kr가)   가 도메인이라면, 서브도메인은

https://api.fastcampus.co.kr

https://api-fastcampus.co.kr

와 같이 작성

- 클라이언트 개발자 포털 서브 도메인은 일관성 있게 만들기

(개발환경과 사용자의 환경을 구분하기 위함)

https://dev-fastcampus.co.kr

https://developer-fastcampus.co.kr

위의 원칙을 지키지 않는다고 요청을 보낼수 없는것은 아니지만 좋은 개발을 위해서 지켜주도록 함이 좋을 것이다!