# COS 라이브러리 기반 파일 업로드

🌺 WHAT IS COS?

- 자바에서 파일 업로드 시에 자주 사용되는 라이브러리
- 우리는 cos.jar 파일을 이용할 것!
- com.oreilly.servlet의 약자!

📌 COS 라이브러리의 cos.jar 파일 설정 방법

1. [http://www.servlets.com/cos](http://www.servlets.com/cos) 접속
2. 스크롤을 내려서(왼쪽 네비게이션 바 부분이 아니라 본문 부분을 내리다 보면 존재!) download 카테고리의 cos-26Dec2008.zip 파일 다운로드(21.06.08 기준 현재 cos-20.08.zip으로 갱신되어있었다!)[[http://www.servlets.com/](http://www.servlets.com/) 로 접속 수 네비게이션 바에서 COS~로 시작하는 카테고리를 눌러도 된다!]
3. 압축파일을 받아 압축을 해제하고,  lib 폴더의 cos.jar 파일을 확인해둔다
4. 이 cos.jar 파일을 WebContent의 WEB-INF 폴더의 하위 폴더인 lib 폴더에 import 해주면 서블릿에서 사용할 수 있게 된다!

🌟 파일을 서버에 전송하기 위해서 form 태그 및 input 태그에서 설정해주어야 할 몇 가지 사항들 🌟

1.  form 태그의 method는 post로!
2. form 태그의 enctype은 multipart/form-data 로 설정하기!!
3. input 태그의 type은 file로!

❓ what is enctype  ❓

ref : [http://tcpschool.com/html-tag-attrs/form-enctype](http://tcpschool.com/html-tag-attrs/form-enctype)

enctype은 서버로 양식을 전송하기 전 인코딩하는 방식을 다루는 속성으로, post method인 경우에만 사용이 가능하다

이 속성의 값으로는

- application/x-www-from-urlencoded : 기본값! 모든 문자들은 서버로 전송되기 전에 인코딩 됨을 명시
- multipart/form-data : 주로 파일을 다룰 때 사용되는 값으로, 모든 문자를 인코딩하지 않음을 나타냄
- text-plain : 공백문자만 + 기호로 인코딩하고, 나머지 문자는 모두 인코딩하지 않음을 명시

❓ enctype을 설정해주는 이유 ❓

- 이 속성이 설정되지 않으면 파일명만 텍스트로 서버로 전송되고, 파일 객체는 전달되지 않기 때문!

❓ post method를 사용하는 이유❓

- get 방식을 이용하게 되면 쿼리스트링에 해당 정보를 노출하여 정보를 전송하게 되는데, 이는 파일과 같은 전송할 데이터의 용량이 큰 경우에 부적합하기 때문!(get 방식으로는 255자(=$2^8$ -1 =즉 8비트를 이용])이하의 적은 용량의 데이터만 전송할 수 있음]

- post 방식은 255자 이상의 대용량 데이터를 전송할 수 있음!

[https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type)

---

- 참고로, cos.jar에서 제공되는 MultipartRequest 클래스(이름에서 풍겨지는.. enctype의 속성값의 향기..?)를 확인하기 위해서는 Build Path- Add to Build Path를 눌러준다!

▶️ 그러면 java resources의 Libraries 의 Referenced Libraries 아래에서 cos.jar 하위에 com.oreilly.servlet 밑에서 [MultipartRequest.](http://multipartrequest.java)class 파일을 확인해볼 수 있다

(클래스 파일을 확인해보고 싶었는데 attach 문제가 발생하여 문서로 확인해보기로 하였다 😭)

[http://www.servlets.com/cos/javadoc/com/oreilly/servlet/MultipartRequest.html](http://www.servlets.com/cos/javadoc/com/oreilly/servlet/MultipartRequest.html)

위의 문서 등을 확인해보면 MutlipartRequest클래스의 생성자는 아래와 같은 파라미터를 이용함을 알 수 있다

[MultipartRequest 생성자의 필드](COS%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%87%E1%85%B3%E1%84%85%E1%85%A5%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A1%E1%86%AB%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF%20%E1%84%8B%E1%85%A5%E1%86%B8%E1%84%85%E1%85%A9%E1%84%83%E1%85%B3%20b932b41137fe419192170865af58f64e/MultipartRequest%20%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC%E1%84%8C%E1%85%A1%E1%84%8B%E1%85%B4%20%E1%84%91%E1%85%B5%E1%86%AF%E1%84%83%E1%85%B3%20a2b3122d4a3c44e2adb6fe62cfb024be.csv)

[MultipartRequest 클래스에서 사용되는 메서드들](COS%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%87%E1%85%B3%E1%84%85%E1%85%A5%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A1%E1%86%AB%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF%20%E1%84%8B%E1%85%A5%E1%86%B8%E1%84%85%E1%85%A9%E1%84%83%E1%85%B3%20b932b41137fe419192170865af58f64e/MultipartRequest%20%E1%84%8F%E1%85%B3%E1%86%AF%E1%84%85%E1%85%A2%E1%84%89%E1%85%B3%E1%84%8B%E1%85%A6%E1%84%89%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%83%E1%85%AC%E1%84%82%E1%85%B3%E1%86%AB%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%A5%E1%84%83%E1%85%B3%E1%84%83%E1%85%B3%E1%86%AF%204ece9b43b45b4ea59e79b26e6c31dd94.csv)

🌟 클라이언트가 jsp 페이지 요청 시, 컴파일된 파일들이 워크스페이스 디렉토리 내의 .metadata/.plugins/org.exlipse.wst.server.core/tmp/wtpwebapps 폴더에 저장된다!

(나의 경우 D:\ji_study\jsp servlet\eclipse_jsp_servlet\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\wtpwebapps\web-study-10 에 저장됨을 확인하였다)

아래의 예제와 함께 몇가지 주의할 사항을 확인하면서 연습해보자

먼저, 파일을 업로드 하는 페이지를 작성해보자

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>파일업로드 테스트</title>
</head>
<body>
	<section>
		<article id="fileUpload">
			<form action="upload.do" method="post" enctype="multipart/form-data">
				<label>글쓴이&nbsp;:<input type="text" name="writer"></label><br>
				<label>제목&nbsp;&nbsp;:<input type="text" name="title"></label><br>
				<label>파일 지정하기 : <input type="file" name="uploadFile"></label><br>
				<input type="submit" value="파일 업로드하기">
			</form>
		</article>
	</section>
</body>
</html>
```

이번에는 위의 페이지와 post method로 연결될 upload.do에 해당되는 서블릿이다

```java
package com.saeyan.controller;

import java.io.File;
import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.oreilly.servlet.MultipartRequest;
import com.oreilly.servlet.multipart.DefaultFileRenamePolicy;

/**
 * Servlet implementation class UploadServlet
 */
@WebServlet("/upload.do")
public class UploadServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public UploadServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//response.getWriter().append("Served at: ").append(request.getContextPath());
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//doGet(request, response);
		//post 방식으로 업로드 처리
		request.setCharacterEncoding("UTF-8");
		//content-type 설정(header setting)
		response.setContentType("text/html;charset=UTF-8");
		PrintWriter out = response.getWriter();
		
		//파일을 다운받는 경로
		String savePath = "upload";
		//최대 업로드 파일 크기를 5MB로 제한
		int uploadFileSizeLimit = 5 * 1024 * 1024;//MB=1024^2 B
		//인코딩
		String encType="UTF-8";
		
		//서버 상 실제 경로 찾기
		ServletContext context = getServletContext();
		String uploadFilePath = context.getRealPath(savePath);
		System.out.println("서버 상 실제 디렉토리:"+uploadFilePath);
		// https://coding-factory.tistory.com/283
		// java.lang.IllegalArgumentException: Not a directory 는 디렉토리가 없어서 생긴 문제이므로
		//이를 체킹 후 생성
		File folder = new File(uploadFilePath);
		if(!folder.exists()) {
			//폴더 생성(폴더가 없으면)
			folder.mkdir();
		}
		
		//HttpServletRequest 객체, 업로드할 경로, 업로드할 사이즈 제한값, 인코딩 방법, 중복된 파일명에 대한
		//새로운 이름 부여를 매개변수로 하는 MultipartRequest객체 생성 -> 서버로 파일이 업로드됨
		try {
			MultipartRequest req = new MultipartRequest(
					request,//multipart request와 연결될 request 객체
					uploadFilePath,//서버 상 실제 디렉토리
					uploadFileSizeLimit,//파일 용량 제한
					encType,//인코딩 방법
					new DefaultFileRenamePolicy()//동일 이름 존재시 새로운 이름 부여
					);
			//업로드된 파일의 서버상 이름 얻기
			String fileName = req.getFilesystemName("uploadFile");
			
			if(fileName == null) {
				//파일이 업로드되지 않았을 때
				System.out.print("파일이 업로드되지 않았음");
			}else {
				//파일이 업로드되었을 때
				out.println("<br>글쓴이 : "+req.getParameter("writer"));//request가 연결된 req객체에서도 이제 파라미터를 마음껏 가져올 수 있음
				out.println("<br>제&nbsp;목: "+req.getParameter("title"));
				out.println("<br>파일명: "+fileName);//서버에 저장된 파일이름 출력
			}
		}catch(Exception e1) {
			e1.printStackTrace();
		}
	}

}
```

주의할 점이 있다면, 위에서 저장되는 위치에 폴더명이 존재하지 않으면 

java.lang.IllegalArgumentException: Not a directory

에러가 발생하는데, 이는 당연하다! 왜냐하면 지금 서버에 폴더가 없으니까!

그래서 폴더를 생성하는 방식을 찾아보았는데,

[https://coding-factory.tistory.com/283](https://coding-factory.tistory.com/283)

위의 글을 참조하였다! 리눅스의 명령어인 mkdir이 떠오르는 메서드를 이용하여

```java
File folder = new File(폴더명);
if(!folder.exists()){
	folder.mkdir();
}
```

위와 같이 폴더가 존재하지 않으면 폴더를 생성하도록 해주는 것이다!

그러면 위에 페이지 등 파일이 저장되는 위치에 upload라는 이름의 폴더가 생성됨을 확인해볼 수 있다

서블릿 부분에서 몇 가지 짚고 넘어갈 사항이 있어서 이 부분을 확인하고 넘어가보자!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/COS/fileUpload/COS%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%ED%8C%8C%EC%9D%BC%EC%97%85%EB%A1%9C%EB%93%9C.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/COS/fileUpload/COS%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%ED%8C%8C%EC%9D%BC%EC%97%85%EB%A1%9C%EB%93%9C.gif?raw=true)

COS 라이브러리를 이용한 파일 업로드

먼저 위에서 

new DefaultFileRenamePolicy()

로 파일 rename 여부에 대한 기준을 세워두었기 때문에, 아래처럼, 동일한 파일명을 가진 파일의 경우, 서버에서 파일명을 새로운 이름으로 변경하여 적용하게 된다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/COS/fileUpload/COS%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-RenamePolicy.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/COS/fileUpload/COS%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-RenamePolicy.gif?raw=true)

COS 라이브러리- new DefaultFileRenamePolicy

그 다음은, multipart/form-data를 지원하기 위한 MultipartRequest 클래스 객체를 생성하기 위한 

- 클라이언트가 업로드한 파일을 서버에서 다운받는 경로(String savePath)
- 파일 업로드 크기 제한(int uploadFileSizeLimit)
- 인코딩(String encType)

를 미리 선언, 정의해둔 후 객체를 생성한 부분이다

```java
	//파일을 다운받는 경로
		String savePath = "upload";
		//최대 업로드 파일 크기를 5MB로 제한
		int uploadFileSizeLimit = 5 * 1024 * 1024;//MB=1024^2 B
		//인코딩
		String encType="UTF-8";

...

MultipartRequest req = new MultipartRequest(
					request,//multipart request와 연결될 request 객체
					uploadFilePath,//서버 상 실제 디렉토리
					uploadFileSizeLimit,//파일 용량 제한
					encType,//인코딩 방법
					new DefaultFileRenamePolicy()//동일 이름 존재시 새로운 이름 부여
					);
```

그리고 위의 필드 중 uploadFilePath와 관련된 부분이 지금 짚고 넘어갈 부분인데, 서버 상의 실제 경로를 찾아내기 위해 ServletContext 객체를 이용하여 getRealPath 메서드를 이용하였다

```java
//서버 상 실제 경로 찾기
		ServletContext context = getServletContext();
		String uploadFilePath = context.getRealPath(savePath);
```

🌟 마지막으로 ,  파일의 서버상 이름을 얻기 위한 부분인데, 이 부분에서 파라미터는 input type=file의  name 값이 들어가야 한다!

```java
	//업로드된 파일의 서버상 이름 얻기
			String fileName = req.getFilesystemName("uploadFile");

/*
input type=file name=uploadFile에서의 name과 일치시켜야!!
*/
```

❓ 음.. 그런데 파일을 한개만 올리는 상황만 있는 것도, 아니고, 여러 개 파일을 받아올 수 있어야 하지 않을까요 ❓

그래서 존재하는 것이 바로 

MultipartRequest 객체.getFileNames()

이다! 이 메서드는 Enumeration 형으로 업로드된 파일명들을 반환해준다!

이를 이용하여

먼저 화면단에 보여질 화면은 다음과 같이 변경해주고

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>파일업로드 테스트</title>
</head>
<body>
	<section>
		<article id="fileUpload">
			<form action="multiUpload.do" method="post" enctype="multipart/form-data">
				<label>글쓴이&nbsp;:<input type="text" name="writer"></label><br>
				<label>제목&nbsp;&nbsp;:<input type="text" name="title"></label><br>
				<label>1.파일 지정하기 : <input type="file" name="uploadFile01"></label><br>
				<label>2.파일 지정하기 : <input type="file" name="uploadFile02"></label><br>
				<label>3.파일 지정하기 : <input type="file" name="uploadFile03"></label><br>
				<input type="submit" value="파일 업로드하기">
			</form>
		</article>
	</section>
</body>
</html>
```

요청이 들어올 컨트롤러에서는 아래와 같이

```java
package com.js.controller;

import java.io.File;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.oreilly.servlet.MultipartRequest;
import com.oreilly.servlet.multipart.DefaultFileRenamePolicy;

/**
 * Servlet implementation class MultiUploadServlet
 */
@WebServlet("/multiUpload.do")
public class MultiUploadServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public MultiUploadServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub

	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//encoding setting
		request.setCharacterEncoding("UTF-8");
		response.setContentType("text/html;charset=UTF-8");
		
		PrintWriter out = response.getWriter();
		
		//클라이언트에서 업로드한 파일(들)을 서버에서 다운로드할 경로
		String savePath  = "upload";
		File   folder    = new File(savePath);
		
		if(!folder.exists()) {
			folder.mkdir();
		}
		//최대 5MB로 제한
		int maxFileSize  = 5 * 1024 * 1024;
		//multipart request객체의 인코딩
		String encType   = "UTF-8";
		
		//서버에서의 파일의 실제 경로를 얻기 위함
		ServletContext context = getServletContext();
		String        realPath = context.getRealPath(savePath);
		
		try {
			
			MultipartRequest mr = new MultipartRequest(
					request,//request객체-서버와 주고받게될 request 객체
					realPath,//서버에서 관리되는 파일의 실제 경로
					maxFileSize,//파일 최대 용량 지정
					encType,//인코딩
					new DefaultFileRenamePolicy()
					);
			
			//연결된 페이지에서 Enumeration 형태로 여러개의 파일 받아오기
			**Enumeration files = mr.getFileNames();**
			int i = 0;
			**while(files.hasMoreElements())** {
				//파일이 존재한다면
				
				//파일 이름들을 저장 후 그 파일명으로 서버에 저장된 파일 이름을 가져오고
				String file = (String)files.nextElement();
				String serverFileName = mr.getFilesystemName(file);
				//클라이언트에서 업로드한 파일명도 가져오기
				String originalFileName = mr.getOriginalFileName(file);
				
				out.print("<p>#"+i+"업로드된 파일명: "+serverFileName+"</p>");
				out.print("<p>업로드된 파일 원본명: "+originalFileName+"</p>");
				out.print("<hr>");
				i++;
			}
			
		}catch(Exception e1) {
			System.out.println("[ERR]서블릿 multipart request 객체 생성 에러");
			e1.printStackTrace();
		}
	}

}
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/COS/fileUpload/COS%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EC%97%AC%EB%9F%AC%EA%B0%9C%20%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/COS/fileUpload/COS%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EC%97%AC%EB%9F%AC%EA%B0%9C%20%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C.gif?raw=true)

Enumeration MultipartRequest.getFileNames()- 여러 개의 파일 업로드

위의 MultipartRequest객체.getFilesystemName(name속성명)으로는 하나의 파일만 접근하여 String으로 받아올 수 있었는데, 이 방식으로 접근하였을때는 보다 많은 파일을 처리할 수 있다는 점이 매력적이다!

Enumeration getFileName()

꺼내는 방법은 자바 컬렉션의 구 iterator인 Enumeration이므로, hasMoreElements()로 접근하여 한개씩 한개씩 커서를 옮기면서 수행하면 된다!