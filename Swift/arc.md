# ARC

Auto Reference Counting의 줄임말로, Swift에서 앱의 메모리 사용을 관리하기 위해 사용한다. ARC가 참조 횟수를 관리하기 때문에 더 이상 사용하지 않는 인스턴스를 메모리에서 해제한다. 따라서 ARC는 개발자가 메모리를 관리해야 하는 짐을 덜어주지만 완전히 책임을 지는 게 아니다. 우선 ARC가 어떻게 동작하는지 알아보자.

&nbsp;
## ARC의 동작 방식

클래스의 새 인스턴스를 만들 때마다 인스턴스의 정보를 담는데 필요한 메모리를 할당한다. 그리고 인스턴스가 더 이상 사용되지 않을 때, ARC는 인스턴스가 차지하고 있는 메로리를 해제해서 공간을 확보한다. 중요한 점은, ARC는 **컴파일 타임**에 컴파일러가 개발자를 대신하여, 메모리를 관리하는 코드를 적절한 위치에 삽입한다. 이러한 ARC의 동작 방식은 **Garbage Collection과 차이가 있다.** Garbage Collection은 **런타임**에 메모리 관리를 수행한다. 프로그램이 실행되는 동안 동적으로 감시를 하므로, Garbage Collection을 위한 메모리가 필요하다. 또한 지속적인 감시를 위해 CPU를 일부 사용해야한다. 하지만 ARC에 비하여 인스턴스가 해제될 확률이 높다.

ARC에서는 아직 사용 중인 인스턴스의 메모리를 해제하지 않기 위해 인스턴스를 참조하는 프로퍼티, 상수 및 변수의 수를 추적한다. 해당 인스턴스에 대한 참조가 하나 이상 존재하는 한 인스턴스의 메모리를 해제하지 않는다. 이를 위해 프로퍼티, 상수 또는 변수에 클래스 인스턴스를 할당할 때마다 해당 프로퍼티, 상수 또는 변수는 인스턴스에 대한 **강한 참조**를 만든다. 인스턴스에 대한 강한 참조가 남아 있는 한 메모리의 해제를 허용하지 않기 때문에 '강한' 참조라고 한다.

&nbsp;
## Strong Reference?

```swift
class Person {
  let name: String
  init(name: String) {
    self.name = name
  }
}
```

실제로 ARC가 어떻게 동작하는지 살펴보기 위한 `Person` 클래스가 있다. 그리고 ARC가 클래스 인스턴스의 참조 횟수에 따라 어떻게 동작을 하는지 알아보기 위해 Person 타입을 갖는 세 개의 변수를 선언한다. 이 변수는 Optional 변수로 초기값으로 `nil`을 가지고 있다.

```swift
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

새로운 `Person` 클래스의 인스턴스를 생성하고 `refercen1`에 할당한다. 이때 `Person` 인스턴스에 대한 강한 참조가 생겨서, ARC는 인스턴스가 메모리에서 유지되도록 한다.

```swift
reference1 = Person(name: "John")
```

`reference1`에 할당한 `Person` 인스턴스를 `reference2`와 `reference3` 변수에 할당하면, `Person` 인스턴스는 3개의 강한 참조가 존재한다.

```swift
reference2 = reference1
reference3 = reference1
```

이제 `reference1`과 `reference2`에 `nil` 값을 설정해서 메모리에서 해제한다. 그러면 `Person` 인스턴스에 대한 2개의 강한 참조가 사라진다. 하지만 `reference3`에 대한 강한 참조가 남아있어 ARC는 `Person` 인스턴스를 메모리에서 해제하지 않는다.

```swift
reference1 = nil
reference2 = nil
```

마지막으로 `reference3`에 대한 강한 참조를 해제하면, ARC는 더 이상 `Person` 인스턴스를 사용하지 않는다고 판단하여 메모리에서 해제한다.

```swift
reference3 = nil
```

&nbsp;
## Strong Reference Cycles

위 예제에서 보다시피 ARC는 기본적으로 클래스 인스턴스에 대한 참조 횟수에 대해 추적한다. 그리고 인스턴스를 더 이상 사용하지 않을 때 인스턴스를 메모리에서 해제한다. 하지만 절대로 메모리에서 해제되지 않는 경우가 있다. 예를들어 클래스의 인스턴스간 강한 참조가 순환 형태로 연결되는 경우인데, 이를 강한 순환 참조라고 한다. 어떻게 강한 참조 순환이 발생하는지 예시를 살펴보자.

```swift
class Person {
  let name: String
  init(name: String) {
    self.name = name
  }
  var apartment: Apartment?
}

class Apartment {
  let unit: String
  init(unit: String) {
    self.unit = unit
  }
  var tenant: Person?
}
```

`Person` 클래스는 `Apartment` 타입의 인스턴스를 소유하고 있고, `Apartment` 클래스는 `Person` 타입의 인스턴스를 소유하고 있다. 그리고 `Apartment`와 `Person` 타입의 변수를 각각 선언한다.

```swift
var john: Person?
var unit4A: Apartment?
```

선언한 변수에 맞는 타입의 인스턴스를 생성한다.

```swift
john = Person(name: "John")
unit4A = Apartment(unit: "4A")
```

이때 `Person`과 `Apartment` 인스턴스에 대한 강한 참조가 생긴다. 지금까지 각 인스턴스의 상태를 그림으로 보면 다음과 같다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170704311-e50e3023-5200-4501-b7b0-1e4212b41b79.png" width="600">
</p>

아직까지 `john`의 `apartment` 변수와 `unit4A`의 `tenant` 변수에는 값이 할당되지 않아 nil 값을 가지고 있다. 이제 `john`의 `apartment`에 `unit4A`를 할당하고, `unit4A`의 `tenant`에 `john`을 할당한다.

```swift
john!.apartment = unit4A
unit4A.tenant = john
```

`Apartment`와 `Person` 인스턴스는 각각 `apartment`, `tenant`가 참조하고 있으므로 참조 횟수는 2가 된다. 이를 그림으로 나타내면 아래와 같다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170714372-7c5da747-658f-438b-a196-0b8935cd4c39.png" width="600">
</p>

이때 `john`과 `unit4A`에 nil을 할당하면 어떻게 될까?

```swift
john = nil
unit4A = nil
```

우리는 `Apartment`와 `Person` 인스턴스는 이제 사용되지 않아 ARC가 메모리에서 해제하기를 기대하지만 그렇지 않다. 왜냐하면 `Apartment`와 `Person` 인스턴스가 서로를 참조하고 있어 여전히 참조 횟수가 1이기 때문이다. 결국 두 인스턴스는 메모리에서 해제되지 않아 메모리 누수가 발생한다. 이렇게 강한 순환 참조가 발생한 상황을 그림으로 나타내면 아래와 같다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170715729-6585b5d6-cc67-4eee-9060-462b8e0f80e4.png" width="600">
</p>

그렇다면 강한 순환 참조는 어떻게 해결해야 할까? 강한 순환 참조를 해결하는데 두 가지 방법이 있다. [weak, unowned](./weak-unowned-reference.md)에서 알아보자.









