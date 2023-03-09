# 연산 프로퍼티

값을 저장하고 있는 저장 프로퍼티와 다르게 연산 프로퍼티는 연산을 통해 계산된 값을 반환한다. 또한 저장 프로퍼티를 사용할 수 있는 클래스, 구조체와 더불어 열거형에서도 사용할 수 있다. 아래의 코드를 살펴보자.

```swift
struct Student {
  var koreanAge: Int = 0

  var westernAge: Int {
    get {
      return koreanAge - 1
    }
    set(newAge) {
      koreanAge = newAge + 1
    }
  }
}
```

위 코드에서 `koreanAge`는 `Int` 타입의 값을 저장하는 저장 프로퍼티이다. 반면에 `westernAge`는 `koreanAge` 변수를 통해 계산된 값을 반환한다. 즉 `koreanAge`의 값에 따라 `westernAge`가 반환하는 값이 변경된다. 이처럼 연산 프로퍼티는 다른 프로퍼티의 값에 의해 반환하는 값이 정해지므로 꼭 변수(`var`)로 선언해야 한다.

### getter, setter

주목할 점은 연산 프로퍼티의 `get`과 `set`이다. `get`은 연산 프로퍼티의 getter라고 하며, 연산 프로퍼티가 값을 탐색하고 연산을 통해 반환되는 값이 정의되어 있다. `set`은 연산 프로퍼티의 setter라고 하며, 간접적으로 다른 프로퍼티의 값을 설정하도록 정의되어 있다. 하지만 모든 연산 프로퍼티를 위 코드처럼 정의할 필요는 없다.

&nbsp;
## Shortand Setter Declaration

앞서 `westernAge`의 setter에서 인자의 이름을 `set(newAge)`라고 명시했지만, 인자의 이름을 설정하지 않으면 기본적으로 `newValue`라는 이름을 갖는다.

```swift
var westernAge: Int {
  get {
    return koreanAge - 1
  }
  set {
    koreanAge = newValue + 1
  }
}
```

&nbsp;
## Read-Only

연산 프로퍼티의 setter는 필수적으로 정의하지 않아도 되지만 getter는 필수적으로 정의해야 한다. 이처럼 getter만 정의되어 있는 연산 프로퍼티를 읽기 전용 연산 프로퍼티라고 한다.

```swift
var westernAge: Int {
  get {
    return koreanAge - 1
  }
}
```

또한 getter 키워드를 생략하고 반환 값만 명시할 수 있다.

```swift
var westerAge: Int {
  return koreanAge - 1
}
```