# 데이터베이스 커넥션 풀 DBCP

🌹 커넥션

- 데이터베이스 및 DBMS를 사용하기 위한 접속 요청을 통한 연결 상태(꼭 선행되어야 함!)

## DBCP; Database Connection Pool

![https://ejbvn.files.wordpress.com/2008/11/95.jpg?w=500&zoom=2](https://ejbvn.files.wordpress.com/2008/11/95.jpg?w=500&zoom=2)

[데이터베이스 커넥션  풀](https://ejbvn.wordpress.com/category/week-2-entity-beans-and-message-driven-beans/day-09-using-jdbc-to-connect-to-a-database/)

- 웹 페이지에 대한 클라이언트 요청이 많은 경우, 서버의 부하 증가로 인한 서버 다운 현상을 해결해주기 위한 방법
- DB의 효율성 및 속도 향상에 도움
- DBCP 매니저가 어느 정도의 연결을 확보해두고 있다가, 클라이언트 요청이 들어오면 연결해주고, 작업이 다 끝나면 클라이언트의 연결을 다시 DBCP 매니저에게 반환하도록 함

      (위의 그림이 그 과정을 잘 보여주고 있음)

🌻 그러면, 만약, 커넥션 풀이 가진 연결 양 < 클라이언트 요청 이라면??

▶️ 커넥션 풀이 가진 연결 양이 n개라면, 그 n 개 중 작업이 마무리 되는 대로 빈 자리에 연결된다!

🌻 만약, 커넥션 풀이 가진 연결 양 > 클라이언트 요청이라면?

▶️ 커넥션 풀이 가진 연결 양이 n개 라면, 클라이언트 요청이 그 숫자보다 적더라도, 항상 n 개 모두 열려있다!

🌺 커넥션 풀이 가진 연결 양은 옵션을 이용해서 수정할 수 있다!!

### DBCP 설치하기

1. 프로젝트 내부에 ojdbcX.jar 파일과 JSTL을 위한 jar 파일을 복사해두기!(WEB-INF/lib 폴더 내부에!)
2. jsp 파일을 생성, 실행하여 server.xml에서 context  패스 설정하기(이클립스에서는 실행하면 자동생성됨)

<Context docBase="web-study-09-DBCP"
					path="/web-study-09-DBCP" reloadable="true"
					source="org.eclipse.jst.jee.server:web-study-09-DBCP" />

3. 위의 context 패스 형태를 아래처럼 바꾸어, DBCP 이용을 위한 Resource 태그를 넣을 준비를 해주기

```xml
<Context docBase="web-study-09-DBCP"
					path="/web-study-09-DBCP" reloadable="true"
					source="org.eclipse.jst.jee.server:web-study-09-DBCP"></Context>
```

4. 아파치 홈페이지 [http://tomcat.apache.org](http://tomcat.apache.org)  에서 Documentation의 Tomcat X.X 선택

- 저는 톰캣 버전 9를 이용하고 있으므로 Documentation의 Version 9.0을 선택하겠습니다

5. 버전마다 항목 순서는 다를 수 있는데, "JDBC DataSource(s)"를 선택합니다

6. 나타나는 화면에서 "4. Oracle 8i, 9i & 10g" 선택

- 저는 지금 oracle 11g 를 사용하고 있지만, 문제되지 않을 것임을 확인하였습니다. (현재 아파치에서 업그레이드 되지 않은 상태라고 합니다)

[https://tomcat.apache.org/tomcat-9.0-doc/jndi-datasource-examples-howto.html#Oracle_8i,_9i_&_10g](https://tomcat.apache.org/tomcat-9.0-doc/jndi-datasource-examples-howto.html#Oracle_8i,_9i_&_10g)

7. "1. Context Configuration"에 있는 Resource 내용을 복사해서 붙여넣어 줍니다! 

- url 부분에서 저는 포트는 1521번, ip 주소는 로컬호스트로 동일하지만, 서비스는 xe로 이용하고 있으므로 mysid부분을 xe로 변경해주도록 하겠습니다!(sid 혹은 서비스네임, 아이디, 비밀번호를 반드시 확인 후 기입해주셔야 합니다!)

```xml
<Context docBase="web-study-09-DBCP"
					path="/web-study-09-DBCP" reloadable="true"
					source="org.eclipse.jst.jee.server:web-study-09-DBCP">
					<Resource name="jdbc/myoracle" auth="Container"
             			 type="javax.sql.DataSource" driverClassName="oracle.jdbc.OracleDriver"
              			url="jdbc:oracle:thin:@127.0.0.1:1521:xe"
              			username="scott" password="tiger" maxTotal="20" maxIdle="10"
              			maxWaitMillis="-1"/>	
</Context>
```

8. 프로젝트에 생성했던 jsp 파일 내부에 위에서 참고한 아파치에 있는 "3. Code example" 부분을 가져와서 스크립트릿으로 넣어줍니다!

```java
Context initContext = new InitialContext();
Context envContext  = (Context)initContext.lookup("java:/comp/env");
DataSource ds = (DataSource)envContext.lookup("jdbc/myoracle");
Connection conn = ds.getConnection();
```

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
**<%@ page import="java.sql.Connection" %>
<%@ page import="javax.sql.DataSource" %>
<%@ page import="javax.naming.InitialContext" %>
<%@ page import="javax.naming.Context" %>**
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>DB연동</title>
</head>
<body>
	<%
		**Context initContext = new InitialContext();
		Context envContext  = (Context)initContext.lookup("java:/comp/env");
		DataSource ds = (DataSource)envContext.lookup("jdbc/myoracle");
		Connection conn = ds.getConnection();**
	%>
</body>
</html>
```

하나하나 오목조목 살펴보도록 합시다!

(1) InitialContext 객체 생성

Context initContext = new InitalContext();

Context와 InitialContext에 대해서 각각 Open Declaration, Open Select를 통해서 확인해보면

InitialContext는 Context 라는 인터페이스를 구현한 클래스인 것을 확인해볼 수 있습니다

이를 바탕으로 이해해보았을 때, 위의 경우는,

InitialContext 객체를 생성하는데, 다형성이 적용되어서

```java
Parent p = new Child();
```

의 형태임을 나타내고 있음을 알 수 있습니다

상기시켜볼 점은

Up-casting 
- 크기가 작은 자료형 ▶️ 크기가 큰 자료형
- 자식 클래스 ▶️ 부모 클래스

되었기 때문에, 암시적인 캐스팅이 되어 형변환자를 생략해도 된다는 점이다!

---

🌺 잠깐! 인터페이스의 활용성 중 하나였던, 추상클래스를 이용한 활용도 다시 살펴보자!

- 아래처럼 하나의 인터페이스에 대해서는 사용할 기능 및 필드를 알려주기만 하고,
- 추상메서드에서 이를 상속받아서 더 상세하게 기능 및 필드를 명시해주는 것이 인터페이스를 사용하였을 때, 기능이 유사한 클래스들에서의 구현이 보다 간편해질 수 있다!

-인터페이스

```java
package com.poly.part01.test06;

public interface Area {
	
	String print =" 면적: ";
	
	public void print();
	public void make();
}
```

-추상메서드

```java
package com.poly.part01.test06;

//인터페이스를 구현하는데, 메서드를 구현하고 싶지 않다면
//클래스와 메서드를 추상클래스, 추상메서드로 만들어도 됨
public abstract class AreaImpl implements Area{

	private String result;
	@Override
	public void print() {
		// TODO Auto-generated method stub
		System.out.println(Area.print +result);
	}

	@Override
	public abstract void make();
	
	public void setResult(String result)
	{
		this.result = result;
	}
}
```

- 또한 이 방식을 이용했을 때, 추상 클래스의 장점으로써, 상세 기능은 추상클래스를 상속받을 클래스에서 작성할 수 있도록 추상 메서드로 설정해둘 수도 있다는 점도 존재한다!

---

(2) Context 객체의 lookup 메서드로 DBCP에서 지정한 이름찾기

- jdbc/myoracle은 Resource 태그에 존재했던 name 속성의 값!
- DataSource 객체는 Resource 태그에서 존재했던 type 속성의 javax.sql.DataSource에 해당!

```java
Context envContext  = (Context)initContext.lookup("java:/comp/env");
DataSource ds = (DataSource)envContext.lookup("jdbc/myoracle");
```

(3) DataSource 객체로 커넥션 객체 얻어내기(JDBC에서는 DriverManager로 얻어냈던 것과 유사하지만, url이나 id, pwd 부분이 매개변수에 기입되지 않아도 된다는 점이 눈에 띈다! 그 이유는 server.xml에서 url 부분과 아이디, 비밀번호, JDBC 드라이버를 로드하는 데에 필요한 부분이 Resource 태그 내에 기입되어 있기 때문인 것 같다!)

```java
Connection conn = ds.getConnection();
```

### DAO 객체와 VO 객체

🌹 DAO: Database Access Object

- DB의 데이터에 접근하기 위한 객체
- DB 레코드의 조회, 추가, 수정, 삭제 역할을 수행
- DB에서 얻은 데이터를 VO에 저장

🌹 VO : Value Object

- 회원 테이블의 정보를 자바에서 얻어오기 전에 회원 정보를 저장할 공간을 위한 준비 과정
- 자바 빈 객체 클래스와 동일한 기능을 한다고 이해하면 쉽다! 데이터를 담고 있는 객체!
- 속성, setter & getter 메서드로 구성됨
- 데이터를 전달하는 목적으로 사용한다고 해서 DTO(Data Transfer Object)라고도 부름

▶️ 자바 빈 == VO == DTO

### VO, DAO 객체를 이용하여 회원 관리 시스템 연습해보기

먼저 로그인 로직을 살펴보자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/DBCP-%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%B2%98%EB%A6%AC%20%EB%A1%9C%EC%A7%81.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/DBCP-%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%B2%98%EB%A6%AC%20%EB%A1%9C%EC%A7%81.png?raw=true)

데이터베이스 풀- 로그인 로직

먼저 구동 원리는 요청과 DB 처리를 위한 DAO, DTO(VO) 처리 단 사이에 컨트롤러가 위치하고, 컨트롤러가 필요에 의해서 DAO에게 일련의 작업을 요청하면, DAO는 DB와 DTO 객체를 주고받음으로써 작업을 수행한다. 그리고 그 결과는 컨테이너에게 전달하게 된다!

컨트롤러는 크게 로그인을 구현하는 LoginServlet, 아이디를 사용할 수 있는지 확인하는 IdCheckServlet,  회원가입을 진행하는 JoinServlet, 회원정보를 수정하는 memberUpdate, 로그아웃을 구현하는 LogoutServlet 으로 구성된다!

그리고 각 컨트롤러는 요청에 따라 
1)LoginServlet 은

- [login.do](http://login.do) 로 url을 매핑
- DB에 존재하는 회원이 확인되면, main.jsp로 이동
- DB에 존재하는 회원이 아니거나, 비밀번호가 일치하지 않으면 기존 페이지에 메시지를 띄우기

2) IdCheckServlet은

- IdCheck.do로 url 매핑
- 회원가입 페이지(join.jsp)에서 자바스크립트로 해당 주소로 아이디가 전달되면서 IdCheck.jsp로 이동된다!

3) JoinServlet은

- join.do로 url 매핑
- 각 입력양식의 최대 길이를 만족하고, 기존 사용자 아이디와 겹치지 않으면 회원가입이 완료됨
- 회원가입이 완료되거나 되지 않아도 login.jsp로 이동하되, 메시지는 다르게 띄우기

4) memberUpdate는

- main.jsp에서 접근할 수 있고, main.jsp로부터 id를 이용해서 dao 객체로부터 사용자 정보를 얻어오고, memberUpdate.jsp로 이동한 후, 수정할 정보를 가져오기
- memberUpdate.do로 url 매핑
- memberUpdate.jsp로부터 받은 정보를 VO 객체에 담고, 세션에 갱신된 정보를 저장한 후,  login.jsp로 리다이렉트

5) LogoutServlet은

- session을 invalidate() 메서드를 통해서 무효화 시키면 된다!
- 그 후에는 login.jsp페이지로 이동한다!

1. VO 클래스 만들기
- 먼저, 데이터를 전달하기 위한 클래스로, VO 클래스를 만들어보자
- 데이터는 아래와 같이 구성해보기로 한다

[Member_DBCP 테이블 구조](%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%E1%84%87%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8F%E1%85%A5%E1%84%82%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%91%E1%85%AE%E1%86%AF%20DBCP%20ebaf07ec15854536a3c0b4ba8280b742/Member_DBCP%20%E1%84%90%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%87%E1%85%B3%E1%86%AF%20%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%2010e273cedd4f4c1ea083bccc1a5b94bf.csv)

위의 데이터를 담기 위한 테이블 MEMBER 테이블을 아래의 질의문으로 생성해보자

- 주석으로 각 데이터에 대한 설명도 넣어주었다!

```sql
DROP TABLE MEMBER;

CREATE TABLE MEMBER(
	USERID VARCHAR2(10) PRIMARY KEY,
	NAME   VARCHAR2(10),
	PWD    VARCHAR2(10),
	EMAIL  VARCHAR2(20),
	PHONE  CHAR(13),
	ADMIN  NUMBER(1) DEFAULT 0 --0: 사용자, 1: 관리자
);

COMMENT ON COLUMN MEMBER.USERID IS '사용자아이디';
COMMENT ON COLUMN MEMBER.NAME IS '사용자이름';
COMMENT ON COLUMN MEMBER.PWD IS '사용자비밀번호';
COMMENT ON COLUMN MEMBER.EMAIL IS '사용자이메일주소';
COMMENT ON COLUMN MEMBER.PHONE IS '사용자연락처';
COMMENT ON COLUMN MEMBER.ADMIN IS '모드식별';

SELECT * FROM USER_COL_COMMENTS WHERE TABLE_NAME='MEMBER';

INSERT INTO MEMBER
VALUES('SOMI','이소미','1234','gmd@gmail.com','010-2362-5157',0);
INSERT INTO MEMBER
VALUES('SANG12','하상오','1234','ha12@naver.com','010-5629-8888',1);
INSERT INTO MEMBER
VALUES('LIGHT','김윤승','1234','youn1004@naver.com','010-9999-8282',0);

SELECT USERID, NAME, PWD, EMAIL, PHONE, ADMIN
FROM   MEMBER;
```

com.js.dto 패키지명으로 MemberVO 클래스를 생성하자!

```java
package com.js.dto;

public class MemberVO {
	//데이터필드
	private String name;
	private String userId;
	private String pwd;
	private String email;
	private String phone;
	private int admin;
	
	//constructors
	public MemberVO() {
		// TODO Auto-generated constructor stub
	}
	
	public MemberVO(String name, String userId, String pwd, String email, String phone, int admin) {
		this.name = name;
		this.userId = userId;
		this.pwd = pwd;
		this.email = email;
		this.phone = phone;
		this.admin = admin;
	}
	//getters & setters
	public String getName() {
		return name;
	}
	
	public void setName(String name) {
		this.name = name;
	}
	public String getUserId() {
		return userId;
	}
	public void setUserId(String userId) {
		this.userId = userId;
	}
	public String getPwd() {
		return pwd;
	}
	public void setPwd(String pwd) {
		this.pwd = pwd;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public String getPhone() {
		return phone;
	}
	public void setPhone(String phone) {
		this.phone = phone;
	}
	public int getAdmin() {
		return admin;
	}
	public void setAdmin(int admin) {
		this.admin = admin;
	}
	
	//jsp 파일에서 넘어오는 값은 String이므로 integer로 바꾸어주기!(<-오버로딩 이용!)
	public void setAdmin(String admin) {
		this.admin = Integer.valueOf(admin);
	}
	
	//toString()
	@Override
	public String toString() {
		return "MemberVO [name=" + name + ", userId=" + userId + ", pwd=" + pwd + ", email=" + email + ", phone="
				+ phone + ", admin=" + admin + "]";
	}
}
```

(1) 먼저 위에서 만든 DB 테이블을 참고하여 데이터 필드를 만들고

(2) 생성자, getter, setter 를 만든다

(3) 그 후에는 toString을 오버라이딩하여 데이터 객체를 String으로 손쉽게 추출할 수 있도록 한다!

2. DAO 객체 만들기

🌟 싱글톤 패턴

- 매번 DB의 데이터를 VO 객체를 통해 얻어오거나, VO 객체에 저장된 값을 DB에 추가할 때 매번 DAO 객체를 생성하는 것은 비효율적!
- 따라서, DAO 객체는 싱글톤 패턴으로 설계하는 것이 보다 효율적!
- 인스턴스는 오로지 단 하나만 존재하여, 객체를 메모리에 단 한 번만 올려놓고 시스템 전반에 걸쳐서 특정 자원(Object, Module, Component)을 공유할 때 사용되는 패턴
- 싱글톤 패턴의 모습을 아래의 구조로 살펴보자!

```java
public class MemberDao{
	private MemberDao(){
	//1.private한 생성자
  }
  //2.DAO객체를 클래스객체로, private하게 필드로 만들어두기
   private static MemberDao dao = new MemberDao();
  //3. 외부에서 객체에 접근하는 수단으로써 public 메서드를 두기
  //static 멤버를 반환해주어야 하고, 동시에 메모리에 실행 시 먼저 로드되는 static 특성상
//static 메서드로 이용
  public static MemberDao getInstance(){
		return dao;
   }
}
```

즉,

1.private한 생성자

2.Dao 클래스 내부에 private한  Dao 객체를 필드로 두기

🌟3.외부에서 접근할 수 있는 public 메서드를 두기

와 같은 형태로 설계하여 사용할 수 있다!

조심할 부분은, 3번인데, public static을 사용한 이유는

public으로 두어서 외부에서 dao 객체에 간접적으로 접근할 수 있도록 하기 위함

static 메서드 특성상 메모리에 프로그램 실행 시작 시, 먼저 탑재되고
static 멤버를 전달해줄 수 있다는 점

에서 사용한 것으로 조심스레 생각해본다!

이를 이용하여 DAO 객체를 위한 클래스를 만들어보자!

- 그리고!! DAO 객체에는 VO 객체를 이용한 데이터 송수신이 필요하므로 , 커넥션 객체를 위한 getConnection() 메서드를 작성해두자!

```java
package com.js.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.Statement;

import javax.naming.Context;
import javax.naming.InitialContext;
import javax.sql.DataSource;

import com.js.dto.MemberVO;
import com.js.invalidException.InvalidException;

public class MemberDao {
	Connection conn 	 = null;
	Statement  st   	 = null;
	PreparedStatement ps = null;
	ResultSet  rs   	 = null;
	
	//싱글톤 패턴
	//2.클래스 변수로 Dao 클래스 객체 만들어두기
	private static MemberDao dao = new MemberDao();
	//1.private 생성자
	private MemberDao() {
		
	}
	
	//3.외부에서 객체를 반환받을 수 있는 메서드
	public static MemberDao getInstance() {
		return dao;
	}
	
	//4.커넥션 객체  얻어오기
	public Connection getConnection() throws Exception{
		//커넥션 객체 준비 
		Connection conn = null;

		//InitialContext 객체 생성
		Context context = new InitialContext();
		//initial context의 lookup 메서드로 name 속성에 접근하기
		Context envContext = (Context) context.lookup("java:/comp/env");//Object->Context! 따라서 명시적 형변환!!
		//DataSource
		DataSource ds   = (DataSource)envContext.lookup("jdbc/myoracle");//Object->DataSource
		//커넥션 객체 생성
		conn = ds.getConnection();
		conn.setAutoCommit(false);//transaction autocommit 방지
		return conn;
	}
	
	//DAO에서 사용자 인증을 위함
	public int userCheck(String userId, String pwd) {
		//성공 or 실패 변수
		int res 			  = -1;//비밀번호가 일치하면 1, 아이디만 존재하면 0 , 아이디가 존재하지 않으면 -1 
		String  confirmQuery  = "SELECT PWD FROM MEMBER WHERE USERID=?";//아이디값으로 비밀번호를 찾아와서 일치 여부 확인
	
		try {
			
			//dbcp로부터 connection가져오기
			conn = getConnection();
			//statement 준비
			ps   = conn.prepareStatement(confirmQuery);
			ps.setString(1, userId);
			//query 실행 ->그 결과를 result set으로 받기
			rs   = ps.executeQuery();
			
			if(rs.next()) {
				//존재하는 것이 확인된다면
				//confirm process
				//null check가 선행되는 것이 보다 검색 성능이 좋을 것! 따라서 이를 먼저 적기!
				String compPwd =rs.getString(1);
				if(compPwd!=null && pwd.equals(compPwd)) {
					res = 1;//아이디, 비밀번호 모두 존재, 일치
				}else {
					res = 0;//아이디만 존재하고 비밀번호는 불일치!
				}
			}else {
				//존재하지 않는다면
				res = -1;
			}
			
		}catch(Exception e1) {
			System.out.println("[ERR]Failed to identify user "+userId);
			e1.printStackTrace();
		}finally {
			try {
				rs.close();
				ps.close();
				conn.close();
			}catch(Exception e2) {
				System.out.println("[ERR]ERROR INDUCED DURING CLOSING CONN IN USERCHECK!");
				e2.printStackTrace();
			}
		}
		return res;//성공 케이스 혹은 실패 케이스 여부 변수 전달(from database to was)(from was to web server)
		//tomcat은 was+web server이므로 위의 두 역할을 모두 수행
	}
	
	//아이디(기본키->식별자)로 회원 정보를 가져오기
	public MemberVO getMemberInfo(String userId) {
		//정보를 담을 vo 객체
		MemberVO mVo 		      = new MemberVO();
		//회원 정보를 담기 위한 조회 쿼리
		String   getUserInfoQuery = "SELECT USERID, NAME, PWD, EMAIL, PHONE, ADMIN FROM MEMBER WHERE USERID=?";
		try {
			//dbcp로부터 커넥션 가져오기
			conn = getConnection();
			//statement 준비
			ps    = conn.prepareStatement(getUserInfoQuery);
			ps.setString(1, userId);
			rs    = ps.executeQuery();
			
			if(rs.next()) {
				//조회 결과가 존재한다면, vo객체에 정보를 매핑해주기
				mVo.setUserId(rs.getString(1));
				mVo.setName(rs.getString(2));
				mVo.setPwd(rs.getString(3));
				mVo.setEmail(rs.getString(4));
				mVo.setPhone(rs.getString(5));
				mVo.setAdmin(rs.getInt(6));
			}else {
				//조회 결과가 존재하지 않는다면, 즉 그러한 레코드가 전혀 존재하지 않으면 콘솔에 문구 띄우기
				throw new InvalidException(new StringBuilder(userId+"는 존재하지 않습니다. 다시 확인해보세요").toString());
			}
		}catch(Exception e1) {
			System.out.println("[ERR]GET USER INFO FAILED! CHECK LOG");
			e1.printStackTrace();
		}finally {
			try {
				rs.close();
				ps.close();
				conn.close();
			}catch(Exception e2) {
				System.out.println("[ERR]CLOSING CONN ERR (GET USER INFO)");
				e2.printStackTrace();
			}
		}
		return mVo;//정보 넘기기
	}
	
	//아이디 중복 체크
	public int confirmId(String userId) {
		//기존 아이디가 존재하면 1, 존재하지 않으면 -1 반환
		int res = -1;
		String confirmIdQuery= "SELECT USERID FROM MEMBER WHERE USERID=?";
		
		try {
			conn = getConnection();
			
			ps   = conn.prepareStatement(confirmIdQuery);
			
			ps.setString(1, userId);
			
			rs   = ps.executeQuery();
			
			if(rs.next()) {
				res = 1;
			}else {
				res = -1;
			}
		}catch(Exception e1) {
			System.out.println("[ERR]아이디 중복체크 중 에러 발생 !!");
			e1.printStackTrace();
		}finally {
			try {
				rs.close();
				ps.close();
				conn.close();
			}catch(Exception e2) {
				System.out.println("[ERR]CLOSE ERROR(USER ID CHECKING)");
				e2.printStackTrace();
			}
		}
		
		return res;
	}
	
	//DB에 회원 정보 추가(저장)
	public int insertMember(MemberVO vo) {
		//insert 성공 여부
		int resIns = -1;
		String insertQuery="INSERT INTO MEMBER VALUES(?,?,?,?,?,?)";
		
		try {
			conn  = getConnection();
			ps    = conn.prepareStatement(insertQuery);
			//USERID, NAME, PWD, EMAIL, PHONE, ADMIN 
			ps.setString(1, vo.getUserId());
			ps.setString(2, vo.getName());
			ps.setString(3, vo.getPwd());
			ps.setString(4, vo.getEmail());
			ps.setString(5, vo.getPhone());
			ps.setInt(6, vo.getAdmin());
			
			resIns = ps.executeUpdate();
			
			if(resIns>0) {
				System.out.println("INSERT 성공");
				conn.commit();
			}else {
				System.out.println("INSERT 실패");
				conn.rollback();
			}
		}catch(Exception e1) {
			System.out.println("[ERR]INSERT FAILED");
			e1.printStackTrace();
		}finally {
			try {
				ps.close();
				conn.close();
			}catch(Exception e2) {
				System.out.println("[ERR]Close Error at INSERTING");
				e2.printStackTrace();
			}
		}
		
		return resIns;
	}
	
	//회원 정보 갱신시키기
	public int updateMember(MemberVO vo) {
		int resUp = -1;
		//USERID, NAME, PWD, EMAIL, PHONE, ADMIN
		String updateQuery="UPDATE MEMBER SET USERID=?, NAME=?, PWD=?, EMAIL=?, PHONE=?, ADMIN=? WHERE USERID=?";
		
		try {
			//dbcp에서 커넥션 얻어오기
			conn = getConnection();
			
			//preparedStatement 준비
			//memberUpdate.jsp의 request객체에 담겨져 있는 정보가 memberUpdate.do에 해당되는
			//서블릿 내부에서 처리 및 전달될 것이고, 그 내부의 dao객체 호출로 그 정보가 전달되어, 작업이 이루어질 것!
			ps   = conn.prepareStatement(updateQuery);
			ps.setString(1, vo.getUserId());
			ps.setString(2, vo.getName());
			ps.setString(3, vo.getPwd());
			ps.setString(4, vo.getEmail());
			ps.setString(5, vo.getPhone());
			ps.setInt(6, vo.getAdmin());
			ps.setString(7, vo.getUserId());
			System.out.println(updateQuery+", data:"+vo.toString());
			//실행
			resUp = ps.executeUpdate();
			
			if(resUp > 0) {
				conn.commit();
			}else {
				conn.rollback();
			}
			
		}catch(Exception e1) {
			System.out.println("[ERR]UPDATE USER INFO ERROR!");
			e1.printStackTrace();
		}finally {
			try {
				ps.close();
				conn.close();
			}catch(Exception e2) {
				System.out.println("[ERR] CLOSE ERROR AT UPDATE USER INFO");
				e2.printStackTrace();
			}
		}
		return resUp;
	}
}
```

- 더불어서, DAO 객체는 질의를 담당하므로, 그에 대한 메서드도 준비해두어야 한다!
- 아래의 표는 위의  DB 정보와 VO 클래스를 바탕으로 필요한 기능을 정리한 것이다!

[백견불여일타 JSP&SERVLET p.473 참조]

[MemberDao에서 필요한 메서드](%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%E1%84%87%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B3%20%E1%84%8F%E1%85%A5%E1%84%82%E1%85%A6%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%91%E1%85%AE%E1%86%AF%20DBCP%20ebaf07ec15854536a3c0b4ba8280b742/MemberDao%E1%84%8B%E1%85%A6%E1%84%89%E1%85%A5%20%E1%84%91%E1%85%B5%E1%86%AF%E1%84%8B%E1%85%AD%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%A5%E1%84%83%E1%85%B3%20e302e9560dbb4aa0a70b5ebc43709650.csv)

### 회원 인증을 위한 아이디, 비밀번호 입력 폼 login.jsp , LoginServlet.java

- 아이디, 비밀번호를 입력받는 폼 ▶️ jsp 페이지

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>회원관리</title>
</head>
<body>
	<header>
		<h1>로그인</h1>
	</header>
	<section>
		<article>
			<form action="./login.do" method="post" name="frm">
				<table>
					<tr>
						<td>아이디 </td>
						<td><input type="text" name="userId" id="userId" value="${userId }"></td>
					</tr>
					<tr>
						<td>비밀번호 </td>
						<td><input type="password" name="pwd" id="pwd"></td>
					</tr>
					<tr>
						<td colspan="2" align="center">
							<input type="submit" value="로그인" id="login" onclick="return loginCheck();">&nbsp;&nbsp;
							<input type="reset" value="취소" title="모든 값이 지워집니다">&nbsp;&nbsp;
							<input type="button" value="회원가입" onclick="location.href=`join.do`">
						</td>
					</tr>
					<tr>
						<td colspan="2">${msg}</td>
					</tr>
				</table>
			</form>
		</article>
		<%-- <article>
			<%
				String connect ="./index.jsp";
			%>
			<jsp:include page="${connect }"/>
		</article> --%>
	</section>
	<script src="../js/member.js"></script>
</body>
</html>
```

- 로그인을 위한 정보가 null인지 확인하는 방법 ▶️ 자바스크립트를 이용

```jsx
/**
 * 아이디, 비밀번호 유효성 검사
 */
//const login=document.getElementById("login");
const form    = document.frm;
const id      = document.frm.userId;
const pwd     = document.frm.pwd;

function loginCheck(){
	
	if(id.value.length===0 || id.value===""){
		alert(`아이디 확인 요망!`);
		return false;
	}
	
	if(pwd.value.length===0 || pwd.value===""){
		alert(`비밀번호 확인 요망`);
		return false;
	}
	return true;
}
```

- 입력받은 양식은 login.do로 연결되도록 하는데, 이를 통하여 서블릿에서 jsp 페이지를 실행할 수 있도록 하기◀️ url 매핑(pattern)을 login.do로 설정[@WebServlet("/login.do")]

```java
package com.js.controller;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import com.js.dao.MemberDao;
import com.js.dto.MemberVO;

/**
 * Servlet implementation class LoginServlet
 */
@WebServlet("/login.do")
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public LoginServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		String url = "member/login.jsp";
		HttpSession session=request.getSession();
		/*
		 * 이미 로그인된 사용자라면 메인페이지로,
		 * 그렇지 않으면 기본적으로 login.jsp로 이동
		 * 
		 * */
		
		//로그인되어있었다면
		if(session.getAttribute("logInUser")!=null) {
			//밑에 있는 post 등을 통해서 세션에 저장해둔 obj이 있었는지 확인
			//MemberDao dao = MemberDao.getInstance();
			MemberVO mVo = (MemberVO)session.getAttribute("logInUser");
			session.setAttribute("logInUser", mVo);
			url = "main.jsp";
		}
		RequestDispatcher dispatcher= request.getRequestDispatcher(url);
		dispatcher.forward(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//연결되는 url
		String connUrl = "member/login.jsp";
		
		//url로 연결되었기 때문에 request객체로부터 값을 가져올 수 있음
		String  userId = request.getParameter("userId");
		String  pwd    = request.getParameter("pwd");
		
		MemberDao dao  = MemberDao.getInstance();//singleton
		RequestDispatcher dispatcher = null;
		
		//login처리
		int   logInCk  = dao.userCheck(userId, pwd);
		
		if(logInCk ==1) {
			//성공 시 유저 정보 가져오기
			MemberVO mVo = dao.getMemberInfo(userId);
			//세션정보를 얻어와서 세션에 정보 저장해두기
			HttpSession session = request.getSession();
			session.setAttribute("logInUser", mVo);
			//로그인[회원가입] 성공 메시지는 세션에 저장할 필요는 없고 request객체 범위에만 저장해도 됨
			//그 이유는 로그인 정보는 사이트 내에서 사용자 정보가 공유되어야 로그인 이후 서비스 제공이 가능해지기 때문
			request.setAttribute("msg", "로그인/회원가입에 성공했습니다!");
			//페이지 포워딩을 위해서 url을 지정
			connUrl = "./main.jsp";
		}else if(logInCk ==0) {
			//비밀번호가 틀린 경우
			//메시지만 띄우기
			request.setAttribute("msg", "비밀번호가 맞지 않습니다");
		}else if(logInCk == -1) {
			//그런 사용자가 존재하지 않는 경우
			request.setAttribute("msg", "존재하지 않는 회원입니다");
		}
		
		dispatcher = request.getRequestDispatcher(connUrl);
		dispatcher.forward(request, response);
	}

}
```

⚠️ 위에서는 jsp 파일을 실행하지 않고 login.do로 요청하여 수행하도록 하고자 하였으나,  login.jsp로 바로 실행하는 경우도 있으며, 주소 입력란에 직접 입력하기 어렵기 때문에 이를 방지하기 위해서 index.jsp 파일을 만들어 홈페이지로 사용 ▶️ 컨텍스트 패스까지만 입력하면 index.jsp 페이지로 이동됨

- 원리- 기본적으로 톰캣서버를 이용할 때 web.xml 내부에서

```xml
<welcome-file-list>
		<welcome-file>index.html</welcome-file>
		<welcome-file>index.htm</welcome-file>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>
```

위와 같이 프론트 페이지 리스트가 설정되어 있기 때문!! 다른 파일을 프론트 페이지로 하고 싶다면 추가하면 된다!

### 회원가입을 위한 Join.jsp, JoinServlet.java

- 회원 가입 부분은 중간에 자바스크립트 오프너를 통해서 idCheck.do와 연결되므로 같이 다루도록 하겠다!

먼저, 앞에서 언급했던 연결되는 부분은 자바스크립트 오프너를 이용해서 팝업창처럼 띄워지게 된다!

```jsx
/**
 * 아이디, 비밀번호 유효성 검사
 */
//const login=document.getElementById("login");
const form    = document.frm;
const id      = document.frm.userId;
const pwd     = document.frm.pwd;

function loginCheck(){
	
	if(id.value.length===0 || id.value===""){
		alert(`아이디 확인 요망!`);
		return false;
	}
	
	if(pwd.value.length===0 || pwd.value===""){
		alert(`비밀번호 확인 요망`);
		return false;
	}
	return true;
}

function idCheck(){
	const url = "http://localhost:8081/web-study-09-DBCP/idCheck.do?userId="+id.value;//아이디 중복 체크 결과만 "출력"할 것이므로 get!!
	if(id.value.length===0 || id.value===""){
		alert(`아이디 확인 요망!`);
		id.focus();
		return false;
	}
	
	window.open(url,"_blank","toolbar=no, menubar=no, scrollbars=yes, resizable=no, width=450,height=200");
}
```

그러면, idCheck.do에서는 dao를 통해 db에서 일치하는 정보가 있는지 찾아서, 그 결과와 아이디를 request객체에 저장한다! 그 이유는 굳이 중복 여부만 확인하는 것인데, 세션에 저장할 필요가 없기 때문이다!

```java
package com.saeyan.controller;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.js.dao.MemberDao;

/**
 * Servlet implementation class IdCheckServlet
 */
@WebServlet("/idCheck.do")
public class IdCheckServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public IdCheckServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		String userId  = request.getParameter("userId");//member.js를 통해서 넘어오게 됨!
		MemberDao dao  = MemberDao.getInstance();
		int       res  = dao.confirmId(userId);
		
		//그저 중복확인만하는 것이기 때문에, 세션 전체에 알리는 것보다는 request간 알리는 것이 보다 효율적
		request.setAttribute("userId", userId);
		request.setAttribute("confirmId", res);
		
		RequestDispatcher dispatcher     = request.getRequestDispatcher("./member/idCheck.jsp");
		dispatcher.forward(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
//	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
//		// TODO Auto-generated method stub
//		doGet(request, response);
//	}

}
```

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%  request.setCharacterEncoding("UTF-8"); %>
<%  response.setContentType("text/html;charset=UTF-8"); %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>아이디 중복 체크</title>
</head>
<body>
	<header>
		<h1>아이디 중복 확인</h1>
	</header>
	<form action="idCheck.do" method="get" name="frm">
		<label>아이디&nbsp;<input type="text" name="userId" value="${userId}"></label>
		<input type="submit" value="중복 체크">
		<br>
		<c:if test="${confirmId==1}">
			<script>
				opener.document.frm.userId.value="":
			</script>
			${userId}는 이미 사용 중인 아이디입니다
		</c:if>
		<c:if test="${confirmId==-1}">
			${userId}는 사용 가능한 아이디입니다
			<input type="button" value="사용" class="cancel" onclick="idOk();">
		</c:if>
	</form>
	<script src="./js/member.js"></script>
</body>
</html>
```

그리고 사용한다는 버튼을 누르게 되면, 자바스크립트의 idOk 함수를 통해서 idCheck.jsp와 join.jsp 간 값 연결을 명시해준다!

```jsx
function idOk(){
	//join.jsp와 연결될 것!(from idCheck.jsp)
	opener.frm.userId.value=document.frm.userId.value;//오프너를 통해서 아이디값 이어주기
	opener.frm.reId.value  =document.frm.userId.value;
	self.close();
}
```

JoinServlet은 회원가입 요청이 들어오면, 그저  join.jsp 페이지로 연결해주고

join.jsp로부터 post 요청(정보 저장 요청으로써)이 들어오면, dao 객체를 통해 insert 작업을 수행하고, 그 결과에 따라 login.jsp 페이지에 띄울 메시지를 저장해준다 ! 최소한 범위가 request이상이면 되나, 세션을 조금 더 공부중인 상황이었기 때문에 session 객체로 메시지와 사용자 아이디를 저장해주었다.

```java
package com.saeyan.controller;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import com.js.dao.MemberDao;
import com.js.dto.MemberVO;

/**
 * Servlet implementation class JoinServlet
 */
@WebServlet("/join.do")
public class JoinServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public JoinServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//연결될 페이지 
		RequestDispatcher dispatcher= request.getRequestDispatcher("./member/join.jsp");
		dispatcher.forward(request,response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//회원정보를 저장하여 dao로 넘기기
		//인코딩 설정
		request.setCharacterEncoding("UTF-8");
		
		//join.jsp에 명시했던 name속성으로 가져오기
		String name   = request.getParameter("name");
		String userId = request.getParameter("userId");
		String pwd    = request.getParameter("pwd");
		String email  = request.getParameter("email");
		String phone  = request.getParameter("phone");
		int admin     = Integer.valueOf(request.getParameter("admin"));
		
		MemberVO vo   = new MemberVO();
		MemberDao dao = MemberDao.getInstance();
		int     resIns = 0;
		HttpSession session = request.getSession();
		
		vo.setUserId(userId);
		vo.setName(name);
		vo.setPwd(pwd);
		vo.setEmail(email);
		vo.setPhone(phone);
		vo.setAdmin(admin);
		
		resIns        = dao.insertMember(vo);
		
		if(resIns ==1) {
			session.setAttribute("userId",vo.getUserId());
			session.setAttribute("msg", "회원 가입에 성공했습니다");
		}else {
			session.setAttribute("msg", "회원 가입에 실패했습니다");
		}
		
		RequestDispatcher dispatcher=request.getRequestDispatcher("./member/login.jsp");
		
		dispatcher.forward(request, response);
	}

}
```

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%  request.setCharacterEncoding("UTF-8");%>
<%  response.setContentType("text/html; charset=UTF-8"); %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>회원 관리</title>
<style>
	em{
		color:red;
	}
</style>
</head>
<body>
	<header>
		<h1>회원 가입</h1>
	</header>
	<section>
		<article>
			<div><em>* 표시 항목은 필수 입력 항목입니다</em></div>
			<form action="join.do" method="post" name="frm">
				<!-- 정보 출력은 get, 저장은 post로 주로 사용됨! -->
				<table border="1">
					<tr>
						<td>이름<pre>		</pre></td>
						<!-- size는 maxlength를 지정 -->
						<td><input type="text" name="name" size="20">*</td>
					</tr>
					<tr>
						<td>아이디<pre>	</pre></td>
						<td>
							<input type="text" name="userId" size="20" id="userId">*
							<input type="hidden" name="reId" size="20">
							<input type="button" value="중복체크" onclick="idCheck();">
						</td>
					</tr>
					<tr>
						<td>암호<pre>		</pre></td>
						<td><input type="password" name="pwd" size="20">*</td>
					</tr>
					<tr height="30">
						<td width="80">암호 확인</td>
						<td><input type="password" name="pwdCheck" size="20">*</td>
					</tr>
					<tr>
						<td>이메일</td>
						<!-- string으로 받는 의미를 살리기 위해 text type 이용 -->
						<td><input type="text" name="email" size="20"></td>
					</tr>
					<tr>
						<td>전화번호</td>
						<td><input type="text" name="phone" size="20"></td>
					</tr>
					<tr>
						<td>이용모드</td>
						<td>
							<input type="radio" name="admin" value="0" checked>일반회원
							<input type="radio" name="admin" value="1">관리자
						</td>
					</tr>
					<tr>
						<td colspan="2" align="center">
							<input type="submit" value="확인" onclick="return joinCheck();"><pre>		</pre>
							<input type="reset" value="취소">
						</td>
					</tr>
					<tr>
						<td colspan="2">${msg}</td>
					</tr>
				</table>
			</form>
		</article>
	</section>
	<script src="./js/member.js"></script>
</body>
</html>
```

```jsx
//회원정보 유효성 검사
function joinCheck(){
	if(document.frm.name.value.length ===0 || document.frm.name.value===""){
		//이름 입력이 빠진 경우
		alert(`이름을 써주세요`);
		frm.name.focus();
		return false;
	}
	
	if(document.frm.userId.value.length===0 || document.frm.userId.value===""){
		//아이디 입력이 빠진 경우
		alert(`아이디를 써주세요`);
		frm.userId.focus();
		return false;
	}
	
	if(document.frm.userId.length<4){
		//아이디 입력 크기가 4이상으로 되도록 제한시키기!
		alert(`아이디는 4글자 이상이어야 합니다`);
		frm.userId.focus();
		return false;
	}
	
	if(document.frm.pwd.value.length=== 0 || document.frm.pwd.value===""){
		//비밀번호 입력이 빠진경우
		alert(`비밀번호는 반드시 입력해야 합니다`);
		frm.pwd.focus();
		return false;
	}
	
	if(document.frm.pwd.value !== document.frm.pwd_check.value){
		//비밀번호가 일치하지 않는 경우
		alert(`비밀번호가 일치하지 않습니다`);
		frm.pwd.focus();
		return false;
	}
	
	if(document.frm.reId.value.length ===0 || document.frm.reId.value ===0){
		//아이디 중복확인 후 입력되는 부분이 비어있는 경우
		alert(`아이디 중복체크를 하지 않았습니다`);
		frm.userId.focus();
		return false;
	}
	return true;
}
```

### 회원 정보를 수정하기

- 회원 정보를 수정하기 위해서, 먼저 가입된 회원이기 때문에 main.jsp 에서 요청을 할 수 있도록 한다
- 해당 요청이 들어오면, memberUpdate.do로 사용자 아이디와 함께 이동되는데, 그러면 그 요청을 받은 memberUpdate 서블릿은 memberUpdate.jsp로 연결해준다
- 그 후 memberUpdate.jsp에서 수정할 정보가 입력되면, memberUpdate.do로 요청이 들어오고, dao 객체를 통해서 update 작업이 이루어지고, 세션의 객체를 갱신시킨다
- 그 다음에는 세션에 저장을 했기 때문에 리다이렉트가 이루어져도 된다

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%  request.setCharacterEncoding("UTF-8"); %>
<%  response.setContentType("text/html;charset=UTF-8"); %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>개인 정보 수정</title>
</head>
<body>
	<header>
		<h1>나의 정보 수정</h1>
	</header>
	<section>
		<article>
			<form action="memberUpdate.do" method="post" id="frm">
				<table border="1" >
					<tr>
						<td>이름</td>
						<td><input type="text" name="name" size="20" value="${targetToUpdate.name}"></td>
					</tr>
					<tr>
						<td>아이디</td>
						<td><input type="text" name="userId" size="20" value="${targetToUpdate.userId }"></td>
					</tr>
					<tr>
						<td>비밀번호</td>
						<td><input type="password" name="pwd" size="20" value="${targetToUpdate.pwd }">*</td>
					</tr>
					<tr>
						<td>암호확인</td>
						<td><input type="password" name="pwd_check" size="20">*</td>
					</tr>
					<tr>
						<td>이메일</td>
						<td><input type="text" name="email" size="20"  value="${targetToUpdate.email }"></td>
					</tr>
					<tr>
						<td>전화번호</td>
						<td><input type="text" name="phone" size="20" value="${targetToUpdate.phone }"></td>
					</tr>
					<tr>
						<td>등급</td>
						<td>
							<c:choose>
								<c:when test="${targetToUpdate.admin==0 }">
									<label><input type="radio" name="admin" value="0" checked>일반회원</label>
									<label><input type="radio" name="admin" value="1">관리자</label>
								</c:when>
								<c:otherwise>
									<label><input type="radio" name="admin" value="0">일반회원</label>
									<label><input type="radio" name="admin" value="1" checked>관리자</label>
								</c:otherwise>
							</c:choose>
						</td>
					</tr>
					<tr>
						<td colspan="2" align="center">
							<input type="submit" value="확인" onclick="return joinCheck();"><pre>		</pre>
							<input type="reset"  title="입력한 정보가 모두 지워집니다" value="취소" onclick="location.href='./main.jsp';">
						</td>
					</tr>
				</table>
			</form>
		</article>
	</section>
	<script src="./js/member.js"></script>
</body>
</html>
```

```java
package com.saeyan.controller;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import com.js.dao.MemberDao;
import com.js.dto.MemberVO;

/**
 * Servlet implementation class memberUpdate
 */
@WebServlet("/memberUpdate.do")
public class memberUpdate extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public memberUpdate() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//메인페이지의 request 객체로부터 정보 가져오기
		String userId = request.getParameter("userId");
		MemberDao dao = MemberDao.getInstance();
		//userId로 사용자 정보 가져오기
		MemberVO  mVo = dao.getMemberInfo(userId);
		HttpSession session = request.getSession();
		session.setAttribute("targetToUpdate",mVo);//정보를 수정할 대상임을 나타내도록 변수 네이밍
		//get을 이용해서는 정보수정을 위한 정보를 받아올 폼페이지로 이동
		RequestDispatcher dispatcher = request.getRequestDispatcher("member/memberUpdate.jsp");
		dispatcher.forward(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//doGet(request, response);
		//dao로 정보 갱신을 위한 정보를 넘기기 위한 일련의 작업 수행
		MemberVO mVo = new MemberVO();
		//dao instance
		MemberDao dao = MemberDao.getInstance();
		HttpSession session = request.getSession();
		
		//0.encoding setting for post method
		request.setCharacterEncoding("UTF-8");
		
		//1. get info from memberUpdate.jsp
		String name=request.getParameter("name");
		String userId = request.getParameter("userId");
		String pwd    = request.getParameter("pwd");//js에서 이미 validation이 수행되었으므로
		//request중 pwd_check를 가져올 필요는 없음
		String email  = request.getParameter("email");
		String phone  = request.getParameter("phone");
		int    admin  = Integer.valueOf(request.getParameter("admin"));
		
		//2.mapping vars to membervo obj
		mVo.setName(name);
		mVo.setUserId(userId);
		mVo.setPwd(pwd);
		mVo.setEmail(email);
		mVo.setPhone(phone);
		mVo.setAdmin(admin);
		
		//3.dao에게 update를 수행시키기
		dao.updateMember(mVo);
		//4세션에 저장하기
		session.setAttribute("logInUser",mVo);
		//5.정보를 넘길 필요없이 리다이렉트로 페이지만 넘겨도 됨
		response.sendRedirect("login.do");
	}

}
```

### 로그아웃하기

- 로그아웃은 가장 간단하다! 지금 세션에 로그인된 사용자 정보가 담겨져 있었는데 이를 무효화하면서, login.jsp 페이지로 이동하면 된다!

```java
package com.saeyan.controller;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

/**
 * Servlet implementation class LogoutServlet
 */
@WebServlet("/logout.do")
public class LogoutServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public LogoutServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		
		//세션무효화->로그아웃
		HttpSession session = request.getSession();
		
		session.invalidate();
		
		RequestDispatcher dispatcher = request.getRequestDispatcher("./member/login.jsp");
		dispatcher.forward(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/DBCP-%EB%A1%9C%EA%B7%B8%EC%9D%B8%EC%B2%98%EB%A6%AC-%EB%A1%9C%EC%A7%81.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/DBCP-%EB%A1%9C%EA%B7%B8%EC%9D%B8%EC%B2%98%EB%A6%AC-%EB%A1%9C%EC%A7%81.gif?raw=true)

데이터베이스 커넥션풀을 이용한 로그인 로직 구현