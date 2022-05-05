# 값 ・ 참조에 의한 호출

## Call by value

할당 또는 파라미터 전달 시 값의 복사가 일어난다. 항상 새로운 변수로 복사가 일어나므로 멀티 쓰레드 환경에서 공유 변수로 인해 문제가 발생할 확률이 적다. 값에 의한 호출로 복사된 변수는 스택 메모리 영역에 할당하여 속도가 빠르고 상속이 불가능하다.

```swift
struct Dog {
	var didFed: Bool = false
}

let mango = Dog()
let cookie = mango

mang.didFed = true
print(mango.didFed) // true
print(cookie.didFed) // false
```

- 값에 의한 호출과 관련된 타입 : `Struct, Enum, Tuple, Array, Dictionary, Set`

&nbsp;
## Call by reference

할당 또는 파라미터 전달 시 객체를 가리키고 있는 메모리의 주소 값만 복사된다. 참조에 의한 호출로 복사된 변수는 힙 메모리 영역에 할당하여 속도가 느리고 상속이 가능하다.

```swift
class Dog {
	var didFed: Bool = false
}

let mango = Dog()
let cookie = mango

mang.didFed = true
print(mango.didFed) // true
print(cookie.didFed) // true
```

- 참조에 의한 호출과 관련된 타입 : `Class, Closure, Function`