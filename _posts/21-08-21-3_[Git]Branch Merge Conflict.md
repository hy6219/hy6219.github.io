# [Git]Git Branch 병합 중 발생하는 충돌 해결하기

병합 충돌은 아래의 저번 공부 중 발생했었는데, 정확하게 어떻게 해결해야 좋을지 학습해보자!

[[GIT]Branch를 만들고, 정보를 확인하고, 병합하기 ❤](https://hy6219.github.io/TIL/gitStudy/doItStudy/versionControl/[Git]%EB%B8%8C%EB%9E%9C%EC%B9%98%EB%9E%80%20%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80(1).html#d7b5e1af-9a49-4692-a5e3-31598c5037ec)

🌟 브랜치 병합 중 충돌이 일어나는 이유- 파일 내용이 서로 다르기 때문!!

## 00. (Appendix) 강제로 브랜치 삭제하기

▶️ `git branch -D 브랜치명`

## 01. (Merge 충돌이 없는 상황)서로 다른 브랜치에 서로 다른 파일을 만든 경우

먼저 main 브랜치에 master.txt라는 문서를 하나 만들어두자

그리고 스테이징과 커밋까지 진행해주자!!

그 후에!! 분기된 브랜치인 exp_git_test로 체크아웃해주자!

그리고 exp_git_test 브랜치에서는 exp.txt라는 문서를 만들어주자

exp.txt도 커밋까지 진행해주자!

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (exp_git_test)
$ git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ vim master.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git commit -am 'test:make conflict'
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        master.txt

nothing added to commit but untracked files present (use "git add" to track)

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git add .
warning: LF will be replaced by CRLF in master.txt.
The file will have its original line endings in your working directory
g
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git commit -m 'test:make conflict'
[main ad0fc8a] test:make conflict
 1 file changed, 1 insertion(+)
 create mode 100644 master.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git checkout exp_git_test
Switched to branch 'exp_git_test'

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (exp_git_test)
$ vim exp.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (exp_git_test)
$ git add .
warning: LF will be replaced by CRLF in exp.txt.
The file will have its original line endings in your working directory

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (exp_git_test)
$ git commit -m 'text:make conflict at exp'
[exp_git_test 20e80c9] text:make conflict at exp
 1 file changed, 1 insertion(+)
 create mode 100644 exp.txt
```

이제 메인 브랜치로 이동하자

.

git checkout main

그리고 현재 커밋 버전을 확인하기 위해서 git log를 찍어보았다

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git log --branches --decorate --graph
* commit 20e80c9af725e5f2e33c00797e1c2b7c36e874a1 (exp_git_test)
| Author: JSJeong <hy6219@naver.com>
| Date:   Sat Aug 21 20:38:32 2021 +0900
|
|     text:make conflict at exp
|
| * commit ad0fc8a2095555d8793e35ddafee71200c6e084d (HEAD -> main)
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 20:35:51 2021 +0900
| |
| |     test:make conflict
| |
| * commit f7cfe5e1903835bdd0d3d8ffaad6e58253ff4bef (origin/main, origin/HEAD)
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 17:45:50 2021 +0900
| |
| |     docs:README.md 업데이트
| |
| * commit 9283bd58ac77e936d4c5f6e62f48b3deee55f0f4
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 17:43:28 2021 +0900
| |
| |     docs:[GIT ERR]Repository not found
```

이전에 올렸던 `ad0fc8a`, `f7cfe5`, `9283bd`  버전에 대해서 각자의 길을 걷고 있는 것을 확인해볼 수 있다

이제 병합해볼까?(악마의 미소! 😼)

git merge exp_git_test

그러면 두 브랜치가 달랐기 때문에 `Merge`메시지가 생기는 것을 vim 에디터에서 확인해볼 수 있다!

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git log --branches --decorate --graph
*   commit 9f31ed04c3a2afc3d578754c383bce6edf34201f (HEAD -> main)
|\  Merge: ad0fc8a 20e80c9
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 20:43:36 2021 +0900
| |
| |     Merge branch 'exp_git_test'
| |
| * commit 20e80c9af725e5f2e33c00797e1c2b7c36e874a1 (exp_git_test)
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 20:38:32 2021 +0900
| |
| |     text:make conflict at exp
| |
* | commit ad0fc8a2095555d8793e35ddafee71200c6e084d
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 20:35:51 2021 +0900
| |
| |     test:make conflict
| |
* | commit f7cfe5e1903835bdd0d3d8ffaad6e58253ff4bef (origin/main, origin/HEAD)
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 17:45:50 2021 +0900
| |
```

그러면 이전의 버전들이 모두병합된 상태의 main 브랜치임을 살펴볼 수 있다

그리고 ls -al을 통해서 exp.txt라는 분기된 브랜치에서 만든 파일도 병합되었음을 확인해볼 수 있다

이를 통해서 `각기 다른 브랜치에서 각기 다른 파일에 대해서는 merge 시에 충돌이 일어나지 않고 자동 병합`됨을 확인해볼 수 있다

## 02. (Merge 충돌이 없는 상황) 파일이 같지만, 위치가 다른 경우

먼저 main브랜치에 다음과 같은 내용으로 common2.txt 파일을 만들고 커밋까지 해주자(TIL위치에)

```java
function a(main){
}
```

그리고 exp_git_test 브랜치로 체크아웃 후 TIL 하위 디렉토리 중 gitStudy 디렉토리에서 아래와 같이 common2.txt파일을 작성하고 커밋까지 해주자

```java
function a(exp){
}
```

그리고 main 브랜치로 이동 후 merge 를 시도하면 vim 에디터로 메시지를 설정하고 병합되는 것을 살펴볼 수 있다(문제없이)

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL/gitStudy (main)
$ git merge exp_git_test
Merge made by the 'recursive' strategy.
 gitStudy/common2.txt | 2 ++
 1 file changed, 2 insertions(+)
 create mode 100644 gitStudy/common2.txt
```

## 03. (Merge 충돌이 일어날 수 있는 상황) 파일과 위치가 같은 경우[=같은 부분을 수정한 경우!]

main 브랜치와 exp_git_test 브랜치에 TIL 폴더 바로 밑에 common.txt 파일을 만들고, 각기 다른 내용을 작성해준 후, 스테이징 및 커밋을 해주자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ vim common.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git add .
warning: LF will be replaced by CRLF in common.txt.
The file will have its original line endings in your working directory

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git commit -m 'text:make conflict'
[main d37d6ee] text:make conflict
 1 file changed, 1 insertion(+)
 create mode 100644 common.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git checkout exp_git_test
Switched to branch 'exp_git_test'

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (exp_git_test)
$ vim common.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (exp_git_test)
$ git add .
warning: LF will be replaced by CRLF in common.txt.
The file will have its original line endings in your working directory

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (exp_git_test)
$ git commit -m 'test: exp conflict'
[exp_git_test 74f7c00] test: exp conflict
 1 file changed, 1 insertion(+)
 create mode 100644 common.txt
```

그 후에 다시 main 브랜치로 체크아웃하자

git checkout main

그리고 exp브랜치를 병합해주자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git merge exp_git_test
**CONFLICT (add/add): Merge conflict in common.txt
Auto-merging common.txt
Automatic merge failed; fix conflicts and then commit the result.**
```

😍 드디어 충돌!!이 일어났다 ❤

메시지를 잘 살펴보면 "자동으로 common.txt를 병합하려다가 실패했다고 메시지에서 뜬다!

그리고 상태를 확인해보기 위해서 git status 를 입혁하면 아래처렴 `main|MERGING`이 표시되는 것을 볼 수 있다

그리고 양측 모두에서 변경하려고 시도했기 때문에 문제가 생겼다라는 메시지를 확인해볼 수 있다

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main|MERGING)
$ git status
On branch main
Your branch is ahead of 'origin/main' by 4 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both added:      common.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

