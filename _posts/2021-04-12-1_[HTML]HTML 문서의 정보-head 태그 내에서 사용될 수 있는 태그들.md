## 💚HTML 문서의 정보-head 태그 내에서 사용될 수 있는 태그들

1. title 태그: 웹페이지의 제목을 나타냄
    - 탭에 이름으로 노출되는 부분을 담고 있음
    - 구조화보다는 정보로써 사용되는 태그

    ```html
    <head>
    	<title>네이버</title>
    </head>
    ```

2. meta 태그 : 웹 페이지의 정보를 나타냄(빈 태그)
    - **title, link, style 등의 태그에서 나타낼 수 없는 기타 모든 정보를 나타내는 태그**
    - html(웹페이지)에 관한 정보(표시 방식, 제작자(소유자), 내용, 키워드 등)을 검색엔진이나 브라우저에 제공

    ```html
    <head>
    <!--문자 인코딩하는 부분은 페이지 내용이 잘 표시되기 외해서 꼭 넣어주기!-->
    	<meta charset = "UTF-8">
    <!--author(제작자)정보-->
    	<meta name="author" content ="홍길동">
    <!--사이트 설명 정보-->
      <meta name="description" content ="우리 사이트가 최고!">
    </head>
    ```

    - 문자 인코딩: 텍스트를 렌더링할 때, 출력되는 방식을 지정하는 것
    ! ! utf -8은 한국어, 중국어, 일본어 등이 지원됨(이전에는 euc-kr을 사용하고는 했었는데, 유지 보수하는 사이트에서 많이 볼 수 있음)

    2-1. <meta name="author" : 제작자 정보 종류를 표시하고 싶음을 명시

    2-1-1. <meta name="author" content ="홍길동"> : 제작자가 "홍길동"임을 표시하고 싶음을 명시

    2-2.  <meta name="description" : 사이트에 대한 설명을 명시

    2-2-1. <meta name="description" content ="우리 사이트가 최고!"> : 사이트에 대한 설명이 content임을 명시

    🌟 **메타태그가 사용할 수 있는 속성들!**🌟

    - name, charset 등은 메타태그에서만 사용할 수 있음
    ➡️ 특정 태그 내에서만 사용가능한 속성이 있음!

    [meta 태그의 속성들](https://www.notion.so/cf7488abb9bb472c9e31ee5bf815355b)

    🌟외우지 말고! 상황에 맞게 찾아서 사용하기!

3. link 태그
    - **외부문서( 대부분은 css ; 그 외에는 html 문서, 파비콘 등 )를 연결할 때 사용**
    - 자바스크립트를 불러오는 것이 아님!
    - link태그도 빈태그!

    ```html
    <head>
    	<link rel="stylesheet" href="./.css/main.css">
    	<!--favorite icon : favicon을 가져오는 것-->
      <link rel="icon" href="./favicon.png">
    </head>

    <문서의 정보범위>
    	<외부 문서 연결 관계="CSS" 문서 경로="./.css/main.css">
    	  <외부 문서 연결 관계="사이트대표아이콘"
    				 문서 경로="./favicon.png">
    </문서의 정보범위>
    ```

    [link 태그의 속성들](https://www.notion.so/bff092c048a446f185dc12784a3b6694)

4. style 태그 
    - **html 문서 내에서 css 를 직접적으로 작성**하기 위해서 사용
    - html 내부에서 css를 작성하려면 꼭!! style 태그 내부에서 사용해야 함

    ```html
    <style>
    	img{
    		width: 100px;
    		height: 200px;
      }
      p{
    		font-size: 20px;
    		font-weight: bold;
      }
    </style>
    ```

    🎁 **css를 html문서 내에 적용하는 방법!**🎁

    1) style 태그를 이용해서 내부에서 작성

    2) link 태그를 이용해서 외부에서 작성된 css 파일을 연결시키기

5. script 태그
    - **js 파일은 script 태그를 통해서 (1)작성 할 수도 있고 (2) 불러올 수도 있음! ( ➡️ css 와의 차이!!)**

    ```html
    <!--외부에서 js 파일 불러오기-->
    <!--src: source의 약자-->
    <script src="./js/main.js"></script>

    <!--내부에서 js 작성하기-->
    <script>
    	function windowOnClickHandler(event){
    		console.log(event);
      }
    window.addEventListener('click', windowOnClickHandler);
    </script>
    ```

## 💚HTML 문서 - body 태그(div, image)

- 🌟**body 태그 내에서 사용하는 태그**🌟들은 🌟**html 문서의 구조**🌟를 나타냄!

1. div 태그
    - division 의 약어로, 분할을 의미하지만 실질적으로는 "문서의 부분이나 섹션을 정의"하는 경우에 많이 사용됨
    - 명확한 의미를 갖고 있지 않아서, 단순히 특정 범위를 묶는(wrap) 용도로 사용
    - 묶인 부분들에 css나 js를 적용하게 됨!(그 그룹에 꾸미거나 동작을 할 수 있도록)

```html
<div>
	<div>
		<p></p>
	</div>
</div>
```

2.  img 태그(빈 태그)

- 이미지를 html문서 내부에 삽입할 경우 사용
- img 태그는 빈 태그이기 때문에, "이미지를 삽입하겠다" 라는 의미만 갖고, 실질적으로 어떤 이미지를 넣을지는 나타내어 있지 않아서 src 및 alt 속성으로 명시해주어야 함!

```html
<body>
	<img src="./kitty.png" alt="냥이">
</body>

<body>
	<이미지 경로="./kitty.png" 대체텍스트="냥이">
</body>
```

**🌟html에 이미지를 삽입하는 방법🌟

1. img 태그를 사용
2. css 내에서 이미지를 삽입**

[img 태그의 속성](https://www.notion.so/9ecc8c48ccff41d0973e8cb6c75653e3)

(필수): 필수 속성; Required Attributes

```html
<img src="./kitty.png">
```

만약, 위와 같이 alt 속성이 누락된 경우, 이는 **"웹 표준에 어긋남"**

## 💚 웹 표준 검사하기

- [W3C validator](https://validator.w3.org/)  에서 작성한 html 문서를 업로드한 후 테스트 가능(기본적인 표준 여부 판단 가능)
- 입문자는 익숙해질때까지 자주 확인 권장!
- 작성한 html 문서가 표준에 부합하는지 테스트 가능
