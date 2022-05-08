# Queue

선형 자료구조의 일종으로 선입선출(FIFO) 형태이다.

## 특징

1. 한 쪽으로만 데이터가 삽입된다. 그래서 가장 먼저 삽입된 데이터가 먼저 나온다. FIFO(First In First Out)

2. 은행의 접수 시스템, 콜센터 고객 대기 등 순서대로 처리하는 분야에서 사용된다.

&nbsp;
## 용어

1. **enqueue** : 데이터를 삽입한다.

2. **dequeue** : 가장 먼저 삽입된 데이터를 꺼낸다.

3. **front** : 가장 먼저 삽입된 데이터의 위치이며 `dequeue` 시 값이 증가한다.

4. **rear** : 가장 나중에 삽입된 데이터의 위치이며 `enqueue` 시 값이 증가한다.

5. **peek** : 가장 먼저 삽입된의 정보를 가져온다.

6. **공백 상태** : `front == rear`

7. **포화 상태** : `rear == array.count - 1`

&nbsp;
## 종류

## Linear Queue

1차원 배열을 이용한 기본적인 큐의 형태이다.

### 특징

1. 고정적인 크기로 크기가 제한적이며 메모리 낭비가 일어날 수 있다.

2. 데이터의 삽입/삭제가 반복되면 빈 공간이 발생한다. `rear`가 큐의 마지막 인덱스를 가리키지만 앞에는 빈 공간이 있을 수 있다.

    - 해결1 : 데이터의 삭제가 일어날 때 마다 빈 공간을 제거한다.(나머지 원소들을 한 칸 앞으로 옮긴다.)
    - 해결2 : 주기적으로 배열의 빈 공간을 제거한다.

&nbsp;
### 구현

```swift
public struct Queue<T> {
  fileprivate var array = [T?]()
  
  public var isEmpty: Bool {
    return count == 0
  }

  public var count: Int {
    return array.count
  }
  
  public mutating func enqueue(_ element: T) {
    array.append(element) // 1
  }
  
  public mutating func dequeue() -> T? {
    if isEmpty {
      return nil
    } else {
      return array.removeFirst() // 2
    }
  }
  
  public var front: T? {
    if isEmpty {
      return nil
    } else {
      return array[head]
    }
  }
}
```

1. `enqueue`의 시간 복잡도는 일반적으로 $O(1)$이다. 하지만 Swift의 배열은 처음에 일정한 크기로 선언됐다가 원소들이 추가되면서 공간이 더 필요하다고 판단되면 배열의 길이를 늘린다. 배열의 길이를 늘린다는 것은 새로운 배열을 선언하고 기존의 배열 원소들을 복사하는 과정으로 시간 복잡도는 $O(n)$이다. 즉 `enqueue`의 시간 복잡도는 $O(1)$이지만 때때로 $O(n)$이다.
2. `dequeue`의 시간 복잡도는 $O(n)$이다. `removeFirst()`는 첫 번째 원소를 삭제하면서 나머지 원소들을 한 칸 앞으로 옮긴다.

&nbsp;

위와 같이 데이터를 삭제할 때 마다 Shift 하지않고 주기적으로 빈 공간을 제거하는 방식이 있다.

```swift
fileprivate var head = 0

public mutating func dequeue() -> T? {
    guard head < array.count, let element = array[head] else { return nil }

    array[head] = nil
    head += 1

    let percentage = Double(head)/Double(array.count)
    if array.count > 50 && percentage > 0.25 {
      array.removeFirst(head) // removeFirst(_ k: Int) : 컬렉션의 시작 부분에서 지정된 수(k)만큼의 요소를 제거한다.
      head = 0
    }
    
    return element
}
```

`dequeue` 연산을 수행할 때마다 `head`의 값을 증가시킨다. 즉 `head`의 값은 빈 공간을 제외한 첫 번째 요소의 위치를 뜻하며 일정 조건을 충족했을 때 빈 공간을 모두 제거한다.

```swift
head 0 : [**1**, 2, 3, 4, 5]
head 1 : [null, 2, 3, 4, 5]
head 2 : [null, null, 3, 4, 5]
```

하지만 위 방식도 결국 $O(n)$의 시간 복잡도를 갖는다. 더 효율적인 자료구조 원형 큐를 사용해보자.

&nbsp;
## Circular Queue

선형 큐에서 발생하는 빈 공간에 대한 메모리의 낭비를 줄이기 위해 만들어진 형태로 Ring Buffer 혹은 Circular Buffer 라고도 한다.

### 특징

1. 큐의 포화, 공백 상태를 구별하기 위해 하나의 공간은 항상 비워둔다.
    
    ![circular-queue1](https://user-images.githubusercontent.com/61190690/167286453-a0e8cc2e-8707-4006-b3b1-b0eb28077a10.png)
    
    (c)와 같이 큐의 모든 위치에 데이터를 채운다면 공백 상태와 마찬가지로 `rear == front` 이므로 포화 상태와 구분할 수 없다.
    
&nbsp;
### vs 선형 큐

|  | front, rear 초기 값 | 포인터 증가 방식 | 공백 상태 | 포화 상태 |
| --- | :---: | :---: | :---: | :---: |
| 선형 큐 | -1 | enqueue : rear+=1</br>dequeue ; front+=1  | rear == front | rear == maxsize - 1 |
| 원형 큐 | 0 | enqueue : (rear+1)%maxsize</br>dequeue : (front+1)%maxsize | rear == front | (rear+1)%maxsize == front |

&nbsp;
### 구현

```swift
public struct CircularQueue<T> {
  fileprivate var array: [T?]
  fileprivate var front = 0
  fileprivate var rear = 0
	fileprivate let MAX_SIZE: Int

  public init(count: Int) {
    array = [T?](repeating: nil, count: count)
		MAX_SIZE = count
  }

	public var isEmpty: Bool {
    return front == rear
  }

  public var isFull: Bool {
    return ((rear + 1) % MAX_SIZE) == front
  }

	public var count: Int {
		return ((rear - front + 1) + MAX_SIZE) % MAX_SIZE
	}

  public mutating func write(_ element: T) -> Bool {
    if !isFull {
      rear = (rear + 1) % MAX_SIZE
			array[rear] == element
      return true
    } else {
      return false
    }
  }

  public mutating func read() -> T? {
    if !isEmpty {
      front = (front+1) % MAX_SIZE
      return array[front]
    } else {
      return nil
    }
  }
}
```

&nbsp;
### 그림으로 보기

![circular-queue2](https://user-images.githubusercontent.com/61190690/167286456-0a7db3f3-12b5-45d5-87c2-9804155dfc12.png)

|  | 초기(공백) | enqueue | enqueue | enqueue |
| --- | :---: | :---: | :---: | :---: |
| front | 0 | 0 | 0 | 0 |
| rear | 0 | 1 | 2 | 3 |
| (rear+1)%max_size | 1 | 2 | 3 | 0 |
| 상태 | front == rear : empty |  |  | (rear+1)%max_size == front : full |

- max_size : 4
- 나머지 연산자는 왼쪽 피연산자가 오른쪽 피연산자보다 작을 경우 결과 값은 왼쪽 피연산자 그대로 나온다.