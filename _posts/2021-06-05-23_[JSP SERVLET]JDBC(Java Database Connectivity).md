# JDBC(Java Database Connectivity)

- 자바 프로그램에서 DB에 일관된 방식으로 접근할 수 있도록 API를 제공하는 클래스의 집합
- 몇몇 클래스들과 인터페이스의 묶음

## JDBC를 이용하여 데이터베이스에 연결하는 과정

1.JDBC 드라이버 로드 

⬇️

2.데이터베이스와 연결: DriverManager.getConnection()으로 Connection 객체 얻어오기

⬇️

3.SQL문 실행 : 
(1) 실행 준비1
Statement ~=connection.createStatement() 혹은
PrefaredStatement ~=connection.prepareStatement(sql문을 의미하는 String)

(2)SELECT문은 Statement객체.executeQuery()
그 외: Statement 객체.updateQuery()

⬇️

4.데이터베이스와 연결 끊음

---

## JDBC 드라이버 연결하기

### 이클립스에서 드라이버 연결을 위한 jar 파일 및 설정 준비하기

I. 이클립스-DB 연결
1)Data Source Explorer

2)Database Connections 우클릭-new 선택

3)New Connection Profile이 뜸(계정명, 비밀번호를 통해서 접속했던 것과 동일)

4)Oracle 선택후 name 변경하지 않아도 된 채 next 누르기

5)new Driver Definition누르기(상자 두 개가 겹쳐진 버튼)

6)oracle thin driver 11 클릭-jar List탭 클릭

7)ojdbc14.jar 지우기(clear all)-add jar-C:\oraclexe\app\oracle\product\11.2.0\server\jdbc\lib 경로에 접근-ojdbc6.jar 선택-열기-(x표시가 없어짐을 확인-)ok

8)service name을 xe 로 적기(Database service name)

- connection url을 보면 @server가 있는데 이는 어디에 접속할지를 나타내는 것!
-밑에 host에 127.0.0.1(localhost)를 입력하면 @server도 127.0.0.1(localhost ip주소)로 바뀜
[이는 기존 url 중 localhost에 해당! 만약,나중에 다른 컴퓨터의 db에 접속하려면 host부분을 변경해주면 됨] ➕ C:\oraclexe\app\oracle\product\11.2.0\server\jdbc\lib [오라클이 설치된 경로] 에서 tnsname.ora를 확인하여 db이름에 대해서 연결된 포트번호 확인하기!(참고로 기본연결은 1521포트)
 -user name, password 모두 sqlplus에서 create user identified by로 생성한 계정으로 로그인
 -save password 체크-test connection 누르기
-ping succeeded!가 뜨면 db와 이클립스가 연결된 것!
-finish 누르기

9)JDBC 연결을 진행할 프로젝트에 우클릭-Build Path-Configure Build Path -Libraries- External Library-위에서 추가했던 경로 내의 ojdbc.jar 파일을 클릭-apply and close

▶️ Referenced Libraries 가 생길 것!

(JSP에서는 아래의 방법을 추천!!)

