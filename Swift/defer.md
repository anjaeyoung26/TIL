# defer

작성된 위치와 상관없이 함수의 종료 직전에 실행되는 구문이다.

```swift
func someFunction() {
    defer { print("3") }
    print("1")
    print("2")
}
```

> 1   
> 2   
> 3

&nbsp;
## 특징

1. 함수가 `defer`를 읽기 전에 종료된다면 실행되지 않는다.

    ```swift
    func someFunction() {
        print("1")
        print("2")
        return
        defer { print("3") }
    }
    ```

    > 1   
    > 2

2. 하나의 함수에서 여러 개의 `defer`를 사용할 수 있으며, 가장 마지막에 위치한 `defer` 부터 실행된다.

    ```swift
    func someFuction() {
        defer { print("1") }
        defer { print("2") }
        defer { print("3") }
    }
    ```

    > 3   
    > 2   
    > 1

3. 중첩되게 사용할 수 있으며, 가장 바깥의 `defer` 부터 실행된다.

    ```swift
    func someFunction() {
        defer {
            defer {
                defer {
                    print("3")
                }
            }
            print("2")
        }
        print("1")
    }
    ```

    > 1   
    > 2   
    > 3   

&nbsp;
## 용도

함수를 종료하기 직전에 정리해야될 변수나 상수를 처리한다. 주로 `NSLock`을 통해 상호배제를 위해 스레드를 잠글 때, `defer`를 통해 함수가 종료되기 직전에 잠금을 해제해서 교착상태에 빠지지 않기 위해 사용된다.

```swift
private let lock = NSLock()

func someFunction() {
    lock.lock()
    defer { lock.unlock() }
    ...
}
```