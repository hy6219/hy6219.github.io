# Spring Boot Custom Validation

[Validation을 위한 어노테이션의 사본](https://www.notion.so/309cdca5cd4941749199ac5c8e59bea7)

- 어노테이션을 DTO객체에 붙여주면, 한 번 클래스에 대한 valdation 설정을 해주었다면 재사용성이 매우 좋음!(Valid 어노테이션만 이용하면 되므로!)

밸리데이션에서 *예외적으로 위의 어노테이션이 필요하지 않을 경우가 존재*한다!

실무에서는 날짜와 관련된 요청시, yyyy-MM-dd의 형태로 보낼 때가 해당!

▶️ **이 때 방법은 크게 두가지!**

1. **AssertTrue/False 어노테이션**으로 메서드 지정을 통해 Custom Logic 적용
2. **ConstraintValidator**를 적용하여 재사용 가능한 Custom Logic  적용

지난 시간에 학습에 사용했던 예제를 그대로 사용하기로 하자

🆙 yyyyMM 형태로 날짜 형태로 요청을 받는 것을 진행해보자!

- 해당 필드의 이름은 reqYearMonth(requestYearMonth)로 하고
- yyyyMM이므로 자릿수가 최소 6자리, 최대 6자리일 것
[▶️ @Size 어노테이션 이용]

그러면 아래와 같이 dto 부분을 수정해볼 수 있을 것이다

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import javax.validation.constraints.*;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    @NotBlank(message="이름에는 공백이 허용되지 않습니다")
    private String name;

    @Min(value=0, message="최소 0살 이상")
    @Max(value=100,message="최대 100살 이하")
    private int age;
    @Email
    private String email;
    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", message="잘못된 핸드폰 번호 양식입니다")
    private String phoneNumber;

    **@Size(min=6, max=6)**
    **private String reqYearMonth;//yyyyMM**

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public String getReqYearMonth() {
        return reqYearMonth;
    }

    public void setReqYearMonth(String reqYearMonth) {
        this.reqYearMonth = reqYearMonth;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", reqYearMonth='" + reqYearMonth + '\'' +
                '}';
    }
}
```

그런데 무조건 6글자이기만 하면 날짜필드로써 의미가 없지 않을까❓

▶️ validation을 위해서 dto 객체 내부에 해당 필드인 reqYearMonth에 대한 검증 메서드를 만들고, 그 위에 @AssertTrue 를 붙여주자!

🌟 이때, 검증을 true, false로 성공, 실패를 나누어서 판단하는데

boolean 타입의 메서드를 AssertTrue를 붙여서 판단할 수 있도록 하기 위해서는 Is~로 메서드 명을 설정해주어야 한다!

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import javax.validation.constraints.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    @NotBlank(message="이름에는 공백이 허용되지 않습니다")
    private String name;

    @Min(value=0, message="최소 0살 이상")
    @Max(value=100,message="최대 100살 이하")
    private int age;
    @Email
    private String email;
    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", message="잘못된 핸드폰 번호 양식입니다")
    private String phoneNumber;

    @Size(min=6, max=6)
    private String reqYearMonth;//yyyyMM

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public String getReqYearMonth() {
        return reqYearMonth;
    }

    public void setReqYearMonth(String reqYearMonth) {
        this.reqYearMonth = reqYearMonth;
    }

    @AssertTrue
    public boolean isReqYearMonthValidation(){
        //return true: 정상
        //return false: 정상x
        System.out.println("assert true");
        try{
            LocalDate localDate = LocalDate.parse(getReqYearMonth()+"01", DateTimeFormatter.ofPattern("yyyyMMdd"));
        }catch(Exception e){
            return false;
        }
        return true;

    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", reqYearMonth='" + reqYearMonth + '\'' +
                '}';
    }
}
```

먼저 지금은 DB가 없이 진행중이므로, 임의로 getReqYearMonth()+"01"로 임의로 테스트를 해주는 사전작업을 해주었다

그리고 DateTimeFormatter.ofPattern("날짜형식")을 통해서 어떤 패턴으로 날짜형식을 맞춰줄 것인지를 알려주어 검증의 기준을 세워주었다

