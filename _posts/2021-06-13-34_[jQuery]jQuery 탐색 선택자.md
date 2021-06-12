# jQuery 탐색 선택자

- 기본 선택자로 선택한 요소 중 원하는 요소를 한 번 더 탐색해서 좀 더 정확하게 선택 가능
- 제이쿼리 탐색 선택자의 종류
1. 위치 탐색 선택자 : 배열의 **인덱스**를 사용하여 선택
2. 속성 탐색 선택자 : 배열에 담겨진 요소 중 지정된 **속성과 값**으로 선택

### 1. 위치 탐색 선택자

## 🌺 위치 탐색 선택자

[위치 탐색 선택자 종류](jQuery%20%E1%84%90%E1%85%A1%E1%86%B7%E1%84%89%E1%85%A2%E1%86%A8%20%E1%84%89%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A2%E1%86%A8%E1%84%8C%E1%85%A1%20fae4a4d18b0b414e8a5866b312ff1e27/%E1%84%8B%E1%85%B1%E1%84%8E%E1%85%B5%20%E1%84%90%E1%85%A1%E1%86%B7%E1%84%89%E1%85%A2%E1%86%A8%20%E1%84%89%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A2%E1%86%A8%E1%84%8C%E1%85%A1%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2%20a6787cdfb1974faa976ce77079bf67f4.csv)

a. first, last 선택자

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(function(){
            //전체 li 요소 중 첫 번째 요소만 선택
            $("li:first").css("background-color","orange");
            //전체 li 요소 중 마지막 요소만 선택
            $("li").last().css("background-color","yellowgreen");
        })
    </script>
</head>
<body>
    <h1>탐색선택자</h1>
    <ul id="menu">
        <li>내용1</li>
        <li>내용2</li>
        <li>내용3</li>
        <li>내용4</li>
    </ul>
</body>
</html>
```

위와 같이, li:first 혹은 li:last 를 실행하게 된다면 전체에서 li 요소가 있는 곳은 아이디가 menu인 ul 태그 내부에 있는 경우만 있으므로, 내용1과 내용4가 선택된다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-first,%20last.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-first,%20last.PNG?raw=true)

탐색 선택자 first, last

b. even, odd 선택자

- even 선택자는 짝수 인덱스, 즉 사용자 입장에서는 홀수번째 요소를 선택하고
- odd 선택자는 홀수 인덱스, 즉 사용자 입장에서는 짝수번째 요소를 선택한다!

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(function(){
            //even 선택자-짝수 인덱스 요소 선택
            $("#menu li:even").css("background-color","violet");
            //odd 선택자-홀수 인덱스 요소 선택
            $("#menu li:odd").css("background-color","orange");
        })
    </script>
</head>
<body>
    <h1>탐색선택자</h1>
    <ul id="menu">
        <li>내용1</li>
        <li>내용2</li>
        <li>내용3</li>
        <li>내용4</li>
    </ul>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-even,odd.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-even,odd.PNG?raw=true)

jQuery - even, odd 선택자

즉, 위의 경우 인덱스가 0, 2 , 짝수인 경우(내용1, 내용 3)에 대해서는 바이올렛 색상으로 스타일링되고,

홀수인 경우(내용2, 내용 4)에 대해서는 오렌지 색상으로 스타일링된다!

c. eq(index), lt(index), gt(index) 탐색 선택자

$("요소선택: eq(index)")  또는 $("요소선택").eq(index)

eq 탐색 선택자는 선택한 요소 중 지정한 인덱스가 참조하는 요소만 선택

$("요소선택: lt(index)") 

lt 탐색 선택자는 선택한 요소 중 지정한 인덱스보다 작은 인덱스를 참조하는 요소만 선택

$("요소선택: gt(index)") 

gt 탐색 선택자는 선택한 요소 중 지정한 인덱스보다 큰 인덱스를 참조하는 요소만 선택

어떻게 본다면 lt와 gt는 전체 형동생 요소 선택자와 비슷한 개념인 것 같다!

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(function(){
            $("#menu li").eq(2).css("background-color","pink");
            $("#menu li:lt(2)").css("color","red");
            $("#menu li:gt(2)").css("color","indigo");
        })
    </script>
</head>
<body>
    <h1>탐색선택자</h1>
    <ul id="menu">
        <li>내용1</li>
        <li>내용2</li>
        <li>내용3</li>
        <li>내용4</li>
        <li>내용5</li>
    </ul>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-eq,lt,gt.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-eq,lt,gt.PNG?raw=true)

위의 경우에서처럼, 인덱스 2인 li 요소는 내용3이므로

eq(2)를 통해서는 내용3이 선택되고

lt(2)를 통해서는 인덱스가 2보다 작은 인덱스0,1인 요소 내용1, 내용2가 선택된다

그리고 gt(2)를 통해서는 인덱스가 2보다 큰 인덱스 3, 4인 내용4, 내용5가 선택된다

d. first-of-type, last-of-type 선택자

- first-of-type은 선택된 요소 무리 중 첫번째 요소를 선택
- last-of-type은 선택된 요소 무리 중 마지막 요소를 선택

한다는 특징이 있다!

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(function(){
            $("li:first-of-type").css("background-color","yellow");
            $("li:last-of-type").css("background-color","pink");
        })
    </script>
</head>
<body>
    <h1>탐색 선택자</h1>
    <ul>
        <li>내용1-1</li>
        <li>내용1-2</li>
        <li>내용1-3</li>
    </ul>
    <ul>
        <li>내용2-1</li>
        <li>내용2-2</li>
        <li>내용2-3</li>
    </ul>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-first-of-type,%20last-of-type.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-first-of-type,%20last-of-type.PNG?raw=true)

탐색 선택자 first-of-type, last-of-type

위의 경우에서, li 무리는 크게 내용1-x인 무리와 내용2-x인 무리 로 나뉘어 진다!

그러므로 각각에 대해서 첫번째 요소, 마지막 요소가 선택됨을 확인해볼 수 있다!

🌺 만약, 위의 경우에 덧붙여서

```html
$("li:first").css("color","red");
```

를 더한다면, 내용1-1만 선택되어 글자색이 빨간색으로 바뀌는 것을 확인해볼 수 있다!

e. nth-child(숫자 혹은 n), nth-last-of-type(숫자)

- nth-child는 n을 사용하면 선택한 요소의 무리들 중 zero-base가 적용되어 n=0,1,...을 적용했을 때 매개변수 인덱스 위치의 요소를 선택하게 된다
- 만약 nth-child 에게 전달된 값이 숫자라면, 선택한 요소의 무리들 중 숫자에 해당되는 요소를 선택한다
- nth-last-of-type은 선택한 요소의 무리들 중 뒤에서부터 숫자번째 요소를 선택한다

```html

