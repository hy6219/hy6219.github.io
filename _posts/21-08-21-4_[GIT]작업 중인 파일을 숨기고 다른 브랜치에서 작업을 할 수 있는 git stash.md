# [Git] Branch-Stash

## 00. `git checkout -b 브랜치명`

- 새로운 브랜치를 만듦과 동시에 그와 함께 새 브랜치로 이동! move move!!

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git checkout -b test
Switched to a new branch 'test'

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git branch
  exp_git_test
  main
* test
```

## 01. stash?

stash "감추다" "숨겨두다"

브랜치를 이용해서 작업 중에 다른 브랜치로 체크아웃을 해야할 경우 사용!

(아직 작업할 작업이 있는데 커밋을 할 수는 없지 않은가!)

이때 작업중이던 파일을 숨겨두고 다른 브랜치로 체크아웃

## 02. `git stash [save]` - 내용을 감추고 다른 브랜치로 이동

main 브랜치에서 stash.txt라는 파일을 만들어 작업하고 커밋까지 해주자

그리고 `git checkout -b test` 로 test용 브랜치를 만듦과 동시에 해당 브랜치로 체크아웃해주자

그리고 stash.txt 파일을 수정해주려 

vim stash.txt

를 실행하고 바로 메인브랜치로 체크아웃하면

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git status
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   stash.txt
```

커밋을 위한 스테이징이 이루어지지 않았다는 메시지가 뜨면서, 다른 브랜치에 영향을 주는 것을 확인해볼 수 있다

다시 test 브랜치로 체크아웃하고, 이제는 `git stash --help` 로 stash의 옵션들을 보자

많은 옵션들이 있지만 save 옵션을 사용해보자 `git stash` 로 끝나도 좋다

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash save
warning: LF will be replaced by CRLF in stash.txt.
The file will have its original line endings in your working directory
Saved working directory and index state WIP on test: 81afa08 test:branch stash

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git status
On branch test
nothing to commit, working tree clean

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git checkout main
Switched to branch 'main'
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (main)
$ git status
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

그러면 작업 트리의 내용과  인덱스의 작업 중인 상태가 test에 저장되었음을 확인해볼 수 있다

✴️ WIP(Working In Process)  : 작업 중이라는 뜻

그리고 더이상 Changes not staged for commit이 아닌 nothing to commit의 메시지를 확인해볼 수 있다

즉, stash.txt는 이제 수정했던 내용을 갖고 있지 않은 상태가 된 것이다!

## 03. 작업 후 , 숨겨두었던 파일을 복원

이제 실컷 작업을 하고 다시 test 브랜치로 이동하여 작업중이었던 파일을 복원해서 작업할 필요가 있다

이때에는 `git stash apply`를 해주자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash apply
On branch test
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   stash.txt
```

그러면 이전과 동일한 메시지를 확인해볼 수 있다

즉, 이전에 숨겨둔 작업중인 파일이 살아나서, 수정된 상태임이 확인되는 것이다

## 04 `git stash list` - 저장했던 여러 번의 stash 기록을 확인

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash list
stash@{0}: WIP on test: 81afa08 test:branch stash
```

## 05. `get reset --hard  HEAD` - 가장 최신 상태로 작업트리를 보내기

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git reset --hard HEAD
HEAD is now at 81afa08 test:branch stash
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git status
On branch test
nothing to commit, working tree clean
```

이는 제거한 것이 아니라 포인터처럼 시점만 움직여준 것이다!

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash list
stash@{0}: WIP on test: 81afa08 test:branch stash

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash apply
On branch test
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   stash.txt

no changes added to commit (use "git add" and/or "git commit -a")

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git status
On branch test
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   stash.txt
```

stash 목록에 여전히 stash가 존재하고, git stash apply로 복원도 잘 되는 것을 확인해볼 수 있다

즉, 포인터처럼 시점/지점을 움직여준 것 뿐이다

🌟 stash를 명시적으로 삭제하지 않는 한 어딘가에 stash 는 존재한다!

## 06. `git stash drop` -가장 최신의 stash 를 삭제