만약 해당 부분이 try영역에서 통과되면 true를 , 아니라면 false를 거치게 될것이다! (try-catch를 이용해서 성공과 실패를 나누어 준것이다!)

🌟 LocalDate, LocalTime, LocalDateTime의 장점 🌟

기존 Calendar, Date 클래스의 문제점을 해결해줌(jdk 8~)

📌 Calendar, Date 클래스의 문제점 📌

- 불변객체가 아니기 때문에 외부에서 고의적으로 변경할 수 있어서 위험
- 상수 필드를 남용해서 사용
- 월 지정 부분이 까다로움
- 일관성 없는 요일 상수
- Date와 Calendar가 각각 역할을 분담함으로써 번거로움이 존재
- Date가 Calendar로 부터 정보를 받아와서 연월일 계산
- 기타 [java.util.Date](http://java.util.Date) 하위 클래스의 문제

[https://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html#getDayOfWeek--](https://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html#getDayOfWeek--)

그러면 우선, 동작만 확인한다면

요청을 아래와 같이 내리게 된다면

```java
{
  "name":"김길동",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"010-123-5678",
  "req_year_month":"111111"
}
```

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/AssertTrue_01_test.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/AssertTrue_01_test.PNG?raw=true)

응답에 필드는 dto에 validation메서드에 상응되도록 설정해주지는 않았지만, 위와 같이 

"req_year_month_validation"으로 표시되면서 true라고 표시된다

이번에는 "a11111"로 req_year_month를 보내보자

```java
{
  "name":"김길동",
  "age":20,
  "email":"steve@gmail.com",
  "phone_number":"010-123-5678",
  "req_year_month":"a11111"
}
```

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/AssertTrue_01_badRequest_wrongRequest.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/AssertTrue_01_badRequest_wrongRequest.PNG?raw=true)

그러면 "**reqYearMonthValidation**"이라는 필드에서 오류가 발생했음을 확인해볼 수 있다

그런데 메시지가 사용자에게 제공되기에도 조금 부족한 면이 있다

사용자는 "무엇이 true라는 거지?"라고 생각할 수 있다

pattern이나 다른 검증 관련 어노테이션에서 message 속성을 관리할 수 있었듯, AssertTrue에서드 해당 속성을 이용해서 메세지 값을 바꿔줄 수 있다.

보다 직관적으로 메시지를 "잘못된 날짜 양식입니다. yyyyMM형태로 입력부탁드립니다."로 설정해주자

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import javax.validation.constraints.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    @NotBlank(message="이름에는 공백이 허용되지 않습니다")
    private String name;

    @Min(value=0, message="최소 0살 이상")
    @Max(value=100,message="최대 100살 이하")
    private int age;
    @Email
    private String email;
    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", message="잘못된 핸드폰 번호 양식입니다")
    private String phoneNumber;

    @Size(min=6, max=6)
    private String reqYearMonth;//yyyyMM

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public String getReqYearMonth() {
        return reqYearMonth;
    }

    public void setReqYearMonth(String reqYearMonth) {
        this.reqYearMonth = reqYearMonth;
    }

    @AssertTrue(message="**잘못된 날짜 양식입니다. yyyyMM형태로 입력부탁드립니다.**")
    public boolean isReqYearMonthValidation(){
        //return true: 정상
        //return false: 정상x
        System.out.println("assert true");
        try{
            LocalDate localDate = LocalDate.parse(getReqYearMonth()+"01", DateTimeFormatter.ofPattern("yyyyMMdd"));
        }catch(Exception e){
            return false;
        }
        return true;

    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", reqYearMonth='" + reqYearMonth + '\'' +
                '}';
    }
}
```

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/AssertTrue_01_assertTrue_with_message.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/AssertTrue_01_assertTrue_with_message.PNG?raw=true)

요청은 위에서와 동일하게 Bad Request를 불러일으킬 수 있도록 작성하게 되면, 이번에는 메시지를 default Message가 아닌 수정된 메시지가 확인된다

그런데 여기서 이런 생각을 해볼 수도 있다!

굳이 Max, Min, Email 이런 밸리데이션을 위한 어노테이션을 붙이지 않고,

AssertTrue만 붙여서 해결하는 것을 생각해볼 수 있다‼️

그런데 그렇게 된다면,  재사용이 불가능하기 때문에, 제공되는 어노테이션을 사용하는 편이 훨씬 좋다

(다른 dto 객체에서 비슷한 패턴을 사용하고 싶을 때 다시 사용할 수 없어서, 새로이 매번 만들어줘야 한다)

이를 제거하는 방법은 **해당 Validation에 대한 어노테이션을 만들어 주는 것**이다! ☺️

먼저 어노테이션을 만들기 위해서 기존 Email 어노테이션을 확인해보자

```java
/*
 * Jakarta Bean Validation API
 *
 * License: Apache License, Version 2.0
 * See the license.txt file in the root directory or <http://www.apache.org/licenses/LICENSE-2.0>.
 */
