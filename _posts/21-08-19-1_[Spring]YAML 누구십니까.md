# WHO ARE YOU 'YAML'?

## 01. YAML의 풀네임

> YAML Ain't Markup Language "YAML은 마크업 언어가 아니다"

▶️ YAML의 핵심은 문서 마크업이 아닌 `데이터 중심에 있다는 것`을 의미!

## 02. YAML의 확장자

> YAML의 확장자는 `*.yml`이다!

🌟 기존의 main-resources-application.properties를 삭제하고 application.yml을 생성하여 사용 가능하다!

🌟 application.properties와 application.yml 모두 있다면 application.yml만 오버라이드되어 적용된다!

## 03. YAML의 장점

1. `프로퍼티 설정값의 깊이에 따라 들여쓰기를 해서 계층구조를 훨씬 쉽게 파악 가능`
2. 1로 인해서 List, Set, Map 등 다양한 바인딩형 매핑이 훨씬 간편

🌟 스프링 부트 스타터에는 `이미 SnakeYAML라이브러리가 기본적으로 내장`되어 있어서 별도 설정 없이 사용 가능!

## 04. 프로파일에 따른 환경 분리

📌 프로파일 profile 📌

[https://zetcode.com/springboot/profile/#:~:text=Spring Boot profiles,-The development process&text=A profile is a set,the properties in an application.&text=Note%3A Spring Boot properties are loaded in a particular order](https://zetcode.com/springboot/profile/#:~:text=Spring%20Boot%20profiles,-The%20development%20process&text=A%20profile%20is%20a%20set,the%20properties%20in%20an%20application.&text=Note%3A%20Spring%20Boot%20properties%20are%20loaded%20in%20a%20particular%20order).

configuration setting(환경 설정)의 집합!

`---는 프로파일 설정 구분자!`

▶️ 프로퍼티 설정을 YAML 파일 내에서 구분할 수 있도록 도와줌

```yaml
#a
server:
  port: 8089
---
#b
spring:
  profiles: local
server:
  port: 8090
---
#c
spring:
  profiles: dev
server:
  port: 9090
```

🌟 이렇게 설정해두면 , a는 기본 설정되는 부분이고, `spring.profiles:{profile}`로 된 부분은 `application-{profile}.yml` 파일을 읽어들여서 어플리케이션 환경이 구성된다!

이때, 프로퍼티 적용 우선순위는 (프로퍼티는 프로파일의 요소라고 생각!) application-dev.yml > application-local.yml> application.yml 순으로 우선순위가 적용된다!

## 05. YAML 파일 매핑하기

`@Value`, `@ConfigurationProperties`

[@Value 와 @ConfigurationProperties](https://www.notion.so/ce8facdb103f4396aa897deffb7c79ac)

① 유연한 바인딩

- 프로퍼티 값을 객체에 바인딩할 경우, 필드를 카멜케이스(표기법)로 선언하고, 프로퍼티의 키는 다양한 표기법으로 바인딩 가능

↔️ 프로퍼티의 표기법 ≠ 객체 필드의 표기법 허용!

② 메타 데이터 지원

- 프로퍼티의 키에 대한 정보를 `메타데이터(특정 목적을 위해 만든 데이터, 데이터에 관한 구조화된 데이터, 다른 데이터를 설명해주는 데이터)` 파일(JSON)로 제공
- 키의 이름, 타입, 설명, 디폴트값 등 키 사용에 앞서 힌트가 되는 정보를 얻을 수 있음

③ SpEL(Spring Expression Language: 스프링 표현 언어) 평가

- SpEL

-런타임에 객체 참조에 대해서 질의하고 조작하는 기능을 지원 

-메서드 호출 및 기본 문자열 템플릿 기능 제공

-@Value만 사용 가능

### 05-1. `@Value를 이용한 프로퍼티 키-value값 처리 확인하기`

application.yml 파일에 프로퍼티값을 아래와 같이 추가

```yaml
server:
  port: 8089

#{키:값}
**property:
  test:
    name:
      property depth test
propertyTest:
    test
propertyTestList:
  a,b,c**
```

이번에는 `AutoconfigurationApplicationTests 클래스`에서 테스트 프로퍼티값을 매핑해서 사용하는 코드를 만들어주겠습니다!

```java
package com.example.yaml_setting;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

**import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.is;**
import static org.junit.jupiter.api.Assertions.assertEquals;

@ExtendWith(SpringExtension.class)//JUnit4의 RunWith(SpringRunner.class)와 동일!
@SpringBootTest
public class AutoConfigurationApplicationTests {
    **@Value("${property.test.name}")**
    private String propertyTestName;

    **@Value("${propertyTest}")**
    private String propertyTest;

    **@Value("${noKey:default value}")**
    private String defaultValue;

    **@Value("${propertyTestList}")**
    private String[] propertyTestArray;

    **@Value("#{'${propertyTestList}'.split(',')}")**
    private List<String> propertyTestList;

    @Test
    public void testYamlKeyValue(){

        assertThat(propertyTestName,is("property depth test"));
        assertThat(propertyTest,is("test"));
        assertThat(defaultValue,is("default value"));

        assertThat(propertyTestArray[0],is("a"));
        assertThat(propertyTestArray[1],is("b"));
        assertThat(propertyTestArray[2],is("c"));

        assertThat(propertyTestList,is(new ArrayList<String>(Arrays.asList("a","b","c"))));
        assertEquals(propertyTestList,new ArrayList<String>(Arrays.asList("a","b","c")));
        assertThat(propertyTestList.get(0),is("a"));
        assertThat(propertyTestList.get(1),is("b"));
        assertThat(propertyTestList.get(2),is("c"));
    }
}
```

인텔리제이 실행키: ctrl+shift+F10

🌟 @Value의 매핑 방식 🌟

[@Value의 매핑 방식](https://www.notion.so/14a655e9263d49ae99f795f40de9842f)

### 05-2. `@ConfigurationProperties를 이용한 프로퍼티 키-value값 처리 확인하기`

application.yml에 아래와 같은 프로퍼티 관련 key-value를 추가해주자

이번에는 List, Map 자료구조로 프로퍼티값을 매핑하는 형식을 알아보자

🌟 `@ConfigurationProperties는 기본적으로 접두사 Prefix를 사용해서 값을 바인딩`한다!

```yaml
server:
  port: 8089

#value
property:
  test:
    name:
      property depth test
propertyTest:
    test
propertyTestList:
  a,b,c

#ConfigurationProperties
**fruit:
  list:
    - name:
        banana
      color:
        yellow
    - name:
        apple
      color:
        red
    - name:
        water melon
      color:
        green**
```

이번에는 위를 이용한 FruitProperty 클래스를 만들자! 

- name과 color가 마치 Map 구조를 이루는 듯하다!
- 그리고 이러한 Map이 리스트 형태를 띄고 있다!

🌟먼저, @ConfigurationProperties를 사용해주기 위해서 `spring boot configuration annotation processor`를 추가해줘야 한다!

🌟 @ConfigurationProperties를 사용해주기 위해서는, 해당 클래스를 빈 객체로 등록해주기 위해서 `@Component`로 선언해야 한다!

[Intellij - Spring Boot Configuration Annotation Processor not configured Error](https://hanke-r.tistory.com/107?category=836499)

```groovy
plugins {
    id 'org.springframework.boot' version '2.5.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
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
    **annotationProcessor 'org.springframework.boot:spring-boot-configuration-processor'**
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
    useJUnitPlatform()
}
```

```java
package com.example.yaml_setting.pojo;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.Map;

@Data
**@Component**
**@ConfigurationProperties("fruit")**
public class FruitProperty {
    private List<Map> list;
}
```

이제 application.yml의 프로퍼티값들은 FruitProperty 클래스의 list 필드로 바인딩된다!

🌟application.yml 이 아닌 다른 이름의 YAML 파일을 따로 생성해서 관리할 경우, 다음과 같이 사용하면 된다!

```java
@ConfigurationProperties(prefix="fruit")
```

이제 FruitProperty 객체를 주입해서 통합테스트를 진행해보자!

```java
package com.example.yaml_setting.pojo;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import java.util.List;
import java.util.Map;

**import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.is;**

@ExtendWith(SpringExtension.class)
@SpringBootTest
public class FruitPropertyTest {

    @Autowired
    private FruitProperty fruitProperty;

    @Test
    public void test(){
        List<Map> fruitData=fruitProperty.getList();

        assertThat(fruitData.get(0).get("name"),is("banana"));
        assertThat(fruitData.get(0).get("color"),is("yellow"));

        assertThat(fruitData.get(1).get("name"),is("apple"));
        assertThat(fruitData.get(1).get("color"),is("red"));

        assertThat(fruitData.get(2).get("name"),is("water melon"));
        assertThat(fruitData.get(2).get("color"),is("green"));
    }

}
```

위의 테스트를 진행해보면, 테스트가 통과되는 것을 확인해볼 수 있다!

그리고 이번에는 컬렉션을 사용하지 않고, POJO를 기반으로 Fruit POJO 클래스를 생성해보자!

- `보다 직관적이고 명확하게 객체를 구성할 수 있다는 장점이 POJO로 구성하는 객체에게 있다!`

```java
package com.example.yaml_setting.pojo;

import lombok.Data;

@Data
public class Fruit {
    private String name;
    private String color;
}
```

```java
package com.example.yaml_setting.pojo;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.Map;

@Data
@Component
@ConfigurationProperties("fruit")
public class FruitProperty {
    //private List<Map> list;
    private **List<Fruit>** list;
}
```

그러면 위와 같이, FruitProperty도 `List<Map>` 이 아닌, `List<Fruit>`의 형태로 의미가 보다 더 명확해진 필드를 확인해볼 수 있다!

더불어, 통합테스트에서도 동일 맥락을 확인해볼 수 있을 뿐 아니라, `getter를 통해 접근할 수 있게 된다!`

```java
package com.example.yaml_setting.pojo;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import java.util.List;
import java.util.Map;

import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.is;

@ExtendWith(SpringExtension.class)
@SpringBootTest
public class FruitPropertyTest {

    @Autowired
    private FruitProperty fruitProperty;

    @Test
    public void test(){
        **List<Fruit> fruitData=fruitProperty.getList();

        assertThat(fruitData.get(0).getName(),is("banana"));
        assertThat(fruitData.get(0).getColor(),is("yellow"));

        assertThat(fruitData.get(1).getName(),is("apple"));
        assertThat(fruitData.get(1).getColor(),is("red"));

        assertThat(fruitData.get(2).getName(),is("water melon"));
        assertThat(fruitData.get(2).getColor(),is("green"));**
    }

}
```

### 05-2-1. `@ConfigurationProperties의 유연한 바인딩`

프로퍼티값을 객체에 바인딩할 경우 `필드를 카멜케이스로 선언`하고, `프로퍼티의 키는 다양한 표기법으로 선언`하여 바인딩할 수 있는 것!

먼저 기존의 application.yml을 아래와 같이 수정해보도록 하자

```yaml
server:
  port: 8089

#value
property:
  test:
    name:
      property depth test
propertyTest:
    test
propertyTestList:
  a,b,c

#ConfigurationProperties
**fruit:
  list:
    - fruit_name:
        banana
      fruit_color:
        yellow
    - fruit-name:
        apple
      fruit-color:
        red
    - FRUITNAME:
        water melon
      FRUITCOLOR:
        green**
```

```java
package com.example.yaml_setting.pojo;

import lombok.Data;

@Data
public class Fruit {
    private String fruitName;
    private String fruitColor;
}
```

```java
package com.example.yaml_setting.pojo;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import java.util.List;
import java.util.Map;

import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.is;

@ExtendWith(SpringExtension.class)
@SpringBootTest
public class FruitPropertyTest {

    @Autowired
    private FruitProperty fruitProperty;

    @Test
    public void test(){
        List<Fruit> fruitData=fruitProperty.getList();

        assertThat(fruitData.get(0).getFruitName(),is("banana"));
        assertThat(fruitData.get(0).getFruitColor(),is("yellow"));

        assertThat(fruitData.get(1).getFruitName(),is("apple"));
        assertThat(fruitData.get(1).getFruitColor(),is("red"));

        assertThat(fruitData.get(2).getFruitName(),is("water melon"));
        assertThat(fruitData.get(2).getFruitColor(),is("green"));
    }

}
```

위와 같이 yaml 파일에서는 언더바 표기법, 스네이크 표기법, 대문자 표기법을 사용하고

자바에서는 카멜 표기법을 이용했는데 위의 테스트를 돌려보면,

test passed가 표시되는 것을 확인해볼 수 있다

## 🌟06. 스프링부트 자동 환경 설정 이해하기 Spring Boot auto-configuration

자동 환경 설정은 스프링부트의 장점이자 매우 중요한 역할을 한다!

Web, H2, JDBC 등 다양한 자동설정을 지원해준다!

새로 추가되는 라이브러리(JAR)는 스프링부트 자동설정 의존성에 따라 설정이 자동적용된다!

### 06-1. 자동 환경 설정 어노테이션

📌 스프링 부트의 장점이자 방향성 📌

### 06-1-1. `@SpringBootApplication`

```java
/*
 * Copyright 2012-2020 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package org.springframework.boot.autoconfigure;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.springframework.beans.factory.support.BeanNameGenerator;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.boot.context.TypeExcludeFilter;
import org.springframework.context.annotation.AnnotationBeanNameGenerator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import org.springframework.core.annotation.AliasFor;
import org.springframework.data.repository.Repository;

/**
 * Indicates a {@link Configuration configuration} class that declares one or more
 * {@link Bean @Bean} methods and also triggers {@link EnableAutoConfiguration
 * auto-configuration} and {@link ComponentScan component scanning}. This is a convenience
 * annotation that is equivalent to declaring {@code @Configuration},
 * {@code @EnableAutoConfiguration} and {@code @ComponentScan}.
 *
 * @author Phillip Webb
 * @author Stephane Nicoll
 * @author Andy Wilkinson
 * @since 1.2.0
 */
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
**@SpringBootConfiguration
@EnableAutoConfiguration**
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

	/**
	 * Exclude specific auto-configuration classes such that they will never be applied.
	 * @return the classes to exclude
	 */
	@AliasFor(annotation = EnableAutoConfiguration.class)
	Class<?>[] exclude() default {};

	/**
	 * Exclude specific auto-configuration class names such that they will never be
	 * applied.
	 * @return the class names to exclude
	 * @since 1.3.0
	 */
	@AliasFor(annotation = EnableAutoConfiguration.class)
	String[] excludeName() default {};

	/**
	 * Base packages to scan for annotated components. Use {@link #scanBasePackageClasses}
	 * for a type-safe alternative to String-based package names.
	 * <p>
	 * <strong>Note:</strong> this setting is an alias for
	 * {@link ComponentScan @ComponentScan} only. It has no effect on {@code @Entity}
	 * scanning or Spring Data {@link Repository} scanning. For those you should add
	 * {@link org.springframework.boot.autoconfigure.domain.EntityScan @EntityScan} and
	 * {@code @Enable...Repositories} annotations.
	 * @return base packages to scan
	 * @since 1.3.0
	 */
	@AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
	String[] scanBasePackages() default {};

	/**
	 * Type-safe alternative to {@link #scanBasePackages} for specifying the packages to
	 * scan for annotated components. The package of each class specified will be scanned.
	 * <p>
	 * Consider creating a special no-op marker class or interface in each package that
	 * serves no purpose other than being referenced by this attribute.
	 * <p>
	 * <strong>Note:</strong> this setting is an alias for
	 * {@link ComponentScan @ComponentScan} only. It has no effect on {@code @Entity}
	 * scanning or Spring Data {@link Repository} scanning. For those you should add
	 * {@link org.springframework.boot.autoconfigure.domain.EntityScan @EntityScan} and
	 * {@code @Enable...Repositories} annotations.
	 * @return base packages to scan
	 * @since 1.3.0
	 */
	@AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
	Class<?>[] scanBasePackageClasses() default {};

	/**
	 * The {@link BeanNameGenerator} class to be used for naming detected components
	 * within the Spring container.
	 * <p>
	 * The default value of the {@link BeanNameGenerator} interface itself indicates that
	 * the scanner used to process this {@code @SpringBootApplication} annotation should
	 * use its inherited bean name generator, e.g. the default
	 * {@link AnnotationBeanNameGenerator} or any custom instance supplied to the
	 * application context at bootstrap time.
	 * @return {@link BeanNameGenerator} to use
	 * @see SpringApplication#setBeanNameGenerator(BeanNameGenerator)
	 * @since 2.3.0
	 */
	@AliasFor(annotation = ComponentScan.class, attribute = "nameGenerator")
	Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

	/**
	 * Specify whether {@link Bean @Bean} methods should get proxied in order to enforce
	 * bean lifecycle behavior, e.g. to return shared singleton bean instances even in
	 * case of direct {@code @Bean} method calls in user code. This feature requires
	 * method interception, implemented through a runtime-generated CGLIB subclass which
	 * comes with limitations such as the configuration class and its methods not being
	 * allowed to declare {@code final}.
	 * <p>
	 * The default is {@code true}, allowing for 'inter-bean references' within the
	 * configuration class as well as for external calls to this configuration's
	 * {@code @Bean} methods, e.g. from another configuration class. If this is not needed
	 * since each of this particular configuration's {@code @Bean} methods is
	 * self-contained and designed as a plain factory method for container use, switch
	 * this flag to {@code false} in order to avoid CGLIB subclass processing.
	 * <p>
	 * Turning off bean method interception effectively processes {@code @Bean} methods
	 * individually like when declared on non-{@code @Configuration} classes, a.k.a.
	 * "@Bean Lite Mode" (see {@link Bean @Bean's javadoc}). It is therefore behaviorally
	 * equivalent to removing the {@code @Configuration} stereotype.
	 * @since 2.2
	 * @return whether to proxy {@code @Bean} methods
	 */
	@AliasFor(annotation = Configuration.class)
	boolean proxyBeanMethods() default true;

}
```

ⓐ `@SpringBootConfiguration` : 스프링 부트의 설정을 나타내는 어노테이션

- 스프링의 `@Configuration`을 대체하는 스프링 부트 전용 어노테이션

ⓑ `@EnableAutoConfiguration` : 자동 설정의 핵심 어노테이션

- 클래스 경로에 지정된 내용을 기반으로 영리하게 설정을 자동화
- 특별한 설정값을 추가하지 않으면 기본값으로 작동

ⓒ `@ComponentScan` : 특정 패키지 경로를 기반으로 `@Configuration`에서 사용할 `@Component` 설정 클래스를 찾음

- basePackages 프로퍼티값에 별도의 경로를 설정하지 않으면 `@ComponentScan`이 위치한 패키지가 루트경로(BasePackage)로 설정됨

```java
/*
 * Copyright 2002-2020 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package org.springframework.context.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.springframework.beans.factory.support.BeanNameGenerator;
import org.springframework.core.annotation.AliasFor;
import org.springframework.core.type.filter.TypeFilter;

/**
 * Configures component scanning directives for use with @{@link Configuration} classes.
 * Provides support parallel with Spring XML's {@code <context:component-scan>} element.
 *
 * <p>Either {@link #basePackageClasses} or {@link #basePackages} (or its alias
 * {@link #value}) may be specified to define specific packages to scan. If specific
 * packages are not defined, scanning will occur from the package of the
 * class that declares this annotation.
 *
 * <p>Note that the {@code <context:component-scan>} element has an
 * {@code annotation-config} attribute; however, this annotation does not. This is because
 * in almost all cases when using {@code @ComponentScan}, default annotation config
 * processing (e.g. processing {@code @Autowired} and friends) is assumed. Furthermore,
 * when using {@link AnnotationConfigApplicationContext}, annotation config processors are
 * always registered, meaning that any attempt to disable them at the
 * {@code @ComponentScan} level would be ignored.
 *
 * <p>See {@link Configuration @Configuration}'s Javadoc for usage examples.
 *
 * @author Chris Beams
 * @author Juergen Hoeller
 * @author Sam Brannen
 * @since 3.1
 * @see Configuration
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Repeatable(ComponentScans.class)
public @interface ComponentScan {

	/**
	 * Alias for {@link #basePackages}.
	 * <p>Allows for more concise annotation declarations if no other attributes
	 * are needed &mdash; for example, {@code @ComponentScan("org.my.pkg")}
	 * instead of {@code @ComponentScan(basePackages = "org.my.pkg")}.
	 */
	@AliasFor("basePackages")
	String[] value() default {};

	/**
	 * Base packages to scan for annotated components.
	 * <p>{@link #value} is an alias for (and mutually exclusive with) this
	 * attribute.
	 * <p>Use {@link #basePackageClasses} for a type-safe alternative to
	 * String-based package names.
	 */
	@AliasFor("value")
	String[] basePackages() default {};

	/**
	 * Type-safe alternative to {@link #basePackages} for specifying the packages
	 * to scan for annotated components. The package of each class specified will be scanned.
	 * <p>Consider creating a special no-op marker class or interface in each package
	 * that serves no purpose other than being referenced by this attribute.
	 */
	Class<?>[] basePackageClasses() default {};

	/**
	 * The {@link BeanNameGenerator} class to be used for naming detected components
	 * within the Spring container.
	 * <p>The default value of the {@link BeanNameGenerator} interface itself indicates
	 * that the scanner used to process this {@code @ComponentScan} annotation should
	 * use its inherited bean name generator, e.g. the default
	 * {@link AnnotationBeanNameGenerator} or any custom instance supplied to the
	 * application context at bootstrap time.
	 * @see AnnotationConfigApplicationContext#setBeanNameGenerator(BeanNameGenerator)
	 * @see AnnotationBeanNameGenerator
	 * @see FullyQualifiedAnnotationBeanNameGenerator
	 */
	Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

	/**
	 * The {@link ScopeMetadataResolver} to be used for resolving the scope of detected components.
	 */
	Class<? extends ScopeMetadataResolver> scopeResolver() default AnnotationScopeMetadataResolver.class;

	/**
	 * Indicates whether proxies should be generated for detected components, which may be
	 * necessary when using scopes in a proxy-style fashion.
	 * <p>The default is defer to the default behavior of the component scanner used to
	 * execute the actual scan.
	 * <p>Note that setting this attribute overrides any value set for {@link #scopeResolver}.
	 * @see ClassPathBeanDefinitionScanner#setScopedProxyMode(ScopedProxyMode)
	 */
	ScopedProxyMode scopedProxy() default ScopedProxyMode.DEFAULT;

	/**
	 * Controls the class files eligible for component detection.
	 * <p>Consider use of {@link #includeFilters} and {@link #excludeFilters}
	 * for a more flexible approach.
	 */
	String resourcePattern() default ClassPathScanningCandidateComponentProvider.DEFAULT_RESOURCE_PATTERN;

	/**
	 * Indicates whether automatic detection of classes annotated with {@code @Component}
	 * {@code @Repository}, {@code @Service}, or {@code @Controller} should be enabled.
	 */
	boolean useDefaultFilters() default true;

	/**
	 * Specifies which types are eligible for component scanning.
	 * <p>Further narrows the set of candidate components from everything in {@link #basePackages}
	 * to everything in the base packages that matches the given filter or filters.
	 * <p>Note that these filters will be applied in addition to the default filters, if specified.
	 * Any type under the specified base packages which matches a given filter will be included,
	 * even if it does not match the default filters (i.e. is not annotated with {@code @Component}).
	 * @see #resourcePattern()
	 * @see #useDefaultFilters()
	 */
	Filter[] includeFilters() default {};

	/**
	 * Specifies which types are not eligible for component scanning.
	 * @see #resourcePattern
	 */
	Filter[] excludeFilters() default {};

	/**
	 * Specify whether scanned beans should be registered for lazy initialization.
	 * <p>Default is {@code false}; switch this to {@code true} when desired.
	 * @since 4.1
	 */
	boolean lazyInit() default false;

	/**
	 * Declares the type filter to be used as an {@linkplain ComponentScan#includeFilters
	 * include filter} or {@linkplain ComponentScan#excludeFilters exclude filter}.
	 */
	@Retention(RetentionPolicy.RUNTIME)
	@Target({})
	@interface Filter {

		/**
		 * The type of filter to use.
		 * <p>Default is {@link FilterType#ANNOTATION}.
		 * @see #classes
		 * @see #pattern
		 */
		FilterType type() default FilterType.ANNOTATION;

		/**
		 * Alias for {@link #classes}.
		 * @see #classes
		 */
		@AliasFor("classes")
		Class<?>[] value() default {};

		/**
		 * The class or classes to use as the filter.
		 * <p>The following table explains how the classes will be interpreted
		 * based on the configured value of the {@link #type} attribute.
		 * <table border="1">
		 * <tr><th>{@code FilterType}</th><th>Class Interpreted As</th></tr>
		 * <tr><td>{@link FilterType#ANNOTATION ANNOTATION}</td>
		 * <td>the annotation itself</td></tr>
		 * <tr><td>{@link FilterType#ASSIGNABLE_TYPE ASSIGNABLE_TYPE}</td>
		 * <td>the type that detected components should be assignable to</td></tr>
		 * <tr><td>{@link FilterType#CUSTOM CUSTOM}</td>
		 * <td>an implementation of {@link TypeFilter}</td></tr>
		 * </table>
		 * <p>When multiple classes are specified, <em>OR</em> logic is applied
		 * &mdash; for example, "include types annotated with {@code @Foo} OR {@code @Bar}".
		 * <p>Custom {@link TypeFilter TypeFilters} may optionally implement any of the
		 * following {@link org.springframework.beans.factory.Aware Aware} interfaces, and
		 * their respective methods will be called prior to {@link TypeFilter#match match}:
		 * <ul>
		 * <li>{@link org.springframework.context.EnvironmentAware EnvironmentAware}</li>
		 * <li>{@link org.springframework.beans.factory.BeanFactoryAware BeanFactoryAware}
		 * <li>{@link org.springframework.beans.factory.BeanClassLoaderAware BeanClassLoaderAware}
		 * <li>{@link org.springframework.context.ResourceLoaderAware ResourceLoaderAware}
		 * </ul>
		 * <p>Specifying zero classes is permitted but will have no effect on component
		 * scanning.
		 * @since 4.2
		 * @see #value
		 * @see #type
		 */
		@AliasFor("value")
		Class<?>[] classes() default {};

		/**
		 * The pattern (or patterns) to use for the filter, as an alternative
		 * to specifying a Class {@link #value}.
		 * <p>If {@link #type} is set to {@link FilterType#ASPECTJ ASPECTJ},
		 * this is an AspectJ type pattern expression. If {@link #type} is
		 * set to {@link FilterType#REGEX REGEX}, this is a regex pattern
		 * for the fully-qualified class names to match.
		 * @see #type
		 * @see #classes
		 */
		String[] pattern() default {};

	}

}
```

`@SpringBootApplication` = `@SpringBootConfiguration` ➕ `@EnableAutoConfiguration` ➕ `@ComponentScan`  !!

📌이 중에서 주의깊게 볼 것은 `@EnableAutoConfiguration` !!

### 06-2. `@EnableAutoConfiguration` 살펴보기

```java
/*
 * Copyright 2012-2020 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package org.springframework.boot.autoconfigure;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.springframework.boot.autoconfigure.condition.ConditionalOnBean;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory;
import org.springframework.boot.web.servlet.server.ServletWebServerFactory;
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.core.io.support.SpringFactoriesLoader;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
**@Import(AutoConfigurationImportSelector.class)**
public @interface EnableAutoConfiguration {

	/**
	 * Environment property that can be used to override when auto-configuration is
	 * enabled.
	 */
	String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

	/**
	 * Exclude specific auto-configuration classes such that they will never be applied.
	 * @return the classes to exclude
	 */
	Class<?>[] exclude() default {};

	/**
	 * Exclude specific auto-configuration class names such that they will never be
	 * applied.
	 * @return the class names to exclude
	 * @since 1.3.0
	 */
	String[] excludeName() default {};

}
```

위는 EnableAutoConfiguration 어노테이션 소스코드이다!

이 중 위의 **`@Import(AutoConfigurationImportSelector.class)`** 

이 부분이 `자동 설정을 지원`해 주는데, 직역해보면 `임포트할 자동설정을 선택`한다고 볼 수 있다!

`AutoConfigurationImportSelector` 클래스를 조금 더 살펴보자

```java
public class AutoConfigurationImportSelector implements **DeferredImportSelector**, BeanClassLoaderAware,
		ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {

(중략)
@Override
	public String[] selectImports(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return NO_IMPORTS;
		}
		AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata);
		return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
	}
```

- AutoConfigurationImportSelector는 `DeferredImportSelector` 인터페이스를 구현한 클래스
- selectImports 메서드가 아래의 과정으로 `자동 설정할 빈을 설정`

-모든 후보빈을 불러오기 (getCandidateConfigurations)

-빈 중복 등록을 방지하기 위해서 getExclusions메서드로 제외할 설정을 지정해주고, 중복된 설정 제외를 위해 removeDuplicates 메서드를 이용

- 빈 등록과 자동 설정에 필요한 파일

-META-INF/spring.factories: 자동 설정 타깃 클래스 목록. 이곳에 선언된 클래스들이 `@EnableAutoConfiguration` 사용 시 자동 설정 타깃이 됨

-META-INF/spring-configuration-metadata.json : 자동 설정에 사용할 프로퍼티 정의 파일

-org/springframework/boot/autoconfigure: 미리 구현해놓은 자동설정리스트

모두 자바 설정 방식을 따르고 있으며, `{특정 설정의 이름}AutoConfiguration` 형식으로 지정되어 있음

![https://github.com/hy6219/TIL/blob/main/Spring/YAML/AutoConfigurationImportSelector-selectImports.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/YAML/AutoConfigurationImportSelector-selectImports.PNG?raw=true)

해당 파일들은 `External Libraries-Gradle:org.springframework.boot.spring-boot-autoconfigure2.5.3` 에 위치한다!

만약, 우리가 application.yml에 `spring.h2.console.path:/h2-test` 를 추가해주면 spring-configuration-metadata.json에  설정이 자동주입된다!!