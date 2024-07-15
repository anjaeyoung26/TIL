# Data race

[Concurrency](https://developer.apple.com/documentation/swift/concurrency/)는 Swift 5.5에 추가된 동시성 프로그래밍 API입니다. 본 글은 Concurrency의 `Actor` 타입에 대한 내용입니다. 이는 동시성 프로그래밍을 작성하면서 흔히 발생하는 data race를 방지하는 타입입니다. 다음은 data race가 발생할 수 있는 예시입니다:

```swift
class Counter {
  var count: Int = 0

  func increment() {
    self.count += 1
  }
}
```

이때 main, global 스레드에서 increment를 호출하면 경고가 표시됩니다.

```swift
let counter = Counter()

DispatchQueue.global().async {
  counter.increment() // Swift access race in RxFlow_Example.Counter.count.modify : Swift.Int ...
}

counter.increment()
```

이는 여러 스레드에서 count의 값을 변경하려고 접근할 수 있는 data race 경고입니다. data race는 데이터가 shared mutable state여서 발생하므로, 데이터가 변경되거나 공유되지 않은 경우 발생할 수 없습니다. 먼저 데이터가 공유되지 않도록 클래스로 선언한 `Counter`를 구조체로 변경해봅시다. (구조체로 변경한 이유는 구조체는 value semantic 타입으로 값 타입의 복사하는 방식을 사용하기 때문입니다.)

```swift
struct Counter {
  var count: Int = 0

  mutating func increment() {
    self.count += 1
  }
}

let counter = Counter()

DispatchQueue.global().async {
  counter.increment() // error: Cannnot use mutating member on immutable value
}

counter.increment() // error: Cannnot use mutating member on immutable value
```

하지만 `let`으로 선언했기 때문에 mutating 메서드를 호출할 수 없습니다. 그러면 `var counter`로 변경하면 data race가 발생하지 않을까요? `var`는 데이터가 변경될 수 있기 때문에 여전히 data race가 발생할 수 있습니다. 이때 Swift 5.5 이전에는 GCD 혹은 NSLock으로 data race를 방지했습니다:

```swift
/// GCD
struct Counter {
  private var count: Int = 0
  private let queue = DispatchQueue(label: "counter.queue")

  mutating func increment() {
    queue.sync {
      self.count += 1
    }
  }

  func getCount() -> Int {
    return queue.sync {
      return count
    }
  }
}

/// NSLock
struct Counter {
  private var count: Int = 0
  private let lock = NSLock()

  mutating func increment() {
    lock.lock()
    self.count += 1
    lock.unlock()
  }

  func getCount() -> Int {
    lock.lock()
    let currentCount = count
    lock.unlock()
    return currentCount
  }
}

```

하지만 GCD와 NSLock은 deadlock, 순환 참조, QoS 등 고려할 부분이 많아서 까다롭습니다.

&nbsp;
# Actor

이때 Concurrency의 `Actor`를 사용하면 해결할 수 있습니다. `Actor`는 앞서 data race의 원인으로 언급한 shared mutable state의 동기화 매커니즘입니다. 이는 프로그램의 나머지 부분과 분리되어 있는 자신만의 상태(own state)를 가지며, 해당 상태에 접근하려면 `Actor`를 거쳐야 합니다. `Actor`를 거칠 때 마다 GCD, NSLock과 같이 상호배제(mutual exclusion)을 제공해서 `Actor`의 상태에 동시에 접근하지 않도록 합니다. 이러한 동기화는 `Actor`를 사용하면 Swift가 근본적으로 보증한다(it is fundamental guarantee provided by Swift)라고 명시되어 있는 만큼 안전성이 보장되어 있습니다.

`Actor`는 Swift의 새로운 타입으로 다른 타입들과 동일하게 property, method, initializer, subscripts를 정의할 수 있고, protocol 및 extension을 사용할 수 있습니다. 또한 참조 타입과 같이 동작해서 클래스와 가장 유사합니다. 하지만 상속을 지원하지 않고 한 번에 하나의 작업만 mutable state에 접근할 수 있어서 데이터를 공유할 수 있는 클래스와 차이점도 있습니다. 이제 `Counter` 클래스를 `Actor` 타입으로 변경해봅시다:

```swift
actor Counter {
  var count: Int = 0

  func increment() {
    self.count += 1
  }
}
```

이때 Actor는 increment 메서드와 같이 데이터 손상을 일으킬 수 있는 작업에 동시 접근을 방지합니다. 예를 들어, increment 메서드가 실행 중일 때 다른 스레드에서 `count`에 접근하려고 하면, 해당 접근은 increment 메서드가 완료될 때까지 대기하게 됩니다. 즉, increment 메서드를 실행하면 완료될 때까지 actor 내부의 다른 코드가 실행되지 않습니다. 따라서 actor 내부 상태에 대한 동시성을 자동으로 관리하기 때문에, 개발자가 별도로 동기화 매커니즘을 구현하지 않아도 돼서 안전한 동시성을 제공합니다. 

## Actor isolation

`Actor`의 메서드는 기본적으로 비동기적으로 수행됩니다. 이는 actor의 메서드를 호출하는 코드가 메서드의 완료를 기다리지 않고 계속 실행될 수 있음을 의미합니다. 따라서 actor의 메서드는 `async`로 호출해야 하고, 호출하는 측에서는 `await`를 사용해야 합니다.

```swift
func asyncMethod() async {
  let counter = Counter()
  await counter.increment()
}
```

이는 Actor isolation과 관련되어 있습니다. Actor isolation은 actor가 mutable state를 보호하는 동시 접근 방지 방법입니다. 예를 들어, `BankAccount`라는 actor가 있습니다:

```swift
actor BankAccount {
  let accountNumber: Int
  var balance: Double

  init(accountNumber: Int, initialDeposit: Double) {
    self.accountNumber = accountNumber
    self.balance = initialDeposit
  }
}

extension BankAccount {
  enum BankError: Error {
    case insufficientFunds
  }

  func transfer(amount: Double, to other: BankAccount) throws {
    if amount > self.balance {
      throw BankError.insufficientFunds
    }

    self.balance = balance - amount
    other.balance = other.balance + amount
  }
}
```

`BankAccount`는 다른 은행 계좌에 `amount`만큼 송금하는 `transfer` 메서드가 있습니다. 이는 `balance`의 값을 변경시키기 때문에, 만약 `BankAccount`가 class였다면 data race가 발생할 수 있습니다. 하지만 위처럼 actor라면 `other.balance` 값을 변경하려는 코드에서 컴파일 에러가 발생합니다.

```swift
func transfer(amount: Double, to other: BankAccount) throws {
  ...

  other.balance = other.balance + amount // error: actor-isolated property `balance` can only be referenced on `self`
}
```

왜냐하면 `balance`는 actor-isolated propety로, actor 내부에 정의된 stored, computed instance properties, instance methods, instance subscripts는 모두 actor-isolated 상태이기 때문입니다. 이들은 서로 `self`를 통해서 자유롭게 참조할 수 있어서 transfer 메서드 안에서 `self.balance = balance - amount`가 허용됩니다. 또한 non actor-isolated는 actor-isolated에 동기적으로 접근할 수 없습니다.

## cross-actor reference

위에서 `BankAccount.balance`는 actor-isolated property로 actor 외부에서 접근하면 컴파일 에러가 발생했습니다. 하지만 `accountNumber`는 actor 외부에서 접근할 수 있습니다. 왜냐하면 `accountNumber`는 `let`으로 선언되어서 불변 상태이기 때문입니다. 이를 cross-actor reference라고 합니다. 주의할 점은 cross-actor reference는 actor가 정의된 모듈에서만 허용됩니다. 예를 들어, `BankAccount`과 동일한 모듈에서는 아래와 같이 cross-actor reference가 허용됩니다:

```swift
public actor BankAccount {
  public let accountNumber: Int
  public var balance: Double

  public init(accountNumber: Int, initialDeposit: Double) {
    self.accountNumber = accountNumber
    self.balance = initialDeposit
  }
}

public class MyStaticLibrary {
  public init() {
    let account = BankAccount(accountNumber: 1, initialDeposit: 10)
    print(account.accountNumber)
    // Prints 1
  }
}
```

하지만 외부 모듈에서 `BankAccount.accountNumber`에 접근할 때는 cross-actor reference가 아닌 async, await를 사용해야 합니다:

```swift
func methodInOutsideModule() async {
  let account = BankAccount(accountNumber: 1, initialDeposit: 10)
  print(await account.accountNumber)
  // Prints 1
}
```

이처럼 외부 모듈에서 cross-actor reference를 허용하지 않는 이유는 클라이언트에 영향을 최소화하기 위해서입니다. 만약 `BankAccount` 모듈에서 `accountNumber`를 `let`에서 `var`로 바꾼다면 모든 클라이언트의 코드가 위처럼 async, await로 바꿔야합니다.