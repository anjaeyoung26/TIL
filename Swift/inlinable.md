## inline

인라인이란 일반적인 프로그래밍 언어에 있는 기능으로, 메서드를 호출하는 부분을 해당 메서드의 본문으로 대체하는 컴파일러 최적화 방법이다.

```swift
override func viewDidLoad() {
  printNumber(1)
  // = print("number is \(1)")
}

@inlinable
func printNumber(_ number: Int) {
  print("number is \(number)")
}
```

`printNumber`와 같이 구현부가 간단한 메서드를 인라인으로 설정하면 오버헤드를 낮출 수 있다. 하지만 메서드의 본문에 재귀 호출을 사용한다면 오히려 오버헤드가 증가되어서 비효율적이다. 

```swift
func printNumberRecursively(_ number: Int) {
  guard number > 0 else { return }
  print(number)
  printNumberRecursively(number - 1)
}
```

또한 Swift에서는 `internal`, `public` 접근 제한에서만 사용할 수 있다.

```swift
@inlinable
private func privateMethod() { .. } // X

@inlinable
fileprivate func fileprivateMethod() { .. } // X
```