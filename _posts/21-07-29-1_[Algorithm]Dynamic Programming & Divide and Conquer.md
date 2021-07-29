# 동적 계획법과 분할정복

## Dynamic Programming(DP) and 'Divide and Conquer'

- 어떤 문제를 풀기위한 전략!

1. 동적계획법 DP
- **입력크기가 작은 부분 문제들을 해결한 후**, 해당 부분 문제의 해를 활용해서 보다 큰 크기의 부분 문제를 해결함으로써 최종적으로 전체 문제를 해결하는 알고리즘
- **상향식 접근법**으로, **가장 최하위 해답**을 구한 후, 이를 저장하고 , 해당 결과값을 이용해서 상위 문제를 풀어가는 방식
- **Memoization(메모이제이션) 기법** 사용
-  프로그램 실행 시 이전에 계산한 값을 저장하여, 다시 계산하지 않도록 함으로써 전체 실행 속도를 빠르게 하는 기술
- 문제를 잘게 쪼갤 때, 부분 문제는 중복되어 재활용됨
-예: 피보나치 수열

2..분할 정복 Divide and Conquer

- **문제를 나눌 수 없을 때까지 나누어서** 각각을 풀면서 다시 합병하여 문제의 답을 얻는 알고리즘
- **하향식 접근법**으로, **상위의 해답을 구하기 위해 아래로 내려가면서 하위의 해답을 구하는 방식**
-일반적으로 재귀함수로 구현
- 문제를 잘게 쪼갤 때, 부분 문제는 서로 중복되지 않음
- 메모이제이션을 사용하지 않음

### 📌 공통점과 차이점 📌

1. 공통점
- 문제를 잘게 쪼개서 가장 작은 단위로 분할

2.차이점

a.동적계획법

- 부분 문제는 중복되어, 상위 문제 해결시 재활용됨
- 메모이제이션 기법 사용(부분 문제의 해답을 저장해서 재활용하는 최적화 기법으로 사용)
- 가장 최하위 해답을 최우선으로 함

b.분할정복

- 부분문제는 서로 중복되지 않음
- 메모이제이션 기법을 사용하지 않음
- 상위의 해답을 우선으로 함

[동적계획법과 분할정복의 차이점](https://www.notion.so/6c38713658db4520a7b5c8e26802f513)

🌹 동적 계획법 알고리즘 이해 - 예시1. 피보나치 수열

[https://lh3.googleusercontent.com/proxy/aioCBLkdHuYPqd-_rYO4-394LXYuC4BL_zpAofSd-_cA8b1GxYnhD8qM0Y1yiuCI99dO2wbFdOm0p6zN4u4FL2BzRznRveTvGlWNqIoHXY4Ugz1o5utFwuwWi6nZrvIf9w](https://lh3.googleusercontent.com/proxy/aioCBLkdHuYPqd-_rYO4-394LXYuC4BL_zpAofSd-_cA8b1GxYnhD8qM0Y1yiuCI99dO2wbFdOm0p6zN4u4FL2BzRznRveTvGlWNqIoHXY4Ugz1o5utFwuwWi6nZrvIf9w)

source: [http://jwilson.coe.uga.edu/EMAT6680Su07/Brown/Assignment 12/Fibonnaci Sequence.htm](http://jwilson.coe.uga.edu/EMAT6680Su07/Brown/Assignment%2012/Fibonnaci%20Sequence.htm)

먼저 몇 개 입력에 대해서만 연습해보자

```java
f(0)=0
f(1)=1
f(2)=1
f(3)=2
f(4)=3
f(5)=5
f(6)=8
f(7)=13
f(8)=21
f(9)=34
```

피보나치 수열은 위와 같이 n 입력에 따라 다른 케이스로 구분된다 

✴️ 음.. 그런데 이렇게 보니 재귀용법으로도 접근할 수 있을 것 같은데..?

➡️ 재귀용법과 동적계획법으로 두가지 방법으로 접근해보자!

- 재귀용법으로 접근

```java
package dpConquer;

public class Fibonacci {

    //재귀용법으로 접근
    public static int fiboRecur(int n){
        if(n<=1){
            return n;
        }
        return fiboRecur(n-2)+fiboRecur(n-1);
    }

    public static void main(String[] args){
        System.out.println("fiboRecure(0): "+fiboRecur(0));
        System.out.println("fiboRecure(1): "+fiboRecur(1));
        System.out.println("fiboRecure(2): "+fiboRecur(2));
        System.out.println("fiboRecure(3): "+fiboRecur(3));
        /*
        * fiboRecure(0): 0
fiboRecure(1): 1
fiboRecure(2): 1
fiboRecure(3): 2
        *
        * */
    }
}
```

재귀용법으로 접근하면 위와 같이 케이스를 나누어서 메서드를 call하도록 하면 될 것이다

- 동적 계획법으로 접근

1.먼저 입력으로 들어오는 것을 "배열의 요소 갯수"라고 생각하고

그 크기만큼 배열을 만들자

```java
public static int fiboDp(int n){
        //n개 만큼의 배열을 만들기
        Integer[] cache=new Integer[n+1];
    }
```

2.그리고, 0일때와 1일때와 같은 케이스를 미리 넣어주자

```java
public static int fiboDp(int n){
        //n개 만큼의 배열을 만들기
        Integer[] cache=new Integer[n+1];

//미리 예외 케이스를 넣어두기
        if(n==0){
            **cache[0]=0;**
        }else if(n==1){
            **cache[0]=0;**
            **cache[1]=1;**
        }

    }
```

3.그리고 그 이후, 즉 인덱스 2부터는 f(n-1)+f(n-2)의 규칙이 적용되므로 이에 대해서는 for 루프를 이용해서 반복해서 넣어주자

```java
public static int fiboDp(int n){
        //n개 만큼의 배열을 만들기
        Integer[] cache=new Integer[n+1];

        //미리 예외 케이스를 넣어두기
        if(n==0){
            cache[0]=0;
        }else if(n==1){
            cache[0]=0;
            cache[1]=1;
        }else{
            cache[0]=0;
            cache[1]=1;
            //그 이후를 넣어주기
            for(int i =2; i < n+1; i++){
                **cache[i]=cache[i-2]+cache[i-1];**
            }
        }

    }
```

▶️2,3의 과정을 통해서 가장 작은 값을 이용해서 그에 대한 상위값을 계산할 수 있다는 것을 알 수 있다!

4.그러면, 결론적으로 cache[n], 즉 최상위값을 리턴해주면 된다

```java
public static int fiboDp(int n){
        //n개 만큼의 배열을 만들기
        Integer[] cache=new Integer[n+1];

        //미리 예외 케이스를 넣어두기
        if(n==0){
            cache[0]=0;
        }else if(n==1){
            cache[0]=0;
            cache[1]=1;
        }else{
            cache[0]=0;
            cache[1]=1;
            //그 이후를 넣어주기
            for(int i =2; i < n+1; i++){
                cache[i]=cache[i-2]+cache[i-1];
            }
        }

        **return cache[n];**
    }
```

▶️ 재귀용법은 각각의 값을 저장하지 않기 때문에 필요한 값들을 그때마다 계산하게 된다는 차이가 있다!

- 위에서 if 로 분기해둔 이유는, 0이나 1일 경우 인덱스에 대해서 java.lang.ArrayIndexOutOfBoundsException 이 발생할 수 있기 때문에 나눠둔 것이다!
- 분할정복은 지금 상태에서는 개념적으로만 이해해보고, 이후 병합정렬과 퀵정렬에서 알아보도록 하자