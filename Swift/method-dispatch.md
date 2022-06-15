## Method Dispatch

대부분의 객체 지향 언어는 하위 클래스에서 상위 클래스의 메서드와 프로퍼티를 오버라이드할 수 있다. 이때 프로그램은 실제 호출할 함수가 어떤 것인지 결정하는 *Method Dispatch*라는 과정이 필요하다. *Method Dispatch*는 프로그램이 어떤 연산을 실행해야 하는지 결정하는 것을 돕는 메커니즘이다. 기본적으로 프로그램이 결정하지만 메서드를 어떻게 구현하느냐에 따라 성능에 영향을 미칠 수 있다. *Method Dispatch*는 크게 *Static Dispatch*와 *Dynamic Dispatch* 두 종류가 있다.

&nbsp;
## Static Dispatch

*Static Dispatch*는 정적 디스패치로, 변수의 명목상 타입에 맞춰서 메서드와 프로퍼티를 참조한다. 이 경우 참조될 요소를 컴파일 타임에 결정할 수 있기 때문에 컴파일러가 최적화할 수 있는 여지가 많아 속도가 빠르다. 그러나 자식 타입의 요소를 호출하고 싶을 때, 명시적인 타입 캐스팅으로 변수를 자식 타입의 타입으로 만들어야 한다. 따라서 프로그램이 다형성을 활용하기 어렵게 만드는 단점이 있다.

&nbsp;
## Dynamic Dispatch

동적 디스패치는 변수의 실제 타입에 맞춰서 메서드와 프로퍼티를 참조한다. 많은 OOP 기반의 언어가 동적 디스패치를 사용하고 있으며, Objective-C 역시도 동적 디스패치를 사용한다. 이는 런타임에 어떤 메서드를 사용할 지 결정하기 때문에 정적 디스패치에 비해 속도가 느리다. 그렇다면 정적 디스패치보다 속도가 느리지만 대부분의 OOP 언어들이 동적 디스패치를 지원하는 이유가 무엇일까?

바로 정적 디스패츠의 다형성과 관련된 단점으로 인해 동적 디스패치를 지원한다. 다형성은 부모 타입의 함수를 그대로 차용해서 사용하거나 오버라이드할 수 있는 개념이다. 이는 객체를 다양하게 표현할 수 있고 더욱 확장성 있는 프로그램을 작성할 수 있게 한다. 따라서 성능상의 손해를 보더라도 다형성을 고수하기 위해 동적 디스패치를 지원한다.

&nbsp;
### Table Dispatch

테이블 디스패치는 동적 디스패치의 기본적인 구현 방식이다. 테이블 디스패치는 *Virtual Table* 이라고 불리는 테이블을 사용한다. 이는 함수 포인터로 이루어진 배열을 의미하며, Swift에서는 *Witness Table* 라고도 부른다. 모든 자식 타입은 자신이 재정의한 모든 메서드의 함수 포인터를 포함하는 테이블을 가지고 있으며, 자식 타입이 새로운 메서드를 추가하면 해당 메소드에 대한 함수 포인터가 테이블에 추가된다. 컴파일러는 런타임에 이 테이블을 사용하여 어떤 메서드를 호출할지 결정한다. 테이블 디스패치에 대한 예시를 살펴보자:

```swift
class ParentCalss {
  func method1() {}
  func method2() {}
}

class ChildClass: ParentClass {
  override func method2() {}
  func method3() {}
}
```

여기서 컴파일러는 `ParentClass`와 `ChildClass` 각각의 테이블을 생성할 것이다. 정적 디스패치와 달리 컴파일러가 먼저 테이블로부터 함수 포인터에 대한 주소를 읽고 해당 위치로 이동해야 하므로 두번의 연산이 요구된다. 이때 `ParentClass`와 `ChildClass`의 테이블을 살펴보면 아래와 같다. 부모 타입으로부터 상속받은 메서드는 부모 타입과 같은 주소값을 유지하고, 오버라이드한 메서드는 새로운 주소값을 갖는다.

```
|0xA00|ParentClass|    |0xB00|ChildClass|
|0x121|  method1  |    |0x121|  method1 |
|0x122|  method2  |    |0x222|  method2 |
                       |0x223|  method3 |
```

&nbsp;
### Message Dispatch

메시지 디스패치는 테이블 디스패치와 마찬가지로 테이블을 사용한다. 메시지 디스패치는 자신이 오버라이드 하거나 새롭게 정의한 메서드만 테이블에 유지하는 대신, 부모 타입으로의 포인트를 가지고 있어서, 부모 타입의 메서드는 부모 타입에서 찾아서 실행된다. 이러한 방식은 다른 매커니즘보다 유연하여 런타임에 메소드의 구현을 동적으로 변경하는 *Method Swizzling*이 가능하다. 다만 이러한 기능을 제공하기 위해 런타임 라이브러리가 필요한데, Swift는 자체적으로 제공하는 라이브러리가 없으므로 Objective-C의 런타임을 이용해야 한다. 또한 다른 매키니즘에 비해 속도가 느리기 때문에 성능 향상을 위해 캐시를 제공하기도 한다. 메시지 디스패치에 대한 예시를 살펴보자:

```swift
class ParentClass {
  dynamic func method1() {}
  dynamic func method2() {}
}

class ChildClass: ParentClass {
  override func method2() {}
  dynamic func method3() {}
}
```

`dynamic`은 Swift와 Objective-C의 상호운용성을 위해 사용하는 키워드로, 해당 함수는 *'Swift 런타임이 아닌 Objective-C 런타임을 사용할 것이다.'*를 의미한다. 이때 `ParentClass`와 `ChildClass`의 테이블을 살펴보면 아래와 같다.

```
|     |ParentClass| <--¬
|     |   super   |    |   |     |ChildClass|
|0x121|  method1  |    ∟-- |     |   super  |
|0x122|  method2  |        |0x222|  method2 |
                           |0x223|  method3 |
```