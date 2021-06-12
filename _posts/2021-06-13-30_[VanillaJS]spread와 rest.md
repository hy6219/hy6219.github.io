# spread와 rest

- ES6에 도입된 문법

## spread 연산자

- 기존 객체 혹은 배열을 확장시킬 수 있음

### 객체에서의 spread 연산자

- 기존 객체를 이용해서 새로운 객체를 만들 때 사용

```jsx
const slime={
    name:'슬라임'
};
const cuteSlime={
    name:'슬라임',
    attribute:'cute'
};
const purpleCuteSlime={
    name:'슬라임',
    attribute:'cute',
    color:'purple'
};
```

⬇️

```jsx
const slime={
    name:'슬라임'
};
const cuteSlime={
    ...slime,
    attribute:'cute'
};
const purpleCuteSlime={
    ...cuteSlime,
    color:'purple'
};

console.log(slime);//{name: "슬라임"}
console.log(cuteSlime);//{name: "슬라임", attribute: "cute"}
console.log(purpleCuteSlime);//{name: "슬라임", attribute: "cute", color: "purple"}
```

이때  

...기존객체명,

으로 기존 객체를 그대로 사용할 수 있으면서, 새로이 속성을 추가할 수 있다!

그리고

```jsx
const slime={
    name:'슬라임'
};
const cuteSlime={
    ...slime,
    attribute:'cute'
};
const purpleCuteSlime={
    ...cuteSlime,
    color:'purple'
};

const greenCuteSlime={
    ...purpleCuteSlime,
    color:'green'
};

console.log(slime);//{name: "슬라임"}
console.log(cuteSlime);//{name: "슬라임", attribute: "cute"}
console.log(purpleCuteSlime);//{name: "슬라임", attribute: "cute", color: "purple"}
console.log(greenCuteSlime);//{name: "슬라임", attribute: "cute", color: "green"}
```

greenCuteSlime 객체의 경우와 같이, spread 연산자로 기존의 속성에 대한 값을 덮어쓸 수도 있다!

그런데

```jsx
const slime={
    name:'슬라임'
};

const cuteSlime = slime;
cuteSlime.attribute='cute';

const purpleCuteSlime = cuteSlime;
purpleCuteSlime.color='purple';

console.log(slime);//{name: "슬라임", attribute: "cute", color: "purple"}
console.log(cuteSlime);//{name: "슬라임", attribute: "cute", color: "purple"}
console.log(purpleCuteSlime);//{name: "슬라임", attribute: "cute", color: "purple"}
```

와 같이 대입연산자를 이용하게 되면, 모든 슬라임들이 같은 객체를 가리키게 되어(마치 개념적으로 자바의 얕은 복사처럼) 결과가 동일하게 나타남!

즉, spread 연산자를 이용하면 서로 다른 객체를 가리키게 됨!

기존 객체의 속성 및 값을 그대로 이용하면서 새로 생성되는 객체 고유의 속성 및 그 값을 지정해줄 수도 있다!

뿐만 아니라, 기존 속성의 값을 바꾸어줄 수도 있다!

📌 하지만! 여기서 그치지 않고! spread 연산자의 순서도 매우 중요하다!📌

```jsx
const slime={
    name:'슬라임'
};
const cuteSlime={
    ...slime,
    attribute:'cute'
};
const purpleCuteSlime={
    ...cuteSlime,
    color:'purple'
};

const greenCuteSlime={
    color:'green',
    ...purpleCuteSlime
    
};

console.log(slime);//{name: "슬라임"}
console.log(cuteSlime);//{name: "슬라임", attribute: "cute"}
console.log(purpleCuteSlime);//{name: "슬라임", attribute: "cute", color: "purple"}
console.log(greenCuteSlime);//{color: "purple", name: "슬라임", attribute: "cute"}
```

위에서는

...purpleCuteSlime,
color:'green'

으로 spread 연산자로 먼저 기존 속성 및 그 값을 전달받은 후, 속성을 추가하였는데

지금은

    color:'green',
    ...purpleCuteSlime

color 속성을 지정한 후, spread 연산자로 (name,attribute, color) 속성을 갖는 pupleCuteSlime을 불러왔다

