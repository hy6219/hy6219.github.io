# Object Mapper

⚠️ 잠깐! 이 부분을 잠깐 보고 가시지오~

[모범사례-Object Mapper](https://www.notion.so/Object-Mapper-0233d0395f8e44079a4f0dd56ec1fff5) 

- 실무에서는 컨트롤러를 통해 validation을 하고, request를 받는 경우가 대부분이지만 json 내부 변경이나 값 확인을 위해 Object Mapper를 알아둘 필요가 있음

---

- Object Mapper는 스프링에서 사용하는 "자바 라이브러리"
- json을 위한 라이브러리 예시 - Gson, Object Mapper

먼저 인텔리제이에서 new project- gradle-java선택하여 gradle dependency를 가진 프로젝트를 만들자

이러한 dependency는 maven repository([https://mvnrepository.com/](https://mvnrepository.com/)) 에서 라이브러리와 관련된 gradle 설정을 가져와서 사용가능하다!

이번에는 이 중 object mapper를 이용해볼 것인데, 보통은 최신 버전을 이용하면 하위 버전에 대한 호환성이 떨어지기 때문에 이 점을 고려해야 한다!

우리는 스프링과 동일환경을 위해서 jackson databind(works on core라는 설명이 붙여져 있다)를 이용할 것이고, 2.12.1 버전으로 적절하게 사용할 것이다! 

해당 버전을 눌러주자!

해당 버튼을 누르면 아래와 같은 화면이 보일 것이다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/maven%20repository_gradle%20%EC%84%A4%EC%A0%95.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/maven%20repository_gradle%20%EC%84%A4%EC%A0%95.PNG?raw=true)

우리는 gradle을 이용하므로 해당 탭을 누르고, 해당 내용을 복사하자!

그 다음 build.gradle의 dependencies에 추가해주자!

그다음에는 공룡모양 새로고침을 해주자!

```java
plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    // https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind
    implementation group: 'com.fasterxml.jackson.core', name: 'jackson-databind', version: '2.12.1'
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0'
}

test {
    useJUnitPlatform()
}
```

이렇게 dependencies를 추가해주면, 라이브러리를 추가해줄 수가 있게 된다!

추가가 되었는지 확인하는 방법은 우측 gradle 메뉴에서 확인해볼 수 있다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4_dependency%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4_dependency%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.PNG?raw=true)

그리고 아래 그림처럼, 왼쪽 메뉴 중 External Libraries를 통해서도 확인해볼 수 있다!

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4_dependencies%20%EC%B6%94%EA%B0%80%ED%9B%84%20%EC%99%B8%EB%B6%80%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%84%A4%EC%A0%95%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%9C%EC%9D%B4_dependencies%20%EC%B6%94%EA%B0%80%ED%9B%84%20%EC%99%B8%EB%B6%80%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%84%A4%EC%A0%95%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.PNG?raw=true)

이제 Object Mapper를 gradle 프로젝트에서 사용할 수 있는 준비가 완료되었다! 이제 시작해보자!

먼저, 자동차를 소유한 사용자에 대한 데이터를 json 타입으로 다룰 것이다

이를 위한 샘플 형태는 아래와 같이 생각해보자(sample.json)

```java
{
  /*자동차를 소지한 사용자*/
  "name": "홍길동",
  "age": 10,
  "cars": [
    {
      //자동차 이름
      "name": "K5",
      "car_number":"11가 1111",
      //자동차 종류
      "TYPE":"세단"
    },
    {
      //자동차 이름
      "name": "Q5",
      "car_number":"11가 1112",
      //자동차 종류
      "TYPE":"SUV"
    }

  ]
}
```

이렇게 보면,  크게 사용될 수 있는 DTO(VO;Value Object) 객체는 사용자와 자동차에 대한 객체 2개로 나누어 볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/objectMapper%ED%99%9C%EC%9A%A9_sample%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A5%BC%20%EC%9C%84%ED%95%9C%20dto.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/objectMapper%ED%99%9C%EC%9A%A9_sample%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A5%BC%20%EC%9C%84%ED%95%9C%20dto.png?raw=true)

