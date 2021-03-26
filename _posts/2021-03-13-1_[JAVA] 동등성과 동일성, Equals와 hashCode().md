# [JAVA] 동등성과 동일성, Equals와 hashCode()

참조형 변수에 대한 동등성과 동일성에 대한 고민은 **'동일한 객체를 가리키는 것'**과 같은 관점에서 중요한 개념이므로 짚고 넘어가도록 하자

*-컬렉션 공부 후 조금 더 보완하여 정리하도록 한다!*

# 동등성과 동일성

동등성과 동일성, 두 개념은 아래와 같이 개념을 정리, 접근해볼 수 있다

 1.동등성(Equality) : *주소값의 같음 여부와 상관없이, "**값(객체의 정보)"가 같은 경우*** -equals, hashCode, identityHashCode


→ (i) equals()를 통하여 두 객체의 값이 같은지 확인 
 단! 원래의 equals는 두 객체가 가리키는 곳이 같은지를 확인하는 개념이므로, **오버라이딩** 해주어야 한다!



Example)
 

    public boolean equals(Object obj)
    {
	  if(this == obj )
	  {
		  //같은 주소인 경우, 값도 같겠죠!
		  return true;
	  }
	  if(obj == null )
	  {
		  //전달받은 객체가 비어있는(null) 경우, 비교할 필요 없음
		  return false;
	  }	  
	  if(obj instanceof [Class])
	  {
		  //obj가 [Class명] 의 객체일 때,
		  if((Class)obj.[member var1] == [member var1] &&
			  (Class)obj.[member var2].equals([member var2]))
			  {
				  //전달받은 객체의 값과 비교대상인 객체의 값이
				  //같으면 당연히 "동등객체!"
				  return true;
			  }
			  else
			  {
				  //같은 [Class명] 의 객체인데,
				  //객체 값이 다르면 동등객체가 아님!
				  return false;
			   }
	  }
	  else
	  {
		  //참조변수 타입이 다른 경우, 상속과 관련되어
		  //[Class 명] extends Parent 혹은
		  //[Other class명] extends [Class 명]인 경우,
		  // 멤버변수 차이가 있을 수 있기 때문에 포함되는
		  // 멤버변수 값만 같고 나머지 멤버변수 값이 다를 수 있어서
		  //이에 따른 적절한 리턴을 해줘야 함!
		  if(~)
		  {
			  return true;
		  }
		  else
		  {
			  return false;
		   }
		}
		
	  


 
 (ii) 위와 같이 equals 메서드를 오버라이딩 하지 않았을 때의 결과(쉽게 설명하자면, 두 객체의 주소값이 다른지 확인 결과를 의미)가
	 가. 결과가 true인 경우 : 

즉, 두 개의 객체가 '같은' 객체라면, 가리키는 '주소' 값도 같아야 하므로 hashCode()도 같은 결과가 나와야 함!
따라서, equals와 hashCode를 통하여 오버라이딩할 필요!(→동일성 ; 그리고 이것은 밑의 규약에서도 언급됨!)


   나. 결과가 false인 경우 : 

즉, 두 개의 객체가 '다른' 객체인 경우,  반드시 hashCode()를 오버라이딩할 필요성은 없음(hashCode()값이 같아도 되고, 달라도 되기 때문)

★ 하지만, 가능하면 같은 hashcode값을 리턴할 수 있도록!

-밑의 주의점[1]과 유사한 맥락인데, consistency(정합성) 규약과 관련은 없지만, immutable한 객체처럼, 값을 변경하면 주소값이 변경되는 것처럼, 변수 1개당 주소 1개로 mapping하게 된다면, 여러 객체가 있는데 그 중 하나의 객체를 검색하는 데에 용이할 것!

***Immutable한 객체의 경우 주의점***

[1]key로 Immutable한 객체로 사용되어야 하지만 그러지 못할 경우(예: Example*), mutable한 멤버로 equals나 hashCode 등을 이용한 비교하지 않도록!
Example*)
	
    People p1 = new People("before");
    [p1을 등록]
    p1.[var1] = "after";
	    
    People p2 = new People("after");
    [p2를 등록]

*- 위와 같이 string constant pool에 같은 내용인 "after"가 들어갔는데(related to: String constant pool----->따라서, 주소와 값이 같아야!),
다른 주소값(hashCode())을 반환하는 경우!
<=> 즉,  마치

    int[] z = {1,2,3};
		System.out.println(z);//[I@4e25154f
		z[0] = 5;
		System.out.println(z);//[I@4e25154f
와 같이 mutable처럼 동작하는 것을 의미! 잘 보면, 위는 String, 즉 immutable한 값을 다루고 있음!

쉽게 말하면, immutable 값(멤버)를 변경한 결과가 전혀 반영되지 않아, 따로따로 저장된 것! 다르게 본 것!

*-보충설명을 하자면, String의 경우, 값이 바뀌면, 새로운 공간을 만들어 값을 저장하는 immutable한 특성이 있음! 

**Example plus)**
- 1학년 2반에 홍길동이라는 학생이 있다고 가정
- 홍길동 학생의 원래 성적이 95점이었다고 가정
- 오채점으로 홍길동 학생의 성적을 100점으로 변경해야 함
- 멤버 변수: String grade, String room, String name, int score 로 가정
→ 이 경우, "홍길동"이라는 사람으로만 전교 내에서 2명이 있다고 가정
(변수를 줄여서 생각해보기 위해서)

