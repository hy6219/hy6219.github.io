# Calendar 클래스와 Date 클래스에 Intro!

 - Date 클래스 : 날짜 및 시간에 대하여 제공하는 기능이 미흡 
 - Calendar 클래스 : Date 클래스보다는 낫지만,
   그래도 부족 → java.time  패키지 등장!

# Calendar cal = Calendar.getInstance()

1. getInstance()를 사용하는 이유
   Calendar는 추상 클래스이기 때문(★getInstance() 메서드가 static인
   이유이기도!)

2. getInstance() 메서드가 static인 이유
~~~java
	public static Calendar getInstance()
    {
        return createCalendar(TimeZone.getDefault(),
        Locale.getDefault(Locale.Category.FORMAT));
    }

    private static Calendar createCalendar(TimeZone zone,
                                           Locale aLocale)
    {
		        ..(생략)
    }
~~~
(1) Calendar 클래스는 "추상 클래스"! 

→ static이 아니라면, 객체를 생성할 수 없음!
**(★주의★ abstract와 static 예약어를 동시에 사용할 수 없는 것이라,
이 경우에서는 전혀 관련없음!)**

(2) Calendar 내부에서 호출하는 메서드는 static 메서드인데, 인스턴스 변수도 존재하지 않기 때문!

3. Calendar.getInstance()의 국가에 따른 작동!
~~~java
Calendar cal = Calendar.getInstance();
~~~
(1) (국가 == "태국")? 
BuddhistCalendar 인스턴스 : GregorianCalendar 인스턴스

(2) GregorianCalendar : 윤년 개념을 포함하는 날짜 및 시간 클래스로,

"그레고리력"을 기반으로 함! (Calendar의 자손클래스!)

4. getInstance()로 인스턴스를 반환하는 이유 

- 최소한의 변경으로 프로그램이 동작하도록 하기 위함

# Calendar to Date!

~~~java
		Calendar cal = Calendar.getInstance();
		Date d1 = new Date(cal.getTimeInMillis());
		
		System.out.println("Calendar to Date: Calendar-"+
							cal+",\nDate - "+d1);
~~~

Calendar 인스턴스는 현재 한국이기 때문에, 그레고리력의 모든 속성을 

보여줄 수 있고, 이를 Date로 변환하게 되면

"요일 월 일 시: 분 : 초 [Locale(KST)] 연도" 형식으로 반환!

★Date 인스턴스 = new Date(Calendar 인스턴스.getTimeInMillis());로 변환하는 이유: Date 생성자 내부에는 long 타입이 들어가야 하기 때문!

![Calendar 클래스를 Date 클래스로 변환하기](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/CalendarToDate.PNG?raw=true)

# Date to Calendar

~~~java
		 //2.Date 인스턴스를 Calendar 인스턴스로 변환하기
		Date d = new Date();
		Calendar cal = Calendar.getInstance();
		
		cal.setTime(d);
		
		System.out.println("Date to Calendar: Calendar-"+
				cal+",\nDate - "+d);
~~~

★ Calendar 인스턴스.setTime(Date 인스턴스)로 변환해줄 수 있음

반환은 Calendar to Date와 동일!

![Date 클래스를 Calendar 클래스로 변환하기](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/DateToCalendar.PNG?raw=true)

# Calendar 인스턴스.get(field)

어떤 날짜나 연도 등에 대한 정보를 Calendar 인스턴스를 통해 얻고 

싶은 경우, 꼭!! get 메서드로 받아서 반환시켜줘야 한다!! 그렇지 않으면

기본설정된 값이 반환/이용될 수 있다!(예: Calendar.YEAR만 

System.out.println()으로 출력하면, 1을 출력)


★ field 변수들은 static 변수로 구성되어 있음(Calendar 클래스)

# Calendar 인스턴스. getActualMaximum(field)

field의 최댓값을 구할 수 있음

(응용)- 이번달의 마지막날 구하기
~~~java
Calendar today = Calendar.getInstance();

System.out.println("이달의 마지막날: getActualMaximum(Calendar.DATE)-->"+
					today.getActualMaximum(Calendar.DATE));//31
~~~

# Calendar.YEAR - 이 해의 연도 

