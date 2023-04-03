# .gitignore

Mac OS, Cocoapods, Xcode 등 개발 환경에 따라 자동으로 생성되는 파일과 같이 git이 트래킹하지 않아도 되는 파일을 명시한다. 파일의 이름뿐만 아니라 확장자를 명시할 수 있다. [gitignore.io](https://www.toptal.com/developers/gitignore)에서 선택한 개발 환경에 알맞은 gitignore 파일을 자동으로 생성할 수 있다. 혹은 버전 관리하고 있는 디렉토리에서 아래의 명령어를 입력해 생성할 수 있다.

```
touch .gitignore
```

주의할 점은 .gitignore에 항목을 추가하기 전에 이미 커밋을 했다면 항목을 추가한 뒤에도 트래킹한다. 해당 파일을 이미 커밋해서 git이 트래킹을 하고 있기 때문이다. 이때 git의 캐시를 비워야 트래킹을 멈춘다.

```
git rm -r --cached .
git add .
git commit -m "메시지"
git push
```