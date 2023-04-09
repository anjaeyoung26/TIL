# rethrows

`rethrows` 키워드는 `throws` 함수를 매개변수로 받는 함수를 작성할 때 사용된다. 우선 `rethrows`를 언제 사용하는지 이해하기 위해, `throws`를 통해 에러를 처리하는 경우를 살펴보자. 에러를 throw하는 `throwing` 함수와 단순히 문자열을 출력하는 `nonThrowing` 함수가 있다. 또한 시간 절약을 위해 문자열을 에러로 throw 할 수 있도록 하는 `String`의 extension이 있다.

```swift
extension String: Error { }

func throwing() throws {
  throw "No"
}

func nonThrowing() {
    print("Yes")
}
```

그리고 `() throws -> Void` 타입의 매개변수를 받는 함수가 있다.

```swift
func foo(function: () throws -> Void) {
  do {
    try function()
  } catch {
    // Handle error
  }
}
```

위와 같이 `foo`를 구현하면 매개변수로 받는 `function`이 에러를 throw할 수 있다. 이때 throw된 에러는 `foo` 내부에서 처리해야 한다. 이번에는 `foo`에 `throws` 키워드를 붙여보자.

```swift
func foo(function: () throws -> Void) throws {
  try function()
}
```

`function`에서 발생하는 에러를 `foo` 내부가 아닌 `foo`를 호출하는 곳에서 처리할 수 있다. 여기까지 일반적으로 `throws`를 통해 에러를 처리하는 경우다. 하지만 `foo`는 에러를 발생시키지 않는 `nonThrowing()`을 매개변수로 받을 수 없다. 그렇다면 `foo`에서 `throwing()` 뿐만 아니라 **`nonThrowing()`을 매개변수로 받을 수 있도록** 구현하려면 어떻게 해야될까? 해답은 `rethrows` 키워드를 사용하는 것이다. `foo`를 아래와 같이 변형시켜보자.

```swift
func foo(function: () throws -> Void) rethrows {
  try function()
}
```

`rethrows` 키워드를 사용함으로써 `foo`는 매개변수 `function`에 따라 에러가 발생할 수도, 발생하지 않을 수도 있는 함수가 되었다. 따라서 아래의 두 가지 케이스를 모두 사용할 수 있다.

```swift
do {
  try foo(function: throwing)
} catch {
  // Handle error
}

foo(function: nonThrowing)
```