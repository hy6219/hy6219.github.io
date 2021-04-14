# Bootstrap레이아웃-grid 시스템

1. 그리드 시스템의 기본적인 구조
- 기본적으로 12개의 열로 페이지를 등분하였다고 이해하면 쉽다!

참조: [bootstrap css reference-grid](https://getbootstrap.com/docs/3.4/css/)

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/bootstrap_grid_sys.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/bootstrap_grid_sys.png?raw=true)

Bootstrap-grid system

- 사용할 때에는 **클래스 뒤에 붙는 숫자의 합이 12를 기준으로 한다는 점만 유의**하면 된다!
- 합 ==12 ➡️ 행의 너비를 꽉 채움
- 합 <12 ➡️ 오른쪽에 남는 부분이 생김
- 합 >12 ➡️ 합이 12를 넘게 한 마지막 열이 다음줄로 이어짐

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>grid sys</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
    <link rel="stylesheet" href="./grid.css">
</head>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js" integrity="sha384-aJ21OjlMXNL5UyIl/XNwTMqvzeRMZH2w8c5cRVpzpU8Y5bApTppSuUkhZXN0VxHd" crossorigin="anonymous"></script>
<body>
    <div class="container">
        <div class="col-sm-2">첫번째</div>
        <div class="col-sm-4">첫번째</div>
        <div class="col-sm-6">첫번째</div>
    </div>
</body>
</html>
```

```css
.container div{
    background:coral;
    border:cornflowerblue 2px solid;
}
```

- 화면 사이즈에 따른 클래스명이 다른데 이를 확인해보자!("[Grid Options](https://getbootstrap.com/docs/3.4/css/)")

[css-grid system-class name by screen size](https://www.notion.so/1dbf7336e640483b9d8f137b6e38a1e7)

- USAGE 쉽게 정리

```html
<div class="container"><!--바깥에서 컨테이너로 감싸고-->
	<div class="grid options by optimal screen size"></div>
<!--화면크기에 따른 클래스를 명시-->
</div>
```

- container와 container-fluid의 차이
    1. container는 기기의 고정된 너비를 기준으로 12등분

    ![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/container.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/container.PNG?raw=true)

    1. container-fluid는 화면 전체를 100%로 간주해서, 12등분

    ![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/container-fluid.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/container-fluid.PNG?raw=true)

- row 클래스

** 그리드의 row 클래스는 한 행 단위로 구성하는 요소를 의미

다음과 같이 row 클래스를 이용한다고 가정하자

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
    <link rel="stylesheet" href="./grid.css">
</head>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js" integrity="sha384-aJ21OjlMXNL5UyIl/XNwTMqvzeRMZH2w8c5cRVpzpU8Y5bApTppSuUkhZXN0VxHd" crossorigin="anonymous"></script>
</head>
<body>
    <div class="container-fluid">
        <div class="row">
          <div class="col-xs-12 col-sm-4">1</div>
          <div class="col-xs-12 col-sm-4">2</div>
          <div class="col-xs-12 col-sm-4">3</div>
        </div>
        <div class="row">
            <div class="col-xs-12 col-sm-4">1</div>
            <div class="col-xs-12 col-sm-4">2</div>
            <div class="col-xs-12 col-sm-4">3</div>
          </div>
    </div>
</body>
</html>
```

```css
.container div{
    background:coral;
    border:cornflowerblue 2px solid;
}
.row div{
    background:coral;
    border:cornflowerblue 2px solid;
}
```

그 결과는 아래와 같이, 각 행을 구성하는 그리드가 보이게 된다.

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/grid_row.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/grid_row.PNG?raw=true)

## visible을 이용한 Responsive column resets

