# git blame

하나의 파일에 대한 변경 사항을 확인한다.

```
git blame <파일>

1c953704 (anjaeyoung   2023-02-16 17:11:06 +0900   6) - [스크롤 뷰의 사이즈를 정하는 방법](Swift/uiscrollview-size.md)
e6c4724f (anjaeyoung   2022-06-19 00:50:38 +0900   7) - [앱의 생명주기](Swift/app-life-cycle.md)
21aa7e3e (anjaeyoung26 2022-05-28 03:24:31 +0900   8) - [연산 프로퍼티](Swift/computed-properties.md)
```

커밋 아이디, 계정, 날짜, 수정 내용이 출력된다. 만약 계정과 날짜를 표시하고 싶지 않다면 아래의 명령어를 입력한다.

```
git blame -s <파일>

1c953704   6) - [스크롤 뷰의 사이즈를 정하는 방법](Swift/uiscrollview-size.md)
e6c4724f   7) - [앱의 생명주기](Swift/app-life-cycle.md)
21aa7e3e   8) - [연산 프로퍼티](Swift/computed-properties.md)
```

하나의 파일이 아닌 특정 line에 대한 변경 사항을 확인할 수 있다.

```
git blame -L 10,13 README.md
```

위 명령어는 README.md 파일의 11~13번째 라인의 변경 사항만 출력된다.
