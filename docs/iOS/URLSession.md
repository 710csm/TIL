# iOS에서의 통신
- iOS에서는 서버와 통신하기 위해 URLSession을 제공하고 있다. Alamofire와 같은 유명한 라이브러리의 기반이 되는것이 URLSession이다. URLSession은 HTTP를 포함한 몇가지 프로토콜을 지원하고, 인증, 쿠키 관리, 캐시 등을 지원한다.

## URLSession의 Request와 Response
- URLSession은 다른 HTTP 통신과 마찬가지로 Request와 Response를 기본 구조로 가지고 있다. Request는 URL 객체를 통해 직접 통신하는 형태와 URLRequest객체를 만들어서 옵션을 설정하고 통신하는 형태가 있다. Response는 설정된 Task의 Completion Handler 형태로 response를 받거나 URLSessionDelegate를 통해 지정된 메소드를 호출하는 형태로 받는 형태가 있다.

- 일반적으로 response를 받을 때는 Completion Handler의 형태를 사용하지만 백그라운드 상태로 들어갈 때에도 다운로드를 지원하도록 하거나, 인증과 캐싱을 default옵션으로 사용하지 않는 상황과 같은 경우에는 delegate 패턴을 사용한다.

## URLSession의 기본 컨셉
- URLSession은 다음과 같은 순서로 진행이된다.
1. Session configuration을 결정하고, Session을 생성한다.
2. 통신할 URL과 Request 객체를 설정한다.
3. 사용할 Task를 결정하고, 그에 맞는 Completion Handler나 Delegate 메소드들을 작성한다.
4. 해당 Task를 실행한다.
5. Task 완료 후 Completion Handler가 실행된다.

## 설정
- iOS는 SSL 인증이 되지 않는 사이트에 대해 기본적으로 막고있기 때문에 Info.plist에 들어가서 설정을 해야한다.
- Information Property List -> App Transport Security Settings -> Exception Domains -> localhost -> NSTemporaryExceptionAllowsInsecureHTTPLoads를 Yes로 변경
- 이 설정은 현재 서버가 localhost일 경우 설정하고 API Server가 따로 구현되어 있다면 해당 Server를 추가한다.
- 실제 배포할 때는 이 설정을 No로 설정해야한다.

### 1. Session
- URLSession은 크게 3가지 종류의 Session을 지원한다.

1. Default Session: 기본적인 Session으로 디스크 기반 캐싱을 지원합니다.
2. Ephemeral Session: 어떠한 데이터도 저장하지 않는 형태의 세션입니다.
3. Background Session: 앱이 종료된 이후에도 통신이 이뤄지는 것을 지원하는 세션입니다.

### 2. Request
URLRequest를 통해서는 서버로 요청을 보낼 때 어떻게 데이터를 캐싱할 것인지, 어떤 HTTP 메소드를 사용할 것인지(Get, Post 등), 어떤 내용을 전송할 것인지 등을 설정할 수 있습니다.

### 3. Task
Task 객체는 일반적으로 Session 객체가 서버로 요청을 보낸 후, 응답을 받을 때 URL 기반의 내용들을 받는(retrieve) 역할을 합니다. 3가지 종류의 Task가 지원됩니다.

1. Data Task: Data 객체를 통해 데이터 주고받는 Task입니다.
2. Download Task: data를 파일의 형태로 전환 후 다운 받는 Task입니다. 백그라운드 다운로드 지원
3. Upload Task: data를 파일의 형태로 전환 후 업로드하는 Task입니다.

## URLSession dataTask 사용
- URLSession과 같은 네트워킹용 API는 일반적으로 앱 전역에서 사용된다. 그래서 ViewController에 작성하기보다는 하나의 모듈(class)을 만들고 static 함수를 만들어 사용하는 것이 좋다.

**1. 일반적인 사용**