즉, color:'green'으로 설정 ▶️ spread 연산자로 연결하는데 그 대상에 color 속성이 있다&&다만, name이나 attribute는 지정하지 않았기 때문에 덮어쓰여지지 않는다 ▶️따라서 기존의 purpleCuteSlime으로 덮어진다!

---

### 배열에서의 spread 연산자

- usage는 객체에서와 비슷한데 차이점은 배열의 [] 내부에서

[...기존 배열명, 새로운값들]

로 사용이 가능하다는 점만 아주 조금 차이가 있다!

물론, 기존 배열을 훼손하지 않는다!

```jsx
const animals=['개','고양이','참새'];
const anotherAnimals=[...animals,'너구리','비둘기'];
console.log(anotherAnimals);// ["개", "고양이", "참새", "너구리", "비둘기"]
```

그리고 이는 concat 함수를 사용한 경우와 동일하다!

```jsx
const animals=['개','고양이','참새'];
const anotherAnimals=animals.concat('너구리','비둘기');
console.log(anotherAnimals);// ["개", "고양이", "참새", "너구리", "비둘기"]
```

다만, spread 연산자를 이용했을 때 그 역할을 파악하기 더 쉬움!

### 함수 인자에서의 spread 연산자

function subtract(a,b){} 
subtract(1,2);
에서 a,b가 파라미터
1,2가 인자!

- 인자 : 함수에 넣어주는 값
- 파라미터: 함수에서 받아오는 값 형태

인자값으로 ...변수명

으로 넣어주면 그에 맞는 함수가 행해질 수 있는데, 변수가 배열이라면, 각 배열에 대해서 맞는 값들이 전부 들어갈 수가 있다!

```jsx
const num=[1,2];
const subtract=(x,y)=>(x+y);
const res=subtract(...num);
console.log(res);//3
```

---

## rest 연산자

- 객체, 배열, 함수 인자에서 사용 가능

...rest연산자 이름

### 객체에서의 rest 연산자

-객체의 비구조 할당과 함께 rest 연산자를 활용

```jsx
const purpleCuteSlime={
    name:'슬라임',
    attribute:'cute',
    color:'purple'
};

const {color, ...rest}=purpleCuteSlime;
//const {color, ...cuteSlime}=purpleCuteSlime;
console.log(color);
console.log(rest);//{name: "슬라임", attribute: "cute"}
```

위와 같이 비구조 할당과 함께 rest 연산자를 활용하면

color 외의 속성들이 저장된 객체가 반환된다!

그리고 **...rest가 아닌 ...cuteSlime으로 그 연산자 이름을 바꾸어주어도 된다!**

spread는 확장시키는 것! rest는 분산된 것들을 모아서 범위를 좁히는 것! 으로 생각!

### 배열에서의 rest 연산자

-배열의 비구조 할당과 rest 연산자를 같이 활용

```jsx
const numbers=[0,1,2,3,4,5,6];
const [first,...others]=numbers;
console.log(others);//[1, 2, 3, 4, 5, 6]
```

위와 같이 배열에서의 비구조 할당에서처럼 무작위로 지은 배열 요소 이름과 더불어 사용하는데

const [변수명1,변수명2,...rest연산자이름] = 배열명;

으로 사용하면 변수명1,변수명2 의 갯수&&인덱스에 매칭되는 요소 외의 요소들이 전부 rest 연산자에 저장된다!

⚠️ 단, [...rest, 변수명] 처럼 rest 연산자가 맨 앞에 올 수는 없다!

### 함수 파라미터에서의 rest

- 함수의 파라미터로 rest 연산자를 넣어주면, 하나의 배열처럼 인식될 수 있음
- 자바의 가변인자와 유사([https://sleepyeyes.tistory.com/29](https://sleepyeyes.tistory.com/29))

-가변인자를 이용한 여러가지 값에 대한 합 함수 구현

```jsx
function sum(...params){
    return params.reduce((acc,cur)=>acc+cur,0);
};
console.log(sum(1,2,3,4,5,6,7,8,9,10));//55
```