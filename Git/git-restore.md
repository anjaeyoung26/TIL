# git restore

Staging Area 혹은 Working Directory에 있는 파일을 복구하는 명령어로 [git 2.23.0](https://github.com/git/git/blob/master/Documentation/RelNotes/2.23.0.txt) 버전에서 추가됐다. 기존의 `git checkout` 명령어의 파일 복구 기능이 분리됐다.

&nbsp;
## Unstaging

`git add` 명령어를 통해 Staging Are에 올라간 파일을 다시 Working Directory로 내린다. 파일의 변경사항은 `Unstaged` 상태로 남아있다.

```
git restore --staged 파일명
```

2.23.0 버전 이전에는 `git reset` 명령어를 사용했다.

```
git reset HEAD 파일명
```

&nbsp;
## Unmodified

Working Directory에 있는 파일을 복구한다. 파일의 변경사항은 삭제된다.

```
git restore 파일명
```

2.23.0 버전 이전에는 `git checkout` 명령어를 사용했다.

```
git checkout -- 파일명
```

&nbsp;
## 특정 커밋 버전으로 복구

파일을 특정 커밋 버전으로 복구한다.

```
git restore --source=커밋ID 파일명
git restore --source=HEAD~2 파일명
```

