
## [TIL]Track Stack 없이 Exception 명칭 출력하기

참고:
1) java api 8
https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#getClass--

https://docs.oracle.com/javase/8/docs/api/

2) 예외처리
https://jyosssss.tistory.com/72

3)스택오버플로우 질문
https://stackoverflow.com/questions/29173645/java-only-get-exception-name-without-stacktrace


문득 공부하다가 보통 에러를 출력하게 되면
![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Exception/%EC%98%88%EC%99%B8%EC%B2%98%EB%A6%AC_%EC%98%88%EC%99%B8%ED%81%B4%EB%9E%98%EC%8A%A4%EB%AA%85%EC%B9%AD%20%EC%B6%9C%EB%A0%A5%ED%95%98%EA%B8%B0.PNG?raw=true)

위와 같은 추적된 에러 stack이 출력되는데, 

> java.util.InputMismatchException 만 출력할 수는 없을까? 이왕이면 깔끔하게 InputMismatchException 만 출력할 수는 없을까?

라는 생각이 들었다.

그래서 위의 참고자료를 바탕으로 정리해보면,
![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Exception/%EC%98%88%EC%99%B8%EC%B2%98%EB%A6%AC_print%20exception%20class%20name%20without%20stack.png?raw=true)
아래와 같이 정리될 수 있다!

아래는 그 중 한가지 소스코드이다

~~~
package com.exception03;

import java.util.Scanner;

public class ExceptionEx04 {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		/*System.out.println(1);
		System.out.println(2);
		try
		{
			System.out.println(3);
			System.out.println(4);
		}
		catch(Exception e)
		{
			System.out.println(5);
		}
		System.out.println(6);
		
		System.out.println();*/
		new ExceptionEx04().reflect();
	}
	public void reflect()
	{
		System.out.println("숫자 하나만 입력해주세요");
		Scanner sc=new Scanner(System.in);
		int val=0;
		
		try
		{
			val=sc.nextInt();
			System.out.println(val+1);
			System.out.println(val+2);
		}
		catch(Exception e)
		{	
		//	e.printStackTrace();
		//	String s=e.getClass().getCanonicalName();
			System.out.println(e.getClass().getSimpleName().toString());
		}
		System.out.println(val+3);
	}

}

~~~



