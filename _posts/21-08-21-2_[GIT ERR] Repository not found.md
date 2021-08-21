# [GIT ERR] Repository not found 😭

깃을 차근차근 공부하려고 깃 배쉬를 재밌게 왔다갔다리 하다가..

처음에는 "git pull"을 하라고 해서, 병합시켜주어야 하나..?라고 생각해서 하는데, 깃 인증 창이 뜨는거다..😢 토큰 발급도 해서 자격 증명 관리자에 넣기도 했고, 버튼눌러서 authorization도 했는데.. 무엇이 문제일까..😢

다시 토큰도 발급받고, 레포지토리를 복사떠두고, git init을 해주기도 했다

하지만 그러던 와중에 "Repository not found"가 떴다.. 인증은 된것인가..

아무튼 해당 문제를 찾아보니까 원격 git 저장소 식별문제인 것 같았다! 아래의 방법으로

git remote set-url origin 레포지토리_깃_주소

로 해결할 수 있었다!

[git remote: Repository not found 해결](https://carpet-part1.tistory.com/289)