## self

`self`는 Java, C++의 `this`와 동일한 키워드이다. 이는 컴파일 타임과 런타임에서 클래스 또는 구조체의 인스턴스를 나타내며, `self` 프로퍼티를 사용하여 인스턴스 메서드 내에서 현재 인스턴스를 참조할 수 있다. Swift에서 `self`가 어떻게 사용되는지 알아보자.

&nbsp;
### 명확성

기본적으로 `self` 키워드는 생략이 가능하다. 하지만 프로퍼티와 인자가 동일한 이름을 가질 때, 둘을 구분하기 위해 `self`를 사용해야 한다. 예를 들어, 아래 코드에서 `init(x:y)`를 살펴보자. `Point` 클래스의 `x`, `y` 프로퍼티와 초기화 시 전달받은 `x`, `y` 매개변수가 동일한 이름을 가지고 있으므로 둘을 구분짓기 위해 `self` 키워드를 사용해야 한다. 반면에 `init()`은 프로퍼티를 구분할 필요가 없으므로 클래스의 `x`, `y`를 나타낼 때 `self` 키워드를 생략할 수 있다.

```swift
class Point {
  var x: Int
  var y: Int

  // 1
  init() {
    x = 0
    y = 0
  }

  // 2
  init(x: Int, y: Int) {
    self.x = x
    self.y = y
  }
}
```

&nbsp;
### 클로저

Swift의 클로저에서 캡처되지 않는 객체의 프로퍼티나 메서드에 접근할 때 `self`를 사용해야 한다. 클로저는 매개변수, 지역 변수 등 클로저에서 사용할 외부의 context를 캡처 리스트에 등록한다. 그런데 캡처 리스트에 등록되지 않는 객체의 프로퍼티에 접근하려면 `self` 키워드를 사용해야 한다는 오류를 한 번 쯤은 봤을 것이다. 이러한 클로저에서 사용되는 `self`는 클로저의 캡처 리스트에 자동으로 추가되는 것으로, 이를 통해 객체의 프로퍼티나 메서드에 접근할 수 있다.

```swift
class Point {
  var x: Int
  var y: Int

  ...

  func updateInBackground() {
    DispatchQueue.global(qos: .default).async {
      x = 5 // Error
      someMethod() // Error
    }
  }

  func someMethod() {
    
  }
}
```

&nbsp;
## Self

`Self`는 인스턴스를 나타내는 `self`와 달리 타입 자체를 의미한다. 예를 들어, 아래의 `squared` 함수의 반환 타입인 `Self`는 `BinaryInteger` 프로토콜을 준수하는 어떠한 타입을 나타낸다. 따라서 `BinaryInteger` 프로토콜을 준수하는 `Int` 타입의 변수가 `squared` 함수를 호출한다면 반환 값은 `Int`가 된다.

```swift
extension BinaryInteger {
  func squared() -> Self {
    return self * self
  }
}
```
