# 삽입 정렬

두 번째 원소부터 그 앞에 위치한 원소들과 비교하여 삽입할 위치를 정하는 알고리즘이다.

## 장점

- 원소의 수가 적을수록 효율이 좋다.
- 대부분의 원소가 정렬되어 있을 때 매우 효율적이다.
- 배열의 모든 원소를 비교하므로 안정적인 정렬 방식이다.

&nbsp;
## 단점

- 비교적 많은 원소들의 이동을 포함한다.
- 원소의 수가 많고 크기가 크면 부적합하다.

&nbsp;
## 구현

- 삽입 정렬은 두 번째 원소부터 시작하는 것이 중요하다.
- 아래의 코드는 오름차순 정렬이다.

    ```swift
    func insertionSort(_ array: [Int]) -> [Int] {
        var sortedArray = array	
        for i in 1..<sortedArray.count {
            var currentIndex = i
            while currentIndex > 0 && sortedArray[currentIndex] < sortedArray[currentIndex - 1] {
                sortedArray.swapAt(currentIndex - 1, currentIndex)
                currentIndex -= 1
            }
        }
        return sortedArray
    }
    ```

- **1회전** : [3, 5, 8, 4, 6]

    - 두 번째 원소인 5와 앞에 위치한 3을 비교한다.

    - 5가 3보다 더 크므로 교환을 하지 않는다.
    
- **2회전** : [3, 5, 8, 4, 6] - [3, 5, 8, 4, 6]

    - 8과 5를 비교해서 8이 더 크므로 교환을 하지 않는다.

    - 8과 3을 비교해서 8이 더 크므로 교환을 하지 않는다.
    
- **3회전** : [3, 5, 8, 4, 6] - [3, 5, 4, 8, 6] - [3, 4, 5, 8 ,6]

    - 4와 8을 비교해서 4가 더 작으므로 교환한다.

    - 4와 5를 비교해서 4가 더 작으므로 교환한다.

    - 4와 3을 비교해ㅓ 4가 더 크므로 교환을 하지 않는다.

