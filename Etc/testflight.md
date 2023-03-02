# TestFlight

앱 스토어에 앱을 출시하기 전에 충분한 테스트를 거쳐야 한다. 앱 스토어 커넥트는 개발자가 테스트 버전을 배포할 수 있도록 TestFlight를 제공한다. 또한 테스터들이 앱에서 수정이 필요한 부분을 개발자에게 전송할 수 있는 피드백 기능을 제공한다. TestFlight를 이용하기 전에, 테스트할 앱을 아카이브하고 앱 스토어 커넥트에 바이너리 파일을 업로드한다. TestFlight로 테스트를 진행하기 위해서 해당 빌드를 심사받아야 한다. 앱을 출시할 때 심사보다는 짧은 시간이 소요된다. TestFlight에 빌드가 업로드되면 테스트할 대상을 초대해야 한다. 테스트할 대상은 테스터라고 부르며 내부 테스터와 외부 테스터가 있다. 우선 내부 테스터에 대해 알아보자.

&nbsp;
## 내부 테스터

앱 스토어 커넥트에서 계정 소유자, 관리자, 앱 관리자, 개발자 또는 마케팅 역할로 등록되어 있는 사용자를 내부 테스터로 초대할 수 있다. **내부 테스터는 최대 100명까지 지정할 수 있다.** 우선 내부 테스터로 초대하기 위해 앱 스토어 커넥트에 사용자를 추가한다. 앱 스토어 커넥트에서 [사용자 및 액세스] 탭으로 이동한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222385287-5445d678-ff72-47f0-a3c9-438f62b8d34e.png" width="350">
</p>

탭 제목 하단의 + 버튼을 클릭해서 예비 테스터의 정보를 입력한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222386280-64ce3941-7371-46f2-a04a-4bdc151234ae.png" width="350">
</p>

입력한 정보는 추후 수정이 가능하니 걱정하지 말자. 하지만 이메일은 앱 스토어 커넥트에 가입할 수 있고 정상적으로 이메일을 수신할 수 있는 주소를 입력해야 한다. 모든 정보를 입력하면 초대 버튼을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222388138-551303c6-b42d-4ff7-a7a5-36ac426ca30d.png" width="500">
</p>

입력한 이메일 주소로 초대 이메일이 전송된다. Accept Invitation을 클릭하면 앱 스토어 커넥트의 계정을 로그인하는 창이 표시된다. 앱 스토어 커넥트의 계정을 로그인하고 초대를 수락한다. 이제 앱 스토어 커넥트의 [TestFlight] - App Store Connect 사용자로 이동해서 테스터 우측의 + 버튼을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222389740-52b9e31d-4afc-486e-8848-7dba15695289.png" width="500">
</p>

App Store Connect 사용자 추가 화면에서 예비 테스터의 계정이 있으면 정상적으로 추가된 것이다.

&nbsp;
## 테스터 그룹

테스터의 그룹을 여러 개 만들고 각 그룹에서 중점적으로 확인하려는 기능에 따라 서로 다른 빌드를 추가할 수 있다. 예를 들어, 새로운 버전에서 A/B 테스트를 진행한다고 가정하자. 우선 앱스토어 커넥트에 A, B 버전에 대한 빌드를 업로드 한다. 이후 두 개의 테스트 그룹을 만들어서 각각의 빌드를 추가하고 테스트를 진행한다. 각 그룹에서는 A, B 버전에서 A/B 테스트가 진행되는 기능을 중점적으로 테스트할 수 있다.

### 테스터 그룹 생성

테스터 그룹은 내부 테스터 그룹과 외부 테스터 그룹을 생성할 수 있는데 본 글에서는 내부 테스터 그룹을 생성하는 방법에 대해 알아본다. 우선 앱 스토어 커넥트의 TestFlight 탭으로 이동한다. 그리고 표시된 내부 테스팅 우측의 + 버튼을 클릭하면 내부 그룹을 생성할 수 있는 팝업이 표시된다. 그룹의 이름을 입력하고 생성 버튼을 클릭한다. 만약 자동 배포 활성화 옵션을 체크하면 TestFlight에 업로드한 모든 앱 버전을 그룹에게 자동으로 공유한다. 그룹에게 공유할 앱 버전을 직접 선택하고 싶다면 체크를 하지 않는다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222412417-3389547a-3253-444c-b1d5-66ae4f433296.png" width="500">
</p>

생성된 내부 테스터 그룹에 테스터를 초대하자. 표시된 테스터 우측의 + 버튼을 클릭하면 테스터로 추가할 계정을 선택하는 팝업이 표시된다. 목록에는 앱 스토어 커넥트에 추가한 사용자들이 표시된다. 초대할 테스터 계정을 체크하고 추가 버튼을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222405394-0c099755-ace9-4ff3-bcda-e8dbdd4da7ad.png" width="500">
</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222405404-21047436-63a5-4df0-a6ff-ddc3d9a99acb.png" width="500">
</p>

