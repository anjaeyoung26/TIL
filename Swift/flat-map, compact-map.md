## flatMap

컬렉션을 단일 수준 컬렉션으로 변환한다.

```swift
let numbers = [1, 2, 3, 4]

let mapped = numbers.map { Array(repeating: $0, count: $0) }
// [[1], [2, 2], [3, 3, 3], [4, 4, 4, 4]]

let flatMapped = numbers.flatMap { Array(repeating: $0, count: $0) }
// [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
```

&nbsp;
## compactMap

컬렉션의 요소 중 `nil`이 아닌 요소만으로 구성된 컬렉션을 반환한다.

```swift
let numbers = ["1", "2", "three", "///4///", "5"]

let mapped: [Int?] = numbers.map { Int($0) } 
// [1, 2, nil, nil, 5]

let compactMapped: [Int] = numbers.compactMap { Int($0) } 
// [1, 2, 5]
```