~~~java
Calendar today = Calendar.getInstance();
System.out.println("올해-Calendar.YEAR(get): "+today.get(Calendar.YEAR));

~~~
![Calendar.YEAR](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Calendar.YEAR.PNG?raw=true)

# Calendar.MONTH- 이번 달

0~ 11의 값을 반환하고, 0이 1월!!
~~~java
Calendar today = Calendar.getInstance();
int mon = today.get(Calendar.MONTH) + 1;
		System.out.println("몇월-get(Calendar.MONTH+1): "+ mon);
~~~
![Calendar.MONTH + 1](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Calendar.MONTH.PNG?raw=true)

# Calendar.	WEEK_OF_YEAR, Calendar. WEEK_OF_MONTH

 - Calendar.WEEK_OF_YEAR: 이번 해의 몇 번째 주인지 출력 
 - Calendar.WEEK_OF_MONTH: 이번 달의 몇 번째 주인지 출력

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Calendar.WEEK_OF_YEAR%28MONTH%29.PNG?raw=true)

# Calendar.DAY_OF_YEAR
이번 해의 몇번째 일인지 반환
~~~java
Calendar today = Calendar.getInstance();
System.out.println("이 해의 몇번째 주- get(Calendar.WEEK_OF_YEAR): "+ today.get(Calendar.WEEK_OF_YEAR));
~~~
![Calendar.DAY_OF_YEAR](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Calendar.DATE,Calendar.DAY_OF_YEAR%28MONTH%29.PNG?raw=true)


# Calendar.DATE ==Calendar.DAY_OF_MONTH

몇일인지 반환

~~~java
Calendar today = Calendar.getInstance();
System.out.println("이 달의 몇일 get(Calendar.DATE): "+
						today.get(Calendar.DATE)+", get(Calendar.DAY_OF_MONTH): "+
						today.get(Calendar.DAY_OF_MONTH));
~~~


![Calendar.DATE, Calendar.DAY_OF_MONTH](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Calendar.DATE,Calendar.DAY_OF_YEAR%28MONTH%29.PNG?raw=true)

# Calendar.DAY_OF_WEEK, Calendar.DAY_OF_WEEK_IN_MONTH

 - Calendar.DAY_OF_WEEK: 일요일부터 토요일까지를 1~7로 반환 
 - Calendar.DAY_OF_WEEK_IN_MONTH: 이번달에 이번 요일이 몇번째로 찾아온 것인지 반환
~~~java
Calendar today = Calendar.getInstance();
System.out.println("요일(1:일요일 ~ 7:토요일)-get(Calendar.DAY_OF_WEEK):"+
						today.get(Calendar.DAY_OF_WEEK));//3-화요일
		System.out.println("이달의 몇번째 요일 -get(Calendar.DAY_OF_WEEK_IN_MONTH): "+
						today.get(Calendar.DAY_OF_WEEK_IN_MONTH));
~~~
![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Calendar.DAY_OF_WEEK%28IN_MONTH%29.PNG?raw=true)


### Calendar.AM_PM, Calendar.HOUR, Calendar.HOUR_OF_DAY, Calendar.MINUTE, Calendar.SECOND, Calendar.MILLISECOND, 타임존

 - Calendar.AM_PM: 오전, 오후 구분 (0 - 오전, 1 - 오후) 
 - Calendar.HOUR: 0~11로 시를 표현
 - Calendar.HOUR_OF_DAY: 0~23으로 시를 표현 
 - Calendar.MINUTE,Calendar.SECOND: 0-59의 수로 각각 분, 초를 표현
 - Calendar.MILLISECOND: 0~ 999의 수로 밀리초를 표현
 - 타임존(Timezone): GMT(그리니치 평균 시; Greenich Mean Time)를 기준으로 시간을 나타내는 동일 지역(한국 : +9)

~~~java
Calendar today = Calendar.getInstance();
System.out.println("TimeZone(-12~+12): "+today.get(Calendar.ZONE_OFFSET)/
				(60*60*1000));//ms 를 시 단위로 변경하기 위함
~~~
![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Calendar_%EC%8B%9C%EB%B6%84%EC%B4%88.PNG?raw=true)
