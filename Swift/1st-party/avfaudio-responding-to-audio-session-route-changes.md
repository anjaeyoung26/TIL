## Responding to Audio Session Route Changes

시스템이 오디오 입력 또는 출력을 추가하거나 제거할 때 오디오의 경로가 변경된다. 경로 변경은 사용자가 헤드폰을 연결하거나, Bluetooth LE 헤드셋을 연결하거나, USB 오디오 인터페이스를 뽑는 등 여러 가지 이유로 발생한다. 경로 변경이 발생하면 오디오 세션은 오디오 신호를 다시 라우팅하고 이에 대한 세부 정보가 포함된 알림을 Post한다.

&nbsp;
### Observe Route Changes

오디오 세션에서 Post한 경로 변경 알림을 관찰할 수 있다. 이는 사용자가 헤드폰을 연결할 때 시스템에서 알려주도록 하여 플레이어 인터페이스에 아이콘이나 메시지를 표시할 수 있도록 하려는 경우에 유용할 수 있다. 먼저 오디오 경로 변경 사항을 관찰하려면 `routeChangeNotification` 알림을 등록한다.

```swift
NotificationCenter.default.addObserver(self,
                                       selector: #selector(handleRouteChange),
                                       name: AVAudioSession.routeChangeNotification,
                                       object: nil)
```

알림의 `Notification` 개체의 `userInfo`는 경로 변경에 대한 세부 정보가 포함되어 있다. 사용자가 새 장치를 연결할 때 이유는 `AVAudioSession.RouteChangeReasonAVAudioSession.RouteChangeReason.newDevice`, 사용자가 장치를 제거할 때 이유는 `AvailableAVAudioSession.RouteChangeReason.oldDeviceUnavailable` 이다. 새 장치를 사용할 수 있게 되면 오디오 세션의 `currentRoute`는 현재 라우팅되는 위치를 나타낸다. 또한 사용자가 장치를 제거하면 `userInfo`에서 제거된 장치에 대한 경로가 포함되어 있다.

```swift
@objc func handleRouteChange(notification: Notification) {
  guard let userInfo = notification.userInfo,
      let reasonValue = userInfo[AVAudioSessionRouteChangeReasonKey] as? UInt,
      let reason = AVAudioSession.RouteChangeReason(rawValue: reasonValue) else {
    return
  }

  switch reason {
  case .newDeviceAvailable:
    let session = AVAudioSession.sharedInstance()
    headphonesConnected = hasHeadphones(in: session.currentRoute)

  case .oldDeviceAvailable:
    if let previousRoute = userInfo[AVAudioSessionRouteChangePreviousRouteKey] as? AVAudioSessionRouteDescription {
      headphonesConnected = hasHeadphones(in: previousRoute)
    }

  default: ()
  }
}

func hasHeadphones(in routeDescription: AVAudioSessionRouteDescription) -> Bool {
  return !routeDescription.outputs.filter({$0.portType == .headphones}).isEmpty
}
```

&nbsp;
### Respond to Route Changes

이제 시스템이 Post한 알림을 통해 어떤 장치가 새롭게 연결됐고, 어떤 장치가 연결이 해제됐는지 확인하는 방법을 알게 됐다. 그렇다면 오디오 루트가 변경될 때 어떻게 대응을 해야할까? 멜론, 유튜브 뮤직, 스포티파이와 같은 대부분의 앱은 [iOS Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/platforms/designing-for-ios)의 *Audio Session Programming Guide*의 권장 사항에 따라 오디오 루트가 변경될 때 아래와 같이 처리한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/178384206-8df86bd6-1050-437e-bd9a-0d37e415adb3.png" height="400">
<sub>https://developer.apple.com/library/archive/documentation/Audio/Conceptual/AudioSessionProgrammingGuide</sub>
</p>

- **사용자가 한 쌍의 유선 또는 무선 헤드폰을 연결 :** 사용자는 현재 미디어를 일시 중지 없이 계속 재생되기를 기대하기 때문에 오디오 재생이 계속되어야 한다.
- **사용자가 헤드폰의 연결 해제 :** 듣고 있는 내용을 다른 사람과 자동으로 공유하고 싶지 않는다. 앱은 이러한 암시적 개인 정보 보호 요청을 존중해야 하며 오디오 재생을 일시 중지해야 한다.
- 녹음 중이라면 연결된 장치와 상관없이 오디오 루트가 변경될 때 녹음을 중지한다.

> `AVPlayer`는 자동으로 오디오 세션을 모니터링하고 경로 변경에 적절하게 응답한다. 사용자가 헤드폰을 연결하면 예상대로 재생이 계속된다. 헤드폰을 분리하면 재생이 자동으로 일시 중지된다.
