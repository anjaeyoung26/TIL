# NSLocking

모든 Lock 개체가 채택하는 프로토콜이다. Lock 개체는 단일 응용 프로그램 내에서 여러 스레드의 작업을 조정하는데 사용된다. 응용 프로그램은 Lock 개체를 사용하여 코드의 중요한 섹션이 별도의 스레드에서 동시에 실행되는 것을 방지해서 공유 자원이 손상되지 않도록 보호할 수 있다. 또한 Lock 개체는 lock, unlock 두 가지 상태만 존재하므로 [임계 영역](../../OS/process-synchronization.md/#임계-영역)에 하나의 스레드만 접근할 수 있다.

```swift
public protocol NSLocking {
    public func lock()
    public func unlock()
}
```

`NSLocking` 프로토콜을 채택한 모든 Lock 개체는 `lock()`과 `unlock()` 메소드를 필수적으로 구현해야 한다.

- `lock()` : lock을 시도한다. 이미 lock 중인 스레드가 있다면 unlock 할 때까지 현재 스레드가 block된다.
- `unlock()` : lock을 해제해서 다른 스레드가 임계 영역에 접근할 수 있도록 한다.

&nbsp;
## NSLock

```swift
open class NSLock : NSObject, NSLocking {
    open func `try`() -> Bool
    open func lock(before limit: Date) -> Bool

    @available(iOS 2.0, *)
    open var name: String?
}
```

가장 기본적인 Lock 개체이다. [공식 문서](https://developer.apple.com/documentation/foundation/nslock)에 따르면 `NSLock`을 사용할 때 두 가지를 주의해야 한다.

1. lock을 거는 스레드와 unlock하는 스레드가 동일해야 한다. 만약 다른 스레드에서 unlock 한다면 정의되지 않은 동작이 발생할 수 있다. 다른 스레드가 접근하지 못하도록 lock을 거는 것인데, 다른 스레드가 unlock을 한다면 접근할 수 있는 여지가 생긴다.

2. 재귀적인 lock을 구현하면 안된다. 동일한 스레드에서 lock을 두 번 건다면 교착상태가 발생해 스레드가 영구적으로 잠길 수 있다. 재귀 함수 등에서 여러번 lock을 걸어야 할 때는 `NSRecursiveLock` 클래스를 사용한다.

&nbsp;

이제 `NSLock`을 어떻게 사용하는지 알아보자. 먼저 아래의 코드는 [Race Condition](../../OS/process-synchronization.md/#race-condition)이 발생한 예시이다.

```swift
var number = 0

Thread.detachNewThread {
    print("1 start")

    (0..<10000).forEach({ element in 
        number += 1
    })

    print("1 end - \(number)")
}

Thread.detachNewThread {
    print("2 start")

    (0..<10000).forEach({ element in 
        number += 1
    })

    print("2 end - \(number)")
}

sleep(3)
print("Final - \(number)")
```

> [2022-05-11 07:19:42 +0000] 1 start   
> [2022-05-11 07:19:42 +0000] 2 start   
> [2022-05-11 07:19:43 +0000] 1 end - 19789   
> [2022-05-11 07:19:43 +0000] 2 end - 19993   
> [2022-05-11 07:19:45 +0000] Final - 19993

두 개의 스레드에서 `number`를 10000번 1씩 증가시키는 작업을 수행한다. 출력을 보면 두 개의 스레드는 동시에 작업을 시작했고 최종적으로 `number`는 20000이 될 것을 기대하지만 실제로는 19993이다. 왜 Race Condition이 발생할까? 두 개의 스레드가 `number` 변수에 동시에 접근해서 값을 증가시킨다. 만약 `number`의 값이 1000일 때 두 개의 스레드가 동시에 값을 읽어서 1을 증가시켜 1001을 저장한다면 1002가 되어야 할 값이 실제로는 1001이 된다.

&nbsp;

이를 해결하기 위해 `NSLock`을 사용해보자.

```swift
let lock = NSLock()
var number = 0

Thread.detachNewThread {
    lock.lock()
    print("1 start")

    (0..<10000).forEach({ element in 
        number += 1
    })

    print("1 end - \(number)")
    lock.unlock()
}

Thread.detachNewThread {
    lock.lock()
    print("2 start")

    (0..<10000).forEach({ element in 
        number += 1
    })

    print("2 end - \(number)")
    lock.unlock()
}

sleep(3)
print("Final - \(number)")
```

> [2022-05-11 07:31:54 +0000] 1 start   
> [2022-05-11 07:31:54 +0000] 1 end - 10000   
> [2022-05-11 07:31:54 +0000] 2 start   
> [2022-05-11 07:31:55 +0000] 2 end - 20000  
> [2022-05-11 07:31:57 +0000] Final - 20000

앞서 Race Condition이 발생한 코드와 달리 두 개의 스레드가 동시에 작업을 시작하지 않는다. 두 번째 스레드가 작업을 시작하려고 할 때 이미 첫 번째 스레드에서 lock을 걸었기 때문에 두 번째 스레드를 block한다. 그리고 첫 번째 스레드가 unlock하길 기다렸다가 작업을 시작한다. `NSLock`을 통해 공유 자원인 `number`에 대해 하나의 스레드만 접근을 허용했기 때문에 Race Condition 현상이 발생하지 않고 최종적으로 20000의 값이 출력됐다. 

&nbsp;

### 부가적인 메소드

첫 번째 스레드가 unlock 할 때 까지 두 번째 스레드는 block되는 점을 완화하기 위해 `NSLock`은 두 가지 메소드를 제공한다.

1. `lock(before:)` : 파라미터로 받은 시점까지 lock을 획득하도록 시도한다. lock을 얻는 즉시 true를 반환하고, 특정 시점까지 lock을 획득하지 못하면 false를 반환한다. `lock()`과 달리 특정 시점 이후에는 스레드가 block 되지 않음을 보장할 수 있다.

    ```swift
    func lock(before limit: Date) -> Bool {
        ...
    }
    ```

2. `try()` : 현재 lock을 획득할 수 있는지 확인한다. 단발성으로 실행되기 때문에 스레드는 block되지 않는다.

    ```swift
    func try() -> Bool {
        ...
    }
    ```

&nbsp;
## NSRecursiveLock

```swift
open class NSRecursiveLock : NSObject, NSLocking {
    open func `try`() -> Bool
    open func lock(before limit: Date) -> Bool

    @available(iOS 2.0, *)
    open var name: String?
}
```

구현부는 `NSLock`과 차이가 없지만 앞에서 언급했듯이 재귀적인 lock을 구현할 때 사용하는 Lock 개체이다. 동일한 스레드에서 교착상태 없이 여러 번 lock을 획득할 수 있다. 하나의 스레드가 lock을 걸었다면 다른 스레드가 공유 자원에 접근할 수 없는 것은 동일하다.

&nbsp;

아래의 코드는 교착상태가 발생하는 예시이다.

```swift
let lock = NSLock()

Thread.detachNewThread {
    print("start")

    lock.lock()
    print("lock - 1")

    lock.lock() // 교착상태 발생
    print("lock - 2")

    lock.unlock()
    print("unlock - 1")

    lock.unlock()
    print("unlock - 2")
}
```

> start   
> lock - 1

스레드에서 lock을 획득한 후 다시 한 번 lock을 획득하려고 하면 교착상태가 발생한다. 

&nbsp;

이제 `NSLock` 대신 `NSRecursiveLock`을 사용한 결과를 살펴보자.

```swift
let lock = NSRecursiveLock()

Thread.detachNewThread {
    ...
}
```

> start   
> lock - 1   
> lock - 2   
> unlock - 1   
> unlock - 2

두 번의 lock을 걸었음에도 교착상태가 발생하지 않고 스레드의 작업이 모두 수행됐다. 주의할 점은 lock과 unlock의 횟수가 동일하지 않으면 마찬가지로 교착상태가 발생한다.

&nbsp;
## NSConditionLock

```swift
open class NSConditionLock : NSObject, NSLocking {
    public init(condition: Int)
    open var condition: Int { get }
    open func lock(whenCondition condition: Int)
    open func `try`() -> Bool
    open func tryLock(whenCondition condition: Int) -> Bool
    open func unlock(withCondition condition: Int)
    open func lock(before limit: Date) -> Bool
    open func lock(whenCondition condition: Int, before limit: Date) -> Bool

    @available(iOS 2.0, *)
    open var name: String?
}
```

조건을 충족하면 lock을 획득하고 unlock 시 조건을 설정할 수 있다. `condition`은 `Int` 타입으로 기본 값은 0이다. 

&nbsp; 

아래의 예시를 살펴보자.

```swift
let lock = NSConditionLock(condition: 1)
var number = 0

Thread.detachNewThread {
    lock.lock(whenCondition: 1) // 1
    print("1 start")

    lock.unlock(withCondition: 2) // 2
    print("1 end")
}

Thread.detachNewThread {
    lock.lock(whenCondition: 2) // 3
    print("2 start")

    lock.unlock(withCondition: 1) // 4
    print("2 end")
}
```

> 1 start   
> 1 end   
> 2 start   
> 2 end

결과적으로 첫 번째 스레드의 작업이 완료된 후 두 번째 스레드가 작업을 시작한다. 2 과정에서 `condition`의 값을 2로 설정하지 않았다면 두 번쩨 스레드는 작업을 시작할 수 없어 교착상태가 발생하니 주의해야 한다. 주석을 표시한 곳에서 `condition`이 어떻게 사용되는지 살펴보자.

- 1 : `condition`이 1이라는 조건을 충족하면 첫 번째 스레드가 lock을 획득한다.
- 2 : 첫 번째 스레드가 unlock 하면서 `condition`의 값을 2로 설정한다.
- 3 : `condition`이 2라는 조건을 충족하면 두 번째 스레드가 lock을 획득한다.
- 4 : 두 번째 스레드가 unlock 하면서 `condition`의 값을 1로 설정한다.






