# DELETE API 작성하기

- 리소스 삭제(D)
- 멱등성- 삭제되었던 데이터였든지, 현재 있는 데이터던지 삭제되기 때문
- 안정성은 없음
- Path Variable, Query Parameter 이용
- Data Body는 넣을 수는 있지만 , 권장하지 않음(고유한 값으로 삭제하기 때문에 Data Body는 필요하지 않음)

1. (Request Body가 없기 때문에 인자값에 RequestBody를 넣지 않아도 됨)DeleteMapping을 통해서 주소 매칭

```java
package com.deleteapi.delete_api.controller;

import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class DeleteController {

    @DeleteMapping("/delete")
    public String delete(){
        System.out.println("delete");
        return "delete";
    }

}
```

간단하게 주소가 매칭되는지 확인해본 것인데, 이렇게 DeleteMapping을 통해서 주소가 [http://localhost:8089/api/delete](http://localhost:8089/api/delete)  에 매칭되고, response와 콘솔로 "delete"를 확인해볼 수 있다!

1. PathVariable과 RequestParam으로 경로 중 변경되는 값을 설정하고 파라미터를 받아오기

예) [http://localhost:8089/api/delete/path/김길동?account=user01](http://localhost:8089/api/delete/path/%EA%B9%80%EA%B8%B8%EB%8F%99?account=user01) 로 PathVariable로 userId를 받아오고, 파라미터로는 account를 받아오기

```java
@DeleteMapping("/delete/path/{userId}")
    public String deleteParam(@PathVariable String userId, @RequestParam String account){
     //db에서 고유값으로 삭제할 것이기 때문에 PathVariable은 한개만 받았음)
        System.out.println(userId+"가 삭제되었습니다");
        System.out.println("account parameter: "+account);
        return "[userId="+userId+", account= "+account+"]\n";
    }
```

![https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/API/DELETE%20API%20-%20PathVariable,%20RequestParam.png?raw=true](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/Spring/API/DELETE%20API%20-%20PathVariable,%20RequestParam.png?raw=true)

위와 같이 PathVariable을 이용하면 주소 중 변동되는 값을 설정하고, RequestParam으로는 ?뒤에 어떤 값을 받아올 것인지 설정할 수 있다

그렇기 때문에 실제 요청은 [http://localhost:8089/api/delete/path/김길동?account=user01](http://localhost:8089/api/delete/path/%EA%B9%80%EA%B8%B8%EB%8F%99?account=user01) 와 같이 구성될 수 있다!

🌟 DELETE는 멱등성의 성질 때문이기도 하지만, 쿼리 등의 오류만 없다면 대부분 200 OK 상태를 보여주게 될 것!

🌟 GET과 동작이 유사하지만, 리소스가 없어도 멱등하게 유지되기 때문에 200 OK를 리턴해줌!

GET에서처럼 RequestParam으로 객체로 받아올 수 있지만(Query Param Object로 파싱), 그렇게 하지 않고 대부분 고유한 값으로 받아와서 삭제함