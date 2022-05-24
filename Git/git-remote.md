# git remote

## 연결된 원격 저장소 확인

```
git remote -v
```

현재 로컬 저장소와 연결되어 있는 원격 저장소의 주소를 표시한다. 만약 연결되어 있는 원격 저장소가 없을 경우 아무것도 표시되지 않는다.

Github의 저장소에 대한 주소:

<img src="https://user-images.githubusercontent.com/61190690/170036173-a8b1c8fa-d091-4091-b05f-6cce0663a7e6.PNG">

&nbsp;
## 원격 저장소와 연결 해제

```
git remote remove <name>
```

원격 저장소와 연결을 해제한다. 

> 원격 저장소의 이름은 기본적으로 `origin`으로 설정된다. 현재 연결되어 있는 원격 저장소의 이름은 `git remote -v`를 통해 확인할 수 있다.

&nbsp;
## 원격 저장소 연결

```
git remote add <name> <url>
```

원격 저장소와 로컬 저장소를 연결한다.

&nbsp;
## 원격 저장소의 이름 재설정

```
git remote rename <old> <new>
```

원격 저장소의 이름을 다시 설정한다.