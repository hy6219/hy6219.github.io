# 그레이들 설치

- Maven: 동적 행위에 제약, 상속구조
- Gradle: 그루비 기반, 설정 주입, 보다 유연한 빌드 환경 구성 가능

[Gradle | Installation](https://gradle.org/install/)

위 링크에 접속하자!

🌟 그레이들 설정 관련 기본 구조 🌟 ▶️ 프로젝트 첫 설정 시 자동으로 그레이들 관련 빌드 설정 `a`

ㅣ—gradle

ㅣ ㅣ_wrapper

ㅣ        l_gradle-wrapper.jar

ㅣ       ㅣ_gradle-wrapper.properties

ㅣ—gradlew

ㅣ—gradlew.bat

- gradlew: 리눅스 및 맥 OS용 쉘 스크립트
- gradlew.bat: 윈도우용 배치 스크립트
- gradle/wrapper/gradle-wrapper.jar: Wrapper JAR
- gradle/wrapper/gradle-wrapper.properties: 그레이들 설정 정보 프로퍼티 파일(버전 정보 등)

위의 사이트의 전제조건을 확인해보니 자바 버전이 1.8.0_121 이상이어야 한다

      

```java
openjdk version "1.8.0_282"
OpenJDK Runtime Environment Corretto-8.282.08.1 (build 1.8.0_282-b08)
OpenJDK 64-Bit Server VM Corretto-8.282.08.1 (build 25.282-b08, mixed mode)
```

나는 해당 조건을 만족하는 것을 확인했으므로 아래에서 설치해주자

[Gradle | Releases](https://gradle.org/releases/)

압축을 풀어주고, 시스템 환경변수-사용자변수에 "GRADLE_HOME"를 gradle-7.1.1(내가 선택한 버전)의 위치로 잡아주자

그리고 시스템변수- Path값을 %GRADLE_HOME%\bin으로 추가해주자

그리고 아래처럼 gradle -v로 그레이들 설치 및 버전을 확인해주자

gradle -v

```java
C:\WINDOWS\system32>gradle -v

------------------------------------------------------------
Gradle 7.1.1
------------------------------------------------------------

Build time:   2021-07-02 12:16:43 UTC
Revision:     774525a055494e0ece39f522ac7ad17498ce032c

Kotlin:       1.4.31
Groovy:       3.0.7
Ant:          Apache Ant(TM) version 1.10.9 compiled on September 27 2020
JVM:          1.8.0_282 (Amazon.com Inc. 25.282-b08)
OS:           Windows 10 10.0 amd64
```

---

스프링 이니셜라이저를 이용하면 `gradle 선택시 기본적으로 자동설치해준다`

### WHY??

▶️ `a`와 같은 그레이들 빌드 설정을 프로젝트 첫 설정 시 자동으로 실행하게 되면, `그레이들 래퍼를 설치하여 깃과 같은 버전관리시스템(VCS; Version Control System)에서 관리하면 공동 작업자들이 그레이들 설치 및 버전 관리를 보다 편리하게 할 수 있기 때문`

🌟 gradle-wrapper.properties에서 distributionUrl을 원하는 그레이들 버전으로 수정가능하다!

각 운영체제에 설치 후 프로젝트를 새로 만들면 이제 프로젝트의 .gradle 폴더에 해당 버전을 확인해볼 수 있다!

![https://github.com/hy6219/TIL/blob/main/Spring/gradle/gradle%20%EB%B9%8C%EB%93%9C%20%EC%84%A4%EC%A0%95%2001.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/gradle/gradle%20%EB%B9%8C%EB%93%9C%20%EC%84%A4%EC%A0%95%2001.PNG?raw=true)

![https://github.com/hy6219/TIL/blob/main/Spring/gradle/gradle%20%EB%B9%8C%EB%93%9C%20%EC%84%A4%EC%A0%95%2002.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/gradle/gradle%20%EB%B9%8C%EB%93%9C%20%EC%84%A4%EC%A0%95%2002.PNG?raw=true)

위와 같이 프로젝트 내에서 빌드 설정 파일들을 확인해볼 수 있다