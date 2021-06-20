# CSS-Flex

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex_basic.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex_basic.png?raw=true)

CSS flex 개념

Flex는 크게

1. 부모 요소로써의 의미가 강한 container
2. container 내부에 정렬될 아이템들 items

로 나뉘어지고, 각각이 사용가능한 CSS 속성은 위의 그림과 같다

🌺 Flex Container 를 위한 속성들 🌺

1. display:flex 혹은 display:inline-flex 
- flex 디스플레이를 지정함으로써 flex container를 정의

flex와 inline-flex의 차이는

- flex는 container를 블록요소로 디스플레이하게 하는 것 같고
- inline-flex는 container를 인라인 요소로 디스플레이하게 하는 것 같다는 점이다

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container{
            width: 500px;
            height: 300px;
            border:3px solid orangered;
            display:flex;
            justify-content: left;/*수평정렬*/
            align-items: center;/*수직정렬*/
            text-align: center;
        }
        .container .item{
            width: 100px;
            height: 100px;
            border:2px solid lightgray;
            border-radius: 10px;
            margin-right:5px;
        }
        .inline-container{
            width: 500px;
            height: 300px;
            border:3px solid orangered;
            display:inline-flex;
            justify-content: left;/*수평정렬*/
            align-items: center;/*수직정렬*/
            text-align: center;
        }
        .inline-container .item{
            width: 100px;
            height: 100px;
            border:2px solid lightgray;
            border-radius: 10px;
            margin-right:5px;
        }
    </style>
</head>
<body>
    <p>flex</p>
    <div class="container">
        <div class="item">item</div>
        <div class="item">item</div>
        <div class="item">item</div>
    </div>
    <div class="container">
        <div class="item">item</div>
        <div class="item">item</div>
        <div class="item">item</div>
    </div>
    <p>inline-flex</p>
    <div class="inline-container">
        <div class="item">item</div>
        <div class="item">item</div>
        <div class="item">item</div>
    </div>
    <div class="inline-container">
        <div class="item">item</div>
        <div class="item">item</div>
        <div class="item">item</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/display%20flex.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/display%20flex.PNG?raw=true)

display:flex

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/display%20inline-flex.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/display%20inline-flex.PNG?raw=true)

display:inline-flex

이레 그림에서 보여지는 것처럼,  display:flex를 지정한 container는 블록요소처럼 하나의 공간을 점유하고 display:inline-flex를 지정한 container는 인라인 요소처럼 수평으로 정렬됨을 확인해볼 수 있다

2. flex-flow

- 단축 속성
- Flex items의 주축(main-axis)를 설정, items의 여러 줄 묶음(줄 바꿈) 설정
- flex-direction(개별속성) : items의 주축 설정(기본값 row)
- flex-wrap(개별속성) : items의 여러 줄 묶음(줄 바꿈) 설정(기본값 nowrap)

[USAGE]

flex-flow: 주축 여러줄묶음;

먼저 flex-direction부터 확인해보자

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container1{
            display:flex;
            width: 500px;
            border:1px solid red;
        }
        .container1 .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            margin:10px;
        }
        .container2{
            display:flex;
            flex-direction: row-reverse;
            width: 500px;
            border:1px solid red;
        }
        .container2 .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            margin:10px;
        }
        .container3{
            display:flex;
            flex-direction: column;
            width: 500px;
            height: 500px;
            border:1px solid red;
        }
        .container3 .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            margin:10px;
        }
        .container4{
            display:flex;
            flex-direction: column-reverse;
            width: 500px;
            height: 500px;
            border:1px solid red;
        }
        .container4 .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            margin:10px;
        }
    </style>
</head>
<body>
    <p>flex-direction:row</p>
    <div class="container1">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
    </div>
    <p>flex-direction:row-reverse</p>
    <div class="container2">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
    </div>
    <p>flex-direction:column</p>
    <div class="container3">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
    </div>
    <p>flex-direction:column-reverse</p>
    <div class="container4">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-direction.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-direction.png?raw=true)

flex-direction 속성

