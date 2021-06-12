# 표현 언어(EL)와 JSTL

## 표현언어 EL; Expression Language

- 값(데이터)를 웹 페이지에 표시(표현)하는 데 사용되는 대그
- JSP 출력에 대한 부분을 쉽게 하기 위해 개발한 태그
- 스크립트릿에서 표현식을 나타내었던

<%=expr%>

이 표현언어 EL에서

${expr}

과 매칭됨!

- 사용가능한 데이터 타입 : 정수형, 실수형, 논리형, 문자열, null 등

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL;Expression Language; 표현언어</title>
</head>
<body>
	문자열: ${"hello"}<br>
	정수형: ${3}<br>
	실수형: ${3.5}<br>
	논리형: ${!true}<br><!-- false -->
		  ${true}<br><!-- true -->
    null : ${null}<br>
    !falsy: ${!null }<br><!-- true -->
</body>
</html>
```

- EL은 식을 계산해서 결과를 출력하는 것을 목적으로 함!
- 표현 언어 연산자 - EL 식으로 접근된 데이터를 조작 및 비교할 연산자

[표현 언어 연산자의 종류](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%89%E1%85%A1%E1%86%AB%E1%84%8C%E1%85%A1%E1%84%8B%E1%85%B4%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2%205f678b6866f94ac5b9c81e4d19458fe3.csv)

⚠️ \${exp}는 표현 언어가 아닌 단순 문자열로 인식할 수 있도록 해준다!

⚠️ JSTL 의 조건식 내에 비교를 위한 관계형 연산자는 반드시 EL로 기술해야함!

[표현 언어에서의 내장객체 종류](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5%E1%84%8B%E1%85%A6%E1%84%89%E1%85%A5%E1%84%8B%E1%85%B4%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2%2044fe05923d83493b8162a694ab20757d.csv)

⚠️ param 내장객체는 마치 자바스크립트에서 객체 멤버에 접근하는 것과 비슷한 형태로 사용됨!

param.필드 혹은 param[필드]

⭐ EL에서만!!  문자열에 대해서 == 연산자와 equals()가 같은 기능을 한다!

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL & JSTL</title>
</head>
<body>
	==연산자 사용결과: <%=request.getParameter("id")=="pinksung" %><br>
	equals 사용결과 : <%=request.getParameter("id").equals("pinksung") %><br>
	EL에서 == 사용 결과: ${param.id=="pinksung"}<br>
	EL에서 equals 사용 결과: ${param.id.equals("pinksung") }<br>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/EL%EC%97%90%EC%84%9C%20==,%20equals%20%EC%97%B0%EC%82%B0%EC%9E%90.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/EL%EC%97%90%EC%84%9C%20==,%20equals%20%EC%97%B0%EC%82%B0%EC%9E%90.PNG?raw=true)

⭐request.getParameter() 메서드는 파라미터를 찾지 못하면 null 값을 리턴하기 때문에, HTTP 상태코드 500을 반환한다! ▶️ 따라서! 이에 따른 예외처리를 해주어야 한다!

### 표현 언어로 내장 객체 접근하기

- 속성(내장 객체에서 정보를 주고받기 위한 메서드로 관리되는 정보의 대상)에 저장된 값에 접근하기

[표현언어의 내장 객체 접근하기](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5%E1%84%8B%E1%85%B4%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%20%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%20%E1%84%8C%E1%85%A5%E1%86%B8%E1%84%80%E1%85%B3%E1%86%AB%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5%20190d16d533934d46b5ac3d957af22fc5.csv)

*복습*

[JSP 내장객체의 정보를 주고 받기 위한 메서드들](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/JSP%20%E1%84%82%E1%85%A2%E1%84%8C%E1%85%A1%E1%86%BC%E1%84%80%E1%85%A2%E1%86%A8%E1%84%8E%E1%85%A6%E1%84%8B%E1%85%B4%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%87%E1%85%A9%E1%84%85%E1%85%B3%E1%86%AF%20%E1%84%8C%E1%85%AE%E1%84%80%E1%85%A9%20%E1%84%87%E1%85%A1%E1%86%AE%E1%84%80%E1%85%B5%20%E1%84%8B%E1%85%B1%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%A5%E1%84%83%E1%85%B3%E1%84%83%20db4c6305803342d7bd9d196e5b4bd48b.csv)

*모델 1 방식: JSP로만 웹 프로그래밍을 하는 것

모델 2 방식: 서블릿에서 비즈니스 로직을 구현, JSP에서는 결과를 사용자에게 어떻게 보여줄 것인지 표현만 함

유효범위에 대한 속성 page, request, session,application에 대한 JSP 내장객체, 서블릿 클래스, 표현 언어의 내장 객체의 사용

[유효범위 속성에 대한 다양한 사용 형태](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/%E1%84%8B%E1%85%B2%E1%84%92%E1%85%AD%E1%84%87%E1%85%A5%E1%86%B7%E1%84%8B%E1%85%B1%20%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%E1%84%8B%E1%85%A6%20%E1%84%83%E1%85%A2%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%83%E1%85%A1%E1%84%8B%E1%85%A3%E1%86%BC%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%20%E1%84%92%E1%85%A7%E1%86%BC%E1%84%90%E1%85%A2%206e260296f5784662a70755412762ad2f.csv)

[JSP 내장 객체에서 속성값을 얻어오는 방법 vs EL 내장 객체에서 속성값을 얻어오는 방법]

(1) page 속성에 대해서

- JSP 내장 객체: pageContext.getAttribute("num1");
- EL 내장 객체 : ${pageScope.num1}

(2) request 속성에 대해서

- JSP 내장 객체 : request.getAttribute("num1");
- EL 내장 객체 : ${requestScope.num1}

(3) session 속성에 대해서

- JSP 내장 객체 : session.getAttribute("num1");
- EL 내장 객체 : ${sessionScope.num1}

(4) application 속성에 대해서

- JSP 내장 객체 : application.getAttribute("num1");
- EL 내장 객체 : ${applicationScope.num1}

```java
package unit07;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class AdditionServlet
 */
@WebServlet("/AdditionServlet")
public class AdditionServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public AdditionServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		int num1 = 20;
		int num2 = 10;
		int sum  = num1 + num2;
		
		request.setAttribute("num1", num1);
		request.setAttribute("num2", num2);
		request.setAttribute("sum", sum);
		
		RequestDispatcher dispatcher=
				request.getRequestDispatcher("07_addition.jsp");
		dispatcher.forward(request, response);
	}

}
```

만약 위와 같이 속성을 지정하고, 포워딩으로 jsp 페이지로 값을 넘겨주었다면,

request 속성에 이미 num1을 넘겨주었기 때문에,

requestScope를 생략하고 ${num1}으로 사용할 수 있다!

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL &amp; JSTL</title>
</head>
<body>
	${num1}+${num2}=${sum}
</body>
</html>
```

그 원리는, EL 내장 객체를 명시하지 않으면 아래와 같이 속성을 순서대로 자동으로 검색하고, 이로 인해서 속성값을 얻어와서 출력하기 때문이다!

pageScope ▶️ requestScope ▶️ sessionScope ▶️ applicationScope

⭐ 그렇기 때문에 아래와 같이 동일한 속성명으로 각 속성에서 값을 지정하고, EL을 이용해서 ${num1}와 같이 JSP에서 적어두면, 가장 좁은 범위인 pageScope에 해당되는 num1을 이용하게 될 것이다!

```java
pageContext.setAttribute("num1", num1);
request.setAttribute("num1", num1);
session.setAttribute("num1", num1);
application.setAttribute("num1", num1);
```

