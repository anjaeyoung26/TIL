# UIModalPresentationStyle

`UIModalPresentationStyle`은 뷰 컨트롤러를 `present`할 때 사용할 수 있는 Modal 표시 형식이다. 여기서 말하는 Modal이란, 네비게이션 푸시와 같이 완전히 화면을 전환하는 것이 아닌 현재 보고 있는 화면에 띄우는 다른 화면을 뜻한다. Modal은 사용자의 주의나 이목을 끌어야할 때 사용하며, 사용자가 빠르게 처리할 수 있는 단순한 내용을 표현하는 것이 좋다.

&nbsp;
## modalPresentationStyle

```swift
var modalPresentationStyle: UIModalPresentationStyle { get set }

let viewController = ViewController()
viewController.modalPresentationStyle = UIModalPresentationStyle.fullScreen
present(viewController, animated: true)
```

`UIViewController` 클래스에는 Modal 표시 형식을 지정하기 위한 프로퍼티가 있다. iOS 12 버전까지 `UIModalPresentationStyle.fullScreen`이 기본값이었지만, iOS 13부터 새로운 케이스가 추가되어 `UIModalPresentationStyle.automatic`으로 변경 되었다. 시스템은 `modalPresentationStyle`의 값을 통해 어떻게 뷰 컨트롤러를 표시할지 결정한다. 하나의 뷰 컨트롤러를 표시하기 전 `modalPresentationStyle`의 값을 설정하여 표시 형식을 지정할 수 있다.

&nbsp;
## fullScreen

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/175427443-b894dfd3-f043-464b-8765-922084d69ef9.gif" height="400">
</p>

현재 보고 있는 화면을 완전히 덮는 표시 방식이다. iOS 12 버전까지 `modalPresentationStyle`의 기본값이었지만, iOS 13부터는 새로운 케이스가 추가되서 더 이상 기본값이 아니다. `fullScreen`이 어떻게 표시되는지 그림으로 살펴보자. 사용자가 보고 있는 화면을 완전히 덮으면서 표시되는 것을 볼 수 있다.

&nbsp;
## overFullScreen

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/175427431-c773850f-475f-4415-bc83-49abdf6a845b.gif" height="400">
</p>

`overFullScreen`은 겉보기에 `fullScreen`과 동일한 방식으로 표시한다. 하지만 `fullScreen`은 뷰 컨트롤러를 `present`한 뒤, *View Hierachy*에서 `presentingViewController`의 뷰가 제거되지만 `overFullScreen`은 제거되지 않고 남아있다. 실제로 `fullScreen`과 `overFullScreen` 방식으로 표시했을 때 *View Hierachy*를 비교해보자. 

&nbsp;
### vs fullScreen

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173089373-ad666551-0e3a-420d-9d24-8f8f29bf2bad.png" width="500">
<img src="https://user-images.githubusercontent.com/61190690/173089387-2b87c192-392a-414c-a5f8-3509366c4fb0.png" width="500">
</p>

위 그림은 각각 `ViewController1`에서 `ViewController2`를 `fullScreen`과 `overFullScreen` 방식으로 `present`한 뒤 *View Hierachy*를 나타낸다. 첫 번째 그림은 `fullScreen`에 해당하며, `ViewController1`의 뷰가 *View Hierachy*에서 제거되어 보이지 않는다. 두 번째 그림은 `overFullScreen`에 해당하며, `ViewController2`의 뷰가 *View Hierachy*에서 제거되지 않고 남아있다.

중요한 점은 `overFullScreen`으로 표시된 `ViewController2`를 `dismiss` 해도 `ViewController1`의 `viewWillAppear`, `viewDidAppear` 메서드가 호출되지 않는다. `overFullScreen`을 처음 사용한다면 `fullScreen`을 사용했을 때와 라이프 사이클이 다르기 때문에 혼동될 수 있다.

&nbsp;
## currentContext, overCurrentContext

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173079908-29046c5b-3b51-46c8-9656-5c856c569041.png" width="600">
</p>

다른 뷰 컨트롤러의 컨텐츠 위에 표시하는 방식이다. 여기서 '컨텐츠 위'는 뷰 컨트롤러의 `view`를 통해 표시된다는 의미이다. 위 그림에서 `currentContext`가 어떻게 표시되는지 살펴보자. 그림에서 `currentContext` 방식으로 A의 컨텐츠 위에 C를 표시했다. 이때 C의 크기를 살펴보면, A의 콘텐츠 크기에 맞춰서 표시된 것을 볼 수 있다. 만약 A의 콘텐츠 크기가 스크린과 동일하다면 `fullScreen`과 동일하게 표시한다. 또한 `currentContext`와 `overCurrentContext`의 차이는 `fullScreen`과 `overFullScreen`의 차이와 같다.

