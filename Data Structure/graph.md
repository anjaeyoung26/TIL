# Graph

노드와 노드를 연결하는 간선을 하나로 모아 놓은 자료구조이다.

## 용어

1. **정점(Vertex)** : 노드의 위치

2. **간선(Edge)** : 노드를 연결하는 선(Link, Branch)

3. **인접 정점(Adjacent Vertex)** : 간선에 의해 연결된 정점

4. **정점의 차수(Degree)** : 하나의 정점에 인접한 정점의 수

5. **진입 차수(In-Degree)** : 방향 그래프에서 외부에서 오는 간선의 수(내차수)

6. **진출 차수(Out-Degree)** : 방향 그래프에서 외부로 향하는 간선의 수(외차수)

7. **경로 길이** : 경로를 구성하는 데 사용되는 간선의 수

8. **단순 경로** : 경로에서 반복되는 정점이 없는 경우

9. **사이클** : 단순 경로의 시작 정점과 종료 정점이 동일한 경우

&nbsp;
## vs Tree

|     | Graph | Tree |
| --- | --- | --- |
| 특징 | 1. 사이클이 가능하다.</br>2. 자체 간선이 가능하다.</br>3. 순환 그래프, 비순환 그래프</br>4. 부모-자식 노드의 개념이 없다.</br>5. 루트 노드의 개념이 없다. | 1. 사이클이 불가능하다.</br>2. 자체 간선이 불가능하다.</br>3. 비순환 그래프</br>4. 부모-자식 노드의 관계이다.</br>4. 한 개의 루트 노드가 존재한다. |
| 방향성 | 방향 그래프</br>무방향 그래프 모두 존재 | 방향 그래프 |
| 간선의 수 | 그래프에 따라 간선의 수가 상이하다. | 노드의 갯수(N) - 1 |
| 순회 | DFS, BFS | 전위 순회, 중위 순회, 후위 순회 |
| 예시 | 지도, 지하철 노선도의 최단 경로, 도로, 선수 과목 등 | 이진 트리, 이진 탐색 트리, 균형 트리, 이진 힙 등 |

&nbsp;
## 종류

### 무방향 그래프

<img src="https://user-images.githubusercontent.com/61190690/168468160-4c1a9713-29ac-478c-b42b-1cfe1f63df71.PNG" width="200">

간선을 통해서 양방향으로 연결되어 있는 정점으로 갈 수 있다. 정점 A와 정점 B를 연결하는 간선은 $(A, B)$와 같이 정점의 쌍으로 표현한다.

- 연결 그래프 : 무방향 그래프에 있는 모든 정점 쌍에 대해서 경로가 존재하는 그래프이며, 대표적인 예로 [Tree](./tree.md)가 있다.

	<img src="https://user-images.githubusercontent.com/61190690/168457437-96c75bf6-2e97-46a9-92f8-a516088097ca.png" width="200">

- 비연결 그래프 : 무방향 그래프에서 특정 정점 사이에 경로가 존재하지 않는 그래프이다.

### 방향 그래프

<img src="https://user-images.githubusercontent.com/61190690/168468165-8619b37f-e5b8-48d0-9123-9e707ed39ac4.PNG" width="200">

간선에 방향성이 존재하여 한 방향으로만 갈 수 있다. 정점 A에서 정점 B로 갈 수 있는 간선은 $<A, B>$로 표시한다.

### 완전 그래프

<img src="https://user-images.githubusercontent.com/61190690/168457508-f48519fa-7009-4804-b455-e5b81a83830a.PNG" width="200">

그래프의 모든 정점이 서로 연결되어 있는 그래프이다. 무방향 그래프와 방향 그래프 모두 완전 그래프가 될 수 있다.

### 가중치 그래프

<img src="https://user-images.githubusercontent.com/61190690/168468107-66fc35e8-2ed3-4477-ba74-88f20007bc19.PNG" width="200">

간선에 비용이 할당된 그래프로 네트워크 라고도 한다.

### 순환 그래프

단순 경로의 시작 정점과 종료 정점이 동일한 그래프로 사이클 그래프 라고도 한다.

