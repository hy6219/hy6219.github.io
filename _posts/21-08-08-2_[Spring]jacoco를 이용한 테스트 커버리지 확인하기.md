# 테스트 커버리지 확인하기

📌 Jacoco 📌

- 자바 코드의 코드 커버리지를 체크하는 라이브러리
- 결과를 html, xml, csv로 확인 가능

📌 코드 커버리지

- 소프트웨어의 `테스트케이스가 얼마나 충족되었는지를 나타내는 지표 중 하나`
- 테스트를 진행했을 때 `코드 자체가 얼마나 실행되었냐는 것을 의미`
- 코드가 커버하는 범위로써의 의미
- 예) dto 객체들 중 일부 객체 혹은 메서드가 실행되지 않은 경우 붉은색과 함께 표시될 수 있음

[코드 커버리지(Code Coverage)가 뭔가요?](https://woowacourse.github.io/tecoble/post/2020-10-24-code-coverage/)

build.gradle의 plugins 부분에 `id: 'jacoco'` 를 추가해주자

```java
plugins {
    id 'org.springframework.boot' version '2.5.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
    **id 'jacoco'**
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
    useJUnitPlatform()
}
```

이렇게 id:jacoco 를 추가해주면 gradle의 task에서 jacoco를 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/test%20coverage-jacoco.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/test%20coverage-jacoco.PNG?raw=true)

🌟 jacoco report가 나오기 위해서는 test가 반드시 실행되어야 함!

◀️ 더블클릭하면 실행됨

이렇게 test를 더블클릭해서 실행하면 build 폴더에 reports 폴더에 index.html 문서가 보이는데 , 이를 통해 커버리지 확인이 가능하다

![https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/jacoco%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%ED%99%95%EC%9D%B8.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JUnit5/jacoco%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%ED%99%95%EC%9D%B8.gif?raw=true)