# Lombok

## I. lombok for `getter` and `setter`

🌟 앞으로 등장하게 될 "엔티티"는

`필드` 와 `getter/setter` 의 조합으로 구성된다!

## 01. 롬복을 사용하지 않고 getter와 setter를 만드는 방법

- 이전에 수행했던 것과 동일하게 alt+insert 등을 통해서 생성

```java
package com.fastcampus.jpa.jpa_setting.domain;

import java.time.LocalDateTime;

public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public LocalDateTime getCreatedAt() {
        return createdAt;
    }

    public void setCreatedAt(LocalDateTime createdAt) {
        this.createdAt = createdAt;
    }

    public LocalDateTime getUpdatedAt() {
        return updatedAt;
    }

    public void setUpdatedAt(LocalDateTime updatedAt) {
        this.updatedAt = updatedAt;
    }
}
```

## 02. 롬복을 사용해서 getter /setter 생성해주기

### 02-1. `@Getter` & `@Setter`

lombok을 도입하면 수많은 데이터 관련 객체들에 대해서 getter/setter 메서드를 직접 입력하거나 위의 방식으로 생성시켜주지 않아도 된다!(컴파일 단계에서 적용됨)

`클래스`에 대해서`@Getter` 어노테이션과 `@Setter` 어노테이션으로 모든 필드에 대해서 getter와 setter를 만들어줄 수 있다

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.Getter;
import lombok.Setter;

import java.time.LocalDateTime;

**@Getter
@Setter**
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

}
```

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/%EB%A1%AC%EB%B3%B5%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20getter,setter%EB%A7%8C%EB%93%A4%EA%B8%B0-@getter,@setter.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/%EB%A1%AC%EB%B3%B5%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20getter,setter%EB%A7%8C%EB%93%A4%EA%B8%B0-@getter,@setter.PNG?raw=true)

하지만 `필드`에 대해서 적용해주면 `해당 필드에 대해서만 getter와 setter`를 만들어줄 수 있다!

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.Getter;
import lombok.Setter;

import java.time.LocalDateTime;

public class User {
    private String name;
    **@Getter
    @Setter**
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

}
```

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/%EB%A1%AC%EB%B3%B5%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20getter,setter%EB%A7%8C%EB%93%A4%EA%B8%B0-@getter,@setter-%ED%95%84%EB%93%9C%EC%97%90%20%EB%8C%80%ED%95%B4%EC%84%9C%EB%A7%8C%20%EC%A0%81%EC%9A%A9.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/%EB%A1%AC%EB%B3%B5%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20getter,setter%EB%A7%8C%EB%93%A4%EA%B8%B0-@getter,@setter-%ED%95%84%EB%93%9C%EC%97%90%20%EB%8C%80%ED%95%B4%EC%84%9C%EB%A7%8C%20%EC%A0%81%EC%9A%A9.PNG?raw=true)

아래의 그림은 DLombok을 통해서 적용되는 getter, setter 메서드를 확인해본 것이다

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/DLombok%EC%9D%84%20%ED%86%B5%ED%95%B4%20%EC%82%B4%ED%8E%B4%EB%B3%B8%20getter,%20setter.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/DLombok%EC%9D%84%20%ED%86%B5%ED%95%B4%20%EC%82%B4%ED%8E%B4%EB%B3%B8%20getter,%20setter.gif?raw=true)

하지만, 보통 모든 필드에 대해서 getter, setter를 이용하기 때문에, 클래스에 대해서 정의해주는 것이 바람직하다(모든 필드마다 붙여주면 가독성도 떨어진다)

이렇게 롬복을 이용하면 코드가 보다 간결해진다!

jpa에서 사용하는 엔티티는 컬럼들이 모두 필드로 표현되는데, 이러한 컬럼 수가 많아질 때 이러한 롬복이 빛나게 될 것이다!

---

## II. lombok for `toString`

## 01. 롬복을 사용하지 않고 직접 작성

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.Getter;
import lombok.Setter;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Getter
@Setter
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

    @Override
    *public String toString()* {
        return "User{" +
                "name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", createdAt=" + createdAt +
                ", updatedAt=" + updatedAt +
                '}';
    }
}
```

지금까지는 toString을 위와 같이 직접 오버라이딩하여 적어주거나 생성해주었을 것이다

이 방식을 이용하면 필드가 추가 혹은 변경될 때마다 계속 수정해주어야 한다

앞으로 롬복을 적용하면 어떻게 될지 확인해보도록 하자

## 02. `@ToString` 을 이용해서 toString 메서드 오버라이딩해주기(Lombok)

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Getter
@Setter
**@ToString**
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

}
```

위와 같이 ToString 애너테이션을 이용하면 위에서처럼 동일하게 toString을 오버라이딩한 것과 같은 효과를 낼 수 있다

---

## III. lombok for `Constructor` (`생성자를 위한 롬복`)

JPA를 사용하게 되면 ,엔티티를 생성해야 하는 경우가 많다

그래서 생성자를 많이 사용하게 된다

## 1. `@NoArgsConstructor`

기본 생성자를 생성

