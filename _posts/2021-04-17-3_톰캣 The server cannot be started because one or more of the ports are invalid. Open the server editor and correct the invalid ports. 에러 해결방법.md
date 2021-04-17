## 이클립스에서 톰캣 서버를 시작하는 과정에서 "The server cannot be started because one or more of the ports are invalid. Open the server editor and correct the invalid ports." 에러 메시지를 발견한 경우

우선 참고한 해결 페이지는 아래의 페이지였다! 감사합니다 ㅠㅠ

https://shinye0213.tistory.com/85


나는 우선 admin 계정의 포트를 변경해주면 되었던 간단한 방법이 통했다

밑에 server 탭에 추가했던 서버를 더블클릭해서

<img src="https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/errors/%ED%86%B0%EC%BA%A3%20%ED%8F%AC%ED%8A%B8%20%EB%B3%80%EA%B2%BD%EC%9C%BC%EB%A1%9C%20%ED%95%B4%EA%B2%B0%ED%95%9C%20%EC%97%90%EB%9F%AC.png?raw=true">

와 같이 Tomcat admin port에 ~라고 되어있는 부분을 8005로 수정해주면 된다! 창을 닫기 전에 ctrl+s로 꾹! 저장해주는 것도 잊지 말자!