[flex-directon의 속성값](https://www.notion.so/f79c6cd2ab0e48bc8db1d44e24902882)

위의 예시에서 확인해볼 수 있듯이, 해당 속성을 사용하지 않으면 row 속성값이 기본값이기 때문에 왼쪽부터 오른쪽 방향으로 차례대로 1, 2, 3 이 표시됨을 확인해볼 수 있다

row-reverse값을 적용한 경우, 오른쪽 끝에서부터 왼쪽 방향으로 천천히 1, 2, 3이 표시됨을 확인해볼 수 있다

column의 경우, 위쪽부터 아래쪽 방향으로 1,2,3이 colum-reverse의 경우 아래쪽 끝부터 위쪽방향으로 1, 2, 3이 표시됨을 확인해볼 수 있다

🌟 row, row-reverse는 수평축!
column, column-reverse는 수직축으로 표시!

📌🌟 Flex 주축(main-axis)과 교차축(cross-axis), 시작점과 끝점 🌟📌

- 위에서 살펴본 것처럼, 주축이 고정되어 있지 않아서 이에 대한 교차축은 상대적일 수밖에 없다!!

🐣 주축==수평축 ▶️ 교차축==수직축

🐣 주축==수직축 ▶️ 교차축==수평축

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/%EC%A3%BC%EC%B6%95%EA%B3%BC%20%EA%B5%90%EC%B0%A8%EC%B6%95.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/%EC%A3%BC%EC%B6%95%EA%B3%BC%20%EA%B5%90%EC%B0%A8%EC%B6%95.png?raw=true)

flex 주축과 교차축

- 시작점(main-start)과 끝점(main-end)는 주축을 기준으로 왼/오른쪽 부분(수직축이 주축이 되는 column-xxx는 주축입장에서의 왼쪽과 오른쪽이 우리가 보았을 때 위아래가 된다)이 지정될 수 있는데 시작점은 첫번째 아이템이 표시되는 부분이 해당된다

🌟 즉, 아이템의 시작하는 부분에 따라 시작점이 결정된다!

시작점=main-start=flex-start

끝점=main-end=flex-end

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/%EC%A3%BC%EC%B6%95%EA%B3%BC%20%EA%B5%90%EC%B0%A8%EC%B6%95,%20%EC%8B%9C%EC%9E%91%EC%A0%90%EA%B3%BC%20%EB%81%9D%EC%A0%90.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/%EC%A3%BC%EC%B6%95%EA%B3%BC%20%EA%B5%90%EC%B0%A8%EC%B6%95,%20%EC%8B%9C%EC%9E%91%EC%A0%90%EA%B3%BC%20%EB%81%9D%EC%A0%90.png?raw=true)

flex 주축,교차축, 시작점, 끝점

3. flex-wrap

- items의 여러줄 묶음(줄 바꿈) 설정

[flex-wrap의 속성값](https://www.notion.so/bf9573ddd64b451fa3d0409b707a7a7c)

주의할 점은 , nowrap은 마치 box-sizing:border-box가 적용된 것처럼 아이템 1개 크기가 조절되는데(border까지 포함한 너비로) 아무리 box-sizing:content-box로 바꾸어보려 해도 바뀌지 않는다

이는 flex-basis값이 auto로 기본설정되어있기 때문이다!

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container1{
            display:flex;
            width: 500px;
            border:1px solid red;
        }
        .container1 .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            margin:10px;
        }
        .container2{
            display:flex;
            width: 500px;
            flex-wrap: wrap;
            border:1px solid red;
        }
        .container2 .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            margin:10px;
        }
        .container3{
            display:flex;
            flex-wrap: wrap-reverse;
            width: 500px;
            border:1px solid red;
        }
        .container3 .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            margin:10px;
        }
    </style>
</head>
<body>
    <p>nowrap</p>
    <div class="container1">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>wrap</p>
    <div class="container2">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>wrap-reverse</p>
    <div class="container3">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-wrap.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-wrap.PNG?raw=true)

flex-wrap

4. justify-content

- 주축의 정렬 방법을 설정

