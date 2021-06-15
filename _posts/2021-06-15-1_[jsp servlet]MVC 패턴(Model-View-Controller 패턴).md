# MVC 패턴(Model-View-Controller 패턴)

❓ 디자인 패턴 ❓

- 객체 지향 프로그래밍 설계를 할 때 자주 발생하는 문제들을 피하기 위해 사용되는 패턴

MVC 패턴은 웹 애플리케이션의 로직 설계를 비즈니스 로직과 프리젠테이션 로직, 이 둘 간 작업을 중제하는 컨트롤러 각각의 역할을 명시해주는 패턴이다!

하지만, 초창기 이용되었던 패턴1은 컨트롤러와 뷰가 분리되어 있지 않았다

먼저 이에 앞서서 애플리케이션을 구성하는 로직을 잠깐 설명하자면,

웹 애플리케이션을 작성하기 위해서는 

1. 비즈니스 로직 Business Logic
- 실제 업무를 처리
2. 프리젠테이션 로직 Presentation Logic
- 화면을 처리하기 위한 부분(html, jsp,...)

와 같은 로직을 구현해야만 한다!

MVC 패턴은 이를 위해서 각각의 역할을 모델, 컨트롤러, 뷰로 나누어 구현한다

각각은

[MVC 패턴 구성 요소](https://www.notion.so/cbd9129d68c948e3aad2f6a47bc312d1)

위와 같이 각각의 역할을 가질 수 있다.

하지만 앞서 말했듯,

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/MVC%ED%8C%A8%ED%84%B41.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/MVC%ED%8C%A8%ED%84%B41.png?raw=true)

MVC 패턴 1

MVC 패턴 1은 컨트롤러와 뷰가 분리되지 않은 형태로 구성되어 있었다. 이러한 패턴 1의 특징은 아래와 같이 양날의 검과 같다

🌻 MVC 패턴1 의 특징 🌻

1. 장점
- 고도의 스킬을 요하지 않음
- 직관적인 코드 ▶️ 생산성 측면에서 매우 효율적
2. 단점
- 개발자와 디자이너 간 협업 시 혼란 초래

이러한 MVC 패턴1의 단점을 개선하고자 MVC 패턴2가 등장하였는데,

패턴2는 컨트롤러와 뷰의 경계가 명확해졌다고 먼저 받아들이면 좋을 것 같다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/MVC%ED%8C%A8%ED%84%B42.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/MVC%ED%8C%A8%ED%84%B42.png?raw=true)

MVC 패턴 2

위는 MVC 패턴 2를 나타낸 흐름도인데, 아까 보았던 패턴 1보다 각자 맡은 역할이 보다 명확해지면서 프리젠테이션 로직과 비즈니스 로직이 분리된 모습을 보인다

- 클라이언트가 요청을 하게 되면, 컨트롤러(주로 서블릿)은 이에 해당되는 요청을 비즈니스 계층(혹은 이 계층이 없고 모델에 단지 퍼시스턴스 계층만 존재할 수도 있다)에게 DTO 객체를 통해서 알맞은 요청을 한다
- 비즈니스 계층은 전달받은 요청을 퍼시스턴스 계층에게 전달하는데, 실질적인 일은 퍼시스턴스 계층이 수행한다
- 퍼시스턴스 계층은 DB를 이용하여 일련의 작업을 수행하고, 그 결과를 비즈니스 계층에게 전달한다
- 비즈니스 계층은 DTO 객체를 통해서 컨트롤러에게 결과를 반환해준다
- 컨트롤러는 그 결과를 뷰에게 전달하면 , 뷰는 알맞은 페이지로 클라이언트에게 응답한다

🌺 이때, 비즈니스 계층과 퍼시스턴스 계층을 구현한다면, 인터페이스로 기능을 기술해둔 후, 이를 구현(implements)하는 클래스로 만들어서 사용하면 기능 관리, 수정에 매우 용이하다!

🌟 퍼시스턴스 계층 : DB와 접속하여 CRUD 작업을 수행하는 계층

패턴 2는 장점도 분명하지만, 단점도 존재한다

🌻 .MVC 패턴2의 특징 🌻

