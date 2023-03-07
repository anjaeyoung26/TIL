# Ad-Hoc

Ad-Hoc은 TestFlight와 같이 특정 대상들에게 테스트를 목적으로 빌드를 배포하는 방식이다. TestFlight는 빌드를 업로드하고 심사를 거쳐야 앱 스토어 커넥트에 나타나서 딜레이가 발생한다. 하지만 Ad-Hoc은 빌드를 업로드하면 딜레이 없이 등록된 디바이스에서 빌드를 테스트할 수 있다.

&nbsp;
## 디바이스 등록

Ad-Hoc은 등록된 디바이스에만 앱을 배포할 수 있어서 개발자가 직접 각 디바이스의 UDID를 등록해야 한다. 이는 일부 사용자에게는 불편할 수 있지만, 최초로 디바이스를 등록하면 빠르게 테스트할 수 있는 환경이 만들어져서 그룹 내에서 앱을 배포하고 테스트하는데 효과적인 방법이다. 우선 디바이스를 등록하는 방법에 대해 알아보자. 개발자 계정에 디바이스를 등록하는 방법은 [여기](https://developer.apple.com/documentation/xcode/distributing-your-app-to-registered-devices#Register-devices-automatically-in-Xcode)에서 확인할 수 있다.

&nbsp;
### Xcode에서 자동으로 다바이스 등록

Automatic Signing을 사용하면 Xcode가 연결된 디바이스 또는 Mac을 자동으로 등록한다. Xcode에서 연결된 디바이스를 실행 대상으로 선택하고 앱을 실행(Run)시키면 디바이스가 등록된다.

&nbsp;
### 개발자 계정에 직접 등록

1. [개발자 페이지](https://developer.apple.com)에서 로그인 한다.

2. 계정 탭의 프로그램 리소스에서 Certificates, Identifiers, and Profiles를 선택한다.

3. Devices 탭에서 + 버튼을 클릭한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223391468-058d0ea7-71b9-4a18-a90d-0e4ce48e58cd.png">
    </p>

4. Platform, Device Name, Device ID(UUID)를 입력하고 Continue를 클릭한다. 디바이스의 UUID는 [여기](./device-udid.md)를 참고하자.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223391471-ff43ede6-440b-4707-9801-4bda79ac8c88.png">
    </p>

5. Devices 탭으로 돌아오면 추가된 디바이스를 확인할 수 있다.

&nbsp;
## 배포

웹 페이지에서 배포하는 방식은 [In-house](./inhouse-distribution.md/#배포)와 동일하다. 본 글에서는 작성자가 현업에서 Firebase Distrubution을 통해 QA 버전을 수월하게 배포했던 경험이 있어서 [Firebase Distribution](https://firebase.google.com/docs/app-distribution?hl=ko)으로 Ad-Hoc 배포를 하는 방법에 대해 정리하려고 한다.


1. Firebase Console - [App Distribution] 선택

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223413673-26f16251-0d7f-4f90-a032-62c14c61ab0b.png" height="500">
    </p>

2. [테스터 및 그룹] - 그룹 추가

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223414046-46b9cec1-496b-4ba0-b2f1-e27b39b87945.png" width="500">
    </p>

3. 테스터의 이메일을 입력하고 추가한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223414254-bba553d9-9f0e-4a73-8720-556f477374b6.png" width="500">
    </p>

    직접 이메일을 입력해서 추가하는 방법 외에csv 파일을 생성해서 드래그 앤 드롭으로 테스터를 추가하는 방법이 있다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223414873-4f637a0a-2cc2-42dd-8c92-4074c50c17ad.png">
    </p>

4. Xcode 상단바 - [Product] - Archive

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223412536-61ce2735-1072-4417-918d-b62771b75910.png" width="500">
    </p>

5. 아카이브가 완료되면 Ad-Hoc 옵션으로 Export 한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223413085-af0fde29-7a5d-48e7-b5a5-728bd5a51ca2.png" width="500">
    </p>

6. Export된 ipa 파일을 Firebase Distribution에 업로드 한다. Firebase Console - [출시 버전] 탭에 ipa 파일을 드래그 앤 드롭한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223415239-5c09b52c-b9c3-4c9c-9fa8-b109cf71b8fb.png" width="500">
    </p>

7. 테스터 또는 그룹을 추가한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223416009-f8a352f7-22f7-4865-a8fe-ac061a5d6736.png" width="500">
    </p>

8. 테스터들에게 전달할 사항을 입력하고 배포 버튼을 클릭한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223415857-f3e7c4e5-a36d-4d0b-a2ec-6cce6b580bb5.png" width="500">
    </p>

9. 테스터의 이메일에 초대 메일이 전송된다. Get started를 클릭한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223417096-7825934b-67e9-498b-8553-8986e0200b25.png" width="320">
    </p>

10. 이름, 디바이스의 UDID, 모델, OS를 개발자에게 공유하겠냐는 내용이다. 체크하고 Accept Invitation을 클릭한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223417798-b8d39084-622b-4a0e-8a9f-95789706c12b.png" width="320">
    </p>

    만약 테스터의 디바이스가 등록되어 있지 않다면 약관에 동의한 뒤 개발자에게 테스터의 UUID가 메일로 전송된다. 개발자는 [Certificates, Identifiers & Profiles](https://developer.apple.com/account/resources/certificates/list)에서 테스터의 디바이스를 등록한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223638333-340d7d87-66c5-4f17-973c-54ecf9aa8114.png" width="500">
    </p>

11. Register device를 클릭한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223417104-124f8b56-7697-4f06-8659-fa4cd4657174.jpeg" width="320">
    </p>

12. 테스터의 디바이스에 Firebase Profile을 설치해야 한다. How to Install에 나와있는 가이드를 따라 설치한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223417105-a15f7d1c-2d7d-47f8-8efb-9905bdc3b829.jpeg" width="320">
    </p>

이제 테스터는 디바이스에 앱을 설치해서 테스트를 진행할 수 있다.








