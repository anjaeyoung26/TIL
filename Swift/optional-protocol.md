## Optional Protocol

일반적으로 프로토콜은 메서드나 프로퍼티 등 프로토콜을 준수하는 타입이 구현해야 하는 요구사항을 정의한다. 하지만 프로토콜은 선택적 요구 사항, 즉 프로토콜을 준수하는 타입이 꼭 구현하지 않아도 되는 요구 사항을 정의할 수 있다. 우리는 알게 모르게 선택적 요구 사항이 포함된 프로토콜을 사용해왔다. 대표적인 예로 `UITableViewDataSource` 프로토콜이 있다.

```swift
public protocol UITableViewDataSource : NSObjectProtocol {
  func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int
  func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell
  optional func numberOfSections(in tableView: UITableView) -> Int
}
```

우리는 테이블 뷰를 사용할 때, `UITableViewDataSource`를 준수하면서 `numberOfRowsInSection`, `cellForRowAt`과 같은 메서드를 구현한 기억이 있을 것이다. 이는 테이블 뷰의 데이터 소스를 구성하면서 필수적으로 구현해야 하는 메서드이다. 반면에 `numberOfSections`는 구현하지 않아도 컴파일러가 아무런 에러를 발생시키지 않는다. 이는 `numberOfSections`의 접두사인 `optional`에서 알 수 있듯이 선택적 요구 사항인 메서드이다. 이처럼 프로토콜의 선택적 요구 사항은 프로토콜을 준수하는 객채에서 필수적으로 구현하지 않아도 된다.

&nbsp;
## 선택적 요구 사항을 정의하는 방법

### 1. @objc

선택적 요구 사항은 `optional` modifier를 접두사로 붙여서 나타내며, 선택적 요구 사항과 이를 정의한 프로토콜은 `@objc` attributes을 붙여야 한다. 이는 Objective-C와 상호 운용되어 Swift 코드는 물론 Objective-C에서도 사용할 수 있도록 알린다.

```swift
@objc protocol SomeProtocol {
  @objc optional func someMethod() -> Int
}
```

위 방법은 `@objc`와 `optional` 만으로 간편하게 선택적 요구 사항을 사용할 수 있지만, 선택적 요구 사항인 메서드와 프로퍼티의 타입이 모두 Objective-C와 상호 운용되야 한다. 이는 `NSObject`를 상속받은 클래스만 프로토콜을 채택할 수 있음을 의미하며, `NSObject`를 상속받지 않은 클래스나 모든 구조체, 열거형에 사용할 수 없다. 따라서 프로토콜의 기능이 매우 한정된 범위로 제한된다.

&nbsp;
### 2. Default implementations

`@objc`를 사용하는 첫 번째 방법이 매우 제한적이므로 두 번째 방법이 많이 선호된다. 이는 프로토콜의 *Extension*을 활용하는 방법으로, 선택적 요구 사항을 만들기 위해 프로토콜의 *Extension*에 기본 구현을 작성한다.

```swift
protocol SomeProtocol {
  func someMethod() -> Int
}

extension SomeProtocol {
  func someMethod() -> Int {
    return 0
  }
}
```

위 방법은 Objective-C와 상호 작용을 하지 않아도 되며, 프로토콜을 모든 클래스, 구조체, 열거형에 사용할 수 있음을 의미한다. 하지만 `someMethod`와 같이 특정 타입을 반환하는 메서드의 경우 *Extension*에 기본값을 반환해야 하는데, 이는 API 설계 중에 실수를 했다는 의미일 수 있다. 아래의 예시를 살펴보자.

```swift
protocol SomeParserDelegate {
  func validate(value: Any) -> Bool
}

extension SomeParserDelegate {
  func validate(value: Any) -> Bool {
    return true
  }
}
```

위 코드에서 `SomeParserDelegate` 프로토콜의 선택적 요구 사항으로 `Any` 타입의 값을 검증하는 `validate` 메서드가 있다. 또한 Extension에서 기본값인 `true`를 반환하여 겉보기에 문제가 없다. 이제 아래의 코드를 살펴보자.

```swift
final class SomeParser {
  weak var delegate: SomeParserDelegate?

  func parse(data: Data) -> [Any] {
    // ...
  }
}
```

`SomeParser`는 데이터를 파싱하고 `delegate`가 `validate`를 구현했다면 검증한 데이터를 반환하고, `validate`를 구현하지 않았다면 파싱한 데이터를 그대로 반환하려고 한다. 하지만 `SomeParserDelegate`의 `validate`에 대한 기본 구현으로 인해, `SomeParser`는 `delegate`가 `validate`를 구현했는지 여부를 알 수 없다. 이는 기본 구현을 통해 선택적 요구 사항을 구현한 프로토콜의 문제점을 보여준다.

&nbsp;
## Reference

- https://stackoverflow.com/questions/24032754/how-to-define-optional-methods-in-swift-protocol
- https://docs.swift.org/swift-book/LanguageGuide/Protocols.html