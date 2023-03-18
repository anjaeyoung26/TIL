## 나머지 연산

일반적으로 Swift에서 두 숫자의 나머지를 구할 때 `%` 연산자를 사용한다.

```swift
var remainder = 10 % 3 // 1
```

하지만 두 숫자가 정수형(Integer)가 아닐 경우, 컴파일러는 오류를 발생시킨다.

```
'%' is unavailable: Use truncatingRemainder instead
```

이는 *"현재 코드에서 `%`를 사용할 수 없으므로, 대신에 `truncatingRemainder`를 사용해라"* 라는 의미이다. 왜 `%` 연산자는 정수형에서만 사용할 수 있는지를 이해하기 위해 `%` 연산자가 구현되어 있는 곳을 살펴보자. 소스는 [`BinaryInteger`](https://github.com/apple/swift/blob/main/stdlib/public/core/Integers.swift)에서 살펴볼 수 있다.

```swift
public protocol BinaryInteger {
  static func %(lhs: Self, rhs: Self) -> Self
}
```

`%` 연산자는 `BinaryInteger` 프로토콜의 요구 사항으로 정의되어 있다. `BinaryInteger` 프로토콜은 모든 정수 유형의 기초가 되는 프로토콜로, `Int` 및 `UInt32`와 같은 표준 라이브러리의 모든 정수 유형은 `BinaryInteger`를 준수한다. 따라서 부동 소수점 유형인 `Double`, `Float`에서 `%` 연산자를 사용할 수 없는 것이다.

&nbsp;

### truncatingRemainder

하지만 컴파일러는 `truncatingRemainder`을 대신 사용하라고 친절하게 안내한다. 이는 [`FloatingPoint`](./%EC%86%8C%EC%88%98%EC%A0%90-%EB%8B%A4%EB%A3%A8%EA%B8%B0.md) 프로토콜의 요구 사항으로 정의되어 있는 메소드로, `%` 연산자와 동일한 기능을 한다.

```swift
var remainder1 = 10.0.truncatingRemainder(dividingBy: 3) // 1
var remainder2 = 10.0.truncatingRemainder(dividingBy: 3.0) // 1
var remainder3 = 10.0.truncatingRemainder(dividingBy: 3.1) // 0.699999999999993
```

&nbsp;

### truncatingRemainder vs remainder

나머지를 구하는 메서드는 `truncatingRemainder`만이 아니다. `FloatingPoint`에는 `remainder`라는 메소드도 존재하는데, 이는 `truncatingRemainder`와 마찬가지로 나머지를 구하는데 사용하지만 결과값이 다르다. 각각이 어떻게 동작하는지 알아보기 위해 공식 문서의 코드를 살펴보자.

```swift
var x = 8.625
let y = 0.75

let q1 = (x/y).rounded(.towardZero)              // 11.5 -> 11
let r1 = x.truncatingRemainder(dividingBy: y)    // 0.375
// x == 0.75 * 11 + 0.375

let q2 = (x/y).rounded(.toNearestOrEven)         // 11.5 -> 12
let r2 = x.remainder(dividingBy: y)              // -0.375
// x == 0.75 * 12 - 0.375
```

`r1`과 `r2`의 값을 살펴보면 서로 반대의 부호를 갖는다. 이는 `truncatingRemainder`와 `remainder`이 몫을 구하는 방법이 다르기 때문이다. 위 코드를 살펴보면 둘은 몫을 구할 때 다른 반올림 방식을 사용한다.

- `truncatingRemainder` : `FloatingPointRoundingRule.towardZero`, 정수부에 영향을 미치지 않으면서 소수점을 버린다.

  ```swift
  (5.2).rounded(.towardZero) // 5.0
  (5.5).rounded(.towardZero) // 5.0
  (-5.2).rounded(.towardZero) // -5.0
  (-5.5).rounded(.towardZero) // -5.0
  ```

- `remainder` : `FloatingPointRoundingRule.toNearestOrEven`, 가장 가까운 정수로 반올림하며, 만약 0.5와 같은 경우에는 짝수 값이 선택된다.

  ```swift
  (5.2).rounded(.toNearestOrEven) // 5.0
  (5.5).rounded(.toNearestOrEven) // 6.0
  (4.5).rounded(.toNearestOrEven) // 4.0
  ```
