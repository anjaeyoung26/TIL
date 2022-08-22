# 강한 순환 참조의 해결

강한 순환 참조가 발생해 메모리가 누수하는 현상을 해결하는 방법은 두 가지가 있다. `weak` 혹은 `unowned` 참조를 사용하여 인스턴스를 참조할 때 참조 횟수를 증가시키지 않아 강한 순환 참조 문제를 해결할 수 있다. `weak`는 참조하고 있는 인스턴스가 먼저 메모리에서 해제될 때 사용하고, 반대로 `unowned`는 참조하고 있는 인스턴스가 같은 시점 혹은 더 뒤에 메모리에서 해제될 때 사용한다. 우선 `weak` 참조에 대해서 알아보자.

> 강한 순환 참조에 대해서는 [ARC](./arc.md)에서 살펴보자.

&nbsp;
## Weak Reference

변수를 약한 참조로 선언하면 참조하고 있는 인스턴스가 먼저 메모리에서 해제되고 ARC는 런타임에 자동으로 참조하고 있는 변수에 nil을 할당한다. 이러한 성질때문에 약한 참조를 하는 변수는 `Optional` 타입이다. ARC에서 약한 참조가 어떻게 동작하는지 살펴보자.

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
  weak var tenant: Person?
}
```

`Person`과 `Apartment` 인스턴스의 변수에서 각각 인스턴스를 상호 참조하도록 할당한다.

```swift
var john: Person?
var unit4A: Person?

john = Person(name: "john")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A.tenat = john
```

이때 각 인스턴스를 참조하고 있는 그림은 아래와 같다. 주목할 점은 `Apartment`의 `tenant` 변수가 `Person` 인스턴스를 약한 참조(weak)로 참조하고 있다는 것이다. 그러므로 `Person` 인스턴스는 `john` 변수만 참조하고 있기 때문에 참조 횟수는 1이다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170764047-ceec1071-5a91-427b-880e-a871e4cbd142.png" width="600">
</p>

`john` 변수에 nil이 할당되면 `Person` 인스턴스의 참조 횟수가 0이 되어 ARC가 `Person` 인스턴스가 더 이상 사용되지 않는다고 판단하고 메모리에서 해제한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170764518-d2fc6692-6edd-4024-a82d-0a412e65e2b9.png" width="600">
</p>

마찬가지로 `unit4A` 변수에 nil을 할당하면 `Apartment` 인스턴스는 ARC에 의해 메모리에서 해제된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170764725-a96575ca-19a1-423f-a0d8-db65b309bc03.png" width="600">
</p>

&nbsp;
## Unowned Reference

미소유 참조는 약한 참조와 다르게 참조 대상이 되는 인스턴스가 같은 life-time을 갖거나 더 긴 life-time을 갖기 때문에 항상 값이 있다고 가정한다. 만약 참조하고 있는 인스턴스가 메모리에서 해제됐는데 접근하게 되면 런타임 에러가 발생한다. 또한 ARC는 미소유 참조에는 nil을 할당하지 않아 약한 참조와 다르게 `Optional` 타입이 아니다. 이제 미소유 참조가 어떻게 동작하는지 살펴보자.

```swift
class Customer {
  let name: String
  var card: CreditCard?
  init(name: String) {
    self.name = name
  }
}

class CreditCard {
  let number: Uint64
  unowned let customer: Customer
  init(number: UInt64, customer: Customer) {
    self.number = number
    self.customer = customer
  }
}
```

`Customer` 클래스는 `card` 변수로 `CreditCard` 인스턴스를 참조하고 있고 `CreditCard` 클래스는 `customer` 변수로 `Customer` 인스턴스를 참조하고 있다. 주목할 점은 `CreditCard` 클래스의 `customer` 변수는 미소유 참조로 선언되어 있다. 이제 `Customer` 타입의 변수를 선언하고 `card` 변수에 값을 설정한다.

```swift
var john: Customer?

john = Customer(name: "john") // 1
john!.card = CreditCard(number: "1234-5678-9101-1121", customer: john!) // 2
```

이 시점에서 인스턴스의 참조 상태를 그림으로 표현하면 다음과 같다. 1에서 `john`이 `Customer` 인스턴스를 참조하고 있고, 2에서 `CreditCard` 인스턴스를 참조하고 있지만 미소유 참조를 하고 있기 때문에 `Customer` 인스턴스에 대한 참조 횟수는 1이다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170767590-f375096f-25bb-4c57-b133-5e46e214f6e9.png" width="600">
</p>

`john` 변수에 nil이 할당되면 아래의 그림과 같다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170767814-b503a303-b134-46a1-8492-6a9987152caa.png" width="600">
</p>

더 이상 `Customer` 인스턴스를 강하게 참조하고 있는 인스턴스가 없으므로 `Customer` 인스턴스는 메모리에서 해제된다. 이어서 `CreditCard` 인스턴스를 강하게 참조하고 있는 개체도 사라지므로 `CreditCard` 인스턴스도 메모리에서 해제된다.