&nbsp;
### definesPresentationContext

`UIKit`에서 `currentContext`, `overCurrentContext`와 같은 context 기반의 표시 방식이 어떻게 동작하는지 알아보자. `UIKit`은 context 기반의 프레젠테이션이 일어날 때, `presentingViewController`부터 시작하여 뷰 컨트롤러 계층을 거슬러 올라간다. 그리고 `definesPresentationContext`의 값이 `true`인 뷰 컨트롤러를 찾으면 해당 뷰 컨트롤러의 컨텐츠에 표시한다.

만약 뷰 컨트롤러 계층에 있는 모든 뷰 컨트롤러가 `definesPresentationContext` 값이 `false`라면, 뷰 컨트롤러 계층의 root에 있는 뷰 컨트롤러의 컨텐츠에 표시된다. `definesPresentationContext`의 기본값은 `false`이다.

&nbsp;
## pageSheet

`pageSheet`는 스크린의 사이즈에 따라 다르게 표시된다.

| Size | iOS 13- | iOS 13+ |
| --- | --- | --- |
| Compact width, compact height | `fullScreen` | `fullScreen` |
| Compact width, regular height | `fullScreen` | Sheet |
| Regular width, compact height | `fullScreen` | `fullScreen` |
| Regular width, regular height | Sheet avoiding status bar | Centered sheet |

일반적으로 iPhone의 사이즈는 *Compact width, regular height*에 해당한다. 표에서 볼 수 있듯 iOS 12까지는 `fullScreen`과 동일하게 표시되었지만, iOS 13 부터는 아래의 그림과 같이 sheet 형식으로 표시된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173001901-e60cd472-e286-40f8-bf7c-dcdd1972cb30.png" width="500">
</p>

WWDC에서 발표된 내용에 의하면, 이러한 layering 디자인은 사용자가 어플리케이션을 사용하면서 어디에 있는지에 대한 컨텍스트를 제공하며, 상단에 둥근 모양은 ***Interactively dismissed*** 될 수 있음을 나타내는 역할을 한다. *Interactively dismissed*란, 스와이프 액션을 통해 아래로 끌어내려 뷰 컨트롤러를 `dismiss` 한다는 의미이다. 그렇다면 11인치의 iPad에서 어떻게 표시될까? 아래의 그림을 살펴보면, 시스템이 dimming(어둑한) layer를 추가하고, 그 위에 컨텐츠를 표시한다. 컨텐츠는 스크린의 중앙에 위치하며 화면 크기, 방향, 글꼴 크기와 같은 여러가지 요소에 의해 컨텐츠의 크기가 최적화된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173088425-9a243824-a6f3-4d0b-be8a-f005d2f748d0.png" height="500">
</p>

&nbsp;
## formSheet

`pageSheet`와 동일하게 동작하지만, 뷰 컨트롤러의 `preferredContentSize` 값을 설정하여 컨텐츠의 크기를 정할 수 있다.

&nbsp;
## .automatic

iOS 13부터 새롭게 추가된 방식이다. `UIViewController`의 `modalPresentationStyle` 기본값은 iOS 12까지 `fullScreen` 이였지만, iOS 13부터 `automatiac`으로 변경되었다. 이름에서 알 수 있듯 특정한 표시 형식을 나타내는 것이 아닌, `UIKit` 시스템이 적절하게 다른 형식으로 매핑하는 방식이다. `UIKit`은 대부분의 뷰 컨트롤러를 `pageSheet` 방식으로 매핑하며, 일부 시스템 뷰 컨트롤러는 `formSheet` 또는 `fullScreen` 방식으로 매핑할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/172983547-3acf46a7-625a-4ed5-ad91-6b961b913e1a.png" width="500">
<img src="https://user-images.githubusercontent.com/61190690/172983552-2d3b4aa3-5912-4783-8f62-e352cf275ebc.png" width="500">
</p>

WWDC에서 소개한 예시를 살펴보면, `UIImagePickerController`를 통해 포토 라이브러리 혹은 카메라를 표시할 때 `automatic`이 어떻게 동작하는지 알 수 있다. 위 그림을 살펴보면 `UIImagePickerController`의 `modalPresentationStyle`을 따로 지정하지 않았지만, `UIKit` 시스템이 자동으로 판단하여 표시한다.