### 비순환 그래프

사이클이 없는 그래프이다.

&nbsp;
## 구현 방식

### 인접 리스트

그래프를 표현하는 가장 일반적인 방법으로 인접 리스트를 사용한다. 모든 정점을 배열 혹은 해시 테이블에 저장하고 배열의 각 인덱스에 있는 정점과 인접한 정점들을 또 다른 리스트(배열, 연결 리스트 등)을 사용하여 표현한다. 정점이 배열에 저장된 위치만 알고 있다면 해당 정점의 인접한 정점으로 쉽게 접근할 수 있다.

<img src="https://user-images.githubusercontent.com/61190690/168456842-b9dcba80-88e4-405a-a09a-4a29cd5c6855.png" width="800">

**장점**
	
1. 인접한 노드를 쉽게 찾을 수 있다.

2. 필요한 만큼의 공간만 사용한다.

3. 그래프에 존재하는 모든 간선의 수를 $O(V+E)$ 시간 안에 알 수 있다.

**단점**

1. 두 정점이 연결되어 있는지 확인할 때 시간 복잡도는 $O(V)$이다. 인접 행렬에 비해 더 많은 시간이 소요된다.

	> 정확한 시간 복잡도는 $O(degree(V))$이다. degree(V)는 정점의 차수를 의미한다.

2. 인접 행렬에 비해 구현이 복잡하다.


&nbsp;
### 인접 행렬

2차원 배열을 사용하여 두 개의 노드가 연결되어 있을 때 간선의 가중치를 저장하고, 연결되어 있지 않을 때 0을 저장한다.

<img src="https://user-images.githubusercontent.com/61190690/168456843-105060ef-80a8-4d05-ab32-cc56febe07e2.png" width="800">

**장점**

1. 두 정점을 연결하는 간선의 존재 여부를 $O(1)$ 시간 안에 알 수 있다.

	> $matrix[i][j]$

2. 정점의 차수를 $O(V)$ 시간 안에 알 수 있다. 인접 배열의 $i$번째 행 또는 열을 모두 더한다.

**단점**

1. 그래프에 존재하는 모든 간선의 수는 인접 행렬의 전체를 조사해야 하므로 $O(V^2)$ 시간 안에 알 수 있다.

2. 간선의 수와 관계없이 항상 $V^2$개의 공간이 필요하므로 공간이 낭비될 수 있다.

3. 정점을 추가하려면 더 큰 행렬을 선언하고 모든 값을 복사해야 하므로 시간 복잡도는 $O(V^2)$이다.

&nbsp;
### 인접 리스트 vs 인접 행렬

인접 리스트와 인접 행렬 중 어떤 것을 사용해야 할까? 대부분의 경우 인접 리스트가 올바른 접근 방식이다. 우선 각 연산에 대해 시간 복잡도를 비교해보자.

| 연산 | 인접 리스트 | 인접 행렬 |
| --- | --- | --- |
| 저장 공간 | $O(V+E)$ | $O(V^2)$ |
| 정점 추가 | $O(1)$ | $O(V^2)$ |
| 간선 추가 | $O(1)$ | $O(1)$ |
| 인접 확인 | $O(V)$ | $O(1)$ |

> V : 정점의 개수, E : 간선의 개수

대부분의 정점이 서로 인접한 그래프일 경우 인접 행렬 방식을 사용한다.

&nbsp;
## 구현

인접 리스트, 인접 행렬 방식에서 모두 사용하는 클래스를 살펴보자.

### 간선

```swift
public struct Edge<T>: Equatable where T: Equatable, T: Hashable {
  public let from: Vertex<T>
  public let to: Vertex<T>

  public let weight: Double?
}
```

각 간선은 `from`에서 `to`의 방향을 나타내며, 가중치를 저장한다. 가중치는 `Optioanl<Double>` 타입으로 가중치 그래프와 가중치가 적용되지 않은 그래프에 모두 사용할 수 있다.

&nbsp;
### 정점

```swift
public struct Vertex<T>: Equatable where T: Equatable, T: Hashable {
  public var data: T
  public let index: Int
}
```

