# [Git] Remote Repository Overview

## 00. Remote Repository 그게 뭔데?

"로컬(지역) 레포지토리"와 대비되는 개념으로, 저장소를 인터넷에 올려서 협업할 때, `저장소와 연결되어 있는 다른 저장소`

`지역 저장소(로컬 레포지토리)와 연결되어 동기화되는 저장소`

🌟 원격 저장소의 가치 🌟

- 버전을 백업
- 다른 사람과 협업

▶️ 프로젝트 규모가 커질수록 더 중요해짐

## 01. 원격 저장소 생성

먼저, 내 컴퓨터에서 폴더 하나를 새로 만들어서 그 위치에서 git init을 해주자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder
$ mkdir git_remote_test
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder
$ cd ./git_remote_Test

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test
$ git init
Initialized empty Git repository in D:/VirtualBox_share_folder/git_remote_test/.git/

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$

```

그리고 f1.txt라는 이름의 파일을 하나 만들고 커밋까지 해주자

`git init --bare 저장소명`

- 저장소로써의 기능만 수행하는 저장소를 만드는 옵션인 bare로 설정
- 작업트리가 없이 .git만 존재하는 저장소
- 이 내부에서는 작업을 할 수 없음(작업트리가 없는 곳이기 때문)

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ git init --bare remote
Initialized empty Git repository in D:/VirtualBox_share_folder/git_remote_test/remote/
```

그러면 git_remote_test 아래에 remote라는 폴더경로가 생겨난다

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ ls -al
total 13
drwxr-xr-x 1 gs813 197609  0 Aug 22 11:52 ./
drwxr-xr-x 1 gs813 197609  0 Aug 22 11:45 ../
drwxr-xr-x 1 gs813 197609  0 Aug 22 11:48 .git/
-rw-r--r-- 1 gs813 197609 16 Aug 22 11:48 f1.txt
**drwxr-xr-x 1 gs813 197609  0 Aug 22 11:52 remote/**
```

그리고 remote 폴더로 이동해서 파일리스트를 확인해보면

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ cd ./remote

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test/remote (BARE:master)
$ ls -al
total 11
drwxr-xr-x 1 gs813 197609   0 Aug 22 11:52 ./
drwxr-xr-x 1 gs813 197609   0 Aug 22 11:52 ../
-rw-r--r-- 1 gs813 197609  23 Aug 22 11:52 HEAD
-rw-r--r-- 1 gs813 197609 104 Aug 22 11:52 config
-rw-r--r-- 1 gs813 197609  73 Aug 22 11:52 description
drwxr-xr-x 1 gs813 197609   0 Aug 22 11:52 hooks/
drwxr-xr-x 1 gs813 197609   0 Aug 22 11:52 info/
drwxr-xr-x 1 gs813 197609   0 Aug 22 11:52 objects/
drwxr-xr-x 1 gs813 197609   0 Aug 22 11:52 refs/
```

HEAD 등이 보이는데, 이는 .git 폴더 내부에 생겨나는 파일들이다

이번에는 remote라는 bare 원격 저장소를 git_remote_test 라는 로컬 저장소에 연결해서 push 해보도록 하겠다

ⓐ `git remote add a위치에_대한_별명 a위치` : (로컬저장소)에서 설정

- 로컬 저장소에서 원격 저장소를 연결

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ git remote add origin /d/VirtualBox_share_folder/git_remote_Test/remote

```

위의 경우는 /d/VirtualBox_share_folder/git_remote_Test/remote 위치의 별명이 origin 인데 로컬에서 원격 저장소인 /d/VirtualBox_share_folder/git_remote_Test/remote를 연결한다는 의미다!

그리고 연결이 되었는지 확인해보기 위해서 `git remote -v`를 입력해보면

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ git remote -v
origin  D:/VirtualBox_share_folder/git_remote_Test/remote (fetch)
origin  D:/VirtualBox_share_folder/git_remote_Test/remote (push)
```

위와 같이 표시되는 것을 확인해볼 수 있다

ⓐ-1. `git remote remove a위치에_대한_별명`: 원격 저장소를 지우기

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ git remote remove origin

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ git remote -v
```

✴️`git push  origin 브랜치이름` : origin에서 뻗어져 나온 브랜치의 이름으로 origin(원격 저장소)에 반영하기

ⓑ  git push 를 해보자

🌟 그런데, 이전에는 matching 방식이었는데 simple 방식으로 변경되었다고 한다.  그래서 `git config --global push.default simple` 을 입력해주자

그 후에 git push를 해주면 아래와 같은 문구를 확인해볼 수 있다

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ git config --global push.default simple

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ git push
**fatal: The current branch master has no upstream branch.
To push the current branch and set the remote as upstream, use**

    **git push --set-upstream origin master**
```

- 📌 **git push --set-upstream origin master : git push를 하게 되면 자동으로 origin의 master 브랜치로 푸시하겠다는 의미(—set-upstream으로 인해서 자동으로 연결)(git push origin master: master 브랜치에서 푸시하면 origin의  master 브랜치로 푸시)** ▶️ 로컬과 원격 저장소를 본격적으로 연결해주는 것! ▶️ 우리가 연결을 마무리 하기 위해서 사용해줄 것!(To push~, use~)

ⓒ `**git push** --set-upstream origin master` 입력

- 로컬과 원격 저장소를 연결
- 📌📌📌git push : 원격 저장소로 로컬 저장소를 밀어 넣는 것!

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test (master)
$ git push --set-upstream origin master
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 231 bytes | 115.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To D:/VirtualBox_share_folder/git_remote_Test/remote
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

이제 로컬 저장소인 git_remote_Test 폴더의 master 브랜치는 remote(origin) 폴더인 원격 저장소에 원격 브랜치로써 연결된 것이다!

ⓓ 원격 저장소인 remote로 이동해서 git log로 버전을 확인해보자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test/remote (BARE:master)
$ git log --branches --decorate --oneline
c6e86f4 (HEAD -> master) test:git remote test
```

그러면 이제 remote 즉 원격저장소에 있던 HEAD(현재 체크아웃된 브랜치를 가리킴)가 master를 가리키고, master 브랜치에서 작성했던 커밋을 확인해볼 수 있다! (혹시나 해서 본인의 커밋을 보여주는 git log를 아래와 같이 해보아도 결과는 동일하다)

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/git_remote_Test/remote (BARE:master)
$ git log
commit c6e86f451b5899eb30a5bbcfc66aeeae42f8c8f6 (HEAD -> master)
Author: JSJeong <hy6219@naver.com>
Date:   Sun Aug 22 11:48:20 2021 +0900

    test:git remote test
```