⭐ 그런데 만약, 각 범위의 값을 제각각 이용하고 싶다면

${pageContext.num1} ${requestScope.num1} ${sessionScope.num1} ${applicationScope.num1}

과 같이 어떤 속성에 매칭되는 지 명시적으로 기재해줄 필요가 있다! 바로 아래처럼!

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%
	pageContext.setAttribute("name", "pageMan");
	request.setAttribute("name","requestMan");
	session.setAttribute("name","sessionMan");
	application.setAttribute("name","applicationMan");
%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL &amp; JSTL</title>
</head>
<body>
	name: ${name }<br>
	page 속성에 들어있는 name 속성: ${pageScope.name }<br>
	request 속성에 들어있는 name 속성: ${requestScope.name }<br>
	session 속성에 들어있는 name 속성: ${sessionScope.name }<br>
	application 속성에 들어있는 name 속성: ${applicationScope.name }<br>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%EC%9C%A0%ED%9A%A8%EB%B2%94%EC%9C%84%EC%97%90%20%EA%B4%80%EB%A0%A8%EB%90%9C%20%EC%86%8D%EC%84%B1%EC%9D%98%20%ED%8A%B9%EC%A7%95.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%EC%9C%A0%ED%9A%A8%EB%B2%94%EC%9C%84%EC%97%90%20%EA%B4%80%EB%A0%A8%EB%90%9C%20%EC%86%8D%EC%84%B1%EC%9D%98%20%ED%8A%B9%EC%A7%95.PNG?raw=true)

이번에는 뒤에 학습하게 될 MVC 패턴 등에 익숙해지기 위해서

- 서블릿 : 자바 빈 객체 생성, 속성은 자바 빈 객체를 넣기
- 자바 빈: 데이터를 관리
- JSP : 서블릿으로부터 전달된 데이터를 EL로 출력하기

와 같이 틀을 잡고 정보를 전달해보자!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%EC%84%9C%EB%B8%94%EB%A6%BF%EA%B3%BC%20%EC%9E%90%EB%B0%94%EB%B9%88,%20JSP%20%EA%B0%84%20%EC%97%AD%ED%95%A0%EC%9D%84%20%EB%B6%84%EB%8B%B4%ED%95%98%EC%97%AC%20%EC%A0%95%EB%B3%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%EC%84%9C%EB%B8%94%EB%A6%BF%EA%B3%BC%20%EC%9E%90%EB%B0%94%EB%B9%88,%20JSP%20%EA%B0%84%20%EC%97%AD%ED%95%A0%EC%9D%84%20%EB%B6%84%EB%8B%B4%ED%95%98%EC%97%AC%20%EC%A0%95%EB%B3%B4%20%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0.png?raw=true)

-서블릿

```java
package unit08;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.saeyoen.javabeans.MemberBean;

/**
 * Servlet implementation class MemberServlet
 */
@WebServlet("/MemberServlet")
public class MemberServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public MemberServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//자바 빈 객체 생성하기
		MemberBean member =new MemberBean("전수빈","pinksubin");
		//자바 빈 객체로 정보를 묶어졌으므로, 이를 request 속성에 보내기
		request.setAttribute("member", member);
		
		RequestDispatcher dispatcher=
				request.getRequestDispatcher("09_el.jsp");
		dispatcher.forward(request, response);
	}

}
```

-자바 빈 클래스

```java
package com.saeyoen.javabeans;

public class MemberBean {
	private String name;
	private String id;
	
	public MemberBean() {
		super();
		// TODO Auto-generated constructor stub
	}
	
	public MemberBean(String name, String id) {
		super();
		this.name = name;
		this.id = id;
	}

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	//자바 빈 객체에 저장된 필드 값을 접근해서 사용하는 레퍼런스 변수만 기술해도
	//출력될 수 있도록!
	@Override
	public String toString() {
		return "MemberBean [name=" + name + ", id=" + id + "]";
	}
	
	
}
```

-JSP

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>EL &amp; JSTL</title>
</head>
<body>
	이름: ${member.name}<br>
	아이디: ${member.id}<br>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%EC%9E%90%EB%B0%94%EB%B9%88,%20%EC%84%9C%EB%B8%94%EB%A6%BF,%20jsp%20%EA%B0%81%EA%B0%81%EC%9D%98%20%EC%97%AD%ED%95%A0%20%EB%B6%84%EB%8B%B4%ED%95%9C%20%EA%B5%AC%EC%A1%B0.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%EC%9E%90%EB%B0%94%EB%B9%88,%20%EC%84%9C%EB%B8%94%EB%A6%BF,%20jsp%20%EA%B0%81%EA%B0%81%EC%9D%98%20%EC%97%AD%ED%95%A0%20%EB%B6%84%EB%8B%B4%ED%95%9C%20%EA%B5%AC%EC%A1%B0.gif?raw=true)

그렇게 되면 위와 같이 서블릿을 통해 생성된 자바 빈 객체에 의해 특정 정보가 담긴 객체가 JSP 페이지에서 사용되어 정보를 출력할 수 있다!

## JSTL; JSP Standard Tag Library

- JSP에서 사용 가능한 표준 태그 라이브러리
- JSP 코드가 보다 더 깔끔하고 가독성이 향상되게 됨!
- 커스텀 태그 :JSP를 작성할 때 자주 사용되는 자바 코드를 웹에서 사용할 수 있는 형태로 만들어진 태그
- 커스텀 태그 라이브러리: 작성한 커스텀 태그를 모아서 압축한 후 이를 배포하서 사용하는 것
- JSTL은 JSP 페이지 안에서 사용할 수 있는 커스텀 태그를 제공하는 라이브러리

### JSTL에서 제공하는 기능

- 간단한 프로그램 로직의 구현(자바에서의 변수 선언, 조건문, 반복문 등에 해당되는 로직)
- 다른 JSP 페이지 호출(<c:redirect>, <c:import>)
- 날짜, 시간, 숫자의 포맷
- JSP 페이지 하나를 가지고 여러 가지 언어의 웹 페이지 생성
- 데이터베이스의 입력, 수정, 삭제, 조회
- XML 문서의 처리
- 문자열을 처리하는 함수 호출

### JSTL의 커스텀 태그

- 위의 JSTL에서 제공되는 기능을 크게
1. core
2. format
3. xml
4. sql
5. functions

로 나누어서 제공

