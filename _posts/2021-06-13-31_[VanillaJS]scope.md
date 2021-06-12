# scope

- 변수 또는 함수가 유효한 범위를 의미
1. Global Scope : 코드의 모든 범위에서 사용 가능
2. Function Scope: 함수 내부에서만 사용 가능한 경우
3. Block Scope: 조건문 등 의 내부에서만 사용 가능한 경우

⚠️ 앞서 언급되었던 것처럼, const나 let과 달리 var는 scope 구분이 잘 되지 않음!(같은 블록 내 다른 블록의 값에서의 영향을 받게 됨)

## Hoisting(호이스팅)

```jsx
myFunc();
function myFunc(){
    console.log('hello world!');
}

```

- 위와 같이, 선언한 부분이 함수가 정의된 부분보다 앞서 있을 때에 제대로 작동하는 방식을 의미
- 자바스크립트 엔진이 실행 전에 구문을 전체적으로 해석하고 실행하기 때문에 가능!
- 호이스팅은 코드 이해에 어려워질 수도 있고, 유지보수가 어려워질 수도 있으므로, 웬만하면 피해야 함!

변수도 완벽하지는 않지만 var를 이용했을 때,

```jsx
console.log(number);
var number=2;//undefined
```

위와 같이 사용하면 변수에 대해서 값이 없는 상태, 즉, number 변수는 있는 상태로 해석된다

그 이유는 자바스크립트 엔진이

```jsx
var number;
console.log(number);
number=2;
```

와 같이 해석하기 때문이다!

⚠️ const, let은 호이스팅이 되지 않음! 다만, Barbel이 지원되는 환경에서는 가능할 수도 있음!(예: code sandbox에서는 작동됨)

⚠️ 함수의 호이스팅을 막는 방법 예시

```jsx
const 함수를 담을 변수명=function 함수명(){
~~
}
```

혹은 순서대로 잘 적어주거나

```jsx
function 함수명(){}
함수명();
```

혹은 ESLint와 같은 도구를 사용하면 경고를 에디터상에서 확인하여 방지할 수 있음