package javax.validation.constraints;

import static java.lang.annotation.ElementType.ANNOTATION_TYPE;
import static java.lang.annotation.ElementType.CONSTRUCTOR;
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.PARAMETER;
import static java.lang.annotation.ElementType.TYPE_USE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

import java.lang.annotation.Documented;
import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import javax.validation.Constraint;
import javax.validation.Payload;
import javax.validation.constraints.Email.List;

/**
 * The string has to be a well-formed email address. Exact semantics of what makes up a valid
 * email address are left to Jakarta Bean Validation providers. Accepts {@code CharSequence}.
 * <p>
 * {@code null} elements are considered valid.
 *
 * @author Emmanuel Bernard
 * @author Hardy Ferentschik
 *
 * @since 2.0
 */
@Documented
@Constraint(validatedBy = { })
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
@Repeatable(List.class)
public @interface Email {

	String message() default "{javax.validation.constraints.Email.message}";

	Class<?>[] groups() default { };

	Class<? extends Payload>[] payload() default { };

	/**
	 * @return an additional regular expression the annotated element must match. The default
	 * is any string ('.*')
	 */
	String regexp() default ".*";

	/**
	 * @return used in combination with {@link #regexp()} in order to specify a regular
	 * expression option
	 */
	Pattern.Flag[] flags() default { };

	/**
	 * Defines several {@code @Email} constraints on the same element.
	 *
	 * @see Email
	 */
	@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
	@Retention(RUNTIME)
	@Documented
	public @interface List {
		Email[] value();
	}
}
```

여기에서 우리는 Target, Constraint, Retention만 사용하도록 하자

---

📌 어노테이션 📌

- 프로그램의 소스코드 안에 다른 프로그램을 위한 정보를 미리 약속된 형식으로 포함시킨 것
- 상속 불가

가. Meta Annotation

- Annotation 선언 시 사용

(1) @Target - 어노테이션 적용 대상 선언 시 사용

[어노테이션 적용 대상](https://www.notion.so/2faa862f6dab4e5db5fe3a5e6c6d1d3a)

(2) @Retention - 어노테이션이 유지되는 기간을 지정 시 사용

[어노테이션의 유지 정책(Retention Policy)의 종류](https://www.notion.so/e37b247bfa8a4630956fd0195e2334c2)

(3) @Documented - 어노테이션에 대한 정보가 javadoc으로 작성한 문서에 포함되도록 함

(4) @Inherited - 어노테이션이 자손 클래스까지 상속되도록 함

(5) @Repeatable - 어노테이션을 여러번 붙일 수 있도록 함

---

"@Constraint(validatedBy = { })" 어노테이션

- validatedBy 값으로 validator를 전달해주면 이를 기반으로 api 호출 시 전달한 값을 가져오면서 validation 수행

groups()

- 밸리데이션 그룹핑
- 제약 조건 집합을 지정

[https://docs.jboss.org/hibernate/validator/4.1/reference/en-US/html/validator-usingvalidator.html#validator-usingvalidator-validationgroups](https://docs.jboss.org/hibernate/validator/4.1/reference/en-US/html/validator-usingvalidator.html#validator-usingvalidator-validationgroups)

message()

- validation이 실패할 경우 제공되는 메시지(따로 설정하지 않은 경우 default 메시지 제공)

[https://docs.jboss.org/hibernate/validator/5.3/reference/en-US/html/ch11.html#section-advanced-constraint-composition](https://docs.jboss.org/hibernate/validator/5.3/reference/en-US/html/ch11.html#section-advanced-constraint-composition)

우선 Email 어노테이션을 참고해서 아래와 같이 만들어보자

```java
package com.validation.springvalidation.annotation;

