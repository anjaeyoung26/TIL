# os_log

[os_log](https://developer.apple.com/documentation/os/os_log)는 통합 로깅 시스템으로, 모든 레벨의 시스템에서 메시징을 캡쳐할 수 있는 고성능 API 이다. 개발자는 os_log를 이용해서 개발이나 운영 시 발생하는 문제점을 추적하거나 모니터링 할 수 있다. os_log는 각 로그 레벨에 따라 메모리와 디스크에 로그를 저장하며, iOS 10.0, macOS 10.12, tvOS 10.0, watchOS 3.0 이상에서 사용할 수 있다. Xcode 14 버전까지는 별도의 콘솔 앱에서 로그를 확인할 수 있었지만, Xcode 15 버전부터는 디버그 콘솔에서 확인할 수 있다. 관련된 내용은 WWDC23의 [Debug with structured logging](https://developer.apple.com/videos/play/wwdc2023/10226/) 동영상을 참고할 수 있다.

&nbsp;
## 사용법

우선 os_log를 사용하기 위해서는 [os](https://developer.apple.com/documentation/os) 프레임워크를 import 해야 한다.

```swift
import os
```

그리고 `os_log` 함수를 사용해서 로깅할 수 있고, 출력되는 로그의 형태는 아래와 같다. [2500:65641]은 의미를 모르겠다.

```
{날짜} {프로젝트명}[2500:65641] {메시지}
```

또한 로그의 레벨을 직접 설정할 수 있고 기본 값은 `os.OSLogType.default`이다.

```swift
os_log("메시지", type: .default)
```

혹은 각 로그 레벨에 맞는 함수를 사용할 수 있다.

```swift
os_log_error("Networking 로그 에러")
os_log_fault("Networking 로그 실패")
// os_log_info
// os_log_debug
```

로그는 콘솔 앱에서 확인할 수 있다.

<p align="center">
<img src="https://github.com/anjaeyoung26/GithubActions/assets/61190690/ee16348a-7861-4b55-ab7c-883410249879">
</p>

시뮬레이터 혹은 디바이스를 선택하면 로그를 확인할 수 있다. 위에서 로깅한 메시지로 검색해서 로그를 확인해보자. '유형' 속성을 보면 각 로그의 레벨에 따른 색깔이 표시되서 쉽게 구분할 수 있다.

<p align="center">
<img src="https://github.com/anjaeyoung26/GithubActions/assets/61190690/1f913489-97ea-42b1-880a-38a4650a3d92">
</p>

&nbsp;
## 하위 시스템과 카테고리

레벨과 더불어 로그를 필터링할 수 있는 '하위 시스템'과 '카테고리'가 있다.

```swift
let osLog: OSLog = .init(subsystem: "com.blahblah", category: "Networking")
os_log("Networking 로그", log: osLog)
```

위와 같이 하위 시스템과 카테고리를 설정하면 Xcode의 디버그 콘솔에 카테고리가 표시된다.

<p align="center">
<img src="https://github.com/anjaeyoung26/GithubActions/assets/61190690/06ce46a5-d368-454c-b5df-679836708342">
</p>

그리고 콘솔 앱에서 로그를 클릭하면 하단에 하위 시스템과 카테고리가 표시된다.

<p align="center">
<img src="https://github.com/anjaeyoung26/GithubActions/assets/61190690/0db3ce5c-6a50-45de-93dc-cbc4cf42b5f8">
</p>

하위 시스템과 카테고리도 마찬가지로 필터링 조건으로 넣을 수 있다.

<p align="center">
<img src="https://github.com/anjaeyoung26/GithubActions/assets/61190690/a5ee10de-56bd-4e3f-937b-35f291f41c90">
</p>

&nbsp;
## Extension 활용

아래와 같이 Extension을 활용해서 도메인 형식으로 사용할 수 있다.

```swift
extension OSLog {
  private static var subsystem = Bundle.main.bundleIdentifier!
  static let networking = OSLog(subsystem: subsystem, category: "Networking")
}

os_log("Networking 로그", log: .network)
```

Xcode 15 버전부터는 디버그 콘솔에서 확인할 수 있다고 하니, 다른 Logger 라이브러리를 도입하기 보다 os_log를 고려해 봐야겠다.