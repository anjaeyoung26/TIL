# Hash Table

배열에 Key-Value 쌍을 저장하고 Key로 배열의 인덱스를 계산해 빠르게 검색할 수 있는 자료구조이다.

## 용어

1. 버킷(Bucket) 또는 슬롯(Slot) : Key-Value 데이터가 저장되는 곳, 즉 배열의 원소(Element)를 뜻한다.

&nbsp;
## 시간 복잡도

|  | Average | Worst |
| --- | :---: | :---: |
| Space | <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(n)"> | <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(n)"> |
| Search | <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(1)"> | <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(n)"> |
| Insert | <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(1)"> | <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(1)"> |
| Delete | <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(1)"> | <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(n)"> |

Worst : 충돌이 발생한 경우, Chaining된 배열이나 연결 리스트를 검색해야 한다.

&nbsp;
## Hashing

![hashing](https://user-images.githubusercontent.com/61190690/167288331-8ba972a9-e3cc-4a69-93c9-19f17e5bbd0b.png)

대부분의 자료구조 에서는 탐색 키를 저장된 키 값과 반복적으로 비교하면서 원하는 요소에 접근한다. 이는 정렬이 되어 있다면 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(log_2n)">, 정렬이 되어 있지 않다면 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(n)">의 시간 복잡도를 갖는다. 반면 해싱은 탐색 키에 산술적인 연산을 적용해 요소가 저장되어 있는 테이블의 주소로 변환되어 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(1)"> 시간 안에 빠르게 접근할 수 있다.

&nbsp;
## Hashing Function

<img src="https://user-images.githubusercontent.com/61190690/167288354-ecc41ebd-50e7-41b9-bcdb-351d1f5cc76a.png" width="500">

해싱 함수는 키 값을 테이블의 주소로 변환하는 함수이다. 해시 알고리즘은 다양한 종류가 있으며, 아래는 문자열을 해싱 하는 `djb2` 알고리즘이다.

```swift
func djb2Hash(_ string: String) -> Int {
  let unicodeScalars = string.unicodeScalars.map { $0.value }
  return unicodeScalars.reduce(5381) {
    ($0 << 5) &+ $0 &+ Int($1)
  }
}

djb2Hash("abc") // outputs 193485963
djb2Hash("bca") // outputs 193487083
```

&nbsp;
## Keeping Arrays Small

<img src="https://user-images.githubusercontent.com/61190690/167288396-7eee355d-b700-415d-8c33-f879935a965a.png" width="500">

하지만 ‘-8378883973431208045’와 같이 큰 값의 정수를 배열의 인덱스로 사용하기에 부담이 된다. 일반적으로 해시가 음수라면 양수로 만든 후 배열의 크기만큼 나눈 나머지를 인덱스로 사용한다. 아래는 `FIRSTNAME`의 인덱스를 계산한다.

```swift
abs(-8378883973431208045) % 3 = 1
```

&nbsp;
## Avoiding Collisions

견고하지 못한 해시 알고리즘을 사용하는 경우 동일한 인덱스가 계산될 수 있다.

```swift
djb2Hash("firstName") % 2 // outputs 1
djb2Hash("lastName") % 2 // outputs 1
```

충돌을 피하기 위해 두 개의 키가 동일한 인덱스에 매핑될 가능성이 적도록 큰 배열을 사용하는 것과 배열의 크기에 소수를 사용한다. 이는 미봉책일 뿐, **결국 해시 테이블의 구조 혹은 해싱 함수를 개선해야 한다.**

&nbsp;
### Open Address

충돌이 발생하면 다른 버킷에 저장한다. 여기서 다른 버킷을 찾기 위한 방법은 세 가지이다.

1. Linear Probbing(선형 탐사) : 순차적으로 탐색한다.

2. Quadratic Probbing(제곱 탐사) : 2차 함수를 이용해 탐색한다.

3. Double Hashing Probbing(이중 해싱 탐사) : 2차 해시 함수를 이용해 새로운 주소를 할당한다. 위 두 가지 방법에 비해 많은 연산량을 요구한다.

> Linear∙Quadratic Probbing는 인접한 위치에 데이터를 계속해서 저장하기 때문에 데이터가 밀집되는 클러스터링(Clustering)이 발생할 수 있다.

&nbsp;
### Separate Chaining

<img src="https://user-images.githubusercontent.com/61190690/167288501-9cabfee9-5716-4437-a7ac-864169a03a30.png" width="600">

1. 각각의 버킷을 연결 리스트로 만들어 동일한 인덱스로 매핑된 값을 리스트로 연결한다. 이는 연결 리스트의 특징을 이어받아 삽입∙삭제는 간단하지만 작은 데이터를 저장하기에 오버헤드가 발생한다.

2. 1번 방법에서 연결 리스트 대신 트리를 사용한다.

> 선택하는 기준은 Key-Value 쌍의 갯수이다. 트리는 기본적으로 메모리를 많이 사용하지만 데이터의 갯수가 적을 때 연결 리스트와 트리의 성능 상 차이가 거의 없어서 데이터의 갯수가 적다면 연결 리스트, 많다면 트리를 사용한다.

&nbsp;
### Open Address vs Separate Chaining

`Open Address`는 연속된 공간에 데이터를 저장하기 때문에 캐시의 효율이 좋지만 버킷의 사이즈가 커질수록 캐시의 적중률이 낮아진다.(캐시의 적중률 = 접근 횟수 / 총 접근 횟수, [참고](https://ndlessrain.tistory.com/entry/%EC%BA%90%EC%8B%9C%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%81%EC%A4%91%EB%A5%A0Hit-Rate)) 또한 `Separate Chaining`은 버킷 외에도 연결 리스트 혹은 트리를 사용하기 때문에 Open Address와 비교하면 더 많은 메모리가 필요하다. 

> 따라서 데이터의 갯수가 적을 때는 Open Address, 많을 때는 Separate Chaining을 사용한다.

&nbsp;
### Dynamic Resizing

버킷의 갯수가 적다면 메모리를 절약할 수 있지만 충돌 가능성이 높아져서 성능이 안좋아진다. 그래서 데이터의 갯수가 버킷의 갯수의 **75%**가 될 때 버킷의 갯수를 두 배로 늘려서 충돌로 인한 성능의 손실을 어느 정도 해결할 수 있다. (0.75라는 숫자는 Load Factor로 불린다.)

&nbsp;
## 구현

```swift
public struct HashTable<Key: Hashable, Value> {
	typealias Element = (key: Key, value: Value)
	typealias Bucket = [Element]
	private var buckets = [Bucket] // Chaining에 연결 리스트 대신 배열을 사용한다.

	public private(set) var count = 0
	public var isEmpty: Bool { return count == 0 }

	// 고정된 크기로 초기화
	public init(capacity: Int) {
		assert(capacity > 0)
		buckets = Array<Bucket>(repeatingElement([], count: capacity))
	}
}
```

&nbsp;
### subscript

```swift
public subscript(key: Key) -> Value? {
	get {
		return value(forKey: key)
	}
	set {
		if let value = newValue {
			updateValue(value, forKey: key)
		} else {
			removeValue(forKey: key)
		}
	}
}
```

`subscript`를 구현 함으로써 아래와 같이 사용할 수 있다.

```swift
/// remove 키 값을 가진 데이터 제거
hashTable["remove"] = nil

// new 키 값을 가진 데이터 삽입
hashTable["new"] = "new value"

// old 키 값을 가진 데이터 탐색
let oldValue = hashTable["old"]
```

&nbsp;
### Hashing Function

매개변수 `key` 값을 테이블의 주소로 변환한다. [Keeping Arrays Small](https://www.notion.so/Hash-Table-5ce6dc18563040a9a7f5ce6a4fdf7a67)를 하기 위해 해시 값을 적절히 변환한다.

```swift
private func index(forKey key: Key) -> Int {
	return abs(key.hashValue % buckets.count)
}
```

&nbsp;
### 조회

```swift
public func value(forKey: Key) -> Value?
	let index = self.index(forKey: key)

	// 버킷에 있는 Chaining 배열에서 탐색
	for element in buckets[index] {
		if element.key == key {
			return element.value
		}
	}

	return nil
}
```

&nbsp;
### 삽입

```swift
@discardableResult 
public mutating func updateValue(_ value: Value, forKey: Key) -> Value? {
	let index = self.index(forKey: key)

	for (i, element) in buckets[index].enumerated() {
		if element.key == key {
			let oldValue = element.value
			buckets[index][i].value = value
			return oldValue
		}
	}

	buckets[index].append((key: Key, value: value))
	count += 1
	return nil
}
	
```

먼저 해시 테이블에 데이터가 존재하는지 조회한다. 만약 데이터가 있다면 값을 갱신하고, 없다면 버킷에 데이터를 삽입한다.

&nbsp;
### 삭제

```swift
@discardableResult
public mutating func removeValue(_ value: Value, forKey: Key) -> Value? {
	let index = self.index(forKey: key)
	
	for (i, element) in buckets[index].enumerated() {
		if element.key == key {
			let removeValue = element.value
			buckets[index][i] = nil
			count -= 1
			return removeValue
		}
	}

	return nil
}

public mutating func removeAll() {
	buckets = Array<Bucket>(repeatingElements([], count: buckets.count))
	count = 0
}
			
```

먼저 해시 테이블에 데이터가 존재하는지 조회한다. 만약 데이터가 있다면 제거한다.

&nbsp;
### Debug

```swift
extension HashTable: CustomStringConvertible {
    public var description: String {
        let pairs = buckets.flatMap { b in b.map { e in "\(e.key) = \(e.value)" } }
        return pairs.joined(separator: ", ")
    }
    
    public var debugDescription: String {
        var str = ""
        for (i, bucket) in buckets.enumerated() {
            let pairs = bucket.map { e in "\(e.key) = \(e.value)" }
            str += "bucket \(i): " + pairs.joined(separator: ", ") + "\n"
        }
        return str
    }
}
```