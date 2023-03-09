## 뷰 컨트롤러의 생명주기

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/224023712-68339b21-d36d-48fc-a2cc-1dc195074b0d.png" height="450">
</p>

뷰 컨트롤러는 iOS 앱을 구현하기 위한 뼈대를 만드는 역할을 하며, `UIKit` 앱의 전반적인 인터페이스를 관리한다. 뷰 컨트롤러가 관리하는 단일 루트 뷰에는 여러 개의 하위 뷰가 포함될 수 있고, 이러한 뷰 계층 구조와 사용자의 상호작용은 뷰 컨트롤러가 처리한다. 개발자는 사용자에게 더 나은 인터페이스를 제공하기 위해 뷰 컨트롤러의 생명주기를 이해하고 상황에 따라 적절한 이벤트를 처리해야 한다. 아래의 그림은 뷰 컨트롤러의 life-cycle을 나타낸다.

&nbsp;
### loadView

뷰 컨트롤러의 `view` 프로퍼티가 요청될 때 호출된다. 만약 뷰 컨트롤러와 관련된 nib 파일이 있는 경우, nib 파일에서 `view`를 로드한다. 그리고 관련된 nib 파일이 없는 경우, 일반적인 `UIView` 객체를 대신 생성한다. 이 메서드는 Interface Builder로 뷰 컨트롤러를 초기화하는 경우 재정의하면 안된다. 즉, 코드로 뷰 컨트롤러를 초기화하는 경우에만 재정의해야 한다. 또한 `loadView`가 호출되는 시점에는 아직 뷰 컨트롤러의 `view` 프로퍼티는 `nil` 값이다.

&nbsp;
### viewDidLoad

뷰 컨트롤러의 `view`가 메모리에 로드된 후 호출된다. 이 메서드는 뷰 컨트롤러가 코드 혹은 Interface Builder 등 어떠한 방식으로 초기화되든 호출된다. 또한 뷰 컨트롤러의 생명주기에서 한 번만 호출되므로, 하위 뷰를 추가하거나 오토 레이아웃의 제약사항을 설정하는 등 추가적인 초기화 작업을 수행한다.'

`UIViewController` 클래스에는 `viewDidLoad`와 관련된 프로퍼티가 있다. 

```swift
var viewIfLoaded: UIView? // 메모리에 로드되어 있는 뷰 컨트롤러의 루트 뷰를 반환하고, 아직 로드되지 않은 경우 nil   
var isViewLoaded: Bool // `view`가 메모리에 로드되어 있는지 여부
```

&nbsp;
### viewWillAppear, viewDidAppear

`viewDidLoad`가 호출되고 나서 뷰 컨트롤러의 뷰는 화면에 표시되기 위해 뷰 계층에 추가된다. `viewWillAppear` / `viewDidAppear`는 뷰 계층에 추가되거나 다시 화면에 나타나기 전 / 후에 호출된다.

`UIViewController` 클래스에는 `viewWillAppear`, `viewDidAppear`와 관련된 프로퍼티가 있다.

```swift
var isBeingPresented: Bool // 뷰 컨트롤러가 표시되는지 여부를 나타낸다.
var isMovingToParent: Bool // 네비게이션 컨트롤러에서 push 될 때 `true`로 설정된다.
```

만약 iOS 13부터 추가된 [`UIModalPresentationStyle.pageSheet`](./uimodalpresentationstyle.md/#pagesheet) 타입으로 표시하고 있는 [`presentedViewController`](./presenting%2C%20presented.md)가 있을 때, 이를 `dismiss`하고 뷰 컨트롤러가 다시 화면에 나타나도 `viewWillAppear`와 `viewDidAppear`는 호출되지 않는다. [`UIModalPresentationStyle.fullScreen`](./uimodalpresentationstyle.md/#fullscreen) 타입으로 표시하고 나면 뷰 컨트롤러의 뷰가 뷰 계층에서 사라지지만, 그 외 타입은 남아있기 때문이다. 이는 `viewWillDisappear`, `viewDidDisappear도 동일하다.

&nbsp;
### viewWillDisappear, viewDidDisappear

뷰 컨트롤러가 화면에서 사라질 때 호출된다. `UIViewController` 클래스에는 `viewWillDisappear`, `viewDidDisappear`와 관련된 프로퍼티가 있다.

```swift
var isBeingDismissed: Bool // 뷰 컨트롤러가 사라지는지 여부를 나타낸다.
var isMovingFromParent: Bool // 네비게이션 컨트롤러에서 pop 되어서 `parentViewController`로 이동할 때 호출된다.
```

&nbsp;
### didReceiveMemoryWarning, viewDidUnload

iOS 5 이하 버전에서는 메모리가 부족할 때 시스템은 필요하지 않는 뷰 컨트롤러의 뷰를 메모리에서 해제한 후 `viewDidUnload` 메소드를 호출했다. 하지만 iOS 6 이상에서는 뷰 컨트롤러의 뷰 및 기타 개체에 대한 참조를 지울 필요가 없어 **더 이상 사용되지 않는다.**