# JSP 내장 객체

- 서블릿 컨테이너가 JSP가 서블릿 파일로 변환될 때, 서블릿 객체가 자동으로 생성해주기 때문에, 우리는 바로 사용이 가능!
- 서블릿 컨테이너가 만든 서블릿 위치 : eclipse\web_workspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\work\Catalina\localhost\jsp-study\org\apache\jsp
- _jspService() 내부에서 객체화됨!
- jsp의 내장객체 종류

[jsp 내장 객체 종류](JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/jsp%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2%20e7007f28b8424d55a927145ff1377b66.csv)

I. 입출력 관련 객체

1. **out 내장 객체**(JspWriter 클래스의 객체!)
- **서버 ▶️ 클라이언트 로 열려있는 출력 스트림**
- JSP 실행 결과를 클라이언트의 브라우저로 출력할 때 가장 효과적으로 사용할 수 있는 객체
- 더이상 서블릿에서처럼 아래와 같은 객체를 생성하여 사용하지 않아도 됨!

PrinterWriter out = response.getWrtier();

2. request 내장 객체

- 클라이언트 ▶️ 서버로 전송되는 데이터를 알 수 있도록 해주는 객체

(1)

[request 내장 객체를 통해서 브라우저 요청시  요청에 대한 정보를 알 수 있도록 도와주는 메서드들](JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/request%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%90%E1%85%A9%E1%86%BC%E1%84%92%E1%85%A2%E1%84%89%E1%85%A5%20%E1%84%87%E1%85%B3%E1%84%85%E1%85%A1%E1%84%8B%E1%85%AE%E1%84%8C%E1%85%A5%20%E1%84%8B%E1%85%AD%E1%84%8E%E1%85%A5%E1%86%BC%E1%84%89%E1%85%B5%20%E1%84%8B%E1%85%AD%20003bfd6593f34d0b81585195d4df653b.csv)

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Server Info</title>
</head>
<body>
	<%="<p>컨텍스트 패스: "+request.getContextPath()+"</p>" %>
	<%="<p>요청 방식: "+request.getMethod()+"</p>" %>
	<%="<p>요청 URL: "+request.getRequestURL()+"</p>" %>
	<%="<p>요청 URI: "+request.getRequestURI()+"</p>" %>
	<%="<p>쿼리스트링: "+request.getQueryString()+"</p>" %>
	<%="<p>Login.jsp 로컬  RequestDispacher 객체: "+request.getRequestDispatcher("Login.jsp")+"</p>" %>
	<%="<p>요청한 호스트의 완전한 이름: "+request.getRemoteHost()+"</p>" %>
	<%="<p>요청한 호스트의 네트워크 주소: "+request.getRemoteAddr()+"</p>" %>
	<%="<p>요청한 사용자의 이름: "+request.getRemoteUser()+"</p>" %>
	<%="<p>세션: "+request.getSession()+"</p>" %>
	<%="<p>서버이름: "+request.getServerName()+"</p>" %>
	<%="<p>프로토콜: "+request.getProtocol()+"</p>" %>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/jsp%20request%20%EB%82%B4%EC%9E%A5%EA%B0%9D%EC%B2%B4-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%20%EC%9A%94%EC%B2%AD%EC%97%90%20%EB%8C%80%ED%95%9C%20%EC%A0%95%EB%B3%B4%EB%A5%BC%20%EC%95%8C%EB%A0%A4%EC%A3%BC%EB%8A%94%20%EB%A9%94%EC%84%9C%EB%93%9C.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/jsp%20request%20%EB%82%B4%EC%9E%A5%EA%B0%9D%EC%B2%B4-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%20%EC%9A%94%EC%B2%AD%EC%97%90%20%EB%8C%80%ED%95%9C%20%EC%A0%95%EB%B3%B4%EB%A5%BC%20%EC%95%8C%EB%A0%A4%EC%A3%BC%EB%8A%94%20%EB%A9%94%EC%84%9C%EB%93%9C.PNG?raw=true)

(2) request 객체의 요청 파라미터 관련 메서드

[request 객체의 요청 파라미터 관련 메서드](JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/request%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%E1%84%8B%E1%85%B4%20%E1%84%8B%E1%85%AD%E1%84%8E%E1%85%A5%E1%86%BC%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%A7%E1%86%AB%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%A5%E1%84%83%E1%85%B3%20da6c89c5cb454023b2f0c58e9c794654.csv)

3. response 내장 객체

- 클라이언트에 대한 응답 처리
- response 객체의 기능 중 리다이렉트 기능(sendRedirect() 메서드)을 多 이용!

