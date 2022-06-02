# git commit

현재 로컬 저장소에서 변경한 내용의 스냅샷을 캡처하는 명령어이다.

&nbsp;
## git commit -m "..."

가장 기본적인 형태로 Staging Area에 올라와 있는 변경 사항의 스냅샷을 캡처한다. `-m` 뒤에 커밋에 대한 메시지를 입력한다.

&nbsp;
## git commit -a

별도의 `git add` 명령어를 거쳐 변경 사항을 Staging Area에 올리지 않고, Working Directory에서 바로 스냅샷을 캡처한다. 즉 `git add .` -> `git commit -m "..."`의 과정을 한 번에 수행한다. 단, Tracked 상태의 파일을 대상으로만 사용할 수 있다.

&nbsp;
## git commit -am "..."

`-m`과 `-a` 옵션을 합친 형태로, Working Directory에서 변경 사항의 스냅샷을 캡처하면서 편집기를 열지 않고 커밋에 대한 메시지를 입력할 수 있다.

&nbsp;
## git commit --amend

가장 최근 커밋을 수정하는 명령어로 스냅샷을 변경하지 않아도 이전 커밋 메시지를 간단하게 편집하는 데 사용할 수 있다.

```
git commit --amend -m "..."
```

`-m` 옵션을 추가하면 편집기를 열지 않아도 명령줄에서 메시지를 수정할 수 있다.