그러면 "어떻게 접근해야 1학년 2반에 있는 홍길동" 이라는 사람의 점수를 바꾸면서도, 동일 인물을 여전히 가리킬 수 있을까?
(equals 오버라이딩 면에서)

→ int score로 접근해서 비교해야 할까? - no!!
 : 같은 점수인 사람이 있을 수 있는데?
 
→ String grade, String room, String name으로 접근할까?-yes!!
: 위의 세 필드는, 값이 바뀌지 않는 한, 같은 곳을 가리키기 때문!

따라서 다음과 같이 가능하다!

    package com.objectClass.equalityNidentity.immutable;

	import java.util.Objects;

	public class Student {

	private String grade;
	private String room;
	private String name;
	private int score;
	
	public Student() {
	
		// TODO Auto-generated constructor stub
	}

	public Student(String grade, String room, String name, int score) {
	
		this.grade = grade;
		this.room = room;
		this.name = name;
		this.score = score;
	}
	

	public String getGrade() {
		return grade;
	}

	public void setGrade(String grade) {
		this.grade = grade;
	}

	public String getRoom() {
		return room;
	}

	public void setRoom(String room) {
		this.room = room;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getScore() {
		return score;
	}

	public void setScore(int score) {
		this.score = score;
	}

	
	
	
	@Override
	public boolean equals(Object obj)
	{
		if(Objects.deepEquals(this, obj))
		{
			return true;
		}
		
		if(obj == null)
		{
			return false;
		}
		
		if(obj instanceof Student)
		{
			if(grade == ((Student)obj).grade &&
					room == ((Student)obj).room &&
					name == ((Student)obj).name
					)
			{
				return true;
			}
			else
			{
				return false;
			}
		}
		else
		{
			return false;//우선은 다른 클래스를 만들지 않는다는 전			
			//제하에
		}
	}
	@Override
	public int hashCode()
	{
		//mutable은 빼기!
		return Objects.hash(grade, room, name);
	}
	
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Student s = new Student();
		s.setGrade("1");
		s.setRoom("2");
		s.setName("홍길동");
		s.setScore(95);
		
		System.out.println(s.hashCode());//54228492-a
		s.setScore(100);
		System.out.println(s.hashCode());//54228492-b
		}
	}


 만약, hashCode()를 아래와 같이 오버라이딩 했다면?

    @Override
	public int hashCode()
	{
		return Objects.hash(grade, room, name, score);
	}

a와 b는 아래와 같이 서로 다른 주소값을 반환!
1681083347
1681083352

 2.동일성(Identity) : 주소값과 값 모두가 같은 것을 확인
 
==, !=

**참고자료 및 사이트
:
https://www.baeldung.com/java-equals-hashcode-contracts
https://blog.weirdx.io/post/3113
**
<a href="http://iilii.egloos.com/4000476" rel="nofollow" style="text-decoration:none;">건강착실청년님(iilii) 블로그 글을 참고하였습니다! 링크검색을 배제하기 위해서 nofollow되었습니다</a>
## 동등성, 조금만 더 알아보자!

**참고자료 : https://www.cs.cornell.edu/courses/cs211/2006sp/Lectures/L14-Comparison/L14cs211sp06.pdf**


#추가적으로 확인하는 것은 추후 정리
- Shallow Equality: 단순히 요소들의 주소값이 같은지를 equals()로 확인
- Deep Equality   : 기본형 자료까지 재귀적으로 훑어가면서 주소값이 같은지 확인; Objects.deepEquals(obj1,obj2) --Arrays.deepEquals0(a,b)와 관련


## equals()관련 규약

(1)reflexive (반사성): a.equals(a) == true! 
> an object must equal itself

(2)symmetric(대칭성): a.equals(b) == b.equals(a)

(3)transitive(추이성) : 마치 삼단논법!
a.equals(b) && b.equals(c) -->a.equals(c)

(4)consistent(정합성; 일관성): 
- equals()값은 포함된 속성이 변경되는 경우에만 변경됨
<=>null값이 아닌 두 참조값을 비교하는 경우, 값이 변경되지 않는 한, 계속해서 똑같은 boolean 값(equals() 결과값)을 반환

- 참조타입 변수인 x,y 에 대해서 둘 중 하나가 null이면 결과값은 무조건 false

****참고 : https://jaehun2841.github.io/2019/01/10/effective-java-item10/#%EC%83%81%EC%86%8D-%EB%8C%80%EC%8B%A0-%EC%BB%B4%ED%8F%AC%EC%A7%80%EC%85%98composition%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC
https://www.baeldung.com/java-equals-hashcode-contracts****

## hashCode() 관련 규약

(1) internal consistency(내부 일치성; 내부 정합성)

-equals(Object obj)의 매개변수인 obj의 값이 변경될 때에만 해시코드 값이 변경되어야 함

(2) equals consistency(동등 일치성)

-동등한 객체인 경우, 동일한 해시코드를 반환해야 함

(3) collisions(해시충돌)

-동등하지 않은 객체들(unequal objects)이어도 동일한 해시코드를 반환할 수 있음(이는 상수풀에 같은 값을 지닌 경우 동일한 해시코드를 갖는 경우와 확연히 구분되는, 다른 개념!)



**참고 : https://www.baeldung.com/java-equals-hashcode-contracts**