참고하면 좋은 API : [https://docs.oracle.com/cd/E17802_01/products/products/jsp/jstl/1.1/docs/tlddocs/](https://docs.oracle.com/cd/E17802_01/products/products/jsp/jstl/1.1/docs/tlddocs/)

[JSTL의 커스텀 태그](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/JSTL%E1%84%8B%E1%85%B4%20%E1%84%8F%E1%85%A5%E1%84%89%E1%85%B3%E1%84%90%E1%85%A5%E1%86%B7%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%20401a6325db834095abfb5bd0099d88d7.csv)

- JSTL도 커스텀태그이기 때문에 라이브러리가 추가로 필요!

따라서

WEB-INF/lib 

폴더에 관련 jar 파일을 복사해두고 사용!

필요한 jar 파일:
1. jstl.jar
2.standard.jar
▶️ [http://jakarta.apache.org](http://jakarta.apache.org) 에서 다운로드하기!

이 두 파일을 찾아두고 JSTL을 설치해야!

[jstl.jar와 standard.jar의 역할](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/jstl%20jar%E1%84%8B%E1%85%AA%20standard%20jar%E1%84%8B%E1%85%B4%20%E1%84%8B%E1%85%A7%E1%86%A8%E1%84%92%E1%85%A1%E1%86%AF%205e6cba3270c14d14878524b699a08aa6.csv)

### jstl.jar, standard.jar 파일 다운로드 및 JSTL 다운로드&&설치하기

1. [http://jakarta.apache.org/](http://jakarta.apache.org/) 접속하기
2. 왼쪽 메뉴바 중 "Ex-Jakarta" 카테고리에 있는 Taglibs 항목 선택하기
3. 가운데 섹션에 있는 "Apache Taglibs" 부분의 "Apache Standard Taglib" 링크를 누르기
4. 지금은 standard1.1 버전으로 실행할 것이기 때문에 standard 1.1로 다운로드 받기
5. download를 눌러서 나오는 화면의 binaries를 클릭([http://archive.apache.org/dist/jakarta/taglibs/standard/binaries/](http://archive.apache.org/dist/jakarta/taglibs/standard/binaries/))
6. [jakarta-taglibs-standard-1.1.2.zip](http://jakarta-taglibs-standard-1.1.2.zip) 를 다운로드
7. 압축파일의 압축을 풀고, lib 폴더로 이동하면, jstl.jar 파일과 standard.jar 파일이 보인다!
8. 이 두 파일을 적용시킬 웹 애플리케이션(=웹 프로젝트 )의 WEB-INF/lib 폴더에 복사하자!

### JSTL 커스텀 태그를 사용하기 위해서 앞서 살펴봤던 taglib 지시자를 이용하자!

[JSTL 기능 분류에 따른 태그 라이브러리의 URI 식별자와 접두사](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/JSTL%20%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%85%E1%85%B2%E1%84%8B%E1%85%A6%20%E1%84%84%E1%85%A1%E1%84%85%E1%85%B3%E1%86%AB%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%87%E1%85%B3%E1%84%85%E1%85%A5%E1%84%85%E1%85%B5%E1%84%8B%E1%85%B4%20URI%20%E1%84%89%E1%85%B5%E1%86%A8%20d46feaa75dbb4535aa8080ccfef2bd9e.csv)

JSTL 커스텀 태그를 사용하기 위해서는 위의 접두사 및 uri를 참조하여 아래와 같이 외부 라이브러리를 연결해준다!

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>

- URI 속성 값: 여러 종류의 태그 라이브러리 중 사용하려는 기능을 사용하기 위한 식별자 역할!

이렇게 taglib 지시자를 이용해서 외부 라이브러리를 연결시키면 아래와 같이 core 태그 등을 사용할 수 있다!

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:out 태그러 간단한 메시지를 출력해보자</title>
</head>
<body>
	<c:out value="Hello JSTL!"/>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20%EB%A7%9B%EB%B3%B4%EA%B8%B0.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20%EB%A7%9B%EB%B3%B4%EA%B8%B0.gif?raw=true)

### 1. JSTL Core 태그

[JSTL Core 태그](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/JSTL%20Core%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%200e729dc958a14bea9751f3dffb0746af.csv)

1. <c:set> 과 <c:remove>

📌 <c:set>

```java
<c:set var="변수 이름" value="저장할 값" [scope="{page|request|session|application}"]/>
```

```java
<c:set var="변수 이름" value="${저장할 값}" [scope="{page|request|session|application}"]/>
```

```java
<c:set var="변수 이름"  [scope="{page|request|session|application}"]>
	저장할 값
</c:set>
```

[<c:set> 태그의 속성 목록의 사본](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/c%20set%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%20%E1%84%86%E1%85%A9%E1%86%A8%E1%84%85%E1%85%A9%E1%86%A8%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A1%E1%84%87%E1%85%A9%E1%86%AB%2090614d1081f44b9db88407940fffada2.csv)

- <c:set> 은

request.setAttribute("name",value);

와 같은 기능을 수행한다!

⭐ <c:set > vs <jsp:setProperty>

- <c:set> 은 value 속성 값으로 자바 빈 객체 생성자를 넣어주어 보다 간결하게 작성할 수 있다!

```java
<c:set var="member(=자바 빈 객체 이름)" value="<%=com.saeyan.javabeans.MemberBean()%>"/>
```

위와 같이 생성해주게 되면, 아래처럼 값을 전달할 수도 있고

```java
<c:set target="${자바 빈 객체}" property="프로퍼티명">
	저장할 값
</c:set>
```

아래처럼 c:set의 각 속성을 이용하여 한 줄로 표현함으로써 값을 설정해줄 수도 있다!

```java
<c:set target="자바 빈 객체" property="프로퍼티 이름" value="저장할 값"/>
```

비슷해보이지만, 액션태그보다 설정 범위를 지정해줄 수 있다는 점(scope)에서 보다 확장성있는 면모를 보여서 c:set 태그가 보다 나은 선택이라고 할 수 있다!

아래의 예시는 위에서 공부한 내용을 복습해볼 수 있는 좋은 예시이다! 

한번 다시 살펴보도록 하자!

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:set태그로 변수에 값 저장하기</title>
</head>
<body>
	<c:set var="msg" value="Hello"/>
	\${msg }=${msg }<br>
	<c:set var="age">
		26
	</c:set>
	\${age }=${age }<br>
	<%--자바 빈 객체 --%>
	<c:set var="member" value="<%=new com.saeyoen.javabeans.MemberBean()%>"></c:set>
	<c:set target="${member}" property="name" value="전수빈"></c:set>
	<c:set target="${member}" property="id">
		pinksubin
	</c:set>
	\${member }=${member }<hr>
	
	<c:set var="add" value="${10+5}"></c:set>
	\${add }=${add }<br>
	<c:set var="flag" value="${10>5 }"></c:set>
	\${flag }=${flag }<br>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-Core%20Tag_%EC%9E%90%EB%B0%94%EB%B9%88%EA%B0%9D%EC%B2%B4%EB%A5%BC%20%EC%83%9D%EC%84%B1,%20%EC%9D%B4%EC%9A%A9%ED%95%98%EA%B8%B0.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-Core%20Tag_%EC%9E%90%EB%B0%94%EB%B9%88%EA%B0%9D%EC%B2%B4%EB%A5%BC%20%EC%83%9D%EC%84%B1,%20%EC%9D%B4%EC%9A%A9%ED%95%98%EA%B8%B0.gif?raw=true)

🏓 <c:remove>

- JSP의 내장객체.removeAttribute()와 같은 역할!

[USAGE]

```java
<c:remove var="변수이름" [scope="{page|request|session|application}"]>
```

⭐ c:set에서 지정해주었던 var 속성과 scope 속성(기본값: page)과 일치하여 적어주어야 같은 변수로 이해할 수 있다!(브라우저 입장)

---

### 흐름을 제어하는 core 태그

지금 배우게 될 core 태그가 없이 JSP에서 if 분기문을 작성했던 것을 상기해보자!

(떡볶이 , 튀김, 순대 중 하나가 선택되면 그에 맞는 문구를 출력해준다고 상황을 가정해보자!)

```j
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>if</title>
</head>
<body>
	<%
		String choice="떡볶이";
		
		if(choice.equals("떡볶이")){
	%>
			<span>떡볶이 맛있죠!</span><hr>
			<%}else if(choice.equals("튀김")){%>
			<span>튀김은 다음에!</span><hr>
			<%}else if(choice.equals("순대")){ %>
			<span>순대도 다음에!</span><hr>
			<%} %>
</body>
</html>
```

중간에 별도의 처리가 필요하면 스트릿트립을 여러번 이용해야 한다

이런 과정이 다소 구조를 복잡하게 할 수 있다!

core 태그 중 흐름을 제어할 수 있는 태그로 이런 문제들을 해결해보자!

a) <c:if> 태그

```java
<c:if test="조건식">
	조건이 참일 경우 실행할 문장
</c:if>
```

(ex)

```java
<c:if test="${param.color ==1}">
	<span>빨강</span>
<c:if>
```

이번에는 드롭다운메뉴에서 선택을 한 후, 그 선택 값에 따른 화면에서의 스타일링을 다르게 적용해보자

-메뉴를 선택하는 jsp 파일

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSTL Core Tag</title>
</head>
<body>
	<form action="12_colorSelect.jsp" method="post">
		<label>색상을 선택하세요: 
			<select id="color" name="color">
				<option value="1">빨강</option>
				<option value="2">파랑</option>
				<option value="3">초록</option>
			</select>
		</label>
		<input type="submit" value="전송">
	</form>
</body>
</html>
```

-선택된 값에 따른 화면을 처리하는 jsp파일

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSTL Core Tag</title>
<link rel="stylesheet" href="./cif.css">
</head>
<body>
	<c:if test="${param.color==1 }">
	<!-- 빨강 -->
		<span class="red">Red</span>
	</c:if>
	<c:if test="${param.color==2 }">
	<!-- blue -->
		<span class="blue">Blue</span>
	</c:if>
	<c:if test="${param.color==3 }">
	<!-- green -->
		<span class="green">Green</span>
	</c:if>
</body>
</html>
```

```css
@charset "UTF-8";
span{
	color:white;
	font-weight:bold;
}
.red{
	background-color:red;
}
.blue{
	background-color:blue;
}
.green{
	background-color:green;
}
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%5BJSTL%5Dcif_coreTag.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%5BJSTL%5Dcif_coreTag.gif?raw=true)

그러면 core tag의 c:if 태그에 따라서 color 속성값에 따라서 글자 및 배경색을 바꾸어 보여주게 된다!

b) <c:choose> 태그

[USAGE]

```java
<c:choose>
	<c:when test="조건1"> 몸체1 </c:when>
	<c:when test="조건2"> 몸체2 </c:when>
	<c:otherwise>몸체3</c:otherwise> <!--그 어떤 조건에도 만족하지 않는 경우-->
</c:choose>
```

- c:choose 태그는 다중분기문으로 생각해주면 된다! 즉, if-else if-else와 동일한 기능을 한다!

이번에는 간단하게 색상 선택에 따른 문구를 화면에 띄워주는데, c:choose를 이용해서 진행해보자

-선택 양식 폼

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>[JSTL]Core tag-c:choose 다중분기문</title>
</head>
<body>
	<form action="13_fruitSelect.jsp" method="post">
		<label> 과일을 선택하세요: 
			<select name="fruits" id="fruits">
				<option value="1">사과</option>
				<option value="2">메론</option>
				<option value="3">바나나</option>
			</select>
		</label>
		<input type="submit" value="전송">
	</form>
</body>
</html>
```

-선택에 대한 화면 처리를 하는 jsp 파일

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>과일 선택</title>
</head>
<body>
	<c:choose>
		<c:when test="${param.fruits==1 }">
			<span>사과</span>
		</c:when>
		<c:when test="${param.fruits==2 }">
			<span>메론</span>
		</c:when>
		<c:when test="${param.fruits==3 }">
			<span>바나나</span>
		</c:when>
	</c:choose>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%5BJSTL%5DCore%20Tag-cchoose.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%5BJSTL%5DCore%20Tag-cchoose.gif?raw=true)

이 예제에서는 제외되었지만, 이러한 다중분기의 장점은 모든 경우에 해당되지 않을 때, 마지막 c:otherwise를 통해서 실행할 수 있도록 한다는 점이다!

위의 예제에서는 선택한 옵션의 값에 따라서 글자가 바뀌어 나타남을 확인해볼 수 있다

c) <c:forEach>

- 배열이나 컬렉션, Map  등의 집합체에 저장되어 있는 값들을 순차적으로 처리할 때 사용

[USAGE]

```java
<c:forEach [var="변수이름"] items="배열과 같은 집합체">
	몸체
</c:forEach>
```

➕🐻 forEach 태그에 사용될 수 있는 속성을 추가로 알아보자! 🐻

(i) varStatus 속성 : 반복 상태에 관련된 정보를 알려줌

[varStatus 속성의 프로퍼티](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/varStatus%20%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%E1%84%8B%E1%85%B4%20%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%91%E1%85%A5%E1%84%90%E1%85%B5%20929d39736bbc4033bb7bc101a58c4221.csv)

- varStatus 속성을 이용하여 인덱스와 count 를 익혀보자

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:forEach</title>
</head>
<body>
	<%
		String[] movieList={"타이타닉" , "시네마 천국", "혹성 탈출","킹콩"};
		pageContext.setAttribute("movieList", movieList);
	%>
	
<%-- 	<c:forEach var="movie" items="${movieList }">
		${movie }<br>
	</c:forEach> --%>
	<table border="1" style="width:100%; text-align:center;">
		<tr>
			<th> index</th>
			<th> count</th>
			<th> title</th>
		</tr>
		<c:forEach var="movie" items="${movieList }" varStatus="status">
			<tr>
				<td> ${status.index }</td>
				<td> ${status.count }</td>
				<td> ${movie }</td>
			</tr>
		</c:forEach>
	</table>
</body>
</html>

```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%5BJSTL%5Dc_forEach_varStatus_makeTable.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%5BJSTL%5Dc_forEach_varStatus_makeTable.PNG?raw=true)

위의 내용을 확인해보자면, varStatus 속성의 명칭인 status라 forEach를 돌 때마다, 상태가 갱신되면서, 그에 따른 인덱스는 0부터 천천히, count는 1부터 천천히 증가한다. 그리고 forEach 태그에서 정의되었던 var 속성에 자동으로 반복자에 따른 값이 들어가게 되어 위와 같이 영화 제목이 천천히 1개씩 출력된다

(2) begin 속성 && end 속성 && step 속성- 짝궁!!

[begin, end, step 속성](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/begin,%20end,%20step%20%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%206b1566a2589140f6a6063d01324d22f1.csv)

```java
<c:forEach [var="변수 이름"] begin="시작 값" end="끝 값" [step="증가치"]>
	몸체
</c:forEach>
```

d) <c:forTokens>

- java.util.StringTokenizer 와 같이 문자열을 구분자로 분리해서 하나씩 추출 가능

[USAGE]

```java
<c:forTokens var="토큰을 저장할 변수" items="토큰으로 나눌 문자열" delims="구분자">

	몸체
</c:forTokens>
```

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:forTokens</title>
</head>
<body>
	<ul>
	<c:forTokens var="tokens" items="서울,인천,대전,대구,광주,부산" delims=",">
		<li>${tokens }</li>
	</c:forTokens>
	</ul>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%5BJSTL%5Dc_forTokens.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/%5BJSTL%5Dc_forTokens.PNG?raw=true)

그리고 위의 경우를 <c:set> 을 이용하여 아래와 같이 page 내부에서 변수를 설정해주고 token 구별으로 사용할 수도 있는데, 이 때 c:set 대신 스크립트릿을 이용하면 적용 유효범위 등으로 인해서 단위체로 연결되는 items 속성의 값으로 전달될 때 문제가 있을 수 있다!

[https://stackoverflow.com/questions/19807156/accessing-a-jstl-el-variable-inside-a-scriptlet#:~:text=2 Answers&text=JSTL variables are actually attributes,(usually pageContext and request](https://stackoverflow.com/questions/19807156/accessing-a-jstl-el-variable-inside-a-scriptlet#:~:text=2%20Answers&text=JSTL%20variables%20are%20actually%20attributes,(usually%20pageContext%20and%20request)).

- ⭐스크립트릿은 언어가 자바로 된 스크립트 언어로 해석되어 일종의 명령문 파편에 불과하기 때문에 JSTL에 사용되는 변수에 접근하려고 한다면 잠깐 언급한 바와 같이 어떤 결과도 확인할 수 없다!⭐

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:forTokens</title>
</head>
<body>
	<c:set var="str" value="서울,인천,대전,대구,광주,부산"></c:set>
	<ul>
	<c:forTokens var="tokens" items="${str}" delims=",">
		<li>${tokens }</li>
	</c:forTokens>
	</ul>
</body>
</html>
```

d) <c:import>

