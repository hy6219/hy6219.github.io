# 비동기 처리하기

1. [Application.java](http://application.java) 부분에 @EnableAsync 를 붙여주자

```java
package com.example.async;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableAsync;

@SpringBootApplication
**@EnableAsync**
public class AsyncApplication {

    public static void main(String[] args) {
        SpringApplication.run(AsyncApplication.class, args);
    }

}
```

2.간단하게, 실질적으로 비즈니스 로직을 담당하는 서비스 부분(@Service)에서 비동기를 유발시키도록 특정 메서드의 윗부분에 @Async 어노테이션을 붙여주자

```java
package com.example.async.service;

import lombok.extern.slf4j.Slf4j;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;

@Service
@Slf4j
public class AsyncService {

    **@Async**
    public void hello(){
        for(int i = 0 ; i < 10; i++){
            try {
                Thread.sleep(2000);
                log.info("thread sleep---#{}",i);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
			log.info("service end");
    }

}
```

3.그리고 Rest Api Controller에서 이 비동기를 유발하는 메서드를 부르도록 하자

```java
package com.example.async.controller;

import com.example.async.service.AsyncService;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
**@RequiredArgsConstructor**
public class RestApiController {

    private final AsyncService asyncService;

    @GetMapping("/hello")
    public String hello(){
        System.out.println("hello start in rest");
        asyncService.hello();
        System.out.println("hello end in rest");
        return "hello";
    }

}
```

지금 정리해보면, [http://localhost:8089/api/hello](http://localhost:8089/api/hello) 로 요청을 보내면

hello()메서드로 인해서 작동되어야 할 기능들은 아래와 같다

- System.out.println("hello start in rest");
- Service단의 hello 메서드
-for 루프를 돌면서 2초 간격으로 로깅
-for루프 후 "service end" 로그 찍기
- System.out.println("hello end in rest");
- plain text response로 "hello " 내려주기

원래 비동기가 아니었다면, 지금 적은 순서대로 수행되어야 하지만 @Async, @EnableAsync로 인해서 동시에 작업이 이루어 지면서 작업이 종료되는 대로 결과가 반환되는 것을 확인해볼 수 있다

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20async/Spring%20async%20%EC%B2%98%EB%A6%AC.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20async/Spring%20async%20%EC%B2%98%EB%A6%AC.gif?raw=true)

위의 경우 response가 먼저 확인되었고, 그 후 콘솔에 "hello start in rest"와 "hello end in rest"가 순서대로 찍힌 후에, service단의 hello 메서드가 찍히는 것을 확인해볼 수 있었다

이를 조금 더 명확하게 살펴보기 위해서 컨트롤러에서도 로깅을 해보자

```java
package com.example.async.controller;

import com.example.async.service.AsyncService;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/api")
@RequiredArgsConstructor
public class RestApiController {

    private final AsyncService asyncService;

    @GetMapping("/hello")
    public String hello(){
        log.info("hello start in rest");
        asyncService.hello();
        log.info("hello end in rest");
        return "hello";
    }

}
```

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20async/Spring%20async-logging.PNG?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20async/Spring%20async-logging.PNG?raw=true)

그러면 이를 통해서 서로 다른 스레드에서 작업이 진행되고 있음을 확인해볼 수 있다

---

## Async 작업에 따른 response 처리

