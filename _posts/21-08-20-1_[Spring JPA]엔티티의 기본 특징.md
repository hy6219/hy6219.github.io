# Entity 기본 속성(Annotation)

## 01. Entity를 나타내는 어노테이션

`@Entity` 어노테이션을 이용!

▶️ `@Id`라는 기본키를 나타내는 어노테이션도 반드시 필요!

## 02. `@GeneratedValue`

```java
/*
 * Copyright (c) 2008, 2019 Oracle and/or its affiliates. All rights reserved.
 *
 * This program and the accompanying materials are made available under the
 * terms of the Eclipse Public License v. 2.0 which is available at
 * http://www.eclipse.org/legal/epl-2.0,
 * or the Eclipse Distribution License v. 1.0 which is available at
 * http://www.eclipse.org/org/documents/edl-v10.php.
 *
 * SPDX-License-Identifier: EPL-2.0 OR BSD-3-Clause
 */

// Contributors:
//     Linda DeMichiel - 2.1
//     Linda DeMichiel - 2.0

package javax.persistence;

import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.RetentionPolicy.RUNTIME;
import static javax.persistence.GenerationType.AUTO;

/**
 * Provides for the specification of generation strategies for the
 * values of primary keys. 
 *
 * <p> The <code>GeneratedValue</code> annotation
 * may be applied to a primary key property or field of an entity or
 * mapped superclass in conjunction with the {@link Id} annotation.
 * The use of the <code>GeneratedValue</code> annotation is only
 * required to be supported for simple primary keys.  Use of the
 * <code>GeneratedValue</code> annotation is not supported for derived
 * primary keys.
 *
 * <pre>
 *
 *     Example 1:
 *
 *     &#064;Id
 *     &#064;GeneratedValue(strategy=SEQUENCE, generator="CUST_SEQ")
 *     &#064;Column(name="CUST_ID")
 *     public Long getId() { return id; }
 *
 *     Example 2:
 *
 *     &#064;Id
 *     &#064;GeneratedValue(strategy=TABLE, generator="CUST_GEN")
 *     &#064;Column(name="CUST_ID")
 *     Long id;
 * </pre>
 *
 * @see Id
 * @see TableGenerator
 * @see SequenceGenerator
 *
 * @since 1.0
 */
@Target({METHOD, FIELD})
@Retention(RUNTIME)

public @interface GeneratedValue {

    /**
     * (Optional) The primary key generation strategy
     * that the persistence provider must use to
     * generate the annotated entity primary key.
     */
    GenerationType strategy() default AUTO;

    /**
     * (Optional) The name of the primary key generator
     * to use as specified in the {@link SequenceGenerator} 
     * or {@link TableGenerator} annotation.
     * <p> Defaults to the id generator supplied by persistence provider.
     */
    String generator() default "";
}
```

```java
/*
 * Copyright (c) 2008, 2019 Oracle and/or its affiliates. All rights reserved.
 *
 * This program and the accompanying materials are made available under the
 * terms of the Eclipse Public License v. 2.0 which is available at
 * http://www.eclipse.org/legal/epl-2.0,
 * or the Eclipse Distribution License v. 1.0 which is available at
 * http://www.eclipse.org/org/documents/edl-v10.php.
 *
 * SPDX-License-Identifier: EPL-2.0 OR BSD-3-Clause
 */

// Contributors:
//     Linda DeMichiel - 2.1
//     Linda DeMichiel - 2.0

package javax.persistence;

/** 
 * Defines the types of primary key generation strategies. 
 *
 * @see GeneratedValue
 *
 * @since 1.0
 */
public enum GenerationType { 

    /**
     * Indicates that the persistence provider must assign 
     * primary keys for the entity using an underlying 
     * database table to ensure uniqueness.
     */
    TABLE, 

    /**
     * Indicates that the persistence provider must assign 
     * primary keys for the entity using a database sequence.
     */
    SEQUENCE, 

    /**
     * Indicates that the persistence provider must assign 
     * primary keys for the entity using a database identity column.
     */
    IDENTITY, 

    /**
     * Indicates that the persistence provider should pick an 
     * appropriate strategy for the particular database. The 
     * <code>AUTO</code> generation strategy may expect a database 
     * resource to exist, or it may attempt to create one. A vendor 
     * may provide documentation on how to create such resources 
     * in the event that it does not support schema generation 
     * or cannot create the schema resource at runtime.
     */
    AUTO
}
```

`@GeneratedValue`를 추적하다 보면, `GenerationType`이라는 열거형을 확인해볼 수 있는데, 이 열거형은 테이블/시퀀스/Identity/Auto 총 4 가지 타입의 전략을 지원하는 것을 확인해볼 수 있다