```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-nth-child,%20nth-last-of-type.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-nth-child,%20nth-last-of-type.PNG?raw=true)

⭐CSS 선택자에서부터 처럼, 오른쪽부터 해석을 함이 바람직하다!

따라서 #menu1 li:nth-child(1)은 무리 중에서도 사용자 관점에서 바라보는 첫 번째 요소를 선택하는데(가상 클래스 선택자), 그 대상이 li 무리로 선택되고, 그 li 무리는 아이디가 menu1인 요소가 조상요소가 되는 경우를 선택한 것이다.

만약, #menu1:nth-child(1)과 같이 선택하고자 한다면,  ul 및 li 태그의 특성 상, 텍스트 노드가 중간에 존재하기 때문에, 원하는 요소를 정확하게 짚어낼 수 없다! 이점을 주의하자!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-nth-child,%20nth-last-of-type%20%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%9D%98%ED%95%A0%20%EC%82%AC%ED%95%AD.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-nth-child,%20nth-last-of-type%20%EC%97%90%EC%84%9C%20%EC%A3%BC%EC%9D%98%ED%95%A0%20%EC%82%AC%ED%95%AD.PNG?raw=true)

선택자 사용시 주의할 사항

e. only-child, slice(index)선택자

- only-child는 특정 요소 무리를 기준으로 살펴보았을 때 그 밑에 자식요소가 단 1개만 존재하면, 그 자식요소를 선택한다
- slice(index) 선택자는

$("요소선택").slice(시작인덱스, 마지막인덱스)

로 사용하면서, [시작인덱스, 마지막인덱스) 범위에 존재하는 요소들을 선택한다!

장점이라함은, 형이나 동생 등의 요소가 아니더라도 , 특정 인덱스보다 큰경우, 작은 경우에 국한되지 않고 원하는 범위 만큼만 선택할 수 있다는 점일 것 같다!

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(function(){
            //only-child
            $("li:only-child").css({
                "background-color":"yellow",
                "border":"3px dashed purple"
            });
            //slice(index)
            $("#menu1 li").slice(0,3).css("color","red");
        })
    </script>
</head>
<body>
    <h1>탐색 선택자</h1>
    <ul id="menu1">
        <li>내용1-1</li>
        <li>내용1-2</li>
        <li>내용1-3</li>
        <li>내용1-4</li>
    </ul>
    <ul id="menu2">
        <li>내용2-1</li>
    </ul>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-only%20child,%20slice.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%ED%83%90%EC%83%89%EC%84%A0%ED%83%9D%EC%9E%90-only%20child,%20slice.PNG?raw=true)

탐색 선택자 only-child , slice(시작,끝)

위의 경우, li 무리 중 자식 요소가 1개뿐인 경우, 그 자식 요소를 선택한다

그리고 인덱스 0부터 2까지의 요소를 선택하는데, 그 대상이 li 무리인 경우를 선택하게 되어

위와 같이 스타일링이 적용된다

### 2. 제이쿼리 배열 관련 메서드

[제이쿼리 배열 관련 메서드의 종류](jQuery%20%E1%84%90%E1%85%A1%E1%86%B7%E1%84%89%E1%85%A2%E1%86%A8%20%E1%84%89%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A2%E1%86%A8%E1%84%8C%E1%85%A1%20fae4a4d18b0b414e8a5866b312ff1e27/%E1%84%8C%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%8F%E1%85%AF%E1%84%85%E1%85%B5%20%E1%84%87%E1%85%A2%E1%84%8B%E1%85%A7%E1%86%AF%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%A7%E1%86%AB%20%E1%84%86%E1%85%A6%E1%84%89%E1%85%A5%E1%84%83%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2%207072beb4b24544968b3c809379c7c691.csv)

a. each(), $.each() 메서드

[USAGE]

1. 배열에 저장된 요소의 갯수만큼 메서드 반복 실행, 매개변수1&& 매개변수2에는 배열에 저장된 요소와 인덱스 값이 각각 저장되어 오름차순으로 배열과 대응됨

$("요소선택").each(function(매개변수1,매개변수2){})

$.each($("요소선택").function(매개변수1,매개변수2){})

2. 배열에 저장된 요소의 갯수만큼 메서드를 반복 실행하는데, 실행될 때마다 this에 배열에 저장된 요소가 오름차순으로 대응됨

$("요소선택").each(function(){$(this)})

$.each($("요소선택"), function(){${this)})

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
       
        $(()=>{
            const obj=[
                {
                    "area":"서울"
                },
                {
                    "area":"부산"
                },
                {
                    "area":"전주"
                }
            ];

            $(obj).each((i,o)=>{
                console.log(`${i} : `,o);
            });

            console.log("==The End1==");

            $.each($(obj),(i,o)=>{
                console.log(`${i} : `,o);
            });

            console.log("==The End2==");

            $(obj).each((i)=>{
                console.log(i,$(this));
            });
            console.log("==The End3==");

            $.each($(obj),(i)=>{
                console.log(i,$(this));
            });

            console.log("==The End4==");
            
        });
    </script>
</head>
<body>
    
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/each%ED%95%A8%EC%88%98.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/each%ED%95%A8%EC%88%98.PNG?raw=true)

위의 경우에서 조심해야할 점은, $(this)로 받게 되면 this에 배열이 상응되어 저장되어, 객체가 저장되어 보인다!

⭐ map, grep 과의 차이점은 each는 자바스크립트 내부의 배열 뿐 아니라, DOM 객체에 대해서도 적용될 수 있다는 점이다!

b. $.map(), $.grep() 메서드

[USAGE]

```jsx
$.map(Array, function(매개변수1, 매개변수2){
	return 데이터;
});