[https://beginnersbook.com/2013/11/jsp-implicit-object-response-with-examples/](https://beginnersbook.com/2013/11/jsp-implicit-object-response-with-examples/)

[response 객체의 메서드](JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/response%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%E1%84%8B%E1%85%B4%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%A5%E1%84%83%E1%85%B3%20f9dceac25fa042e190bf18bf28b79871.csv)

🍑 로그인 성공 실패에 따른 서로 다른 페이지 이동을 위해서 리다이렉트 기능을 사용하는 경우 多

로그인 성공, 실패에 따른 리다이렉트를 다음과 생각해볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/%EB%A6%AC%EB%8B%A4%EC%9D%B4%EB%A0%89%ED%8A%B8%20%EB%B0%A9%EB%B2%95%EC%9C%BC%EB%A1%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%B2%98%EB%A6%AC%ED%95%98%EB%8A%94%20%EB%B0%A9%EC%8B%9D.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/%EB%A6%AC%EB%8B%A4%EC%9D%B4%EB%A0%89%ED%8A%B8%20%EB%B0%A9%EB%B2%95%EC%9C%BC%EB%A1%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%20%EC%B2%98%EB%A6%AC%ED%95%98%EB%8A%94%20%EB%B0%A9%EC%8B%9D.png?raw=true)

response 객체로 리다이렉트하기-로그인

1) (지금은 DB연동없이 단순 값 비교만 하였지만) DB에 존재하는 값과 일치하는지 확인(회원 인증 절차 jsp 파일)

2-1) 일치한다면 메인 페이지로 리다이렉트[이때, java.net.URLEncoder 클래스의 encode 메서드로 인코딩이 깨지지 않게 하면서, 쿼리 스트링으로 실을 변수와 그 값을 넘겨줌]

2-2) 일치하지 않는다면, 로그인 페이지로 리다이렉트

- 로그인 페이지

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Login Page</title>
</head>
<body>
	<form action="testLogin.jsp" method="post">
		<div class="id">
			<label for="id">id: </label>
			<input type="text" name="id" id="id"placeholder="아이디">
		</div>
		<div class="pw">
			<label for="pw">pw: </label>
			<input type="password" name="pw" id="pw"placeholder="비밀번호">
		</div>
		<div>
			<input type="submit" value="로그인">
		</div>
	</form>
</body>
</html>

```

- 회원 인증 절차 jsp 파일

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ page import="java.net.URLEncoder" %>
<%!
	//id
	String id = "";
	//pw
	String pw = "";
%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>로그인세션</title>
</head>
<body>
	<%
		//response 객체에 실어서 보낼 사용자의 이름
		String name ="지수";
		request.setCharacterEncoding("UTF-8");
		id = request.getParameter("id");
		pw = request.getParameter("pw");
		
		if(id.equals("js") && pw.equals("pw")){
			response.sendRedirect("main.jsp?name="+
			URLEncoder.encode(name,"UTF-8"));
		}else{
			response.sendRedirect("loginForm.jsp");
		}
		
		
	%>
</body>
</html>
```

- 메인 페이지

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Main Page</title>
</head>
<body>
	<%=request.getParameter("name")+"님 안녕하세요!<br>"+
	"저희 홈페이지에 방문해 주셔서 감사합니다!<br> 즐거운 시간 되세요!<br>"%>
</body>
</html>
```

---

### JSP 에서 다른 페이지로 이동하기 위한 방법

1. 리다이렉트 방식 - response.sendRedirect("리다이렉트 할 url")
2. 포워드 방식

---

1. 리다이렉트 방식 : 브라우저의 URL을 변경하도록 하여 페이지를 이동
- request, response 객체가 유지되지 않음
- 위의 로그인 예시에서 확인해 본 것처럼, 리다이렉트 메서드에 기술했던 URL이 쿼리 스트링과 함께 나타남
- 한글 인코딩 처리를 위하여 java.net.URLEncoder 클래스의 encode 메서드로 처리해주어야 함!

response.sendRedirect("main.jsp?name="+
			URLEncoder.encode(name,"UTF-8"));

- pageContext, request 객체가 모두 유실됨

![JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/___pageContext_request___.png](JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/___pageContext_request___.png)

2. 포워드 방식 : requestDispatcher 객체로 접근

- requestDispatcher 객체=request.getRequestDispatcher("URL") 로 생성 가능
- 포워드 방식: requestDispatcher 객체.forward() 를 통하여 getRequestDispatcher 메서드로 전달된 URL, 즉 페이지로 이동됨
- 서버 상에서 페이지가 이동됨
- URL 변경 ❌
- request, response 유지된 채 이동됨 ▶️ request.setAttribute(name,value); 로 데이터를 전송(쿼리 스트링 형태로 전달 ❌) && 이동된 페이지에서는 getAttribute(name)으로 속성 값을 얻어와서 사용 가능!
-단, getAttribute는 Object 형으로 반환되므로, 적절히 캐스팅해주어야 함!
- pageContext 객체만 유실됨

![JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/__pageContext_.png](JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/__pageContext_.png)

- 한글 인코딩을 위한 별다른 처리가 필요 ❌

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/%ED%8F%AC%EC%9B%8C%EB%94%A9%20%EB%B0%A9%EC%8B%9D%EC%9C%BC%EB%A1%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/%ED%8F%AC%EC%9B%8C%EB%94%A9%20%EB%B0%A9%EC%8B%9D%EC%9C%BC%EB%A1%9C%20%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%98%EA%B8%B0.png?raw=true)

- 로그인 포맷 페이지 -LoginForm.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Login Page</title>
</head>
<body>
	<form action="testLogin.jsp" method="post">
		<div class="id">
			<label for="id">id: </label>
			<input type="text" name="id" id="id"placeholder="아이디">
		</div>
		<div class="pw">
			<label for="pw">pw: </label>
			<input type="password" name="pw" id="pw"placeholder="비밀번호">
		</div>
		<div class="age">
			<label for="age">나이: </label>
			<input type="number" name="age" id="age"placeholder="나이">
		</div>
		<div>
			<input type="submit" value="로그인">
		</div>
	</form>
</body>
</html>
```

