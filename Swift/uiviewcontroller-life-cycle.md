뷰 컨트롤러는 iOS 앱을 구현하기 위한 뼈대를 만드는 역할을 하며, `UIKit` 앱의 전반적인 인터페이스를 관리한다. 뷰 컨트롤러가 관리하는 단일 루트 뷰에는 여러 개의 하위 뷰가 포함될 수 있고, 이러한 뷰 계층 구조와 사용자의 상호작용은 뷰 컨트롤러가 처리한다. 개발자는 사용자에게 더 나은 인터페이스를 제공하기 위해 뷰 컨트롤러의 생명 주기를 이해하고 상황에 따라 적절한 이벤트를 처리해야 한다.

아래의 그림은 뷰 컨트롤러의 life-cycle을 나타낸다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170872970-2aeb0f92-b9b0-4478-ad8f-48fb229fd74c.png" height="600">
</p>

iOS에서 뷰 컨트롤러를 만드는 방법에 따라 호출되는 초기화 메소드가 상이하다.

## init(coder:)

스토리보드를 통해 뷰 컨트롤러를 만드는 경우 호출되는 초기화 메소드다. 매개변수로 `NSCoder` 타입의 `coder`를 전달받는데, 이는 객체를 Byte Stream으로 바꿔 디스크에 저장하거나 네트워크를 통해 전송하는 직렬화 작업을 할 때를 제외하고 사용하지 않는다. `init(coder:)`에서 life-time 동안 필요한 자원들을 할당받지만 아직 뷰 컨트롤러의 루트 뷰가 생성되지 않아서 하위 뷰에 접근을 시도할 수 없다.

&nbsp;
## init(nibName:bundle)

스토리보드가 아닌 .nib를 통해 뷰 컨트롤러를 만드는 경우 호출되는 초기화 메소드다. 매개변수로 뷰 컨트롤로와 연결할 .nib 파일의 이름과 .nib 파일을 검색할 번들을 전달받는다.

&nbsp;
## loadView

컨트롤러가 관리하는 뷰를 생성하는 이벤트다. 이는 뷰 컨트롤러를 programatically한 방식으로 사용할 때 재정의한다. [공식 문서](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621454-loadview)에서 명시된 바와 같이 스토리보드나 .nib 파일로 사용하는 경우 인터페이스 빌더에 있는 뷰를 사용하니 `loadView`를 재정의하지 않는다. 또한 `self.loadView()`와 같이 메소드를 직접 호출하면 안된다.

&nbsp;
## viewDidLoad

뷰 컨트롤러가 뷰 계층 구조를 메모리에 로드한 후에 호출된다. 이 메소드는 뷰 계층 구조가 .nib 파일에서 로드되었거나 메서드에서 프로그래밍 방식으로 생성되었는지 여부에 관계없이 호출된다. `viewDidLoad`가 호출된 시점에서 하위 뷰에 접근을 시도할 수 있다.

### 관련 프로퍼티

`UIViewController` 클래스에는 `viewDidLoad`와 관련된 여러가지 프로퍼티가 있다. 

```swift
var view: UIView!
var viewIfLoaded: UIView?
```

뷰 컨트롤러가 관리하는 루트 뷰가 메모리에 로드된 후에 위의 두 가지 프로퍼티에 설정된다. 만약 `view`가 nil 값을 가질 때 접근한다면 뷰 컨트롤러는 자동으로 `loadView()`를 호출해서 메모리에 로드하고 결과를 반환한다. `viewIfLoaded`는 단순히 메모리에 로드되어 있는 뷰 컨트롤러의 루트 뷰를 반환하고, 아직 로드되지 않은 경우 nil 값을 갖는다. 

```swift
var isViewLoaded: Bool 
```

또한 뷰 컨트롤러의 뷰가 현재 메모리에 로드되어 있는지 여부를 나타내는 `Bool` 타입의 변수가 있다. `isViewLoaded`는 `viewDidLoad` 메소드가 호출된 후 `true`로 설정되어 메모리에 로드되어 있는지 알 수 있다.

