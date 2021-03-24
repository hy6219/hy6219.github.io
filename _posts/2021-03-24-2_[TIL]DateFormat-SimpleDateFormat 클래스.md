
# DateFormat, SimpleDateFormat

저번에는 숫자를 형식화했다면, 이번에는 "날짜"를 형식화해보자!
SimpleDateFormat은 DateFormat의 하위클래스이다
https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

★주의할 점 ★
Calendar 클래스의 경우, format 메서드와 parse 메서드를 직접적으로 사용할 수 없다!
(Calendar타입을 지원하는 메서드가 존재하지 않기 때문)
*따라서 Calendar 인스턴스.getTime()등의 방법으로 Date로 변환 후 사용하도록 하자!
~~~java
Calendar cal = Calendar.getInstance();

...
DateFormat df1 = new SimpleDateFormat(pattern);
SimpleDateFormat df2 =
	new SimpleDateFormat(pattern);
Date d 		 = cal.getTime();
//혹은 Date d = new Date(cal.getTimeInMillis());

String out1 = df1.format(d);
String out2 = df2.format(d);
~~~

# 형식화된 문자열을 Date형태로 변경하기

DateFormat.class 내의 Date parse(String)을 이용하자!
~~~java
SimpleDateFormat sdf1=
	new SimpleDateFormat("yyyy/MM/dd");

String date   = "2015/12/31";
Date parsed   = sdf1.parse(date);
/*
 * 시간 차이 등을 위해서 Calendar가 필요하다면
 * Calendar cal = 
 *    Calendar.getInstance();
 * cal.setTime(parsed);로 변경해서
 * 응용해주면 됨
 */
~~~
★ **포맷인스턴스.format 메서드(Date 객체):**
*Date 객체* 를 **포맷인스턴스에 맞추어서** Date형으로 반환

# Date 객체를 형식화된 문자열로 변경하기

★핵심! ★
[format 패턴이 저장된 인스턴스].format(Date객체);

~~~java
DateFormat df= new SimpleDateFormat("yyyy년 MM월 dd일");
Date d;
		try
		{
		//형식화된 문자열을 Date로 바꾸어줌
			d = df.parse("2015년 3월 23일");
			
			/*
			 * 형식화해주지 않으면, Date에서 반환하는 시간이라던지, KST 등 모두
			 * 출력되어지는데, 시간부분은 0으로 되어있을것!(설정을 해두지 않아서))
			 */
			
			//format 메서드: 인스턴스 형식에 따라 매개변수를 형식화해서 문자열로 반환하기
			System.out.println(df.format(d));
		}
		catch(ParseException e)
		{
			e.printStackTrace();
		}//2015년 03월 23일 출력
~~~

이제 조금 응용해서! 스캐너로 형식화된 입력을 받고, Calendar의 메서드를 활용해서 시간 차이를 구해보자!
~~~java
String pattern = "yyyy/MM/dd";
		//다형성에 의거해서 형식화 객체 만들기
		DateFormat df1 = new SimpleDateFormat(pattern);
		
		Scanner s 	   = new Scanner(System.in);
		
		Date input	   = null;
		
		System.out.println("> 입력: 2015/12/31과 같이 날짜를 입력해주세요");
		
		Loop:
		while(s.hasNextLine())
		{
			try
			{
				//문자열을 Date형태로 바꾸기
				input  = df1.parse(s.nextLine());
				break Loop;
			}
			catch(Exception e)
			{
				System.out.println("> 입력: 2015/12/31과 같이 날짜를 다시 입력해주세요");
			}
		}
		
		//Date->Cal
		Calendar cal  = Calendar.getInstance();
		cal.setTime(input);
		Calendar today = Calendar.getInstance();
		//몇시간 차이인지 확인
		long hour = (cal.getTimeInMillis() - today.getTimeInMillis()) / (3600 * 1000);
		String c  = new StringBuffer(cal.get(Calendar.YEAR)+"/"+
				(cal.get(Calendar.MONTH)+1)+"/"+
				cal.get(Calendar.DAY_OF_MONTH)
				).toString();
		String t  = new StringBuffer(today.get(Calendar.YEAR)+"/"+
				(today.get(Calendar.MONTH)+1)+"/"+
				today.get(Calendar.DAY_OF_MONTH)
				).toString();
		
		System.out.println("입력하신 날짜인 "+c+"은 "+
					t+"와 "+hour+"시간 차이가 있습니다");

~~~

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Date-String%EA%B0%84%20%EB%B3%80%ED%99%98,%20Calendar%20%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC%20%ED%99%9C%EC%9A%A9%ED%95%9C%20%EC%8B%9C%EA%B0%84%20%EC%B0%A8%EC%9D%B4%20%EA%B5%AC%ED%95%98%EA%B8%B0.PNG?raw=true)

위처럼, 사용자로부터 어떠한 형식화된 입력값을 받아온 후 시간차이를 계산하기 위해서는, 몇가지 단계가 존재한다. 차례로 정리하면서 마무리해보자

1. 형식화된 입력을 받기
2. 그 입력을 parse를 통해서 Date객체로 저장해두기
3. Date 객체를 Calendar 타입으로 바꾸기
4. Calendar.getTimeInMillis()를 통해서 시간차이를  계산하기

이러한 과정 중 2번은 String을 Date로 바꾸는 parse 메서드, 3번은 저번 게시물이었던 Date 객체를 Calendar 타입으로 바꾸는 것과 관련되어 있다! 한번씩 꼭 짚고 넘어가자!
