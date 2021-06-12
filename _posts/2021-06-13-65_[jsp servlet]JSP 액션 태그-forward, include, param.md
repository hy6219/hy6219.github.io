# JSP 액션 태그-forward, include, param

- JSP 페이지를 구성하는 요소 중 하나

[JSP 액션 태그의 종류](JSP%20%E1%84%8B%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3-forward,%20include,%20param%209c87e32fc2b544d6804e5a4df914f024/JSP%20%E1%84%8B%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2%20c16f2da63a5445b9b39b95a555fd63df.csv)

useBean, set/getProperty는 자바 빈과 관련된 액션태그에서 따로 정리할 예정입니다!

- 액션태그 사용 목적과 장점: 자바코드나 지시자를 사용하지 않음으로써 가독성 높은 소스코드 작성이 가능하며, 코드양이 확연하게 감소할 수 있기 때문!

- XML 문법을 따름(Extensible Markup Language)

<jsp:...속성="값">내용</jsp:...>

혹은

<jsp:...속성="값"/> <!—마치 빈태그처럼! 내용이 없을 때 사용—>

1. <jsp:forward page="이동할 페이지"></jsp:forward>
- 페이지 포워딩

2. <jsp:param/>

<jsp:param name="파라미터" value="파라미터값/>

위와 같은 형식으로 사용하되, 단독으로 사용하지 못하고 include 및 forward 액션태그와 같이 사용됨!

- 요청한 페이지로 정보를 전달할 때 사용하는 태그

3. <jsp:include>

- include 지시자처럼 사용!
- 모든 페이지에서 공통적으로 사용되는 양식을 동적으로 포함시킴
- 화면 모듈화에 적합! ▶️ 모듈화될 각 파일은 모두 독립적!

<jsp: include page="페이지 URL" flush="true or false"/>

- page 속성 값에는 상대 및 절대 경로 모두 적용 가능!
- flush 속성 : 출력 버퍼에 저장되어 있는 데이터를 전달해주는 의미로 사용됨