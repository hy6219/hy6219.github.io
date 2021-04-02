# HTML 문법

1.  기본적인 형태

```html
1. <TAG></TAG> 
<!--하나의 element(요소)-->

2. <TAG>CONTENT</TAG>

3. <TAG/> <!--빈태그-->
<br/>나 <img/> 등의 경우는 이렇게만 사용

```

-   태그가 곧 요소!
-   하지만, 큰 의미는 없지만, 열리는 태그(start tag)와 닫히는 태그(end tag)는 "태그"

열리는 태그+닫히는 태그가 "요소"

*h1 태그는 "대제목" "대주제"를 의미하기 때문에 브라우저는 아래의 내용을 다음과 같이 해석

🌟**semantic element** : 의미를 갖는 요소(태그들의 모임!) - 예) h1, p

```html
<h1>토끼와 거북이</h1>

```

[브라우저의 해석]

```html
<주제1(여기서 열림)>토끼와 거북이</주제1(여기서 닫힘)>

```

🌟 빈 태그(empty tag)

```html
<!--2가지 방식 모두 HTML5에서 적용될 수 있음-->
<!--원래 사용할 수 있었던 곳: XHTML-->
type 1. self- closing tag 
<TAG/>
<TAG />
<!--원래 사용할 수 있었던 곳: HTML1,2,3,4-->
type 2. empty tag
<TAG>


```

-   XHTML 버전이나 Lint(린트) 환경인 경우: self-closing[그 외에서는 type2로 사용 권장]
-   self-closing을 사용하려면 전체적으로 모든 요소가 self-closing이어야 좋음
-   /가 없는 경우로 적용하려면 전체적으로 통일해서 사용
-   단, 1+2유형으로 사용하는 것은 권장되지 않음

**🌟빈 태그 대부분은🌟**

```html
<img/>
<!--태그가 가진 의미 뿐 아니라,
확장시킬 수 있는 속성="값"이 포함되어 있음-->

```

## HTML 속성과 값

**Usage**

```html
<TAG 속성="값"></TAG>

```

**Example**

```html
<img src ="./my_photo.jpg"alt="내 프로필 사진"/>
<!--src : source 이미지 주소-->
<!--alt : alternative 대체 텍스트-->
<!--대체 텍스트: 다양한 이유로 이미지가 비정상적으로-->
<!--삽입되지 못하였을 때 표시할 텍스트-->

<!--div: division 의미를 갖지 않는 태그 != semantic tag-->
<!--class는 별명이라고 생각해도 됨-->
<div class ="name">홍길동></div>

```

[위의 경우를 브라우저는 아래와 같이 해석]

```html
<이미지 삽입 소스위치="~" 대체 텍스트="~"/>

```

## 부모와 자식 요소

**Usage - 상대적인 개념!**

요소 a를 바깥에서 감싸고 있는 형태의 블록이 존재할 경우

-   바깥 요소 ➡️ 부모 요소
-   내부 요소 ➡️ 자식 요소

```html
<Parent>
	<Child>
  </Child>
</Parent>

```

**Example**

```html
<section class ="fruits">
  <h1>과일목록</h1>
  <ul>
		<li>사과</li>
		<li>딸기</li>
		<li>바나나</li>
		<li>오렌지</li>
  </ul>
</section>

```

만약, 위와 같이 li > ul > section의 형태로 존재하는 블럭에서는 li는 과연 자식 요소라고 부를 수 있을까?

➡️ **li : 후손(자손, 하위) 요소**

➡️ **section : 조상(상위) 요소**

➡️➡️ 나중에 이러한 **부모 요소, 자식 요소, 조상(상위)요소, 후손(자손, 하위) 요소가 구조를 파악함에 있어서 중요**!

🌟 css 요소를 적용할 때, 위에서 말한 구조 파악이 중요!!

## HTML 문서의 범위를 나타내는 태그들을 알아보기

html 문서는 아래와 같은 구조를 갖는다.

```html
<!DOCTYPE html>
<html>
	<head>
		문서의 정보
  </head>
  <body>
	  문서의 구조
  </body>
</html>

```

1.  **html 태그** : <html>~</html> 까지가 전체 html 문서임을 나타냄

-   문서의 시작과 끝을 나타냄

2.  **head 태그** : 전체 문서의 정보를 나타냄

-   과연 그 정보란 무엇일까? a. 인코딩 정보 b. 저자 정보 c. 사이트 설명 d. 페이지 이름(탭에 붙여질 이름) e. 링크될 css f. 자바스크립트 ... 와 같은 것이 "정보"라고 할 수 있음

3.  **body 태그** : 전체 문서의 구조를 나타냄
    
    ```
    - 구조란 무엇일까?
      a. 정보를 바탕으로 화면에 출력될 이미지, 동영상 등
    
    ```
    
4.  **DOCTYPE(DTD; Document Type Definition)** 태그
    

-   특정 html 파일이 브라우저에서 **어떤 버전으로 해석되어 출력**될 지 정의

(HTML 버전) - HTML 1~5(현재 표준은 HTML5), XHTML

-   DOCTYPE은 버전에 따라 아래와 같이 사용될 수 있음

a. XHTML 1.1

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "<http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd>">

```

-   XHTML에도 [다양한 버전](https://en.wikipedia.org/wiki/Document_type_declaration) 이 존재함

b. HTML 4.01

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "<http://www.w3.org/TR/html4/loose.dtd>">

```

**c. HTML 5** 🌟

```html
<!DOCTYPE html>

```