import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.ElementType.TYPE_USE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

@Constraint(validatedBy = { })
//메서드, 생성자, 파라미터, 사용중인 곳 그 어디서라도, 필드, 어노테이션 유형 선언 시에 어노테이션이 적용됨
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
public @interface YearMonth {
    String message() default "{javax.validation.constraints.Email.message}";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    String pattern() default "yyyyMM";
}
```

📌 Payload 페이로드

- 전송되는 데이터 자체

```json
{
    "status":"OK",
    "data": {
        📌**"message":"Hello, world!"**📌
    }
}
```

- 메시지 프로토콜(나중에 정리할 예정!) 중 프로토콜 오버헤드와 구별하기 위해서 사용됨

📌 오버헤드

- 어떤 처리를 하기 위해 소요되는 간접적인 시간 및 메모리 등을 의미

[https://en.wikipedia.org/wiki/Overhead_(computing)](https://en.wikipedia.org/wiki/Overhead_(computing))

[https://docs.jboss.org/hibernate/validator/5.3/reference/en-US/html/ch11.html#section-advanced-constraint-composition](https://docs.jboss.org/hibernate/validator/5.3/reference/en-US/html/ch11.html#section-advanced-constraint-composition)

위에서 pattern()메서드는 validator와 연동해서 해당 형태에 대한 검증을 처리하기 위해서 만든 메서드다 

그러면 ConstraintValidator를 만드는데, 이는 YearMonth 어노테이션과 String을 이용하는 것으로 정의하자

그리고

- pattern이라는 필드를 마련
- pattern 필드는 initialize 부분에서 YearMonth의 pattern()으로 가져와지는 값으로 초기화

@YearMonth(pattern=~)

에서 pattern의 값을 가져와서 초기화

- 제네릭스 중 String은 검사할 값이 들어오는데, 이를 이용해서 isValid 부분에서 검증할 수 있도록 정의하자
- 검증 방식은 위에서 진행했던 LocalDate~ 와 같은 방식으로 진행하자

```java
package com.validation.springvalidation.validator;

import com.validation.springvalidation.annotation.YearMonth;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;
import java.lang.annotation.Annotation;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class YearMonthValidator implements ConstraintValidator<YearMonth,String> {
    private String pattern;
    @Override
    public void initialize(YearMonth constraintAnnotation) {
        this.pattern= constraintAnnotation.pattern();
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        //yyyyMM01
        System.out.println("custom annotation validation call");
        try{
            LocalDate localDate = LocalDate.parse(value+"01", DateTimeFormatter.ofPattern(this.pattern));
        }catch(Exception e){
            return false;
        }
        return true;
    }
}
```

그리고 어노테이션에서 메시지를 더이상 email의 default 메시지가 아닌, 

"yyyyMM형식이 아닙니다"로 변경해주자

```java
package com.validation.springvalidation.annotation;

import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.ElementType.TYPE_USE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

@Constraint(validatedBy = { })
//메서드, 생성자, 파라미터, 사용중인 곳 그 어디서라도, 필드, 어노테이션 유형 선언 시에 어노테이션이 적용됨
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
public @interface YearMonth {
    **String message() default "yyyyMM 형식이 아닙니다. 다시 확인해주세요(예: 202107)";**

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    String pattern() default "yyyyMM";
}
```

그리고 물론, message 속성값도 dto객체에서 다시 지정해줄 수 있다

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import com.validation.springvalidation.annotation.YearMonth;

import javax.validation.constraints.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    @NotBlank(message="이름에는 공백이 허용되지 않습니다")
    private String name;

    @Min(value=0, message="최소 0살 이상")
    @Max(value=100,message="최대 100살 이하")
    private int age;
    @Email
    private String email;
    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", message="잘못된 핸드폰 번호 양식입니다")
    private String phoneNumber;

    @YearMonth(pattern="yyyyMM", **message="올바른 형태가 아닙니다.")**
    private String reqYearMonth;//yyyyMM

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public String getReqYearMonth() {
        return reqYearMonth;
    }

    public void setReqYearMonth(String reqYearMonth) {
        this.reqYearMonth = reqYearMonth;
    }

//    @AssertTrue(message="잘못된 날짜 양식입니다. yyyyMM형태로 입력부탁드립니다.")
//    public boolean isReqYearMonthValidation(){
//        //return true: 정상
//        //return false: 정상x
//        System.out.println("assert true");
//        try{
//            LocalDate localDate = LocalDate.parse(getReqYearMonth()+"01", DateTimeFormatter.ofPattern("yyyyMMdd"));
//        }catch(Exception e){
//            return false;
//        }
//        return true;
//
//    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", reqYearMonth='" + reqYearMonth + '\'' +
                '}';
    }
}
```

