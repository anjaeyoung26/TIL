# Stack

선형 자료구조의 일종으로 후입선출(LIFO) 형태이다.

## 특징

1. 한 쪽으로만 데이터가 삽입된다. 그리고 가장 나중에 삽입된 데이터가 먼저 나온다. LIFO(Last In First Out)

2. 함수 호출 순서의 제어, 인터럽트 처리, 수식 계산, 재귀적인 문제를 동적 프로그래밍 방식으로 해결하는 분야에서 사용된다.

&nbsp;
## 용어

1. **Push** : 데이터를 삽입한다.

2. **Pop** : 가장 최근에 삽입된 데이터를 꺼낸다.

3. **Peek** : 가장 최근에 삽입된 데이터를 가져온다. Pop과 다르게 데이터를 스택에서 꺼내지 않고 정보만 가져온다.

4. **Top** : 가장 최근에 삽입된 데이터

5. **Bottom** : 가장 오래전에 삽입된 데이터

&nbsp;
## 구현

```swift
public struct Stack<T> {
  private var array: [T] = []

  public var count: Int {
    return array.count
  }

  public var isEmpty: Bool {
	return array.isEmpty
  }

  public var top: T? {
	return array.last
  }

  public var bottom: T? {
	return array.first
  }

  public mutating func push(value: T) {
	array.append(value)
  }

  public mutating func pop() -> T? {
	return array.popLast()
  }

  public func peek() -> T? {
	return array.top
  }
}
```