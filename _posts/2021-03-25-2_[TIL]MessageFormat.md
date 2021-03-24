# MessageFormat
MessageFormat은 정해진 양식에 칸을 채워 넣고 배포하는 것과 같다.

예를 들어서, 어떤 a 경진대회에 신청하기 위해서는 해당 주최 사무실에 방문하여 참여신청서를 작성해야 하는 상황이 있다고 하자. 이 참여신청서가 사람마다 달라야 할까?

아니다


동일한 양식이어야 한다

바로 이것이다! 동일한 양식에 많은 사람/많은 이용 대상이 기록하게 되는 것! 그것이 바로 MessageFormat의 핵심 개념이다

단, 주의할 점 2가지가 있다

★ 주의 ★

1. String 패턴과 형식에 맞는 값이 담긴 객체를 넘겨준다
2. String 패턴에는 인덱스로 접근하는데, 순서가 반드시 순차적으로 0 , 1, 2, ... 이런식으로 지정할 필요는 없다

아래의 코드와 결과로 다시 한번 정리하고 마무리하자

~~~java
String msg = "Name: {0}\nHP: {1}\nAge:{2}\n"
				+ "BirthDay: {3}";
		
Object[] arguments=
{
	"이자바", "010-1234-5678","30","1996-01-01"
};
/*
 * MessageFormat.class
 * 
 * String 리턴, 
 * 매개변수- 패턴(String), Object(가변인자-->배열로 가능)args
 */
String res = MessageFormat.format(msg,arguments);
System.out.println(res);
		System.out.println("===================================");
msg ="Name: {0}\nHP: {2}\nAge:{3}\nBirthDay: {1}";
res = MessageFormat.format(msg,arguments);
System.out.println(res);

~~~

아래는 위의 예제 수행 결과이다
맥락을 배제하고 본다면, 인덱스 순서는 반드시 순차적일 필요가 없음을 알 수 있다

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/%EC%9D%B8%EB%8D%B1%EC%8A%A4%20%EC%A7%80%EC%A0%95%EC%9D%84%20%EB%B0%98%EB%93%9C%EC%8B%9C%20%EC%88%9C%EC%84%9C%EB%8C%80%EB%A1%9C%20%EC%A7%80%EC%A0%95%ED%95%B4%EC%A4%84%20%ED%95%84%EC%9A%94%EB%8A%94%20%EC%97%86%EB%8B%A4%28%EC%9C%84%EC%9D%98%20%EC%98%88%EC%A0%9C%EB%8A%94%20%EC%88%9C%EC%84%9C%EB%A7%8C%20%EC%83%9D%EA%B0%81%29.PNG?raw=true)