$.grep(Array,function(매개변수1, 매개변수2){
	return [true|false];
}
```

map과 grep의 차이는 map은 데이터를 반환하고, grep은 true 나 false를 반환한다는 점이다

그리고 공통점은 리턴되는 값에 따른 배열을 반환한다는 점이다

⭐ 그리고 매개변수는 앞에서부터 데이터와 인덱스가 저장된다(each와 반대!)

```jsx
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(()=>{
            class emp{
                constructor(area, name){
                    this.area = area;
                    this.name = name;
                }
            }

            const arr1 = [
                    new emp("서울","무대리"),
                    new emp("부산","홍과장"),
                    new emp("대전","박사장"),
                    new emp("서울","빅마마")
            ];
            const resArr = $.map(arr1, (i,j)=>{
                console.log(i,j);
                if(i.area ==="서울"){
                    return i;
                }
            });
            console.log(resArr);
            console.log("==first end==");

            const resGrep = $.grep(arr1,(i,j)=>{
                console.log(i,j);
                if(i.area==='서울'){
                    return true;
                }else{
                    return false;
                }
            });
            console.log(resGrep);
            
        });
    </script>
</head>
<body>
    
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/map%EA%B3%BC%20grep%20%ED%95%A8%EC%88%98.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/map%EA%B3%BC%20grep%20%ED%95%A8%EC%88%98.PNG?raw=true)

