# git branch

## 생성

```
git branch dev
```

```
git checkout -b feature/login dev
```

&nbsp;
## 확인

1. 로컬과 원격 저장소의 모든 브랜치를 확인한다.

    ```
    git branch -a
    ```

2. 로컬 저장소의 모든 브랜치를 확인한다.

    ```
    git branch
    ```

3. 원격 저장소의 모든 브랜치를 확인한다.

    ```
    git branch -r
    ```

    목록 화면에서 다시 되돌아가려면 터미널에 `:q`를 입력한다.

&nbsp;
## 전환

```
git checkout master
```

&nbsp;
## 병합

1. 현재 브랜치에 dev 브랜치를 병합한다.

    ```
    git merge dev
    ```

2. 현재 브랜치에 병합된 브랜치 복록을 나타낸다.

    ```
    git branch --merged
    ```

3. 현재 브랜치에 병합되지 않은 브랜치 목록을 나타낸다.

    ```
    git branch --no-merged
    ```

&nbsp;
## 삭제

브랜치는 태그와 마찬가지로 로컬과 원격 저장소에서 따로 관리된다.

1. 로컬 저장소의 브랜치를 삭제한다. `-d`의 경우 삭제하려는 브랜치가 병합되어 있어야 한다.

    ```
    git branch -d dev
    ```

2. 로컬 저장소의 브랜치를 삭제한다. `-D`의 경우 삭제하려는 브랜치가 병합되어 있지 않아도 강제로 삭제한다.

    ```
    git branch -D dev
    ```

3. 원격 저장소의 브랜치를 삭제한다. 하지만 일반적으로 원격 저장소의 브랜치는 Developer 권한으로 삭제할 수 없어서 거의 사용하지 않는다.

    ```
    git push origin --delete dev
    ```

&nbsp;
## 이름 변경

1. 로컬 저장소에서 브랜치의 이름을 변경한다.

```
git branch -m old_branch new_branch
```

2. 변경된 브랜치의 이름을 원격 저장소로 푸시한다.

```
git push origin :old_branch new_branch
```