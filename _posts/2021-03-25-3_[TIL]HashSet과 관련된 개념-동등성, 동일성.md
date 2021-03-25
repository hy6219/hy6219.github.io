# hashCode() 오버라이딩 시 준수해야 할 사항

이전에 동등성과 동일성에 대해서는 다룬 적 있으니, 해당 개념은 스킵한다

컬렉션 프레임워크에서, hashSet의 경우 아래와 같은 경우가 존재한다
Person.class
~~~java
public class Person {
	String name;
	int age;
	public Person(String name, int age) {

		this.name = name;
		this.age = age;
	}
	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + "]";
	}
	
	
}
~~~

그리고 이를 이용해서 HashSet에 추가해보자

~~~java

HashSet h = new HashSet();
		
h.add("abc");
h.add("abc");
h.add(new Person("David",10));
h.add(new Person("David",10));
System.out.println("set: "+h);
~~~
결과를 출력해보기 전에 추측한다면, HashSet은 중복을 허용하지 않기 때문에 [abc, Person[name=David, age=10]] 이 출력될 것이라고 생각할 것이다

하지만!! 지금 중요한 것은, 컴퓨터가, 컴파일러가, 그들의 입장에서는, '어? 음.. 두 객체가 주소가 다르네.. 다른 객체인 것 같네..!' 라고 생각할 수 있게 만든다!

그러면, 같은 값을 가진 객체가 중복되지 않게 하려면?

동일한 객체로 인식할 수 있도록, hashCode메서드를 오버라이딩해주면 된다!

hashCode를 오버라이딩하면, equals 메서드도 적절히 오버라이딩해주는 것이 좋을 것이다(equals값이 true이면 hashCode도 같아야 하지만, 반대의 경우는 필수까지는 아니다!)
★하지만, 컬렉션의 검색성능 향상을 위해서는 equals가 false를 반환하면 hashCode값이 다른 것이 좋기 때문에 권장된다!

따라서 새로운 클래스 Person2.class를 아래와 같이 작성하고, HashSet에 등록해보자
Person2.class
~~~java
public class Person2 {
	String name;
	int age;
	public Person2(String name, int age) {

		this.name = name;
		this.age = age;
	}
	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + "]";
	}
	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + age;
		result = prime * result + ((name == null) ? 0 : name.hashCode());
		return result;
	}
	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Person2 other = (Person2) obj;
		if (age != other.age)
			return false;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		return true;
	}
	
	

~~~
이제 HashSet 이 어떻게 되는지 보자

~~~java
HashSet h = new HashSet();
		
h.add("abc");
h.add("abc");
h.add(new Person2("David",10));
h.add(new Person2("David",10));
System.out.println("set: "+h);

~~~

콘솔에 아름답게 "set: [abc, Person [name=David, age=10]]" 이라고 출력됨을 확인해볼 수 있다
