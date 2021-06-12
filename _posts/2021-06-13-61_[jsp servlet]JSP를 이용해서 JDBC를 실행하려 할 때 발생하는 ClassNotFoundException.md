# JSP를 이용해서 JDBC를 실행하려 할 때 발생하는 java.lang.ClassNotFoundException:oracle.jdbc.driver.OracleDriver 처리하기

jsp 파일 내부에서 JDBC를 사용하려고 general java project처럼 build path를 통해서 ojdbc.jar 파일을 추가해서 referenced library를 연결해주고, 이를 이용하려 했었다..

하지만..

java.lang.ClassNotFoundException: oracle.jdbc.driver.OracleDriver

에러가 엄청나게 많이 뜨게 되었다..😭

WEB-INF/lib를 확인해보아도 분명 jar파일이 들어가 있는데도, 문제가 되어 찾아보다가

import방식을 해보았는데.. 일단 해결은 되었다..😭

아래는 참고한 블로그이다!

[https://wakestand.tistory.com/101](https://wakestand.tistory.com/101)