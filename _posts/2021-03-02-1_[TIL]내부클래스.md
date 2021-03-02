## 내부클래스(Inner Class)

 - **핵심 1: 내부클래스도 *외부클래스의 멤버* 다!**
[1] 내부클래스  외에서 접근하는 경우
1.외부 클래스 인스턴스 생성
~~~
outer o=new outer();
~~~
2.접근하려는 내부클래스 유형에 따른 접근 

 - 인스턴스 내부클래스인경우

~~~
outer.InstanceInner inst=o.new InstanceInner();
~~~

혹은

~~~
InstanceInner ii2=o.new InstanceInner();
~~~
로 간접접근!

 - static(클래스) 내부 클래스인 경우

~~~
StaticInner si=new StaticInner();
~~~

로 직접 접근!
[내부클래스] 

   ~~~
    package com.inner04;
    
	public class outer {
	class InstanceInner
	{
		int iv=100;
	}
	static class StaticInner
	{
		int iv=200;
		static int cv=300;
	}
	void myMethod()
	{
		class LocalInner
		{
			int iv=400;
		}
		LocalInner li=new LocalInner();
		System.out.println("li iv: "+li.iv);
		}
	}
~~~
	
[실행코드 1]
~~~
package com.inner04;

import com.inner04.outer.*;

public class InnerEx4 {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		outer o=new outer();
		/**
		 * 인스턴스클래스의 인스턴스 생성시,
		 * 외부 클래스의 인스턴스를 먼저 생성해야 함
		 * 
		 * 내부클래스도 외부클래스의 멤버이기 때문에
		 * 인스턴스 멤버변수처럼
		 * "외부 클래스 인스턴스.내부 클래스 참조변수 	내부클래스 인스턴스=~"로 접근!
		 */
		outer.InstanceInner ii=o.new InstanceInner();
		InstanceInner ii2=o.new InstanceInner();
		StaticInner si=new StaticInner();
	}

}

~~~

[2] 내부클래스  간 접근하는 경우
[소스코드]
~~~
package com.inner03;

public class InnerEx3 {

	private int outerIv=1;
	static int outerCv=2;
	int outerDv=3;
	class InstanceInner
	{
		/**
		 * 인스턴스 클래스는 외부클래스의 private 멤버도 접근 가능
		 */
		int iiv=outerIv;
		int iiv2=outerCv;
		int iiv3=outerDv;
		
		InstanceInner inst=new InstanceInner();
		StaticInner si=new StaticInner();
	}
	static class StaticInner
	{
		//int siv=outerIv; ->static 클래스 내부에서는 외부클래스의 인스턴스 멤버가 접근할 수 없음
		InnerEx3 ii=new InnerEx3();
		int siv=ii.outerIv;
		
		static int scv=outerCv;
		InstanceInner inst=ii.new InstanceInner();
		StaticInner sii=new StaticInner();
	}
	
	void myMethod()
	{
		int iv=10;
		final int LV=20;//JDK 1.8~: final 생략 가능
		
		class LocalInner
		{
			int liv=outerIv;
			int liv2=outerCv;
			//int liv3=lv;//->지역클래스를 감싸고 있는 근처 외부클래스의 경우, final이 붙은 변수만 접근 가능
			//JDK 1.8~: 에러 아님
			int liv3=LV;
			int liv4=outerDv;
		}
		LocalInner l=new LocalInner();
		System.out.println("LocalInner liv: "+l.liv+", liv2: "+l.liv2+", liv3: "+l.liv3+", liv4: "+l.liv4);
	}
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		InnerEx3 i=new InnerEx3();
		i.myMethod();
		StaticInner si=new StaticInner();
		System.out.println("StaticInner siv: "+si.siv);
		System.out.println("StaticInner InstanceInner inst var: "+si.inst.iiv);
	
	}

}

~~~


위의 코드처럼

1.내부 클래스 인스턴스를 생성하려는 영역이

 - **인스턴스 클래스인 경우 : 모두 직접 접근 가능!!**

(1) 생성하려는 클래스가 인스턴스 클래스인 경우

~~~
InstanceInner inst=new InstanceInner();
~~~

(2) 생성하려는 클래스가 static 클래스인 경우

~~~
StaticInner sii=new StaticInner();
~~~

**static 클래스인 경우 : static만 직접 접근 가능!!**
(1) 생성하려는 클래스가 인스턴스 클래스인 경우

~~~
InstanceInner inst=o.new InstanceInner();
~~~

(2) 생성하려는 클래스가 static 클래스인 경우

~~~
StaticInner sii=new StaticInner();
~~~


<<정리>>

~~~
class Outer
{
	InstanceInner
	{
		//Both can access directly!
		InstanceInner ii=new InstanceInner();
		StaticInner si=new StaticInner();
	}
	static StaticInner
	{
		//only static can access directly!
		StaticInner sii=new StaticInner();

		Outer o=new Outer();
		InstanceInner ii=o.new InstanceInner();
	}
}
public class Tester
{
	public static void main(String[] args)
	{
		Outer o=new Outer();//generate outer class instance
		//indirect access to instance inner class
		InstanceInner ii=o.new Outer();
		outer.InstanceInner ii2=o.new Outer();
		//direct access to static inner class
		StaticInner si=new StaticInner();
	}
}
~~~


