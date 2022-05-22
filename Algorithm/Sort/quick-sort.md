# 퀵 정렬

퀵 정렬은 [분할 정복 알고리즘](./merge-sort.md) 중 하나로 병합 정렬과 달리 리스트를 비균등하게 분할한다.

## 용어

1. 피벗(Pivot) : 리스트를 분할하기 위해 선택한 원소이다.
2. 파티셔닝(Partitioning) : 피벗을 기준으로 입력 배열을 2개의 부분 배열로 분할하는 과정이다.

&nbsp;
## 장점

- 평균적으로 수행 속도가 매우 빠르다. 평균적으로 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(nlogn)">의 시간 복잡도를 갖는 다른 정렬 알고리즘과 비교해도 가장 빠르다.
- <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(logn)"> 만큼의 메모리 공간 외 추가적으로 필요하지 않는다.

&nbsp;
## 단점

- 정렬된 리스트에 대해서는 비균등 분할에 의해 오히려 수행 시간이 길다. 최악의 경우 시간 복잡도는 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(n^2)">이다.

> 비균등 분할을 방지하기 위해 최대한 리스트를 균등하게 분할할 수 있는 피벗을 선택한다.

&nbsp;
## 구현

배열을 사용하여 퀵 정렬을 하는 과정이다. 아래의 배열을 퀵 정렬해보자.

```
[10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26]
```

퀵 정렬은 분할 정복 알고리즘에 따라 분할 - 정복 - 결합의 단계로 이루어진다. 각 단계를 살펴보자.

1. **분할** : 입력 배열을 피벗을 기준으로 2개의 부분 배열로 분할한다. 중요한 점은 왼쪽 부분 배열에는 피벗보다 작은 원소를, 오른쪽 부분 배열에는 피벗보다 큰 원소가 있도록 분할한다. 일단 입력 배열의 가운데 원소를 피벗으로 선택하는 방식으로 구현해보자. 초기 상태에서 피벗은 `8`이다.

    ```
    Less:     [0, 3, 2, 1, 5, -1]
    Equal:    [8, 8]
    Greater:  [10, 9, 14, 27, 26]
    ```

2. **정복** : 부분 배열을 정렬한다. 만약 부분 배열의 크기가 충분히 작지 않다면 재귀 호출을 이용하여 다시 분할한다.

    1에서 왼쪽 부분 배열인 `[0, 3, 2, 1, 5, -1]`을 살펴보니 크기가 충분히 작지 않다. `1`을 피벗으로 다시 분할하자.(피벗으로 2를 선택해도 문제 없다.)

    ```
    Less:    [0, -1]
    Equal:   [1]
    Greater: [3, 2, 5]
    ```

    같은 방식으로 더 이상 부분 배열을 분할할 수 없을때 까지 반복한다. 결과 적으로 입력 배열은 아래와 같이 분할된다. 분할을 할 때마다 피벗은 최종 정렬 위치에 있다.

    ```
                       [10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26]

                    (Less)                (Equal)            (Greater)
                      ⇊                     ⇊                   ⇊

              [0, 3, 2, 1, 5, -1]         [8, 8]         [10, 9, 14, 27, 26]     

            ⇊         ⇊         ⇊                     ⇊         ⇊         ⇊

         [0, -1]      [1]    [3, 2, 5]               [10, 9]     [14]    [27, 26]

      ⇊    ⇊    ⇊         ⇊    ⇊    ⇊          ⇊    ⇊    ⇊        ⇊    ⇊    ⇊

      []  [-1]  [0]        []   [2] [3, 5]       []    [9]  [10]      []   [26]  [27]
                                
                                  ⇊   ⇊   ⇊

                                 [3]  [5]   []

    ```

3. **결합** : 부분 배열을 하나의 배열에 합친다.

    ```
    [-1, 0, 1, 2, 3, 5, 8, 8, 9, 10, 14, 26, 27]
    ```

&nbsp;
### Pseudocode

```swift
func quickSort<T: Comparable>(_ array: [T]) -> [T] {
  guard array.count > 1 else { return array }

  let pivot = array[array.count/2]
  let less = array.filter { $0 < pivot }
  let equal = array.filter { $0 == pivot }
  let greater = array.filter { $0 > pivot }
  return quickSort(less) + equal + quickSort(greater)
}
```