- Table 전략은 아이디를 관리할 별도의 테이블을 만들어두고, 테이블에서 아이디값을 계속 추출해서 사용하도록 제공
- SEQUENCE 전략은 오라클에서 자주 사용하는 전략

-H2 db에서도 이 전략을 자주 사용

- Identity 전략은 MySQL에서 자주 사용하는 전략
-auto_increment를 이용해서 기본키값을 증가시킴
- 일반적으로 AUTO전략을 기본값처럼 설정하여 사용하는데, 이렇게 될 경우 각 DB에 맞는 값을 자동으로 연결해주어 사용하게 됨[DB의존성이 없는 장점!]

- AUTO 전략이 DB의존성이 없다는 점에서 장점을 갖고는 있지만, 특정 DB를 연결하여 사용하는 경우가 많기 때문에 전략을 정해주고 사용하는 경우가 많이 있다!

## 03. `@Table`

```java
/*
 * Copyright (c) 2008, 2019 Oracle and/or its affiliates. All rights reserved.
 *
 * This program and the accompanying materials are made available under the
 * terms of the Eclipse Public License v. 2.0 which is available at
 * http://www.eclipse.org/legal/epl-2.0,
 * or the Eclipse Distribution License v. 1.0 which is available at
 * http://www.eclipse.org/org/documents/edl-v10.php.
 *
 * SPDX-License-Identifier: EPL-2.0 OR BSD-3-Clause
 */

// Contributors:
//     Linda DeMichiel - 2.1
//     Linda DeMichiel - 2.0

package javax.persistence;

import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

/**
 * Specifies the primary table for the annotated entity. Additional
 * tables may be specified using {@link SecondaryTable} or {@link
 * SecondaryTables} annotation.
 *
 * <p> If no <code>Table</code> annotation is specified for an entity 
 * class, the default values apply.
 *
 * <pre>
 *    Example:
 *
 *    &#064;Entity
 *    &#064;Table(name="CUST", schema="RECORDS")
 *    public class Customer { ... }
 * </pre>
 *
 * @since 1.0
 */
@Target(TYPE) 
@Retention(RUNTIME)
public @interface Table {

    /**
     * (Optional) The name of the table.
     * <p> Defaults to the entity name.
     */
    String name() default "";

    /** (Optional) The catalog of the table.
     * <p> Defaults to the default catalog.
     */
    String catalog() default "";

    /** (Optional) The schema of the table.
     * <p> Defaults to the default schema for user.
     */
    String schema() default "";

    /**
     * (Optional) Unique constraints that are to be placed on 
     * the table. These are only used if table generation is in 
     * effect. These constraints apply in addition to any constraints 
     * specified by the <code>Column</code> and <code>JoinColumn</code> 
     * annotations and constraints entailed by primary key mappings.
     * <p> Defaults to no additional constraints.
     */
    UniqueConstraint[] uniqueConstraints() default {};

    /**
     * (Optional) Indexes for the table.  These are only used if
     * table generation is in effect.  Note that it is not necessary
     * to specify an index for a primary key, as the primary key
     * index will be created automatically.
     *
     * @since 2.1
     */
    Index[] indexes() default {};
}
```

위의 테이블을 살펴보면, 테이블 이름과 카탈로그, 스키마, 인덱스 등에 대해서 제공을 해주고 있는 것을 살펴볼 수 있다

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
**@Table(name="user")**
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at")
    private Date createdAt;
    @Column(name="updated_at")
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;
}
```

그리고 위에서는 `@Table(name='user')`로 명시해주었는데, 테이블 이름은 자동으로 클래스명과 매칭되기 때문에 `@Table`로 명시해주어도 괜찮다!

다만, `마이그레이션이나 이관작업`을 하는 경우에는, `@Table(name=테이블명)` 처럼 기입해주어야 할 상황도 존재한다!

- 마이그레이션: 데이터나 소프트웨어를 다른 시스템으로 이동시키는 것

이번에는 테이블 이름을 나타내는 name 속성의 값을 "user_legacy"로 변경해보자

물론, data.sql에서 테이블명을 user라고 해두었기 때문에, 분명히 오류는 날 것이다

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user_legacy")
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at")
    private Date createdAt;
    @Column(name="updated_at")
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;
}
```

```java
create table user_legacy (
       id bigint not null,
        active boolean,
        created_at timestamp,
        email varchar(255),
        name varchar(255),
        updated_at timestamp,
        primary key (id)
    )
```