- JPA에서는 기본 생성자가 필요하기 때문에 항상 명시해주는 것이 좋음
- NonNull이 붙고 RequiredArgsConstructor를 붙여주게되는 경우, 객체 생성시 다른 필드에 대한 접근을 위해서 필요

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import lombok.ToString;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Getter
@Setter
@ToString
**@NoArgsConstructor**
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/NoArgsConstructor.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/NoArgsConstructor.gif?raw=true)

## 2. `@**AllArgsConstructor**`

모든 필드를 파라미터로 갖는 생성자 생성

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Getter
@Setter
@ToString
@NoArgsConstructor
**@AllArgsConstructor**
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

## 3. `@RequiredArgsConstructor`

꼭 필요한 경우에 대해서 생성자를 생성

 🌟`@Data` 에서 기본적으로 제공되는 것이 바로 `@RequiredArgsConstructor`

🌟 `@NonNull` 이나 `final`이 붙는 경우가 아니면 기본 생성자(`@NoArgsConstructor`)처럼 동작!

### case 1) final이 붙은 생성자

- 기본 생성자와 동일

```java
package com.fastcampus.jpa.jpa_setting.controller;

import com.fastcampus.jpa.jpa_setting.domain.User;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/helloApi")
@RequiredArgsConstructor
public class HelloWorldController {

    private final User user;

    @GetMapping("/hello")
    public String helloWorld(){
        return "helloWorld";
    }

}
```

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/RequiredArgsConstructor.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/RequiredArgsConstructor.gif?raw=true)

### case 2) 일반 클래스의 필드 중 생성자에 대해서 반드시 필요한 경우가 아닌 경우 ▶️ 기본 생성자 NoArgsConstructor처럼 동작

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Getter
@Setter
@ToString
@NoArgsConstructor
@AllArgsConstructor
**@RequiredArgsConstructor**
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

↔️(DLombok 이용)

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Getter
@Setter
@ToString
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

    public User(String name, String email, LocalDateTime createdAt, LocalDateTime updatedAt) {
        this.name = name;
        this.email = email;
        this.createdAt = createdAt;
        this.updatedAt = updatedAt;
    }

    public User() {
    }
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

### case 3) `@NonNull`과 같은 롬복(NotNull이 아닌 NonNull(lombok에서 제공))이 붙은 경우

NonNull 필드에 대한 생성자 생성

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Getter
@Setter
@ToString
@NoArgsConstructor
@AllArgsConstructor
**@RequiredArgsConstructor**
public class User {
    private String name;
    **@NonNull**
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

↔️(DLombok 이용)

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.Getter;
import lombok.NonNull;
import lombok.Setter;
import lombok.ToString;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Getter
@Setter
@ToString
public class User {
    private String name;
    @NonNull
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

    public User(String name, @NonNull String email, LocalDateTime createdAt, LocalDateTime updatedAt) {
        this.name = name;
        this.email = email;
        this.createdAt = createdAt;
        this.updatedAt = updatedAt;
    }

    public User() {
    }

    **public User(@NonNull String email) {
        this.email = email;
    }**
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

---

## IV. lombok for `equals` and `hashCode`

JPA에서 딱히 이에 대해서 사용할 일은 크게 없지만, `객체 동등성 및 동일성 비교`를 위해서 && `@Data와 관련`되어 있는 부분 이기 때문에 살펴보자

➡️ `@EqualsAndHashCode` 이용

 

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Getter
@Setter
@ToString
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
**@EqualsAndHashCode**
public class User {
    private String name;
    @NonNull
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

![https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/EqualsAndHashCode.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/JPA/Lombok/EqualsAndHashCode.gif?raw=true)

---

## V. lombok `@Data`

- 모든 필드에 NonNull이 붙지 않았다면 기본 생성자처럼, NonNull이 붙은 생성자가 단 하나라도 있다면 그 필드에 대한 생성자만 존재

⏩`@RequiredArgsConstructor` 처럼

- `@Setter`
- `@Getter`
- `@ToString`
- `@EqualsAndHashCode`

위에 대한 롬복들을 모두 함축!

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
**@Data**
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

---

## VI. lombok `@Builder`

해당 클래스에 대한 객체 생성을 `builder()`로 진행할 수 있도록 하여 마치 setter와 생성자를 함축한 것 같은 효과를 볼 수 있음

```java
package com.fastcampus.jpa.jpa_setting.domain;

import lombok.*;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;

@Component
@Data
**@Builder**
public class User {
    private String name;
    private String email;
    //생성된 시간과 수정된 시간은 jpa 도메인에 항상 포함되도록 되어 있음
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
//클래스이름위 커서+alt+shift : 테스트 생성

}
```

```java
package com.fastcampus.jpa.jpa_setting.domain;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;

public class UserTest {

    @Autowired
    private User user;

    @Test
    public void userTest(){
//        user.setName("steve");
//        user.setEmail("123");
        **User myUser=User.builder().name("steve").email("123").build();**
        System.out.println(myUser);//User(name=steve, email=123, createdAt=null, updatedAt=null)
    }

}
```