위의 예에서 보다시피, 매개변수1에는 데이터, 매개변수2에는 인덱스가 들어갔음을 확인해볼 수 있다

그리고, 차이점은 return 데이터 혹은 return boolean값; 이라는 점을 확인해볼 수 있다!

마지막으로 공통점은 리턴되는 상태에 따라서 새로운 배열이 반환된다는 점이다!

c. $.inArray(), $.isArray(), $.merge() 메서드

[USAGE]

```jsx
$.inArray(data, Array, start index)

$.isArray(object)

$.merge(Array1,Array2)
```

먼저 inArray 메서드는 지정한 데이터를 인덱스 오름차순으로 찾아서 먼저 찾은 데이터의 인덱스 값을 반환한다

isArray는 지정 객체가 배열객체일 경우에만 true를 반환한다

merge는 두 배열을 하나의 배열 객체로 묶는다

```jsx
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(()=>{
            class emp{
                constructor(area, name){
                    this.area = area;
                    this.name = name;
                }
            }

            const arr1 = [
                    new emp("서울","무대리"),
                    new emp("부산","홍과장"),
                    new emp("대전","박사장"),
                    new emp("서울","빅마마")
            ];

            const res1 = $.isArray(arr1);
            console.log(`arr1은 배열입니까? ${res1}`);
            const arr2 = ["서울","대전","부산","대구","인천"];
           
            const res2 = $.inArray("부산",arr2,0);
            console.log(res2);
            const res3 = $.inArray("부산",arr2,3);
            console.log(res3);

            const merge = $.merge(arr1,arr2);
            console.log(merge);
            
            
        });
    </script>
</head>
<body>
    
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/isArray,inArray,merge.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/isArray,inArray,merge.PNG?raw=true)

위의 예제로 해당 함수를 다시 살펴볼 수 있는데, 주의할 점이 있다면,

inArray의 data 부분에는 객체가 아닌 단순 값이 들어가야 하며,

시작 인덱스를 생략하게 된다면 자동으로 시작 인덱스가 0으로 지정되어 있다는 점이다!

위의 경우, 인덱스0부터 부산을 찾으면 첫번째로 등장한 인덱스 2를 반환하는데,

인덱스3부터 찾기 시작하면 존재하지 않으므로 -1을 반환한다

그리고 merge의 경우, 왼쪽부터 차례대로 [매개변수1 배열, 매개변수2 배열] 로 병합된 것을 확인해볼 수 있다!

d. index 메서드

- DOM 객체에 대해서 선택된 요소 그룹에서 지정 선택 요소에 대해서 인덱스 값을 반환

$("요소선택").index("지정 선택 요소");

$("요소 선택").index();

```jsx
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(()=>{
            const idx = $("#list3").index();
            const idx2 = $("li").index($("#list3"));
       
            console.log(idx,idx2);//2 2

        });
    </script>
</head>
<body>
    <h1>배열 관련 함수</h1>
    <ul>
        <li>내용1</li>
        <li>내용2</li>
        <li id="list3">내용3</li>
        <li>내용4</li>
    </ul>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/index%20%ED%95%A8%EC%88%98.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/index%20%ED%95%A8%EC%88%98.PNG?raw=true)

위와 같이 아이디가 list3인 요소에 대해서 인덱스를 알아보고자 한다면,

아이디 선택자로 선택한 후, 인덱스 메서드를 이용해도 좋고,

태그 선택자로 선택한 후, 선택요소를 매개변수로 하는 index 메서드를 사용해도 좋다!