- visible
1. USAGE: class="visible-xs/sm/md/lg-block"
2. 각 화면에서만! 보일 수 있도록 설정
3. 아래는 [bootstrap 페이지](https://getbootstrap.com/docs/3.4/css/)에서 이용한 코드이다

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
    <link rel="stylesheet" href="./grid.css">
</head>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js" integrity="sha384-aJ21OjlMXNL5UyIl/XNwTMqvzeRMZH2w8c5cRVpzpU8Y5bApTppSuUkhZXN0VxHd" crossorigin="anonymous"></script>
</head>
<body>
    <div class="container-fluid">
        <div class="row">
          <div class="col-xs-12 col-sm-4">1</div>
          <div class="col-xs-12 col-sm-4">2</div>
          <div class="col-xs-12 col-sm-4">3</div>
        </div>
        <div class="row">
            <div class="col-xs-12 col-sm-4">1</div>
            <div class="col-xs-12 col-sm-4">2</div>
            <div class="col-xs-12 col-sm-4">3</div>
        </div>
        <div class="row">
            <div class="col-xs-6 col-sm-4">1</div>
            <div class="col-xs-6 col-sm-4">2</div>
            <!-- Optional: clear the XS cols if their content doesn't match in height -->
            <div class="clearfix visible-xs-block">3</div>
            <div class="col-xs-6 col-sm-4">4</div>
        </div>
    </div>
</body>
</html>
```

```css
.container div{
    background:coral;
    border:cornflowerblue 2px solid;
}
.row div{
    background:coral;
    border:cornflowerblue 2px solid;
}
```

그 결과는 아래처럼, 특정 화면 크기일때에만, 특정요소가 보일 수 있고

나머지 화면 크기에서는 보이지 않는다

- 특정 화면 크기 이외의 경우

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/visible.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/visible.PNG?raw=true)

- 특정 화면 크기인 경우

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/visible-2.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/visible-2.PNG?raw=true)

### 🌟gutter 🌟

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/gutter.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/gutter.png?raw=true)

gutter란?

1. gutter란?
- Bootstrap의 grid 레이아웃을 통하여 반응적으로 화면이 구성될 때의 **컬럼 사이의 패딩** ✅

- remove gutters

[https://getbootstrap.com/docs/3.4/css/](https://getbootstrap.com/docs/3.4/css/)

를 참고하면,

"row-no-gutters"를 통하여 gutter를 삭제할 수 있는 것을 확인해볼 수 있다

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
    <link rel="stylesheet" href="./grid_gutter.css">
</head>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js" integrity="sha384-aJ21OjlMXNL5UyIl/XNwTMqvzeRMZH2w8c5cRVpzpU8Y5bApTppSuUkhZXN0VxHd" crossorigin="anonymous"></script>
</head>
<body>
    <div class="clearfix">
        <div class="center">
            <div class="container">
                <h1 style="color:green;padding:13px;">
                  <a href="https://www.geeksforgeeks.org/how-to-remove-gutter-space-for-a-specific-div-in-bootstrap/">GeeksforGeeeksExample</a>
                </h1>
                <br>
                <p class="row p-3">
                    <h2><em>With Gutter space</em></h2>
                </p>
                <div class="row border border-dark ">
                    <div class="col-12 col-sm-6 col-md-8 bg-primary ">
                        .col-12 .col-sm-6 .col-md-8
                    </div>
                    <div class="col-6 col-md-4 bg-secondary" style="border:1px solid black;">
                        .col-6 .col-md-4
                    </div>
                </div>
      
                <p class="row p-3">
                    <h2><em>Without Gutter space</em></h2>
                </p>
                <div class="row row-no-gutters border border-dark">
                    <div class="col-12 col-sm-6 col-md-8 bg-primary ">
                        .col-12 .col-sm-6 .col-md-8
                    </div>
                    <div class="col-6 col-md-4 bg-secondary"style="border:1px solid black;">
                        .col-6 .col-md-4
                    </div>
                </div>
            </div>
        </div>
    </div>
</body>
</html>
```

```css
.clearfix{
    padding:10%;
}
.clearfix::after{
    content:"";
    display:block;
    clear: both;
}
```

예제는 위에서 언급하였다 시피, [부트스트랩 문서](https://getbootstrap.com/docs/3.4/css/)와 [geeks for geeks 예제](https://www.geeksforgeeks.org/how-to-remove-gutter-space-for-a-specific-div-in-bootstrap/)를 이용하였다

그 결과는 아래처럼, 패딩이 없어진 것과 같은 효과를 확인해볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/gutter_remove.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/gutter_remove.PNG?raw=true)

Bootstrap: row-no-gutters

## Column Wrapping

앞서 위에서 언급했던 클래스 뒤에 붙는 숫자의 합이 12가 넘어갈 경우, 다음 행으로 이어지는 것을 보여주는 예제이다!

다음과 같은 상황을 생각해보자

스마트폰 기기에서 다음과 같이 9칸을 차지한 후, 3칸을 차지하고, 그 후에 5칸을 차지한다!

그러면, 이 상황에서는 5칸을 차지하는 단계에서 합이 12를 넘어서서, 다음줄에 표시되게 될 것이다

마치 아래와 같이!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/grid_column_wrapping.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/grid_column_wrapping.PNG?raw=true)

column wrapping

위를 실행하기 위해 작성한 코드는 아래와 같다

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
    <link rel="stylesheet" href="./gridColumnWrapping.css">
</head>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js" integrity="sha384-aJ21OjlMXNL5UyIl/XNwTMqvzeRMZH2w8c5cRVpzpU8Y5bApTppSuUkhZXN0VxHd" crossorigin="anonymous"></script>
<body>
    <div class="container-fluid">
        <div class="col-xs-9">9</div>
        <div class="col-xs-3">12</div>
        <div class="col-xs-5">17!Over!!</div>
    </div>
