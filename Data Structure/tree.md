# Tree

비선형 자료구조로 계층적 관계를 표현한다. 단순히 선형 자료처럼 무언가를 저장하고 꺼내는 사고에서 벗어나자

## 용어

<img src="https://user-images.githubusercontent.com/61190690/167287249-264792dd-380b-4cca-b436-732970fa51d9.png" width="500">


1. **노드(Node)** : 트리를 구성하고 있는 각각의 요소

2. **간선(Edge)** : 노드와 노드를 연결하는 선

3. **루트 노드(Root Node)** : 최상위에 있는 노드

4. **단말 노드(Terminal Node, Leaf Node)** : 하위에 다른 노드가 연결되어 있지 않은 노드

5. **비단말 노드, 내부 노드(Internal Node)** : 단말 노드를 제외한 모든 노드

6. **경로(Path)** : 간선으로 연결된, 즉 인접한 노드들로 이뤄진 시퀸스

&nbsp;
## 속성

루트 노드를 제외한 모든 노드는 단 하나의 부모 노드만을 가진다. 그로인해 다음과 같은 성질을 만족한다.

- 임의의 노드에서 다른 노드로 가는 경로는 유일하다. 아래의 그림은 4로 가는 경로가 두 개이므로 트리가 아니다.
        
	![tree-properties1](https://user-images.githubusercontent.com/61190690/167287316-de5204ca-5e9c-4f5f-843d-a8173e9e132c.png)
        

- 모든 노드는 서로 연결되어 있다. 아래의 그림은 모든 노드가 연결되지 않아 트리가 아니다.
        
    ![tree-properties2](https://user-images.githubusercontent.com/61190690/167287364-3c3b46e2-b2b3-4bca-9c6a-8988fbb1d98a.png)
        
- 회로(Cycle)가 존재하지 않는다.
        
    ![tree-properties3](https://user-images.githubusercontent.com/61190690/167287380-83991fbe-f9a2-44e8-aaaa-36f462681bf5.png)

- 간선을 하나 자르면 두 개의 트리로 분리된다.
- 엣지의 수는 노드의 수에서 1을 뺀 것과 같다.

&nbsp;
## Tree Traversal

트리 순회, 트리의 각 노드를 체계적인 방법으로 방문하는 과정이다. 체계적이란 트리의 모든 노드를 중복없이 한 번만 방문하는 것을 의미하며, 노드를 방문하는 순서에 따라 세 가지 방식이 있다.

![tree-traversal](https://user-images.githubusercontent.com/61190690/167287459-e912dad7-7509-411d-9324-b56f3e98b53e.png)


## 전위순회

Preorder, 루트 노드 → 왼쪽 서브트리 → 오른쪽 서브트리 순으로 순회하는 방식으로 깊이 우선 순회(Depth-First Traversal) 라고도 한다.

```swift
func preorder(node: Node?) {
	guard let node = node else { return }
	print(node.data)
	preorder(node: node.left)
	preorder(node: node.right)
}
```

> 1 → 2 → 4 → 5 → 3

&nbsp;
## 중위순회

inorder, 왼쪽 서브트리 → 루트 노드 → 오른쪽 서브트리 순으로 순회하는 방식으로 대칭 순회(Symmetric Traversal) 라고다 한다.

```swift
func inorder(node: Node?) {
	guard let node = node else { return }
	preorder(node: node.left)
	print(node.data)
	preorder(node: node.right)
}
```

> 4 → 2 → 5 → 1 → 3

&nbsp;
## 후위순회

postorder, 왼쪽 서브트리 → 오른쪽 서브트리 → 루트 노드 순으로 순회하는 방식이다.

```swift
func postorder(node: Node?) {
	guard let node = node else { return }
	preorder(node: node.left)
	preorder(node: node.right)
	print(node.data)
}
```

> 4 → 5 → 2 → 3 → 1

&nbsp;
## Binary Tree

이진 트리, 자식 노드가 최대 두 개인 노드들로 구성된 트리이다.

&nbsp;
## Full Binary Tree

정 이진 트리, 모든 레벨의 노드들이 두 개의 자식 노드를 갖는다.

![full-binary-tree](https://user-images.githubusercontent.com/61190690/167287518-f522d09c-c1c6-48f7-863b-df9b5c84d850.png)


각 레벨에 따른 노드의 숫자는 아래와 같다.

| 레벨 | 노드 수 |
| :---: | :---: |
| 0 | $2^0$ |
| 1 | $2^1$ |
| 2 | $2^2$ |
| ... | ... |
| $k$ | $2^k$ |
| total | $2^{k+1}-1$ |

&nbsp;
## Complete Binary Tree

완전 이진 트리, **마지막 레벨을 제외**한 모든 레벨의 노드들이 두 개의 자식 노드를 갖는다.

![complte-binary-tree](https://user-images.githubusercontent.com/61190690/167287565-29f489cd-59e0-4a4e-8021-97cb6156f217.png)

정 이진 트리와 완전 이진 트리는 **1차원 배열**로 나타낼 수 있다.

![complte-binary-tree2](https://user-images.githubusercontent.com/61190690/167287576-be377c8a-1370-4c69-a34c-b34fbb5189d8.png)

### 관계식

- 트리의 최소 높이 = $log_2(N+1)$
- 루트 노드의 인덱스가 0인 경우
    
    ```swift
    parent_idx = floor((index - 1)/2) // floor: 소수점 아래 제거
    left_index = 2 * index + 1
    right_index = 2 * index + 2
    ```
    
- 루트 노드의 인덱스가 1인 경우
    
    ```swift
    parent_idx = floor(index/2)
    left_index = 2 * index
    right_index = 2 * index + 1
    ```
    
&nbsp;
## Binary Search Tree

이진 탐색 트리, 이진 트리를 탐색용 자료구조로 사용하기 위해 원소 크기에 따라 노드의 위치를 정의한다. 

<img src="https://user-images.githubusercontent.com/61190690/167287620-2b633788-6ed9-4036-9ddf-bcc6b8aad875.png" width="500">

&nbsp;
### 데이터 저장 규칙

1. 이진 탐색 트리의 노드에 저장된 키는 유일하다.

2. 루트 노드의 키가 왼쪽 서브트리를 구성하는 어떠한 노드의 키보다 크다.

3. 루트 노드의 키가 오른쪽 서브트리를 구성하는 어떠한 노드의 키보다 작다.

4. 왼쪽•오른쪽 서브트리도 마찬가지로 이진 탐색 트리이다.

&nbsp;
### 특징

1. 중위순회를 통해 모든 키를 정렬된 순서로 가져올 수 있다.
2. 검색∙삽입∙삭제에 대한 시간 복잡도는 균형 상태일 경우 $O(logN)$, 불균형 상태일 경우 최대 $O(N)$이다.

	<img src="https://user-images.githubusercontent.com/61190690/167287714-b7502c33-bc35-4142-99ae-21d7602ec39d.png" width="500">

    데이터 저장 규칙에 따라 한 쪽으로만 노드가 추가되는 경우를 불균형 상태라고 한다. 배열보다 많은 메모리를 사용하며 데이터를 저장했지만 탐색에 필요한 시간 복잡도가 같게 되는 비효율적인 상황이 발생한다. 이를 해결하기 위해 **Rebalancing** 기법이 등장하였다. **Rebalancing** 이란 균형을 잡기 위한 트리 구조의 재조정을 뜻하며 이 기법을 구현한 트리 중 Red-Black Tree가 있다.
    
&nbsp;
### 구현

- $h$ : 트리의 높이, $log_2N$으로 나타낼 수 있다.
- $n$ : 노드의 갯수

	```swift
	public class BinarySearchTree<T: Comparable> {
		public var parent: BinarySearchTree?
		public var left: BinarySearchTree?
		public var right: BinarySearchTree?
		public var value: T

		public init(value: T) {
			self.value = value
		}

		public var isLeaf: Bool {
			return left == nil && right == nil
		}

		public var isRoot: Bool {
			return parent == nil
		}

		public var count: Int {
			return 1 + left.count ?? 0 + right.count ?? 0
		}
	}
	```

&nbsp;

**삽입**

```swift
public func insert(value: T) {
	if value < self.value {
		if let left = left {
			left.insert(value: value)
		} else {
			left = BinarySearchTree(value: value)
			left.parent = self
		}
	} else {
		if let right = right {
			right.insert(value: value)
		} else {
			right = BinarySearchTree(value: value)
			right.parent = self
		}
	}
}
```

&nbsp;

**삭제**

- 삭제할 노드가 단말 노드인 경우 : 부모 노드와의 관계를 끊는다.
    
    ![binary-search-tree3](https://user-images.githubusercontent.com/61190690/167287771-17c73401-f2f9-4999-829a-7dc62b72368e.png)
    

- 삭제할 노드가 하나의 서브트리를 갖음 : 삭제할 노드의 부모 노드와 서브트리를 붙인다.
    
	![binary-search-tree5](https://user-images.githubusercontent.com/61190690/167287818-36f84f5d-ee63-4341-babc-45c90a074859.png)
    

- 삭제할 노드가 두 개의 서브트리를 갖음 : 삭제할 노드의 (1)**왼쪽 서브트리 중 가장 큰 값**을 삭제할 위치에 가져오거나 (2)**오른쪽 서브트리 중 가장 작은 값**을 삭제할 위치에 가져온다.
    
    ![binary-search-tree4](https://user-images.githubusercontent.com/61190690/167287774-920fc9d7-4cf2-43ab-bf5c-2a4728e871f0.png)
    

	```swift
	@discardableResult public func remove() -> BinarySearchTree? {
		let replacement: BinarySearchTree?

		if let right = right {
			replacement = right.minimum()
		} else if let left = left {
			replacement = left.maximum()
		} else {
			replacement = nil
		}

		replacement?.left = left
		replacement?.right = right
		left.parent = replacement
		right.parent = replacement

		if let parent = parent {
			if parent.left == self {
				parent.left = replacement
			} else if parent.right == self {
				parent.right = replacement
			}
		}

		replacement?.parent = parent
		parent = nil
		left = nil
		right = nil

		return replacement
	}
	```

&nbsp;

**검색**

```swift
public func search(value: T) -> BinarySearchTree? {
	var node: BinarySearchTree<T>? = self

	while let node = node {
		if value < self.value {
			node = self.left
		} else if value > self.value {
			node = self.right
		} else {
			return node
		}
	}

	return nil
}
```

재귀를 이용하여 구현할 수 있다.

```swift
public func search(value: T) -> BinarySearchTree? {
	if value < self.value {
		return left?.search(value: value)
	} else if value > self.value {
		return right?.search(value: value)
	} else {
		return self
	}
}
```

&nbsp;

**최솟값**

- 시간 복잡도 : 한 쪽 방향으로 순회하므로 트리의 높이만큼, $O(h)$ 혹은 $O(logN)$으로 나타낼 수 있다.

	```swift
	public func minimum() -> BinarySearchTree {
		var node: BinarySearchTree<T> = self

		while let left = node.left {
			node = left
		}
		return node
	}
	```

&nbsp;

**최댓값**

- 시간 복잡도 : 한 쪽 방향으로 순회하므로 트리의 높이만큼, $O(h)$ 혹은 $O(logN)$으로 나타낼 수 있다.

	```swift
	public func maximum() -> BinarySearchTree {
		var node: BinarySeachTree<T> = self

		while let right = node.right {
			node = right
		}
		return node
	}
	```

&nbsp;

**트리의 높이**

- 시간 복잡도 : $O(n)$, 모든 노드 순회

	```swift
	public func height() -> Int {
		if isLeaf {
			return 0
		} else {
			return 1 + max(left?.height() ?? 0, right?.height() ?? 0)
		}	
	}
	```

&nbsp;

**깊이**

- 시간 복잡도 : $O(n)$, 모든 노드 순회

	```swift
	public func depth() -> Int {
		var node: BinarySearchTree<T> = self
		var edges = 0

		while let parent = node.parent {
			node = parent
			edges += 1
		}
		return edges
	}
	```

&nbsp;

**현재 트리가 이진 탐색 트리인지 판단**

- 왼쪽 분기에 현재 노드의 값보다 작은 값만 포함되어 있고, 오른쪽 분기에는 더 큰 값만 포함되어 있는지 확인
- 시간 복잡도 : $O(n)$, 모든 노드 순회

	```swift
	public func isBST(minValue: T, maxValue: T) -> Bool {
		if value < minValue || value > maxValue { return false }
		let isLeftBST = left?.isBST(minValue: minValue, maxValue: value) ?? true
		let isRightBST = right?.isBST(minValue: value, maxValue: maxValue) ?? true
		return isLeftBST && isRightBST
	}

	public func isBST() -> Bool {
		return isBST(minValue: Int.min, maxValue: Int.max)
	}
	```