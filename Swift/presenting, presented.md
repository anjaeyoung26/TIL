<img src="https://user-images.githubusercontent.com/61190690/167772456-e3c5d25a-420f-4a82-a005-db806ebc07cc.png" width="700" height="500">

`UIViewController` 클래스가 갖고 있는 `UIViewController` 타입의 옵셔널 프로퍼티이다. 이름에 present가 붙어있듯이 `present(_:animated:completion:)` 함수와 관련있다.

&nbsp;
## presentingViewController

```swift
open class UIViewController {
	open var presentingViewController: UIViewController? { get }
}
```

그림에서 VC1는 VC2를 `present(_:animated:completion:)` 함수로 present 하고있다. 이때 VC2의 `presentingViewController`는 VC1이다. 한 가지 주의할 점은 VC1이 네비게이션 컨트롤러고 VC2를 push 했다면 VC2의 `presentingViewController`는 `nil`이다.


&nbsp;
## presentedViewController

```swift
open class UIViewController {
	open var presentedViewController: UIViewController? { get }
}
```

그림에서 VC2는 VC3를 `present(_:animated:completion:)` 함수로 present 하고있다. 이때 VC2의 `presentedViewController`는 VC3이다. 마찬가지로 VC2가 네비게이션 컨트롤러고 VC3를 push 했다면 VC2의 `presentedViewController`는 `nil`이다.