입력 배열의 가운데 원소를 피벗으로 선택하여 구현한 퀵 정렬 알고리즘이다. 하지만 `filter` 함수를 세 번이나 호출하여 Partitioning 하는 것은 비효율적이다. 보다 효과적인 분할 알고리즘을 살펴보자.

&nbsp;
## Lomuto Partitioning

Lomuto 알고리즘은 항상 배열의 마지막 요소를 피벗으로 사용한다. 또한 for 루프에서 배열을 4개의 영역으로 나눈다.

1. `array[low...i]` : 피벗보다 작거나 같은 원소가 포함
2. `array[i+1...j-1]` : 피벗보다 큰 원소가 포함
3. `array[j...high-1]` : 아직 분할 과정을 거치지 않은 배열의 원소들
4. `array[high]` : 피벗

배열의 마지막 원소를 피벗으로 사용하여 위치를 고정시키고, for 루프가 끝날 때 피벗보다 큰 첫 번째 요소와 피벗의 위치를 바꾼다. 따라서 피벗을 기준으로 왼쪽은 작은 값, 오른쪽은 큰 값으로 배열이 적절하게 분할된다.

`high`와 `low`는 전체 배열을 분할하지 않고 일부분만 분할하고 싶을 경우 사용하므로, 전체 배열을 분할하는 경우 `high`는 `array.count - 1`, `low`는 `0`과 동일하다.

Lomuto 알고리즘의 동작 순서:

1. `j`에 위치한 원소와 피벗을 비교한다.
2. 피벗보다 작다면 `j`에 위치한 원소와 `i`에 위치한 원소를 맞바꾼다.
3. `j`의 값을 1만큼 증가시킨다.
4. `j`의 값이 `high`가 될 때까지 반복한다.

&nbsp;
### 예시

Lomuto 알고리즘을 통해 분할하는 과정을 살펴보자. 앞서 입력 배열의 가운데를 피벗으로 사용하는 방식과 대조하기 위해 입력 배열을 아래와 같이 변경한다.

```
[10, 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1, 8]
 low                                      high
 i
 j
```

`low`, `i`, `j`의 초기값은 모두 0이다. 또한 피벗의 위치를 나타내는 `high`의 값은 배열의 마지막 원소 8의 위치인 12이다. 

`j`에 위치한 10이 8보다 크므로 다음 원소로 넘어간다.

```
[10, 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1, 8]
 low                                      high
 i
     j
```

`j`에 위치한 0이 8보다 작으므로 `i`에 위치한 10과 0의 위치를 맞바꾼다. 그리고 `i`의 값을 1만큼 증가시키고 다음 원소로 넘어간다.

```
[0, 10, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1, 8]
 low                                      high
     i
        j
```

`j`가 `high` 값이 될 때 까지 반복하면 배열은 아래와 같이 변경된다.

```
[0, 3, 2, 1, 5, 8, -1, 27, 9, 10, 14, 26, 8]
 low                                      high
                        i                 j
```

이제 피벗보다 큰 27, 9, 10, 14, 26, 8 중 첫 번째에 위치한 27을 피벗과 맞바꾼다.

```
[0, 3, 2, 1, 5, 8, -1, 8, 9, 10, 14, 26, 27]
 low                                     high
                       i                 j
```

비로소 피벗인 8을 기준으로 왼쪽은 작은 값, 오른쪽은 큰 값으로 배열이 분할됐다. `filter`를 통해 새 배열을 반환하는 것보다 입력 배열의 내용을 직접 변경하므로 훨씬 효율적이다. 

&nbsp;
### Pseudocode

```swift
func partitionLomuto<T: Comparable>(_ array: inout [T], low: Int, high: Int) -> Int {
  let pivot = array[high]

  var i = low
  for j in low..<high {
    if array[j] <= pivot {
      array.swapAt(i, j)
      i += 1
    }
  }

  array.swapAt(i, high)
  return i
}
```

입력 배열을 분할하고 피벗의 위치인 `i`의 값을 반환한다. 이것을 퀵 정렬에 적용해보자.

```swift
func quizSortLomuto<T: Comparable>(_ array: inout [T], low: Int, high: Int) {
  if low < high {
    let pivot = partitionLomuto(&array, low: low, high: high)
    quickSortLomuto(&array, low: low, high: pivot - 1)
    quickSortLomuto(&array, low: pivot + 1, high: high)
  }>
}
```