- 다른 페이지의 내용을 포함시키기 위함
- <jsp:include> 액션태그처럼 여러 페이지 내부에 들어갈 일부분을 모듈화해둔 후 삽입할 때 사용됨!

<c:import url="URL" [var="변수 이름"] [scope="영역"] [charEncoding="charEncoding"]>
</c:import>

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:import</title>
</head>
<body>
	<c:import url="http://localhost:8081/web-study-07-EL_JSTL/EL.jsp"
		var="data" scope="page" charEncoding="UTF-8"></c:import>
	${data }
</body>
</html>
```

이때, scope에는 page, request, session, application가 올 수 있다

그리고 **charEncoding의 속성값은 import 대상 및 import 되는 위치의 인코딩을 고려하여 값을 넣어주어야 한다!**

e) <c:param> 태그

- <c:import> 혹은 <c:url> 태그의 URL에 파라미터를 전달하기 위해 사용됨
- 열린태그-닫힌태그(value 속성을 열린태그와 닫히는 태그 사이에서 사용 가능), 빈 태그 형식으로 모두 사용 가능

(i) <c:import>와 사용

```java
<c:import url="URL" [var="변수 이름"] [scope="영역"] [charEncoding="charEncoding"]>
	<c:param name="URL에 설정할 파라미터의 이름" value="URL에 설정할 파라미터의 값"/>
