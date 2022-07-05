# 병합 정렬

정렬되지 않은 리스트를 절반으로 나눠 각 부분 리스트를 재귀적으로 합병 정렬한 뒤 하나로 합치는 정렬 알고리즘이다. 병합 정렬은 폰 노이만이 제안한 방법으로 안정 정렬에 속하며, 분할 정복 알고리즘의 하나이다.

[분할 정복 알고리즘](https://ko.wikipedia.org/wiki/%EB%B6%84%ED%95%A0_%EC%A0%95%EB%B3%B5_%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)이란 그대로 해결할 수 없는 문제를 작은 문제로 분할하여 해결하는 알고리즘이다. 분할 정복 알고리즘의 대표적인 정렬 알고리즘으로 병합 정렬과 퀵 정렬이 있다. 일반적으로 재귀 함수를 통해 구현되며, 빠른 실행이나 부문제 해결 순서를 선택하기 위해 스택, 큐 등의 자료구조를 이용하는 방법도 존재한다. 분할 정복 알고리즘은 분할(Divide) - 정복(Conquer) - 결합(Combine)의 단계로 이루어진다는 점을 숙지하고, 분할 정복 알고리즘에 대한 글은 따로 작성하기로 하자..

&nbsp;
## 장점

- 입력 데이터가 무엇이든 간에 정렬되는 시간은 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(nlogn)">으로 동일하여 안정적인 정렬 방법이다.
- 크기가 큰 레코드를 [연결 리스트](../../Data%20Structure/linked-list.md)를 사용하여 정렬할 경우 정렬 알고리즘 중 가장 효율적이다.

&nbsp;
## 단점

- 배열로 정렬을 할 경우에 여러가지 단점이 있다.
  - 문제를 나누는 데 임시 배열을 사용하여 추가적인 메모리 공간이 필요하다.
  - 제자리 정렬이 아니므로 크기가 큰 레코드의 경우 이동 횟수가 많으므로 시간적 낭비를 초래한다.
> 연결 리스트를 사용하면 위 단점을 개선할 수 있다.

&nbsp;
## 구현

배열을 사용하여 병합 정렬을 하는 과정이다. 아래의 배열을 병합 정렬해보자.

```
[21, 10, 12, 20, 25, 13, 15, 22]
```

병합 정렬은 분할 정복 알고리즘에 따라 분할 - 정복 - 결합의 단계로 이루어진다. 각 단계를 살펴보자.

1. **분할** : 입력 배열을 같은 크기의 2개의 부분 배열로 분할한다. 부분 배열을 더 이상 분할할 수 없을때 까지 반복하여 n개의 원소를 병합 정렬하기 위해서는 하나의 숫자가 있는 n개의 부분 배열이 생긴다. 각 부분 배열에 대해 정복 - 결합 과정을 반복하여 최종적으로 하나의 배열로 합병한다.

    ```
    [21, 10, 12, 20]
    [25, 13, 15, 22]
    ```

    ```
    [21, 10], [12, 20]
    [25, 13], [15, 22]
    ```

    ```
    [21], [10], [12], [20]
    [25], [13], [15], [22]
    ```

2. **정복** : 부분 배열을 정렬한다.

3. **결합** : 정렬된 부분 배열들을 하나의 배열에 병합한다.

    ```
    [21]  [10]     [12]  [20]     [25]  [13]     [15]  [22]

        ⇊              ⇊             ⇊              ⇊

     [10, 21]       [12, 20]       [13, 25]       [15, 22]

                ⇊                             ⇊

        [10, 12, 20, 21]               [13, 15, 22, 25]
                              ⇊

                [10, 12, 13, 15, 20, 21, 22, 25]
    ```

&nbsp;

이제 코드로 살펴보자.

```swift
func mergeSort(_ array: [Int]) -> [Int] {
  guard array.count > 1 else { return array }    // 1
  let middleIndex = array.count / 2              // 2
  let leftArray = mergeSort(Array(array[0..<middleIndex]))             // 3
  let rightArray = mergeSort(Array(array[middleIndex..<array.count]))  // 4
  return merge(leftPile: leftArray, rightPile: rightArray)             // 5
}
```

- 1 : 배열이 비어있거나 1개의 원소만 가지고 있을 경우 배열을 그대로 반환한다.

- 2 : 배열을 절반으로 나누기 위한 인덱스를 찾는다.

- 3 : 배열의 절반을 기준으로 왼쪽을 재귀적으로 분할한다.

- 4 : 마찬가지로 배열의 절반을 기준으로 오른쪽을 재귀적으로 분할한다.

- 5 : 모든 부분 배열을 정렬하고 병합한다.

  ```swift
  func merge(leftPile: [Int], rightPile: [Int]) -> [Int] {
    // 1
    var leftIndex = 0
    var rightIndex = 0

    // 2
    var orderedPile = [Int]()
    orderedPile.reserveCapacity(leftPile.count + rightPile.count)

    // 3
    while leftIndex < leftPile.count && rightIndex < rightPile.count {
      if leftPile[leftIndex] < rightPile[rightIndex] {
        orderedPile.append(leftPile[leftIndex])
        leftIndex += 1
      } else if leftPile[leftIndex] > rightPile[rightIndex] {
        orderedPile.append(rightPile[rightIndex])
        rightIndex += 1
      } else {
        orderedPile.append(leftPile[leftIndex])
        leftIndex += 1
        orderedPile.append(rightPile[rightIndex])
        rightIndex += 1
      }
    }

    // 4
    while leftIndex < leftPile.count {
      orderedPile.append(leftPile[leftIndex])
      leftIndex += 1
    }

    while rightIndex < rightPile.count {
      orderedPile.append(rightPile[rightIndex])
      rightIndex += 1
    }

    return orderedPile
  }
  ```

  두 개의 부분 배열 `leftPile`과 `rightPile`을 병합 정렬한다. `leftPile`과 `rightPile`은 내부적으로 이미 정렬되어 있는 상태이다.

  - 1 : 병합하는 동안 두 배열의 진행 상황을 추적하기 위한 인덱스이다.

  - 2 : 두 배열의 병합된 배열이다. 실제로 병합을 하기 전 병합된 배열의 길이를 이미 알고있다.

    > 나중에 재할당 오버헤드를 피하기 위해 [reverseCapacity](https://developer.apple.com/documentation/swift/array/1538966-reservecapacity) 함수를 통해 공간을 미리 예약한다.

  - 3 : 왼쪽과 오른쪽의 요소를 비교하여 병합된 결과가 순서대로 유지되는지 확인하고 `orderPile`에 추가한다.

      ```
      leftPile      rightPile     orderedPile
      [1, 7, 8]     [3, 6, 9]     []
       l             r
      ```

      `leftPile`의 첫 번째 요소인 1이 `rightPile`의 첫 번째 요소인 3보다 작으므로 `orderPile`에 추가하고 `leftIndex`의 값을 하나 증가시킨다.

      ```
      leftPile      rightPile     orderedPile
      [1, 7, 8]     [3, 6, 9]     [1]
       -->l          r              
      ```

      `rightPile`의 첫 번째 요소인 3이 `leftPile`의 두 번째 요소인 7보다 작으므로 `orderPile`에 추가하고 `rightIndex`의 값을 하나 증가시킨다.

      ```
      leftPile      rightPile     orderedPile
      [1, 7, 8]     [3, 6, 9]     [1]
          l          -->r              
      ```

  - 4 : 과정3 에서 `leftPile`과 `rightPile` 중 `orderedPile` 모든 요소를 추가한 부분 배열이 있을 때, 다른 부분 배열의 나머지 요소를 `orderedPile`에 추가한다.

      ```
      leftPile       rightPile       orderedPile
      [1, 7, 8]      [3, 6, 9]       [1, 3, 6]
          l              -->r

      leftPile       rightPile       orderedPile
      [1, 7, 8]      [3, 6, 9]       [1, 3, 6, 7]
          -->l              r

      leftPile       rightPile       orderedPile
      [1, 7, 8]      [3, 6, 9]       [1, 3, 6, 7, 8]
             -->l           r
      ```

