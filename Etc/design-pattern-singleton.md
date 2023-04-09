## Singleton

싱글톤 패턴은 디자인 패턴의 종류 중 하나로, 싱글턴 패턴을 따르는 클래스는 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이다. 최초로 하나의 객체가 생성된 이후 호출된 생성자는 기존에 생성되었던 객체를 반환한다. 

### 장점

1. 클래스를 사용할 때마다 객체를 생성하여 메모리를 할당받지 않고, 최초로 하나의 객체를 생성할 때 할당받는 메모리를 활용하여 **메모리를 절약할 수 있다.**
2. 최초로 생성된 객체를 전역적으로 사용하면서 **데이터를 쉽게 공유할 수 있다.**

### 문제점

1. 전역적으로 데이터를 공유하면 **동시성 문제가 발생할 수 있다.** 이는 여러 스레드가 동시에 객체에 접근할 때 [Race Condition](../OS/process-synchronization.md/#race-condition)이 발생하여 타이밍, 순서 등의 결과값에 영향을 받을 수 있다. 이러한 멀티 스레딩 환경에서 발생할 수 있는 문제를 방지하려면 적절한 동기화가 필요하다. 
2. 데이터를 공유하고 있기 때문에 **독립적인 테스트 환경이 갖춰지지 않는다.** 각 테스트가 독립적으로 수행되기 위해서 매번 인스턴스의 상태를 초기화 시켜야 한다. 만약 하나의 테스트가 끝나고 인스턴스의 상태를 초기화 시켜주지 않으면 다른 테스트의 결과에 영향을 미칠 수 있다.
3. 의존성을 구현 클래스가 아닌 인터페이스에 두면, 실제 구현 클래스의 구현이 변경돼도 클라이언트의 코드는 큰 영향을 받지 않는다. 하지만 싱글톤은 인터페이스가 아닌 구현 클래스의 내부에서 직접 객체를 생성하기 때문에 클라이언트가 구현 클래스에 의존한다. 따라서 **SOLID 원칙 중 DIP를 위반할 수 있다.**
4. 3번의 문제점으로 싱글톤을 사용하는 클라이언트와 싱글톤 클래스 간의 결합도가 높아진다. 이는 싱글톤 클래스의 구현이 변경되면 클라이언트의 코드도 마찬가지로 변경할 가능성이 높다는 것을 의미한다. 따라서 **SOLID 원칙 중 OCP를 위반할 수 있다.**

### Swift의 싱글톤

C, Java 언어에서는 전역 변수와 정적 변수(static)는 컴파일 타임에 초기화되서, 만약 프로그램의 실행 ~ 종료까지 싱글톤 객체에 접근하지 않는다면 메모리 공간이 낭비됐다. 하지만 Swift에서 전역 변수와 정적 변수를 lazy하게 초기화된다. 아래는 공식 문서인 [Global and Local Variables](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties/#:~:text=or%20width.-,Global%20and%20Local%20Variables,-The%20capabilities%20described)의 내용이다.

> Global constants and variables are always computed lazily, in a similar manner to Lazy Stored Properties. Unlike lazy stored properties, global constants and variables don’t need to be marked with the lazy modifier.
> Local constants and variables are never computed lazily.

그리고 Objective-C에서 싱글톤 객체를 구현하려면 `dispatch_once`를 사용했다. 이는 여러 스레드에서 접근할 수 있는 싱글톤 객체의 무결성(atomic)을 보장하기 위해 사용했는데, Swift3 부터는 싱글톤 객체를 구현할 때 `dispatch_once`를 사용하지 않아도 된다. 아래는 공식 문서인 [Migrating to Swift3](https://www.swift.org/migration-guide-swift3/#:~:text=attributes%3A%20.qosDefault.-,Dispatch,-The%20free%20function)의 내용이다.

> The free function dispatch_once is no longer available in Swift. In Swift, you can use lazily initialized globals or static properties and get the same thread-safety and called-once guarantees as dispatch_once provided.

하지만 `static` 키워드를 붙여 정적 변수로 사용한다고 해도 thread-safe를 보장할 수 없다. 왜냐하면 동시에 두 개의 스레드가 접근했을 때, 먼저 임계구역에 들어간 스레드가 값을 변경했다면 이후에 접근한 스레드는 이전에 들어간 스레드가 값을 변경했기 때문에 예상과 다른 결과가 나타날 수 있기 때문이다. 따라서 무결성(atomic)과 불변성(immutable)을 모두 가져야 thread-safe 할 수 있다.

```swift
class Singleton {
  static let shared = Singleton()
  private init() {}
}
```

Swift에서는 `static let`으로 싱글톤 객체를 thread-safe한 환경에서 사용할 수 있다.