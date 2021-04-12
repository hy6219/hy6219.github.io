# 💚CSS 기본 문법

CSS 기초 usage

```css
div{
	font-size: 20px;
	color: red;
}

선택자 {
	속성:값;
	속성:값;
}
```

1. 선택자

a. 역할
    - html에 스타일(css)를 적용하기 위해서 html의 특정 요소를 선택하는 
    사인

```html
    <div>
    	<h1>제목..</h1>
    	<p>본문..</p>
    </div>
 ```



```css
    h1{
    	color: red;
    }

    p{
    	color: blue;
    }
 ```

2. 속성(Properties)과 값(Value)

- 글자 색이나 너비, 여백과 같은 파트( ➡️속성)를 어떤 스타일( ➡️값)로 
지정하여 꾸밀 것인지 지정하여 사용시에 활용!

```css
div{
	color: red;/*글자색은 빨강*/
	font-size: 20px;/*글자 크기는 20px*/
	width: 300px;/*가로 너비는 300px*/
	margin: 20px;/*바깥 여백은 20px*/
	padding: 10px 20px;/*왼쪽 여백은 위아래 10px, 좌우 20px*/
	position: absolute; /*position: 위치 기준을 잡는 요소*/
/*위치는 부모 요소 기준(이게 어떤 의미일지는 나중에 확인!)*/
}
```
