# Swift Concurrency

[https://github.com/apple/swift-evolution/blob/main/proposals/0296-async-await.md](https://github.com/apple/swift-evolution/blob/main/proposals/0296-async-await.md)

## 소개

WWDC 2021에서 동시성 프로그래밍과 관련 기능이 추가된 Swift 5.5가 발표됐다. 기존에 사용하던 `NSLock`, `Thread`, `GCD` 보다 더욱 편리한 기능들이 제공된다. 기존의 비동기 프로그래밍에서 수 많은 클로저와 완료 핸들러는 사용하기 까다롭다. 제어 흐름이 복잡해질수록 처리해야할 오류가 늘어나 실수를 야기할 수 있다. async, await 는 비동기 함수를 적절한 클로저 및 State machine으로 변환할 수 있다. 또한 동기 프로그래밍에서 사용하는 언어 구조를 비동기 프로그래밍에서 사용할 수 있어 마치 동기적으로 보여 가독성이 높아진다.

&nbsp;
## 기존의 문제점

완료 핸들러를 사용하는 비동기 프로그래밍은 아래와 같은 문제가 있다.

### Deeply-nested closures

일련의 간단한 비동기 작업에는 종종 깊고 중첩된 클로저가 필요하다.

```swift
func processImageData1(completionBlock: (_ result: Image) -> Void) {
    loadWebResource("dataprofile.txt") { dataResource in
        loadWebResource("imagedata.dat") { imageResource in
            decodeImage(dataResource, imageResource) { imageTmp in
                dewarpAndCleanupImage(imageTmp) { imageResult in
                    completionBlock(imageResult)
                }
            }
        }
    }
}
```

이는 코드가 실행되는 위치를 읽고 추적하는 것을 어렵게 만든다. 또한 각 단계에서 오류를 처리해야 한다면 두 번째 문제로 이어진다.


### Error handling

콜백은 오류 처리를 어렵고 장황하게 만든다. Swift 2에 도입된 동기 코드에 대한 오류 처리 모델의 이점은 콜백 기반 인터페이스에서 얻지 못한다.

```swift
func processImageData2a(completionBlock: (_ result: Image?, _ error: Error?) -> Void) {
    loadWebResource("dataprofile.txt") { dataResource, error in
        guard let dataResource = dataResource else {
            completionBlock(nil, error)
            return
        }
        loadWebResource("imagedata.dat") { imageResource, error in
            guard let imageResource = imageResource else {
                completionBlock(nil, error)
                return
            }
            decodeImage(dataResource, imageResource) { imageTmp, error in
                guard let imageTmp = imageTmp else {
                    completionBlock(nil, error)
                    return
                }
                dewarpAndCleanupImage(imageTmp) { imageResult, error in
                    guard let imageResult = imageResult else {
                        completionBlock(nil, error)
                        return
                    }
                    completionBlock(imageResult)
                }
            }
        }
    }
}
```

Swift 5에 소개된 `Result` 타입으로 오류 처리가 어느정도 향상되었지만, 여전히 클로저 중첩 문제는 남아 있다.

```swift
func processImageData2(completionBlock: (Result<Image, Error>) -> Void) {
    loadWebResource("dataprofile.txt") { dataResourceResult in
        switch dataResourceResult {
        case .success(let dataResource):
            loadWebResource("imagedata.dat") { imageResourceResult in
                switch imageResourceResult {
                case .success(let imageResource):
                    decodeImage(dataResource, imageResource) { imageTmpResult in
                        switch imageTmpResult {
                        case .success(let imageTmp):
                            dewarpAndCleanupImage(imageTmp) { imageResult in
                                completionBlock(imageResult)
                            }
                        case .failure(let error):
                            completionBlock(.failure(error))
                        }
                    }
                case .failure(let error):
                    completionBlock(.failure(error))
                }
            }
        case .failure(let error):
            completionBlock(.failure(error))
        }
    }
}
```

### Conditional execution

비동기 함수를 조건부로 실행하는 것은 매우 힘이든다. 예를 들어, 이미지를 얻은 후 Swizzle 해야한다고 가정한다. 그러나 때때로 Swizzle 하기 전에 이미지를 디코딩하기 위해 비동기 함수를 사용해야 한다. 이러한 동작을 구조화하는 가장 좋은 방법은 아래와 같이 완료 핸들러에서 조건부로 캡처되는 연속 클로저에 작성하는 것이다.

```swift
func processImageData3(recipient: Person, completionBlock: (_ result: Image) -> Void) {
    let swizzle: (_ contents: Image) -> Void = {
      // ... continuation closure that calls completionBlock eventually
    }
    if recipient.hasProfilePicture {
        swizzle(recipient.profilePicture)
    } else {
        decodeImage { image in
            swizzle(image)
        }
    }
}
```

이것은 함수의 자연스러운 하향식 구성을 반전시킨다. (하향식 구성이란, 후반부에서 실행될 코드는 전반부에서 실행되는 부분보다 앞에 나타나야 함을 뜻한다.) 조건부로 실행되는 비동기 함수의 수가 늘어남에 따라 문제는 더욱 악화되고, 결국은 1번 문제와 반대되는 **반전된 파멸적 피라미드**가 된다.

### Many mistakes are easy to make

완료 핸들러를 호출하지 않고 `return` 하는 등의 실수를 야기할 수 있고, 이는 디버깅이 어렵다.

```swift
func processImageData4a(completionBlock: (_ result: Image?, _ error: Error?) -> Void) {
    loadWebResource("dataprofile.txt") { dataResource, error in
        guard let dataResource = dataResource else {
            return // <- forgot to call the block
        }
        loadWebResource("imagedata.dat") { imageResource, error in
            guard let imageResource = imageResource else {
                return // <- forgot to call the block
            }
            ...
        }
    }
}
```

완료 핸들러를 호출한다 해도 `return`을 잊을 수 있다. 위와 같이 `guard` 문을 사용하면 컴파일러가 에러를 발견해서 `return`을 잊을리 없지만 항상 사용할 수는 없는 노릇이다.

```swift
func processImageData4b(recipient:Person, completionBlock: (_ result: Image?, _ error: Error?) -> Void) {
    if recipient.hasProfilePicture {
        if let image = recipient.profilePicture {
            completionBlock(image) // <- forgot to return after calling the block
        }
    }
    ...
}
```

&nbsp;
## 솔루션

중첩된 클로저 구조를 async / await를 통해 개선하면 아래와 같다.

```swift
func loadWebResource(_ path: String) async throws -> Resource
func decodeImage(_ r1: Resource, _ r2: Resource) async throws -> Image
func dewarpAndCleanupImage(_ i : Image) async throws -> Image

func processImageData() async throws -> Image {
  let dataResource  = try await loadWebResource("dataprofile.txt")
  let imageResource = try await loadWebResource("imagedata.dat")
  let imageTmp      = try await decodeImage(dataResource, imageResource)
  let imageResult   = try await dewarpAndCleanupImage(imageTmp)
  return imageResult
}
```

마치 비동기 함수가 동기 함수처럼 보인다. 동기 함수와의 유사성은 많은 이점을 얻을 수 있다.

- 코드를 디버깅, 프로파일링 및 탐색하는 동안 일관된 경험을 제공하기 위한 더 나은 도구
- 작업 우선 순위, 취소와 같은 동시성 기능을 위한 기반
- 비동기 코드의 성능 향상

`async`를 통해 비동기 함수로 선언하며, `await`를 통해 해당 함수를 호출하는 쪽에서 잠재적인 일시 중단 지점을 지정한다. 기존의 비동기 함수는 또 다른 비동기 함수를 호출할 수 있지만, 호출하는 동안 스레드를 포기할 수 없다. 그러므로 호출한 비동기 함수의 결과를 기다리지 않고 이어진 동작을 수행한다. `await`는 결과값을 기다리고 이어진 동작을 수행할 수 있도록 일시 중단 지점을 지정한다. '잠재적인' 일시 중단 지점이라 명시한 이유는 실제로 일시 중단되는지 여부를 정적으로 알 수 없기 때문이다.

### Async block

잠재적인 일시 중단 지점은 `async` 함수와 같은 비동기 컨텍스트 내에서 발생해야 한다. (Any potential suspension point must occur within an asynchronous context (e.g., an async function).) 만약 아래와 같은 `async` 함수를 `viewDidLoad` 내에서 호출하고자 할 때, 컴파일러는 에러를 표시한다. `viewDidLoad`와 같은 동기적인 컨텍스트에서 `async` 함수를 호출하고 싶다면 **Async block**를 사용한다. 

```swift
func loadWebResource(_ path: String) async -> Resource? {
		guard let url = URL(string: path), let data = try? Data(contentsOf: url) else { return nil }
		return Resource(data: data)
}
```

이를 Async block 내에 호출하면 **암시적인 비동기 컨텍스트(Implicit asynchronous context)**가 생성된다.

```swift
async {
		let resource = try await loadWebResource(_ path: "https://..")
}
```

&nbsp;
## `Task`

```swift
func fetchToDos() {
	self.todos = await fetchToDos(idx: idx)
}
```

위 `fetchTodos()` 함수와 같이 Swift Concurrency 환경이 아닌 동기 함수에서 `async` 함수를 호출하면 `‘async’ call in a function that does not support concurrency` 에러가 발생한다. 이를 위해 `fetchToDos() async` 로 바꾸면 되지만 `fetchToDos()`를 호출하는 또 다른 동기 함수가 있다면 귀찮아진다. 이때 `Task`를 사용한다.

```swift
func fetchToDos() {
	Task {
		self.todos = await fetchToDos(idx: idx)
	}
}
```

`Task`의 특징은 아래와 같다.
- 비동기 작업의 단위로 모든 `async` 함수는 `Task`의 한 부분으로 실행된다.
- 자식 `Task`를 트리 형태로 가지고 있어 작업의 취소, 에러의 전파가 용이하다.
- `Task` 인스턴스를 이용해 작업을 취소할 수 있다.
    
    ```swift
    let handler = Task { ... }
    handler.cancel()
    ```
    
- `Task`는 `suspended`, `running`, `completed` 세 가지 상태를 갖는다.

&nbsp;
## AsyncStream

`AsyncSequence`는 각 element를 비동기로 받을 수 있으며 기존의 `[Sequence](https://developer.apple.com/documentation/swift/sequence)`처럼 `map`, `reduce`, `contains` 같은 기존의 메서드를 사용할 수 있다.

`AsyncStream`은 `AsyncSequence` 타입을 구현하지 않고 기존에 있던 코드를 `AsyncSequence`로 바꿀 때 사용한다.

```swift
let digits = AsyncStream<Int> { continuation in 
	for digit in 1...10 {
		continuation.yield(digit)
	}
	continuation.finish()
}

for await digit in digits {
	...
}
```

`AsyncSequence`로 바꾸는 과정에서 에러가 발생할 수 있다면 `AsyncThrowingStream`를 사용한다. 방법은 `AsyncStream`과 동일하며 호출하는 곳에서 예외 처리(do-try-catch 등)를 한다. 또한 `onTermiation`를 통해 스트림이 종료되거나 취소될 때 콜백을 설정할 수 있다.

```swift
let digits = AsyncStream(Int.self) { continuation in 
	continuation.onTermiation = { termination in 
		switch termination {
		case .finished: print("finished")
		case .cancelled: print("cancelled")
		}
	}

	for digit in 1...100 {
		print(digit)
		continuation.yield(digit)
	}

	print("finished before")
	continuation.finish()
	print("finished after")
}
```

```
1
2
...
100
finished before
finished
finished after
```

&nbsp;
## TaskGroup
