# 힙 정렬

[Heap](../../Data%20Structure/heap.md)을 이용해 최대 힙 혹은 최소 힙을 구성해 정렬하는 알고리즘이다.

## 장점

- 추가적인 메모리를 필요로 하지 않으면서 항상 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(logN)">의 시간 복잡도를 갖는다.
- 가장 큰 값이나 가장 작은 값을 구할 때 유용하다.

&nbsp;
## 단점

- 데이터의 상태에 따라 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(logN)">의 시간 복잡도를 갖는 정렬(퀵 정렬, 병합 정렬) 중 가장 느리다.
- [Heap](../../Data%20Structure/heap.md)의 특성 상 반정렬 상태를 유지하므로 불안정하다.

&nbsp;
## 구현

우선 배열을 내림차순 정렬하기 위해서는 최소 힙으로, 오름차순 정렬하기 위해서는 최대 힙으로 구성한다. 배열을 힙으로 구성하는 과정은 아래와 같다. 자세한 코드는 [buildHeap](../../Data%20Structure/heap.md/##배열을힙으로구성)를 참고하자.

```swift
private mutating func buildHeap(fromArray array: [T]) {
  for value in array {
    insert(value)
  }
}
```

배열을 힙으로 구성한 뒤 아래의 과정을 반복한다.

1. 힙의 최상위 노드는 최대값 혹은 최소값인 것을 이용해 최상위 노드와 말단 노드의 위치를 맞바꾼다.
2. 말단 노드와 위치를 맞바꾼 최상위 노드를 제외하고 다시 [Heapify](../../Data%20Structure/heap.md/##구현/###Heapify) 연산을 수행한다.

```swift
extension Heap {
  public mutating func sort() -> [T] {
    for i in stride(from: (elements.count - 1), through: 1, by: -1) {
      swap(&elements[0], &elements[i])
      shiftDown(0, heapSize: i)
    }
    return elements
  }
}
```


&nbsp;

힙 정렬의 과정을 그림으로 알아보자. 아래의 배열을 오름차순 정렬하고자 한다.

```
[7, 6, 5, 8, 3, 5, 9, 1, 6]
```

1차원 배열을 완전 이진 트리로 나타낸 그림은 아래와 같다.

<img src="https://user-images.githubusercontent.com/61190690/168402340-e8ba53b3-d8b7-4b65-a75b-2dfdfcfd3fff.png" width="400">

&nbsp;

오름차순 정렬을 하기 위해서 배열을 최대 힙으로 구성해야 한다.

<img src="https://user-images.githubusercontent.com/61190690/168402390-b54fea4b-3890-4af1-a420-13a235403d32.png" width="400">

배열을 최대 힙으로 구성하여 최상위 노드에 최대값인 9가 위치했다. 

&nbsp;

힙 정렬을 시작해보자. 우선 최상위 노드와 마지막 노드의 위치를 맞바꾼다. 빨갛게 표시된 노드는 정렬이 완료된 상태이다.

<img src="https://user-images.githubusercontent.com/61190690/168406163-4fb82dc7-1aac-4b67-846b-fa5607d6075a.PNG" width="400">

&nbsp;

9는 최대 힙의 최대값으로 오름차순 배열에서 알맞은 위치에 있으므로 9을 제외한 나머지 노드를 기준으로 [Heapify](../../Data%20Structure/heap.md/##구현/###Heapify) 연산을 수행한다.

<img src="https://user-images.githubusercontent.com/61190690/168406291-58f5b302-dd4a-4479-b2b8-e4502fd2e1ab.PNG" width="400">

&nbsp;

9를 제외한 최대 힙의 최대값인 8이 최상위 노드에 위치해있다. 8을 마지막에서 두 번째 원소인 1과 위치를 맞바꾼다.

<img src="https://user-images.githubusercontent.com/61190690/168406410-2ac886a7-bddf-4554-ab37-0e455d9440ba.PNG" width="400">

마찬가지로 8도 정렬이 완료된 상태이다. 정렬이 완료된 8과 9를 제외한 나머지 노드를 기준으로 [Heapify](../../Data%20Structure/heap.md/##구현/###Heapify) 연산을 수행하고 과정을 반복한다.