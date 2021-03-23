# Calendar.set(field,value): 원하는 시간으로 반환할 수 있도록!

[Reference]
[Calendar.set(field, value)](https://www.geeksforgeeks.org/calendar-set-method-in-java-with-examples/)

기존에 Calendar의 static 상수는 MONTH 등과 같이 반환하는 값이 일정값/범위로 지정되어 있는 경우가 있다!

하지만, set을 사용하면, 원하는 static 상수의 값을 value 변수의 값으로 지정하여 변경이 가능하다!

~~~java
Calendar cal = Calendar.getInstance();
		
		System.out.println("Before set MONTH value: "+cal.get(Calendar.MONTH));
		
		cal.set(Calendar.MONTH, 3);
		System.out.println("After set MONTH value: "+cal.get(Calendar.MONTH));

~~~
![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Calendar%20instance.set%28%29-%EC%9B%90%ED%95%98%EB%8A%94%20%EC%8B%9C%EA%B0%84%EC%9C%BC%EB%A1%9C%20%EC%84%A4%EC%A0%95%20%EA%B0%80%EB%8A%A5.PNG?raw=true)

# Calendar set 메서드의 다양한 오버로딩 메서드 형태

~~~java
 public void set(int field, int value)
    {
       
    }

    public final void set(int year, int month, int date)
    {
        set(YEAR, year);
        set(MONTH, month);
        set(DATE, date);
    }


    public final void set(int year, int month, int date, int hourOfDay, int minute)
    {
        set(YEAR, year);
        set(MONTH, month);
        set(DATE, date);
        set(HOUR_OF_DAY, hourOfDay);
        set(MINUTE, minute);
    }
~~~

이 중에서
~~~java
public final void set(int year, int month, int date)
    {
        set(YEAR, year);
        set(MONTH, month);
        set(DATE, date);
    }
~~~

를 이용한 경우를 예제로 연습해보도록 하자

~~~java
package com.part01_dateNcal.part01_Calendar;

import java.util.Calendar;

public class SetDayOfWeek {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		final String[] DAY_OF_WEEK=
				{"","일","월","화","수","목","금","토"};
		//기존의 값은 1부터 시작하기 때문에 맞춰주었음
		
		Calendar date1 = Calendar.getInstance();
		Calendar date2 = Calendar.getInstance();
	
		date1.set(2000,01,01);
		System.out.println("date1은 "+toString(date1)+" "+
				DAY_OF_WEEK[date1.get(Calendar.DAY_OF_WEEK)]+"요일입니다");
		System.out.println("오늘은 "+toString(date2)+" "+
				DAY_OF_WEEK[date2.get(Calendar.DAY_OF_WEEK)]+"요일입니다");
		
		//두 날짜 간 차이
		long diff=
				(date2.getTimeInMillis()-date1.getTimeInMillis())/(1000 * 24 * 60 * 60);//일
		System.out.println(toString(date1)+"일 부터 "+toString(date2)+"일 까지 "+
				diff+"일이 지났습니다");
		
	}


	public static String toString(Calendar date1)
	{
		return date1.get(Calendar.YEAR)+" 년 "+date1.get(Calendar.MONTH)+" 월 "+
				date1.get(Calendar.DAY_OF_MONTH)+"일 ";
	}
}


~~~
이렇게 될 경우, 연&&월 && 일을 매개변수로 받아 날짜를 다시 설정해주었고, toString메서드를 재정의하지 않고 다시 만들어 정의해주었기 때문에 새로운 날짜를 기준으로 다양한 활용을 해볼 수 있다!

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Calendar_set%EB%A9%94%EC%84%9C%EB%93%9C%EC%9D%98%20%ED%99%9C%EC%9A%A9.PNG?raw=true)
