# 재귀 용법 Recursive call; 재귀 호출

- 함수 안에서 동일한 함수를 호출하는 형태

예제 - 팩토리얼을 구하는 알고리즘 생각

n! = n * (n-1) * (n-2) * (n-3) * ... * 1

   =  n * **(n-1)!**

즉, factorial(n)=n*factorial(n-1)의 규칙성을 보이게 된다! (단, n>1) 그리고 factorial(n-1)은 내부적으로 풀면 또 (n-1)*factorial(n-2)를 불러온다. 즉, 똑같은 패턴이 "되풀이(recursive)"되는 것을 볼 수 있다!

이를 직관적으로 표현해보면

```java
public static int factorial(int n){
        **if(n<=1)**{
            return 1;
        }else{
            **return n*factorial(n-1);**
        }
    }
```

위와 같이 1 이하는 1을 반환하고

그 외에는 n*factorial(n-1)을 반환하게 한다!

그러면 factorial(n-1)은 내부적으로 또 해당 메서드를 불러서 적용하게 될 것이다!

그리고 인자값으로 3을 넣고 테스트를 해보면 콘솔에 6이 표시되는 것을 알 수 있다!

```java
package recursive;

public class Factorial {
    public static int factorial(int n){
        if(n<=1){
            return 1;
        }else{
            return n*factorial(n-1);
        }
    }

    public static void main(String[] args){
        System.out.println(factorial(3));//6
    }
}
```

위의 경우, 재귀호출에 의해 해당 함수가 n-1번 호출되므로

시간복잡도는 O(n)이 된다!

그리고 공간복잡도는 내부적인 호출에 의한 값이 지역변수처럼 n-1개 존재하게 되므로 O(n)이 된다!

---

▶️ 재귀 호출의 일반적인 형태

작성 패턴은 크게 2가지로 보이는데, 흐름은 동일하다!

```java
function(입력){
 if(입력 <= 일정값){
    return 일정값 or 입력값 or 특정값;
  }else{
    return function(입력-1);
  }
}
```

```java
function(입력){
   if(입력<=일정값){
      return 일정값 or 입력값 or 특정값;
    }
    return function(입력-1);
}
```

🌟 재귀호출은 스택의 전형적인 예시!!

- 함수는 내부적으로 스택처럼 관리됨
- 위에서처럼 팩토리얼을 호출하게 되면, 2이상부터는 스택에 n*factorial(n-1)이 축척된다
- 그리고 return을 하게 될 때에는 위에서부터 pop하면서 값이 전달된다!