하지만, 쿼리에서 테이블을 생성할 때 user_legacy로 실행되었음을 확인해볼 수 있다

🌟 하지만, 일반적으로 테이블과 엔티티는 1:1 관계가 되어 이름이 동일한 것이 좋다!

### 03-1. `@Table` 어노테이션에서 인덱스와 UNIQUE 제약조건 붙여주기- `indexes={@Index(columnList="컬럼명")}` , `uniqueConstraints={@UniqueConstraint(columnNames={"컬럼명"})}`

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
**@Table(indexes = {@Index(columnList = "name")},uniqueConstraints = {@UniqueConstraint(columnNames = {"email"})})**
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at")
    private Date createdAt;
    @Column(name="updated_at")
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;
}
```

위에서 잠깐 언급했던 것처럼 Table 어노테이션 내에서는 인덱스와 UNIQUE 제약조건을 걸어줄 수 있다!

이는 각각 `indexes={@Index(columnList="컬럼명")}` , `uniqueConstraints={@UniqueConstraint(columnNames={"컬럼명"})}` 을 이용해서 위와 같이 적용가능하다!

```java
Hibernate: 
    
    drop table if exists address CASCADE 
Hibernate: 
    
    drop table if exists user CASCADE 
Hibernate: 
    
    drop sequence if exists hibernate_sequence
Hibernate: create sequence hibernate_sequence start with 1 increment by 1
Hibernate: 
    
    create table address (
       id bigint not null,
        primary key (id)
    )
Hibernate: 
    
    create table user (
       id bigint not null,
        active boolean,
        created_at timestamp,
        email varchar(255),
        name varchar(255),
        updated_at timestamp,
        primary key (id)
    )
**Hibernate: create index IDXgj2fy3dcix7ph7k8684gka40c on user (name)
Hibernate: 
    
    alter table user 
       add constraint UKob8kqyqqgmefl0aco34akdtpe unique (email)**
```

그리고 테스트를 실행해보면 DDL에서 `테이블이나 시퀀스가 존재하면 삭제하는 것을 먼저 수행하고 테이블을 만드는 형태를 확인해볼 수 있고

인덱스를 만들고, UNIQUE 제약조건을 추가하는 것을 살펴볼 수 있다!

하지만 이러한 인덱스나 제약조건에 대한 적용사항은 실제 DB에 저장되는 것과는 다를 수 있다! 일반적으로 많이 사용되는 select, update, insert, delete 쿼리 실행 시에는 어떤 효력도 주지 않는다! 

따라서 인덱스나 제약조건은 DB에 맡기고 엔티티에 표기하지 않는 경우가 보다 많다!

## 04. `@Column`

객체 필드에 각 컬럼 속성을 지정

- DB는 그대로 유지하고 웹 어플리케이션을 새로 리빌딩하는 경우 등에 적합

예전에는 예를 들어서 createdAt과 같은 필드를 모음을 최대한 적게 쓰기 위해서 crtdat와 같은 형태로 사용했었다. 하지만 이는 가독성이 떨어지기 때문에 이 경우에 Column을 적용하면 좋다![A]

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(indexes = {@Index(columnList = "name")},uniqueConstraints = {@UniqueConstraint(columnNames = {"email"})})
public class User {
    @Id
    @GeneratedValue
    **@Column(name="id")**
    private Long id;
    @NonNull
    **@Column(name="name")**
    private String name;
    @NonNull
    **@Column(name="email")**
    private String email;
    **@Column(name="created_at")**
    private Date createdAt;
    **@Column(name="updated_at")**
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    **@Column(name="active")**
    private boolean active;
}
```

👉[A] 과거에 사용하던 데이터베이스 필드명에 자바 필드를 매칭시켜줄 때, 가독성을 UP!

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(indexes = {@Index(columnList = "name")},uniqueConstraints = {@UniqueConstraint(columnNames = {"email"})})
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    **@Column(name="crtdat")
    private Date createdAt;**
    @Column(name="updated_at")
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;
}
```

그러면 지금은 당연히 오류가 날 수밖에 없지만(data.sql에 insert할 컬럼명을 createdAt으로 적어두었기 때문), DDL만 확인해본다면, crtdat컬럼이 생긴 것을 확인해볼 수 있다

```java
Hibernate: 
    
    create table user (
       id bigint not null,
        active boolean,
        **crtdat timestamp,**
        email varchar(255),
        name varchar(255),
        updated_at timestamp,
        primary key (id)
    )
```

### 04-1. `@Column-nullable속성`

