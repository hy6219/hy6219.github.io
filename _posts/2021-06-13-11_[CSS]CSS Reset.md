# CSS Reset

## CSS 스타일을 초기화해야하는 이유! 필요성!

- 브라우저에 따른 지원되는 태그의 기본값이나 여백의 기본값 등이 달라질 수 있기 때문

## 효율적으로 초기화하는 방법!

- Reset.css 파일을 만들어서 초기화하자!
1. reset css cdn 검색하여 [https://www.jsdelivr.com/package/npm/css-reset](https://www.jsdelivr.com/package/npm/css-reset) 를 들어가보면, reset.css를 클립보드 모양을 눌러서 copy to html 선택!

~~.min.css 가 보이는데, min은 공백을 최대한 없애서 압축했음을 의미!

2. 생성된 link 태그(<link rel="stylesheet" href="[https://cdn.jsdelivr.net/npm/css-reset@0.0.1/reset.css](https://cdn.jsdelivr.net/npm/css-reset@0.0.1/reset.css)">)를 내가 만든 css 파일 link 태그 보다 위에 작성!하여 초기화하기!

---

## Emmet 문법

- 복잡한 구조를 효율적으로 작성 가능!
1. 태그명 입력 후 탭/엔터 ➡️ 태그를 바로 생성할 수 있음
2. .클래스명 +탭/엔터키 ➡️ <div class="클래스명"></div> 가 생성됨
3. 태그명.클래스명 + 탭/엔터키 ➡️ <태그 class="클래스명></태그> 가 생성됨(즉, 2+3번은 css 선택자를 이용하여 태그를 생성하는 것!)
4. .클래스명>태그명.클래스명 ▶️
(자식요소 선택자 이용)

```html
<div class="~">
	<태그 class="~">
	</태그>
</div>
```

5. *갯수 ▶️ * 전의 요소들에 대해서 갯수만큼 생성 가능

6. $표시는 생성되는 순서의 값을 의미!

7.태그{}는 태그 사이에 들어갈 값을 넣겠다는 의미!

(예제)

```html
.container>ul.list>li.list-item*10>a{list$}
```

```html
<div class="container">
        <ul class="list">
            <li class="list-item"><a href="">list1</a></li>
            <li class="list-item"><a href="">list2</a></li>
            <li class="list-item"><a href="">list3</a></li>
            <li class="list-item"><a href="">list4</a></li>
            <li class="list-item"><a href="">list5</a></li>
            <li class="list-item"><a href="">list6</a></li>
            <li class="list-item"><a href="">list7</a></li>
            <li class="list-item"><a href="">list8</a></li>
            <li class="list-item"><a href="">list9</a></li>
            <li class="list-item"><a href="">list10</a></li>
        </ul>
    </div>
```

- 외부에는 container클래스의 div 요소가 둘러싸고 있음
- 그 내부에는 10개의 li 태그(클래스명은 list-item)가 있는 ul 요소가 있음
- 각 li 태그 내부에는 "list$" 값을 같는 a 태그가 들어 있는데, $는 생성되는 순서값이 해당됨!

---

8.  CSS 내부에서 w:100입력 후 탭/엔터키를 누르면

width:100px; 속성이 추가됨

9. h: 100은 height:100px; 속성이 추가됨!