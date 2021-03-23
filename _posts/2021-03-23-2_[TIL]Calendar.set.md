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