</c:import>
```

🐻 c:import 및 c:param으로 모듈을 부착시킬 화면 19_jstlUrl.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:import</title>
</head>
<body>
	<c:import url="http://localhost:8081/web-study-07-EL_JSTL/cParam.jsp">
		<c:param name="title" value="hello to test c:param!"/>	
	</c:import>
</body>
</html>
```

🐻 데이터를 받고 출력할 화면 cParam.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSTL c:param</title>
</head>
<body>
	\${param.title } : ${param.title}
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL_core_param%20tag.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL_core_param%20tag.PNG?raw=true)

c:param 태그

(ii) <c:url> 태그와 함께 사용하기

🐻데이터를 띄워주는 화면 cParams2.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSTL c:param</title>
</head>
<body>
	\${param.checker} : ${param.checker}
</body>
</html>
```

🐻데이터를 출력해주는 화면을 링크로 연결해줄 화면  20_jstlUrl_sec.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:url</title>
</head>
<body>
	<c:url var="urlEx" value="http://localhost:8081/web-study-07-EL_JSTL/cParam2.jsp" scope="application">
		<c:param name="checker" value="확인하였습니다 in 20_jstlUrl_sec(from cParam2)"/>
	</c:url>
	<a href="${urlEx }">이동하기</a>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-core%20url%20&&%20core%20params.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-core%20url%20&&%20core%20params.gif?raw=true)

사용방법은 비슷한데, 위의 경우에는 a 링크를 통해서 cParam2.jsp로 연결하여 결과값을 확인할 수 있다

---

또 다른 방법으로, import 태그 내부에서 변수를 이용해서 작동될 수 있도록 c:url 태그를 먼저 생성해주는 방법을 택해보자!

잠깐 환기시켜보면,

1) 첫번째 import와 param만 이용한 예제에서는, 하나의 페이지에서 import를 사용하기 이전에 url을 담을 변수에 대한 준비가 없었기 때문에, url을 바로 넘겨주었다. 그리고 데이터를 넘겨받은 페이지에서 param.데이터필드를 통해서 값을 출력하는 모습을 19_jstlUrl.jsp 화면에서 화면이동없이 확인할 수 있었다

2) 두번째에서는 url 태그를 이용해서 url 을 담은 변수를 저장해두고, 링크로 이동할 수 있도록 해준 후, 링크를 클릭했을 때 데이터를 확인할 수 있도록 하였다

3) 이번 마지막 예제에서는 url을 담을 변수를 url 태그에서 먼저 설정하면서, parameter를 전달해준다. 그 이후 import 태그로 화면에 파라미터를 출력해주는 cParams3.jsp 화면을 화면이동없이 연결해준다.

🐻 데이터를 보여주는 cParams3.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSTL c:param</title>
</head>
<body>
	<h3>21_jstlUrl에서 확인하는 cParam3.jsp의 내용!</h3>
	\${param.title} : ${param.title}<br>
	\${param.checker} : ${param.checker}
</body>
</html>
```

🐻 모듈화된 화면을 합하여 보여주는 21_jstlURL.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:url 태그와 c:params 이용하기</title>
</head>
<body>
	<c:url value="http://localhost:8081/web-study-07-EL_JSTL/cParam3.jsp" var="url">
		<c:param name="title" value="url에서 변수도 지정해주고, url을 미리 저장해주자"/>
		<c:param name="checker" value="cParam3의 내용을 import 하기 위한 밑작업!!"/>
	</c:url>
	<c:import url="${url }"></c:import>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-Core_params_using_url_import.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-Core_params_using_url_import.PNG?raw=true)

f) <c:url>태그

- **여러 번 반복되어 사용할 주소를 변수에 저장**하기 위해 사용됨

```java
<c:url value="URL" [var="변수이름"] [scope="영역"]>
</c:url>
```

url 태그를 이용한다면, 아래와 같이, url을 변수에 저장해두고, 링크를 누르면 연결되도록 해줄 수도 있다!(위에서 param예제에서 해보았던 것처럼 말이다!)

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSTL-c:url 태그</title>
</head>
<body>
	<c:url var="myURL" value="http://www.naver.com">
	</c:url>
	<a href="${myURL }">네이버로 이동해보실까요?</a>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20url%20%ED%83%9C%EA%B7%B8.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20url%20%ED%83%9C%EA%B7%B8.gif?raw=true)

또한 변수에 URL 주소가 저장되기 때문에, URL 주소를 확인해보거나, 이미지 등의 멀티미디어를 확인해볼 수도 있다! 

⚠️ base로 공통 경로나 기준이 되는 경로 지정해둔 후, body에서 상대경로로 접근하였다면, 변수에는 상대경로가 저장되어 있을 것이다! 그 이유는 base에 기준경로가 저장되어 있기 때문이다!

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<base href="https://cdn.econovill.com/news/photo/">
<meta charset="UTF-8">
<title>JSTL-c:url 태그</title>
</head>
<body>
	<c:url var="myURL" value="./202104/528277_435746_3854.jpg">
	</c:url>
	<h6>아래는 변수에 저장된 URL 주소입니다</h6>
	<p>${myURL }</p>
	<h6>아래는 변수에 저장된 URL 주소를 img 태그를 이용해서 이미지를 추출해온 것입니다</h6>
	<img src="${myURL }"width="300px">
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-core%20url%20%ED%83%9C%EA%B7%B8%EB%A1%9C%20%EC%9D%B4%EB%AF%B8%EC%A7%80%20%EB%B0%8F%20url%20%EC%A3%BC%EC%86%8C%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-core%20url%20%ED%83%9C%EA%B7%B8%EB%A1%9C%20%EC%9D%B4%EB%AF%B8%EC%A7%80%20%EB%B0%8F%20url%20%EC%A3%BC%EC%86%8C%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.gif?raw=true)

