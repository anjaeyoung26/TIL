## 인앱결제 SandBox 테스트

인앱결제를 구현한 뒤, 테스트를 진행하고자 한다. 실제 계정으로 진행하면 테스트를 할 때마다 결제가 되므로, 애플에서 제공하는 SandBox 계정을 이용해야 한다. 우선 앱스토어 커넥트에서 SandBox 테스터를 추가할 수 있다. 앱스토어 커넥트 페이지의 상단에 **사용자 및 액세스**에 접근한다. 그리고 **Sandbox 테스터** 탭으로 이동해서 추가할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/197767174-0df88ac7-abca-4d00-b1d3-97ebfb332dac.png">
</p>

테스터 정보는 가상의 이름, 성, 이메일 등으로 입력한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/197767821-9ee6c51b-cc9e-4dd7-86a5-7bd5621c4b44.png" height=450>
</p>

iPhone 기기의 설정으로 이동한다. 설정에서 App Store - 샌드 박스 계정을 클릭해서 로그아웃 한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/197769341-02f2647b-c5d8-42fc-87cf-a0c80feed39e.jpg" height=450>
</p>

이제 테스트할 앱에서 인앱결제를 시도한다. 그러면 앱스토어 계정의 이메일과 비밀번호를 입력하는데, 위에서 생성한 SandBox 계정 정보를 입력한다. 결제를 진행하면 *Environment: SandBox*가 표시되서, 샌드박스 환경에서 인앱결제가 진행됨을 알 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/198166895-5e3aacf5-922d-483b-b815-11345448e941.jpeg">
</p>

