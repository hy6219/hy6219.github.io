# MultipartRequest를 이용한 파일업로드 시 주의할 부분

MultipartRequest 객체를 파일업로드를 위해서 사용하게 되는데 , MVC 패턴을 이용한 설계중 커맨드 패턴을 이용하여 설계, 개발을 하다보면 커맨드를 파악하기 위해서 컨트롤러에도 액션(invoker 클래스)에도 MultipartRequest가 필요한 경우가 있다. 그렇게 되면 

- java.io.IOException: Corrupt form data: premature ending 에러가 날 수도 있다!

[https://azurecourse.tistory.com/1101](https://azurecourse.tistory.com/1101)

이 경우에는 web.xml 파일을 수정하면 된다고는 하지만 그럴 경우 다른 파일에도 영향이 가는 경우까지 고려해야 함을 명심하자![MultipartRequest 객체가 2개인데 그 사이에 IOStream이 손실되어 발생]

- java.io.IOException: Posted content type isn't multipart/form-data 에러가 날 수도 있다

-이 에러는 필자의 경우, submit을 button으로 바꾼 후 쿼리스트링으로 넘기려 했는데 이 경우에는 넘겨져야할 정보가 넘어가지 않게 되어 발생하는 것이다

그래서 찾은 가장 간단명료한 해법은 form 요소의 action에 커맨드를 붙여주어 request로 보내질 필요가 있는 파라미터를 전달해주는 것이다. 그렇게 되면 기존의 설계요소를 크게 변경하지 않아도 적용가능하다

[https://hatssarjy.tistory.com/172](https://hatssarjy.tistory.com/172)