&nbsp;
## viewWillAppear, viewDidAppear

`viewDidLoad`가 호출되고 나서 뷰 컨트롤러의 뷰는 화면에 표시되기 위해 뷰 계층에 추가된다. `viewWillAppear` / `viewDidAppear`는 뷰 계층에 추가되거나 다시 화면에 나타나기 전 / 후에 호출된다. 하지만 `UIModalPresentationStyle`에 따라 호출되지 않을 수 있다.

### 예외 케이스

만약 iOS 13부터 추가된 `UIModalPresentationStyle.pageSheet` 형식으로 표시하고 있는 `presentedViewController`가 있을 때, 이를 `dismiss`하고 뷰 컨트롤러가 다시 화면에 나타나도 `viewWillAppear`와 `viewDidAppear`는 호출되지 않는다. 왜냐하면 `UIModalPresentationStyle.fullScreen` 형식으로 표시하고 나면 뷰 컨트롤러의 뷰가 뷰 계층에서 사라지지만, 그 외 형식은 계속 남아있기 때문이다.

> 이는 `viewWillDisappear`, `viewDidDisappear`에서도 동일하다.

&nbsp;
### 관련 프로퍼티

`UIViewController` 클래스에는 뷰 계층이 추가되는 시점, 즉 뷰 컨트롤러가 처음으로 화면에 표시될 때를 캐치할 수 있는 프로퍼티가 있다.

- `isBeingPresented` : 뷰 컨트롤러가 표시되는지 여부를 나타낸다.
- `isMovingToParent` : 네비게이션 컨트롤러에서 push 될 때 `true`로 설정된다.

이 두 가지 프로퍼티는 `viewWillAppear`과 `viewDidAppear` 메소드가 처음 호출될 때 `true`로 설정되고, 이후에는 `true`로 설정되지 않는다. 따라서 뷰 컨트롤러가 처음으로 화면에 표시될 때 원하는 동작을 추가할 수 있다.

```swift
override func viewDidAppear(_ animated: Bool) {
  super.viewDidAppear(animated)
  if isBeingPresented || isMovingToParent {
    
  }
}
```

&nbsp;
## viewWillDisappear, viewDidDisappear

뷰 컨트롤러의 뷰가 뷰 계층에서 제거되기 전과 후에 호출된다. 

### 관련 프로퍼티

`viewWillAppear`, `viewDidAppear`과 마찬가지로 메소드가 처음 호출됐을 때를 캐치할 수 있는 프로퍼티가 있다.

- `isBeingDismissed` : 뷰 컨트롤러가 사라지는지 여부를 나타낸다.
- `isMovingFromParent` : 네비게이션 컨트롤러에서 pop 되어서 `parentViewController`로 이동할 때 호출된다.

이 두 가지 프로퍼티는 `viewWillDisappear`과 `viewDidDisappear` 메소드가 처음 호출될 때 `true`로 설정되고, 이후에는 `true`로 설정되지 않는다. 따라서 뷰 컨트롤러가 처음으로 화면에서 사라질 때 원하는 동작을 추가할 수 있다.

```swift
override func viewDidDisappear(_ animated: Bool) {
  super.viewDidDisappear(animated)
  if isBeingDismissed || isMovingFromParent {
    
  }
}
```

&nbsp;
## didReceiveMemoryWarning

앱이 메모리 경고를 수신하면 뷰 컨트롤러로 전달된다. 이 메서드를 재정의하여 뷰 컨트롤러에서 사용하는 추가 메모리를 해제할 수 있다.

&nbsp;
## viewDidUnload

iOS 5 이하 버전에서는 메모리가 부족할 때 시스템은 필요하지 않는 뷰 컨트롤러의 뷰를 메모리에서 해제한 후 `viewDidUnload` 메소드를 호출했다. 하지만 iOS 6 이상에서는 뷰 컨트롤러의 뷰 및 기타 개체에 대한 참조를 지울 필요가 없어 더 이상 사용되지 않는다.