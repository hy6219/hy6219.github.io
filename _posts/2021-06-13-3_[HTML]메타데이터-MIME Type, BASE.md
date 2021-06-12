# 메타데이터-MIME Type, BASE

# 1. MIME Type

📌**Overview**📌

- link 태그에서 생략된 속성과 그에 대한 값 type="text/css"

```html
<link rel="stylesheet" href="~" **type="text/css"**>
```

- style 태그에서 생략된 속성과 그에 대한 값 type="text/css"

```html
<style **type="text/css"**>
</style>
```

➡️ HTML5에서는 CSS를 가져올 때,  자동으로 타입을 체크하도록 되어있기 때문에 가능!

## MIME Type

- 위의 두 경우와 다르게, 어떤 타입의 파일이 오는지 명시해주어야 하는 경우에 해당됨

- [서버나 클라이언트 부분에서 오류가 발생하지 않고 파일을 인식하기 위한 방식](https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/MIME_types)

- text./html : 외부에서 가져온 html 파일임을 명시
- 오디오, 동영상 파일 등도 해당될 수 있음

⚠️ 잠깐! ⚠️

경로와 관련해서 

```html
<link href="style.css" rel="stylesheet">
```

와 같이 설정된 경우는 아래와 같이 ./ 가 앞에 생략된 구조다!

```html
<link href="./style.css" rel="stylesheet">
```

---

# 2. BASE 태그

> HTML <base> 요소는 문서 안의 모든 상대 URL이 사용할 기준 URL을 지정합니다. *[-mdn 문서-](https://developer.mozilla.org/ko/docs/Web/HTML/Element/base)*

- **base 태그에서 정한 경로 이하의 경로에서 검색해볼 수 있도록 함**
- 경로를 필요로 하는 태그 요소보다 앞에 위치하도록!(그리고 title 태그보다 앞에 있을 수록 좋음)
- 특정 경로를 기준으로 그 다음에 html 문서에서 경로를 요청할 시에 경로를 정할 경우 필요 ➡️ base 태그 이후의 상대경로 설정에 영향

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <base href="./RangeMeta/">
    <title>Document</title>
    <link href="main.css" rel="stylesheet">
</head>
<body>
    
</body>
</html>
```

- 장점 : 상대경로를 이용할 때 기준의 혼동이라던지, 타고 넘어가는 경로가 많아지는 불편함이 적음
- **HTML 문서에는 base는 한번만!**

**⚠️ 공통적으로 들어가는 주소의 끝에는 꼭!! / 를 붙여야 함!! ⚠️**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <base href="https://heropcode.github.io/GitHub-Responsive/"">
    <title>Document</title>
</head>
<body>
    <img src="img/bg-small.jpg" 
         alt="sample img">
    <img src="img/feature-tile__build.png" alt="sample2">

    <!--
        실질적으로
        https://heropcode.github.io/GitHub-Responsive/
        에서 확인해보면 url은
        https://heropcode.github.io/GitHub-Responsive 부분이
        공통적으로 묶여져서 생략되어 있음(./)

        이렇게 여러번 공통적으로 나타나는 주소를 base로 묶으면 좋음!
    -->
</body>
</html>
```