[justify-content의 속성값](https://www.notion.so/75d546b958b5415486df39e883ae5fae)

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            border:2px dashed blue;
        }
        .container1{
            display:flex;
            justify-content: flex-start;
        }
        .container2{
            display:flex;
            justify-content: flex-end;
        }
        .container3{
            display: flex;
            justify-content: center;
        }
        .container4{
            display: flex;
            justify-content: space-between;
        }
        .container5{
            display:flex;
            justify-content: space-around;
        }
    </style>
</head>
<body>
    <p>flex-start</p>
    <div class="container1">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>flex-end</p>
    <div class="container2">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>center</p>
    <div class="container3">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>space-between</p>
    <div class="container4">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>space-around</p>
    <div class="container5">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/justify-content.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/justify-content.PNG?raw=true)

주축을 기준으로 아이템을 정렬하는 justify-content

위의 경우에는 따로 flex-direction을 설정해두지 않았기 때문에 flex-direction:row로 되어있고, 그렇기 때문에 시작점은 왼쪽, 끝점은 오른쪽이 된다. 그렇기 때문에 flex-start는 왼쪽부터 차례대로, flex-end는 오른쪽부터 차례대로 보여진다. 그리고 center는 그 중간에 보여진다.

다만, space-between과 space-around의 큰 차이점이 주목할만하다. 위의 그림에서 보듯이, space-between은 아이템의 시작이 시작점과 붙어있고, 아이템의 마지막이 끝점과 붙어있다는 점이 눈여겨볼만하다. 그 외에 아이템 간 간격은 균등하다는 점은 같다

✴️ space-around는 flex-start에서 아이템 정렬 후 남는 공간을 분배하여 사용하는 개념으로, 아이템의 왼쪽과 오른쪽으로 균등한 너비를 비워둔다!

5. align-content

- 교차축의 정렬 방법을 설정
- ⚠️ flax-wrap 속성을 통해서 items가 여러줄 이상(2줄 이상)이고, 여백이 있을 때에만 사용 가능

⚠️ items 가 한 줄인 경우, align-items 속성을 이용할 것!

 코드와 함께 익혀보고 마지막으로 표로 이해한 바를 정리해보자

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            border:2px dashed blue;
        }
        .container1{
            display:flex;
            width: 300px;
            height: 800px;
            flex-wrap: wrap;
            border:1px solid red;
        }
        .container2{
            display:flex;
            align-content:flex-start;
            width: 300px;
            height: 800px;
            flex-wrap:wrap;
            border:1px solid red;
        }
        .container3{
            display:flex;
            align-content:flex-end;
            width: 300px;
            height: 800px;
            flex-wrap:wrap;
            border:1px solid red;
        }
        .container4{
            display:flex;
            align-content: center;
            width: 300px;
            height: 800px;
            flex-wrap: wrap;
            border:1px solid red;
        }
        .container5{
            display:flex;
            align-content: space-between;
            width: 300px;
            height: 800px;
            flex-wrap: wrap;
            border:1px solid red;
        }
        .container6{
            display:flex;
            align-content: space-around;
            width: 300px;
            height: 800px;
            flex-wrap:wrap;
            border:1px solid red;
        }
    </style>
</head>
<body>
    <p>align-content:stretch</p>
    <div class="container1">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>align-content:flex-start</p>
    <div class="container2">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>align-content:flex-end</p>
    <div class="container3">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>align-content:center</p>
    <div class="container4">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>align-content:space-between</p>
    <div class="container5">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>align-content:space-around</p>
    <div class="container6">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_stretch.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_stretch.PNG?raw=true)

align-content:stretch

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_space-between.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_space-between.PNG?raw=true)

align-content:space-between

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_space-around.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_space-around.PNG?raw=true)

align-content:space-around

위의 셋을 묶은 이유는 형태가 매우 비슷하기 때문이다!

먼저 justify-content의 기본값이 flex-start 점을 기억해서 참고하자

그리고 현재 flex-direction의 기본값인 row라는 점을 참고하자

즉, 현재는 주축이 수평 교차축이 수직방향축이라는 것이다

