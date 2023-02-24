# XCFramework란?

XCFramework는 WWDC19의 [Binary Frameworks in Swift](https://developer.apple.com/videos/play/wwdc2019/416/) 세션에서 공개된 새로운 포멧으로 여러 Framework를 묶어 배포할 수 있다. Xcode 11 버전부터 지원되며, Xcode가 지원하는 모든 플랫폼(iOS, macOS, tvOS, watchOS)에서 사용할 수 있다. 

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220613182-42a45f6d-24a8-4ce3-8ef3-e399bb4c1a45.jpeg" width="500">
</p>

Dynamic Framework 뿐만 아니라 Static Framework도 헤더와 함께 묶을 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220613519-501d1a51-c496-4d04-994c-9b6987c7abe6.jpeg" width="500">
</p>

&nbsp;
## 왜 만들었을까?

기존에는 멀티 플랫폼을 지원하기 위해서 Universal Framework를 생성했다. Universal Framework는 모든 디바이스에서 필요한 아키텍처를 포함해서 Fat Framework 라고도 한다. 만약 시뮬레이터에서 x86_64, 디바이스에서 arm64 아키텍처가 필요하다면 두 아키텍처를 Universal Framework에 포함시켜야 한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220630684-6a3b2b74-fff1-406d-a5d4-4303f0dfa96d.png" width="400">
</p>

만약 시뮬레이터는 x86_64 아키텍처만 필요해도 arm64 아키텍처가 포함된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220630693-f315c688-28c4-4fd4-b214-f697962cedb4.png" width="400">
</p>

Fat Framework는 용량이 커질뿐만 아니라 필요하지 않은 아키텍처는 앱 배포 시 제외시켜야 한다. 이러한 문제는 해결하기 위해 XCFramework를 사용한다. XCFramework는 각각의 아키텍처를 분리하고 필요한 아키텍처로 빌드한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220701691-a315ecfe-641b-4d00-a71b-f48065776374.png" width="400">
</p>