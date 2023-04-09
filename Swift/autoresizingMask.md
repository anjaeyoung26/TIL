## AutoresizingMask

`superview`의 `bounds`가 변경될 때, 현재 뷰의 크기를 조정하는 방법을 결정하는 정수형 bit mask이다.

```swift
public struct AutoresizingMask : OptionSet {
  public init(rawValue: UInt)
  public static var flexibleWidth: UIView.AutoresizingMask { get }
  public static var flexibleHeight: UIView.AutoresizingMask { get }
  public static var flexibleTopMargin: UIView.AutoresizingMask { get }
  public static var flexibleBottomMargin: UIView.AutoresizingMask { get }
  public static var flexibleLeftMargin: UIView.AutoresizingMask { get }
  public static var flexibleRightMargin: UIView.AutoresizingMask { get }
}
```

`UIView`를 상속받는 모든 클래스는 `autoresizingMask`라는 프로퍼티를 가지고 있다. 기본값은 `none`으로, `superview`의 `bounds`가 변경되어도 리사이징되지 않는다. 또한 `AutoresizingMask` 구조체는 `OptionSet` 프로토콜을 채택하고 있으므로, 하나 이상의 옵션들을 설정할 수 있다.

```swift
mySubview.autoresizingMask = [.flexibleWidth, .flexibleTopMargin]
```

만약 `[.flexibleLeftMargin, .flexibleRightMargin]`과 같이 상충되는 옵션을 사용하면 아무것도 설정하지 않는 것과 같다. AutoresizingMask는 애플이 만든 여러 가지 레이아웃이 있는데, 이 중 원하는 레이아웃을 제공하지 않는다면 `layoutSubviews()`를 오버라이딩하여 하위 뷰를 더 정확하게 배치할 수 있다.

### flexibleWidth

`superview`와의 left, right margin이 유지되어서 width가 변한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/190974494-f391a6aa-bf54-489c-9b2d-71c7c149ec36.gif">
</p>

### flexibleHeight

`superview`와의 top, bottom margin이 유지되어서 height가 변한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/190975409-a06e698d-be3f-45d8-8fbb-2262e689c506.gif">
</p>

### flexibleLeftMargin

`superview`와의 left margin이 유동적으로 변한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/190975598-2cbd408e-8d04-4997-a7e8-31929679b493.gif">
</p>

### flexibleRightMargin

`superview`와의 right margin이 유동적으로 변한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/190975741-b451541b-1718-4419-99f6-11b000b21b2e.gif">
</p>

### flexbieTopMargin

`superview`와의 top margin이 유동적으로 변한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/190975886-a897b6ee-2156-4112-a164-f5689d605b91.gif">
</p>

### flexibleBottomMargin

`superview`와의 bottom margin이 유동적으로 변한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/190975957-5ae6bdf4-3c60-4948-89ef-d23df4e3c51d.gif">
</p>