# 스프링 maven 설정 중 나타나는 "Bean named 'proxy' is expected to be of type 'com.test06.MessageBeanImpl' but was actually of type 'com.sun.proxy.$Proxy4'" 에러

AOP를 메이븐 기반 프록시 설정을 해주다가, 직면하게된 에러인데, 구현한 인터페이스가 있는 클래스에서 프록시 설정을 하게 되면 나타나는 에러다!

즉, 아래의 상황에서 발생할 수 있다

- MessageBean 인터페이스를 구현한 MessageBeanImpl 클래스가 존재하고
- bean configuration에서 아래와 같이 프록시를 설정해준 경우

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- target 지정 -->
	<bean id="msg" class="com.test06.MessageBeanImpl">
		<property name="name" value="홍길동"/>
	</bean>
	<!-- advice 설정 -->
	<bean id="LoggingAdvice" class="com.test06.LoggingAdvice"/>
	<!-- advisor 설정=advice+pointcut -->
	<bean id="advisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
		<!-- advice -->
		<property name="advice" ref="LoggingAdvice"/>
		<!-- pointcut -->
		<property name="pointcut">
			<bean class="org.springframework.aop.support.JdkRegexpMethodPointcut">
				<property name="pattern">
					<value>.*sayHello.*</value>
				</property>
			</bean>
		</property>
	</bean>
	<!-- proxy 설정 -->
	<bean id="proxy" class="org.springframework.aop.framework.ProxyFactoryBean">
		**<property name="target" ref="msg"/>**
		<property name="interceptorNames">
			<list>
				<value>advisor</value>
			</list>
		</property>
	</bean>
</beans>
```

```java
package com.test06;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		ApplicationContext context=
				new ClassPathXmlApplicationContext("com/test06/applicationContext.xml");
		
		MessageBeanImpl m=
				context.getBean("proxy",MessageBeanImpl.class);
		
		m.sayHello();
	}

}
```

이를 이렇게 메인 메서드 부분을 수정해주면 해결 가능하다!

```java
package com.test06;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		ApplicationContext context=
				new ClassPathXmlApplicationContext("com/test06/applicationContext.xml");
		
		**MessageBean m=
				context.getBean("proxy",MessageBean.class);**
		
		m.sayHello();
	}

}
```