```java
package dto;

import java.util.List;

public class User {
    private String name;
    private int age;
    private List<Car> cars;

    public User() {
    }

    public User(String name, int age, List<Car> cars) {
        this.name = name;
        this.age = age;
        this.cars = cars;
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

    public List<Car> getCars() {
        return cars;
    }

    public void setCars(List<Car> cars) {
        this.cars = cars;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", cars=" + cars +
                '}';
    }
}
```

```java
package dto;

public class Car {
    private String name;
    private String carNumber;
    private String type;

    public Car() {
    }

    public Car(String name, String carNumber, String type) {
        this.name = name;
        this.carNumber = carNumber;
        this.type = type;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCarNumber() {
        return carNumber;
    }

    public void setCarNumber(String carNumber) {
        this.carNumber = carNumber;
    }

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

    @Override
    public String toString() {
        return "Car{" +
                "name='" + name + '\'' +
                ", carNumber='" + carNumber + '\'' +
                ", type='" + type + '\'' +
                '}';
    }
}
```

먼저 객체를 text json 으로 변경해보자

ObjectMapper의 writeValueAsString(객체)를 이용하면 된다!(기억을 상기시킬 수 있다!!!)

```java
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import dto.Car;
import dto.User;

import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) throws JsonProcessingException {
        System.out.println("main");

        ObjectMapper objectMapper = new ObjectMapper();

        //ObjectMapper에 사용될 객체
        User user = new User();
        user.setName("홍길동");
        user.setAge(10);

        Car car1 = new Car();
        car1.setName("K5");
        car1.setCarNumber("11가 1111");
        car1.setType("세단");

        Car car2 = new Car();
        car2.setName("Q5");
        car2.setCarNumber("11가 1112");
        car2.setType("SUV");

        List<Car> cars = new ArrayList<>();
        cars.add(car1);
        cars.add(car2);

        user.setCars(cars);

        String json = objectMapper.writeValueAsString(user);
        System.out.println("java Object to text json: "+json);

    }
}
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/object%20to%20text%20json.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/object%20to%20text%20json.PNG?raw=true)

그다음에는 반대로 text json을 객체로 변경해보자

이번에는 ObjectMapper의 readValue(text json(◀️String), 클래스)를 이용하면 object로 변경가능하다!

```java
      User object = objectMapper.readValue(json,User.class);
        System.out.println("text json to java object: "+object);
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/text%20json%20to%20java%20object.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/text%20json%20to%20java%20object.PNG?raw=true)

추가로, 자바 콘솔에서 길게 일렬로 출력된 json 데이터를 그 구조를 반듯하게 보고 싶으면 json validator를 이용해보자

"json validator" 라고 검색하면 결과가 많이 나온다! 이 중에서 상단에 나온 사이트를 이용하기로 한다 [https://jsonformatter.curiousconcept.com/](https://jsonformatter.curiousconcept.com/)

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/java%20object%20to%20text%20json-json%20validator.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/java%20object%20to%20text%20json-json%20validator.PNG?raw=true)