테스터 계정에 초대 이메일이 전송되면 진행 상태에 초대됨이라고 표시된다. 초대받은 테스터는 어떻게 해야하는지 궁금하다면 [초대 수락](#초대-수락)을 살펴보자.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222405410-ed1e4339-b58f-446d-99e7-cc27afabdf61.png" width="500">
</p>

이제 그룹에서 테스트할 빌드를 추가한다. 만약 그룹을 생성할 때 자동 배포 활성화를 체크한 경우, 이미 업로드된 빌드가 표시된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222413639-686ae5e0-252a-40f8-acae-4c13bd6d7e7e.png" width="500">
</p>

자동 배포 활성화를 체크하지 않은 경우, 표시된 빌드 우측의 + 버튼을 클릭해서 테스트할 빌드를 추가한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222405413-ddc5e458-9d15-49ec-922b-0feed1e91527.png" width="500">
</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222405420-84606045-ff06-4930-b486-d6e651899cf6.png" width="500">
</p>

해당 빌드에서 테스트할 사항을 테스터에게 알릴 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222405422-d2a9f1f6-760f-4c67-9711-5333f09e1d19.png" width="500">
</p>

테스트할 빌드를 추가하면 테스터 계정으로 초대 이메일이 전송된다.

&nbsp;
## 외부 테스터

외부 테스터는 앱 스토어 커넥트에 추가된 사용자가 아닌 외부의 사용자를 초대해서 테스트를 진행한다. 외부 테스터는 최대 10,000명까지 초대할 수 있다. 내부 테스트와 다르게 외부 테스트는 앱 베타 버전을 외부에 공개하기 때문에 테스트를 시작하기 전에 첫 번째 빌드는 앱 심사에서 승인받아야 한다. 빌드를 그룹에 추가할 때 자동으로 심사 대기 상태가 된다.


### 외부 테스터 그룹 생성

외부 테스트를 진행하기 위해 그룹을 생성한다. 외부 테스팅 우측의 + 버튼을 클릭해서 그룹을 생성할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222423116-51aec930-89e0-490d-a78a-2b2175b70538.png" width="500">
</p>

### 이메일을 사용하여 테스터 초대

외부 테스터 그룹을 생성한 뒤 테스터를 추가해서 이메일을 전송하는 방식으로 초대한다. 내부 테스터는 앱 스토어 커넥트에 추가한 사용자 중에서 선택했지만, 외부 테스터는 이메일, 성, 이름 정보만 입력하고 초대한다. 따라서 아래와 같이 외부 테스터를 추가할 때 역할을 설정하지 못한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222424837-a4f4c85f-079f-4964-951e-e109e548dcf5.png" width="500">
</p>

### 공개 링크를 사용하여 테스터 초대

외부 테스터를 직접 초대하는 방법 외에 공개 링크를 생성해서 공유하는 방식이 있다. 테스트할 빌드를 선택하고 심사가 승인되면 공개 링크가 생성된다. 해당 링크를 외부에 공개하거나 외부 테스터들에게 전달해서 테스트를 진행할 수 있다. 또한 외부 테스터의 수를 제한할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222507145-a2ec8d7f-2d77-47e6-ba53-410abfbd428b.png" width="500">
</p>

&nbsp;
## 초대 수락

테스터는 개발자로부터 초대 이메일을 수신한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222510118-f98fa12d-2c1d-4edb-a54e-855ec2e7a0ee.png" height="500">
</p>

View in TestFlight를 클릭하면 TestFlight가 실행되면서 테스트를 수락할 수 있는 화면이 표시된다. 만약 디바이스에 TestFlight가 설치되어 있지 않다면 앱 스토어 화면이 표시된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222510123-2ff85b4b-082d-457b-a2c0-5abf71492149.png" height="500">
</p>

테스트를 수락하면 빌드를 설치할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222510125-6bdd9ccb-7ed8-4fbb-97a5-d851676bebeb.png" height="500">
</p>

테스트를 진행하면서 수정이 필요한 부분에 대해서 피드백을 전송할 수 있다. 테스트가 시작되면 TestFlight 앱에서 베타 피드백 보내기 버튼이 표시된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222511546-3d3e2112-613c-4b83-93e5-82bb01d2bb6e.png" height="500">
</p>

피드백에 스크린샷을 포함시킬 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222511557-f8db6e20-c112-455d-8c33-b3f2a55702f9.png" height="500">
</p>

피드백 내용을 입력하고 제출하면 개발자에게 전달된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/222511563-5d02c279-69f6-459e-b1e2-86c447fd247f.png" height="500">
</p>