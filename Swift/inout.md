## inout

Swift에서 함수와 메서드의 매개변수는 기본적으로 상수가 전달된다. 이는 직접 매개변수의 값을 변경할 수 없음을 의미하며, 매개변수의 값을 변경하기 위해서는 별도의 지역번수를 사용해야 한다. 하지만 지역변수의 스코프는 함수로 제한되어 있기 때문에 함수를 벗어나는 순간 그대로 사라지게 된다.

```swift
var number = 4

func square(_ num: Int) {
  var num = num
  num *= num
}

square(number)
print(number) // 4, 변화 없음
```

위 코드에서 매개변수로 전달된 숫자의 제곱을 반환하는 함수 `square`가 있다. 이때  `Int` 타입의 변수 `number`를 `square`의 매개변수로 사용해도 `number`의 값은 여전히 4이다. 만약 `square` 함수에서 매개변수로 전달된 숫자의 값을 직접 변경하고 싶다면 `inout` 키워드를 사용한다.

```swift
var number = 4

func square(_ num: inout Int) {
  num *= num
}

square(number)
print(&number) // 16, 변화 있음
```

위 코드를 살펴보면 `inout` 매개변수를 주입할 때 `&`을 사용해야 한다. 이는 변수의 값을 전달하는 것이 아닌, 변수의 주소값을 넘겨 직접 접근할 수 있도록 한다. 또한 `inout` 매개변수는 함수 안에서 값이 변화될 수 있음을 의미하기 때문에, 상수는 사용할 수 없다.

```swift
let number = 4
square(&number) // Error
```

### 동작 원리

`inout`을 추가한 `square` 함수는 매개변수로 전달된 `number`의 값을 직접 수정한다. 그렇다면 `inout`의 동작 원리는 무엇일까? 먼저 `inout`은 *copy-in copy-out*의 줄임말로, 이는 함수의 매개변수로 전달된 변수가 값이 변경되고 다시 재할당되는 과정에서 두 번의 복사가 발생함을 의미한다. 아래는 `inout`이 동작하는 과정을 나타낸다.

1. 함수가 호출되면 매개변수로 전달된 변수가 복사된다.
2. 함수에서 복사했던 값을 변화시킨다.
3. 함수가 종료될 때, 변화된 값을 원본 변수에 재할당한다.

위 동작 과정을 이해하기 위한 코드를 살펴보자. 출력된 결과를 살펴보면 `square` 함수 내에서 전달받은 인자의 값을 변경해도, `number`의 값은 변하지 않는다. 그리고 `square` 함수가 종료된 후 `number`의 `didSet`가 호출된다.

```swift
var number: Int = 4 {
  didSet {
    print("number did set.")
  }
}

func square(_ num: inout Int) {
  print(number)
  num *= num
  print(number)
}

square(&number)
// 4
// 4
// number did set.
```

이는 위 `inout`의 동작 과정과 같이 함수가 종료될 때, 변경된 값이 원본 변수에 할당되는 [*Call by value*](./call-by-value.md/#call-by-value)의 형태임을 알 수 있다. 이때 Swift의 최적화 옵션에 따라 [*Call by reference*](./call-by-value.md/#call-by-reference)와 같이 동작하여 값의 복사가 일어나지 않고, 주소값으로 직접 변수에 접근하여 값을 수정하게 된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/175812947-87fd3f13-821b-4228-a444-d5129b339fd7.png">
</p>

하지만 [*Call by reference*](./call-by-value.md/#call-by-reference)를 사용할 때, 동시에 여러 `inout` 함수나 메소드에서 사용되거나 장기간 동안 접근하여 메모리와 관련된 문제가 발생할 수 있다. 이와 관련된 내용은 [Swift Language Guide - Memory Safety](https://docs.swift.org/swift-book/LanguageGuide/MemorySafety.html)에서 살펴볼 수 있다.