- 일반적인 조회 기능 사용 시에는 ,사전에 걸러주는 validation 역할을 수행하지 않음
- nullable은 기본적으로 true로 되어있음
- NOT NULL 제약조건을 만들어주기 위한 것
- `nullable=false ↔️ NOT NULL 제약조건 추가`
- `nullable=true ↔️ 비어있어도 된다 ↔️NOT NULL 제약조건 없음`
- nullable은 체크해야할 속성으로써 추후 테스트 등에서 유용하게 사용되어서 자주 사용된다!

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(indexes = {@Index(columnList = "name")},uniqueConstraints = {@UniqueConstraint(columnNames = {"email"})})
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="crtdat")
    private Date createdAt;
    **@Column(name="updated_at",nullable = false)**
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;
}
```

```java
Hibernate: 
    
    create table user (
       id bigint not null,
        active boolean,
        crtdat timestamp,
        email varchar(255),
        name varchar(255),
        **updated_at timestamp not null**,
        primary key (id)
    )
```

그러면, `nullable=false를 지정해준 필드에 not null이 붙은 것을 알 수 있다`

---

```java
/*
 * Copyright (c) 2008, 2019 Oracle and/or its affiliates. All rights reserved.
 *
 * This program and the accompanying materials are made available under the
 * terms of the Eclipse Public License v. 2.0 which is available at
 * http://www.eclipse.org/legal/epl-2.0,
 * or the Eclipse Distribution License v. 1.0 which is available at
 * http://www.eclipse.org/org/documents/edl-v10.php.
 *
 * SPDX-License-Identifier: EPL-2.0 OR BSD-3-Clause
 */

// Contributors:
//     Linda DeMichiel - 2.1
//     Linda DeMichiel - 2.0

package javax.persistence;

import java.lang.annotation.Target;
import java.lang.annotation.Retention;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

/**
 * Specifies the mapped column for a persistent property or field.
 * If no <code>Column</code> annotation is specified, the default values apply.
 *
 * <blockquote><pre>
 *    Example 1:
 *
 *    &#064;Column(name="DESC", nullable=false, length=512)
 *    public String getDescription() { return description; }
 *
 *    Example 2:
 *
 *    &#064;Column(name="DESC",
 *            columnDefinition="CLOB NOT NULL",
 *            table="EMP_DETAIL")
 *    &#064;Lob
 *    public String getDescription() { return description; }
 *
 *    Example 3:
 *
 *    &#064;Column(name="ORDER_COST", updatable=false, precision=12, scale=2)
 *    public BigDecimal getCost() { return cost; }
 *
 * </pre></blockquote>
 *
 *
 * @since 1.0
 */ 
@Target({METHOD, FIELD}) 
@Retention(RUNTIME)
public @interface Column {

    /**
     * (Optional) The name of the column. Defaults to 
     * the property or field name.
     */
    String name() default "";

    /**
     * (Optional) Whether the column is a unique key.  This is a 
     * shortcut for the <code>UniqueConstraint</code> annotation at the table 
     * level and is useful for when the unique key constraint 
     * corresponds to only a single column. This constraint applies 
     * in addition to any constraint entailed by primary key mapping and 
     * to constraints specified at the table level.
     */
    boolean unique() default false;

    /**
     * (Optional) Whether the database column is nullable.
     */
    boolean nullable() default true;

    /**
     * (Optional) Whether the column is included in SQL INSERT 
     * statements generated by the persistence provider.
     */
    boolean insertable() default true;

    /**
     * (Optional) Whether the column is included in SQL UPDATE 
     * statements generated by the persistence provider.
     */
    boolean updatable() default true;

    /**
     * (Optional) The SQL fragment that is used when 
     * generating the DDL for the column.
     * <p> Defaults to the generated SQL to create a
     * column of the inferred type.
     */
    String columnDefinition() default "";

    /**
     * (Optional) The name of the table that contains the column. 
     * If absent the column is assumed to be in the primary table.
     */
    String table() default "";

    /**
     * (Optional) The column length. (Applies only if a
     * string-valued column is used.)
     */
    int length() default 255;

    /**
     * (Optional) The precision for a decimal (exact numeric) 
     * column. (Applies only if a decimal column is used.)
     * Value must be set by developer if used when generating 
     * the DDL for the column.
     */
    int precision() default 0;

