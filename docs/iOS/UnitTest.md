# Mock을 활용한 Unit Test
- 먼저 테스트 대상은 외부 환경에 의존하면 안된다. 그 이유는 실제로 api를 호출 하면 느리고 네트워크 환경에 영향을 많이 받아 통신이 실패할 가능성도 있기 때문이다. 또한 DB의 데이터가 변경되어 테스트가 실패할 수도 있다. 그리고 요청이 post 요청이라면 테스트 코드가 실제 서버의 프로덕션 환경을 변경시킬 수 있기 때문에 위험하기 때문이다.
 - 그 외에도 status code가 200이 아닌 에러 코드가 내려왓을 때 제대로 예외 처리를 확인하거나 api 호출로 받아온 데이터를 가지고 제대로 처리가 되고 있는지 테스트를 하기 위해서 외부 환경에 의존하지 않고 테스트를 진행해야 한다.
 
 ## Mock 객체 만들기
 - 네트워크 환경을 테스트 하기위해 URLSession을 주입받는 NetworkManager를 만들어 테스트 할 경우에 MockURLSession을 만들어 주입 후 테스트를 진행한다. 이후 테스트 시에는 Json파일이나 코드로 정의해둔 Mock 데이터를 받아서 테스트를 진행해야 한다.

```Swift
// Protocol for MOCK/Real
// URLSession의 instance method인 dataTask(with:completionHandler:)랑 동일한 시그니처를 정의해둔 protocol
protocol URLSessionProtocol {
    func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTaskProtocol
}

final class NetworkManager {
    let session: URLSessionProtocol
    
    // URLSession 을 주입 받음. 테스트할 때는 MockURLSession 을 주입, 실제 프로덕션 코드에서는 다른 URLSession을 주입 받아야 한다.
    init(session: URLSessionProtocol = URLSession.shared) {
        self.session = session
    }
    
    func fetchData<T: Decodable>(for url: String,
                                 dataType: T.Type,
                                 completion: @escaping (Result<T, Error>) -> Void) {
        
        guard let url = URL(string: url) else {
            return
        }
        
        let dataTask: URLSessionDataTaskProtocol = session.dataTask(with: url, completionHandler: { (data, response, error) in
            if let error = error {
                completion(.failure(error))
                return
            }
            
            if let data = data,
               let response = response as? HTTPURLResponse,
               200..<400 ~= response.statusCode {
                do {
                    let data = try JSONDecoder().decode(T.self, from: data)
                    completion(.success(data))
                } catch {
                    completion(.failure(NetworkError.failToParse))
                }
            } else {
                completion(.failure(NetworkError.invalid))
            }
        })
        dataTask.resume()
    }
}

class MockURLSessionDataTask: URLSessionDataTaskProtocol {
    
    private let resumeHandler: () -> Void
    
    init(resumeHandler: @escaping () -> Void) {
        self.resumeHandler = resumeHandler
    }
    
    // resume 해도 실제 네트워크 요청들이 일어나면 안됨. 그냥 단순히 completionHandler 호출 용
    func resume() {
        resumeHandler()
    }
}

// MARK: Conform the protocol
// 테스트를 위한 MockURLSession
class MockURLSession: URLSessionProtocol {

    typealias Response = (data: Data?, urlResponse: URLResponse?, error: Error?)
    
    let response: Response
    
    init(response: Response) {
        self.response = response
    }
    
    func dataTask(
        with url: URL,
        completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void
    ) -> URLSessionDataTaskProtocol {
        return MockURLSessionDataTask(resumeHandler: {
            completionHandler(self.response.data,
                              self.response.urlResponse,
                              self.response.error)
        })
    }
    
    static func make(url: String, data: Data?, statusCode: Int) -> MockURLSession {
        let mockURLSession: MockURLSession = {
            let urlResponse = HTTPURLResponse(url: URL(string: url)!,
                                           statusCode: statusCode,
                                           httpVersion: nil,
                                           headerFields: nil)
            let mockResponse: MockURLSession.Response = (data: data,
                                                         urlResponse: urlResponse,
                                                         error: nil)
            let mockUrlSession = MockURLSession(response: mockResponse)
            return mockUrlSession
        }()
        return mockURLSession
    }
}
```

- 실제 테스트 코드 작성

```Swift
import XCTest
@testable import NetworkMock

class NetworkManagerTests: XCTestCase {
    
    var url: String!
    var data: Data!
    
    override func setUpWithError() throws {
        url = "https://api.sampleapis.com/coffee/hot"
        data = JsonLoader.data(fileName: "Coffees")
    }
    
    override func tearDownWithError() throws {
        url = nil
        data = nil
    }
    
    func test_fetchData_Data가_있고_statusCode가_200일때() {
        // given
        let mockURLSession = MockURLSession.make(url: url,
                                                 data: data,
                                                 statusCode: 200)
        let sut = NetworkManager(session: mockURLSession)
        
        // when
        var result: [Coffee]?
        sut.fetchData(for: url,
                      dataType: [Coffee].self) { response in
            if case let .success(coffees) = response {
                result = coffees
            }
        }
        
        // then
        let expectation: [Coffee]? = JsonLoader.load(type: [Coffee].self, fileName: "Coffees")
        XCTAssertEqual(result?.count, expectation?.count)
        XCTAssertEqual(result?.first?.title, expectation?.first?.title)
    }
    
    func test_fetchData_Data에_대한_잘못된_dataType을_넘겼을때() {
        // given
        let mockURLSession = MockURLSession.make(url: url,
                                                 data: data,
                                                 statusCode: 200)
        let sut = NetworkManager(session: mockURLSession)
        
        
        // when
        var result: NetworkError?
        sut.fetchData(for: url,
                      dataType: Coffee.self) { response in
            if case let .failure(error) = response {
                result = error as? NetworkError
            }
        }
        
        // then
        let expectation: NetworkError = NetworkError.failToParse
        XCTAssertEqual(result, expectation)
    }
    
    func test_fetchData_Data가_없고_statusCode가_500일때() {
        // given
        let mockURLSession = MockURLSession.make(url: url,
                                                 data: nil,
                                                 statusCode: 500)
        let sut = NetworkManager(session: mockURLSession)
        
        // when
        var result: Error?
        sut.fetchData(for: url,
                      dataType: [Coffee].self) { response in
            if case let .failure(error) = response {
                result = error
            }
        }
        
        // then
        XCTAssertNotNil(result)
    }
}
```

## 참고
- [Mock 을 이용한 Network Unit Test 하기](https://sujinnaljin.medium.com/swift-mock-%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-network-unit-test-%ED%95%98%EA%B8%B0-a69570defb41)
