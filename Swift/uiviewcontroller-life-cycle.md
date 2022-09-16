# 뷰 컨트롤러의 생명주기

뷰 컨트롤러는 iOS 앱을 구현하기 위한 뼈대를 만드는 역할을 하며, `UIKit` 앱의 전반적인 인터페이스를 관리한다. 뷰 컨트롤러가 관리하는 단일 루트 뷰에는 여러 개의 하위 뷰가 포함될 수 있고, 이러한 뷰 계층 구조와 사용자의 상호작용은 뷰 컨트롤러가 처리한다. 개발자는 사용자에게 더 나은 인터페이스를 제공하기 위해 뷰 컨트롤러의 생명주기를 이해하고 상황에 따라 적절한 이벤트를 처리해야 한다. 아래의 그림은 뷰 컨트롤러의 life-cycle을 나타낸다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170872970-2aeb0f92-b9b0-4478-ad8f-48fb229fd74c.png" height="450">
</p>

&nbsp;
## [loadView](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621454-loadview)

뷰 컨트롤러의 `view` 프로퍼티가 요청될 때 호출된다. 만약 뷰 컨트롤러와 관련된 nib 파일이 있는 경우, nib 파일에서 `view`를 로드한다. 그리고 관련된 nib 파일이 없는 경우, 일반적인 `UIView` 객체를 대신 생성한다. 이 메서드는 Interface Builder로 뷰 컨트롤러를 초기화하는 경우 재정의하면 안된다. 즉, 코드로 뷰 컨트롤러를 초기화하는 경우에만 재정의해야 한다. 또한 `loadView`가 호출되는 시점에는 아직 뷰 컨트롤러의 `view` 프로퍼티는 `nil` 값이다.

&nbsp;
## [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload)

뷰 컨트롤러의 `view`가 메모리에 로드된 후 호출된다. 이 메서드는 뷰 컨트롤러가 코드 혹은 Interface Builder 등 어떠한 방식으로 초기화되든 호출된다. 또한 뷰 컨트롤러의 생명주기에서 한 번만 호출되므로, 하위 뷰를 추가하거나 오토 레이아웃의 제약사항을 설정하는 등 추가적인 초기화 작업을 수행한다.

`UIViewController` 클래스에는 `viewDidLoad`와 관련된 여러가지 프로퍼티가 있다. 

```swift
var viewIfLoaded: UIView? // 메모리에 로드되어 있는 뷰 컨트롤러의 루트 뷰를 반환하고, 아직 로드되지 않은 경우 nil

var isViewLoaded: Bool // `view`가 메모리에 로드되어 있는지 여부
```

&nbsp;
## [viewWillAppear](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621510-viewwillappear), [viewDidAppear](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621423-viewdidappear)

`viewDidLoad`가 호출되고 나서 뷰 컨트롤러의 뷰는 화면에 표시되기 위해 뷰 계층에 추가된다. `viewWillAppear` / `viewDidAppear`는 뷰 계층에 추가되거나 다시 화면에 나타나기 전 / 후에 호출된다. 하지만 [`UIModalPresentationStyle`](./uimodalpresentationstyle.md)에 따라 호출되지 않을 수 있다. 또한 `UIViewController` 클래스에는 뷰 계층이 추가되는 시점, 즉 처음으로 화면에 표시될 때를 캐치할 수 있는 프로퍼티가 있다. 해당 프로퍼티들은 `viewWillAppear`과 `viewDidAppear` 메소드가 처음 호출될 때 `true`로 설정되고, 이후에는 `true`로 설정되지 않는다. 따라서 뷰 컨트롤러가 처음으로 화면에 표시될 때 원하는 동작을 추가할 수 있다.

```swift
override func viewDidAppear(_ animated: Bool) {
  super.viewDidAppear(animated)
  if isBeingPresented || // 뷰 컨트롤러가 표시되는지 여부를 나타낸다.
     isMovingToParent {  // 네비게이션 컨트롤러에서 push 될 때 `true`로 설정된다.
    
  }
}
```

만약 iOS 13부터 추가된 [`UIModalPresentationStyle.pageSheet`](./uimodalpresentationstyle.md/#pagesheet) 형식으로 표시하고 있는 [`presentedViewController`](./presenting%2C%20presented.md)가 있을 때, 이를 `dismiss`하고 뷰 컨트롤러가 다시 화면에 나타나도 `viewWillAppear`와 `viewDidAppear`는 호출되지 않는다. [`UIModalPresentationStyle.fullScreen`](./uimodalpresentationstyle.md/#fullscreen) 형식으로 표시하고 나면 뷰 컨트롤러의 뷰가 뷰 계층에서 사라지지만, 그 외 형식은 계속 남아있기 때문이다. 이는 `viewWillDisappear`, `viewDidDisappear`에서도 동일하다.

&nbsp;
## [viewWillDisappear](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621485-viewwilldisappear), [viewDidDisappear](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621477-viewdiddisappear)

뷰 컨트롤러가 화면에서 사라질 때 호출된다. 마찬가지로 [`UIModalPresentationStyle`](./uimodalpresentationstyle.md)에 따라 호출되지 않을 수 있다. `viewWillAppear`, `viewDidAppear`과 마찬가지로 메소드가 처음 호출됐을 때를 캐치할 수 있는 프로퍼티가 있다. 해당 프로퍼티들은 `viewWillDisappear`과 `viewDidDisappear` 메소드가 처음 호출될 때 `true`로 설정되고, 이후에는 `true`로 설정되지 않는다. 따라서 뷰 컨트롤러가 처음으로 화면에서 사라질 때 원하는 동작을 추가할 수 있다.

```swift
override func viewDidDisappear(_ animated: Bool) {
  super.viewDidDisappear(animated)
  if isBeingDismissed ||  // 뷰 컨트롤러가 사라지는지 여부를 나타낸다.
     isMovingFromParent { // 네비게이션 컨트롤러에서 pop 되어서 `parentViewController`로 이동할 때 호출된다.
    
  }
}
```

&nbsp;
## didReceiveMemoryWarning, viewDidUnload

iOS 5 이하 버전에서는 메모리가 부족할 때 시스템은 필요하지 않는 뷰 컨트롤러의 뷰를 메모리에서 해제한 후 `viewDidUnload` 메소드를 호출했다. 하지만 iOS 6 이상에서는 뷰 컨트롤러의 뷰 및 기타 개체에 대한 참조를 지울 필요가 없어 더 이상 사용되지 않는다.