    /**
     * (Optional) The scale for a decimal (exact numeric) column. 
     * (Applies only if a decimal column is used.)
     */
    int scale() default 0;
}
```

위는 Column 어노테이션에 대한 부분인데, 

- unique는 unique 제약조건에 대한 부분이다

-Table에서는 복합 컬럼에 대해 unique 제약조건을 걸 수 있는 uniqueConstraints가 있다면,

column에는 단독적으로 각 필드에 걸 수 있도록 되어있다!

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(indexes = {@Index(columnList = "name")},**uniqueConstraints = {@UniqueConstraint(columnNames = {"email"})**})
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    **@Column(name="name",unique = true)
    private String name;**
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="crtdat")
    private Date createdAt;
    @Column(name="updated_at",nullable = false)
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;
}
```

```java
create table user (
       id bigint not null,
        active boolean,
        crtdat timestamp,
        email varchar(255),
        name varchar(255),
        updated_at timestamp not null,
        primary key (id)
    )
Hibernate: create index IDXgj2fy3dcix7ph7k8684gka40c on user (name)
Hibernate: 
    
    alter table user 
       add constraint UKob8kqyqqgmefl0aco34akdtpe unique (email)
Hibernate: 
    
    **alter table user 
       add constraint UK_gj2fy3dcix7ph7k8684gka40c unique (name)**
```

- length()는 해당 필드의 길이를 나타내는데,  기본값이 255이라서 varchar(255)로 표기되어 있는 것을 볼 수 있다

이메일 필드의 길이를 4000으로 늘려보자

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(indexes = {@Index(columnList = "name")},uniqueConstraints = {@UniqueConstraint(columnNames = {"email"})})
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name",unique = true)
    private String name;
    @NonNull
    @Column(name="email"**,length = 4000**)
    private String email;
    @Column(name="crtdat")
    private Date createdAt;
    @Column(name="updated_at",nullable = false)
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;
}
```

```java
create table user (
       id bigint not null,
        active boolean,
        crtdat timestamp,
        **email varchar(4000)**,
        name varchar(255),
        updated_at timestamp not null,
        primary key (id)
    )
```

그러면 이제 이메일 필드의 길이는 더이상 255가 아니라 4000이 되었음을 확인해볼 수 있다

- insertable, updatable: 다른 속성들과 다르게 DDL이 아닌 일반적인 DML 쿼리에 영향을 끼침

먼저 User 객체를 다시 정리해보고, 아래와 같이 테스트해보자

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(indexes = {@Index(columnList = "name")},uniqueConstraints = {@UniqueConstraint(columnNames = {"email"})})
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email",length = 4000)
    private String email;
    @Column(name="created_at")
    private Date createdAt;
    @Column(name="updated_at")
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;
}
```

```java
@Test
    public void dmlAccess(){
        User user=new User();

        user.setName("martin");
        user.setEmail("martin2@fastcampus.com");

        userRepository.save(user);//insert

        User user2=userRepository.findById(1L).orElseThrow(RuntimeException::new);
        user2.setEmail("update_1l_id@fastcampus.com");

        userRepository.save(user2);//update
    }
```

그러면 당연히, 처음에는 insertable과 updatable의 기본값이 true로 되어있어서 insert, update가 가능하여 아래와 같은 쿼리를 확인해볼 수 있다

```java
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user
        (active, created_at, email, name, updated_at, id) 
    values
        (?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.name as name5_1_0_,
        user0_.updated_at as updated_6_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.name as name5_1_0_,
        user0_.updated_at as updated_6_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        active=?,
        created_at=?,
        email=?,
        name=?,
        updated_at=? 
    where
        id=?
```

🌟 이번에는 created_at 필드에 대해서 update되지 못하도록 `updatable=false`를 설정해주고, updated_at 필드에 대해서 insert되지 못하도록 `insertable=false`를 설정해주자

```java
package com.example.jpa_sort_practice.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    **@Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;**

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;
}
```

그리고 결과를 레코드들의 묶음으로 보기 위해서 출력을 해주면

```java
@Test
    public void dmlAccess(){
        User user=new User();

        user.setName("martin");
        user.setEmail("martin2@fastcampus.com");

        userRepository.save(user);//insert

        User user2=userRepository.findById(1L).orElseThrow(RuntimeException::new);
        user2.setEmail("update_1l_id@fastcampus.com");

        userRepository.save(user2);//update

        userRepository.findAll().forEach(System.out::println);
    }
```

