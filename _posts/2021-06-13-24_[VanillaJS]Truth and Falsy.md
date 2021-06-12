# Truth and Falsy

- Truthy는 true, Falsy는 false같은 것!

상황을 가정해보자!

```jsx
function print(person){
    //null checking
    if(person===undefined || person===null){
        return;
    }
    console.log(person.name);
}

const person2={
    name:null
};

print();//원래는 null checking이 없었다면 undefined(객체가 없음)
print(person2);//원래는 null checking이 없었다면 null(값이 없음)
```

원래 print()나 print(son)과 같은 경우는 undefined, null 의 결과를 맺는데

 if(person===undefined || person===null){
        return;
    }

와 같은 null checking을 통해서 에러를 피할 수가 있다

⚠️하지만 조금 너저분하다!

이를 깔끔하게 해주기 위해서

- undefined, null, 0, ' ' , NaN, false가 falsy한 값이다! 
그 외에는 truthy한 값!

라는 점을 이용해보자!

그렇다면 위에서 언급되었던

 if(person===undefined || person===null){
        return;
    }

는

if(!person){
return;
}

와 같다!

그렇게 된다면, truthy한 값에 대한 처리는 아래와 같이 사용해볼 수 있을 것이다!

```jsx
const value={a:1};
if(value){
    console.log('값이 truthy 하네요!');
}
```

만약, truthy한 값을 true, falsy한 값을 false로 바꾸고 싶다면 

!!변수명

을 이용하면 변경이 가능하다!

```jsx
const val=[];
const tr =null;
const truthy = !!val;
console.log(truthy);//true
console.log(!!tr);//false;
```