- 나이 기준에 따른 처리 - testLogin.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
 <%!
	//id
	String id = "";
	//pw
	String pw = "";
	//age
	int age = 0;
 %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Forward Login</title>
</head>
<body>
	<%
		request.setCharacterEncoding("UTF-8");
		id = request.getParameter("id");
		pw = request.getParameter("pw");
		age = Integer.valueOf(request.getParameter("age"));
		
		if(age < 19){
			%>
			<script type="text/javascript">
				alert(`19세 미만이므로 입장 불가`);
				history.go(-1);
			</script>
			<% 
		}
		else{
			request.setAttribute("name", "지수");
			RequestDispatcher dispatcher=
					request.getRequestDispatcher("Result.jsp");
			dispatcher.forward(request, response);
		}
		%>
</body>
</html>
```

- 로그인 성공 시 서버상 작동되는 페이지- Result.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%--get attribute --%>
<%
//선언문에서는 메서드를 사용할 수 없지만 스크립트릿에서는 사용할 수 있으므로! 
//이를 이용
	String id = request.getParameter("id");
	String pw = request.getParameter("pw");
	int   age = Integer.valueOf(request.getParameter("age"));
	//서버에서 보낸 값 받기
	String name = (String)request.getAttribute("name");
%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Forward</title>
</head>
<body bgcolor=tomato>
	forward 방식으로 이동된 페이지<br>
	나이 : <%=age %>
	이름 : <%=name %>
</body>
</html>
```

[history 객체의 속성과 메서드]

[history 객체의 속성](JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/history%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%20ee037891f00a45999685612a6d6399ef.csv)

[history 객체의 메서드](JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/history%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%E1%84%8B%E1%85%B4%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%A5%E1%84%83%E1%85%B3%20ba51189177b9464dacd8e81814742589.csv)

---

4. application 내장 객체

- 하나의 웹 애플리케이션 안에서 자원을 공유
- 하나의 웹 애플리케이션을 관리

▶️ 

application.setAttribute(속성,값)

은 application 객체 내부에 속성이 저장되고, 이 속성-값이 모든 JSP 페이지 내에서 공통적으로 사용될 수 있게 됨

- javax.servlet.ServletContext 인터페이스를 구현한 객체
- 웹 애플리케이션 당 하나만 생성됨
- 서블릿 컨테이너 정보 제공, 서블릿 컨테이너에게 로그 처리를 요청 등..

[application 객체의 메서드](JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%200dd1d6c5414b4e359bd6429c2044d178/application%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%E1%84%8B%E1%85%B4%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%A5%E1%84%83%E1%85%B3%204281c1144c32463c8cd4f375da9c2d45.csv)

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/application%20%EA%B0%9D%EC%B2%B4.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/application%20%EA%B0%9D%EC%B2%B4.PNG?raw=true)

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/application-log.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/builtInObject/application-log.PNG?raw=true)

---

**📌 내장 객체의 영역 📌**

- 객체의 유효기간!!

**page 영역 < request 영역 < session 영역 < application 영역**

1. page 영역
- 하나의 JSP 페이지를 처리할 때 사용되는 영역
- 한 번의 클라이언트 요청에 하나의 JSP 페이지를 범위로 가짐(해당되는 pageContext 내장객체를 할당받음)

2. request 영역

- 하나의 요청을 처리할 때 사용되는 영역
- 하나의 요청을 처리하는 데 사용되는 모든 JSP 페이지
- 브라우저가 요청을 할 때마다 새로운 request 내장 객체가 생성되고, 매번 새로운 request 영역이 생성됨

3. session 영역

- 웹 브라우저를 닫기 전까지 페이지를 이동하더라도 사용자의 정보를 잃지 않고 서버에 보관할 수 있도록 하는 객체

4. application 영역

- 하나의 웹 애플리케이션과 관련된 모든 페이지, 그 페이지에 대한 요청, 세션을 모두 포함
- 하나의 웹 애플리케이션에 존재하는 모든 JSP 페이지는 하나의 application 내장 객체를 공유함
(application 내장 객체는 application 영역에 포함됨)

*내장객체에서 사용할 수 있는 메서드 추가!!*

- getAttributeNames(): 현재 객체에 관련된 모든 속성의 이름을 추출함
- removeAttribute(name) : 매개변수로 전달된 속성의 값을 제거
- 리턴값은 Object 타입!