## translatesAutoresizingMaskIntoConstraint

시스템은 `AutoresizingMask`를 Autolayout constraint로 변환하여, 하나의 constraints 세트를 뷰에 적용한다. 이는 뷰의 크기와 위치를 완전히 지정하기 때문에 constraint를 추가할 수 없다. 이와 관련된 하나의 프로퍼티가 있다.

```swift
var translatesAutoresizingMaskIntoConstraints: Bool { get set }
```

이는 뷰의 `AutoresizingMask`가 Autolayout constraints로 변환되는지 여부를 나타내며, 기본값은 `true`이다. Code-base UI로 작업한 사람이라면 익숙한 프로퍼티이다. 뷰의 layout constraints를 코드로 추가하기 위해서 `translatesAutoresizingMaskIntoConstraints`를 `false`로 설정해야 한다.

```swift
myView.translatesAutoresizingMaskIntoConstraints = false
```

이는 뷰를 코드로(=programmatically) 생성하는 경우 `true`로 설정되고, Interface Builder로 추가하는 경우 `false`로 설정된다.