```java
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    **insert 
    into
        user
        (active, created_at, email, name, id) 
    values
        (?, ?, ?, ?, ?)**
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.name as name5_1_0_,
        user0_.updated_at as updated_6_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.name as name5_1_0_,
        user0_.updated_at as updated_6_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    **update
        user 
    set
        active=?,
        email=?,
        name=?,
        updated_at=? 
    where
        id=?**
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.updated_at as updated_6_1_ 
    from
        user user0_
User(id=1, name=martin, email=update_1l_id@fastcampus.com, createdAt=2021-08-19 14:32:55.101, updatedAt=2021-08-19 14:32:55.101, active=true)
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-19 14:32:55.114, updatedAt=2021-08-19 14:32:55.114, active=true)
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-19 14:32:55.114, updatedAt=2021-08-19 14:32:55.114, active=false)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-19 14:32:55.114, updatedAt=2021-08-19 14:32:55.114, active=false)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-19 14:32:55.115, updatedAt=2021-08-19 14:32:55.115, active=true)
User(id=6, name=martin, email=martin2@fastcampus.com, createdAt=null, updatedAt=null, active=false)
```

id=6, 즉 새로 삽입한 martin은 updated_at 이 insertable=false이기 때문에 값에서도, 쿼리에서도 빠진 것을 확인해볼 수 있고

id=1인 martin은 created_at이 updatable=false라서 쿼리에서 update 시 created_at이 빠진 것을 확인해볼 수 있다!

🌟 엔티티는 데이터에 대한 객체이기 때문에 DB 레코드의 값을 그대로 반영한다! 하지만, 객체이기 때문에 별도의 데이터를 갖고 싶은 니즈도 생기기 마련인데, 이를 위해 존재하는 어노테이션이 바로 `@Transient` 이다!

## 05. `@Transient`

추가적으로, testData라는 필드를 User 객체에 추가해보자

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    **private String testData;**
}
```

```java
Hibernate: 
    
    create table user (
       id bigint not null,
        active boolean,
        created_at timestamp,
        email varchar(255),
        name varchar(255),
        **test_data varchar(255),**
        updated_at timestamp,
        primary key (id)
    )
(중략)
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user
        (active, created_at, email, name, test_data, id) 
    values
        (?, ?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.name as name5_1_0_,
        user0_.test_data as test_dat6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.name as name5_1_0_,
        user0_.test_data as test_dat6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        active=?,
        email=?,
        name=?,
        test_data=?,
        updated_at=? 
    where
        id=?
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.test_data as test_dat6_1_,
        user0_.updated_at as updated_7_1_ 
    from
        user user0_
User(id=1, name=martin, email=update_1l_id@fastcampus.com, createdAt=2021-08-19 14:43:22.501, updatedAt=2021-08-19 14:43:22.501, active=true, testData=null)
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-19 14:43:22.517, updatedAt=2021-08-19 14:43:22.517, active=true, testData=null)
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-19 14:43:22.518, updatedAt=2021-08-19 14:43:22.518, active=false, testData=null)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-19 14:43:22.518, updatedAt=2021-08-19 14:43:22.518, active=false, testData=null)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-19 14:43:22.519, updatedAt=2021-08-19 14:43:22.519, active=true, testData=null)
User(id=6, name=martin, email=martin2@fastcampus.com, createdAt=null, updatedAt=null, active=false, testData=null)
```

그러면, test_data라는 필드가 생겨나고,  insert와 update 시 해당 필드가 노출되는 것을 확인해볼 수 있다

하지만 이 `testData는 DB에는 반영하지 않고 객체에서만 사용하고 싶은 오브젝트의 속성일 수도 있다`

📌이때 `@Transient`를 붙여주면, `이 어노테이션이 붙은 필드는 DB에 반영되지 않음을 나타낼 수 있다`

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    **@Transient
    private String testData;**
}
```

```java
create table user (
       id bigint not null,
        active boolean,
        created_at timestamp,
        email varchar(255),
        name varchar(255),
        updated_at timestamp,
        primary key (id)
    )
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    insert 
    into
        user
        (active, created_at, email, name, id) 
    values
        (?, ?, ?, ?, ?)
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.name as name5_1_0_,
        user0_.updated_at as updated_6_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.name as name5_1_0_,
        user0_.updated_at as updated_6_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        active=?,
        email=?,
        name=?,
        updated_at=? 
    where
        id=?
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.name as name5_1_,
        user0_.updated_at as updated_6_1_ 
    from
        user user0_
```

그러면 앞서 확인되었던 testData가 `DB 필드에는 전혀 반영이 되지 않는 것(DDL, DML에 모두 반영x`▶️ `영속성x)을 확인해볼 수 있다` 그리고 이러면서 `해당 엔티티와 생명주기를 같이하게 된다`

## 🌟 06. Enum에 대한 처리

먼저 아래와 같이 성별에 대한 enum을 만들어보자

```java
package com.example.jpa_entity.domain;

public enum Gender {
    MALE,
    FEMALE
}
```