stretch는 교차축의 시작점에 첫번째 아이템을 배치하고 잡아 당긴 형태를 보인다. 다만, space-between과 다른 점은 stretch는 마지막 아이템이 교차축의 끝점에 고정되어 있지 않다는 점이다

space-between는 시작과 끝 아이템을 각각 교차축의 시작점과 끝점에 고정시킨 후 아이템 간 간격을 균등하게 나눈 것이다

space-around는 각 아이템의 위쪽(왼쪽)과 아래쪽(오른쪽)에 균등한 여백을 배치한 것이다

괄호는 교차축이 수평인 경우 생각될 수 있는 부분이다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_flex-start.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_flex-start.PNG?raw=true)

align-content:flex-start

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_flex-end.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_flex-end.PNG?raw=true)

align-content:flex-end

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_center.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-content_center.PNG?raw=true)

align-content:center

앞서 언급한 바처럼, justify-content의 기본값이 flex-start이고 주축은 수평축이기 때문에, center의 경우 수직방향으로 가운데 위치에 정렬된 것을 확인해볼 수 있다

그리고 flex-start와 flex-end는 각각 교차축인 수직축의 시작점과 끝점에서 아이템 정렬을 하되, 주축이 flex-start로 정렬되기 때문에 위에서부터 보았을 때에는 1 2 3 4 5 순으로 정렬되지만 교차축이 flex-end인 경우에는 바닥에 붙어서 정렬된 것처럼 보인다

이제 정리해보자

[align-content](https://www.notion.so/c9de8f3a6d794c229d243d465ebd16d3)

6. align-items

- 교차축에서 items의 정렬을 위해 사용되는 방법으로,  items가 한 줄일 경우 많이 사용됨
- flex-wrap을 통해서 여러 줄 이상인 경우, align-content가 우선됨
- align-items를 사용하려면 align-content:stretch로 설정해주면 여러 줄인 상황에서도 적용 가능

align-content에서처럼 먼저 간단한 확인을 위한 코드로 이해해보고, 정리해보자

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .items{
            width: 100px;
            height: 100px;
            background-color: tomato;
            border:2px dashed blue;
        }
        .container1{
            display:flex;
            width: 300px;
            height: 500px;
            border:1px solid red;
            flex-wrap:wrap;
            align-items:stretch;
        }
        .container2{
            display:flex;
            width: 300px;
            height: 500px;
            border:1px solid red;
            flex-wrap: wrap;
            align-items:flex-start;
        }
        .container3{
            display:flex;
            width: 300px;
            height: 500px;
            border:1px solid red;
            flex-wrap:wrap;
            align-items:flex-end;
        }
        .container4{
            display:flex;
            width: 300px;
            height: 500px;
            border:1px solid red;
            flex-wrap:wrap;
            align-items:center;
        }
        .container5{
            display:flex;
            width: 300px;
            height: 500px;
            border:1px solid red;
            flex-wrap: wrap;
            align-items:baseline;
        }
        .container5 .items:nth-child(3){
            font-size:50px;
        }
    </style>
</head>
<body>
    <p>align-items:strectch</p>
    <div class="container1">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>align-items:flex-start</p>
    <div class="container2">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>align-items:flex-end</p>
    <div class="container3">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>align-items:center</p>
    <div class="container4">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
    <p>align-items:baseline</p>
    <div class="container5">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-items.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-items.png?raw=true)

align-items

먼저 공통적으로 확인해볼 수 있듯, 지금은 align-content가 기본값인 stretch가 적용되었기 때문에 align-items가 우선 적용될 수 있음을 확인해볼 수 있다

지금은 stretch와 flex-start가 비슷해보이지만, 의미상 다르다!

stretch는 교차축의 방향에 따른 너비(지금은 수직축의 너비, 즉 높이)를 꽉 채우도록 늘려서 배치한다

flex-start는 각 줄의 시작점에 맞추어 정렬한다

그리고 flex-end는 각 줄의 끝점에 맞추어 정렬한다

center는 교차축의 가운데 줄에 맞추어 정렬된다

마지막으로 baseline은 특이한데, 문자기준선에 맞추어 정렬되는데, 위의 그림에서처럼 글자 크기가 다른 경우 적용하기 용이하다

