# Moya
- 열거형을 사용해 보다 명확하게 네트워킹을 구현할 수 있게 해주는 네트워킹 라이브러리

### 기존 네트워크 구현 방식의 문제
<img width="332" alt="스크린샷 2021-06-03 오전 9 29 47" src="https://user-images.githubusercontent.com/45002556/120568130-3f189180-c44e-11eb-8f77-6c19a58d53f0.png">

- 새 앱을 작성하기가 어려워진다
- 기존 애플리케이션 유지 관리 어려움이 잇다
- 단위 테스트가 어렵다

### 장점
<img width="250" alt="스크린샷 2021-06-03 오전 9 30 42" src="https://user-images.githubusercontent.com/45002556/120568191-5f485080-c44e-11eb-9c17-fccafe0491cf.png">

- 재사용이 편리하다.
- Moya는 Network layer를 템플릿화 하고 사용할때는 request, response만 처리하면 된다.
- 올바른 API endpoint 액세스에 대한 컴파일 시간을 확인할 수 있다.
- 여러 endpoint와 각 endpoint에 해당하는 열거형 사용하여 명확한 사용법을 정의할 수 있다. 
- Test stub을 1급 시민으로 취급하여 유닛 테스트가 매우 간편하다.

## 설치하는 법
```
---- CocoaPods ----
pod 'Moya', '~> 14.0'

# or 

pod 'Moya/RxSwift', '~> 14.0'

# or

pod 'Moya/ReactiveSwift', '~> 14.0'

# then run pod install

---- Carthage ----
github "Moya/Moya" ~> 14.0

# then run carhage update
```

## TargetType 정의
- baseURL: 서버의 Base URL
- path: API 주소. baseURL 뒤에 경로 형태
- method: HTTP method (GET, POST, …)
- sampleData: 테스트용 Mock이나 Stub
- task: 리퀘스트에 사용되는 파라미터 설정
- validationType: 허용할 response의 타입
- headers: HTTP header

```swift
import Moya
import Alamofire

public enum NetworkingManager {
    public static var parameter = Parameters()
    case login
    case userInfo
    case homeData
}

extension NetworkingManager: TargetType {

    public var baseURL: URL {
        return URL(string: "https://www.test.com")!
    }

    public var path: String {
        switch self {
        case .login:     return "/user/login"
        case .userInfo:  return "/user/userInfo"
        case .homeData:  return "/main/home"
        }
    }
    
    public var method: Moya.Method {
        switch self {
        default:
            return .post
        }
    }
    
    public var sampleData: Data {
        return Data()
    }
    
    public var task: Task {
        switch self {
        default:
            return .requestParameters(
                parameters: NetworkingManager.parameter,
                encoding: URLEncoding.default
            )
        }
    }
    
    public var headers: [String: String]? {
        return [
            "Content-Type": "application/x-www-form-urlencoded",
            "User-Agent": WebViewUtils.getUserAgentForAPI()
        ]
    }
    
    public var validationType: ValidationType {
        return .successCodes
    }
}
```

## Request 호출
```swift
func requestUserInfo(parameters: Parameters) {
    let provider = MoyaProvider<NetworkingManager>()
    NetworkingManager.parameter = parameters
    
    provider.request(.userInfo) { [weak self] result in
        guard let self = self else { return }
        
        switch result {
        case .success(let response):
            print(try? response.mapJSON())
        case .failure(let error):
            print(error.localizedDescription)
        }
    }
}

```

## Moya git 주소
<img width="866" alt="스크린샷 2021-06-03 오전 9 04 46" src="https://user-images.githubusercontent.com/45002556/120566702-c06e2500-c44a-11eb-9dcb-5e49ca53b0a3.png">


https://github.com/Moya/Moya
