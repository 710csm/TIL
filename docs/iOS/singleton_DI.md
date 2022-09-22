# Singleton vs Dependency Injection
- 우리는 iOS 앱을 만들 때 앱의 한 부분을 관리해주는 클래스를 만드는일이 종종 있다. 이러한 클래스들은 보통 manager라는 이름으로 만들고 이 클래스들은 REST API, WebSockets, database, caching, notifications, chat 등의 기능을 가지고 있다. manager  클래스를 만들 때, 고려하는것중 하나는 "뷰 컨트롤러 내부와 같은 내 앱에서 액세스하려면 어떻게 해야 할까?"이다. 이러한 경우 싱글톤과 Dependency Injection 패턴 두가지 방법이 있다.

## 싱글톤(Singleton)
- 싱글톤 패턴은 쉽게 사용할 수 있다고 잘 알려져 있다. 보통 shared라 불리는 static 인스턴스를 가지고 있는 파사드 클래스로 구성되어 있다. MyFaced.shared와 같은 방식으로 앱내 어디에서든 액세스 할 수 있다. 파사드 클래스의 initializer는 private로 만들어져 있기 때문에 앱에서 유일한것으로 만들어진다.

```Swift
import StreamChat
import UIKit

extension ChatClient {
    static var shared: CharClient!
}

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        let token = Token("[jwt token]")
        let tokenProvider = TokenProvider.static(token)
        let config = ChatClientConfig(apiKeyString: "[api_key]")
        
        ChatClient.shared = ChatClient(config: congif, tokenProvider: tokenProvider)
        
        return true
    }
}
```

### 싱글톤의 장점
- 싱글톤 패턴의 장점은 액세스가 쉽다는 점이다. shared 인스턴스가 필요한경우 코드를 더 작성하지 않아도 액세스 할 수 있다.
- 파사드 클래스의 initializer가 private로 만들어졌기 때문에 데이터베이스에 대한 동시 쓰기와 같은 충돌을 피할 수 있는 간단한 방법이다.

### 싱글톤의 단점
- 싱글톤 패턴의 단점은 앱이 더 거대해지고 복잡해질수록 잘 들어난다. 앱의 여러 부분에서 shared 인스턴스에 액세스 할수록 앱의 동작을 예측할 수 없게 되고 모든 코드를 싱글톤의 글로벌 상태와 동기화 하는것이 어려워진다.
- 또한 mock 데이터를 만드는것이 어려우면 유닛 테스트 또한 힘들 수 있다.


## 의존성 주입(Dependency Injection)
- Dependency Injection은 예측 불가능성을 추가할 위험이 적으면서 규모가 큰 앱에서 싱글톤 패턴에 대한 좋은 대안이 될 수 있다.

```Swift
import StreamChat
import UIKit

class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    var window: Window?
    
    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, option connectionOption: UIScene.ConnectionOptions) {
        guard let scene = (scene as? UIWindowScene) else { return }
        
        let viewController = window?.rootViewController as? ViewController
        let config = ChatClientConfig(apiKeyString: "[api_key]")
        let chatClient = ChatClient(config: config, tokenProvider: tokenProvider)
        
        viewController?.chatclient = chatClient
    }
}
```

### 의존성 주입의 장점
- 의존성 주입은 여러가지 장점이 있다. 우선 인스턴스화가 제한되지 않기 때문에 mock 인스턴스를 만들어 유닛 테스트를 쉽게 할 수 있다.
- 또한 의존성 주입은 액세스가 인스턴스를 명시적으로 보유하는 코드 조각으로 제한되기 때문에 더 예측 가능하다.
- 의존성 주입은 코드에 전역 상태를 추가하지 않으므로 재사용이 더 쉬워진다.

### 의존성 주입의 단점
- 의존성 주입의 단점은 use case에 따라 문제가 생길 수 있다. 예를 들어 객체에 액세스 하려면 참조할 속성 또는 매개 변수를 추가해야한다. 이는 기존 프로젝트에서 API가 깨지거나 개발과정에서 추가적인 단계가 필요할 수 있다.
- 의존성 주입을 사용하면 컴파일 과정에서 유일한 인스턴스를 보장받을 수 없다. 만약 여러개의 인스턴스가 생긴다면 런타임에서 컨플릭트를 유발할 수 있다. 따라서 이러한 잠재적인 이슈를 처리해야할 필요가 있다.
- 의존성 주입이 무조건 복잡하고 구현하기 어려운건 아니다. 하지만 이런 프로젝트를 처음으로 접하는 경우 파악하기가 어려울 수 있다.

## 결론
- 어떤 앱을 개발하냐에 따라 다르겟지만, 의존성 주입은 까다롭지만 clean architecture를 구현할 때 많이 사용된다. 싱글톤 패턴은 클린 코드를 선호하는 사람들에게 Anti-Pattern으로 간주되지만 훨씬 쉽다. 여전히 많은 개발자들이 두가지 패턴을 모두 사용하고 있기 때문에 두가지 방법중 현재 프로젝트에 가장 적합한 항목을 잘 선택해야 한다. 

## reference
https://getstream.io/blog/singleton-dependency-injection-in-swift/
