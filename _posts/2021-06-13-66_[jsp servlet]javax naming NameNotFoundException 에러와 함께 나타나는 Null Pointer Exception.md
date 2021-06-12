# javax.naming.NameNotFoundException 에러와 함께 java.lang.NullPointerException이 뜨는 경우!

(1) 방법 1

이 경우에는 server.xml에서 데이터베이스 커넥션풀을 위해 설정했던 resource 태그의 auth 옵션과 type 옵션, name 옵션에 맞추어

<resource-ref>
		<description>DB Connection</description>
		<res-ref-name>jdbc/myDb</res-ref-name>
		<res-type>javax.sql.DataSource</res-type>
		<res-auth>Container</res-auth>
</resource-ref>

와 같은 부분을 web.xml의 welcome-file-list 밖에 기입해주어야 한다!

참고: [https://jinsiri.tistory.com/144](https://jinsiri.tistory.com/144)

(2) 방법 2

server.xml에서

<Context docBase="ManageEmployee" path="/ManageEmployee" reloadable="true" source="org.eclipse.jst.jee.server:ManageEmployee">
					<Resource auth="Container" 
					driverClassName="oracle.jdbc.OracleDriver" 
					maxIdle="10" maxTotal="20" maxWaitMillis="-1" 
					name="jdbc/myoracle" password="~~" 
					url="jdbc:oracle:thin:@127.0.0.1:1521:XE" 
					type="javax.sql.DataSource"
					username="~~"/>	
				</Context>

와 같이 연결, 기입해주었는지 확인해보자! type때문에  null pointer exception이 근본원인이 되어 

java.lang.NullPointerException at org.apache.naming.factory.ResourceFactory.getDefaultFactory(ResourceFactory.java:45)

와 같은 에러가 Null Pointer Exception at~~의 하단에 나타날 수도 있다!

참고:

[http://tomcat.apache.org/tomcat-7.0-doc/config/systemprops.html#Other](http://tomcat.apache.org/tomcat-7.0-doc/config/systemprops.html#Other)