그리고 이러한 enum을 User의 필드로써 넣어주도록 하자

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    **private Gender gender;**
}
```

그리고 아이디가 1인 user를 찾아서 성별을 MALE로 설정하는 update를 실행해주자

```java
@Test
    public void enumTest(){
        User user=userRepository.findById(1L).orElseThrow(RuntimeException::new);

        user.setGender(Gender.MALE);
        userRepository.save(user);//update

        userRepository.findAll().forEach(System.out::println);

    }
```

그러면 아래처럼  아이디가 1인 사용자만 gender값으로 MALE이 들어간 것을 확인해볼 수 있다

```java
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        active=?,
        email=?,
        gender=?,
        name=?,
        updated_at=? 
    where
        id=?
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.gender as gender5_1_,
        user0_.name as name6_1_,
        user0_.updated_at as updated_7_1_ 
    from
        user user0_
**User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-20 14:04:02.216, updatedAt=2021-08-20 14:04:02.216, active=true, testData=null, gender=MALE)**
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-20 14:04:02.232, updatedAt=2021-08-20 14:04:02.232, active=true, testData=null, gender=null)
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-20 14:04:02.232, updatedAt=2021-08-20 14:04:02.232, active=false, testData=null, gender=null)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-20 14:04:02.233, updatedAt=2021-08-20 14:04:02.233, active=false, testData=null, gender=null)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-20 14:04:02.233, updatedAt=2021-08-20 14:04:02.233, active=true, testData=null, gender=null)
```

enum을 사용한 데이터를 다루는 것을 보다 이해하기 위헤서 아직 배우지는 않았지만 네이티브 쿼리를 이용해보자

```java
package com.example.jpa_entity.repository;

import com.example.jpa_entity.domain.User;
import org.springframework.data.domain.Sort;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;

import java.util.List;
import java.util.Map;

public interface UserRepository extends JpaRepository<User,Long> {

    List<User> findTop1ByName(String name);
    List<User> findTop1ByNameOrderByIdDesc(String name);

    List<User> findFirstByNameOrderByIdDescEmailAsc(String name);
    List<User> findFirstByName(String name, Sort sort);

    **@Query(value="select * from user limit 1;", nativeQuery = true)
    Map<String, Object> findRawRecord();**
}
```

그리고 간단하게 테스트해보자

```java
@Test
    public void enumTest(){
        User user=userRepository.findById(1L).orElseThrow(RuntimeException::new);

        user.setGender(Gender.MALE);
        userRepository.save(user);//update

        userRepository.findAll().forEach(System.out::println);

        **System.out.println(userRepository.findRawRecord().get("gender"));**
    }
```

```java
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        active=?,
        email=?,
        gender=?,
        name=?,
        updated_at=? 
    where
        id=?
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.gender as gender5_1_,
        user0_.name as name6_1_,
        user0_.updated_at as updated_7_1_ 
    from
        user user0_
User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-20 14:09:55.478, updatedAt=2021-08-20 14:09:55.478, active=true, testData=null, gender=MALE)
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-20 14:09:55.494, updatedAt=2021-08-20 14:09:55.494, active=true, testData=null, gender=null)
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-20 14:09:55.494, updatedAt=2021-08-20 14:09:55.494, active=false, testData=null, gender=null)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-20 14:09:55.495, updatedAt=2021-08-20 14:09:55.495, active=false, testData=null, gender=null)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-20 14:09:55.495, updatedAt=2021-08-20 14:09:55.495, active=true, testData=null, gender=null)
Hibernate: 
    select
        * 
    from
        user limit 1;
**0**
```

그러면 `@Query` 안의 내용으로 보아 USER 테이블에서 1개 데이터를 가져오는 내용으로 추측된다

그리고 get("gender")를 했을 때에 대한 결과를 바라보는 것이 바로 위의 테스트이다

하지만 0이 출력되는 것을 확인해볼 수 있다!

하지만 enum의 내용을 아래처럼 변경하면 1이 출력되는 것을 확인해볼 수 있다.

```java
package com.example.jpa_entity.domain;

public enum Gender {
    FEMALE,
    MALE
}
```

```java
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        active=?,
        email=?,
        gender=?,
        name=?,
        updated_at=? 
    where
        id=?
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.gender as gender5_1_,
        user0_.name as name6_1_,
        user0_.updated_at as updated_7_1_ 
    from
        user user0_
