## 클래스와 구조체

클래스와 구조체는 내부에 속성과 메서드를 정의하여 여러 기능을 수행하며, 일반적으로 프로그램의 코드를 조직화 하기 위해 사용한다. Swift는 다른 프로그래밍 언어와 다르게 Interface 파일과 Implementation 파일을 분리해서 만들 필요가 없다. Swift에서 단일 파일에 구조체 또는 클래스를 정의하면, 다른 코드에서 해당 클래스와 구조체를 사용할 수 있는 인터페이스를 생성해준다. 클래스와 구조체는 객체 지향 프로그래밍(OOP)를 위한 필수 요소로 여겨져 전통적으로 **객체**로 알려져 있다. 그러나 Swift의 클래스와 구조체는 다른 언어보다 기능면에서 훨씬 더 가깝고, 일반적으로 클래스와 구조체의 기능은 해당 클래스와 구조체의 인스턴스에 적용되는 기능을 의미한다. 

### 공통점

Swift에서 클래스와 구조체는 많은 공통점이 있다. 클래스와 구조체에서 아래와 같은 기능을 수행할 수 있다.

- 값을 저장하기 위한 [Properties](https://docs.swift.org/swift-book/LanguageGuide/Properties.html) 정의
- 기능을 제공하기 위한 [Methods](https://docs.swift.org/swift-book/LanguageGuide/Methods.html) 정의
- 특정 값에 접근할 수 있는 [Subscripts](https://docs.swift.org/swift-book/LanguageGuide/Subscripts.html) 정의
- 초기 상태를 설정할 수 있는 [Initializer](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html) 정의
- 기본 구현 이상으로 기능을 확장할 수 있는 [Extensions](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html) 정의
- 특정 종류의 표준 기능을 제공하기 위한 [Protocols](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html) 준수

### 차이점

클래스는 구조체에 없는 추가 기능이 있지만 클래스는 지원하는 기능이 많은 만큼 복잡성이 증가한다. 따라서 Swift의 일반적인 지침으로 구조체를 선호하고 필요할 때 클래스를 사용한다. Swift에서 프로그램을 개발할 때 *구조체와 클래스 중 어떤 것을 선택해야 하는가* 에 대한 공식 문서 [Choosing Between Structures and Classes](https://developer.apple.com/documentation/swift/choosing-between-structures-and-classes)를 참고하자.

- [Inhertitance](https://docs.swift.org/swift-book/LanguageGuide/Inheritance.html)를 통해 한 클래스가 다른 클래스의 특성을 상속할 수 있다.
- [Type Casting](https://docs.swift.org/swift-book/LanguageGuide/TypeCasting.html)을 사용하여 런타임에 클래스 인스턴스의 타입을 확인하고 다른 부모∙하위 클래스로 처리할 수 있다.
- [Deinitialization](https://docs.swift.org/swift-book/LanguageGuide/Deinitialization.html)를 사용하여 클래스의 인스턴스가 할당된 리소스를 해제할 수 있다.
- [Automatic Reference Counting](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html)는 클래스 인스턴스에 대한 둘 이상의 참조를 허용한다.

### 선언 문법

```swift
struct Resolution {
  var width = 0
  var height = 0
}

class VideoMode {
  var resolution = Resolution()
  var interlaced = false
  var frameRate = 0.0
  var name: String?
}
```

클래스와 구조체는 비슷한 선언 문법을 가지고 있다. 이름 앞에 클래스는 `class` 키워드를, 구조체는 `struct` 키워드를 적어서 선언할 수 있다. Swift에서 클래스와 구조체의 이름은 `Int`, `String`과 같이 *UpperCamelCase*에 따라서 지어야 한다.

### 클래스와 구조체의 인스턴스

클래스와 구조체는 인스턴스를 생성하는 구문이 매우 유사하다. 가장 간단한 초기화 구문은 클래스 또는 구조체 이름 뒤에 빈 괄호를 사용하는 것이다. 그러면 모든 프로퍼티가 기본값으로 초기화된 클래스 또는 구조체의 새 인스턴스가 생성된다. 만약 클래스 또는 구조체 내부에 기본값을 갖지 않은 프로퍼티가 있다면, 초기화 시 해당 프로퍼티의 값을 설정해야 한다. 이는 [Initialization](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html)에서 자세히 살펴볼 수 있다. 

```swift
let someResolution = Resolution()
let someVideoMode = VideoMode()
```

### 프로퍼티 접근

점(.) 구문을 통해 클래스 또는 구조체 인스턴스의 프로퍼티에 접근할 수 있다. 만약 `someResolution` 인스턴스를 통해 `Resolution` 구조체의 프로퍼티에 접근하려면 아래와 같은 코드를 작성한다.

```swift
print("The width of someResolution is \(someResolution.width)")
// The width of someResolution is 0
```

점(.) 구문을 사용하여 변수 프로퍼티에 새 값을 할당할 수 있다.

```swift
someVideoMode.resolution.width = 1280
print("The width of someVideoMode is now \(someVideoMode.resolution.width)")
// The width of someVideoMode is now 1280
```

### 구조체의 멤버별 초기화

모든 구조체는 초기화 시 프로핕를 선언할 수 있는 *Memberwise Initializer*가 자동으로 생성되며, 이를 사용하여 새 구조체 인스턴스의 멤버 프로퍼티를 초기화할 수 있다. 아래와 같은 멤버의 초기화는 구조체 내부에 `width`와 `height` 프로퍼티가 정의되어 있다면 자동으로 사용할 수 있다.

```swift
let vga = Resolution(width: 640, height: 480)
```

하지만 구조체와 달리 클래스의 인스턴스는 *Memberwise Initializer*가 자동으로 생성되지 않으며, 이는 [Initialization](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html)에서 자세히 살펴볼 수 있다.

### 값 타입, 참조 타입

클래스와 구조체는 변수나 상수에 값을 할당하거나 함수에 인자로 전달될 때 동작이 다르다. 이는 클래스와 구조체의 가장 중요한 차이점이며, [값 타입과 참조 타입](./call-by-value-reference.md)에서 자세히 살펴볼 수 있다.