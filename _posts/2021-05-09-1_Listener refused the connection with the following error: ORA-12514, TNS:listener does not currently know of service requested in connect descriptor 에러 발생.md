SQLD 공부 겸 데이터베이스 설계 공부를 하기 위해서 디비버와 오라클을 설치할 필요가 있었다
그런데, 디비버에서 "Listener refused the connection with the following error: ORA-12514, TNS:listener does not currently know of service requested in connect descriptor"
에러가 발생해서 검색해본 결과는 대부분 "서비스 네임이 잘못되었다는 것"이었다

여러번 확인해보아도, 서비스네임은 맞았었다. 그래서 추가로 찾아본 내용이 바로

- https://tipland.tistory.com/68

- https://ifuwanna.tistory.com/255
바로 위의 블로그 부분이었다

즉, tnsnames.ora 파일과 listener.ora 파일을 확인해보라는 것이었다
1.그래서 먼저 tnsnames.ora 파일을 열기 위해서
설치된 애플리케이션의 경로를 따라서 찾았다.

나는 아래와 같은 경로에 설치했던 것으로 확인했다!
D:\app\(계정명)\virtual\product\12.2.0\dbhome_2

2.그리고 ora파일을 확인하기 위해서
D:\app\(계정명)\virtual\product\12.2.0\dbhome_2\network\admin 경로로 이동했다

3.이번에는 메모장으로 tnsnames.ora파일을 열고, 설치시 지정했던 orcl 이름으로 된 서비스를 찿아내었다

4.정말 많은 시간동안 삽질을 했었는데, 원인은.. 포트번호가 디비버에 입력된 기존에 연결하는 윈도우에 기입된 포트가 아닌, 다른 포트를 사용하는 것이 문제였다..!

원인을 찾아내어 포트번호를 잘 입력해주니 연결에 성공했다 🙂