그러면 위와 같이 json 구조로 잘 잡힌 데이터[valid라고 표시되면 json 데이터로 유효함을 나타낸다]를 확인해볼 수 있다! (해당 사이트에서는 이 데이터를 json파일로 다운로드받을 수도 있다!

만약 포맷이 틀리면 "INVALID"라고 표시된다

조금 아쉬운 것은 json으로 변환되었을 때와 그 반대일 때 case 변환 및 적용이 아쉽다

JsonProperty("TYPE"을 위함!)를 붙여주도록 하자

```java
package dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import java.util.List;

public class User {
    private String name;
    private int age;
    private List<Car> cars;

    public User() {
    }

    public User(String name, int age, List<Car> cars) {
        this.name = name;
        this.age = age;
        this.cars = cars;
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

    public List<Car> getCars() {
        return cars;
    }

    public void setCars(List<Car> cars) {
        this.cars = cars;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", cars=" + cars +
                '}';
    }
}
```

```java
package dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

public class Car {
    private String name;
		**@JsonProperty("car_number")**
    private String carNumber;
	  **@JsonProperty("TYPE")**
    private String type;

    public Car() 
    }

    public Car(String name, String carNumber, String type) {
        this.name = name;
        this.carNumber = carNumber;
        this.type = type;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCarNumber() {
        return carNumber;
    }

    public void setCarNumber(String carNumber) {
        this.carNumber = carNumber;
    }

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

    @Override
    public String toString() {
        return "Car{" +
                "name='" + name + '\'' +
                ", carNumber='" + carNumber + '\'' +
                ", type='" + type + '\'' +
                '}';
    }
}
```

그러면 콘솔에서

java Object to text json: {"name":"홍길동","age":10,"cars":[{"name":"K5","car_number":"11가 1111","TYPE":"세단"},{"name":"Q5","car_number":"11가 1112","TYPE":"SUV"}]}

text json to java object: User{name='홍길동', age=10, cars=[Car{name='K5', carNumber='11가 1111', type='세단'}, Car{name='Q5', carNumber='11가 1112', type='SUV'}]}

snake case와 camel case로 아주 보기 좋게 표시되는 것을 확인해볼 수 있다

📌 잠깐! 인텔리제이 인코딩 설정

1. File 메뉴 - Settings - encoding 검색- File Encoding- Global Encoding& Project Encoding&Default encoding for properties 을 UTF-8로 변경 후 apply-ok
2. Help 메뉴 - Find Action - edit custom vm option -

제일 마지막에 

-Dfile.encoding=UTF-8

기입하기

1. 인텔리제이 재시작

▶️ 스프링은 기본적으로 UTF-8 인코딩이 설정되어 있는데, 자바에서는 UTF-8로 설정해줄 필요가 있음(하지만, 맥은 기본이 UTF-8)

만약, json 데이터를 자바에서 다루는 등 순수 자바를 이용해야 할 경우, UTF-8인코딩을 확인해줄 것! 그렇지 않으면 받는 쪽에서 확인이 잘 안되거나 데이터를 주고 받는데에 지장이 있을 수 있다!

---

이제는 단순 변환이 아닌, 노드에 접근해보자

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/ObjectMapper%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20json%20%EA%B0%9D%EC%B2%B4%20%EB%85%B8%EB%93%9C%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B0%20-%20%EB%85%B8%EB%93%9C%20overview.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/ObjectMapper%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20json%20%EA%B0%9D%EC%B2%B4%20%EB%85%B8%EB%93%9C%20%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B0%20-%20%EB%85%B8%EB%93%9C%20overview.png?raw=true)

위의 json 데이터는 지금 실습해본 json 데이터인데, "홍길동" 사용자에 대한 데이터로 끊어진 것이 바로 하나의 json 노드이다[다만, 더 구체적으로는 cars는 새로운 노드인 것!!]

그리고 그 내부에 "name":~ 등은 json 노드를 구성하는 값들이라고 할 수 있다

이를 gradle에서는 **JsonNode 클래스**와 **ObjectMapper 의 readTree(String json)**으로 접근할 수 있다!

```java
**JsonNode jsonNode = objectMapper.readTree(json);**
```

그리고 키값으로 value를 가져오려면 이 JsonNode 객체의 get메서드를 이용해서

JsonNode객체.get(키값)

으로 접근할 수 있다! 다만, 반환 타입이 JsonNode이기 때문에 적절하게 형변환해주거나 asText등의 메서드를 사용할 필요가 있다

```java
String name = **jsonNode.get("name").asText()**;
System.out.println("json node - name value: "+name);
```

위의 경우 , 콘솔에 "json node - name value: "홍길동" 이 출력되는 것을 확인해볼 수 있다!

asText 등의 메서드는 지금과 같이 특정 자료형임을 알고 있을 때 노드를 건들경우 사용 가능하다

