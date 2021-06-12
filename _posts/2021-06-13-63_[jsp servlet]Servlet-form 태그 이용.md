# Servlet-form 태그 이용

기본적으로 서블릿과 html을 이용한 서비스를 만들기 위한 기본적인 form 태그 이용은 아래와 같이 사용된다

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	<div>
		📌**<form method="get/post 방식" action="호출할 서블릿">**
			<input type="text" value="아무거나 테스트">

		<!-- action 속성에 기술된 서블릿으로 데이터를 전송하기 위한
		submit 버튼 빌요 -->
		📌	**<input type="submit" value="전송">**

		<!-- 취소버튼은 데이터를 다시 입력할 수 있도록, 이전에 입력한
		내용을 깨끗이 지우도록! -->
		****📌	**<input type="reset" value="취소">**
		</form>
	</div>
</body>
</html>
```