### 3. 속성 탐색 선택자

[속성 탐색 선택자 종류](jQuery%20%E1%84%90%E1%85%A1%E1%86%B7%E1%84%89%E1%85%A2%E1%86%A8%20%E1%84%89%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A2%E1%86%A8%E1%84%8C%E1%85%A1%20fae4a4d18b0b414e8a5866b312ff1e27/%E1%84%89%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A5%E1%86%BC%20%E1%84%90%E1%85%A1%E1%86%B7%E1%84%89%E1%85%A2%E1%86%A8%20%E1%84%89%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A2%E1%86%A8%E1%84%8C%E1%85%A1%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2%20c342066995db4464a79f958e8898b3ff.csv)

🌺 속성과 값에 따른 탐색 선택자

(1) 해당 속성을 가진 요소 선택

$("요소선택[속성]")

(2)  해당 속성의 값이 주어진 값과 일치하는 경우에 대한 요소 선택

$("요소선택[속성=값]") 혹은 $("요소선택[속성~=값]")

(3) 해당 속성의 값이 주어진 값으로 시작되는 경우 선택

$("요소선택[속성^=텍스트]")

(4) 해당 속성의 값에 주어진 값이 중간에 포함된 경우 선택

$("요소선택[속성*=텍스트]")

(5) 해당 속성의 값에서 주어진 값이 마지막으로 나오는 경우 선택

$("요소선택[속성$=텍스트]")

(6) input 태그의 type 값이 주어진 값인 경우 선택

$(";type 속성값")

아래의 예제와 그 결과를 확인해보자

```jsx
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(()=>{
            //해당 속성을 가진 경우만 선택
            $("a[target]").css({
                "width":"300px",
                "background":"yellow"
            });
            $("a[target]").after(" 해당 속성을 가진 경우만 선택");

            //.com으로 끝나는 경우만 선택
            $("a[href$='.com']").css("background-color","pink");

            //https://로 시작하는 경우 선택
            $("a[href^='https://']").css("color","orangered");
            //중간에 goo가 들어간 경우 선택
            $("a[href*='goo']").css("border","2px solid purple");
            //https://www.naver.com인 경우 선택
            $("a[href='https://www.naver.com']").css("border","3px dashed yellowgreen");
            //https://www.daum.net 인 경우 선택
            $("a[href~='https://www.daum.net']").css("border","3px dashed indigo");
            //input type이 password인 경우 선택
            $(":password").css("background-color","pink");
        });
    </script>
</head>
<body>
    <div id="wrap">
        <p><a href="https://easyspub.co.kr" target="_blank">EasysPub</a></p>
        <p><a href="https://www.google.com">Google</a></p>
        <p><a href="https://www.naver.com">Naver</a></p>
        <p><a href="https://www.daum.net">Daum</a></p>
    </div>
    <form action="#" method="get" id ="member_f">
        <p><label for="userId">아이디</label><input type="text"name="userId" id="userId"></p>
        <p><label for="pw">비밀번호</label><input type="password" name="pw" id="pw"></p>
    </form>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%86%8D%EC%84%B1%EA%B3%BC%20%EA%B0%92%EC%97%90%20%EB%94%B0%EB%A5%B8%20%ED%83%90%EC%83%89%20%EC%84%A0%ED%83%9D%EC%9E%90.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%86%8D%EC%84%B1%EA%B3%BC%20%EA%B0%92%EC%97%90%20%EB%94%B0%EB%A5%B8%20%ED%83%90%EC%83%89%20%EC%84%A0%ED%83%9D%EC%9E%90.PNG?raw=true)

먼저 a태그의 href 속성값이 .com으로 끝나는 경우를 선택하여 분홍색 배경을 갖도록 지정해주었다

그 후에는 a태그의 href 속성값이 https://로 시작하는 경우 orangered 컬러의 글자색으로 스타일링해주었다

그리고 goo를 a 태그의 href 속성값 중간에 포함하는 경우를 찾아보았고,

a href 속성값이 특정 도메인과 일치하는 경우를 선택하였다

그 후에는 type이 password인 input 태그를 선택하였다.

🌺 속성 상태에 따른 탐색 선택자

(1)$("요소선택:[visible|hidden]") - 선택한 요소 중 보이는 상태(visible)혹은 숨겨진 상태(hidden)의 요소만 선택

(2)$(":selected") - 선택상자 중 현재 선택된 옵션 요소만 선택

(3)$(":checked") - 체크박스나 라디오 버튼 요소 중 체크된 요소만 선택

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(()=>{
            //visible요소 선택
            $("#wrap p:visible").css({
                "background-color":"yellow",
                "color":"red",
                "width":"300px",
                "font-weight":"bold"
            });
            //hidden요소 선택
            $("#wrap p:hidden").css({
                "display":"block",
                "background-color":"pink",
                "color":"blue",
                "width":"300px",
                "font-weight":"bold"
            });
            //selected 요소에 대해서 선택
            const sel=$("#zone1 :selected").val();
            console.log(sel);

            //checked 요소에 대해서 선택
            const checked1 = $("#zone2 :checked").val();
            console.log(checked1);

            const checked2 = $("#zone3 :checked").val();
            console.log(checked2);
        })
    </script>
<body>
    <div id="wrap">
        <p>내용1</p>
        <p style="display:none;">내용2</p>
        <p>내용3</p>
    </div>
    <form action="#">
        <p id="zone1">
            <select name="course" id="course">
                <option value="opt1">옵션1</option>
                <option value="opt2"selected>옵션2</option>
                <option value="opt3">옵션3</option>
            </select>
        </p>
        <p id="zone2">
            <input type="checkbox" name="hobby1" value="독서" checked>독서
            <input type="checkbox" name="hobby2" value="자전거">자전거
            <input type="checkbox" name="hobby3" value="등산">등산
        </p>
        <p id="zone3">
            <input type="radio" name="gender" value="male">남성
            <input type="radio" name="gender" value="female" checked>여성
        </p>
    </form>
</body>
</html>
```