우선 아래와 같이 기본값만 가지고 validation을 실행해보자

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import com.validation.springvalidation.annotation.YearMonth;

import javax.validation.constraints.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    @NotBlank(message="이름에는 공백이 허용되지 않습니다")
    private String name;

    @Min(value=0, message="최소 0살 이상")
    @Max(value=100,message="최대 100살 이하")
    private int age;
    @Email
    private String email;
    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", message="잘못된 핸드폰 번호 양식입니다")
    private String phoneNumber;
    
    **@YearMonth**
    private String reqYearMonth;//yyyyMM

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public String getReqYearMonth() {
        return reqYearMonth;
    }

    public void setReqYearMonth(String reqYearMonth) {
        this.reqYearMonth = reqYearMonth;
    }

//    @AssertTrue(message="잘못된 날짜 양식입니다. yyyyMM형태로 입력부탁드립니다.")
//    public boolean isReqYearMonthValidation(){
//        //return true: 정상
//        //return false: 정상x
//        System.out.println("assert true");
//        try{
//            LocalDate localDate = LocalDate.parse(getReqYearMonth()+"01", DateTimeFormatter.ofPattern("yyyyMMdd"));
//        }catch(Exception e){
//            return false;
//        }
//        return true;
//
//    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", reqYearMonth='" + reqYearMonth + '\'' +
                '}';
    }
}
```

그런데!! 우리가 빠뜨린 점이 하나 있다! 만약, 위와 같은 진행내용으로 수행하게 되면,

javax.validation.UnexpectedTypeException: HV000030: No validator could be found for constraint 'com.validation.springvalidation.annotation.YearMonth' validating type 'java.lang.String'. Check configuration for 'reqYearMonth'

no validator라고 안내받게 되는데

그것은 우리가 앞서 짧게 살펴봤던 validatedBy의 값이 비어져 있기 때문이다! 여기에 우리가 만든 validator(ConstraintValidator를 상속받음)를 넣어주자!

```java
package com.validation.springvalidation.annotation;

import com.validation.springvalidation.validator.YearMonthValidator;

import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.ElementType.TYPE_USE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

**@Constraint(validatedBy = {YearMonthValidator.class})**
//메서드, 생성자, 파라미터, 사용중인 곳 그 어디서라도, 필드, 어노테이션 유형 선언 시에 어노테이션이 적용됨
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
public @interface YearMonth {
    String message() default "yyyyMM 형식이 아닙니다. 다시 확인해주세요(예: 202107)";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    String pattern() default "yyyyMM";
}
```

그런데, 111111과 같이 올바른 형식을 넣어도 에러가 발생한다..

LocalDate부분과 catch부분에 브레이크포인트를 걸고 디버깅을 시작해보자

그러면 아래와 같이 패턴과 다른 패턴으로 값이 +"01"로 DateTimeParseException이 발생되어 catch부분에 접근하여 false가 반환되었기 때문임을 알 수 있다! 따라서 +"01"부분을 지워주자

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/pattern%EB%B6%88%EC%9D%BC%EC%B9%98%EB%A1%9C%20%EC%9D%B8%ED%95%B4%20%EC%9C%A0%EB%B0%9C%EB%90%A0%20%EC%88%98%20%EC%9E%88%EB%8A%94%20DateTimeParseException.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/pattern%EB%B6%88%EC%9D%BC%EC%B9%98%EB%A1%9C%20%EC%9D%B8%ED%95%B4%20%EC%9C%A0%EB%B0%9C%EB%90%A0%20%EC%88%98%20%EC%9E%88%EB%8A%94%20DateTimeParseException.PNG?raw=true)

```java
package com.validation.springvalidation.validator;

