## Protocol Type의 문제점

아래의 `Shape`는 특정 도형을 그리기 위한 `draw` 함수를 요구하는 프로토콜이다. 그리고 `Triangle` 타입은 `Shape` 프로토콜을 준수하여, 삼각형을 그리는 `draw` 함수가 구현되어 있다. 만약 클라이언트에서 `Triangle` 타입을 사용해도 `size`에 따라 도형을 그리는 기능을 사용할 뿐, 어떻게 도형을 그리는지에 대한 구체적인 정보를 알 수 없고, 알 필요도 없다.

```swift
protocol Shape {
  func draw() -> String
}

struct Triangle: Shape {
  var size: Int
  func draw() -> String {
    var result: [String] = []
    for length in 1...size {
      result.append(String(repeating: "*", count: length))
    }
    return result.joined(separator: "\n")
  }
}
let smallTriangle = Triangle(size: 3)
print(smallTriangle.draw())
// *
// **
// ***
```

또한 제너릭을 사용하여 도형을 세로로 뒤집는 것과 같은 작업을 구현할 수 있다. 아래 코드에서 `flippedTriangle`을 초기화하는 부분을 살펴보면, 매개변수로 `smallTriangle`을 사용한다. 여기서 `FlippedShape` 타입을 선언하기 위해서 필요한 제너릭 타입을 노출하는 문제가 있다.

```swift
struct FlippedShape<T: Shape>: Shape {
  var shape: T
  func draw() -> String {
    let lines = shape.draw().split(separator: "\n")
    return lines.reversed().joined(separator: "\n")
  }
}

let flippedTriangle = FlippedShape(shape: smallTriangle)
print(flippedTriangle.draw())
// ***
// **
// *
```

이제 도형의 모양을 뒤집는 기능과 더불어 두 개의 모양을 수직으로 결합하는 기능을 추가한다.

```swift
struct JoinedShape<T: Shape, U: Shape>: Shape {
  var top: T
  var bottom: U
  func draw() -> String {
    return top.draw() + "\n" + bottom.draw()
  }
}

let joinedTriangles = JoinedShape(top: smallTriangle, bottom: flippedTriangle)
print(joinedTriangles.draw())
// *
// **
// ***
// ***
// **
// *
```

모듈의 기능을 추가할수록 다른 모듈에게 불필요한 세부 사항이 노출된다. 이를 해결하기 위해서 해당 구조체들은 모두 `Shape` 프로토콜을 채택하고 있다는 점을 이용한다. 모듈에서는 `FlippedShape`와 `JoinedShape` 같은 구조체가 아닌 `Shape` 프로토콜을 반환하는 메소드를 구현하여, 모듈의 Public 인터페이스에서 구체적인 세부 사항을 숨긴다.

```swift
func flip<T: Shape>(_ shape: T) -> Shape {
  return FlippedShape(shape: shape)
}

func join<T: Shape, U: Shape>(_ shape1: T, _ shape2: U) -> Shape {
  return JoinedShape(top: shape1, bottom: shape2)
}
```

하지만 프로토콜로 타입을 반환받으면 프로토콜의 일부 인터페이스만 이용할 수 있는 문제가 발생한다. 만약 `Shape` 프로토콜에 `Equatable`을 추가하려고 해도 타입 정보를 이용할 수 없기 때문에 "Binary Operator '==' cannot be applied to two 'any Shape' operands" 에러가 표시된다.

```swift
protocol Shape: Hashable { ... }
extension Shape {
  static func == (lhs: Self, rhs: Self) -> Bool {
    return lhs.hashValue == rhs.hashValue
  }
}

let flippedTriangle = flip(smallTriangle)
let sameThing = flip(smallTriangle)
flippedTriangle == sameThing  // Error
```

또한 프로토콜로 반환한 값은 중첩해서 사용할 수 없다. 이는 프로토콜 타입으로 반환한 값은 해당 프로토콜을 준수하지 않은 것으로 여겨지기 때문에, 컴파일러는 *Protocol 'Shape' as a type cannot conform to the protocol itself* 라는 에러를 발생시킨다.

```swift
let flippedTriangle = flip(smallTriangle)
let original = flip(flippedTriangle) // Error
```

