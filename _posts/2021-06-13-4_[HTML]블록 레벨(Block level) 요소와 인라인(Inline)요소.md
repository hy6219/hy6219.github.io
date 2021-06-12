# 블록 레벨(Block level) 요소와 인라인(Inline) 요소

[블록요소와 인라인 요소의 차이](%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%A9%E1%86%A8%20%E1%84%85%E1%85%A6%E1%84%87%E1%85%A6%E1%86%AF(Block%20level)%20%E1%84%8B%E1%85%AD%E1%84%89%E1%85%A9%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%B5%E1%86%AB%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB(Inline)%20%20f3da7c33da24454ea184fd241910d277/%E1%84%87%E1%85%B3%E1%86%AF%E1%84%85%E1%85%A9%E1%86%A8%E1%84%8B%E1%85%AD%E1%84%89%E1%85%A9%E1%84%8B%E1%85%AA%20%E1%84%8B%E1%85%B5%E1%86%AB%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB%20%E1%84%8B%E1%85%AD%E1%84%89%E1%85%A9%E1%84%8B%E1%85%B4%20%E1%84%8E%E1%85%A1%E1%84%8B%E1%85%B5%205c8c1d68d57d43df8bb1d5300c785583.csv)

- html5에 적용된 100% 완벽한 개념은 아니지만, css 사용에 있어서 모르게 되면, 문제가 될 수 있음!

⚠️ 인라인 요소만이 가질 수 있는 특징 ⚠️

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="./main.css">
</head>
<body>
    <div>hihi</div>
    <div>hihi</div>
    <div>hihi</div>
    <div>hihi</div>
    <div>hihi</div>

    **<span>안녕하세요!</span><span>안녕하세요!</span><span>안녕하세요!</span> 
    <span>안녕하세요!</span>** 

</body>
</html>
```

위의 주황색 음영된 부분처럼, 인라인 태그 간에 개행 및 공백이 없이 작성이 된다면, 아래 그림처럼, 공백이 없이 결과를 출력해볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/HTML/Tags/Block_Inline/%EC%9D%B8%EB%9D%BC%EC%9D%B8%20%EC%9A%94%EC%86%8C%EC%9D%98%20%ED%8A%B9%EC%A7%95.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/HTML/Tags/Block_Inline/%EC%9D%B8%EB%9D%BC%EC%9D%B8%20%EC%9A%94%EC%86%8C%EC%9D%98%20%ED%8A%B9%EC%A7%95.PNG?raw=true)

inline 요소의 특징

즉, 인라인 요소는 개행이나 공백이 있으면 이를 공백으로 인식되는 것처럼 처리함!

# display속성을  이용한 블록/인라인 요소 지정

## 인라인 요소 ➡️ 블록 요소

```css
display:block;
```

인라인 요소를 블록 요소처럼 사용하고 싶다면,

위와 같은 디스플레이 속성만 추가해준다면, 가능하다!

## 블록 요소 ➡️ 인라인 요소

```css
display:inline;
```

반대로, 블록 요소를 인라인 요소처럼 사용하고 싶다면,

위와 같은 디스플레이 속성만 추가해준다면, 가능하다!

# display 속성의 기본 설정값

기본적으로, css 에서 

- 블록 요소의 경우,

```css
div{
	display : block;
}
```

- 인라인 요소의 경우,

```css
span{
	display: inline;
}
```

와 같이 설정되어 있다!

➡️  블록 요소, 인라인 요소 ← css display 속성