# git switch

작업하고 있는 브랜치를 전환하는 명령어로 [git 2.23.0](https://github.com/git/git/blob/master/Documentation/RelNotes/2.23.0.txt) 버전에서 추가됐다. 기존의 `git checkout` 명령어의 브랜치 전환 기능이 분리됐다.

```
git switch 브랜치명
```

새로운 브랜치를 생성하면서 전환할 수 있다.

```
git switch -c 브랜치명
```

위에서는 `HEAD`에서 브랜치가 생성된다. 특정 커밋이나 브랜치에서 브랜치를 생성하고 싶다면 브랜치명 뒤에 지정할 수 있다.

```
git switch -c 브랜치명 1234abc
```