</body>
</html>
```

```css
.container-fluid div{
    border:1px solid salmon;
}
```

## Column Offesets

- col-xs/sm/md/lg-offset-x : col-xs/sm/md/lg-x에 해당되는 열 크기만큼 띄어서 다음 컬럼을 진행offset-x : col-xs/sm/md/lg-x에 해당되는 열 크기만큼 띄어서 다음 컬럼을 진행
- col-xm/sm/md/lg-x (이전에 오프셋 등 컬럼에 대한 크기를 지정한 내용 작성 후) col-xs/sm/md/lg-offset-x: 오프셋 오버라이딩

예시:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
    <link rel="stylesheet" href="./gridOff.css">
</head>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js" integrity="sha384-aJ21OjlMXNL5UyIl/XNwTMqvzeRMZH2w8c5cRVpzpU8Y5bApTppSuUkhZXN0VxHd" crossorigin="anonymous"></script>
<body>
    <div class="row">
        <div class="col-md-4">.col-md-4</div>
        <div class="col-md-4 col-md-offset-4">.col-md-4 .col-md-offset-4</div>
      </div>
      <div class="row">
        <div class="col-md-3 col-md-offset-3">.col-md-3 .col-md-offset-3</div>
        <div class="col-md-3 col-md-offset-3">.col-md-3 .col-md-offset-3</div>
      </div>
      <div class="row">
        <div class="col-md-6 col-md-offset-3">.col-md-6 .col-md-offset-3</div>
      </div>
      <p></p>
      <p></p>
      <div class="row">
        <div class="col-xs-6 col-sm-4">1
        </div>
        <div class="col-xs-6 col-sm-4">2
        </div>
        <div class="col-xs-6 col-xs-offset-3 col-sm-4 col-sm-offset-2">override
        </div>
      </div>
</body>
</html>
```

```css
.row div{
    border:1px solid salmon;
}
```

이렇게 실행하게 되면, 아래와 같이 오프셋 결과(일반)과 오프셋 오버라이딩 결과를 확인해볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/column_offset.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/column_offset.png?raw=true)

column offset

## nesting columns

- 이 기능은 아래 그림처럼 컬럼 안에 컬럼 여러개를 중첩시킬 수 있는 기능이다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/nestingColumn.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/nestingColumn.PNG?raw=true)

nesting columns

위의 결과는 아래의 코드로 연습해볼 수 있다.

핵심은 마치 자바에서 중첩된 for loop처럼 하나의 column을 나타내는 div 파트를

바깥에서 또 하나의 column을 나타내는 파트가 감싸는 형태로 있다는 점이다

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
    <link rel="stylesheet" href="./gridNest.css">
</head>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js" integrity="sha384-aJ21OjlMXNL5UyIl/XNwTMqvzeRMZH2w8c5cRVpzpU8Y5bApTppSuUkhZXN0VxHd" crossorigin="anonymous"></script>
<body>
    <div class="container">
        <div class="row">
            <div class="col-sm-9">
              Level 1: .col-sm-9
              <div class="row">
                <div class="col-xs-8 col-sm-6">
                  Level 2: .col-xs-8 .col-sm-6
                </div>
                <div class="col-xs-4 col-sm-6">
                  Level 2: .col-xs-4 .col-sm-6
                </div>
              </div>
            </div>
          </div>
    </div>
</body>
</html>
```

```css
.row div{
    border:1px solid salmon;
    padding:5px;
}
```

## column ordering

- USAGE

```html
<div class="row">
  <div class="col-md-A col-md-push-B">.col-md-9 .col-md-push-3</div>
  <div class="col-md-B col-md-pull-A">.col-md-3 .col-md-pull-9</div>
</div>
```

col-xs/sm/md/lg-push또는 pull-x : 원래는 두 요소가 순차적이었다면, 뒤바뀌어질 수 있도록 지원

예제:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title><link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">
    <link rel="stylesheet" href="./gridOrd.css">
</head>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js" integrity="sha384-aJ21OjlMXNL5UyIl/XNwTMqvzeRMZH2w8c5cRVpzpU8Y5bApTppSuUkhZXN0VxHd" crossorigin="anonymous"></script>
<body>
    <div class="container">
        <div class="row">
            <div class="col-md-9 col-md-push-3">.col-md-9 .col-md-push-3</div>
            <div class="col-md-3 col-md-pull-9">.col-md-3 .col-md-pull-9</div>
          </div>
    </div>
</body>
</html>
```

```css
.row div{
    border: 1px solid salmon;
}
```

위와 같이 push, pull을 통해서 아래와 같이 순서를 바꾸어볼 수 있다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/column_ordering.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/FE/CSS/Framework/Bootstrap/column_ordering.PNG?raw=true)

### Grid-mixin && variables ➡️다음에 따로 페이지에서 정리해봅시다!