그리고 List 타입에 대해서 변환하는 것은 다소 복잡할 수 있는데,

[https://stackoverflow.com/questions/11536051/converting-jsonnode-to-java-array/53648791](https://stackoverflow.com/questions/11536051/converting-jsonnode-to-java-array/53648791)

스택오버플로우의 도움을 받아서 아이디어를 내보았다

어차피 get의 반환형태는 JsonNode이기 때문에

- get으로 jsonNode를 반환받는다
- 그런데 우리는 arrayList로 받고 싶으니까, **ObjectMapper.convertValue(JsonNode 값, 클래스-우리는 ArrayList.class)**로 변환해주면 된다! 그런데 JsonNode의 모음이므로, **List<JsonNode>**로 받아주자!

```java
//json 노드 접근하기
        JsonNode jsonNode= objectMapper.readTree(json);
        //name 값
        String name = jsonNode.get("name").asText();

        //age값
        int age = jsonNode.get("age").asInt();
        //cars
        **List<JsonNode> myCar = objectMapper.convertValue(jsonNode.get("cars"),ArrayList.class);**
        System.out.println("json node- name: "+name+", age: "+age+", cars: "+myCar);
```

그러면 콘솔에서

json node- name: 홍길동, age: 10, cars: [{name=K5, car_number=11가 1111, TYPE=세단}, {name=Q5, car_number=11가 1112, TYPE=SUV}]

과 같이 출력되는 것을 확인해볼 수 있다!

또 다른 방법은 cars는 "ArrayNode"인데, 이를 이용해서

- cars를 JsonNode로 받고
- ArrayNode로 파싱해줄 것
- List<Car>로 받고 싶기 때문에 , ObjectMapper.converValue를 이용(변환)하되, 인자 중 위의 ArrayList가 아닌
- **TypeReferenece(List<Car>**(){}) 를 이용한다

```java
        JsonNode carsJ = jsonNode.get("cars");
        ArrayNode arrayNode=((ArrayNode) carsJ);
        **List<Car> myCar = objectMapper.convertValue(arrayNode, new TypeReference<List<Car>>() {
        });**

        System.out.println("json node- name: "+name+", age: "+age+", cars: "+myCar);
```

결과는 위와 동일하다

🌟 이렇게 하기 위해서는 미리 json 스펙을 알고 있어야 한다!!

---

그런데 JsonNode의 값은 어떻게 바꿀까? - **ObjectNode**를 이용하면 값을 바꿀 수 있다!(JsonNode에서는 막아두었다!)

1. 먼저 JsonNode에서 ObjectNode로 형변환해주자

```java
ObjectNode objectNode = (ObjectNode) jsonNode;
```

1. 그러면 put을 이용해서 값을 바꿔주자
- put과 set은 둘다 값을 설정한다는 취지에서 동일하다! 하지만, set은 두번째 인자로써 JsonNode 타입을 요구한다

```java
        objectNode.put("name","steve");
        objectNode.put("age",20);
        System.out.println("값 변경 using ObjectNode: "+objectNode);
        System.out.println("값 변경 using JsonNode: "+jsonNode);
```

그러면 ObjectNode 뿐 아니라 JsonNode에서도 값 자체가 변경됨을 확인해볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/ObjectNode%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20JsonNode%20%EA%B0%92%20%EB%B3%80%EA%B2%BD.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/ObjectNode%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20JsonNode%20%EA%B0%92%20%EB%B3%80%EA%B2%BD.PNG?raw=true)

그리고 ObjectNode의 toPrettyString을 이용하면 아래와 같이 json 구조로 알맞게 확인해볼 수 있다

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/ObjectNode%20toPrettyString.PNG?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/ObjectMapper/ObjectNode%20toPrettyString.PNG?raw=true)

이러한 방법들은 AOP(부가적인 공통 로직에 사용될 시에 결합이 가능할 것)나 필터 등에서 각각의 노드에서 사용될 수 있다!