import com.validation.springvalidation.annotation.YearMonth;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;
import java.lang.annotation.Annotation;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class YearMonthValidator implements ConstraintValidator<YearMonth,String> {
    private String pattern;
    @Override
    public void initialize(YearMonth constraintAnnotation) {
        this.pattern= constraintAnnotation.pattern();
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        //yyyyMM01
        System.out.println("custom annotation validation call");
        try{
            LocalDate localDate = LocalDate.parse(**value**, DateTimeFormatter.ofPattern(this.pattern));
        }catch(Exception e){
            return false;
        }
        return true;
    }
}
```

그리고 한 번 다시 실행해보면

java.time.DateTimeException: Unable to obtain LocalDate from TemporalAccessor: {MonthOfYear=11, Year=1111},ISO of type java.time.format.Parsed

위와 같이 parse할 수 없다는 에러메시지가 뜨는데 

스택오버플로우신의 도움을 받아보니..

[Java: Unable to obtain LocalDate from TemporalAccessor](https://stackoverflow.com/questions/45320971/java-unable-to-obtain-localdate-from-temporalaccessor)

year, month, day가 모두 필요하다고 한다 😭

그래서 YearMonth 어노테이션에서 기본 패턴을 "yyyyMMdd"로 변경해주자

그리고 메시지도 그에 맞게 변경해주자

- 그게 아니라면 LocalDate에서 value를 value+"01"로 변경해주어도 무방하다

```java
package com.validation.springvalidation.annotation;

import com.validation.springvalidation.validator.YearMonthValidator;

import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.ElementType.TYPE_USE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

@Constraint(validatedBy = {YearMonthValidator.class})
//메서드, 생성자, 파라미터, 사용중인 곳 그 어디서라도, 필드, 어노테이션 유형 선언 시에 어노테이션이 적용됨
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
public @interface YearMonth {
    String message() default "**yyyyMMdd 형식이 아닙니다. 다시 확인해주세요(예: 20210701)**";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    String pattern() default "**yyyyMMdd**";
}
```

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20custom%20validation%20with%20custom%20validator,%20custom%20annotation,%20bindingResult.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20Boot%20Validation/Spring%20custom%20validation%20with%20custom%20validator,%20custom%20annotation,%20bindingResult.gif?raw=true)

그러면 지금은 yyyyMMdd 형태를 사용했고, 메시지도 default message를 이용했으므로

"11111111"과 같은 올바른 형태라면, 200 OK와 함께 에코된 response를 확인해볼 수 있고

그와 다르게 "a1111111"과 같은 올바르지 못한 형태라면, Bad Request로 400 상태코드와 함께 에러가 발생한 필드와 메시지를 확인받을 수 있다

🌟 그러면, 이제는, AssertTrue와 다르게, 해당 커스텀 어노테이션을 이용해서 원하는 곳에서 다시 사용할 수 있게 된다!

---

이번에는 

에서 이용했던 car dto를 활용해보자

그리고 User에 **List<Car> cars** 필드를 추가해보자

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import com.validation.springvalidation.annotation.YearMonth;

import javax.validation.constraints.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.List;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    @NotBlank(message="이름에는 공백이 허용되지 않습니다")
    private String name;

    @Min(value=0, message="최소 0살 이상")
    @Max(value=100,message="최대 100살 이하")
    private int age;
    @Email
    private String email;
    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", message="잘못된 핸드폰 번호 양식입니다")
    private String phoneNumber;

    @YearMonth
    private String reqYearMonth;//yyyyMM

    **private List<Car> cars;**

    public User() {
    }

    public User(String name, int age, String email, String phoneNumber) {
        this.name = name;
        this.age = age;
        this.email = email;
        this.phoneNumber = phoneNumber;
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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public String getReqYearMonth() {
        return reqYearMonth;
    }

    public void setReqYearMonth(String reqYearMonth) {
        this.reqYearMonth = reqYearMonth;
    }

    **public List<Car> getCars()** {
        return cars;
    }

    **public void setCars(List<Car> cars)** {
        this.cars = cars;
    }
//    @AssertTrue(message="잘못된 날짜 양식입니다. yyyyMM형태로 입력부탁드립니다.")
//    public boolean isReqYearMonthValidation(){
//        //return true: 정상
//        //return false: 정상x
//        System.out.println("assert true");
//        try{
//            LocalDate localDate = LocalDate.parse(getReqYearMonth()+"01", DateTimeFormatter.ofPattern("yyyyMMdd"));
//        }catch(Exception e){
//            return false;
//        }
//        return true;
//
//    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", reqYearMonth='" + reqYearMonth + '\'' +
                ", cars=" + cars +
                '}';
    }
}
```

