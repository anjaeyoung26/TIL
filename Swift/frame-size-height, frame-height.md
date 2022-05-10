# frame.height vs frame.size.height

`frame`은 `CGRect` 타입으로 좌표와 크기를 나타낸다. 우선 `CGRect` 타입의 구현부를 살펴보자.

```swift
struct CGRect {
	var origin: CGPoint
	var size: CGSize
}
```

각각 좌표와 크기를 나타내는 `origin`, `size`가 정의되어 있다. 보다시피 `frame.size.height`는 위 `size`를 통해 접근할 수 있음을 알 수 있다. 그렇다면 `frame.height`는 어디서 접근할 수 있을까? `CGRect` 타입의 extension을 살펴보자.

```swift
extension CGRect {
	var width: CGFloat { get }
	var height: CGFloat { get }
}
```

extension 안에 넓이와 높이를 나타내는 `width`, `height`가 정의되어 있다. 중요한 점은 `width`와 `height` 모두 `get` 키워드만 요구한다. 즉 외부에서 값을 변경할 수 없다. 따라서 `frame.height`와 `frame.size.height`의 차이는 아래와 같이 정리할 수 있다.

```swift
view.frame.height = 10 // 에러
view.frame.size.height = 10
```