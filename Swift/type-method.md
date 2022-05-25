Swift의 함수는 앞에 붙는 키워드에 따라 크게 세 가지 종류로 나뉠 수 있다. 각각의 함수에 대해 알아보자.

```swift
class MyClass {
  func instanceFunc() { }
  class func classFunc() { }
  static func staticFunc() { }
}
```

&nbsp;
## Instance method

대부분의 함수는 `instanceFunc`과 같은 형태이다. 이는 `MyClass`의 instance를 통해 호출할 수 있어서 instance 메소드라고 한다.

```swift
let myClass = MyClass()
myClass.instanceFunc()
```

&nbsp;
## Type method

그렇다면 `classFunc`과 `staticFunc`은 무엇일까? 이 둘은 타입 메소드라고 한다. instance를 통해 호출하는 instance 메소드의 개념과 다르게 `MyClass`의 타입과 연관되어 있다. 타입 메소드는 instance를 생성하지 않고 아래와 같이 호출할 수 있다.

```swift
MyClass.classFunc()
MyClass.staticFunc()
```

`classFunc`과 `staticFunc`은 `MyClass` 타입으로 호출할 수 있다는 점이 동일하다. 하지만 둘은 상속에서 차이가 있다. class 메소드는 상속이 가능하지만 static 메소드는 불가능하다. 이는 class라는 키워드에서 힌트를 얻을 수 있는데, 상속이 불가능한 struct, enum 에서는 class 메소드를 정의할 수 없다.

```swift
class MySubclass: MyClass {
  override class func classFunc() { } // O
  override static func staticFunc() { } // X
}
```

&nbsp;
## final class vs static

여기서 한 가지 의문이 들 수 있다. 바로 class 메소드에 `final` 키워드를 붙이면 상속이 불가능한데, 이게 static 메소드와 다를게 뭐냐 라는 의문이다. 결론적으로 둘은 똑같다.