그러고 나서

- Car의 모든 필드에 NotBlank를 부여
- User의 email, phoneNumber, reqYearMonth 지우기

로 정정해보자

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.annotation.JsonProperty;
import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import javax.validation.constraints.NotBlank;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class Car {
    @NotBlank
    private String name;
    @NotBlank
    @JsonProperty("car_number")
    private String carNumber;
    @NotBlank
    @JsonProperty("TYPE")
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

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import com.validation.springvalidation.annotation.YearMonth;

import javax.validation.constraints.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.List;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    @NotBlank(message="이름에는 공백이 허용되지 않습니다")
    private String name;

    @Min(value=0, message="최소 0살 이상")
    @Max(value=100,message="최대 100살 이하")
    private int age;
  //  @Email
  //  private String email;
//    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", message="잘못된 핸드폰 번호 양식입니다")
//    private String phoneNumber;

 //   @YearMonth
 //   private String reqYearMonth;//yyyyMM

    private List<Car> cars;

    public User() {
    }

    public User(String name, int age, List<Car> cars/*, String email, String phoneNumber*/) {
        this.name = name;
        this.age = age;
        this.cars=cars;
     //   this.email = email;
      //  this.phoneNumber = phoneNumber;
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

//    public String getEmail() {
//        return email;
//    }
//
//    public void setEmail(String email) {
//        this.email = email;
//    }

/*    public String getPhoneNumber() {
        return phoneNumber;
    }*/

//    public void setPhoneNumber(String phoneNumber) {
//        this.phoneNumber = phoneNumber;
//    }
//
//    public String getReqYearMonth() {
//        return reqYearMonth;
//    }
//
//    public void setReqYearMonth(String reqYearMonth) {
//        this.reqYearMonth = reqYearMonth;
//    }

    public List<Car> getCars() {
        return cars;
    }

    public void setCars(List<Car> cars) {
        this.cars = cars;
    }
//    @AssertTrue(message="잘못된 날짜 양식입니다. yyyyMM형태로 입력부탁드립니다.")
//    public boolean isReqYearMonthValidation(){
//        //return true: 정상
//        //return false: 정상x
//        System.out.println("assert true");
//        try{
//            LocalDate localDate = LocalDate.parse(getReqYearMonth()+"01", DateTimeFormatter.ofPattern("yyyyMMdd"));
//        }catch(Exception e){
//            return false;
//        }
//        return true;
//
//    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
               /* ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", reqYearMonth='" + reqYearMonth */+ '\'' +
                ", cars=" + cars +
                '}';
    }
}
```

그리고 ObjectMapper에서 사용했던 요청

```java
{
  "name": "홍길동",
  "age": 10,
  "cars": [
    {
      "name": "K5",
      "car_number":"11가 1111",
      "TYPE":"세단"
    },
    {
      "name": "Q5",
      "car_number":"11가 1112",
      "TYPE":"SUV"
    }

  ]
}
```

을 그대로 사용해보자

그러면 200 OK와 함께 그대로 에코되는 것을 확인해볼 수 있다

그런데 만약

```java
{
  "name": "홍길동",
  "age": 10,
  "cars": [
    {
      "name": "K5",
    },
    {
      "name": "Q5"
    }

  ]
}
```

위와 같이 ArrayList의 일부 내용이 비워져 있다면,

원래는 아래의 방법으로 접근해보는 것이 적합하겠지만

지금은 공백에 대한 처리가 중요하기 때문에 

---

나중에 더 학습해볼 부분인데, null을 방지하기 위해서는

**@JsonInclude(JsonInclude.Include.NON_NULL)**로 방지하거나

**objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);**로 방지할 수 있다

---

```java
{
  "name": "홍길동",
  "age": 10,
  "cars": [
    {
      "name": "K5",
      "car_number":"",
      "TYPE":""
    },
    {
      "name": "Q5",
      "car_number":"",
      "TYPE":""
    }

  ]
}
```

위와 같이 공백으로 테스트해보자

그러면 원래는, Bad Request가 떠야하는데

```java
{
"name": "홍길동",
"age": 10,
"cars":[
{
"name": "K5",
"car_number": "",
"TYPE": ""
},
{
"name": "Q5",
"car_number": "",
"TYPE": ""
}
]
}
```

위와 같이 응답이 되는 것을 확인해볼 수 있다

이는 User에서 cars 필드에 valid가 없기 때문이다!

```java
package com.validation.springvalidation.dto;

