# [Git]원격 저장소와 지역 저장소의 동기화 방법 (Github)

하나의 원격저장소를 중심으로 두 개의 로컬 저장소가 작업을 동기화되도록 해보자!(협업에 좋을 것이다!)

ⓐ 원격저장소의 git 주소를 복사하자

나는 저번에 사용했던 git_remote_test의 깃주소를 그대로 사용하겠다!

[https://github.com/hy6219/git_remote_test.git](https://github.com/hy6219/git_remote_test.git)

ⓑ 두 개의 로컬 저장소를 만들자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder
$ mkdir synGit01

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder
$ mkdir syncGit02
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/synGit01
$ git clone https://github.com/hy6219/git_remote_test.git gitHome
Cloning into 'gitHome'...
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 6 (delta 0), reused 6 (delta 0), pack-reused 0
Receiving objects: 100% (6/6), done.
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/syncGit02
$ git clone https://github.com/hy6219/git_remote_test.git gitHome
Cloning into 'gitHome'...
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 6 (delta 0), reused 6 (delta 0), pack-reused 0
Receiving objects: 100% (6/6), done.
```

그리고 synGit01에서 hello.txt 파일의 내용을 수정해주자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/synGit01/gitHome (master)
$ vim hello.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/synGit01/gitHome (master)
$ cat hello.txt
hello git!
update content!!
on synGit01
```

🌟커밋을 수정하는 방법: `git commit --amend`

(개정하다 amend)

지역저장소의 내용을 바꾸는 것은 그 파일이 컴퓨터에 있는 동안에만 해야 하고, 푸쉬를 한 이후에는 수정하지 않는 것이 협업에 좋다!

---

그리고 푸쉬까지 synGit01에서 해주자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/synGit01/gitHome (master)
$ git commit -am 'test:gitHome on synGit01'
[master e4a72f5] test:gitHome on synGit01
 1 file changed, 1 insertion(+)

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/synGit01/gitHome (master)
$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Writing objects: 100% (3/3), 291 bytes | 291.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/hy6219/git_remote_test.git
   7e0301f..e4a72f5  master -> master
```

그리고 깃허브 원격저장소에서 새로고침해보면 커밋한 내용을 확인해볼 수 있다!

그리고 나서 다른 저장소 `git pull`을 통해서 원격 저장소의 내용을 로컬 저장소에 동기화하게 된다!(비밀번호를 묻지 않는 것은 공개 저장소로 설정해두었기 때문!)

그러면 우리는 syncGit02에서 git pull을 해주면 동기화될 것이다!!

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/syncGit02/gitHome (master)
$ git pull
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 271 bytes | 0 bytes/s, done.
From https://github.com/hy6219/git_remote_test
   7e0301f..e4a72f5  master     -> origin/master
Updating 7e0301f..e4a72f5
Fast-forward
 hello.txt | 1 +
 1 file changed, 1 insertion(+)

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/syncGit02/gitHome (master)
$ ls -al
total 5
drwxr-xr-x 1 gs813 197609  0 Aug 22 16:07 ./
drwxr-xr-x 1 gs813 197609  0 Aug 22 15:56 ../
drwxr-xr-x 1 gs813 197609  0 Aug 22 16:07 .git/
-rw-r--r-- 1 gs813 197609 43 Aug 22 16:07 hello.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/syncGit02/gitHome (master)
$ cat hello.txt
hello git!
update content!!
on synGit01
```

그러면 synGit01이 원격 저장소에 보낸 수정된 내용이 syncGit02에 동기화되어 보여지는 것을 확인해볼 수 있다!

마찬가지로 이번에는 syncGit02에서 hello.txt를 수정하고, 푸쉬까지 진행해보자

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/syncGit02/gitHome (master)
$ vim hello.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/syncGit02/gitHome (master)
$ cat hello.txt
hello git!
update content!!
on synGit01
confirmed on syncGit02
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/syncGit02/gitHome (master)
$ git commit -am 'test:confirmed on syncGit02'
[master a269986] test:confirmed on syncGit02
 1 file changed, 1 insertion(+)

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/syncGit02/gitHome (master)
$ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 308 bytes | 154.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/hy6219/git_remote_test.git
   e4a72f5..a269986  master -> master
```

그러면 역시 깃허브의 원격 저장소에서 커밋된 내용을 볼 수 있다

그리고 이번에는 다시 synGit01로 와서 😸 `git pull`을 통해 동기화해주자!

```java
gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/synGit01/gitHome (master)
$ git pull
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 288 bytes | 0 bytes/s, done.
From https://github.com/hy6219/git_remote_test
   e4a72f5..a269986  master     -> origin/master
Updating e4a72f5..a269986
Fast-forward
 hello.txt | 1 +
 1 file changed, 1 insertion(+)

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/synGit01/gitHome (master)
$ ls -al
total 5
drwxr-xr-x 1 gs813 197609  0 Aug 22 16:10 ./
drwxr-xr-x 1 gs813 197609  0 Aug 22 15:59 ../
drwxr-xr-x 1 gs813 197609  0 Aug 22 16:10 .git/
-rw-r--r-- 1 gs813 197609 67 Aug 22 16:10 hello.txt

gs813@DESKTOP-4A8Q9J9 MINGW64 /d/VirtualBox_share_folder/synGit01/gitHome (master)
$ cat hello.txt
hello git!
update content!!
on synGit01
confirmed on syncGit02
```

그러면 이전과 비슷한 맥락처럼, 다른 로컬 저장소에서 수정된 내용을 확인해볼 수 있게 된다!

🌟 이렇게 되면, 협업 뿐 아니라 백업까지도 편리하게 관리할 수 있다!

🌟 어떤 작업을 하기 전에 반드시 로컬 저장소에서 `git pull`로 먼저 동기화하는 것이 바람직하다! 그 후에 push 해도 안늦다!!