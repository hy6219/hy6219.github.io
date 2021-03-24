# ChoiceFormat, 연속/불연속적 범위의 값 처리에 적합한 형식화 클래스

ChoiceFormat은 어떤 연속적 혹은 불연속적 범주에 대해서 기준을 정하고, 이에 대한 입력값이 어떤 범주에 해당되는지를 분류해주는 형식화 클래스라고 생각하면 쉽다

***왜 필요하나요?*** 
보통, 예를 들어서, 학점을 부여하는 방식을 생각해보다

 - 90점< : A
 -  80점 < : B 
 - 70점 < :C 
 - 60점 < : D

라고 생각해보자(현실적으로는 +, -, 0, F학점 등이 더 있지만, 간단하게 생각해보자)

여태까지 우리는 아래와 같이 접근해왔다

~~~java
import java.util.Scanner;
...
   public static void main(String[] args)
   {
	   Scanner s = new Scanner(System.in);
	   int scoreIn = Integer.parseInt(s.nextLine());
	   System.out.println("grade(by if condition): "+getGrade(scoreIn));
	   System.out.println("grade(by switch-case): "+calGrade(scoreIn));
   }
	public static char getGrade(int score)
	{
		if(score>90)
		{
			return 'A';
		}
		else if(score > 80)
		{
			return 'B';
		}
		else if(score > 70)
		{
			return 'C';
		}
		else
		{
			return 'D';
		}
	}
	public static char calGrade(int score)
	{
		/*
		 *몫이 9-> A
		 *몫이 8-> B
		 *몫이 7-> C
		 *몫이 6-> D 
		 */
		
		int standard = score/10;
		char res = '\u0000';
		switch(standard)
		{
			case 9:
				res = 'A';
				break;
			case 8:
				res = 'B';
				break;
			case 7:
				res = 'C';
				break;
			case 6:
				res = 'D';
				break;
			default:
				break;			
		}
		return res;
	}
~~~
단점은, 복잡하게 처리된다는 것이다!

이제, 이 점에서 ChoiceFormat의 맛을 보자

# 값을 담고 있는 배열과 "값에 대한 기준을 담고 있는 배열"을 이용하기

이번 공간(?)에서는 ChoiceFormat 생성자 중 
~~~java
public ChoiceFormat(double[] limits, String[] formats) {
        setChoices(limits, formats);
    }

~~~
를 다룰 것이다

위의 경우에서 우리는 크게 두 가지를 꼭!! 지켜야 
효과적인 범주틀을 만들 수 있다!

1. double 배열은 낮은 값부터 큰 값 순서로 적어주자
2. double 배열의 크기 == String 배열의 크기  로 꼭! 매칭해주자!

한 번 해보자! 위의 학점 조건은 동일하게 끝까지 생각하기로 하자

~~~java
//1.범위는 낮은 값부터 큰값순으로 적어주기
		double[] limits = {60, 70, 80, 90};
		
		//2.limits 순서와 갯수를 맞추기
		String[] grades = {"D","C","B","A"};
		//limits와 grades는 1:1로 연결된 마치 키-값 관계로 된 map 자료구조와 비슷!
		
		
		int[] scores    = {100,95,88,70,52,60,70};
		
		ChoiceFormat form = new ChoiceFormat(limits, grades);
		
		for(int i = 0 ; i < scores.length; i++)
		{
			System.out.println(scores[i]+":"+form.format(scores[i]));
			/*
			 * 100:A
				95:A
				88:B
				70:C
				52:D
				60:D
				70:C
			 */
		}
~~~
마지막에는 출력된 결과를 적어두었는데, 원리는 우리가 위에서 값 x(경계값)을 기준으로, x이상이면 format된 범주에 대한 문자열을 반환하게 되는 ChoiceFormat 객체를 우리가 double, String 배열을 통해 만든 것이다.


# 패턴으로 ChoiceFormat 사용하기

이 방법은 패턴으로 제공되는 #, < 구분자만 조심하면 된다
1. #- 경계값을 범위에 포함
2. <- 경계값을 범위에 포함x

~~~java

String pattern = "60 < D| 70 < C | 80 < B| 90 < A ";
int[] scores = {91, 90, 80, 88, 70, 52, 60};
		
ChoiceFormat form = new ChoiceFormat(pattern);
for(int i = 0 ; i < scores.length; i++)
{
    System.out.println(scores[i]+":"+form.format(scores[i]));
	/*
91: A 
90: B
80: C 
88: B
70: D
52: D
60: D
	*/
}
~~~

위는 정규화 패키지의 모습과 많이 익숙하다고 생각한다. 여기서는 # 및 < 구분자를 이용하여 범주에 대해서 형식화할 String을 저장해두고, 이를 ChoiceFormat에 저장한다. 이후 범주에 따른 결과를 String으로 반환해주는 NumberFormat.class의 format 메서드로 입력값에 대한 범주에 맞는 형식화된 문자열을 반환받는다. 결과는 아랫줄의 주석과 같다.
