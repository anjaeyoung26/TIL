# Jenkins

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231418353-1432a766-f03c-4b84-93e5-6dd71782baab.png">
</p>

[Jenkins](https://www.jenkins.io/)는 많이 사용되는 오픈 소스, Java 기반의 지속적 통합 툴이다. Jenkins는 무료이며 Windows, macOS 및 기타 운영 체제에서 쉽게 설치할 수 있고 구축, 배포 및 자동화를 지원하는 다양한 플러그인을 제공한다. 또한 사전 빌드 설정에서 Shell 및 Windows 명령 실행을 지원하는 뛰어난 도구이다. 본 글에서는 macOS에서 Jenkins를 설치하는 과정을 알아본다.

&nbsp;
## Jenkins 설치

Jenkins는 macOS에서 Homebrew를 통해 설치할 수 있다. 터미널에서 아래의 명령어를 입력한다.

```
brew install jenkins
```

Homebrew로 Jenkins를 제어할 수 있는 명령어는 세 가지가 있다. Jenkins 설치가 완료되면 시작하자.

```
brew services start jenkins // Jenkins 시작
brew services stop jenkins // Jenkins 중지
brew services restart jenkins // Jenkins 재시작
```

Jenkins가 정상적으로 시작되면 웹 브라우저에서 http://localhost:8080/ 으로 접속한다.

<img src="https://user-images.githubusercontent.com/61190690/231441955-29551b67-4eba-4465-a009-0802275401af.png">

Unlock Jenkins 아래의 문장을 읽어보면 '관리자가 Jenkins를 안전하게 설정할 수 있도록 서버의 로그와 이 파일에 암호를 기록했습니다. 두 위치 중 하나에서 암호를 복사하여 아래에 붙여넣으십시오.'라고 한다. 빨갛게 표시된 주소로 접근해서 암호를 복사하고 입력 칸에 붙여넣기 한다.

<img src="https://user-images.githubusercontent.com/61190690/231443094-db4e22ea-deb2-4b24-9547-1ffaa0e9854c.png">

암호가 확인되면 Jenkins를 커스터마이징 할 수 있는 페이지로 넘어간다. 해당 페이지에서는 원하는 플러그인을 설치할 수 있지만, Jenkins 커뮤니티에서 가장 유용하다고 여겨지는 플러그인을 설치하는 'Install suggested plugins'를 클릭한다.

<img src="https://user-images.githubusercontent.com/61190690/231443741-ca2dd30e-177b-403f-bd58-4bebecd4e437.png">

추천 플러그인을 포함해 Jenkins에서 필요한 항목들이 설치되고 있다. 최하단에서 Jenkins의 버전을 확인할 수 있다. 만약 설치되지 않는 파일이 있다면 방화벽을 살펴보길 바란다.

<img src="https://user-images.githubusercontent.com/61190690/231444733-30453ee6-eca4-4e9f-a6e9-39f4e7808a76.png">

Jenkins가 모두 설치되면 계정 정보를 입력하는 페이지로 넘어간다. 사내에서 CI를 구축하는 거라면 회사 이메일을 입력해야겠지만 필자는 학습 목적으로 개인 정보를 입력했다.

<img src="https://user-images.githubusercontent.com/61190690/231445738-3336cfbd-f122-4d9e-b0ea-75d77ca07b5a.png">

Jenkins의 기본 URL을 변경할 수 있는 페이지로 넘어간다. 나중에 Jenkins Dashboard - [Jenkins 관리] - [System Configuration] - [System] 에서 변경할 수 있다.

<img src="https://user-images.githubusercontent.com/61190690/231447112-9892d8fd-f511-4730-844d-722584805606.png">

Jenkins가 모두 셋업되고 사용할 준비가 됐다. Start using Jenkins를 클릭한다.

<img src="https://user-images.githubusercontent.com/61190690/231447349-50784f3f-1332-49ac-827f-151a6c06bc44.png">

Jenkins Dashboard가 표시된다.