⭐ jQuery에서는 

.val()

메서드를 통해서 요소의 value 속성값을 가져올 수 있다!

위에서는 아이디 이름이 zone1 인 요소 중 선택된 경우, zone2 및 zone3인 요소 중 체크된 경우에 대해서 val() 메서드를 통해서 value 속성값을 가져와서 콘솔에 그 값이 출력된다

그리고 id가 wrap인 요소 내부에 있는 visible 및 hidden 상태의 요소를 각각 선택하여 아래와 같이 스타일을 적용하게 된다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%86%8D%EC%84%B1%EC%83%81%ED%83%9C%EC%97%90%20%EB%94%B0%EB%A5%B8%20%ED%83%90%EC%83%89%20%EC%84%A0%ED%83%9D%EC%9E%90.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%86%8D%EC%84%B1%EC%83%81%ED%83%9C%EC%97%90%20%EB%94%B0%EB%A5%B8%20%ED%83%90%EC%83%89%20%EC%84%A0%ED%83%9D%EC%9E%90.PNG?raw=true)

🌟 name이 ~인 input 태그 중 선택되지 않은 경우를 확인하려면 길이가 0인지 확인하면 될 것!

if($("input[name=~]:checked).length===0)

### 4. 콘텐츠 탐색 선택자

[콘텐츠 탐색 선택자의 종류](jQuery%20%E1%84%90%E1%85%A1%E1%86%B7%E1%84%89%E1%85%A2%E1%86%A8%20%E1%84%89%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A2%E1%86%A8%E1%84%8C%E1%85%A1%20fae4a4d18b0b414e8a5866b312ff1e27/%E1%84%8F%E1%85%A9%E1%86%AB%E1%84%90%E1%85%A6%E1%86%AB%E1%84%8E%E1%85%B3%20%E1%84%90%E1%85%A1%E1%86%B7%E1%84%89%E1%85%A2%E1%86%A8%20%E1%84%89%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A2%E1%86%A8%E1%84%8C%E1%85%A1%E1%84%8B%E1%85%B4%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2%202ac709d60c8d4a02945b845365dc8270.csv)

(예시- contains(), contents(), has(), not(), end() 탐색 선택자)

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(()=>{
            //선택한 p 요소 중 내용1을 포함하는 요소만 선택
            $("#inner_1 p:contains('내용1')").css({
                "border":"1px solid red",
                "width":"300px"          
            });
            //선택한 p 요소 중 strong 요소를 포함하는 요소만 선택
            $("#inner_1 p:has('strong')").css({
                "background-color":"yellow",
                "width":"300px"
            });
            //선택한 p요소 중 가장 가까운 하위 요소 선택
            $("#inner_1").contents().css({
                "margin-left":"100px",
                "border":"2px dashed purple"
            });
            //선택한 p 요소 중 마지막 요소가 아닌 경우만 선택
            $("#inner_2 p").not(":last").css({
                "margin-left":"50px",
                "background-color":"pink",
                "width":"300px",
                "padding":"5px"
            });
            //필터링 적용되기 전 요소를 선택
            $("#inner_2 p").not(":last").end().css({
                "border":"1px solid orangered",
                "border-radius":"10px"
            });
        })
    </script>
<body>
    <div id="outer_wrap">
        <h1>콘텐츠 탐색 선택자</h1>
        <section id="inner_1">
            <h2>contains(), contents(),has()</h2>
            <p><span>내용1</span></p>
            <p><strong>내용2</strong></p>
            <p><span>내용3</span></p>
        </section>
        <section id="inner_2">
            <h2>not(),end()</h2>
            <p>내용4</p>
            <p>내용5</p>
            <p>내용6</p>
        </section>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%BD%98%ED%85%90%EC%B8%A0%20%ED%83%90%EC%83%89%20%EC%84%A0%ED%83%9D%EC%9E%90.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%BD%98%ED%85%90%EC%B8%A0%20%ED%83%90%EC%83%89%20%EC%84%A0%ED%83%9D%EC%9E%90.PNG?raw=true)

먼저 내용1이라는 텍스트를 가진 요소를 contains를 이용해서 선택하게 되고, 이에 따라서 빨간색 테두리가 적용된다

그리고 inner_1 아이디 요소의 하위 요소인 p 중 strong 요소를 포함하는 경우를 has 를 이용해서 선택하여, 노란색 배경으로 설정하게 된다

그 다음은, contents()를 이용하여 inner_1 아이디 요소의 하위 요소 중 가장 가까운 요소를 선택하고, 왼쪽 마진값을 설정해준다

그 다음은 not(),end()라고 굵게 표시된 부분을 확인해보자

먼저 not을 사용하게 되면 not에 전달되는 선택자로 선택되는 요소 외의 요소들이 선택된다

그 결과, 내용 4와 내용 5 부분이 분홍색 배경으로 칠해지게 된다

마지막으로 end()는 요소를 선택한 후 end를 사용하기 전에 필터링되는 메서드가 있었다면 이를 취소하고 원래 선택했던 요소를 선택하는 것이다! 즉, 위의 경우에는 inner_2 아이디 인 요소의 하위 요소 p 요소들이 선택되어 orangered 색의 테두리가 적용된다

(예시-find,filter)

얼핏보면 find는 has와 유사한 듯 보이나, has는 선택된 요소 중 해당 하위 요소가 존재하는 경우, 그 선택요소를 선택하는 것이고!

find는 선택된 요소 중 해당 요소의 하위 요소 중 필터링할 요소를 선택하여 그 하위 요소를 선택하는 것이다!

$("요소선택").find("하위 요소 중 필터링할 요소 선택")

$("요소선택").filter("선택한 요소 중 필터링할 요소 선택")

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
    <script src="./js/jquery-3.6.0.min.js"></script>
    <script>
        $(()=>{
            $("#inner_1").find(".txt1").css("color","red");
            $("#inner_1 div").filter(".txt2").css("background-color","yellow");
            $("#inner_2 p").filter((idx,obj)=>{
                console.log(idx);
                return idx%2==0;
            }).css("background-color","orange");
        })
    </script>
<body>
    <div id="outer_wrap">
        <h1>콘텐츠 탐색 선택자</h1>
        <section id="inner_1">
            <h2>find(),filter()</h2>
            <div class="txt1">내용1</div>
            <div class="txt2">내용2</div>
        </section>
        <section id="inner_2">
            <h2>filter(function)</h2>
            <p>index0</p>
            <p>index1</p>
            <p>index2</p>
            <p>index3</p>
        </section>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%BD%98%ED%85%90%EC%B8%A0%20%ED%83%90%EC%83%89%20%EC%84%A0%ED%83%9D%EC%9E%90-find,filter.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%BD%98%ED%85%90%EC%B8%A0%20%ED%83%90%EC%83%89%20%EC%84%A0%ED%83%9D%EC%9E%90-find,filter.PNG?raw=true)

