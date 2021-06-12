# 쿼리 스트링과 jsp/servlet 메서드 overview

## 쿼리 스트링

- 사용자가 입력한 데이터를 서버로 전달하는 가장 단순한 방법으로, GET 방식을 요청했을 때 아래와 같은 방식을 취함

리소스(url)? 이름 = 값 & 이름 = 값

- 사용 목적 : 페이지 사이의 정보 교환이 필요한 경우에 사용하기 위함
- 쿼리 스트링 URL 인코딩 규칙

1. 위의 예처럼, 쿼리 스트링이 두개 이상인 경우, & 로 연결!

2. 영어 대소문자는 그대로 전달

3. 공백은 +로 변환되어 전달

4. 한글은 %기호와 함께 16진수로 변환되어 전달

## 텍스트 데이터를 전송하는 쿼리 스트링을 위한 input 태그

텍스트 데이터를 서버로 전송할 때에는 type이 text인 input 태그를 이용하는 text box를 사용한다

자세한 형태는 아래와 같다

```html
<input type="text" name="서버에서 구별할 이름" value="초기값">
```

조금 덧붙이자면, 아래와 같이, **label 태그의 for 속성과 input 태그의 id 속성을 맞추어 주면,** 두 태그가 서로 연결되어, 마치 **입력 양식을 클릭한 것과 동일한 효과**를 얻을 수 있다!

```java
<label **for="uId"**>ID: </label>
<input type="text" name="id" **id="uId"**>
```

## 요청(HttpServletRequest).getParameter(text-box의 name값)

getParameter 메서드는 HttpServletRequest 객체의 메서드로, String을 반환한다!

- value 속성값 혹은 innerText를 읽어옴

예)

```java
String id = req.getParameter("id");
```

## 여러줄을 입력할 수 있는 글상자$^1$ && 베타적 선택을 하는 라디오 버튼$^2$

### 1. 여러줄을 입력할 수 있는 글상자 textarea

✅ 속성

rows : 텍스트 영역의 높이 지정

cols:  텍스트 영역의 너비 지정

name: 어떤 입력 양식에 입력된 데이터인지 구별

### 2. 라디오버튼 : 여러 항목 중 하나를 선택(베타적 선택!)

- checked 속성: 기본값으로 선택

사용 방식

```html
<input type="radio" name="그룹명">

예:
<form>
	<label for="uGen"> 성별 :</label>
	<input type="radio" id="uGen" name="**gender**" value="M" checked>남자
  <input type="radio" id="uGen" name="**gender**" value="F" checked>여자
</form>
```

## 체크박스와 HttpServletRequest.getParameterValues()

-

- 체크박스: 사용자가 선택하기 편하도록 여러 개의 항목을 나열하여 이중 원하는 항목을 다중 선택이 가능하도록 지원

```html
<input type="checkbox">
```

- HttpServletRequest.getParameterValues(): String[] 형태로 반환(Iterator의 구버전!)-value 속성값 혹은 innerText를 읽어옴

## 콤보박스, 리스트박스

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/Basic/%EC%BD%A4%EB%B3%B4%EB%B0%95%EC%8A%A4%EC%99%80%20%EB%A6%AC%EC%8A%A4%ED%8A%B8%EB%B0%95%EC%8A%A4.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/Basic/%EC%BD%A4%EB%B3%B4%EB%B0%95%EC%8A%A4%EC%99%80%20%EB%A6%AC%EC%8A%A4%ED%8A%B8%EB%B0%95%EC%8A%A4.PNG?raw=true)

콤보박스와 리스트박스

- 콤보박스 : size 속성을 1로 지정하거나 생략하여 사용! 드롭다운메뉴처럼 bottom-arrow를 누르면 선택지가 나오고, 선택은 단 한 개만!
- 리스트박스 : multiple 속성을 multiple로 지정함으로써 드래그 혹은 ctrl+클릭으로 여러개 선택 가능! size를 2 이상으로 지정할 수 있고, size ≥ 2로 인해서 위의 그림처럼 리스트에 size만큼씩 보이도록 할 수 있음! (size: 한번에 보여줄 아이템의 갯수)

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>콤보박스와 리스트박스</title>
<link rel="stylesheet" href="./style.css">
</head>
<body>
	<div>
		<form method="get" action="select">
			<span class="combo clearfix">
				<label for="job">직업</label>
					<select id="job" name="job" size="1">
						<option value="">선택하세요</option>
						<option value="학생">학생</option>
						<option value="컴퓨터/인터넷">컴퓨터/인터넷</option>
						<option value="언론">언론</option>
						<option value="공무원">공무원</option>
						<option value="서비스업">서비스업</option>
					</select>
			</span>
			
			<span>
				<label class="inLabel clearfix" for="interest">관심분야</label>
				<select id="interest" name="interest" size='5' multiple="multiple">
					<option value="아이스 아메리카노">아이스 아메리카노</option>
					<option value="로스팅">로스팅</option>
					<option value="생두">생두</option>
					<option value="찻잎 덖기">찻잎 덖기</option>
					<option value="핸드드립">핸드드립</option>
					<option value="원두">원두</option>
				</select>
			</span>
			<input type="submit" value="전송">
		</form>
	</div>
</body>
</html>
```

```css
@charset "UTF-8";

.combo,
.inLabel{
	float:left;
	margin:5px;
}

.clearfix::after{
	content:"";
	clear:both;
	display:block;
}
```

```java
package unit01;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class select
 */
@WebServlet("/opts")
public class opts extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public opts() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub		
		//응답페이지의 컨텐츠 타입 설정
		response.setContentType("text/html;charset=UTF-8");
		//값 가져오기
		String job = request.getParameter("job");
		//여러 선택
		String[] interests=request.getParameterValues("interest");
		PrintWriter out = response.getWriter();
		out.print("<html><body>");
		out.print("당신이 선택한 직업: <b>");
		out.print(job);
		out.print("</b><hr> ");
		out.print("당신이 선택한 관심 분야: <b>");
		
		if(interests==null) {
			out.print("관심분야가 없습니다");
		}else
		{
			for(int i = 0 ; i < interests.length;i++) {
				out.print(interests[i]+" ");
			}
		}
		
		out.println("</b><br><a href='javascript:history.go(-1)'>다시</a>");
		out.print("</body></html>");
		out.close();
		
	}

	

}
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/Basic/%EC%BD%A4%EB%B3%B4,%20%EB%A6%AC%EC%8A%A4%ED%8A%B8%EB%B0%95%EC%8A%A4.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JSP%20Servlet/Basic/%EC%BD%A4%EB%B3%B4,%20%EB%A6%AC%EC%8A%A4%ED%8A%B8%EB%B0%95%EC%8A%A4.PNG?raw=true)