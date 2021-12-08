# Resolver 
- iOS를 위한 초경량 의존성 주입 / 서비스 로케이터 프레임워크

## 설치방법
- cocoapod 설치      

```
pod "Resolver"
```      

- Swift Package Manager

```Swift
dependencies: [
    .package(url: "https://github.com/hmlongco/Resolver/git", from: "1.1.2")
]
```      

## 사용법
- @Injected Property wrapper 키워드를 사용해 변수 앞에 선언해준다. 

```Swift
// 기존
private let session = URLSession(configuration: .default)

// Resolver
@Injected private var session: URLSession
```

- Resolver 클래스를 extension하고 ResolverRegistering을 상속받아 registerAllServices 함수를 구현한다.
- registerAllServices에서 register를 통해 등록한다.

```Swift
extension Resolver: ResolverRegistering {
    public static func registerAllServices() {
        register { URLSession(configuration: .default) }
    }
}
```

### Scope
- 범위 지정을 통해 싱글톤처럼 동작할지 정할 수 있다.

1. Graph: Graph는 Resolver에 기본 scope이다. Resolver는 요청된 object를 확인하면 기존에 생성된 모든 object를 무시한다. 결과적으로, Resolver가 매 호출마다 새로운 인스턴스를 생성한다.

2. Application: Application은 싱글톤과 동일하다. Resolver가 object를 최초로 확인했을 때 이 object의 인스턴스를 가지고있다가 앱이 활성화된 상태인 한 이후의 필요한곳에서 계속 사용한다.

- defaultScope를 통해 default scope를 설정할 수 있다. 
```Swift
defaultScope = .graph
or
defaultScope = .application
```  

- .scope()를 통해 각 등록마다 scope를 지정할 수 있다.
```Swift
register { URLSession(configuration: .default) }.scope(.graph)
or
register { URLSession(configuration: .default) }.scope(.application)
```

## Registering Protocols