import com.fasterxml.jackson.annotation.JsonInclude;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import com.validation.springvalidation.annotation.YearMonth;

import javax.validation.Valid;
import javax.validation.constraints.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.List;

@JsonNaming(value= PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {

    @NotBlank(message="이름에는 공백이 허용되지 않습니다")
    private String name;

    @Min(value=0, message="최소 0살 이상")
    @Max(value=100,message="최대 100살 이하")
    private int age;
  //  @Email
  //  private String email;
//    @Pattern(regexp ="^\\d{2,3}-\\d{3,4}-\\d{4}$", message="잘못된 핸드폰 번호 양식입니다")
//    private String phoneNumber;

 //   @YearMonth
 //   private String reqYearMonth;//yyyyMM
    **@Valid**
    private List<Car> cars;

    public User() {

    }

    public User(String name, int age, List<Car> cars/*, String email, String phoneNumber*/) {
        this.name = name;
        this.age = age;
        this.cars=cars;

     //   this.email = email;
      //  this.phoneNumber = phoneNumber;
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

//    public String getEmail() {
//        return email;
//    }
//
//    public void setEmail(String email) {
//        this.email = email;
//    }

/*    public String getPhoneNumber() {
        return phoneNumber;
    }*/

//    public void setPhoneNumber(String phoneNumber) {
//        this.phoneNumber = phoneNumber;
//    }
//
//    public String getReqYearMonth() {
//        return reqYearMonth;
//    }
//
//    public void setReqYearMonth(String reqYearMonth) {
//        this.reqYearMonth = reqYearMonth;
//    }

    public List<Car> getCars() {
        return cars;
    }

    public void setCars(List<Car> cars) {
        this.cars = cars;
    }
//    @AssertTrue(message="잘못된 날짜 양식입니다. yyyyMM형태로 입력부탁드립니다.")
//    public boolean isReqYearMonthValidation(){
//        //return true: 정상
//        //return false: 정상x
//        System.out.println("assert true");
//        try{
//            LocalDate localDate = LocalDate.parse(getReqYearMonth()+"01", DateTimeFormatter.ofPattern("yyyyMMdd"));
//        }catch(Exception e){
//            return false;
//        }
//        return true;
//
//    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
               /* ", email='" + email + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", reqYearMonth='" + reqYearMonth */+ '\'' +
                ", cars=" + cars +
                '}';
    }
}
```

그러면 위와 같이 정정해주고, 요청을 동일하게 보내면 이번에는

```java
field: cars[1].carNumber,	 message: 공백일 수 없습니다field: cars[0].type,	 message: 공백일 수 없습니다field: cars[1].type,	 message: 공백일 수 없습니다field: cars[0].carNumber,	 message: 공백일 수 없습니다
```

위와 같은 에러 메시지를 접하게 된다!

🌟 즉, Valid는 파라미터 위치 뿐 아니라, dto 객체에서도 다른 dto와 연관된 필드의 위에 붙일 수 있다! 그럼으로써 연결되는 dto에 대한 검증이 가능하다!

는 것을 알 수 있다