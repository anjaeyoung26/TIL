# git rm

이미 원격 저장소에 푸시한 파일을 다시 삭제하고 싶을 때 로컬에서 해당 파일을 삭제하고 푸시를 해도 원격 저장소에 반영되지 않는다. 아래의 명령어로 상황에 맞춰 파일을 삭제해야 한다.

```
$ git rm <fileName>
```

```
$ git rm --cached <fileName>
```

```
$ git rm --cached -r [폴더명]
```