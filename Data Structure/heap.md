# Heap

완전 이진 트리의 일종으로 컬렉션의 최대∙최소값을 빠르게 구하기 위해 고안된 자료구조이다.

## 특징

1. 용도 : 우선순위 큐 구현, 힙 정렬 지원, 빠르게 컬렉션의 최대∙최소값 계산

2. ‘부모 노드 ≥ 자식 노드' 혹은 ‘부모 노드 ≤ 자식 노드' 정도의 반정렬 상태(느슨한 정렬 상태)를 유지한다.

3. 이진 탐색 트리와 다르게 중복된 값을 허용한다.

4. 힙에서 모든 연산은 트리의 최대 높이($h = log_2N$)만큼의 시간 복잡도를 갖는다. $O(log_2N)$

&nbsp;
## 종류

### Max Heap

<img width="353" alt="max-heap" src="https://user-images.githubusercontent.com/61190690/167288071-a1a84872-8d6a-467f-af56-020aebd6a7b3.png">

최대힙, 부모 노드의 키 값이 자식 노드의 키 값보다 크거나 같은 완전 이진 트리

- 부모 노드 ≥ 자식 노드

&nbsp;
### Min Heap

<img width="350" alt="min-heap" src="https://user-images.githubusercontent.com/61190690/167288072-8fc42876-7e55-4e79-8a9b-e2b7873186c5.png">

최소 힙, 부모 노드의 키 값이 자식 노드의 키 값보다 작거나 같은 완전 이진 트리

- 부모 노드 ≤ 자식 노드

&nbsp;
## 구현

### 정의

```swift
public struct Heap<T> {
	public var nodes: [T] = []

	public var orderCriteria: (T, T) -> Bool

	public(sort: @escaping (T, T) -> Bool) {
		self.orderCriteria = sort
	}

	internal func parentIndex(of index: Int) -> Int {
		return (index - 1) / 2

	internal func leftIndex(of index: Int) -> Int {
		return 2*index + 1

	internal func rightIndex(of index: Int) -> Int {
		return 2*index + 2
	}
}
```

- `orderCriteria`는 정렬 기준으로 >는 최대 힙, < 최소 힙으로 구성된다.
- `parentIndex`, `leftIndex`, `rightIndex` [참고](https://www.notion.so/Tree-71768379728c4ae29f4fcf690f5a3875)

&nbsp;
### Heapify

트리가 힙 속성(최대 힙, 최소 힙)을 만족하도록 요소들의 위치를 재구성하는 연산

- Shift Up : 인덱스에 있는 노드를 부모 노드와 비교하며 알맞은 위치로 이동시킨다.

	```swift
	internal mutating func shiftUp(_ index: Int) {
		var childIndex = index // 6
		var parentIndex = self.parentIndex(of: childIndex)

		while childIndex > 0 && orderCriteria(nodes[childIndex], nodes[parentIndex]) {
			// 자식 노드 > 부모 노드라면 위치를 맞바꾼다.
			nodes.swapAt(childIndex, parentIndex)
			childIndex = parentIndex
			parentIndex = self.parentIndex(of: childIndex)
		}
	}
			
	```

- Shift Down : 인덱스에 있는 노드를 자식 노드와 비교하며 알맞은 위치로 이동시킨다.

	```swift
	internal mutating func shiftDown(from index: Int) {
		let leftChildIndex = leftIndex(of: index)
		let rightChildIndex = rightIndex(of: index)
		var priority = index

		// 왼쪽 자식 노드의 우선순위가 더 높을 때
		if leftChildIndex < nodes.endIndex && orderCriteria(nodes[leftChildIndex], nodes[index]) {
			priority = leftChildIndex
		}

		// 오른쪽 노드의 우선순위가 더 높을 때
		if rightChildIndex < nodes.endIndex && orderCriteria(nodes[rightChildIndex], nodes[index]) {
			priority = rightChildIndex
		}

		// 교환이 필요한 경우
		if priority != index {
			nodes.swapAt(index, priority)
			shiftDown(from: priority)
		}
	}
	```

&nbsp;
### 삽입

트리의 가장 마지막 위치에 노드를 추가하고 `shiftUp`

![heap-insert2](https://user-images.githubusercontent.com/61190690/167288180-23174558-d798-4a5e-87e9-0bc5b69d06cd.png)
![heap-insert3](https://user-images.githubusercontent.com/61190690/167288183-1d72239d-b3ac-4686-8e0f-9bfd5872015a.png)
![heap-insert4](https://user-images.githubusercontent.com/61190690/167288184-272d9fba-9549-4dd9-b2c8-fce72eaf6021.png)
![heap-insert1](https://user-images.githubusercontent.com/61190690/167288185-5e97d889-a888-450c-9ab8-4d9ef52104e0.png)

```swift
public mutating func insert(_ value: T) {
	nodes.append(value)
	shiftUp(nodes.endIndex)
}
```

&nbsp;
### 삭제

노드를 삭제하고 트리의 가장 마지막 노드를 삭제한 노드의 위치로 옮긴다. 그리고 `shiftDown`

![heap-remove1](https://user-images.githubusercontent.com/61190690/167288212-611eb154-6d9a-4d41-b7d0-6eb273481739.png)
![heap-remove2](https://user-images.githubusercontent.com/61190690/167288213-79df3242-e9a1-4492-b364-2d7375b643dc.png)
![heap-remove3](https://user-images.githubusercontent.com/61190690/167288214-6b11032a-5ca3-4f12-a116-f84a57752311.png)
![heap-remove4](https://user-images.githubusercontent.com/61190690/167288215-f790306b-4e4d-4085-bf44-7256fceb655a.png)

```swift
@discardableResult public mutating func remove(at: index: Int) -> T? {
	guard index =< nodes.endIndex else { return nil }
	let lastNode = nodes.romoveLast()
	nodes[index] = lastNode
	shiftDown(from: index)
}
```