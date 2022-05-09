# Mock Networking

네트워크 요청과 같은 비동기 작업은 아래와 같이 테스트한다.

&nbsp;
## 실제 네트워킹 요청

실제 네트워킹 요청을 보내서 받은 응답으로 테스트를 한다. 하지만 실제로 네트워킹 요청을 보낸다면 기기의 인터넷 연결, 서버의 부하 상태에 따라 응답이 느리게 오거나 요청이 실패할 수 있기 때문에 좋은 테스트의 조건을 만족하지 못한다. 우선 실제로 네트워킹 요청을 보내는 테스트 방식을 살펴보자.

### 세션

```swift
var sessionUnderTest: URLSession!

override func setUpWithError() throws {
	sessionUnderTest = URLSession(configuration: URLSessionConfiguration.default)
}

override func tearDownWithError() throws {
	sessionUnderTest = nil
}

func testGetsHTTPStatusCode200() {
	// given
	let url = URL(string: "url")

	let promise = expectation(description: "description")

	// when
	let dataTask = sessionUnderTest.dataTask(with: url!) { data, response, error in

	// then
	if let error = error {
		XCTFail("실패 메시지")
	} else if (response as? HTTPURLResponse)?.statusCode == 200 {
		promise.**fullfill**()
	}

	dataTask.resume()
	waitForExpectation(timeout: 5, handler: nil)
}
```

API의 응답 데이터를 확인하는 테스트가 아닌 이상 매번 네트워킹 요청을 보낼 필요가 없다. 또한 테스트는 빠르게 이루어져야 하고, 항상 동일한 결과를 얻어야 한다.

&nbsp;
## Mock 네트워크 요청

실제 네트워킹 요청을 보내는 대신 우리가 원하는 응답을 하는 세션을 구현한다.

### Mock 데이터

가짜 세션에 제공할 데이터가 필요하다. 네트워크 응답 형태를 미리 JSON 파일로 작성하여 네트워크 요청에 대한 응답을 받은 것 처럼 위장한다. 프로젝트에 네트워크 응답 형태를 JSON 파일로 작성하여 추가한다.

```swift
{
	"resultCount" : 3,
	"results" : [
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
import Foundation

public protocol MockURLSession {
  func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
  func dataTask(with request: URLRequest, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
}

extension URLSession: MockURLSession { }

public final class URLSessionMock: MockURLSession {
	private let dataTaskMock: URLSessionDataTaskMock
  	var request: URLRequest?
  	var url: URL?
  
  	public convenience init?(jsonDict: [String: Any], response: URLResponse? = nil, error: Error? = nil) {
    	guard let data = try? JSONSerialization.data(withJSONObject: jsonDict, options: []) else { return nil }
    	self.init(data: data, response: response, error: error)
  	}
  
  	public init(data: Data? = nil, response: URLResponse? = nil, error: Error? = nil) {
    	dataTaskMock = URLSessionDataTaskMock()
    	dataTaskMock.taskResponse = (data, response, error)
 	 }
  
 	 public func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask {
     	self.url = url
     	self.dataTaskMock.completionHandler = completionHandler
     	return self.dataTaskMock
 	 }
  
  	public func dataTask(with request: URLRequest, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask {
      	self.request = request
      	self.dataTaskMock.completionHandler = completionHandler
     	return self.dataTaskMock
 	 }
  
  	final private class URLSessionDataTaskMock: URLSessionDataTask {
    
    	typealias CompletionHandler = (Data?, URLResponse?, Error?) -> Void
    	var completionHandler: CompletionHandler?
    	var taskResponse: (Data?, URLResponse?, Error?)?
   	
    	override func resume() {
      		DispatchQueue.main.async {
        		self.completionHandler?(self.taskResponse?.0, self.taskResponse?.1, self.taskResponse?.2)
      		}
    	}
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
let response = HTTPURLResponse(url: url!, statusCode: 200, httpVersion: nil, headerFields: nil)

// 3
let mockSession = URLSessionMock(data: data, response: response, error: nil)
```

- 1 : [Mock 데이터](#mock-데이터)에서 생성한 Mock 데이터를 가져온다.
- 2 : 테스트 시 원하는 네트워킹 응답을 정의한다.
- 3 : Mock 세션을 생성한다.

&nbsp;
### 테스트

만약 뷰 컨트롤러가 소유한 `URLSession`을 통해 네트워크 요청을 해서 응답 데이터를 받아 업데이트 했을 때, 가짜 응답과 데이터를 통해 업데이트가 정상적으로 되는지 테스트한다.

```swift
func test_UpdateSongs_ParsesData() {
	// given
	let promise = expectation(description: "description")
	controller.defaultSession = mockSession

	// when
	let url = URL(string: "url")
	let dataTask = controller.defaultSession.dataTask(with: url!) { data, response, error in
		if (response as? HTTPURLResponse)?.statusCode == 200 {
			promise.fulfill()
			self.controller.update(data)
		}
	}

	dataTask.resume()
	waitForExpectations(timeout: 5, handler: nil)

	// then
	XCTAssertEqual(controller.results.count, 3, "테스트 성공 메시지")
}
```