g) <c:redirect> 태그

- response.sendRedirect()와 동일한 기능을 제공
- 지정 페이지로 이동하는데, 코드를 보다 간결하게 기술할 수 있음!

[USAGE]

```java
<c:redirect url="URL" [context="경로명"]>
</c:redirect>
```

- context는 다른 컨텍스트 패스로 (=프로젝트) 이동하고 싶을 경우 이용

예제 ) url 값을 통하여 네이버로 리다이렉트하기

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>[JSTL] Core Tag- c:redirect tag</title>
</head>
<body>
	<c:redirect url="http://www.naver.com"></c:redirect>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20core%20tag-c%20redirect%20tag.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20core%20tag-c%20redirect%20tag.gif?raw=true)

위와 같이, redirect 태그 내부의 url 속성의 값으로 이동할 외부 경로 혹은 같은 프로젝트 내의 파일명을 적어주면 자동으로 리다이렉트될 수 있다!

⭐ <c:redirect>도 url을 이용하고 있기 때문에, c:param 태그를 사용할 수 있다

[usage]

```java
<c:redirect url="연결할 페이지 url">
		<c:param name="전달할 프로퍼티명" value="전달할 프로퍼티값"/>
</c:redirect>
```

-리다이렉트할 메인 jsp 페이지23_jstlRedirect.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>[JSTL] Core Tag- c:redirect tag</title>
</head>
<body>
<%-- 	<c:redirect url="http://www.naver.com"></c:redirect> --%>
	<c:redirect url="23-2_cParam.jsp">
		<c:param name="check" value="잘 넘어왔습니다!"/>
	</c:redirect>
</body>
</html>
```

-리다이렉트될 서브 jsp 페이지 23-2_cParam.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>c:redirect &amp; c:param</title>
</head>
<body>
	<h3>23_jstlRedirect.jsp에서 23-2_cParam.jsp로 넘어왔습니다</h3>
	<pre>\${param.check }:${param.check }</pre>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20redirect%20and%20param-core%20tag.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20redirect%20and%20param-core%20tag.gif?raw=true)

잘 살펴본다면, a 태그를 이용하였던 url 태그와 param 태그를 이용한 경우와 다른 점이라면, 사용자가 별도의 이벤트를 발생시키지 않더라도, 자동으로 리다이렉트되어 데이터를 넘겨받아 출력하는 페이지를 보여준다는 점이다!

h) <c:out> 태그

- value 속성에 지정한 문자열이나 변수의 내용을 출력
- JSP의 expression 태그<%= %>와 동일한 기능을 수행

[USAGE]

<c:out value="value" [default="기본값"]/>

[c:out 태그의 속성](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/c%20out%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%20dce817e5e0244c79bbee516216493408.csv)

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>변수나 문자열을 출력해주는 c:out 태그</title>
</head>
<body>
	<c:set var="variable01" value="변수01"/>
	<c:out value="${variable01}" default="10"/>
	<c:out value="variable01" default="10"/>
  <c:out value="${null }" default="10"/>
</body>
</html>
```

위와 같은 경우, 

	<c:out value="${variable01}" default="10"/>

은 EL을 통하여 변수 variable01에 저장된 값이 있다면 그 값을, 아니라면 기본값 10을 출력해줄 것인데, 위의 경우 "변수01"이 출력된다

<c:out value="variable01" default="10"/>

그리고 두번째 케이스인 경우는 ""로써 문자열값임을 나타내고 있다. 사실상 value에 문자열 값을 넣어준 셈이라서 default는 무의미하다🙂

결론적으로, 이 경우는 variable01이라는 문자열이 출력된다

<c:out value="${null }" default="10"/>

그리고 마지막은 null값이 들어간 것이라서 값이 없으므로 기본값인 10이 들어가서 출력된다

⚠️위의 경우와 합하여 정리하자면,  만약 variable01 변수가 존재하지 않아도(undefined) && 값이 없어도(null) 기본값이 지정된다!

- 더불어, default값을 <c:out> 태그 사이에 넣어줄 수도 있다!

```java
<c:out value="${age}>
 기본값
</c:out>
```

i) <c:catch> 태그

- 예외처리를 수행하는 태그
- 변수로 지정한 공간에 예외 메시지가 저장됨

[USAGE]

```java
<c:catch var="변수이름">
	예외가 발생할 수 있는 코드
</c:catch>
```

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSTL Core Tag- c:catch tag</title>
</head>
<body>
	<c:set var="age" value="30"/>
	나이:<c:out value="${age }">10</c:out><!-- 30 -->
	
	<c:remove var="age" scope="page"></c:remove>
	나이:<c:out value="${age }">10</c:out><!-- 10 -->
	
	<c:catch var="errMsg">
		예외발생전<br>
		<%=1/0 %><br>
		예외발생후<br>
	</c:catch>
	
	<c:out value="${errMsg}"></c:out>
</body>
</html>
```

즉, 위와 같이 변수에 에러를 저장해두고, c:out으로 출력하게 되면 아래와 같이 ArithmeticException의 메시지가 표시된다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-core%20catch.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-core%20catch.PNG?raw=true)

---

## JSTL-fmt 태그

[JSTL fmt 태그](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/JSTL%20fmt%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%20782265799a3742599557c6720e480f66.csv)

🌺 JSTL fmt 태그 uri

▶️ http://java.sun.com/jstl.fmt

🌺 JSTL fmt 태그 prefix

▶️ fmt

### 1.<fmt:formatNumber>

[USAGE]

```java
<fmt:formatNumber value="수치 데이터"
									[type="{number|currency|percent}"]
									[pattern="패턴"]
									[currencySymbol="화폐단위"]
									[groupingUsed="{true|false}"]
									[var="변수이름"]
									[scope="{page|request|session|application}"]>	
```

[fmt:formatNumber 태그의 속성](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/fmt%20formatNumber%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%20e94d210defd24f2d9af4682f93a17e34.csv)

예제로 빠르게 이해해보자!

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jstl/fmt" prefix="fmt" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>fmt 태그들</title>
</head>
<body>
	<p>fmt:formatNumber 의 type 기본값은 number</p>
	<p>수치 데이터에 대한 groupingUsed=true(기본값)</p>
	<h3>pattern: #,#00.0#은 소수점 이하 2자리 까지 표시할 수 있도록!</h3>
	<fmt:formatNumber value="12345678.12545"
					  pattern="#,#00.0#"/><!-- 12,345,678.13 -->
	<h4>패턴에서 지정한 소수점 자릿수보다 주어진 숫자의 소수점 자릿수가 작으면 공백으로 채워짐!</h4>
	<fmt:formatNumber value="12345678.1"
					  pattern="#,#00.0#"/><!-- 12,345,678.1 -->
	<h3>pattern: .0#은 소수점 이하 2자리 까지 표시할 수 있도록! 결론적으로는 첫 번째 예시와 동일!</h3>
	<fmt:formatNumber value="12345678.12545"
					  pattern=".0#"/><!-- 12,345,678.13 -->
	<h3>pattern: .000은 소수점 이하 3자리 까지 표시할 수 있도록 하지만, 
	주어진 숫자의 소수점 자릿수가 3자리보다 작으면 0으로 채워짐</h3>
	<fmt:formatNumber value="12345678.3"
					  pattern=".000"/><!-- 12345678.300 -->
	<hr>
	<p>[groupingUsed=false]</p>
	<fmt:formatNumber value="12345678.12545"
					  pattern="#,#00.0#"
					  groupingUsed="false"/><!-- 12345678.13 -->
	<hr>
	<p>type=currency</p>
	<fmt:formatNumber value="1200000" type="currency" /><!-- ￦1,200,000 -->
	<fmt:formatNumber value="1200000" type="currency" currencySymbol="$" /><!--$1,200,000 -->
	<hr>
	<p>type=percent</p>
	<fmt:formatNumber value="0.5" type="percent"/><!-- 50% -->
</body>
</html>
```

