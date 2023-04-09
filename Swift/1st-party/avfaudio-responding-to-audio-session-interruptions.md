## Responding to Audio Session Interruptions

오디오 세션의 Interruptions란, iPhone의 TV 앱에서 영화를 보는 동안 전화를 받아서 영화의 오디오가 페이드 아웃되고 재생이 일시 중지되며 통화 벨소리가 페이드 인되는 동작이다. 통화를 거부하면 제어가 다시 TV 앱으로 돌아가고 영화의 오디오가 페이드 인되면서 재생이 다시 시작된다. 오디오 세션은 Interruptions가 시작되면 앱에서 적절한 조치를 취할 수 있도록 알림을 Post한다.

### Customize the Interruption Behavior

대부분의 앱은 시스템의 기본 Interruption 동작에 의존한다. 그러나 iOS 14.5부터 앱에서 기본 동작을 사용자 지정하는 방법을 제공한다.

- 최신 iPad 모델은 사용자가 Smart Folio 덮개를 닫을 때 하드웨어 수준에서 내장 마이크를 음소거하는 기능을 제공한다. 이러한 기본 Interruption 동작을 비활성화하기 위해 오디오 세션을 구성할 때 `.overrideMutedMicrophoneInterruption` 옵션을 설정한다. 그러면 앱이 오디오를 재생하고 녹음하는 경우 시스템에서 마이크를 음소거하더라도 계속 재생할 수 있다.

- 기본 동작으로 전화 수신과 같은 시스템 경고는 오디오 세션을 중단한다. 이러한 경우 시스템이 앱의 오디오 세션을 중단하지 않도록 하려면 오디오 세션의 `setPrefersNoInterruptionsFromSystemAlerts(_:)` 메서드를 사용한다.

    ```swift
    func setPrefersNoInterruptionsFromSystemAlerts(_ inValue: Bool) throws
    ```

### Observe Interruptions

시스템이 전화 수신과 같은 이유로 앱의 오디오 세션을 중단할 때 알림을 관찰할 수 있다. 이는 Interruptions로 인해 재생을 일시 중지하는 시기를 알고자 할 때 유용하다. 먼저 오디오 중단을 관찰하려면 `AVAudioSession.interruptionNotification` 알림을 등록한다.

```swift
NotificationCenter.default.addObserver(self,
                                       selector: #selector(handleInterruption),
                                       name: AVAudioSession.interruptionNotification,
                                       object: AVAudioSession.sharedInstance())

@objc func handleInterruption(notification: Notification) {

}
```

### Respond to Interruptions

알림의 `Notification` 개체의 `userInfo`는 Interruptions에 대한 세부 정보가 포함되어 있다. `userInfo` 딕셔너리에서 `AVAudioSession.InterruptionType` 값으로 Interruptions의 유형을 알 수 있다. 이는 Interruptions가 시작 또는 종료 중인지 여부를 나타낸다.

```swift
@objc func handleInterruption(notification: Notification) {
  guard let userInfo = notification.userInfo,
      let typeValue = userInfo[AVAudioSessionInterruptionTypeKey] as? UInt,
      let type = AVAudioSession.InterruptionType(rawValue: typeValue) else {
    return
  }

  switch type {
  case .began:
    // Interruption 시작

  case .ended:
    // Interruption 종료, 필요 시 재생을 다시 시작한다.
    guard let optionsValue = userInfo[AVAudioSessionInterruptionOptionKey] as? UInt else { return }
    let options = AVAudioSession.InterruptionOptions(rawValue: optionsValue)
    if options.contains(.shouldResume) {
      // Interruption가 종료돼서 오디오 세션을 재개할 수 있다.
    } else {
      // Interruption이 종료됐지만 오디오 세션을 재개할 수 없다.
    }

  default: ()
  }
}
```

Interruptions 유형이 `AVAudioSession.InterruptionType.ended`인 경우 `userInfo` 딕셔너리에는 `AVAudioSession.InterruptionOptions`의 값이 포함되어 있으며, 이 값을 사용하여 재생이 자동으로 재개되는지 여부를 결정한다. 만약 `AVAudioSession.InterruptionOptions.shouldResume`가 포함되어 있다면, Interruptions가 종료됨에 따라 오디오 세션을 재개할 수 있음을 나타낸다.