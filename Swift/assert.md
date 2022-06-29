## assert

`assert` 함수는 C언어의 `assert`문을 메시지와 함께 넣어서 사용하는 것으로, 특정 조건을 성립하지 않을 경우 메시지를 출력한다. `assert`는 *-O* 빌드, 즉 앱스토어에 올라간 앱에서는 효과가 없으며, 오로지 *-Onone* 빌드인 디버그 환경에서만 효과가 있다. 이는 Xcode가 설정한 기본값으로 *Build Settings*에서 변경할 수 있지만 예상치 못한 사이드 이펙트가 발생할 수 있다. 따라서 공식 문서는 앱스토어에 올라간 앱에서도 특정 조건을 확인하고 싶다면, [precondition](https://developer.apple.com/documentation/swift/precondition(_:_:file:line:))을 사용하라고 권장한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/176377518-a3403f87-8e9c-4402-ab4b-d31bc45fff15.png">
</p>

`assert`의 구현부를 살펴보면 총 4가지 파라미터를 가지고 있다. 각 파라미터가 어떤 용도인지 알아보자.

```swift
func assert(
  _ condition: @autoclosure () -> Bool,
  _ message: @autoclosure () -> String = String(),
  file: StaticString = #file,
  line: UInt = #line
)
```

1. `condition` : `assert`를 통해 확인하는 조건으로, `Bool`을 반환하는 클로저이다. 만약 `false`를 반환하면 디버그가 가능한 상태로 프로그램이 중지된다.
2. `message` : 조건을 만족하지 못하는 경우, 즉 `condition`이 `false`를 반환할 때 출력되는 메시지이다. 빈 문자열을 기본값으로 가지고 있다.
3. `file` : 조건을 만족하지 못할 때, `message`와 함께 출력되는 파일의 이름이다. `assert` 함수가 호출되는 파일의 이름을 기본값으로 가지고 있다.
4. `line` : 조건을 만족하지 못할 때, `message`와 함께 출력되는 코드 라인이다. `assert` 함수가 위치한 라인을 기본값으로 가지고 있다.

&nbsp;
### 예시

```swift
func onlyEven(number: Int) {
  assert(number % 2 == 0, "The number should be event number.")
}
```

위 `onlyEven(number:)` 함수는 파라미터인 `number`가 짝수임을 확인하기 위해 `assert`를 사용한다. 만약 `onlyEven(number: 3)`을 호출했을 경우, 숫자 3은 짝수가 아니기 때문에 `message`가 출력될 것이다. 아래는 Playground에서 `onlyEven(number: 3)`을 호출했을 때 출력되는 메시지이다.

```swift
__lldb_expr_990/Playground.playground:6: Assertion failed: The number should be event number.
```