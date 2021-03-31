

# [Git] 리눅스 명령 연습하기 (1) 리눅스에서의 디렉토리 기호, cd, pwd, ls, atime/ctime/mtime
0. 깃 bash에서 앞에 붙은 ~ : 홈 디렉토리를 알려줌

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/raw/main/gitStudy/doItStudy/InstallGit/Ubuntu/%EC%9A%B0%EB%B6%84%ED%88%AC%EC%97%90%EC%84%9C%20%EA%B9%83%20%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0_5.%EA%B9%83%20%EB%A0%88%ED%8F%AC%EC%A7%80%ED%86%A0%EB%A6%AC%20%EB%82%B4%EB%A0%A4%EB%B0%9B%EA%B8%B0.PNG?raw=true)

※ 리눅스에서 디렉토리를 나타내는 기호

|기호| 설명
|--|--
| ~ | 현재 접속중인 사용자의 홈 디렉토리(윈도우 : c:/Users/사용자아이디, Ubuntu : /home/(계정명)
|./ | 사용자가 작업중인 현재 디렉토리
|../ | 현재 디렉토리의 상위 디렉토리

1. cd 명령어 : directory 로 이동

|cd 명령어  |특징
|--|--|
|cd [directory name] | 특정 디렉토리로 이동
|cd , cd ~ , cd $HOME  |HOME 디렉토리로 이동
|cd .|현재 디렉토리로 이동
|cd ..| 상위 디렉토리로 이동
|cd / | 최상위 디렉토리로 이동(비유 : /c/ 리눅스에서는 컴퓨터 디렉토리인 /로 이동!)
|cd - | 이전 디렉토리로 이동

- cd [directory name]  : 특정 디렉토리로 이동

![cd [directory]](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cd/cd%20directory%20-%20directory%EB%A1%9C%20%EC%9D%B4%EB%8F%99.PNG?raw=true)

- cd : HOME 디렉토리로(/home/[user name]) 이동
  
 ![cd ](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cd/cd%20%ED%99%88%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC%EB%A1%9C%20%EC%9D%B4%EB%8F%992.PNG?raw=true)

-  cd ~ : HOME 디렉토리로 이동

![cd ~](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cd/cd%20~%20home%20directory%EB%A1%9C%20%EC%9D%B4%EB%8F%99.PNG?raw=true)

- cd $HOME : HOME 디렉토리로 이동

![cd $HOME](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cd/cd%20HOME,%20pwd.PNG?raw=true)

- cd . : 현재 디렉토리로 이동

![cd .](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cd/cd%20.%20%ED%98%84%EC%9E%AC%20%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC%EB%A1%9C%20%EC%9D%B4%EB%8F%99.PNG?raw=true)

- cd ..  : **상위** 디렉토리로 이동

![cd ..](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cd/cd%20..%20%EC%83%81%EC%9C%84%20%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC%EB%A1%9C%20%EC%9D%B4%EB%8F%99.PNG?raw=true)

- cd / : **최상위** 디렉토리로 이동

![cd /](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cd/cd%20%20%EC%B5%9C%EC%83%81%EC%9C%84%20%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC%EB%A1%9C%20%EC%9D%B4%EB%8F%99.PNG?raw=true)

- cd - : 이전 디렉토리로 이동

![cd -](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cd/cd%20-%20%EC%9D%B4%EC%A0%84%20%EA%B2%BD%EB%A1%9C%EB%A1%9C%20%EC%9D%B4%EB%8F%99.PNG?raw=true)


2. pwd 명령어

- 현재 위치하고 있는 디렉토리를 반환

![pwd](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/pwd/pwd.PNG?raw=true)

3. ls 명령어 : 현재 위치한 디렉토리 내에 존재하는 파일 리스트를 출력


|   ls 명령어|특징
|--|--|
|ls| 현재 디렉토리에 있는 파일들을 보여줌
|ls -a |**숨겨진 파일 및 디렉토리까지** 보여줌
|ls -l |1. 파일타입+**파일 권한**+포함된 파일 수+소유자+그룹+파일크기+수정일자+파일명까지 상세하게 보여줌,  2. 리눅스 mtime(modification time; 수정시간)을 알려줌
|ls -lu| 리눅스 atime(access time; 접근 시간)을 출력해줌
|ls -lc| 리눅스 ctime(change time; 변경 시간)을 출력해줌
|ls -S |파일크기를 내림차순으로 정렬하여 보여줌
|ls -R |**하위 디렉토리까지 상세하게 보여줌**
|ls -r |파일 및 폴더명에 대해서 알파벳 역순으로 출력 
|ls -h |사람이 보기 편한 K(KB), M(MB),G(GB) 단위로 표시해줌
|ls -t |파일 및 폴더 작성 시간에 대해서 내림차순 정렬하여 보여줌

- ls  : 현재 디렉토리에 존재하는 파일 및 폴더를 보여줌 ![ls](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls.PNG?raw=true)

- ls -a :숨겨진 파일 및 폴더 를 보여줌 ; ls -all
![ls -a](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls%20-a.PNG?raw=true)

*리눅스에서의 mtime, atime, ctime
가. mtime: modification time; 수정 시간
      mtime 변경시, atime 및 ctime 모두 견경됨

나. atime : access time; 접근 시간

다. ctime : change time: 변경 시간 - 파일 권한 등이 변경될 때 ctime이 갱신됨

참조: https://withcoding.com/115

- ls -l ; ls long
(1) 파일유형+파일 퍼미션 - 포함된 파일 수 -소유자-그룹-파일크기-수정시간(mtime)-파일명 출력


![ls -l](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls%20-l.png?raw=true)


![ls - l  수정시간](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls%20-l_2_%EC%88%98%EC%A0%95%EC%8B%9C%EA%B0%84%20%EC%95%8C%EB%A0%A4%EC%A4%8C.PNG?raw=true)

(2) 리눅스 mtime(수정시간) 출력

- ls -lc : 변경시간(ctime) 출력
  ls -lu : 접근시간(atime) 출력


![ls -lu, ls -lc](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls%20-lu%20_%20atime,%20ls%20-lc_%20ctime.PNG?raw=true)

- ls -S : 파일크기에 대해서 내림차순으로 정렬 ; ls -Size


![ls -S](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls%20-S%20%ED%8C%8C%EC%9D%BC%20%ED%81%AC%EA%B8%B0%EC%88%9C%EC%9C%BC%EB%A1%9C%20%EB%82%B4%EB%A6%BC%EC%B0%A8%EC%88%9C%EC%9C%BC%EB%A1%9C%20%EC%A0%95%EB%A0%AC%ED%95%98%EC%97%AC%20%EC%B6%9C%EB%A0%A5.PNG?raw=true)

- ls -R : 하위 디렉토리까지 출력 ; ls - recursive


[ls -R](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls%20-R.mkv)

- ls -r : 파일 및 폴더명에 대해서 알파벳 역순으로 출력; ls -reverse


![ls -r](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls%20-r%20%EC%95%8C%ED%8C%8C%EB%B2%B3%20%EC%97%AD%EC%88%9C%EC%9C%BC%EB%A1%9C%20%EC%B6%9C%EB%A0%A5.PNG?raw=true)

- ls -h : 사람이 보기 쉬운 형태로 파일 크기 표현(K, M, G) ; ls -human


![ls -h](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls%20-h.PNG?raw=true)


- ls -t :  파일 작성시간에 대해서 내림차순(최근파일이 상단, 오래된 파일이 가장 하단)으로 출력

![ls -t](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/ls/ls%20-t%20%ED%8C%8C%EC%9D%BC%20%EC%9E%91%EC%84%B1%EC%8B%9C%EA%B0%84%20%EC%88%9C%EC%9C%BC%EB%A1%9C%20%EB%82%B4%EB%A6%BC%EC%B0%A8%EC%88%9C%20%ED%91%9C%EC%8B%9C_%EC%B5%9C%EC%8B%A0-%EC%98%A4%EB%9E%98%EB%90%9C%20%EC%88%9C.PNG?raw=true)



