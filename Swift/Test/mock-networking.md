# Mock Networking

좋은 테스트는 **'특정 환경에 종속되지 않아야 한다'** 라는 조건이 있다. 이는 서버의 부하, 기기의 인터넷 연결 상태 등 외부적인 요인에 따라 테스트의 결과가 바뀌지 않아야 하는 것을 의미한다. 만약 네트워킹 통신의 응답 데이터를 우리가 원하는 모델로 디코딩이 문제없이 되는지 테스트를 하려면 어떻게 해야될까?

실제로 네트워킹 요청을 보낸다면 시스템 환경에 따라 응답이 느리게 올 수 있고, 심지어 네트워킹 통신이 실패할 수도 있다. 또한 서버의 데이터베이스가 변경되어서 우리가 원하는 데이터가 오지 않을 수 있다. 우리는 서버가 응답하는 데이터에 어떠한 값이 담아져 있는지 테스트하는 것이 아닌, A라는 응답이 올 때 클라이언트에서 의도한대로 잘 처리가 되는지 테스트를 해야한다.

이때 우리가 원하는 응답을 하도록 가짜 세션을 구현하는 방법이 있다. 우선 네트워킹 통신을 할 때 `URLSession`을 사용한다고 가정하고, `URLSession`의 인스턴스 메소드인 `dataTask(with:completionHandler)`가 우리가 원하는 응답을 하도록 구성하기 위해 새로운 프로토콜을 정의한다.

### URLSessionProtocol

```swift
protocol URLSessionProtocol {
  func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
}
```

테스트 환경에서는 가짜 세션을 사용하고, 실제로 네트워킹 통신을 할 때 `URLSession`을 사용한다. 따라서 `URLSession`도 마찬가지로 프로토콜을 채택해야 한다. 이미 Foundation 프레임워크 내에서 `URLSession`의 `dataTask(with:completionHandler)`가 구현되어 있으니 프로토콜을 채택하기만 하면 된다.

```swift
extension URLSession: URLSessionProtocol { }
```

&nbsp;
### MockURLSessionDataTask

`dataTask(with:completionHandler)`가 동작하는 순서는 `url`을 파라미터로 받아 `URLSessionDataTask` 객체를 생성한 뒤 `URLSessionDataTask` 객체에 `resume()` 함수를 호출하여 네트워크 통신을 수행한다. 하지만 가짜 세션에서 실제로 네트워킹 통신이 이루어지면 안되므로, `resume()` 함수를 오버라이드할 `URLSessionDataTask` 객체를 정의해야 한다.

```swift
class MockURLSessionDataTask: URLSessionDataTask {
  private let resumeHandler: () -> Void

  init(resumeHandler: () -> Void) {
    self.resumeHandler = resumeHandler
  }

  override func resume() {
    resumeHandler()
  }
}
```

이제 네트워킹 통신을 수행하는 `NetWorking` 클래스를 살펴보자.

```swift
final class Networking {
  let session: URLSessionProtocol

  init(session: URLSessionProtocol = URLSession.shared) { // 1
    self.session = session
  }

  func fetch<T: Decodable>(url: URL, type: T.Type, completion: @escaping ((Result<T, Error>) -> Void)) {
    let dataTask = session.dataTask(with: url, completionHandler: { data, response, error in 
      if let error = error {
        completion(.failure(error))
        return
      }

      if let data = data, let response = response as? HTTPURLResponse {
        do {
          let decoded = try JSONDecoder().decode(T.self, from: data)
          completion(.success(decoded))
        } catch {
          completion(.failure(error))
        }
      }
    })
    dataTask.resume() // 2
  }
}
```

1. 초기화 시 `URLSessionProtocol` 타입을 주입받고 실제로 네트워킹 통신을 할 때는 `URLSession.shared` 세션을 사용한다. 이로써 테스트를 할 때 가짜 세션을 주입할 수 있도록 테스트 환경이 갖춰졌다. 

2. `MockURLSessionDataTask`를 통해 우리가 원하는 응답을 받을 수 있다.

&nbsp;
### Mock 데이터

가짜 세션에 제공할 데이터가 필요하다. 네트워크 응답 형태를 미리 JSON 파일로 작성하여 네트워크 요청에 대한 응답을 받은 것 처럼 위장한다. 프로젝트에 네트워크 응답 형태를 JSON 파일로 작성하여 추가한다.

```swift
{
  [
    {"name" : "Dancing Queen", "artist" : "ABBA"},
    {"name" : "Mamma Mia", "artist" : "ABBA"},
    {"name" : "Take a Change On Me", "artist" : "ABBA"}
  ]
}
```

&nbsp;
### Mock 세션

초기화 시 응답과 데이터를 미리 설정하여 상호작용을 위장하기 위한 세션이다.

```swift
class MockURLSession: URLSessionProtocol {
  typealias Response = (data: Data?, urlResponse: URLResponse?, error: Error?)

  let response: Response
  init(response: Response) {
    self.response = response
  }

  func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask {
    return MockURLSessionDataTask(resumeHandler: {
      completionHandler(self.response.data, self.response.urlResponse, self.response.error)
    })
  }
}
```

&nbsp;
### 테스트를 위한 Mock 세션 설정

```swift
// 1
let bundle = Bundle(for: type(of: self))
let path = bundle.path(forResource: "mockData", ofType: "json")
let data = try? Data(contentsOf: URL(fileURLWithPath: path!), options: .alwaysMapped)

// 2
let url = URL(string: "url")
let urlResponse = HTTPURLResponse(url: url!, statusCode: 200, httpVersion: nil, headerFields: nil)
let mockResponse: MockURLSession.Response = {
  return (data: data, urlResponse: urlResponse, error: nil)
}

// 3
let sut = MockURLSession(response: mockResponse)
```

- 1 : [Mock 데이터](#mock-데이터)에서 생성한 가짜 데이터를 가져온다.
- 2 : 테스트 시 원하는 네트워킹 응답을 정의한다.
- 3 : 가짜 세션을 생성한다.

&nbsp;
### 테스트

```swift
func test() {
  var result: [Song] = []
  sut.fetch(url: url, type: [Song].self) { result in 
    if case let .success(data) = result {
      result = data
    }
  }
  
  XCTAssertEqual(result.count, 3)
  XCTAssertEqual(result.first?.name, "Dancing Queen")
}
```