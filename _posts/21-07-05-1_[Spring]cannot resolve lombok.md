# Spring 초기 세팅- cannot resolve lombok

JSP 서블릿 기본 개념을 공부하고, 물론 복습을 해야하지만

스프링을 공부하려고 초기 세팅을 하고 있었는데 맛보기로 lombok이 아직

어떤 것인지는 모르겠으나 잘 작동하는지 확인하기 위해서

@Data를 입력했는데 빨간글자..내가 싫어하는 에러를.. 맛보게 되었다..

https://devkingdom.tistory.com/209

이 블로그를 참고해서 build.gradle 파일도 확인해보고, 플러그인 설치 여부도 확인해보았는데

갑자기 든 생각 'Spring initializer에서 내가 lombok을 체크하지 않았던것 같은데..?'

워크스페이스 폴더를 삭제하고 다시 차근차근 initializer에서 lombok과 spring web을

모두 체크했더니 어노테이션이 잘 작동하였다..

나중을 위해서 참고한 블로그는 아래에 메모하였다

https://m.blog.naver.com/light-star/221969272088

https://m.blog.naver.com/kiel0103/221766235162

https://hyesun03.github.io/2019/08/27/intellij-cannot-resolve-symbol-error/