![https://miro.medium.com/max/826/1*KcVIy-BMNuJ7H6wh4yS8kw.png](https://miro.medium.com/max/826/1*KcVIy-BMNuJ7H6wh4yS8kw.png)

source: [https://medium.com/@williambdale/recursion-the-pros-and-cons-76d32d75973a](https://medium.com/@williambdale/recursion-the-pros-and-cons-76d32d75973a)

이러한 재귀 방식을 접목해서 배열의 합을 생각해보면,

처음에는 

arr[0]+arr[1]+arr[2]+... +arr[n-1]=arr[0]+(arr[1]+arr[2]+... +arr[n-1])

과 같이 생각해보았는데, 이 방식도 괜찮지만,

arr[0]+arr[1]+arr[2]+... +arr[n-1]=arr[n-1]+(arr[0]+arr[2]+... +arr[n-2])

로 접근하는 것으로 시도해보았다!

해당 방식으로 생각해보면, 배열의 크기를 1씩 줄인 복사된 배열이 필요한데,

이때, System.arrayCopy도 괜찮지만, 

- 배열크기-1

이 필요하다고 생각해서, 케이스를 조금 더 다분화 했다! 그 이유는 NegativeArraySizeException 이 예상되었기 때문이다

그래서

- case 1: 배열크기가 0인 경우 ▶️ 0반환
- case 2: 배열크기가 1인 경우 ▶️ 복잡도를 줄이기 위해서 arr[0]을 바로 반환
- case 3: 배열크기가 2이상인 경우▶️ 인자로 주어진 배열의 크기보다 크기가 1 작은 배열로 배열을 복사하고, 이를 이용하자!(편의상 B라고 하겠다)

arr[n-1]+B를 리턴!

위의 논리로 접근해보면

```java
public static int summation(Integer[] arr){
        //sum(a[0]+..+a[n-1])=a[0]+sum(a[1]+...+a[n-1])
        //sum(a[0]+..+a[n-1])=a[n-1]+sum(a[0]+...+a[n-2])
        int size = arr.length;

        if(size<=0){
            return 0;
        }else if(size==1){
            return arr[0];
        }else{
            int clSize = size-1;
            Integer[] cloned = new Integer[size-1];

            for(int i = 0 ; i  < clSize; i++){
                cloned[i] = arr[i];
            }
            return arr[clSize]+summation(cloned);
        }
    }
```

위와 같이 정리해볼 수 있다

그리고 이를 테스트해보자!

```java
public static void main(String[] args){
        System.out.println("배열 {1,2,3,4,5,6,7,8,9,10}의 합: "+summation(arr));
//배열 {1,2,3,4,5,6,7,8,9,10}의 합: 55
    }
```

그러면 1부터 10까지의 합이 알맞게 계산된 것을 확인해볼 수 있다!

지금 위의 경우를 ArrayList를 이용한다면, ArrayList의 List subList(int fromIndex, int toIndex) 메서드를 이용하면 유용하다!

다만, 

Exception in thread "main" java.lang.ClassCastException: java.util.ArrayList$SubList cannot be cast to java.util.ArrayList

와 같은 cast 에러가 날 수 있기 때문에 파라미터를 List로 해보거나,

아니면,  new ArrayList<>(list.subList(~))로 해보자!

```java
public static int summation2(ArrayList<Integer> list){

        int size= list.size();
        if(size<=0){
            return 0;
        }
        //sublist
        ArrayList<Integer> cloned = new ArrayList<>(list.subList(1,size));
        return list.get(0)+summation2(cloned);
    }
```

 

그러면 위와 같이 간단하게 접근할 수 있다! 이 방법은 인덱스 1부터 시작하면 2번째 요소부터 클론할 수 있다는 점에 기인한 것이다!

---

백준 9095번 문제-1, 2, 3 더하기

[9095번: 1, 2, 3 더하기](https://www.acmicpc.net/problem/9095)

정수 4를 1, 2, 3의 합으로 나타내는 방법은 총 7가지가 있다. 합을 나타낼 때는 수를 1개 이상 사용해야 한다.

- 1+1+1+1
- 1+1+2
- 1+2+1
- 2+1+1
- 2+2
- 1+3
- 3+1

정수 n이 주어졌을 때, n을 1, 2, 3의 합으로 나타내는 방법의 수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 테스트 케이스의 개수 T가 주어진다. 각 테스트 케이스는 한 줄로 이루어져 있고, 정수 n이 주어진다. n은 양수이며 11보다 작다.

## 출력

각 테스트 케이스마다, n을 1, 2, 3의 합으로 나타내는 방법의 수를 출력한다.

위의 문제를 재귀용법을 적용해보기 전 규칙성을 찾아보기 위해서 직접 5까지만 적용해보았다

[백준9095번 규칙성 찾기](https://www.notion.so/1177b39a6c06496cbba13e48dc15a358)

위의 표를 실제로 종이에 그려보았는데, 

- 1일 때는 1을, 2일 때는 2를 반환! ▶️ n ≤ 2일 때 n 반환
- 3일때는 4를 반환
- 4 이상부터는 함수로 표현하면, f(n)=f(n-3)+f(n-2)+f(n-1) 의 형태

를 보인다는 것을 찾았다!

그래서 이를 재귀방법과 접목하면 아래와 같이 정리해볼 수 있었다!

```java
package recursive.boj9095;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
    public static int special(int n){
        if(n<=2){
            return n;
        }else if(n==3){
            return 4;
        }else{
            //f(n)=f(n-3)+f(n-2)+f(n-1)
            return special(n-3)+special(n-2)+special(n-1);
        }
    }
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int T = Integer.valueOf(br.readLine());

        for(int i = 0 ; i < T;i++){
            int temp = Integer.valueOf(br.readLine());
            int res = special(temp);
            System.out.println(res);
        }
    }
}
```