User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-20 14:15:04.59, updatedAt=2021-08-20 14:15:04.59, active=true, testData=null, gender=MALE)
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-20 14:15:04.602, updatedAt=2021-08-20 14:15:04.602, active=true, testData=null, gender=null)
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-20 14:15:04.603, updatedAt=2021-08-20 14:15:04.603, active=false, testData=null, gender=null)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-20 14:15:04.603, updatedAt=2021-08-20 14:15:04.603, active=false, testData=null, gender=null)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-20 14:15:04.604, updatedAt=2021-08-20 14:15:04.604, active=true, testData=null, gender=null)
Hibernate: 
    select
        * 
    from
        user limit 1;
**1**
```

User의 Gender 필드에 `@Enumerated`를 붙여보자

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    **@Enumerated
    private Gender gender;**
}
```

그리고 Enumerated를 잘 보면 Enum의 타입에 대한 기본값이 ORDINAL 즉 서수로 되어 있는 것을 확인해볼 수 있다

(이외에도 String이 가능)

🌟 즉, 첫번째에 위치한 값이 0이고, 그 다음이 1 이런 순서로 매겨져서 DB에 저장되기 때문에 그런것이었다! 이는 테스트로는 확인하기 어렵다(MALE, FEMALE로 출력되기 때문)

하지만 지금 테스트해본 것처럼 저장 순서가 바뀌거나, 혹은 이외의 상황으로 값을 추가하면 잠재적인 버그가 일어날 수 있기 때문에 ORDINAL로 다루는 것은 바람직하지 못하다

🌟 따라서 반드시 Enum을 필드로 사용하려면 `@Enumerated(value=EnumType.String)`을 설정하고 사용해주자!!

```java
package com.example.jpa_entity.domain;

import lombok.*;

import javax.persistence.*;
import java.util.Date;

@Data
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Entity
@Table(name="user")
public class User {
    @Id
    @GeneratedValue
    @Column(name="id")
    private Long id;
    @NonNull
    @Column(name="name")
    private String name;
    @NonNull
    @Column(name="email")
    private String email;
    @Column(name="created_at",updatable = false)
    private Date createdAt;
    @Column(name="updated_at",insertable = false)
    private Date updatedAt;

    //IsNotEmpty 확인용
    //@OneToMany(fetch=FetchType.EAGER)
    //private List<Address> addresses;

    @Column(name="active")
    private boolean active;

    @Transient
    private String testData;

    //enum
    **@Enumerated(EnumType.STRING)
    private Gender gender;**
}
```

```java
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    select
        user0_.id as id1_1_0_,
        user0_.active as active2_1_0_,
        user0_.created_at as created_3_1_0_,
        user0_.email as email4_1_0_,
        user0_.gender as gender5_1_0_,
        user0_.name as name6_1_0_,
        user0_.updated_at as updated_7_1_0_ 
    from
        user user0_ 
    where
        user0_.id=?
Hibernate: 
    update
        user 
    set
        active=?,
        email=?,
        gender=?,
        name=?,
        updated_at=? 
    where
        id=?
Hibernate: 
    select
        user0_.id as id1_1_,
        user0_.active as active2_1_,
        user0_.created_at as created_3_1_,
        user0_.email as email4_1_,
        user0_.gender as gender5_1_,
        user0_.name as name6_1_,
        user0_.updated_at as updated_7_1_ 
    from
        user user0_
User(id=1, name=martin, email=martin@fastcampus.com, createdAt=2021-08-20 14:21:14.97, updatedAt=2021-08-20 14:21:14.97, active=true, testData=null, gender=MALE)
User(id=2, name=dennis, email=dennis@fastcampus.com, createdAt=2021-08-20 14:21:14.981, updatedAt=2021-08-20 14:21:14.981, active=true, testData=null, gender=null)
User(id=3, name=sophia, email=sophia@slowcampus.com, createdAt=2021-08-20 14:21:14.982, updatedAt=2021-08-20 14:21:14.982, active=false, testData=null, gender=null)
User(id=4, name=james, email=james@slowcampus.com, createdAt=2021-08-20 14:21:14.983, updatedAt=2021-08-20 14:21:14.983, active=false, testData=null, gender=null)
User(id=5, name=martin, email=martin@another.com, createdAt=2021-08-20 14:21:14.983, updatedAt=2021-08-20 14:21:14.983, active=true, testData=null, gender=null)
Hibernate: 
    select
        * 
    from
        user limit 1;
📌**MALE**📌
```

그러면 더이상 0이나 1로 저장 순서에 따라 바뀌지 않고, MALE로 DB에 저장되었음을 확인해볼 수 있다!

🌟 추후 DB 장애나 마이그레이션 시 문제를 방지하기 위해서 Enum 사용에 주의하자!