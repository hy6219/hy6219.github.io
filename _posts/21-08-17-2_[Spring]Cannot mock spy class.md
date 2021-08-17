# Cannot mock/spy class org.springframework.test.web.servlet.MockMvc Mockito cannot mock/spy because : - final class

오늘 단위 테스트를 진행하던 중 아래와 같은 실수를 해서 발생했는데, gradle에서 아래와 같이 의존성을 추가해주어야 하는 경우가 있는 것을 확인했다!

```java
@MockBean
private MockMvc mockMvc;
```

```groovy
testImplementation 'org.mockito:mockito-inline:2.13.0'
```

https://withhamit.tistory.com/171