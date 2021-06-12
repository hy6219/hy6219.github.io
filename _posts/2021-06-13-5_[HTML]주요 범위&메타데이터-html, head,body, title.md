# 주요 범위&메타데이터-html, head,body, title

**Overview**

- 화면에 노출되는 컨텐츠와는 다르게, html 태그 밖에 존재하는 컨텐츠는 검색엔진에서 인식하기에 어려움이 있음
-html 태그에는 일반적으로, language라는 전역 속성을 사용 가능!

```html
<html lang="ko">
</html>
```

➡️ [ISO639-1에 해당되는 약어를 lang 속성에 작성해줄 수 있음!](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)

⚠️ 국가 표시 ≠ 언어 표시 !! (예: 한국 국가 표시- kr, 언어 표시: ko)

# 📌HTML 문서 내부에서 html, head, body 태그의 역할 📌

```html
<html lang="ko">
    <head>
				<!--HTML 문서의 제목-->
        <!--기타 정보(meta 태그)-->
        <!--내장 방식의 css-->
		    <!--외장 방식의 css를 연결-->
        📌**문서의 정보**📌
    </head>
    <body>
        📌**문서의 구조**📌
    </body>
</html>
```

➡️ HTML 태그 내부에서 문서의 범위를 나누어 놓고(html 태그로), 그 내부에 어떤 정보를 최적화해서 넣을 것인지(head, body)만 차이가 날 뿐!

---

## head 태그 영역에 들어갈 수 있는 태그! 정보!

### 1. title 태그 - 웹 페이지의 제목

```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        <!--문서의 정보-->
        <!--HTML 문서의 제목-->
        <title>HTML 요소 레퍼런스</title>
        
    </head>
    <body>
      
    </body>
</html>
```

### 2. meta 태그 - 기타 정보([참고자료](https://developer.mozilla.org/ko/docs/Web/HTML/Element/meta))

- 메타 태그는 빈 태그!
- 따라서! 속성을 가지지 않으면 역할을 완벽하게 수행할 수 없음!
- name,  content, charset 등과 같은 속성이 사용될 수 있음
- scheme 등의 속성은 html5에서 지원되지 않음!
- 아래의 경우와 같은 property 속성도 있는데, 이는 드물다. 알고만 있자!
— og: open graph ➡️SNS에서 특정 사이트에 있는 정보를 공유했을 때, 
공유된 정보를 그 사이트에서 체크할 때 필요한 속성에 대한 값!

```html
<meta property="og:title" content="~">
```

```html
<!DOCTYPE html>
<html lang="ko">
    <head>
        <!--문서의 정보-->
        <!--기타 정보(meta 태그)-->
				<!--인코딩-->
        <meta charset = "UTF-8">
        <!--HTML 문서의 제목-->
        <title>HTML 요소 레퍼런스</title>
    </head>
    <body>
        
    </body>
</html>
```

- 위의 경우, charset 속성은 [인코딩](https://en.wikipedia.org/wiki/Character_encoding)을 설정해주는 역할을 수행한다!
- **기본적으로 인코딩을 먼저 적용될 수 있도록 하기 위해서, title 태그는 meta 태그 뒤에 위치!**

- EUC-KR 방식은 완성형 조합으로 된 인코딩, UTF-8은 조합형 인코딩 방식이라고 명명할 수 있음

예시) "떡볶이" 라는 단어

➡️ 완성형: 떡 볶 이 

처럼 모든 완성된 문자에서 접근, 비교를 해야 함

➡️ 조합형: ㄸ ㅓ ㄲ .. 

처럼 초성, 중성, 종성을 따로따로 보았을 때 접근,비교

이러한 과정차이로 인해서 EUC-KR 인코딩 방식은 글자가 깨져보일 수 있음

그래서 한글, 한자를 포함하여 지원하고자 할 때에는, UTF-8을 지원하는 것이

좋음!(mozilla에서도 권장하는 바!)

