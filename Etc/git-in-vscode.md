## Visual Studio Code에서 Git 사용

Visual Studio Code에서 Command + Shift + P 를 입력한다. 그리고 `Shell Command: Install 'code' command in PATH`를 클릭해서 code 명령어를 설치한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/229393265-0cae6ee4-42bc-4ca8-8345-17192120c229.png" width="500">
</p>

터미널을 실행해서 아래의 명령어를 입력한다. Git의 기본 코드 에디터를 Visual Studio Code로 변경하는 명령어이다.

```
git config --global core.editor "code --wait"
```

&nbsp;
### 테스트

버전 관리가 되고 있는 프로젝트에서 아래의 명령어를 입력한다.

```
git rebase -i HEAD~1
```

터미널이 아닌 Visual Studio Code에서 Rebase 할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/229393757-5699213f-b5c7-488b-a584-a08bc021597e.png" width="500">
</p>