```Swift
class NetworkHandler {
    class func getData(resource: String) {
        let defaultSession = URLSession(configuration: .default)
    
        guard let url = URL(string: "\(resource)") else {
            print("URL is nil")
            return
        }
        
        let request = URLRequest(url: url)
        
        let dataTask = defaultSession.dataTast(
            with: request
        ) { (data: Data?, response: URLResponse?, error: Error?) in
            guard error = nil else {
                print("Error occur: \(String(describing: error))")
                return
            }
            
            guard let data = data, let response = response as? HTTPURLResponse, response.statusCode == 200 else {
                return
            }
            
            // 통신에 성공한 경우 data에 Data 객체가 전달됩니다.
            // 받아오는 데이터가 json 형태일 경우 json을 serialize하여 json 데이터를 swift 데이터 타입으로 변환한다.
            // json serialize란 json 데이터를 String 형태로 변환하여 Swift에서 사용할 수 있도록 하는것을 말한다.
            
            guard let jsonToArray = try? JSONSerialization.jsonObject(with: data, options: []) else {
                print("json to Any Error")
                return
            }
            // 원하는 작업
            print(jsonToArray)
        }
        dataTask.resume()
    }
}

NetworkHandler.getData(resource: "http://www.example.com")
```

**2. GET 메소드 사용**

```Swift
struct Response: Codable {
    let success: Bool
    let result: String
    let message: String
}

class func requestGet(url: String, completionHandler: @escaping (Bool, Any) -> Void) {
    guard let url = URL(string: url) else {
        print("Error: cannot create URL")
        return
    }
    
    var request = URLRequest(url: url)
    request.httpMethod = "GET"
    
    URLSession.shared.dataTask(with: request) { data, response, error in
        guard error = nil else {
            print(error)
            return
        }
        
        guard let data = data else {
            print("Error: Did not receive data")
            return
        }
        
        guard let response response as? HTTPURLResponse, (200 ..< 300) ~= response.statusCode else {
            print("Error: HTTP request failed")
            return
        }
        
        guard let output = try? JSONDecoder().decode(Response.self, from: data) else {
            print("Error: JSON Data Parsing failed")
            return
        }
        
        completionHandler(true, output.result)
    }.resume()
}
```

**3. 그 외 메소드 사용**

```Swift
struct Response: Codable {
    let success: Bool
    let result: String
    let message: String
}

class func requestPost(url: String, method: String, param: [String: Any], completionHandler: @escaping (Bool, Any) -> Void) {
    let sendData = try! JSONSerialization.data(withJSONObject: param, options: [])
    
    guard let url = URL(string: url) else {
        print("Error: cannot create URL")
        return 
    }
    
    var request = URLRequest(url: url)
    request.httpMethod = method
    request.addValue("application/json", forHTTPHeaderField: "Content-Type")
    request.httpBody = sendData
    
    URLSession.shared.dataTask(with: request) { data, response, error in    
        guard error == nil else {
            print("Error: errror calling GET")
            print(error)
            return
        }
        
        guard let data = data else {
            print("Error: Did not receive data")
            return
        }
        
        guard let response = response as? HTTPURLResponse, (200 ..< 300) ~= response.statusCode else {
            print("Error: HTTP request faild")
            return
        }
        
        guard let output = try? JSONDecocder().decode(Response.self, from: data) else {
            print("Error: JSON data Parsing failed")
            return
        }
        
        completionHandler(true, output.result)
    }.resume()
}
```

## Alamofire와 비교
- Alamofire가 더 직관적이고 간단하다는것을 확인할 수 있다.

**1. GET 메소드 사용**

```Swift
AF.request("http://localhost:5000/test/get").responseJSON() { response in
    switch response.result {
    case .success:
        if let data = try! response.result.get() as? [String: Any] {
            print(data)
        }
    case .failure(let error):
        print("Error: \(error)")
        return
    }
}
```

**2. 그 외 메소드 사용**

```Swift
AF.request(
    "http://localhost:5000/test/post", 
    method: .post, 
    parameters: ["key": "Hello!"], 
    encoding: URLEncoding.httpBody
).responseJSON() { response in
    switch response.result {
    case .success:
        if let data = try! response.result.get() as? [String: Any] {
            print(data)
        }
    case .failure(let error):
        print("Error: \(error)")
        return
    }
}
```