> 페이지의 문자 인코딩을 선언합니다. 이 특성이 존재할 경우, 그 값은 반드시 문자열 "utf-8"의 대소문자 구분 없는 ASCII 표현이어야 합니다. *[-mdn 문서-](https://developer.mozilla.org/ko/docs/Web/HTML/Element/meta)*

### 2.1. meta 태그의 content 속성

- http-equiv 혹은 name 속성 중 어떤 것이 사용되는 지에 따라 해당 속성의 값을 가짐
- name 속성이 적용된 경우가 문서에 존재하면, name에 해당되는 값이 들어갈 것(예: 작성자 이름, 설명 내용)
- http-equiv 속성이 적용된 경우라면, 브라우저 환경 렌더링을 하기 위한 브라우저 버전이 content에 적용될 수 있음!

### 2.2 meta 태그의 http-equiv 속성

- 서버나 사용자의 환경에 대한 작동방식을 변경해주는 지시사항을 명시
- IE 브라우저에서 많이 사용됨(렌더링 방식)
- 일반적으로는 많이 명시해주는 편!
- http 통신 프로토콜에 특정 정보를 담아서 전달해주는 역할 수행!

[https://stackoverflow.com/questions/6771258/what-does-meta-http-equiv-x-ua-compatible-content-ie-edge-do](https://stackoverflow.com/questions/6771258/what-does-meta-http-equiv-x-ua-compatible-content-ie-edge-do)

```html
<!--
            최신의 인터넷 익스플로러 버전을 지원하도록 작성

        -->
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
```

- 위와 같이 사용되고는 하는데, 이는 IE 브라우저에서 최신 버전을 이용하여 환경을 렌더링해주기 위함이다!

## 2.3 meta 태그의 name 속성

- 정보를 지칭할 때, [정보의 종류](https://developer.mozilla.org/ko/docs/Web/HTML/Element/meta/name)가 무엇인지 표시

[1] [반응형 웹사이트를 위한 정보를 담음](https://www.w3schools.com/tags/tag_meta.asp)

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

- viewport: 화면에 정보를 렌더링하기 위한 영역
- width=device-width : 디바이스 화면 크기에 맞게 조정
- initial-scale=1.0 : 브라우저에서 페이지를 처음 로드할 때 초기

    확대/축소수준 설정

[2] author, description 값을 이용한 페이지의 작성자, 상세 설명 작성

```html
<!--meta- name 속성을 이용한 작성자, 상세 설명 내용-->
<meta name="author" content="고영희">
<meta name="description" content="고영희님의 사이트입니다!">
```

[meta 태그의 name 속성의 값](%E1%84%8C%E1%85%AE%E1%84%8B%E1%85%AD%20%E1%84%87%E1%85%A5%E1%86%B7%E1%84%8B%E1%85%B1&%E1%84%86%E1%85%A6%E1%84%90%E1%85%A1%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5-html,%20head,body,%20title%20a417d4c18e8344fd8c2044f1554734c9/meta%20%E1%84%90%E1%85%A2%E1%84%80%E1%85%B3%E1%84%8B%E1%85%B4%20name%20%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%E1%84%8B%E1%85%B4%20%E1%84%80%E1%85%A1%E1%86%B9%20319802c444e84df4b25e488962ba0d9e.csv)

# link 태그에 대해서

- 외부 리소스를 연결할 때 사용됨
- [다른 relationship도](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) 가능하지만, 우선 css만 연결해보면

```html
<link rel="stylesheet" href="~">
```

와 같이 작성하여 사용이 가능함!

- crossorigin 속성: html 5에서 새롭게 추가된 속성
-다른 도메인의 사이트 간에 데이터를 어떻게 공유하는 지를 기술하는 것
(CORS: Cross-Origin Resource Sharing)
- href 속성 : 링크된 리소스의 url[절대적 혹은 상대적 경로]
- hreflang 속성: 링크된 리소스의 언어(전역속성이었던 lang과 같은 언어라면, 생략해두어도 좋음! 하지만, 일반적으로는 생략!)
- [rel 속성: 링크된 리소스와 현재 문서와의 관계](https://developer.mozilla.org/en-US/docs/Web/HTML/Link_types)
-가장 많이 사용하는 값: stylesheet, icon

## 3. style 태그 : 내장 css를 넣는 부분!

```html
<style type="text/css">

</style>
```

ref: [https://developer.mozilla.org/en-US/docs/Web/HTML/Element/style](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/style)

- style 태그는 위와 같이 type속성에 text/css 값을 지니는데, 이 속성과 값은 생략해도 기본적으로 들어가 있음!
- 기본적으로 원래는! head 영역 내부에 있어야 하지만, body 영역에 있어도, 기능은 함 ➡️ 하지만! head 태그 내부에서 사용하자!(비효율적이기 때문!)
- scoped 속성은 IE, Opera, Safari 브라우저에서 지원되지 않음
- media 속성도 잘 사용하지 않음(나중에 css에서 미디어쿼리를 이용하기 때문)
- title 속성도 잘 사용하지 않음(전역속성)

# <!DOCTYPE html>의 의미

```html
**<!DOCTYPE html>**
<html lang="ko">
    <head>
        📌**문서의 정보**📌
    </head>
    <body>
        📌**문서의 구조**📌
    </body>
</html>
```

- <!DOCTYPE html> 이하에 작성된 부분은 html5로 해석하여 출력해달라는 의미