🌺 "#,#00.0#"은 소수점 두 번째 자리까지 표시하는 것을 의미하는 패턴인데, 주어진 값이 소수점 자릿수가 2자리보다 크다면 반올림되어 나타난다

🌺 ".0#"은 앞에서 언급했던 "#,#00.0#"와 같은 결과를 내는 패턴이다

🌺 ".000"은 소수점 3자리까지 나타내되, 남는 자리는 0으로 채우도록 하는 패턴이다!'

🌺 percent 백분율은 0이상, 1 이하의 실수값을 넣어주어야 한다!

🌺 통화관련해서는, 한국 기준 기본값이 "원"기호가 표시된다! 이를 바꾸려면 currencySymbol 속성을 $등 사용하려는 기호값으로 바꾸어 넣어주어야 한다!

위와 같은 사항을 뒷받침해보았을 때, 위의 예제의 결과는 아래처럼 확인될 수 있다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20fmt%20tag.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20fmt%20tag.PNG?raw=true)

<fmt:formatNumber>

### 2. <fmt:formatDate>

- 날짜와 시각에 대한 형식을 표현하는 태그

[USAGE]

```java
<fmt:formatDate value="date"
		 [type="{time|date|both}"]
		 [dateStyle="{default|short|medium|long|full}"]
		 [timeeStyle="{default|short|medium|long|full}"]
		 [pattern="customPattern"]
     [timeZone="timeZone"]
 		[var="varName"]
	  [scope="{page|request|session|application}"]>
```

[<fmt:formatDate> 태그의 속성](%E1%84%91%E1%85%AD%E1%84%92%E1%85%A7%E1%86%AB%20%E1%84%8B%E1%85%A5%E1%86%AB%E1%84%8B%E1%85%A5(EL)%E1%84%8B%E1%85%AA%20JSTL%20209733a181774076bc26eac796aceaec/fmt%20formatDate%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%204b16d3dd653f47ea803ed8e6ae043918.csv)

예제를 통해서 각각에 대해서 살펴보자

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ page import="java.util.Date" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>fmt:formatDate</title>
</head>
<body>
	<%--java.util.Date 객체 생성해야 함! --%>
	<c:set var="cur" value="<%=new Date() %>"/>
	\${cur }:${cur }<br><%--${cur }:Thu May 13 14:16:59 KST 2021 --%>
	<fmt:formatDate value="${cur}"></fmt:formatDate><%--2021. 5. 13 --%>
	
	<hr>
	<p>type-date</p>
	<fmt:formatDate value="${cur }" type="date"></fmt:formatDate><!-- 2021. 5. 13 -->
	<hr>
	<p>type-time</p>
	<fmt:formatDate value="${cur }" type="time"></fmt:formatDate><!-- 오후 2:19:50 -->
	<hr>
	<p>type-both</p>
	<fmt:formatDate value="${cur }" type="both"></fmt:formatDate><!-- 2021. 5. 13 오후 2:20:25 -->
	<br>
	<hr>
	<p>dateStyle-default</p>
	<fmt:formatDate value="${cur }" dateStyle="default"></fmt:formatDate><!-- 2021. 5. 13 -->
	<hr>
	<p>dateStyle-short</p>
	<fmt:formatDate value="${cur }" dateStyle="short"></fmt:formatDate><!-- 21. 5. 13 -->
		<hr>
	<p>dateStyle-medium</p>
	<fmt:formatDate value="${cur }" dateStyle="medium"></fmt:formatDate><!-- 2021. 5. 13 -->
	<hr>
	<p>dateStyle-long</p>
	<fmt:formatDate value="${cur }" dateStyle="long"></fmt:formatDate><!-- 2021년 5월 13일 (목) -->
	<hr>
	<p>dateStyle-full</p>
	<fmt:formatDate value="${cur }" dateStyle="full"></fmt:formatDate><!-- 2021년 5월 13일 목요일 -->
	
	<br>
	<hr>
	<p>timeStyle-default</p>
	<fmt:formatDate value="${cur }" type="time" timeStyle="default"></fmt:formatDate><!-- 오후 2:27:41 -->
	<hr>
	<p>timeStyle-short</p>
	<fmt:formatDate value="${cur }" type="time" timeStyle="short"></fmt:formatDate><!-- 오후 2:27 -->
	<hr>
	<p>timeStyle-medium</p>
	<fmt:formatDate value="${cur }" type="time" timeStyle="medium"></fmt:formatDate><!-- 오후 2:32:00 -->
	<hr>
	<p>timeStyle-long</p>
	<fmt:formatDate value="${cur }" type="time" timeStyle="long"></fmt:formatDate><!-- 오후 2시 27분 41초 -->
	<hr>
	<p>timeStyle-full</p>
	<fmt:formatDate value="${cur }" type="time" timeStyle="full"></fmt:formatDate><!-- 오후 2시 27분 41초 KST -->
	
	<br>
	<hr>
	<p>날짜를 커스텀 형식으로 나타내기</p>
	<fmt:formatDate value="${cur }" type="both" pattern="YYYY년 MM월 dd일  aa hh시 mm분"></fmt:formatDate><!-- 2021년 05월 13일 오후 02시 31분 aa는 오전오후 -->
	
</body>
</html>
```

위의 예제를 통한 결과를 같이 확인하면서 이해하고 넘어가보자!

먼저 value 속성 및 type 속성을 설정한 결과부터 살펴보자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-fmt%20formatDate%20type%20attr.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-fmt%20formatDate%20type%20attr.PNG?raw=true)

가장 상단이 value 속성 이외의 어떤 속성을 설정해두지 않은 상태이다

🐨먼저 가장 중요한 것은 formatDate 태그를 사용하기 전에는 **Date 객체를 만들어두어야** 한다!

본론으로 돌아와서, value 속성만으로 해당 date 객체를 확인해보면, 요일-월-일 시:분:초 타임존 연도

순서로 출력됨을 확인해볼 수 있다!

그리고 **type 값**을 잘 살펴보면,

(1) date : 연도 네자리. 월. 일

(2) time : 오전/오후(자바에서의 date 형식에서는 aa 와 매칭됨) 시:분:초

(3) both: 연도 네자리. 월. 일 오전/오후 시:분:초

를 반환함을 확인해볼 수 있다!

다음은 dateStyle을 살펴보자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-fmt%20formatDate%20dateStyle%20attr.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-fmt%20formatDate%20dateStyle%20attr.PNG?raw=true)

위의 결과와 함께 살펴보면 

🐨 dateStyle의 default 는 medium 결과와 같다는 사실을 우선 살펴볼 수 있다!

그리고 차분차분하게 각각의 속성을 다같이 정리해보면,

(1) dateStyle=default 혹은 medium : 연도 네자리. 월. 일

(2)dateStyle=short: 연도 두자리.월.일

(3)dateStyle=long : "연도 네자리"년 "월"월 "일"일 (요일 약어-예:월)

(4)dateStyle=full : "연도 네자리"년 "월"월 "일"일 (요일 fullname-예:월요일)

과 같은 값을 상황별로 반환받을 수 있음을 확인해볼 수 있다!

이어서, timeStyle 속성을 살펴보자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-fmt%20formatDate%20timeStyle%20attr.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL-fmt%20formatDate%20timeStyle%20attr.PNG?raw=true)

🐨 timeStyle도 default 값과 medium 값에 대한 반환값이 같다!

timeStyle의 각 속성값에 대한 반환값은 위의 결과를 바탕으로 하면, 아래와 같이 정리해볼 수 있다!

(1) timeStyle=default 혹은 medium: 오전/오후 시:분:초

(2) timeStyle=short: 오전/오후 시:분

(3)timeStyle=long: 오전/오후 몇 시 몇 분 몇 초

(4)timeStyle=full: 오전/오후 몇 시 몇 분 몇 초 Timezone

### 3. <fmt:setTimeZone>, <fmt:timeZone>

<fmt:setTimeZone>

- 특정 지역의 타임존 설정

🌻 타임존: 같은 시간을 사용하는 지역을 묶어서 나누어 놓은 것(ROK: +09:00)

[USAGE]

```java
<fmt:setTimeZone value="timezone"
								 [var="varName"]
								 [scope="{page|request|session|application}"]>
