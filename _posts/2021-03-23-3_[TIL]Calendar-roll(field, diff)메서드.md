
# Calendar-roll(field, diff)메서드

Calendar 클래스에 있는 roll 메서드는 static field의 값을 diff 만큼 변경시킬 수 있다! 하지만 몇가지 주의해야할 사항들이 있어, 정리해보자

1. Calendar 인스턴스.roll()메서드는 아래의 경우(2)를 제외하고서는 다른 static 상수값에 영향을 끼치지 않는다!

~~~java
public static void main(String[] args) {
		// TODO Auto-generated method stub
       Calendar date = Calendar.getInstance();
		
       date.set(2015, 0, 31);//2번에 해당
       System.out.println(toString(date));
       date.roll(Calendar.MONTH,1);
       System.out.println(toString(date));
		
       date.set(2015,12,31);
       System.out.println(toString(date));
       date.roll(Calendar.YEAR,1);
       System.out.println(toString(date));
       date.roll(Calendar.DAY_OF_MONTH,1);
       System.out.println(toString(date));
		
}
public static String toString(Calendar cal)
{
       return cal.get(Calendar.YEAR)+"년 "+
	      (cal.get(Calendar.MONTH)+1)+"월 "+
	     cal.get(Calendar.DATE)+"일";
}
~~~



2. 말일인데 MONTH 필드를 roll한 경우

★ roll된 MONTH 월의 말일로 DATE or DAY_OF_MONTH 값이 설정됨

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/Date_Calendar_roll.png?raw=true)