1. 장점
- 재사용성 향상
- 가독성   향상
- 클라이언트 요청 처리, 응답 처리, 비즈니스 로직 처리 부분을 모듈화 시킴으로써(처리 작업 분리) 유지보수 & 확장 용이, 개발자와 디자이너 간 역할 및 책임 구분이 명확해짐
2. 단점
- MVC 구조에 대한 이해가 필요
- 개발자의 높은 스킬이 요구됨

🌟 컨트롤러를 위한 패턴- Front Controller 패턴, Command 패턴

- 컨트롤러 계층을 위한 패턴은 다양한 패턴이 존재하지만, 그 중에서 Front Controller 패턴과 Command 패턴을 공부해보자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/%5B%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%20%EA%B3%84%EC%B8%B5%20%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4%5DFront%20Controller%20%ED%8C%A8%ED%84%B4%20vs%20%EC%BB%A4%EB%A7%A8%EB%93%9C%20%ED%8C%A8%ED%84%B4.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/%5B%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%20%EA%B3%84%EC%B8%B5%20%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4%5DFront%20Controller%20%ED%8C%A8%ED%84%B4%20vs%20%EC%BB%A4%EB%A7%A8%EB%93%9C%20%ED%8C%A8%ED%84%B4.png?raw=true)

컨트롤러 계층 디자인패턴- Front Controller 패턴, 커맨드 패턴

🌺 Front Controller 패턴

- 프론트 컨트롤러 패턴은 하나의 서블릿 파일로 모든 요청들이 집하되고, 서블릿 내에서 요청에 따른 분기된 일련의 작업이 실행된다

예를 들어 아래와 같은 경우를 생각해보자

-먼저, 테스트용 jsp 페이지를 만들어보자

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%  request.setCharacterEncoding("UTF-8"); %>
<%  response.setContentType("text/html;charset=UTF-8"); %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>컨트롤러 페턴 테스트</title>
</head>
<body>
	<a href="insert.do">insert</a>
	<hr>
	<!-- http1.1 port -->
	<a href="http://127.0.0.1:8089/<%=request.getContextPath()%>/update.do">update</a>
	<hr>
	<a href="http://127.0.0.1:8089/<%=request.getContextPath()%>/delete.do">delete</a>
	<hr>
	
</body>
</html>
```

-그리고 위의 각종 요청에 따른 명령을 처리할 서블릿 파일을 딱 하나만 만들어보자

```java
package com.js.frontController;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class FrontController
 */
//어떤 파일이던지 확장자명이 do이면 이 서블릿에 도착할 수 있도록 함
//->front controller
/*
 * 넘어온 URI를 이용해서 컨택스트 패스를 구하고
 * 컨텍스트패스 이후의 ~.do 요청을 파악
 * */