```

<fmt:timeZone>

- 타임존을 부분 적용

[USAGE]

```java
<fmt:timeZone value="timezone">
몸체
</fmt:timeZone>
```

두 타임존 태그는 동일한 결과를 확인해볼 수 있는데, 아래의 예제와 함께 확인해보자

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>타임존 설정하기</title>
</head>
<body>
	<%--자바 빈 객체를 생성하는 방식을 이용해서 Date 객체 만들기 --%>
	<jsp:useBean id="now" class="java.util.Date"></jsp:useBean>
	
	<pre>
	default:<c:out value="${now }"></c:out>
	Korea KST: <fmt:formatDate value="${now }" type="both" dateStyle="full"
				timeStyle="full"></fmt:formatDate>
	</pre>
	<p>이제 그리니치 표준 시간을 timeZone 태그를 이용하여 적용해보기</p>
	<pre>
	<fmt:timeZone value="GMT+9">
	Korea GMT: <fmt:formatDate value="${now }" type="both" dateStyle="full"
				timeStyle="full"></fmt:formatDate>
	</fmt:timeZone>
	<fmt:timeZone value="GMT+2">
	Swiss GMT: <fmt:formatDate value="${now }" type="both" dateStyle="full"
				timeStyle="full"></fmt:formatDate>
	</fmt:timeZone>
	<fmt:timeZone value="GMT-4">
	NewYork GMT: <fmt:formatDate value="${now }" type="both" dateStyle="full"
				timeStyle="full"></fmt:formatDate>
	</fmt:timeZone>
	</pre>
	<hr>
	<p>timeZone을 바꾸기</p>
	<fmt:setTimeZone value="GMT+2"/>
	ChangedTime KST : <fmt:formatDate value="${now }" type="both" dateStyle="full"
				timeStyle="full"></fmt:formatDate>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20fmt%20timeZone%20tag.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20fmt%20timeZone%20tag.PNG?raw=true)

🍑 먼저,  한 가지 중요한 사실은 Date 객체가 java.util 패키지 내부에 있는 클래스에서 만들어지기 때문에 "클래스"를 이용한다는 점에서 자바 빈 객체로 만들어서 공유할 수 있다는 점이다!

<jsp:useBean id="now" class="java.util.Date"></jsp:useBean>

이번에는 timeZone 태그를 이용했을 때의 특징을 정리해보자

[타임존을 사용하지 않았을 때]

1) 생성된 날짜 객체를 c:out 태그를 이용한다면, 영문으로 "요일약자  월 일 시:분:초 KST 네자리연도"가 출력될 것

2) fmt:formatDate 객체로 출력한다면 위에서 살펴본 특성에 따라 출력될 것

[타임존을 사용했을 때]

3) 같은 시간 대역을 사용하는 타임존에 대해서 그리니치 시간 GMT를 이용하면 해당 지역에 맞추어서

네자리 연도 년    몇 월     몇 일 몇요일 오전/오후 몇시 몇분 몇초 GMT+xx.xx

를 반환한다! 이는 setTimeZone 태그도 마찬가지다!

### 4. <fmt:setLocale>

- 로케일 지정은 참고로, requestEncoding 태그도 가능하다!
- 통화 및 날짜 등의 사용이 국가마다 다르기 때문에 이를 맞춰줄 수 있는 기능!
- 다국어 페이지를 만들 경우, 사용하면 유용!

[USAGE]

```java
<fmt:setLocale value="locale">
```

locale은 언어코드_국가코드를 적어주면 된다!(예: 한국 ko_kr)

*언어코드: [ISO639-1코드](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)

*국가코드: [https://ko.wikipedia.org/wiki/국가별_국가_코드_목록](https://ko.wikipedia.org/wiki/%EA%B5%AD%EA%B0%80%EB%B3%84_%EA%B5%AD%EA%B0%80_%EC%BD%94%EB%93%9C_%EB%AA%A9%EB%A1%9D)

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>로케일 지정하기</title>
</head>
<body>
	<jsp:useBean id="now" class="java.util.Date"></jsp:useBean>
	<pre>
		1. 톰캣 서버의 기본 로케일: <%=response.getLocale() %>
		<fmt:setLocale value="ko_KR"></fmt:setLocale>
		2-1. 로케일을 한국어로 설정한 후 로케일 확인: <%=response.getLocale() %>
		<fmt:setLocale value="en_USA"></fmt:setLocale>
		2-2. 로케일을 영어, 미국로 설정한 후 로케일 확인: <%=response.getLocale() %>
		<fmt:setLocale value="fr_FRA"></fmt:setLocale>
		2-3. 로케일을 불어, 프랑스로 설정한 후 로케일 확인: <%=response.getLocale() %>
		
	</pre>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20fmt%20tag-setLocale.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/JSTL%20fmt%20tag-setLocale.PNG?raw=true)

위와 같이 언어코드_국가코드로 로케일을 변경할 수 있고 이를 response 내장객체의 getLocale 메서드로 확인해보면 위와 같이 언어코드_국가코드로 설정한 부분을 확인해볼 수 있다!

### 5. <fmt:requestEncoding>

- post 방식으로 넘어오는 페이지에서 한글 데이터가 깨지지 않도록 인코딩을 설정해주는 역할!

request.setCharacterEncoding("인코딩방식");

과 같은 기능!

[USAGE]

<fmt:requestEncoding value="charsetName">

사용하는 간단한 예시를 확인해보자

-이름을 .post방식으로 전달받고, 출력하는 예시이다

(1) 이름을 입력받는 페이지 30_Info.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>post방식에서 인코딩 깨짐 방지</title>
</head>
<body>
	<form method="post" action="30_jstlFmt.jsp">
		<label>이름: 
			<input type="text" name="name">
		</label>
		<input type="submit" value="전송">
	</form>
</body>
</html>
```

(2) 입력받은 이름을 출력해주는 페이지 30_jstlFmt.jsp

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSTL &amp; EL</title>
</head>
<body>
	<%--set encoding for post transfer method --%>
	<fmt:requestEncoding value="UTF-8"></fmt:requestEncoding>
	<div>이름: <%=request.getParameter("name") %></div>
</body>
</html>
```

만약 이 웹 애플리케이션에서 "홍길동"이라는 입력을 입력한다면, 30_jstlFmt.jsp 페이지로 넘어가서 "이름: 홍길동" 을 보여줄 것이다!
