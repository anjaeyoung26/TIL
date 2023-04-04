# Copy-on-Write

Swift의 값 타입은 Copy semantic을 가지고 있다. 이는 값 타입을 변수에 대입하거나 함수의 파라미터로 넘기면 해당 값의 데이터가 모두 복사된다는 의미이다. 이는 값 타입의 데이터가 클수록 매우 비싼 연산을 하게된다. Swift에서는 이러한 복사 비용을 줄이기 위해 Copy-on-Write 매커니즘을 사용한다. Copy-on-Write는 값 타입을 변수에 대입하거나 함수의 파라미터로 넘기면 즉시 값을 복사하지 않고, 동일한 메모리 주소를 참조하다가 실제로 데이터가 변경될 때 복사하는 매커니즘이다. 아래의 예제를 살펴보자.

```swift
import Foundation

func print(address o: UnsafeRawPointer ) {
  print(String(format: "%p", Int(bitPattern: o)))
}

var array1: [Int] = [0, 1, 2, 3]
var array2 = array1

print(address: array1) //0x600000078de0
print(address: array2) //0x600000078de0
```

`[Int]` 타입의 `array1` 배열을 생성하고 `array2`에 대입했다. 이 시점에 Copy-on-Write 매커니즘에 의해서 값의 복사가 일어나지 않고 동일한 메모리 주소를 가리킨다.


```swift
array2.append(4)

print(address: array1) //0x600000078de0
print(address: array2) //0x6000000aa100
```

`array2` 배열에 4를 넣고 두 배열의 주소를 다시 출력했다. 데이터의 변경이 일어난 `array2` 배열이 이전과 다른 메모리 주소를 가리키고 있다.