다시 test 브랜치에서 stash2.txt파일을 만들고 스테이징까지만 해주자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ vim stash2.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git add stash2.txt
warning: LF will be replaced by CRLF in stash2.txt.
The file will have its original line endings in your working directory
```

그리고 stash를 해주자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash
Saved working directory and index state WIP on test: 81afa08 test:branch stash

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git status
On branch test
nothing to commit, working tree clean
```

그리고 `git stash list`를 확인해보면 2개의 stash 가 존재함을 확인해볼 수 있다

그리고 최근에 들어온 stash가 바로 인덱스 0이다!!(보여지는 순서와 들어간 순서를 생각하면 스택구조같다]

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash list
**stash@{0}: WIP on test: 81afa08 test:branch stash**
stash@{1}: WIP on test: 81afa08 test:branch stash
```

그리고 이 상황에서 `git stash apply`는 맨 위에 있는 `stash[0]에 대해서 처리한다!`

그런데 `stash[0]을 먼저 복원 후 stash[1]을 복원하는 순차적인 작업을 하고 싶다면`

`git stash drop`으로 가장 최근의 stash를 제거하자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash drop
Dropped refs/stash@{0} (24cc8455b5ac859f3a9db7f261e75d1a8182f343)

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash list
stash@{0}: WIP on test: 81afa08 test:branch stash
```

그러면 위와 같이 이제는 stash가 1개 뿐임을 확인해볼 수 있다

(이 과정에서 파일이 삭제될 수 있다)

## 07. 06의 확장- `git stash apply; git stash drop;`: stash 복원과 drop을 동시에 한 줄에서 실행

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash apply; git stash drop;
On branch test
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   stash2.txt

Dropped refs/stash@{0} (f49e5f95b6ade63dc3a2109b6009cb6b20760671)

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash list
stash@{0}: WIP on test: 81afa08 test:branch stash
```

위와 같이 한줄에 apply와 drop을 모두 실행하면 맨 위에 있던 stash는 복원되었지만 여전히 stash로 있었다가 drop으로 삭제되는 것을 확인해볼 수 있다

즉, stash에는 1개만 남게 되는 것이다

## 08. 06의 확장, 07을 더 편하게! `git stash pop` -맨 위의 stash를 복원되고 제거까지 !

 HEAD 위치로 (최근의 위치로 )이동한 후 , 복원하지 않고 stash2.txt 내용을 변경 후 stash하자

그 후에는, stash list를 확인하고 stash pop, stash list를 해봄으로써 pop을 살펴보자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git reset --hard HEAD
HEAD is now at 81afa08 test:branch stash
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ vim stash2.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash
Saved working directory and index state WIP on test: 81afa08 test:branch stash

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash list
stash@{0}: WIP on test: 81afa08 test:branch stash

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash pop
On branch test
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   stash.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        stash2.txt

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (3510c1fc6c88304f3a2c613499758b918b5c3765)

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash list
```

그러면 stash 후에는 stash list에서 stash를 확인해볼 수 있었는데

stash pop에서 복원될 때 보이는 메시지와 삭제할 때 보이는 메시지를 모두 확인해볼 수 있었다

그 후 stash list를 확인해보니 비어있는 것을 알 수 있었다

## 09. `stash`는 `tracked되고 있는 파일(버전 관리가 되고 있는 파일)에 대해서만` 적용가능하다!

만약 stash3.txt를 만들고, 기존의 stash2.txt는 내용 변경 후 스테이징만 한다고 하자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git status
On branch test
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   stash2.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   stash.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        stash3.txt
```

그러면 stash3.txt가 추적되지 않는다 

이 상태에서 stash 해보자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash
Saved working directory and index state WIP on test: 81afa08 test:branch stash

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git stash list
stash@{0}: WIP on test: 81afa08 test:branch stash
```

그러면 단 한 개에 대해서만 stash가 되었음을 알 수 있다

그러면 그것이 어떤 것일지 궁금해서 git status를 해보면 stash2.txt파일만 stash 됨을 알 수 있다

즉, tracked files에 대해서만 stash가 가능한 것이다!

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/TIL (test)
$ git status
On branch test
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        stash3.txt
```