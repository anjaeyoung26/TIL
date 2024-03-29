## clipsToBounds, masksToBounds

```swift
extension UIView {
  open var clipsToBounds: Bool
}

open class CALayer : NSObject, NSSecureCoding, CAMediaTiming {
  open var masksToBounds: Bool
}
```

서브 뷰의 컨텐츠를 현재 뷰의 테두리를 기준으로 자를 것인지 여부를 결정한다. `clipsToBounds`와 `masksToBounds`의 기본 값은 모두 `false`이다. 둘의 값을 변경했을 때 효과는 동일하지만 호출할 수 있는 지점이 다르다. `clipsToBounds`는 바로 접근할 수 있지만, `masksToBounds`는 `CALayer`를 거쳐서 접근할 수 있다.

### true, false 비교

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/168581889-cb3c9be2-1f93-4e92-a1a6-e5f87914b9f2.png">
</p>

위 그림에서 1번은 `true`, 2번은 `false`로 설정했을 때 보여지는 차이다. 1번에서는 검은색 네모가 상위 뷰인 흰색 네모까지 표시된다. 이는 검은색 네모의 컨텐츠가 상위 뷰의 테두리를 기준으로 잘린 것이다. 중요한 점은 상위 뷰의 `frame`이 아닌 `bounds`를 기준으로 삼는다.