[align-items의 속성값](https://www.notion.so/68c004c3c34a492398666a11afa4f7c2)

추가로, 위에서 아이템을 하나의 컨테이너로 본다면, 아이템 입장에서는 내부에 한 줄로만 되어 있기 때문에 교차축에 대해서는 align-items를 사용하여 정렬할 수 있을 것이다. 그리고 주축은 기본값으로 row가 설정되어 있을 것이다

이를 이용한다면, display:flex와 함께 justify-content:center, align-items:center를 이용하면 아이템 내부의 컨텐츠를 가운데에 위치할 수 있도록 할 수 있을 것이다

```css
.container2{
            display:flex;
            width: 300px;
            height: 500px;
            border:1px solid red;
            flex-wrap: wrap;
            align-items:flex-start;
        }
        .container2 .items{
            display:flex;
            justify-content: center;
            align-items: center;
        }
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-%EC%95%84%EC%9D%B4%ED%85%9C%20%EB%82%B4%20%EC%BB%A8%ED%85%90%EC%B8%A0%20%EC%A0%95%EB%A0%AC.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-%EC%95%84%EC%9D%B4%ED%85%9C%20%EB%82%B4%20%EC%BB%A8%ED%85%90%EC%B8%A0%20%EC%A0%95%EB%A0%AC.PNG?raw=true)

flex 아이템 내 컨텐츠 정렬

🌺 Flex Items를 위한 속성들 🌺

1. order
- 아이템의 순서를 설정
- 숫자가 클수록 순서가 밀림(z-index와는 반대경향)
- 음수 사용 가능
- HTML 구조와  상관없이 순서를 변경할 수 있기 때문에 유용

[order 속성값](https://www.notion.so/3a628e5edf3b4f2599a607b468fd877e)

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container{
            display:flex;
            width: 800px;
            height: 700px;
            border:1px solid red;
        }
        .items{
            width: 100px;
            height: 100px;
            background-color:yellow;
            border:2px dashed blue;
        }
        .items:first-child{
            order:1;
        }
        .items:nth-child(2){
            order:0;
        }
        .items:nth-child(3){
            order:-1;
        }
        .items:nth-child(4){
            order:7;
        }
        .items:nth-child(5){
            order:17;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="items">1</div>
        <div class="items">2</div>
        <div class="items">3</div>
        <div class="items">4</div>
        <div class="items">5</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex%20order.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex%20order.PNG?raw=true)

flex order

위에서 확인할 수 있듯이, order의 값에는 음수도 들어갈 수 있다!

그리고 당연히 음수가 양수보다 값이 작기 때문에 음수가 지정된 3이 가장 먼저 위치되고

그다음은 order값이 0인 2가, 그 다음에는 order값이 1인 1이, 그 다음에는 order 값이 7인 4가, 그 다음에는 order 값이 17일 5가 위치하게 된다

🌟 order값이 같으면 html 구조적으로 가장 마지막에 기재된 요소가 뒤로 밀린다!

2. flex 속성

2-1. flex-grow

- 아이템의 증가 너비 비율 설정
- 숫자가 크면 더 많은 너비를 가짐
- item이 가변 너비가 아니거나, 값이 0이라면 효과가 없음

[flex-grow 속성값](https://www.notion.so/b4f60c2f650b443a978a1fe961b76650)

🌟 만약, 여러개의 item들 중 하나만 속성값으로 1 보다 큰 값을 갖고 나머지는 다 0이 되면, 의미적인 모호성이 있을 수 있다

-flex-grow가 1보다 큰 요소는 전체 컨테이너 너비를 차지해야 의미상 맞지만, 나머지 요소들이 크기를 갖고 있으므로 의미상 맞지 않을 수 있다

하지만, 이는 의미상 "크기를 가진 요소를 제외한 컨테이너 내부 너비를 차지"한다고 생각할 수 있어서 완전히 맞지 않다고도 할 수 없다 😭

일부러 이러한 특성을 이용할 수도 있는데, 이 경우 flex-grow를 지정한 요소만 가변시킬 수 있어서 사용될 수 있다

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container{
            display:flex;
            border:2px solid red;
            border-radius: 10px;
        }
        .container .item1{
            background-color: yellow;
            flex-grow:1;
            border:2px dashed blueviolet;
        }
        .container .item2{
            background-color: cadetblue;
            width: 100px;
            border:2px dashed blueviolet;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item1">1</div>
        <div class="item2">2</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-grow_%EB%84%88%EB%B9%84%EB%A5%BC%20%EA%B0%96%EB%8A%94%20%EA%B2%BD%EC%9A%B0%EC%99%80%20%ED%98%BC%EC%9A%A9.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-grow_%EB%84%88%EB%B9%84%EB%A5%BC%20%EA%B0%96%EB%8A%94%20%EA%B2%BD%EC%9A%B0%EC%99%80%20%ED%98%BC%EC%9A%A9.gif?raw=true)

위의 경우처럼, 어떤 요소는 크기가 고정되어 있고, 다른 요소는 flex-grow를 설정해둔다면 화면크기가 변동되면 그에 따라서 flex-grow를 설정해둔 요소의 크기도 변경됨을 확인해볼 수 있다

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container{
            display:flex;
            width: 800px;
            height: 300px;
            border:2px solid red;
        }
        .container div{
            background-color: yellow;
            border:2px dashed blueviolet;
        }
        .container .item1{
            flex-grow:1;/*200px*/
        }
        .container .item2{
            flex-grow:2;/*400px*/
        }
        .container .item3{
            flex-grow:1;/*200px*/
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item1">1</div>
        <div class="item2">2</div>
        <div class="item3">3</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-grow.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-grow.PNG?raw=true)

flex-grow

위와 같이 세 개 요소는 각각 flex-grow 값이 1, 2, 1 이기 때문에 각각 컨테이너의 너비를 기준으로  1/(1+2+1), 2/(1+2+1), 1/(1+2+1) 비율만큼씩 가로 너비를 차지하게 된다!

즉, 200px, 400px, 400px 씩 너비를 갖게 된다

2-2. flex-shrink

- item이 감소하는 너비의 비율을 설정
- 숫자가 크면 더 많은 너비가 감소
- item이 가변 너비가 아니거나 값이 0인 경우는 flex-grow에서처럼 효과가 없음

[flex-shrink 속성값](https://www.notion.so/736e645a4c78461da7bb924f0bc8cc86)

🌟 container 크기에 따라 아이템이 증가, 감소하는 개념은 flex-grow,

flex-shrink는 container가 크기가 변할 때, 다른 아이템보다 더 많이 줄어드는 의미로 사용

🌟 flex-grow와 flex-shrink는 모두 width, height, flex-basis의 영향을 받기 때문에 계산이 까다로울 수 있다!(flex-basis는 요소의 기본 너비)

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-shrink%20%EA%B3%84%EC%82%B0%EB%B0%A9%EC%8B%9D.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-shrink%20%EA%B3%84%EC%82%B0%EB%B0%A9%EC%8B%9D.png?raw=true)

앞서, flex-basis에 따라 flex-shrink 계산에 대한 영향이 존재할 것이라고 하였는데

이러한 점을 참고하여 flex container 내부에 2개의 아이템이 존재하는 상황을 상상해보자

이 때, shrink 비율은 각 요소에 대해서 flex-shrink * flex-basis 값의 다른 요소에 대한 상대적인 비율로 확인할 수 있다. 그 결과 위의 경우에는 4:1이 성립되었다

이를 이용하여 만약 90px만큼 컨테이너 크기를 줄인다면

원래의 너비-(감소되는 만큼의 크기(위의 경우 90px)*요소가 차지하는 비중/전체 비율합)이 변경되는 요소의 너비가 되어 적용될 것이다

2-3. flex-basis

- items의 공간 배분 전 기본 너비를 설정

[flex-basis의 속성값](https://www.notion.so/2a5605d88538497b971dff4bb48f4579)

🌟 flex 단축속성에서 flex-basis를 생략할 경우에는 해당 속성값이 0이 될 수 있다!

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container{
            display:flex;
            border:2px solid red;
        }
        .container .item:first-child{
            background-color: yellow;
            border:2px dashed blueviolet;
            flex-basis: 300px;
        }
        .container .item:nth-child(2){
            background-color: yellow;
            border:2px dashed blueviolet;
            flex-basis: 100px;
        }
        .container .item:last-child{
            background-color: yellow;
            border:2px dashed blueviolet;
            flex-basis: 200px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item">Good Job!</div>
        <div class="item">A</div>
        <div class="item">Hello World!</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-basis_%EB%B3%80%EA%B2%BD%20%EC%A0%84%20%EC%9A%94%EC%86%8C%EC%9D%98%20%EB%84%88%EB%B9%84%EB%A5%BC%20%EC%A7%80%EC%A0%95.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-basis_%EB%B3%80%EA%B2%BD%20%EC%A0%84%20%EC%9A%94%EC%86%8C%EC%9D%98%20%EB%84%88%EB%B9%84%EB%A5%BC%20%EC%A7%80%EC%A0%95.gif?raw=true)

flex-basis

위와 같이 flex-basis를 지정하게 되면, 변경 전 너비를 지정해줄 수 있다!

단, 이때 변화는 컨텐츠 부분을 제외한 요소 내 영역에서 flex-grow 값을 이용하여 크기가 조정된다

(만약 flex-grow가 1이라면 컨텐츠 영역을 기준으로 왼쪽과 오른쪽으로 1씩 적용)

[https://heropy.blog/2018/11/24/css-flexible-box/](https://heropy.blog/2018/11/24/css-flexible-box/)

그리고 flex-basis가 0인 경우는 아래의 예시를 통해 확인해볼 수 있듯이,

만약 컨텐츠가 한개도 없었으면 색이 입혀진 점선정도로만 보였을 텐데, 컨텐츠가 있어서 딱 그 컨텐츠를 두를 수 있을 정도만 공간 차지가 된다

그리고 이러한 경우에는 flex-grow값에 따라 영향을 받는다

아래의 경우에는 grow값이 1이기 때문에 변화가 없는 것처럼 보일 수 있다

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container{
            display:flex;
            border:2px solid red;
        }
        .container .item:first-child{
            background-color: yellow;
            border:2px dashed blueviolet;
            flex-basis: 300px;
        }
        .container .item:nth-child(2){
            background-color: yellow;
            border:2px dashed blueviolet;
            flex-basis: 0;
        }
        .container .item:last-child{
            background-color: yellow;
            border:2px dashed blueviolet;
            flex-basis: 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item">Good Job!</div>
        <div class="item">A</div>
        <div class="item">Hello World!</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-basis%EA%B0%92%EC%9D%B4%200%EC%9D%B8%20%EA%B2%BD%EC%9A%B0.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-basis%EA%B0%92%EC%9D%B4%200%EC%9D%B8%20%EA%B2%BD%EC%9A%B0.gif?raw=true)

flex-basis:0인 경우

🌟 flex-basis는 width와 다른 개념으로, 컨텐츠를 포함하여 flex 컨테이너 크기 변동 전 유지될 수 있는 최소 공간!의 의미를 갖는다!

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container{
            display:flex;
            border:2px solid red;
        }
        .container div{
            background-color: yellow;
            border:2px dashed blueviolet;
            flex-basis: 100px;
            flex-grow:1;
        }
        .container .item:last-child{
            background-color: yellow;
            border:2px dashed blueviolet;
            flex-grow: 2;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item">Good Job!</div>
        <div class="item">A</div>
        <div class="item">Hello World!</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-basis%EA%B0%92%EC%9D%B4%20%EB%8B%A8%EC%9C%84%EB%A1%9C%20%EC%A0%80%EC%9E%A5%EB%90%9C%20%EA%B2%BD%EC%9A%B0.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex-basis%EA%B0%92%EC%9D%B4%20%EB%8B%A8%EC%9C%84%EB%A1%9C%20%EC%A0%80%EC%9E%A5%EB%90%9C%20%EA%B2%BD%EC%9A%B0.gif?raw=true)

flex-basis 값으로 단위를 이용할 때

그리고 위에서 예제에서는 basis를 단위로 사용한 적이 있었다. 하지만 이런 경우 문제점이 있다면, 특정 비율로 요소들을 배치하고 싶을 때, 정확한 비율로 배치할 수 없다는 점이 단점이다

🌟flex-basis가 auto일 경우에는, 여백의 너비가 요소의 너비에 영향을 주어 맞추어질 수 있다

2-4. flex

- 단축속성
- item의 너비(증가, 감소, 변동 전)을 설정

[flex의 속성값](https://www.notion.so/8938d191fa0e4aff967a09726f118ebb)

[USAGE]

flex: 증가너비 감소너비 기본너비;
flex:증가너비 감소너비;
flex:증가너비 기본너비[너비에 단위를 사용하면 브라우저가 기본너비로 인식];

📌 주의할 점 📌

flex:1; 

과 같이 비율을 나타내는 숫자를 하나만 적게 되면,

증가너비는 0이 아닌 1로

감소너비는 1로

설정되지만 **flex-basis는 단위도 auto도 아니면 무조건 0으로 설정하기에 flex-basis값이 0으로 설정된다!**

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container{
            border:4px solid;
            display:flex;
        }
        .container .item{
            height: 100px;
            background-color: yellow;
            border:4px dashed red;
            border-radius:10px;
            flex:1;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item item1">Good Job!</div>
        <div class="item item2">A</div>
        <div class="item item3">Hello World!</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex%EB%8B%A8%EC%B6%95%EC%86%8D%EC%84%B1.gif?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/flex%EB%8B%A8%EC%B6%95%EC%86%8D%EC%84%B1.gif?raw=true)

flex 단축속성

위의 경우, 3 개의 요소를 1:1:1 비율로 공간 차지하도록 하기 위해서 증감비율을 1, 기본 너비값을 0으로 설정하여 정확하게 1:1:1비율로 맞아떨어지도록 해주었다

만약 여백의 영향을 조금 받더라도, 일부러 그런 상황을 연출하고 싶다면

flex:1 1 auto;

로 설정해주면 된다

3. align-self

- 교차축에서 개별 아이템의 정렬 방법을 설정
- align-items : container 내 모든 아이템의 정렬 방법을 설정
- 필요에 의해서 일부 아이템의 정렬방법 변경이 필요하다면, align-self를 사용하는데, 이는 align-items 속성보다 우선시된다

[align-self의 속성값](https://www.notion.so/a807e3d8876c4c13af9d237e3e12e6fd)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        *{
            padding:0;
            margin:0;
        }
        .container{
            display:flex;
            flex-wrap:wrap;
            width: 500px;
            height: 400px;
            align-items:space-between;
            border:2px solid red;
        }
        .container .item{
            width: 100px;
            height: 100px;
            background-color: tomato;
            border:2px dashed blueviolet;
        }
        .container .item2{
            align-self:center;
        }
        .container .item3{
            align-self:flex-end;
        }
       
        .container .item6{
            align-self:flex-start;
        }
        .container .item7{
            height: auto;
            font-size:30px;
            align-self: stretch;
        }
        .container .item8{
            
            align-self:baseline;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item item1">1</div>
        <div class="item item2">2</div>
        <div class="item item3">3</div>
        <div class="item item4">4</div>
        <div class="item item5">5</div>
        <div class="item item6">6</div>
        <div class="item item7">7</div>
        <div class="item item8">8</div>
    </div>
</body>
</html>
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-self.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Flex/align-self.png?raw=true)

align-self

위에서 조심할 점은, center는 원래 요소가 있던 자리에서의 중심축을 기준으로 가운데 정렬을 하고

baseline은 해당 요소의 글자크기의 하한선을 기준으로 정렬한다는 점이다

그 외에는 align-items와 동일한 것을 알 수 있는데, stretch의 경우, 기존에 height값이 100으로 잡혀져 있었기에 이를 auto로 풀어주면, 위와 같이 끝까지 공간을 차지함을 확인해볼 수 있다