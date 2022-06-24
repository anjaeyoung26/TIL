# UISheetPresentationController

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/175305578-a20f26e4-5fd1-4848-8459-859ea05915b2.gif" height="400">
</p>

[UISheetPresentationController](https://developer.apple.com/documentation/uikit/uisheetpresentationcontroller)는 iPhone의 지도 앱에서 볼 수 있는 시트 스타일의 컨트롤러이다. `UISheetPresentationController`를 통해 시트와 시트 뒤에 있는 콘텐츠와 동시에 상호 작용할 수 있는 *non-modal* 환경을 구축할 수 있다. 이는 iOS 15 버전 이상부터 사용할 수 있으며, 자세한 내용은 WWDC21의 [Customize and resize sheets in UIKit](https://developer.apple.com/videos/play/wwdc2021/10063/) 세션 9:45부터 살펴볼 수 있다. 사용 방법으로는 `UIViewController` 클래스의 `sheetPresentationController`를 통해서 뷰 컨트롤러를 표시하기 전에 시트의 동작과 모양을 설정할 수 있다.

```swift
func showMyViewControllerInACustomizedSheet() {
  let viewControllerToPresent = MyViewController()
  if let sheet = viewControllerToPresent.sheetPresentationController {
    sheet.detents = [.medium(), .large()]
    ...
  }
  present(viewControllerToPresent, animated: true, completion: nil)
}
```

또는 뷰 컨트롤러 내에서 시트에 대해 설정할 수 있다.

```swift
func viewDidLoad() {
  super.viewDidLoad()
  if let sheet = self.sheetPresentationController {
    sheet.detents = [.medium(), .large()]
    ...
  }
}
```

&nbsp;
## Detent

`UISheetPresentationController.Detent`는 시트가 놓일 수 있는 높이를 나타내는 클래스이다. *Detent*를 해석하면 멈춤쇠(?)인데, iPhone의 지도 앱에도 *Detent*가 적용되어 있다. 시트를 어느 정도 위치까지 손가락으로 누르면서 움직였다가 놓으면 시트가 일정 높이로 맞춰진다. 이는 사용자가 자연스러운 동작으로 시트를 사용할 수 있게 하며, 실제로 시트를 사용하고 있는 대부분의 앱이 *Detent*와 같은 기능을 제공한다. 아래의 그림은 iPhone의 지도 앱에서 *Detent*가 어떻게 작용하고 있는지를 나타낸다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/175462570-daa778a5-165d-478e-b86a-e86b94672f01.gif" height="400">
<img src="https://user-images.githubusercontent.com/61190690/175462588-efcca692-3df8-4906-b46b-821b67c250fc.gif" height="400">
</p>


*Detent*는 기본적으로 시스템이 정의한 `large`와 `medium`가 있으며, iOS 16 부터는 `static func custom(identifier:resolver)` 메서드를 통해 커스텀 타입을 정의할 수 있다. 위 그림에서 왼쪽은 `large`, 오른쪽은 `medium` 보다 낮은 높이를 갖는 커스텀 타입이다. `UISheetPresentationController` 클래스의 `detents` 프로퍼티를 통해 원하는 *Detent*를 설정할 수 있다. `detents`는 `UISheetPresentationController.Detent` 값을 포함하는 배열이며, 기본적으로 `large()`를 포함하고 있다. 그리고 최소한 하나의 *Detent*를 포함해야 한다.

```swift
var detents: [UISheetPresentationController.Detent] { get set }
```

중요한 점은 시트를 처음 `present`할 때, `detents`의 앞에 있는 *Detent* 높이에 맞춰 표시된다. 따라서 공식 문서에는 `detents`의 값을 설정할 때 가장 작은 높이에서 가장 큰 높이 순으로 *Detent*를 지정하라고 권장한다. 혹은 `detents`의 순서와 상관없이 특정 *Detent*에 맞춰 표시하고 싶다면 `selectedDetentIdentifier`를 사용한다.

```swift
var selectedDetentIdentifier: [UISheetPresentationController.Detent.Identifier] { get set }
```

&nbsp;
## Grabber

*Grabber*는 시트의 크기를 조정할 수 있음을 나타내는 시각적 어포던스(행동유도성)이다. *Grabber*를 표시하는 것은 시트의 크기를 조정할 수 있는지 확실하지 않거나 시트가 대화식으로 해제할 수 없는 경우에 유용할 수 있다. 만약 *compact-height* 사이즈의 클래스에서 시트가 전체 화면으로 표시되거나 시트 위에 또 다른 시트가 표시되는 경우 시스템은 자동으로 *Grabber*를 숨긴다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/175473380-aabd4255-cb4d-409b-aa8a-6fd298d36be1.png" height="400">
</p>

`prefersGrabberVisible` 프로퍼티를 통해 시트의 *Grabber*를 표시할 지 결정할 수 있다. 기본 값은 `false`로 기본적으로 시트의 *Grabber*는 표시되지 않는다.

```swift
var prefersGrabberVisible: Bool { get set }
```

&nbsp;
## preferredCornerRadius

뷰의 `cornerRadius`를 설정했을 때와 같이 가장자리를 둥글게 만들 수 있다. 기본 값은 nil로 기본적으로 시트는 네모난 모양으로 표시된다. 또한 `preferredCornerRadius`는 가장 맨 앞에 있는 시트에만 효과가 있다.

```swift
var preferredCornerRadius: CGFloat? { get set }
```

아래의 그림은 시트의 `preferredCornerRadius`를 50으로 설정한 경우를 나타낸다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/175474169-bb9699af-4fc9-470b-922d-9c3d99733761.png" height="400">
</p>

&nbsp;
## prefersScrollingExpandsWhenScrolledToEdge

만약 시트에 테이블 뷰가 포함되어 있다면, 시트를 움직이는 제스처로 인해 테이블 뷰가 스크롤되지 않는다. `preferesScrollingExpandsWhenScrolledToEdge`를 `false`로 설정하면 시트가 더 큰 *Detent*로 확장될 수 있는 경우, 시트에서 위로 스크롤하면 *Detent*가 증가하는 대신 시트의 컨텐츠를 스크롤할 수 있다. `preferesScrollingExpandsWhenScrolledToEdge`의 기본값은 `true`로 기본적으로 시트의 컨텐츠 대신 *Detent*가 증가한다. 아래의 그림에서 시트를 위로 스크롤할 때 `preferesScrollingExpandsWhenScrolledToEdge`의 값에 따라 동작의 차이를 살펴보자. 왼쪽 그림은 기본값인 `false`일 때, 오른쪽은 `true`로 설정했을 때의 동작을 나타낸다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/175475380-418b9b53-ad3d-4594-8c32-a7690edd24ce.gif" height="400">
<img src="https://user-images.githubusercontent.com/61190690/175475359-01805d7c-bd2c-452f-8f75-02e7304c5f1c.gif" height="400">
</p>

&nbsp;
## largestUndimmedDetentIdentifier

시트가 표시될 때를 자세히 살펴보면 시트 아래에 생기는 어두운 효과를 볼 수 있다. 이는 시스템이 모든 *Detent*에서 시트 아래에 추가하는 *noninteractive dimming view*이다. 만약 특정 *Detent*에서 해당 효과를 나타내고 싶지 않다면, `largestUndimmedDetentIdentifier`의 값을 설정한다. 이는 시트의 아래를 어둡게 하지 않는 가장 큰 *Detent*를 의미한다. 즉 시트의 현재 높이가 `largestUndimmedDetentIdentifier`에서 설정한 *Detent*의 높이보다 클 때만 어두워지는 효과가 나타난다. 아래의 그림에서 왼쪽은 `largestUndimmedDetentIdentifier`를 설정하지 않았고, 오른쪽은 `medium`으로 설정했을 때의 동작을 나타낸다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/175478414-e6cdf79b-45bf-4195-a79a-4afc9c2b21a8.gif" height="400">
<img src="https://user-images.githubusercontent.com/61190690/175478437-82d1ce64-313f-4c70-9cdc-bd535b32b877.gif" height="400">
</p>