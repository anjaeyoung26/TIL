# Variadic Parameters

Variadic Parameters는 가변 매개변수로 타입 이름 뒤에 마침표 세 개(...)로 명시한다. 가변 매개변수는 함수 본문에서 배열의 형태로 사용할 수 있다.

```swift
func total(_ numbers: Double...) -> Double {
  var total: Double = 0
  for number in numbers {
    total += number
  }
  return total
}

total(1, 2, 3, 4, 5) // 15
total(3, 8.25, 18.75) // 30
```

가변 매개변수는 0개 이상의 값을 허용한다.

```swift
total() // 0
```