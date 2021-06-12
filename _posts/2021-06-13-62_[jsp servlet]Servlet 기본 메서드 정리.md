# Servlet 기본 메서드 정리

- HttpServletRequest객체.**setCharacterEncoding** : **POST 방식**에서 깨지는 한글 인코딩을 방지

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response){
	request.setCharacterEncoding("UTF-8");
```

- HttpServletRequest.getParameter("name 속성값") : name 속성값으로 된 요소의 정보를 추출하여 String 형태로 반환

- HttpServletRequest.getParameterValues("name 속성값") : 이 경우는 체크박스와 같이, 여러 선택지 중에서 복수선택값을 받아올 때, 그 그룹값(name 속성값)을 매개변수로 전달하여 String 배열 타입으로 반환받을 수 있게 함