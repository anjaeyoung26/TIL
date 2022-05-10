# git checkout

브랜치 전환과 파일 복구 두 개의 기능을 수행하는 명령어이다. [git 2.23.0](https://github.com/git/git/blob/master/Documentation/RelNotes/2.23.0.txt) 버전에서 브랜치 전환 기능은 [git switch](./git-switch.md) 명령어로, 파일 복구 기능은 [git restore](./git-restore.md) 명령어로 분리됐다.

- checkout : Switch branches and restore working tree files
- switch : Switch branches
- restore : Restore working tree files

&nbsp;
## 브랜치 전환

```
git checkout 브랜치명
```

&nbsp;
## 파일 복구

Working Directory에 있는 파일을 복구한다. 파일의 변경사항은 삭제된다.

```
git checkout -- 파일명
```

