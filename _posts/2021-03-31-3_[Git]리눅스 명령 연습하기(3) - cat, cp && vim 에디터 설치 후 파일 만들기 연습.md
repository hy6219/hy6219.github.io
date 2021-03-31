# [Git]리눅스 명령 연습하기(3) - cat, cp && vim 에디터 설치 후 파일 만들기 연습

I. 리눅스 명령어 - cat, cp
1. cat : 파일 내용 확인하기

| 기호 | 특징
|--|--
|cat 파일명.확장자| 파일명.확장자인 파일의 내용을 확인
|cat 파일1 파일2... 파일n > 새 파일 | 파일 1, ...파일 n의 내용을 합쳐서 새파일을 만듦
|cat 파일1 >> 파일2 | 파일2의 뒤에 파일1의 내용을 덧붙이기

- cat 파일명.확장자
![cat 파일](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cat/cat%20%EB%AA%85%EB%A0%B9%EC%96%B4%20-%20%ED%85%8D%EC%8A%A4%ED%8A%B8%20%EB%AC%B8%EC%84%9C%20%EB%82%B4%EC%9A%A9%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.PNG?raw=true)

- cat 파일1 파일2... 파일n > 새 파일

![파일들을 합쳐서 새로운 파일을 만들기](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cat/cat%20%ED%8C%8C%EC%9D%BC1%20%ED%8C%8C%EC%9D%BC2%20%28%EC%9A%B0%EA%BA%BD%EC%87%A0%EA%B8%B0%ED%98%B8%29%20%ED%8C%8C%EC%9D%BC3%20--%ED%8C%8C%EC%9D%BC1%EA%B3%BC%20%ED%8C%8C%EC%9D%BC2%EB%A5%BC%20%ED%95%A9%EC%B3%90%EC%84%9C%20%ED%8C%8C%EC%9D%BC3%EC%9D%84%20%EB%A7%8C%EB%93%A4%EA%B8%B0.PNG?raw=true)

- cat 파일1 >> 파일2
![cat 파일1>>파일2 : 파일1을 파일2에 append](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cat/cat%20%ED%8C%8C%EC%9D%BC1%20%28%EC%9A%B0%EA%BA%BD%EC%87%A0%EA%B8%B0%ED%98%B82%EA%B0%9C%29%20%ED%8C%8C%EC%9D%BC2%20-%20%ED%8C%8C%EC%9D%BC2%20%EB%92%A4%EC%97%90%20%EC%9D%B4%EC%96%B4%EC%84%9C%20%ED%8C%8C%EC%9D%BC1%EC%9D%98%20%EB%82%B4%EC%9A%A9%EC%9D%84%20%EB%B6%99%EC%9D%B4%EA%B8%B0.PNG?raw=true)

2. cp 파일1 파일2 : 파일1을 복사하여 파일2를 만들기

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/commands/cp%20file1%20file2/cp%20%ED%8C%8C%EC%9D%BC1%20%ED%8C%8C%EC%9D%BC2%20-%20%ED%8C%8C%EC%9D%BC1%EC%9D%84%20%EB%B3%B5%EC%82%AC%ED%95%9C%20%ED%8C%8C%EC%9D%BC2%20%EC%83%9D%EC%84%B1.PNG?raw=true)


II. vim 에디터로 파일 생성하기

1. vim 만들 파일
   예) vim test.txt
  
  - 저의 경우에는, vim이 설치되어 있지 않았기 때문에 sudo apt install vim을 입력해서 먼저 설치해주도록 하였습니다
![vim에디터가 설치되어 있지 않았을 때 vim 설치](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/vim/makeFile/vim%20%EC%9D%B4%EC%9A%A9%ED%95%B4%EC%84%9C%20%EB%AC%B8%EC%84%9C%20%EB%A7%8C%EB%93%A4%EA%B8%B0.PNG?raw=true)

![vim 설치](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/vim/makeFile/%EC%9A%B0%EB%B6%84%ED%88%AC_vim%20%EC%84%A4%EC%B9%98.PNG?raw=true)

2. vim 만들 파일로 1과 같이 입력을 하게 된다면,

a. 파일이 원래 존재했던 경우 : 덮어쓰기

b. 파일이 존재하지 않은 경우 : 새 파일 만들기

3. 기본적으로 vim 에디터에는 ex 모드와 입력모드가 존재하는데, 처음에 들어가게 되면 ex 모드가 활성화되어 있을 것입니다! 이 상태에서 

- **입력**을 하기 위해서는 **i 키 혹은 a 키**를 누르면 입력모드로 진입하게 됩니다

-입력을 중단하고 **다시 ex 모드로** 돌아가서 저장 및 종료 등을 하고자 한다면, **esc 키**를 입력합니다. ex 모드에서 동작하게 할 수 있는 여러가지 모드는 아래와 같습니다.


![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/vim/makeFile/vim%20-%20i%EB%82%98%20a%EB%8A%94%20ex%20%EB%AA%A8%EB%93%9C%EB%A5%BC%20%EC%9E%85%EB%A0%A5%EB%AA%A8%EB%93%9C%EB%A1%9C%20%EC%A0%84%ED%99%98,%20esc%EB%8A%94%20%EB%8B%A4%EC%8B%9C%20ex%EB%AA%A8%EB%93%9C%EB%A1%9C,%20wq%EB%8A%94%20%EC%A0%80%EC%9E%A5w%20%ED%9B%84%20%EC%A2%85%EB%A3%8Cq.PNG?raw=true)


- ex 모드에서 사용 가능한 기능들



| 키 | 기능
|--|--
|:w 또는 :write|저장
|:q 또는 :quit | 종료
|:wq (파일명)| (파일명)을 뒤에 붙이면 해당 파일명으로 저장됨
|q!|문서를 저장하지 않고 편집기를 종료(.swp 임시파일이 생성됨)

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/vim/makeFile/vim%20ex%20%EB%AA%A8%EB%93%9C%20q!%20%ED%85%8C%EC%8A%A4%ED%8A%B8%202.PNG?raw=true)

![enter image description here](https://github.com/hy6219/TIL-Today-I-Learned-/blob/main/gitStudy/doItStudy/linux/vim/makeFile/vim%20ex%20%EB%AA%A8%EB%93%9C%20q!%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EA%B2%B0%EA%B3%BC.PNG?raw=true)
