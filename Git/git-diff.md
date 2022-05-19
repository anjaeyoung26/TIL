<img src="https://user-images.githubusercontent.com/61190690/169242257-08145d40-3135-42ab-97af-2778ffa419e4.png">

&nbsp;
## gif diff

Working Directory와 Staging Area 사이의 차이를 확인하기 위한 명령어이다. 

원격 저장소에 아래와 같은 텍스트 파일이 저장되어 있다고 하자.

```
hello
```

텍스트 파일의 "hello" 다음 줄에 "world" 를 추가했다.

```
hello
world
```

그리고 `gif diff` 명령어를 실행하면 아래와 같이 출력된다.

```
diff --git a/text.txt b/text.txt    // 두 파일 사이의 차이점을 출력
--- text.txt                        // 기존 파일
+++ text.txt                        // 수정된 파일
@@ -1 +1,2 @@                       // 기존 파일에서 수정된 라인
    hello
   +world                           // 수정된 내용
```

주의할 점은 변경 사항을 `git add` 명령어를 통해 Staging Area에 옮긴다면 `gif diff` 명령어를 실행해도 출력되지 않는다. 왜냐하면 Workding Directory와 Staging Area에 변경 사항이 모두 반영되어 둘의 차이가 없어졌기 때문이다.

&nbsp;
## git diff HEAD

Working Directory와 Staging Area의 변경 사항을 원격 저장소의 HEAD 커밋과 비교한다.

앞에서 `gif diff` 명령어를 통해 출력되지 않는 상황에서 `git diff HEAD` 명령어를 사용하면 변경 사항이 출력된다.

&nbsp;
## gif diff --staged

원격 저장소의 파일과 Staging Area의 변경 사항을 비교하며, `git diff --cached` 명령어와 동일하다.

`--staged` 뒤에 파일 이름을 추가해서 특정 파일만 비교할 수 있다.

```
git diff --staged text.txt
```

&nbsp;
## 커밋 간의 비교

각 커밋의 id를 입력해서 커밋 간 파일의 상태를 비교한다.

```
gif diff f0cdce5 027f7aa
```

&nbsp;
## 브랜치 간의 비교

브랜치 간 파일의 상태를 비교한다.

```
git diff master develop
git diff master..develop
```