&nbsp;
## Hoare Partitioning

Lomuto 알고리즘과 다르게 배열의 첫 번째 원소, 즉 `low`에 위치한 원소가 피벗이다. 또한 `i`와 `j` 모두 0부터 시작하는 것이 아닌 `i`는 `low`, `j`는 `high + 1` 부터 시작한다. 

Hoare 알고리즘의 동작 순서:

1. `array[i]`가 피벗보다 클 때 까지 `i`의 값을 증가시킨다.
2. `array[j]`가 피벗보다 작을 때 까지 `j`의 값을 증가시킨다.
3. 1,2에서 값을 모두 찾았다면 `array[i]`와 `array[j]`의 위치를 맞바꾼다.
4. `i`와 `j`의 값이 겹칠 때 까지 반복한다.
5. `array[j]`와 피벗의 위치를 맞바꾼다.

&nbsp;
### 예시

Hoare 알고리즘을 통해 분할하는 과정을 살펴보자. Lomuto 알고리즘과 마찬가지로 앞서 입력 배열의 가운데를 피벗으로 사용하는 방식과 대조하기 위해 입력 배열을 아래와 같이 변경한다.

```
[8, 0, 3, 9, 2, 14, 10, 27, 1, 5, 8, -1, 26]
 low                                     high
 i                                             j
```

이제 `i`의 값을 1만큼 증가시키고, `j`의 값을 1만큼 감소시키자.

```
[8, 0, 3, 9, 2, 14, 10, 27, 1, 5, 8, -1, 26]
 low                                     high
    i                                    j
```

`array[i]`는 0으로 피벗보다 값이 작으니 다음 원소로 넘어간다. 또한 `array[j]`의 값은 26으로 피벗보다 크니 다음 원소로 넘어간다.

```
[8, 0, 3, 9, 2, 14, 10, 27, 1, 5, 8, -1, 26]
 low                                     high
       i                              j
```

`array[i]`는 3으로 피벗보다 값이 작으니 다음 원소로 넘어간다. 또한 `array[j]`의 값은 -1으로 피벗보다 값이 작으니 멈춘다.

```
[8, 0, 3, 9, 2, 14, 10, 27, 1, 5, 8, -1, 26]
 low                                     high
          i                           j
```

`array[i]`는 9로 피벗보다 값이 크니 멈춘다. 이제 `i`와 `j` 루프에서 값을 모두 찾았으니 `array[i]`와 `array[j]`의 위치를 맞바꾼다.

```
[8, 0, 3, -1, 2, 14, 10, 27, 1, 5, 8, 9, 26]
 low                                     high
           i                          j
```

위 과정을 `i`와 `j`가 겹칠 때 까지 반복한다. `i`와 `j`가 겹칠 때 배열은 아래와 같이 변경된다.

```
[8, 0, 3, -1, 2, 8, 5, 1, 27, 10, 14, 9, 26]
 low                                     high
                       i  j
```

이제 피벗과 `array[i]`의 위치를 맞바꾼다.

```
[1, 0, 3, -1, 2, 8, 5, 8, 27, 10, 14, 9, 26]
 low                                     high
                       i  j
```

피벗인 8을 기준으로 왼쪽은 작은 값, 오른쪽은 큰 값으로 분할되었다. 

&nbsp;
### Pseudocode

```swift
func partitionHoare<T: Comparable>(_ array: inout [T], low: Int, high: Int) -> Int {
  let pivot = array[low]
  var i = low - 1
  var j = high + 1

  while true {
    repeat { j -= 1 } while array[j] < pivot
    repeat { i += 1 } while array[i] > pivot

    if i < j {
      array.swap(i, j)
    } else {
      return j
    }
  }
}
```

`while` 루프 안에 `j`와 `i`의 값을 반복해서 증감시키는 두 개의 `repeat` 루프가 보인다. 이것을 퀵 정렬에 적용해보자.

```swift
func quickSortHoare<T: Comparable>(_ array: inout [T], low: Int, high: Int) {
  if low < high {
    let pivot = partitionHoare(&array, low: low, high: high)
    quickSortHoare(&array, low: low, high: pivot)
    quickSortHoare(&array, low: pivot + 1, high: high)
  }
}
```

