# CSS-속성

크기, 여백, 색상과 같은 실질적으로 눈에 보이는 스타일을 지정 가능!

1. 크기

a) width 

- 요소의 가로 너비를 지정
- 웹에서는 보통 width를 300px로 많이 사용

```css
div{
	width: 300px;
}

div{
	요소가로너비: 너비값;
}
```

b) height

- 요소의 세로 너비를 지정

```css
div{
	height: 150px;
}

div{
	요소세로너비:너비값;
}
```

c) font-size

- 글자 크기

```css
div{
	font-size: 16px;
}

div{
	글자크기:글자값;
}
```

- **대부분의 브라우저에서 폰트크기의 기본값은 16px로 fix되어 있음!** 🌟

2. 여백(margin/padding)

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Basic/Properties/%ED%8C%A8%EB%94%A9%EA%B3%BC%20%EB%A7%88%EC%A7%84.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Basic/Properties/%ED%8C%A8%EB%94%A9%EA%B3%BC%20%EB%A7%88%EC%A7%84.png?raw=true)

여백개념: margin과 padding의 개념

**a) margin** 🌟

- 요소 바깥에 존재하는 여백으로, 시각적으로는 두 개 요소 간 거리가 있는 것 처럼 보임

```css
div{
	margin:20px;/*상하좌우 방향으로 모두 20px의 마진으로 설정하자*/
}

div{
	요소바깥여백: 여백값;
}
```

- 마진을 보다 세분화하기 위해서 한 방향씩 지정 가능("개별 속성")

```css
div{
	margin-top: 20px;
	margin-right:20px;
	margin-bottom:20px;
	margin-left:20px;
}

div{
	요소바깥여백-위쪽: 여백값;
	요소바깥여백-오른쪽:여백값;
	요소바깥여백-아래쪽:여백값;
	요소바깥여백-왼쪽: 여백값;
}
```

- margin 을 설정할때 코드를 보다 짧게 하기 위하여 다음과 같이 작성하는 방법들이 존재
("Margin - Shorthand Property" "단축 속성")
**(개별 속성이 있는 경우, 단축속성이 있을 확률이 높음!)**

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Basic/Properties/margin%20%EC%86%8D%EC%84%B1-margin%20shorten%20hand.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Basic/Properties/margin%20%EC%86%8D%EC%84%B1-margin%20shorten%20hand.png?raw=true)

margin shortenhand! 

i. margin: top right bottom left

- 상 ➡️ 우 ➡️ 하 ➡️좌 (윗쪽부터 시계순서대로) 마진을 설정

ii. margin: top right&left bottom

- 상 ➡️ 좌우 ➡️ 하 순서대로 마진 설정

iii. margin: top&bottom right&left

- 상하 ➡️ 좌우 순서로 마진을 설정

iv. margin: all

- 적용 순서는 관련 없이, 모든 방향으로 일정 마진값 동일하게 마진 설정

- reference: [w3cschools-margin](https://www.w3schools.com/css/css_margin.asp)

**b) padding** 🌟

- 요소의 내부 여백을 지정 ➡️ **기존의 요소 크기보다 부피가 커짐**
- 내부 여백 = 자식 요소를 감싸는 여백

```css
div{
	padding: 20px;/*단축속성 all*/
}

div{
	요소내부여백:여백값;
}
```

- 개별 속성( 한 방향씩 지정 )

```css
div{
	padding-top:20px;
	padding-right:20px;
	padding-bottom:20px;
	padding-left:20px;
}

div{
	요소내부여백-위쪽:여백값;
	요소내부여백-오른쪽:여백값;
	요소내부여백-아래쪽:여백값;
	요소내부여백-왼쪽:여백값;
}
```

- padding shortenhand property! 단축속성!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Basic/Properties/padding%20%EC%86%8D%EC%84%B1-padding%20shortenhand%20property!.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Basic/Properties/padding%20%EC%86%8D%EC%84%B1-padding%20shortenhand%20property!.png?raw=true)

Padding shortenhand property

i. padding: top right bottom left

- 상 ➡️ 우 ➡️ 하 ➡️좌 (윗쪽부터 시계순서대로) 패딩을 설정

ii. padding: top right&left bottom

- 상 ➡️ 좌우 ➡️ 하 순서대로 패딩 설정

iii. padding: top&bottom right&left

- 상하 ➡️ 좌우 순서로 패딩을 설정

iv. padding: all

- 적용 순서는 관련 없이, 모든 방향으로 일정 마진값 동일하게 패딩 설정

- reference: [w3cschool-padding](https://www.w3schools.com/css/css_padding.asp)

3. 색상

a) color

- 글자 색상을 지정
- **주의!! font-color, text-color 속성은 없음!!**

```css
div{
	color:red;
}

div{
	글자색상:빨강;
}
```

b) background

- 요소의 배경 색상을 지정(color와 비교하여 차이를 알고 있기!)
(background-color와 같은 개별 속성으로 지정 가능)
(background는 단축 속성!)

```css
div{
	background-color:red;
}

div{
	요소배경색상 : 빨강;
}
```