정점은 고유성을 강화하기 위해 `Hashable`과 `Equatable`인 제너릭 타입의 데이터를 저장한다. 또한 정점 자체도 `Equatable`이다.

&nbsp;
## 구현 - 인접 리스트

```swift
private class EdgeList<T> where T: Equatable, T: Hashable {
  var vertex: Vertex<T>
  var edges: [Edge<T>]? = nil

  init(vertex: Vertex<T>) {
    self.vertex = vertex
  }

  func addEdge(_ edge: Edge<T>) {
    edges?.append(edge)
  }
}
```

<img src="https://user-images.githubusercontent.com/61190690/168462748-970aba12-522e-4bb8-bfe4-9e7da7cbf1d1.png">

인접 리스트를 유지하기 위해 하나의 정점에 인접하는 모든 정점을 나타내는 클래스이다. 그래프는 이 객체의 배열을 유지하고 필요에 따라 수정한다.

> 간선, 정점과 다르게 클래스로 구현하는 이유는 무엇일까? 이미 `EdgeList`를 가지고 있는 정점에 참조를 통해 간선을 추가할 수 있기 때문이다.

&nbsp;
### 정점 추가

```swift
open override func createVertex(_ data: T) -> Vertex<T> {
  let matchingVertices = vertices.filter() { vertex in
    return vertex.data == data
  }

  // 1
  if matchingVertices.count > 0 {
    return matchingVertices.last!
  }

  // 2
  let vertex = Vertex(data: data, index: adjacencyList.count)
  adjacencyList.append(EdgeList(vertex: vertex))
  return vertex
}
```

- 1 : 이미 그래프에 정점이 존재하는지 확인한다.

- 2 : 존재하지 않는다면 추가한다.

&nbsp;
### 방향성이 있는 간선 추가

$<A, B>$와 같이 한 방향으로 갈 수 있는 간선을 추가한다.

```swift
open func addDirectedEdge(_ from: Vertex<T>, to: Vertex<T>, withWeight weight: Double?) {
  let edge = Edge(from: from, to: to, weight: weight)
  let edgeList = adjacencyList[from.index]
  if edgeList.edges != nil { // 인접한 정점이 있을 때
    edgeList.addEdge(edge)
  } else { // 인접한 정점이 없을 때
    edgeList.edges = [edge]
  }
}
```

&nbsp;
### 방향성이 없는 간선 추가

$(A, B)$와 같이 양 방향으로 갈 수 있는 간선을 추가한다.

```swift
open func addUndirectedEdge(_ vertices: (Vertex<T>, Vertex<T>), withWeight weight: Double?) {
  addDirectedEdge(vertices.0, to: vertices.1, withWeight: weight)
  addDirectedEdge(vertices.1, to: vertices.0, withWeight: weight)
}
```

&nbsp;
### 모든 정점

```swift
open var vertices: [Vertex<T>] {
  var vertices = [Vertex<T>]()
  for edgeList in adjacencyList {
    vertices.append(edgeList.vertex)
  }
  return vertices
}
```

&nbsp;
### 모든 간선

```swift
open var edges: [Edge<T>] {
  var allEdges = Set<Edge<T>>()
  for edgeList in adjacencyList {
    guard let edges = edgeList.edges else {
      continue
    }

    for edge in edges {
      allEdges.insert(edge)
    }
  }
  return Array(allEdges)
}
```

&nbsp;
### 두 정점의 인접 확인

두 정점이 인접해 있는지 확인하기 위해 인접 리스트의 모든 정점을 순회해야 한다. 정점의 수를 $V$라고 할 때 최악의 경우 모든 정점을 확인해야 하므로 시간 복잡도는 $O(V)$이다.

```swift
open func hasEdges(from: Vertex<T>, to: Vertex<T>) -> Bool {
  guard let edgeList = adjacencyList.first(where: { $0.vertex == from }) else { 
    return false
  }
  
  for edge in edgeList.edges {
    if edge.to == to {
      return true
    }
  }
  return false
}
```






