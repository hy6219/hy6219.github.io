
# 숫자를 포맷하는 클래스, DecimalFormat

DecimalFormat은 다양한 형태로 숫자를 형식화하거나, 형식화된 문자열을 역으로 숫자로 돌이킬 수 있도록 기능을 지원해준다

# 숫자를 형식화된 문자열로 

참고로 format 메서드는 Format.class에 저장되어 있는 메서드로, 아래와 같이 정의되어 있다
~~~java
public final String format (Object obj) {
        return format(obj, new StringBuffer(), new FieldPosition(0)).toString();
    }
~~~
잠시 return 되는 추상메서드인 format을 연결해서 확인해보면,

~~~java
public abstract StringBuffer format(Object obj,
                    StringBuffer toAppendTo,
                    FieldPosition pos);
~~~
즉, String format 메서드는 매개변수로 전달받은 새로운  StringBuffer 객체에 offset/시작 위치를 FieldPosition로 

부터 전달하고, 그 위치부터 패턴을 의미하는 매개변수 obj에 맞추어 값을 넣고, StringBuffer의 toString()으로 

String 객체를 반환해주는 것이다

그렇다면, 이것만 추가로 기억해주자! 이렇게 한다면, 숫자를 형식화된 문자열로 만들 수 있다!

★[방법 2]
1. DecimalFormat 객체에 패턴을 저장해두기
2. DecimalFormat객체.format(Number 객체)

아래의 예제로 패턴에 따라 문자열을 만들어보자
~~~java
DecimalFormat df = 
	new DecimalFormat("#.###E0");//지수기호이용
double num1 = 1234567,89;
Double num2 = 123.4567;//auto-boxing

//num1에 대한 auto-boxing이 이루어짐)
String parsedNum1 = df.format(num1);
//매개변수는 객체!
String parsedNum2 = df.format(num2);
~~~

# 형식화된 문자열을 숫자로
형식화된 문자열을 숫자로 바꾼다?

기존의 Wrapper 클래스에 존재하는 parsing 지원 메서드만으로는 지원될 수가 없다

"그러면?"

이것만 기억하자!

★[방법 1]
1. DecimalFormat, NumberFormat에 존재하는 DecimalFormat(<-pattern 저장) 객체.parse(String source) 메서드를 통해서 Number(Wrapper 클래스의 슈퍼클래스!)객체를 반환받기
2. 반환받은 Number 객체를 사용하고자 하는 기본형으로 바꾸어주기
3. 1의 과정에서 parse는 ParseException을 발생시키므로(throws), 적절히 예외처리하자!
예) 
~~~java
//0.패턴을 저장
DecimalFormat d = 
		new DecimalFormat(pattern);
//1.+3.
Number wrapper;
double parsed ;
try
{
	wrapper = d.parse("1234567.89");//parse(string)
	//2.
	parsed  = wrapper.doubleValue();
	System.out.println("wrapper: "+
		wrapper+", toDouble: "+
		parsed);
}
catch(ParseException e)
{
	//..예외처리
}
}
~~~
위와 같은 과정만 잘 숙지, 연습한다면 랩퍼 클래스에서의 한계를 NumberFormat의 parse메서드를 통해서 시원하게 해결볼 수 있을 것이다!



아래의 예제를 통해 숫자를 형식화된 문자열로, 형식화된 문자열을 숫자로 변환하는 과정을 다시 한번 점검해보자
~~~java
DecimalFormat df1 = new DecimalFormat("#,###.##");
DecimalFormat df2 = new DecimalFormat("#.###E0");
Number num;//Wrapper class
		
	try
	{
	   /*
	    * Number format: DecimalFormat의 조상 클래스
	    */
			num = df1.parse("1,234,567.89");
			System.out.print("num: "+num+",\n"+ "decimal format"
					+ " 인스턴스.parse(string)후 double형으로 저장(wrapper)=>");
			
			double d = num.doubleValue();
			System.out.print(d+",\ndecimal format(객체)의 format 메서드를 이용->");
			System.out.println(df2.format(num));
			
			System.out.println();
	 }
	catch(Exception e)
	{
			e.printStackTrace();
	}
~~~
![formatted string to number](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/JAVA/Calendar_Date/DecimalFormat-String,%20Obj.PNG?raw=true)