1.먼저 [Application.java](http://application.java) 에서 EnableAsync를 그대로 유지해주자

```java
package com.example.async;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableAsync;

@SpringBootApplication
@EnableAsync
public class AsyncApplication {

    public static void main(String[] args) {
        SpringApplication.run(AsyncApplication.class, args);
    }

}
```

2.서비스에서는 hello에서 Async를 지우고,CompletableFuture 형을 반환하는 run()메서드를 만들자! 이때 run 메서드에서는 AsyncResult(hello()),completable()을 반환한다. 그리고 run에 Async를 붙이자

- CompleatableFuture: 비동기 작업시 2개 이상의 스레드에 대한 정보를 받을 수 있는 클래스
- AsyncResult : 비동기 작업 결과를 Future로 반환
- public CompletableFuture<V> completable() : AsyncResult를 CompletableFuture로 만들기

[CompletableFuture (Java Platform SE 8 )](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)

[AsyncResult (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/AsyncResult.html)

```java
package com.example.async.service;

import lombok.extern.slf4j.Slf4j;
import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.AsyncResult;
import org.springframework.stereotype.Service;

import java.util.concurrent.CompletableFuture;

@Service
@Slf4j
public class AsyncService {

	**@Async**
    **public CompletableFuture run(){
        return new AsyncResult(hello()).completable();
    }**
    //@Async
    public String hello(){
        for(int i = 0 ; i < 10; i++){
            try {
                Thread.sleep(2000);
                log.info("thread sleep---#{}",i);

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        log.info("service end");
        return "hello";
    }

}
```

3,RestApiController에서는 내부적으로 서비스의 run()메서드를 hello()메서드 내부에서 부르도록 하자

```java
package com.example.async.controller;

import com.example.async.service.AsyncService;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.concurrent.CompletableFuture;

@Slf4j
@RestController
@RequestMapping("/api")
@RequiredArgsConstructor
public class RestApiController {

    private final AsyncService asyncService;

    @GetMapping("/hello")
    public CompletableFuture hello(){
//        log.info("hello start in rest");
//        asyncService.hello();
//        log.info("hello end in rest");
//        return "hello";
				log.info("completable future");
        **return asyncService.run();**
    }

}
```

**CompletableFuture**는 다른 스레드에서 해당 작업을 수행할 수 있도록 지원해준다!

![https://github.com/hy6219/TIL/blob/main/Spring/Spring%20async/CompletableFuture%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%B3%84%EB%8F%84%EC%9D%98%20%EC%8A%A4%EB%A0%88%EB%93%9C%EC%97%90%EC%84%9C%EC%9D%98%20%EC%9E%91%EC%97%85%EC%B2%98%EB%A6%AC.gif?raw=true](https://github.com/hy6219/TIL/blob/main/Spring/Spring%20async/CompletableFuture%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%B3%84%EB%8F%84%EC%9D%98%20%EC%8A%A4%EB%A0%88%EB%93%9C%EC%97%90%EC%84%9C%EC%9D%98%20%EC%9E%91%EC%97%85%EC%B2%98%EB%A6%AC.gif?raw=true)

위의 경우, log.info("completable future");의 스레드와 " Async와 CompletableFuture가 붙은 메서드"의 스레드가 서로 다르게 돌아가고 있음을 확인해볼 수 있다

🌟 비동기를 적용하기 알맞은 예시 🌟

- 여러개의 api를 전송 후 그 결과를 조인할 경우

✴️ 위의 경우는 기본적으로 스프링에서 정한 스레드 최대 범주(약 최대 8개)에서 비동기 작업을 수행 가능 ▶️ 하지만, 이 부분이 한계가 될 수 있다! 직접 스레드를 만들어 지정해보자

🌟 Async : AOP 기반이기 때문에 프록시 패턴을 따른다!

🌟 하나의 메서드에서 Async를 붙인 상황에서 내부적으로 메서드를 호출하면 그 내부 메서드에는 Async가 붙지 않음

## (심화)쓰레드를 직접 만들어서 해당 쓰레드를 이용하도록 하기

- 이 방법은 경험이 쌓이면서 접근해보고, 웬만하면 스프링 웹 MVC를 이용하는 것을 권장[DB와 연동시, MySQL의 경우 Spring 웹 flux와 같이 사용하는 것을 권장하지만, 실상 DB는 동기방식으로 진행하기 때문에 async가 소용없음]

📌 ThreadPoolTaskExecutor

- 쓰레드풀을 이용해서 멀티 쓰레드 구현을 할 수 있도록 도와줌

[ThreadPoolTaskExecutor (Spring Framework 5.3.9 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html)

- setCorePoolSize 메서드 - 동시에 실행시킬 수 있는 쓰레드 갯수 설정
- setMaxPoolSize 메서드 - 쓰레드 풀의 최대 사이즈
- setQueueCapacity 메서드 : 쓰레드 corePoolSize가 다 차면 큐의 용량만큼 사용하고, 큐도 다 사용하면 corePoolSize로 지정한 갯수만큼 corePoolSize가 늘어나서 사용하고, 또 다 사용하면 큐 용량만큼 이용 반복
- setThreadNamePrefix 메서드 : 쓰레드 이름 접미사 설정

쓰레드를 만들 클래스를 AppConfig로 하고, 빈 객체로 등록하자

- 쓰레드의 이름 접미사는 "Async-"로 설정

```java
package com.example.async.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

@Configuration
public class AppConfig {

    @Bean("async-thread")
    public Executor asyncThread(){
        **ThreadPoolTaskExecutor threadPoolTaskExecutor=
                new ThreadPoolTaskExecutor();

        threadPoolTaskExecutor.setMaxPoolSize(100);
        threadPoolTaskExecutor.setCorePoolSize(10);//core pool 10개를 다 쓰게 되면 큐로 넘어감
        //그러다가 큐도 다 차면, core는 인자값으로 넣어준 크기만큼 또 생김
        threadPoolTaskExecutor.setQueueCapacity(10);
        threadPoolTaskExecutor.setThreadNamePrefix("Asnyc-");
        return threadPoolTaskExecutor;**
    }
}
```

그리고 기존의 서비스의 비동기 메서드에 대해서 Async("빈 객체명")으로 수정해주자

```java
package com.example.async.service;

import lombok.extern.slf4j.Slf4j;
import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.AsyncResult;
import org.springframework.stereotype.Service;

import java.util.concurrent.CompletableFuture;

@Service
@Slf4j
public class AsyncService {

    **@Async("async-thread")**
    public CompletableFuture run(){
        return new AsyncResult(hello()).completable();
    }
    //@Async
    public String hello(){
        for(int i = 0 ; i < 10; i++){
            try {
                Thread.sleep(2000);
                log.info("thread sleep---#{}",i);

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        log.info("service end");
        return "hello";
    }

}
```

```java
2021-08-04 02:07:45.407  INFO 40524 --- [nio-8089-exec-1] c.e.async.controller.RestApiController   : completable future
2021-08-04 02:07:47.426  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#0
2021-08-04 02:07:49.437  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#1
2021-08-04 02:07:51.440  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#2
2021-08-04 02:07:53.447  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#3
2021-08-04 02:07:55.457  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#4
2021-08-04 02:07:57.464  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#5
2021-08-04 02:07:59.472  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#6
2021-08-04 02:08:01.478  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#7
2021-08-04 02:08:03.481  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#8
2021-08-04 02:08:05.488  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : thread sleep---#9
2021-08-04 02:08:05.488  INFO 40524 --- [        Asnyc-1] com.example.async.service.AsyncService   : service end
```

그러면 더이상 Task-1이 아닌 지정했던 접미사를 이용한 Async-1이라는 스레드가 확인된다!