[https://hy6219.github.io/1_JSP%EC%97%90%EC%84%9C-JDBC-%EC%97%B0%EA%B2%B0-%EC%8B%9C-%EB%82%98%ED%83%80%EB%82%98%EB%8A%94-%EC%97%90%EB%9F%AC/](https://hy6219.github.io/1_JSP%EC%97%90%EC%84%9C-JDBC-%EC%97%B0%EA%B2%B0-%EC%8B%9C-%EB%82%98%ED%83%80%EB%82%98%EB%8A%94-%EC%97%90%EB%9F%AC/)

### JDBC 사용을 위한 템플릿 만들어서 사용하기

- JDBC사용을 동일한 계정으로 접근하는 경우 등이라면, 템플릿을 만들어두고 접근하여 사용하는 것이 보다 효율적이다! 차근차근 접근해보자!
- 주의할 점은, 두 개의 작업이 진행되고 있었는 데 둘 중 하나라도 작업이 실패하면 바로 둘 다 취소할 수 있도록 commit과 rollback을 적용해주어야 한다!
1. JDBC 드라이버를 등록하고, Connection 객체로 접속하기

```java
//1.DB연결
	public static Connection getConnection() {
		
		try {
			//1.드라이버 등록
			Class.forName("oracle.jdbc.driver.OracleDriver");
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			//드라이버 등록 실패 알리기
			System.out.println("[ERR]드라이버 등록 실패");
			e.printStackTrace();
		}
		//2.DB연결 준비-url, 계정명, 비밀번호
		String url="jdbc:oracle:thin:@localhost:1521:xe";
		String id = "KH";
		String pwd = "KH";
		
		//3.Connection 객체로 DB 연결하기
		Connection conn = null;
		
		try {
			conn = DriverManager.getConnection(url,id,pwd);
			conn.setAutoCommit(false);//자동으로 commit하는 것을 막고
			// 둘 중 하나라도 작업 실패 시 취소할 수 있도록 메서드를 만들어 개발자가
			//관리할 수 있도록 하기!
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return conn;
	}
```

먼저, JDBC 드라이버를 등록하기 위해서

Class.forName 메서드에 JDBC 드라이버를 의미하는 **oracle.jdbc.driver.OracleDriver**를 매개변수로 넣어준다!

그 다음에는 

Connection 커넥션인스턴스=DriverManager.getConnection(url, uId, uPwd);

와 같이 

jdbc 드라이버 타입 및 호스트, 포트번호, db이름 정보를 담은 url
**jdbc:oracle:thin:[hostname][:port]:dbname**

계정명, 계정 비밀번호

로 커넥션 객체를 만들어 DB에 연결해준다!

2. ResultSet, Statement, Connection 객체를 모두 닫아서 연결 해제하기!(오버로딩)

```java
//2.DB연결 해제-오버로딩하여 statement와 resultSet, connection 모두 close
	public static void close(Connection conn) {
		try {
			conn.close();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			System.out.println("[ERR]Connection Close Error!");
			e.printStackTrace();
		}
	}
	
	public static void close(Statement stat) {
		try {
			stat.close();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			System.out.println("[ERR]Statement Close Error");
			e.printStackTrace();
		}
	}
	
	public static void close(ResultSet rs) {
		try {
			rs.close();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			System.out.println("[ERR]ResultSet Close Error!");
			e.printStackTrace();
		}
	}
```

템플릿을 이용해서 JDBC 드라이버 등록 후 DB 연결 후에 사용을 하였다면, 그 다음에는 DB 연결을 해제해주어야 한다! **SELECT문을 사용했다면 ResultSet이 반환**되므로, ResultSet도 닫아주어야 한다!

이를 오버로딩을 이용한다면 보다 효율적으로 사용할 수 있다!

3. 둘 중 하나가 작업 문제가 생겼을 때를 위한 메서드1-commit()

```java
//3.커밋(저장)
	public static void commit(Connection conn) {
		try {
			conn.commit();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			System.out.println("[ERR]Commit Error!");
			e.printStackTrace();
		}
	}
```

- 앞서 언급했던 바와 같이, 동시에 작업되던 관계있는 작업 중 하나가 실패했을 때에는 저장되지 않도록 방지해야 한다!
- 그래서 위에서 커넥션을 만들때 커넥션객체에 대해서 setAutoCommit을 false로 설정했다!
- 지금 위에서는 JDBC 템플릿 클래스에서 사용가능한 commit을 작성해줌으로써, 이제는 개발자가 원하는 로직, 상황, 시점에서 커밋을 통해 데이터를 저장해줄 수 있다!

4. 둘 중 하나가 작업 문제가 생겼을 때를 위한 메서드2-rollback()

```java
//4.롤백(취소)
	public static void rollback(Connection conn) {
		try {
			conn.rollback();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			System.out.println("[ERR]Rollback Error!");
			e.printStackTrace();
		}
	}
```

- 이번에는 앞서 설명한 것과 동일한 상황에서, 작업을 취소해줄 필요가 있을 때 사용하고자 기존의 rollback 메서드를 JDBC 템플릿 클래스에서 이용하여 작성하였다!

---

## Statement, PreparedStatement

1. SELECT, UPDATE, DELETE 사용에 따른 쿼리문 실행 메서드 종류

[쿼리문 실행 메서드](https://www.notion.so/10e90c3a5c8a4e218b3b4de7eba61174)

🌺 특히, SELECT문은 한 행 이상의 데이터가 ResultSet으로 반환되므로, 자바에서도 ResultSet 객체를 만들어서 결과를 저장해서 statement 객체와 활용해주어야 한다!!

Statement stat= conn.createStatement();
stat.executeQuery(쿼리문); 혹은 stat.executeUpdate(쿼리문);

🌺 ResultSet 객체는 실제 데이터가 저장된 영역 외에 BOF(Begin Of File)과 EOF(End Of File)이 존재한다!

🌹 BOF: 첫 행 이전의 레코드 Set ≠ 헤더

🌹 EOF: 마지막 행 이후의 레코드 Set

🌺 ResultSet 클래스 메서드

- 성공적으로 진행될 경우에만 true 반환!

[ResultSet 클래스에서 제공되는 메서드](https://www.notion.so/4e341d6313984be39493fa1c8548cdba)

ResultSet 객체로 결과를 반환받아왔다면, getXXX메서드로 데이터를 선택해서 저장할 수 있다!

XXX는 String, Int 등을 의미한다! getXXX메서드는

🌺getXXX(인덱스[1부터 시작])

🌺getXXX("속성명")

으로 값에 연결하여 사용할 수 있다!

2. PreparedStatement

- 컬럼 갯수가 많은 경우 등에 바인딩변수인 ?를 이용하여 쿼리문에 속성을 기입하는 어려움을 해소 시켜줄 수 있는 방법!
- 대신, 바인딩 변수?에 setXXX로 값을 설정해주어야 한다!
- 바인딩 변수를 이용한 쿼리문 준비

```java
String sql = "SELECT (?,?,?,?,?) FROM 테이블명";
```

- PreparedStatement 객체 생성

```java
PreparedStatement pStat = 커넥션객체.prepareStatement(sql);
```

- 바인딩 변수로 지정된 매개 변수에 값 설정

```java
pStat.setString(1,name);
pStat.setInt(2,Integer.valueOf(age));
```

- 쿼리문 실행 후 닫기

```java
pStat.executeQuery(); 혹은 pStat.executeUpdate();
pStat.close();
```

정리해보자면, Statement와 PreparedStatement 의 usage는

Statement: sql문 없이 먼저 connection 객체로 statement 객체 생성 ▶️ 
                  statement 객체의 executeXXX메서드로 쿼리문 실행 ▶️  statement 객체 닫기

```java
package statement;

import static common.JDBCTemplate.*;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class JDBCStatement {
	public static void main(String args[]) {
		//쿼리문 준비
		String sql="SELECT ID AS 아이디, NAME AS 이름, AGE AS 나이, EMAIL AS 이메일 FROM MEMBER_TMP";
		//1.JDBCTemplate을 이용한 DB 연결
		//connection
		Connection conn = getConnection();
		
		//2.Statement 준비
		Statement stat  = null;
		//ResultSet 준비
		ResultSet rs =null;
		try {
			stat = conn.createStatement();
			//3.sql문 실행
			rs = stat.executeQuery(sql);
			//4.실행결과를 한행씩 넘기면서 출력하기
			while(rs.next()) {
				StringBuilder sb = new StringBuilder("아이디: "+ rs.getString(1)+", 이름: "+
									rs.getString(2)+", 나이: "+rs.getInt(3)+", 이메일: "+rs.getString(4));
				System.out.println(sb.toString());
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			System.out.println("[ERR]Statement 생성 오류!");
			e.printStackTrace();
		}finally {
			//닫기
			close(rs);
			close(stat);
			close(conn);
			
		}
	}
}
```

PreparedStatement : sql문으로 connection객체를 기반으로 PreparedStatement 객체 생성
▶️ 바인딩 변수 및 setXXX메서드로 쿼리문 마저 완성하기 ▶️ executeXXX메서드로 쿼리문 실행 후 닫기

```java
package preparedStatement;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import static common.JDBCTemplate.*;
public class Prepared {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		//1.sql문 입력해두기
		String sql="SELECT * FROM MEMBER_TMP WHERE ID=? AND NAME=?";
		//2.Connection, PreparedStatement, ResultSet 준비
		Connection conn = null;
		PreparedStatement stat= null;
		ResultSet rs= null;
		
		//3.DB연결하기
		conn=getConnection();
		//4.statement생성하기
		try {
			stat = conn.prepareStatement(sql);
			//5.바인딩변수 ? 와 매칭시키기
			stat.setString(1, "100");
			stat.setString(2, "김길동");
			//6.쿼리 실행하기
			rs = stat.executeQuery();
			while(rs.next()) {
				StringBuilder sb = new StringBuilder("아이디: "+ rs.getString(1)+", 이름: "+
									rs.getString(2)+", 나이: "+rs.getInt(3)+", 이메일: "+rs.getString(4));
				System.out.println(sb.toString());
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally {
			close(rs);
			close(stat);
			close(conn);
		}
	}

}
```

와 같이 처음에 Connection 객체에 sql문을 같이 넣어주는지, executeXXX메서드 내부에 쿼리문을 넣어주는지(넣으면 Statement, 아니라면 PreparedStatement)의 차이가 있다!