위와 같이 find와 filter는 선택된 요소를 기준으로 필터링할 요소를 선택하게 된다

다만, filter는 내부에 함수가 들어갈 수도 있다! 그리고 차이점이 뚜렷해보이지는 않지만, 의미상으로,

find는 하위 요소 중에서 , filter는 선택한 요소 중에서 필터링할 요소를 선택하는 것이다!

### 5. 선택자와 함께 알아두면 유용한 메서드

[메서드 종류](jQuery%20%E1%84%90%E1%85%A1%E1%86%B7%E1%84%89%E1%85%A2%E1%86%A8%20%E1%84%89%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A2%E1%86%A8%E1%84%8C%E1%85%A1%20fae4a4d18b0b414e8a5866b312ff1e27/%E1%84%86%E1%85%A6%E1%84%89%E1%85%A5%E1%84%83%E1%85%B3%20%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%85%E1%85%B2%208c2c41eb08274d5783b0de249f94c0e7.csv)

*noConflict 참고: [https://www.codingfactory.net/10795](https://www.codingfactory.net/10795)

*get에 매개변수가 없다면 해당되는 요소를 전부 반환하게 될 것!
매개변수로 숫자를 넣으면 , 해당 숫자 인덱스를 선택하게 될 것!

[https://api.jquery.com/get/#get2](https://api.jquery.com/get/#get2)

🌟 is 메서드에서 확인해볼 수 있는 상태 

:[checked | selected |visible |hidden |animated]

다른 상태들은 한번씩 확인해보았는데 animated는 처음 보는 상태인데, 이는 

선택 요소가 애니매이션 동작 상태이면 true, 아니면 false를 반환해준다!

```html
<!DOCTYPE html>
<html lang="ko">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<script src="./js/jquery-3.6.0.min.js"></script>
<script>
    $(()=>{
        const res1=$("#inner_1 p").eq(0).is(":visible"),
              res2=$("#inner_1 p").eq(1).is(":hidden"),
              res3=$("#inner_2 :checkbox").eq(1).is(":checked");
        console.log(res1);
        console.log(res2);
        console.log(res3);
    })
</script>

<body>
    <div id="outer_wrap">
        <h1>is()</h1>
        <section id="inner_1">
            <h2>문단 태그 영역</h2>
            <p>내용1</p>
            <p style="display:none;">내용2</p>
        </section>
        <section id="inner_2">
            <h2>폼 태그 영역</h2>
            <p>
                <label for="ch1">체크1</label>
                <input type="checkbox" name="chk1" id="chk1" checked>
                <label for="ch2">체크2</label>
                <input type="checkbox"name="chk1"id="chk2"></p>
        </section>
    </div>
</body>

</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%84%A0%ED%83%9D%EC%9E%90%EC%99%80%20%ED%95%A8%EA%BB%98%20%EC%95%8C%EC%95%84%EB%91%90%EB%A9%B4%20%EC%9C%A0%EC%9A%A9%ED%95%9C%20%EB%A9%94%EC%84%9C%EB%93%9C.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/Javascript/jQuery/%EC%84%A0%ED%83%9D%EC%9E%90%EC%99%80%20%ED%95%A8%EA%BB%98%20%EC%95%8C%EC%95%84%EB%91%90%EB%A9%B4%20%EC%9C%A0%EC%9A%A9%ED%95%9C%20%EB%A9%94%EC%84%9C%EB%93%9C.PNG?raw=true)

is 메서드만 확인해보자

위의 경우, 아이디가 inner_1인 요소에 존재하는 요소들 중 인덱스가 1인 요소는 hidden으로 되어 있고 나머지는 visible하다. 따라서 인덱스 1을 선택한 res2에 대해서 hidden인지 물어본다면 당연히 true를 반환할 것이고, 인덱스 1이 아닌 요소를 선택한 res1에 대해서 visible한지 물어본다면 당연히 true를 반환할 것이다

그리고 체크박스의 경우에는, 체크1, 즉 인덱스 0인 경우에 체크되어 있기 때문에 res3에서처럼 인덱스1(체크2)에 체크되어 있는지 묻는다면 false를 반환한다

- add(선택자): 선택된 요소 뿐 아니라 선택자 요소도 선택하여 같이 적용

$("선택요소").add(선택자)

[https://www.codingfactory.net/10190](https://www.codingfactory.net/10190)