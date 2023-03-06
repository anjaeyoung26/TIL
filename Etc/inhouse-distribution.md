# 인하우스 배포

앱을 일반 사용자가 아닌 회사의 직원들에게만 배포해야 하는 경우가 있다. 하지만 [Developer Program](https://developer.apple.com/programs/)으로 사내용 앱을 배포하면 앱 심사에서 리젝될 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223004980-bce16f3c-4e41-4a17-b603-46059e00ccc2.png">
</p>

> 검토 결과 귀사의 앱은 파트너, 클라이언트 또는 직원을 포함한 특정 비즈니스 또는 조직에서 사용하도록 설계되었지만, 귀하는 App Store Connect의 App Store에서 공개 배포를 선택했습니다. 앱 스토어는 일반 사용자가 있는 앱을 위한 것이므로 Apple Developer Program 계정을 통해 사용할 수 있는 다른 배포 옵션을 검토하는 것이 좋습니다. <sub>출처 파파고</sub>

앱 심사를 한 결과 특정 조직에서만 사용하도록 설계되었지만, 모든 사용자를 대상으로 앱 스토어에 출시했다. 따라서 다른 배포 옵션을 고려해보라는 의미이다. Apple은 대규모 조직 내부에서만 사용하는 전용 앱을 개발하여 직원에게 배포할 수 있는 [Enterprise Program](https://developer.apple.com/kr/programs/enterprise/)을 제공하고, Enterprise Program을 통해 배포하는 방식을 **인하우스(In-House)** 배포라고 한다. Developer Program은 구독료만 결제하면 멤버십에 가입할 수 있지만, Enterprise Program은 몇 가지 자격 요건을 충족해야 
한다.

&nbsp;
## [자격](https://developer.apple.com/kr/programs/enterprise/#:~:text=%EC%82%AC%EB%A1%80%EB%A5%BC%20%EC%9C%84%ED%95%9C%20%EA%B2%83%EC%9E%85%EB%8B%88%EB%8B%A4.-,%EC%9E%90%EA%B2%A9,-Apple%20Developer)

1. 직원이 100명 이상이어야 한다. 회사가 아니더라도 학교의 재학생이 사용하는 앱이라면 재학생의 수가 기준이 될 수 있다.
2. 법인이어야 한다. DBA, 허위 사업자, 상표명 또는 지점 이름은 허용되지 않는다.
3. 이 프로그램은 사내에서만 사용하는 전용 앱을 생성하여 조직 내의 직원에게 비공개로 안전하게 배포하기 위한 용도로만 사용해야 한다.
4. 직원만 내부용 앱을 다운로드하고 멤버십 자격 증명 및 자산을 보호할 수 있는 시스템을 갖추고 있어야 한다.
5. Apple의 확인 인터뷰 및 지속적인 평가 절차에 참여하고 통과해야 한다.

&nbsp;
## 멤버십 등록

### 준비 사항

1. **Apple ID**: Enterprise Program에 가입할 애플 계정이다. 계정과 관련해서 몇 가지 고려해야 할 사항이 있다.

    - 웹사이트 도메인의 이름을 사용하는 이메일이어야 한다. 예를 들어, 도메인이 myapp.co.kr 이라면 이메일은 myemail@myapp.co.kr의 형태여야 한다.
    - 이미 Developer Program에 등록했거나, 다른 미디어 유형을 배포하기 위한 앱 스토어 커넥트 계정이 있는 경우, 다른 계정을 사용해야 한다.
    - 이중 인증이 켜져있어야 한다.

2. **D-U-N-S 번호**: [Dun & Bradstreet](https://www.dnb.com/)라는 회사에서 사업자 구분을 하기 위해 기업에 부여하는 코드이다. 기존에 던스 번호가 발급됐는지 확인하기 위해 [D-U-N-S 번호 조회](https://developer.apple.com/enroll/duns-lookup/#!/search)에 접속한다. 고객사의 던스 번호를 대신 발급받거나 Dun & Bradstreet에서 이미 번호를 할당했을 수 있기 때문에 확인이 필요하다. 만약 던스번호를 발급받아야 한다면 Enterprise Program 등록 과정 중에서 가장 오랜 시간이 소요된다.

    2-1. [D-U-N-S 번호 조회](https://developer.apple.com/enroll/duns-lookup/#!/search) 페이지에서 법인에 대한 정보를 입력하고 Continue를 클릭한다. 정보는 홈텍스에서 발급받는 **영문 사업자등록증**을 참고한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223090240-1c2c7a8f-0fb6-4ed1-aa55-8b8b67892800.png">
    </p>

    2-2. 이미 할당된 던스번호가 있다면 애플 계정 이메일로 정보를 전달해준다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223010651-04f753eb-b43a-4830-9412-240a837dc61e.png" width="460">
    </p>

    2-3. 할당된 던스번호가 없다면 이전 페이지에서 입력한 정보를 토대로 던스번호 발급 요청을 할 수 있는 페이지가 표시된다. 주의할 점은 던스번호를 발급받은 뒤 법인에 대한 정보를 수정하려면 Dun & Bradstreet 회사의 승인이 필요하므로 주의깊게 살펴보자.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223011878-2559e446-d935-4be5-aed4-bd7d321c242a.png" width="460">
    </p>

    2-4. 던스번호 발급이 요청되면 아래와 같은 페이지가 표시된다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223010654-b259fc8e-9297-4b9e-940a-5852bb3dcaf7.png" width="460">
    </p>

    2-5. 던스번호 발급을 요청한 당일에 발급 승인 요청 메일이 수신된다. 그리고 1~2일 내로 2-1에서 Your Contact Information에 입력한 전화번호로 070으로 시작하는 국제전화가 온다. 전화는 Dun & Bradstreet 회사의 담당자가 법인의 던스 번호 발급을 확인하기 위해 메일로 전달된 request id를 요청한다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223013268-13fd8d86-c163-45bd-8d1f-862661e227dc.png" width="460">
    </p>

    2-6. 약 일주일 후에 던스번호가 발급됐다는 메일이 수신된다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/223013270-9fcfcd75-0494-40ee-890a-f0a13eb3c06f.png" width="460">
    </p>

3. **웹 사이트**: 인하우스 배포를 하기 위한 웹 사이트이다. 웹 사이트는 반드시 공개적으로 이용이 가능해야 한다. 또한 도메인은 조직과 연관된 이름이어야 한다.

&nbsp;
### 등록 시작

[Enterprise Program 등록 페이지](https://developer.apple.com/kr/programs/enterprise/enroll/)에 접속한다. 그리고 하단의 등록 시작하기를 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223014101-d9c64dcd-c1b1-4473-abcb-7a1f76aa626f.png" width="460">
</p>

신청 양식을 작성한다. 위는 한글로, 아래는 영어로 작성하면 된다. 영문 주소는 네이버에 "서울시 ooo ooo 영문 주소"와 같이 검색할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223026386-0f69cec8-20a3-48a2-966f-8c245c5401d8.png" width="460">
</p>

계정의 유형을 선택한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223021904-e9ae2ccb-5170-46c0-8c31-eff42ce29aa8.jpeg" width="460">
</p>

회사명과 던스 번호를 입력하고 Continue를 클릭한다. 회사명은 던스번호를 발급받을 때 수신된 이메일에 있는 Business Name을 입력한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223015069-969170bd-4e45-45ec-8f75-a784be3b045f.png" width="460">
</p>

마지막으로 몇 가지 정보를 입력하고 Continue를 클릭한다. Confirm your signing authority는 아래를 참고해서 선택한다.

- Owner/Founder: 회사의 대표자 이름으로 만든 계정이라면 선택한다.
- My organization has given me ...: 내가 조직의 대표/설립자, 임원, 선임 프로젝트 관리자이거나 임원에게 법적 권한을 부여 받았다면 선택한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223023210-9a151f85-82ac-41c5-acbb-6ce6dbd2bf71.jpeg" width="460">
</p>

입력한 정보를 보여주고 신청이 완료됐다는 페이지가 표시된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223023215-af772333-1f11-4a3f-815d-a8a0e06b6399.jpeg" width="460">
</p>

다음날에 두 개의 이메일이 수신된다. 하나는 Apple Developer Program에 대한 안내 사항이다. 계정에 대한 소유권 이전, 계정 도움말 등의 내용이 포함된 간단한 안내문이다. 또 다른 하나는 개발자 프로그램 등록 계속하기에 대한 내용이다. 메일로 전달된 링크를 클릭하면 멤버십 계약 동의 페이지가 표시된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223026834-b7a007a5-b413-4f75-9afb-4ab010817715.png" width="460">
</p>

약관에 동의하고 Continue를 클릭하면 결제 페이지가 표시된다. Enterprise Program의 1년 멤버십 비용은 $299 이다. 결제까지 완료하면 다음날 Enterprise Program에 가입된 계정이 활성화된다.

&nbsp;
## 배포

앱을 아카이브한 뒤 Enterprise 옵션으로 배포를 진행한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223070987-cd1df4fd-d4e7-4344-8150-6742e3deb231.png" width="460">
</p>

Include manifest for over-the-airt installation 옵션을 체크하고 Next를 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223071003-b0554c23-a1d0-4fad-8db3-55f81b46d2a0.png" width="460">
</p>

manifest 정보를 입력한다. 추후 manifest.plist 파일을 수정할 수 있으니 대략적으로 입력한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223071642-792d9b39-d296-4851-ad06-06674f31e9d0.png" width="460">
</p>

- Name : 앱 이름
- App URL : ipa 파일의 위치
- Display Image URL : 앱 아이콘 파일의 위치
- Full Size Image URL : Full size 앱 아이콘 파일의 위치

세 URL은 SSL 인증서가 적용된 https여야 한다. 정보를 모두 입력하고 Next를 클릭하면 사이닝을 진행한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223075590-72ca0706-0b85-4f96-adcb-ba3f1ddcced0.png" width="460">
</p>

사이닝이 모두 완료되면 아카이브된 파일에 대한 정보가 표시된다. Export를 클릭해서 파일들을 저장할 위치를 지정한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223076162-db8bab41-4213-4044-814a-6fb2c55fcd1b.png" width="460">
</p>

Export가 완료되면 해당 경로에 Export된 파일들이 보인다. 우리가 눈여겨봐야 할 파일은 `~.ipa`와 `manifest.plist`이다. ipa 파일과 manifest 파일을 서버에 저장하기 위해 서버 개발자에게 전달한다. 그리고 서버에 저장된 위치를 전달받아 manifest.plist 파일에 입력한다.

```
<dict>
  <key>items</key>
  <array>
    <dict>
      <key>assets</key>
      <array>
        <dict>
          <key>kind</key>
          <string>software-package</string>
          <key>url</key>
          <string>서버에 저장된 ipa 파일의 위치</string>
        </dict>
        <dict>
          <key>kind</key>
          <string>display-image</string>
          <key>...</key>
          <string>https://</string>
        </dict>
        <dict>
          <key>kind</key>
          <string>full-size-image</string>
          <key>...</key>
          <string>https://</string>
        </dict>
      </array>
      <key>metadata</key>
      <dict>
        <key>bundle-identifier</key>
        <string>com.ajy.sample</string>
        <key>bundle-version</key>
        <string>1.0.0</string>
        <key>kind</key>
        <string>software</string>
        <key>platform-identifier</key>
        <string>ios</string>
        <key>title</key>
        <string>앱 이름</string>
      </dict>
    </dict>
  </array>
</dict>
```

&nbsp;
### 웹 페이지에서 다운로드

이제 구성원들이 웹 페이지에서 앱을 다운로드 할 수 있는 환경을 구축해야 한다. 웹 페이지는 공개적으로 이용 가능해야 하며, 도메인 이름은 조직과 연관된 이름이어야 한다는 점 기억하자. 아래는 웹 페이지의 다운로드 버튼을 클릭하면 서버에 저장되어 있는 manifest.plist 파일을 실행시키는 코드이다.

```html
<meta charset="utf-8">
<div">
	<a href="itms-services://?action=download-manifest&url=https://myserver/file/manifest.plist"
	style="font-size: 10rem;">Install</a>
</div>
```

manifest.plist 파일에 설정되어 있는 ipa 파일의 위치를 통해서 사용자의 디바이스에 앱이 설치된다.

&nbsp;
### 신뢰하지 않는 기업용 개발자

만약 인하우스 배포를 통해 처음으로 앱을 다운로드 하고 실행시키면 "신뢰하지 않는 기업용 개발자"라는 알럿이 표시되는 경우가 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223095794-8965b0ba-53f4-4f78-8c30-168ba05e4beb.png" width="300">
</p>

인하우스 배포가 불특정 다수를 대상으로 배포하는 방식이므로, 디바이스의 설정에서 해당 기업을 신뢰해야 한다. 디바이스의 [설정] - [일반] - [VPN 및 기기 관리]로 이동한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223095907-05715e8b-c4fa-4701-98b8-a240f3cafc8c.png">
</p>

'기업용 앱' 섹션에서 인하우스 배포를 통해 다운로드 한 앱을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223095911-c35b1877-a56e-4a01-99ff-9c076a42bca1.png">
</p>

'해당 기업을 신뢰함'을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223095912-4cc922d2-cb14-40d5-a16d-3dcf561f2c98.png">
</p>

알럿의 신뢰 버튼을 클릭한다. 그러면 디바이스에서 해당 기업의 앱을 실행시킬 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/223095915-88c6fee2-8fe0-44ef-a115-90a57ffa4e26.png">
</p>
