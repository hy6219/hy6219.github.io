# 모범사례-Object Mapper

- 아래와 같이 Text 형태의 json과 Object 간 변환을 도와줌

**Text json ↔️ Object**

↔️ Controller에서 text json 요청이 들어오면 Object로 변환

↔️ Controller에서 Object 요청이 들어오면 text json으로 변환

지금까지는 ObjectMapper 객체를 명시하지 않고 사용해왔지만, Test 패키지에서 ObjectMapper 객체를 명시하여 사용해보자

먼저 main 패키지에 이름과 나이를 갖는 사용자 클래스를 만들고, 생성자와 toString만 명시해주자

```java
package com.objectmapperex.objectmapperex.dto;

public class User {
    private String name;
    private int    age;

    public User() {
    }

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

1. 이런 User 객체를 text로 변환하기 위해서는 **ObjectMapper 클래스의 writeValueAsString(객체)**를 이용해주자!

먼저 test패키지에서 테스트용으로 contextLoads 메서드 내부에 Object▶️text json 변환을 해보자

```java
package com.objectmapperex.objectmapperex;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.objectmapperex.objectmapperex.dto.User;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class ObjectMapperExApplicationTests {

    @Test
    void contextLoads() **throws JsonProcessingException** {
        System.out.println("---");
        //Text json->object
        //object->text json
        **ObjectMapper mapper = new ObjectMapper();

        //object->text
        User user = new User("steve",10);
        String text = mapper.writeValueAsString(user);**
				**System.out.println(text);**
        //text->object
    }

}
```

- 먼저 user객체를 생성하고
- writeValueAsString 메서드로 문자열로 변환해주자!(이때 JsonProcessingException이 발생한다)

그런데 이대로 실행하면

No serializer found for class com.objectmapperex.objectmapperex.dto.User and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS)
com.fasterxml.jackson.databind.exc.InvalidDefinitionException: No serializer found for class com.objectmapperex.objectmapperex.dto.User and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS)
n.databind.SerializerProvider.reportBadDefinition(SerializerProvider.java:1276)

위와 같은 경고가 발생한다

그 이유는 

- ObjectMapper는 getter 를 참조하기 때문이다!

따라서 getter 메서드를 dto 객체에서 추가해주자

```java
package com.objectmapperex.objectmapperex.dto;

public class User {
    private String name;
    private int    age;

    public User() {
    }

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

그리고 나서 다시 재시작해보면

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/object%20to%20text%20json.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/object%20to%20text%20json.PNG?raw=true)

위와 같이 json 형태로 객체를 확인해볼 수 있다!(위에서는 setter도 같이 만들어줬는데, getter만 준비해줘도 확인해볼 수 있다)

1. 반대로 **From text json To Object** 는 어떨까?

🌟 **ObjectMapper클래스의 readValue 메서드**를 이용하자!

🌟 readValue의 인자로는

- text json(String)
- 클래스

가 필요하다!

readValue도 writeValueAsString처럼 JsonProcessingException을 발생시킨다

```java
//text->object
        //json text, class
        User objUser = mapper.readValue(text,User.class);
        System.out.println(objUser);
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/text%20json%20to%20object.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/text%20json%20to%20object.PNG?raw=true)

그러면 위와 같이 자바에서 객체에 대한 toString형식으로 **객체**를 확인해볼 수 있다!

🌟 그리고 위에서 처음부터 기본 생성자를 사용했었는데, **ObjectMapper(readValue를 사용하고자 할 때)**는 기본 생성자가 꼭! 있어야 한다!

🌟 주의할 점은, ObjectMapper의 대상이 될 클래스의 경우, 필드에 대한 getter 이외에는 데이터를 처리하고자 할 메서드의 첫 도입부가 get~이어서는 안된다![Infinite recursion(StackOverFlowError) 등의 예외가 발생할 수 있기 때문!]

이는 즉, 메서드 명을 get~로 하지 않고 아래와 같이 네이밍해줌이 바람직함을 의미한다!

```java
public User **defaultUser()**{
        return new User("default",0,"010-1111-2222");
}
```

그리고 Test 패키지 내부에서 SnakeCaseStrategy를 사용하게 되면 "deprecated API" 라고 하면서 사용할 수 없음을 내비치는 에러를 보내준다! 이때에는 적절히 JsonProperty 어노테이션을 이용하자!

⏩ 위에서 학습한 text json과 Object 간 변환은 컨트롤러 밖에서 변환이 필요한 경우 등에 유용하다!