위와 같이 프로토콜을 반환하는 인터페이스는 구현의 세부 사항을 숨김으로써 실제 타입에 대한 정보를 얻을 수 없는 문제가 발생한다. 이러한 문제를 해결하기 위해 Swift 5.1에서는 [*Opaque Types*](https://docs.swift.org/swift-book/LanguageGuide/OpaqueTypes.html), 즉 불투명한 타입이 도입된다.

&nbsp;
## Opaque Type이란?

*Opaque Type*은 *Protocol Type*과 같이 프로토콜을 함수 또는 메서드의 반환 값으로 사용한다. 그렇다면 둘의 차이점은 무엇일까? 우선 위 `flip`, `join`를 *Opaque Type*을 반환하는 메서드로 변경해보자. *Opaque Type*은 'some + 프로토콜'를 반환하는 형태로 사용한다.

```swift
func flip<T: Shape>(_ shape: T) -> some Shape {
  return FlippedShape(shape: shape)
}

func join<T: Shape, U: Shape>(_ shape1: T, _ shape2: U) -> some Shape {
  return JoinedShape(top: shape1, bottom: shape2)
}
```

프로토콜을 반환하는 것에서 *Protocol Type*과 같아 보이지만, *Opaque Type*은 *Protocol Type*과 달리 Type Identity를 보존한다. 그로 인해 *Protocol Type*의 몇 가지 문제점을 보완할 수 있다.

```swift
let flippedTriangle = flip(smallTriangle)
let original = flip(flippedTriangle) // Error X
print(flippedTriangle == original) // Error X
```

&nbsp;
### Opaque Type vs Generic Type

*Opaque Type*은 *Generic Type*과 비슷한 개념이지만 반대의 동작을 한다. *Generic Type*의 경우 호출부에서 구체적인 타입을 지정하고 구현부에서 추상적인 타입이 유지됐다면, *Opaque Type*은 구현부에서 구체적인 타입을 제공하고, 호출부에서 추상적인 타입으로 사용한다. 먼저 generic 타입의 원소를 저장하는 Stack 구조체를 살펴보자.

```swift
struct Stack<Element> {
  var items: [Element] = []

  mutating func push(_ item: Element) {
    items.append(item)
  }

  mutating func pop() -> Element {
    return items.removeLast()
  }
}
```

`Stack`의 구현부에서는 `Element`의 타입을 모르지만, 아래와 같이 `Stack` 구조체를 사용하는 호출부에서 지정한다.

```swift
var intStack = Stack<Int>()
```

이제 Opaque Type을 반환하는 함수를 살펴보자.

```swift
func makeArray() -> some Collection {
  return [1, 2, 3]
}
```

`makeArray` 함수의 내부에서는 반환 타입이 `[Int]`라고 알고 있지만, 아래와 같이 `makeArray` 함수를 호출하는 곳에서는 `Collection` 프로토콜을 준수하는 타입인 것만 알 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/224534705-ed1f50b4-cb69-4ad6-a2c9-80b2db380cc2.png" width="400">
</p>

&nbsp;
### Opaque Type with associated type

만약 Opaque Type이 associated type을 가지고 있다면, 해당 associated type에 대한 정보도 유지된다.

```swift
protocol Container {
  associatedtype Item
  var count: Int { get }
  subscript(i: Int) -> Item { get }
}
extension Array: Container { }
```

`Container`는 프로토콜 내부에 `associatedtype`을 가지고 있기 때문에, 함수의 반환 타입으로 사용할 수 없다. 또한 함수 외부에서 제너릭 타입에 대해 유추할 수 있는 정보가 충분하지 않기 때문에, 제너릭 반환 타입의 제약 조건으로 사용할 수 없다.

```swift
func makeProtocolContainer<T>(item: T) -> Container {
  return [item]
}

func makeProtocolContainer<T, C: Container>(item: T) -> C {
  return [item]
}
```

위와 같은 코드에서 컴파일러가 `Container` 프로토콜의 내부에 있는 `associatetype`을 유추할 수 없기 때문에 에러를 발생시킨다. 이러한 문제는 제너릭이 아닌 *Opaque Type*인 `some Container`를 반환 타입으로 사용하여 해결할 수 있다. *Opaque Type*을 반환하는 함수는 `Container` 타입을 반환하지만 구체적인 타입을 지정하지 않는다.

```swift
func makeOpaqueContainer<T>(item: T) -> some Container {
  return [item]
}
let opaqueContainer = makeOpaqueContainer(item: 12)
let twelve = opaqueContainer[0]
print(type(of: twelve))
// Ints
```

`makeOpaqueContainer`의 매개변수인 `T`는 `Int` 타입으로 유추되며, `opaqueContainer`는 `Int` 타입의 요소를 포함하는 배열이 된다. 마찬가지로 `twelve`는 `Int` 타입으로 유추된다.