@WebServlet("*.do")
public class FrontController extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public FrontController() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
	//	response.getWriter().append("Served at: ").append(request.getContextPath());
		System.out.println("doGet!!");
		//요청한 작업을 파악(쿼리스트링에 command=~로 보내는 것도 방법)
		actionDo(request,response);//로직은 캡슐화시키기
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//doGet(request, response);
	}
	
	private void actionDo(HttpServletRequest request, HttpServletResponse response) {
		
		String uri = request.getRequestURI();//요청으로부터 URI를 가져올 수 있는 메서드
		String contextPath = request.getContextPath();
		String cmd  = uri.substring(contextPath.length());

		System.out.println(cmd);
		
		//명령에 따른 분기 실행
		if(cmd.contains("insert")) {
			//insert
			System.out.println("I'm insert");
		}else if(cmd.contains("delete")) {
			//delete
			System.out.println("I'm delete");
		}else if(cmd.contains("update")) {
			//update
			System.out.println("I'm update");
		}
		
	}

}
```

위와 같이 확장자 패턴("*.do")을 이용하여 [insert.do](http://insert.do) 등 모든 요청이 들어오면 해당 서블릿이 호출되게 된다. 그 후 URI에서 명령을 확인하여 분기 실행하게 된다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/MVC%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%EA%B3%84%EC%B8%B5%20-%20FrontController%20%ED%8C%A8%ED%84%B4.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/MVC%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%EA%B3%84%EC%B8%B5%20-%20FrontController%20%ED%8C%A8%ED%84%B4.gif?raw=true)

컨트롤러 패턴- Front Controller 패턴

🌻 URI는 URL과 어떠한 자원의 위치를 가르키는 개념이지만, URL은 위치를 가리키는 개념이라면, URI는 자원을 식별하는 식별자 개념을 의미한다. 최근에는 apache 등 다양한 기술로 인해서 URI를 보다 많이 사용하는 편이라고 한다

URL: Uniform Resource Location

URI : Uniform Resource Identifier

[https://blog.lael.be/post/61](https://blog.lael.be/post/61)

🌺 커맨트 패턴

- 아직 커맨드 패턴이 많이 어렵지만,  정리해보면 "커맨드 객체"를 구체화하는 패턴이라는 점이다!
- 이러한 커맨드 패턴에는 invoker(sender라고도 함),  커맨드 객체를 위한 인터페이스, 다양한 커맨드 객체들, receiver가 존재
- invoker는 클라이언트로부터 받은 명령을 실행할 수 있는 메서드(커맨드 객체 및 리시버를 통해 실행할 수 있는 캡슐화된 상태이기 때문에 간접적으로 실행하는 느낌인 것 같다)를 실행한다
- 위의 invoker로 인해 인터페이스의 실행메서드가 구현된 커맨드 객체의 메서드로 하여금, receiver 객체를 통해 일련의 작업(operation(a,b,c))을 실행하게 한다!
- 🌟 클라이언트는 모든 요청 파라미터를 receiver를 포함하여 커맨드 객체에 전달해주어야 하는데(receiver.operation으로 일련의 작업이 바인딩되어 실행됨) , 이로 인해서 하나 혹은 다수의 invoker와 관련된 커맨드 객체가 생성될 수 있다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/%EC%BB%A4%EB%A7%A8%EB%93%9C%ED%8C%A8%ED%84%B4.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/%EC%BB%A4%EB%A7%A8%EB%93%9C%ED%8C%A8%ED%84%B4.png?raw=true)

컨트롤러 패턴- 커맨드 패턴

[https://refactoring.guru/design-patterns/command](https://refactoring.guru/design-patterns/command)

BoardServlet?command=boardList

위와 같이 특정 이름의 파라미터(예-command)에 명령어 정보를 담아서 전달함으로써 우리는 각 명령어에 따른 로직을 처리하는 코드를 별도의 클래스로 작성할 수 있게 된다!

🌟 별도의 경우지만,  유사한 작업을 할 때 클래스마다 완전히 다른 이름의 메서드로 작업을 할 수 있도록 하는 것은 완전 비추! 동일 이름의 메서드로 접근할 수 있도록 하는 것이 보다 큰 편의를 제공함!!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/%EC%BB%A4%EB%A7%A8%EB%93%9C%ED%8C%A8%ED%84%B4%EC%9C%BC%EB%A1%9C%20%EC%84%A4%EA%B3%84%EB%90%9C%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20MVC%20%ED%8C%A8%ED%84%B4.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/MVCPattern/%EC%BB%A4%EB%A7%A8%EB%93%9C%ED%8C%A8%ED%84%B4%EC%9C%BC%EB%A1%9C%20%EC%84%A4%EA%B3%84%EB%90%9C%20%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20MVC%20%ED%8C%A8%ED%84%B4.png?raw=true)

커맨드 패턴으로 구성된 컨드롤러가 포함된 MVC 패턴

즉, 정리해보자면, 커맨드 패턴으로 설계된 컨트롤러가 포함된 MVC 패턴은

- 요청이 들어오면, 하나의 서블릿으로 모이게 되는데, 서블릿에서는 작업을 처리하지 않고 단순히 커맨드 객체를 생성 및 관리하는 클래스로 명령을 전달
- 서블릿으로부터 요청 명령 식별 변수를 받은 클래스(invoker 클래스)는 모든 커맨드 객체들의 공통 실행 메서드를 갖춘 인터페이스가 구현된 커맨드 객체를 생성
- 커맨드 객체는 리시버를 통해 일련의 작업을 수행하게 되면서 dao에게 일을 시키게 됨
- dao의 결과는 컨트롤러에 전달되어 그에 따라 뷰가 클라이언트에게 응답

와 같은 일련의 과정을 수행하게 된다!