🌟 이제 vim 에디터를 켜자

vim common.txt

```java
**<<<<<<< HEAD**
make conflict
**=======**
On branch origin/exp_git_test
**>>>>>>> exp_git_test**
```

그러면 정체불명의 기호들이 보인다 😭

- ======= : equal 구분자! 이 구분자를 기준으로 HEAD와 브랜치를 확인 가능!
- **<<<<<<< HEAD : 현재 체크아웃 한 브랜치의 수정 사항**
- **>>>>>>> exp_git_test: exp_git_test 브랜치의 수정 사항**

즉, 깃이 사용자에게 충돌을 해결하라고 위임한 것➕ 충돌이 난 부분을 표시

▶️ 자유롭게 수정

```java
make conflict On branch origin/exp_git_test and origin/main
```

그 후 커밋까지 실행해준 후, 로그를 살펴보면 , 충돌이 해결되고 병합된 것을 살펴볼 수 있다

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main|MERGING)
$ git add common.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main|MERGING)
$ git commit -m 'test: merge conflict solving'
[main 7fd6a32] test: merge conflict solving

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git log --branches --decorate --graph
*   commit 7fd6a327291afc044adc753f0b9d59bf4f43669e (HEAD -> main)
|\  Merge: d37d6ee 74f7c00
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 21:09:14 2021 +0900
| |
| |     test: merge conflict solving
| |
| * commit 74f7c0043bc21412c65d070d1d1c193e0a084b4b (exp_git_test)
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 20:51:54 2021 +0900
| |
| |     test: exp conflict
| |
* | commit d37d6ee91f9c72434dbb13b13b7a14a42114eeff
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 20:50:41 2021 +0900
| |
| |     text:make conflict
| |
* | commit 9f31ed04c3a2afc3d578754c383bce6edf34201f
|\| Merge: ad0fc8a 20e80c9
| | Author: JSJeong <hy6219@naver.com>
| | Date:   Sat Aug 21 20:43:36 2021 +0900
```