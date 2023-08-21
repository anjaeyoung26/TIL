# git stash

현재 브랜치에서 커밋하지 않은 변경사항이 있을 때 다른 브랜치로 전환하면 아래와 같은 오류가 발생한다. 다른 브랜치로 전환하기 전에 변경 사항을 커밋하거나 Stash 하라는 오류이다.

```
$ git checkout master
error: Your local changes to the following files would be overwritten by checkout:
        00_topsection/css/meritz.css
Please commit your changes or stash them before you switch branches.
Aborting
```

현재 브랜치에 아직 스테이지에 추가하지 않은(`git add`) Unstaged 상태의 변경 사항들이 있는 경우 임시로 스테이지가 아닌 다른 장소에 저장해놓을 수 있다. Stash를 한 브랜치뿐만 아니라 다른 브랜치에도 적용할 수 있다.

&nbsp;
## 저장

```
git stash
```

저장할 때 이름을 명시하면 이름을 통해 가져올 수 있다.

```
git stash save Title
```

특정 파일만 저장할 수 있다.
```
git stash -- Module/Component/SignupValidator.swift
```

&nbsp;
## 목록 확인

현재 브랜치에 Stash 되어있는 변경사항을 확인할 수 있다.

```
git stash list
```

&nbsp;
## 적용

Stash한 변경사항을 현재 브랜치로 가져온다. 가져온 변경사항은 Unstaged 상태이다.

1. 최근 Stash한 변경사항을 가져온다.


    ```
    git stash apply
    ```

2. "Title" 이름의 Stash를 가져온다.

   ```
   git stash apply Title
   ```

   만약 별도로 이름을 설정하지 않았다면 번호로 가져올 수 있다.

   ```
   git stash apply stash@{0}
   ```

4. 최근 Stash한 변경사항을 가져오고 목록에서 제거한다.

   ```
   git stash pop
   ```

&nbsp;
## 삭제

1. 최근 Stash한 변경사항을 목록에서 제거한다.

   ```
   git stash drop
   ```

2. "Title" 이름의 Stash를 목록에서 제거한다.

   ```
   git stash drop Title
   ```
