# Xcode Build Configuration

Staging 환경을 분리하거나 무료•유료, 국내•해외에 따라 서버의 API 주소, API 클라이언트 키, 파일 등의 값을 달리해야 할 때가 있다. 이를 Xcode의 Build Configuration, Build Settings, Scheme를 활용해서 해결할 수 있다. Xcode 프로젝트 편집기에서 [Info] - Configurations 항목을 보면 프로젝트를 생성할 때 Xcode가 기본적으로 추가한 Debug, Release가 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221560816-63185219-5d06-4972-8fdd-626c4f37b90b.png">
</p>

그리고 프로젝트의 Build Settings 탭을 보면 Debug, Release 환경에 따라 정의된 설정들을 볼 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221560821-061bc2f0-7081-4077-ac2d-27a6b3019391.png">
</p>

기본적으로 개발할 때 실행되는 빌드 설정은 Debug 이다. 이는 Xcode 상단바 - [Product] - [Scheme] - [Edit Scheme...]에서 Run의 Build Configuration을 보면 Debug가 선택돼있다. 그렇다면 Release는 언제 사용될까?

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221560809-ec98210e-985c-45a1-9b98-be62bf97cd60.png">
</p>

Release는 아카이브와 프로파일링 할 때 사용된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221562515-830580d0-5fc5-498f-8298-c90306299754.png">
</p>

&nbsp;
## 빌드 환경 추가하기

Xcode가 기본적으로 추가한 개발, 운영뿐만 아니라 인하우스 배포에 대한 빌드 환경을 추가하고자 한다. 이전에 확인했던 프로젝트의 [Info] - Configurations 항목에서 +를 클릭한다. 그리고 Duplicate "Debug" Configuration 메뉴를 선택해서 새로운 빌드 환경을 추가하고 "Inhouse"라는 이름으로 변경한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221563417-dec27061-310c-4205-886f-3da50ab3826f.png"> <img src="https://user-images.githubusercontent.com/61190690/221563695-6997e1f8-4541-46cb-9573-03b1935a3ade.png">
</p>

Inhouse 빌드 환경을 생성했으면 Xcode 상단바 - [Product] - [Scheme] - [Manage Schemes...]를 선택한다. 그리고 왼쪽 아래의 +를 클릭해서 Inhouse 빌드 환경에 맞는 스키마를 추가한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221564036-6a4cd798-c9d4-4920-b8d3-437b5dd04e47.png">
</p>

Xcode 상단에 스키마를 선택하는 곳을 클릭하면 방금 생성한 스키마가 보인다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221564153-89a2c01d-3ebe-48d8-8494-6ab005d792a0.png">
</p>

Sample.inhouse 스키마를 선택하고 [Edit Scheme...] 메뉴에서 Build Configuration을 변경한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221564925-8cc0e3b9-73e2-470e-a72e-c1231316dc94.png">
</p>

&nbsp;
## 빌드 환경 분리하기

이제 Debug, Inhouse 환경인 스키마가 각각 존재한다. 만약 개발 환경에 따라 서버의 API 주소를 설정하려면 어떻게 해야될까? 이는 User-Defined Setting을 활용할 수 있다.

### User-Defined Setting

User-Defined Setting은 이름 그대로 Xcode에서 생성된 빌드 설정이 아닌 개발자가 직접 추가한 설정을 의미한다. Build Settings 탭에서 + 버튼을 클릭하고 Add User-Defined Setting 메뉴를 선택한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221566441-bd40a5aa-8cee-42b4-b9bf-ec912ed589d0.png">
</p>

그리고 `API_URL` 라고 이름을 짓는다. 왼쪽에 화살표를 클릭하면 빌드 환경에 따라 다른 값을 설정할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221737611-00437380-677d-4cc3-bfd1-53fbc4e8517f.png">
</p>

User-Defined Setting의 값을 가져오기 위해서 Info.plist에 프로퍼티를 추가한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221736317-c5ce76c3-c4a0-435e-9db5-0e194205da94.png">
</p>

이제 Debug, Inhouse, Release 환경에서 차례대로 빌드해보면 `API_URL` 값이 다른 것을 확인할 수 있다.

```swift
if let urlString = Bundle.main.object(forInfoDictionaryKey: "APIURL") as? String {
  // ...
}
```

&nbsp;
### Bundle Identifier

각 개발 환경에서 Bundle Identifier를 달리하고 싶을 때도 User-Defined Setting을 활용한다. 아래와 같이 User-Defined Setting에 값을 추가한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221743626-9cc9457a-8213-42cb-9e67-19d254378b1e.png">
</p>

[Build Settings] - [Packaging] - Product Bundle Identifier 항목에 `com.sample.$(BUNDLE_IDENTIFIER_SUFFIX)`를 입력하고 엔터를 친다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221743631-cbf590ed-f62d-455d-98bb-044a51cb2ded.png">
</p>

그러면 User-Defined Setting에 추가한 `BUNDLE_IDENTIFIER_SUFFIX`의 값이 각 개발 환경에 맞춰서 자동으로 설정된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221743634-61671991-e22a-4489-bf12-32ce26cc5b60.png">
</p>

참고로 Bundle Identifier의 값이 다르기 때문에, 각 개발 환경을 Run 하면 디바이스에 여러 개의 앱이 설치된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221744862-b2d14e10-0a77-4f72-8201-a10d60580833.png">
</p>

개발 환경에 따라 다른 앱 이름을 사용하고 싶다면 마찬가지로 User-Defined Setting을 활용할 수 있다.

&nbsp;
### 앱 아이콘

위에서 추가한 `BUNDLE_IDENTIFIER_SUFFIX`를 활용해서 개발 환경에 따른 앱 아이콘을 사용할 수 있다. 우선 각 개발 환경에 맞는 아이콘을 에셋에 추가한다. 

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221748525-2746c641-1795-4549-ba06-1185a8c4059b.png">
</p>

중요한 점은 아이콘의 이름을 `AppIcon $(BUNDLE_IDENTIFIER_SUFFIX)`와 같이 개발 환경을 구분할 수 있도록 지어야 한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221748528-146a2d81-0367-4360-af7c-d4c05edecef3.png">
</p>

[Build Settings] - [Asset Catalog Compiler - Options] - Primary App Icon Set Name 항목에 `AppIcon $(BUNDLE_IDENTIFIER_SUFFIX)`를 입력하고 엔터를 친다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221748521-de04af8f-7f9f-4456-82e4-31af28d808d0.png">
</p>

그리고 각 개발 환경을 Run 하면 앱 아이콘이 다른 것을 확인할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221748530-5d83e036-cc42-457c-a158-997dba2a0da9.png">
</p>