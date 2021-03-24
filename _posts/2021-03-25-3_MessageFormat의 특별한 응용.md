# MessageFormat, 인덱스를 이용하면서 parse로 해당되는 객체의 요소에 접근

MessageFormat 클래스는 인덱스로 접근한다

여기에 덧붙여서 생각할 것은 
~~~java
public Object[] parse(String source) throws ParseException 
{
     ... 
     Object[] result = parse(source, pos);
     return result;       
 }

~~~

이 부분,  MessageFormat.class 내에 존재하는 Object 배열을 반환하는 parse메서드이다.

반환하는 객체는 MessageFormat.class 내에 존재하는 "public Object[] parse(String source, ParsePosition pos)" 메서드이다

즉, format이 형식에 맞춰서 값을 넣은 한 줄로 된 String을 반환한다면

MessageFormat의 parse는
인덱스에 맞는 값들을 하나의 Object 배열 내에 묶어서 Object 배열로 바꾸어 반환해줄 수 있다는 점이 다르다

바로 아래처럼 말이다
~~~java
String tableName = "CUST_INFO";
		
String msg = "INSERT INTO "+tableName+" VALUES (''{0}'',''{1}'',''{2}'',''{3}'')";
		
Object[][] argu =
			{
					{"이자바", "010-1234-5678","30","1996-01-01"},
					{"정자바", "010-1234-5678","30","1996-01-01"}
			};
		
for(int i = 0 ; i < argu.length; i++)
{
	String res = MessageFormat.format(msg, argu[i]);
	System.out.println(res);
}

~~~

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/MessageFormat-parse%20%EB%A9%94%EC%84%9C%EB%93%9C.PNG?raw=true)
