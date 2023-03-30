## @autoclosure

`@autoclosure` 키워드는 함수 호출 시 해당 파라미터의 값을 클로저로 감싸서 전달하는 것을 의미한다.

```swift
func normal(_ closure: () -> Void) {
  closure()
}
```

위 코드에서 `normal`은 일반적인 클로저를 파라미터로 전달받는다. 이 함수를 호출할 때 클로저 파라미터를 대괄호로 묶어야 한다.

```swift
normal({ print("Normal closure") })
```

하지만 `@autoclosure`를 사용한다면 대괄호로 묶지 않고 일반 표현식을 사용할 수 있다.

```swift
func autoclosure(_ closure: @autoclosure () -> Void) {
  closure()
}

autoclosure(print("Auto closure"))
```