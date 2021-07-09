# 구글 로그인

## 사용법
1. CocoaPods 설치 

```
pod 'GoogleSignIn'

// thne
pod install
```

2.  URL scheme 추가
- OAuth 클라이언트 ID를 생성 
- 프로젝트 설정 -> info 탭 -> URL Types 에서 URL scheme에 역전된 클라이언트 id 입력

<img width="853" alt="스크린샷 2021-07-09 오후 1 36 07" src="https://user-images.githubusercontent.com/45002556/125023837-9f889780-e0ba-11eb-916d-6939ac50770f.png">

3. AppDelegate에 Google Login 설정

```Swift
GIDSignIn.sharedInstance().cliendID = "69429070123-59m4qebb7ikg9toofh1kv0ir40123.apps.googleusercontent.com"
```

- ios 13 미만

```Swift
// AppDelegate
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey: Any]) -> Bool {
    return GIDSignIn.sharedInstance().handle(url)
}
```

- ios 13 이상

```Swift
// SceneDelegate
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
    if let url = URLContexts.first?.url {
        if let scheme = url.scheme,
           scheme.contains("com.googleusercontent.apps") {
            GIDSignIn.sharedInstance().handle(URLContexts.first?.url)
        }
    }
```

4. 구글 로그인 실행

```Swift
import GoogleSignIn

class ViewController: GIDSignInDelegate {

    override func viewDidLoad() {
        super.viewDidLoad()
        // delegate 설정 및 로그인 화면이 나올 화면 설정
        GIDSignIn.sharedInstance().delegate = self
        GIDSignIn.sharedInstance().presentingViewController = self
    }
    
    func sign(_ signIn: GIDSignIn!, didSignInFor user: GIDGoogleUser!, with error: Error!) {
        if let error = error {
            print(error.localizedDescription)
            return 
        }
        
        guard let user = user else { return }
        guard let accessToken = user.authentication.accessToken else { return }
        guard let refreshToken = user.authentication.refreshToken else { return }
        guard let expiredIn = user.authentication.accessTokenExpirationDate
        
        guard let id = user.userID else { return }
        guard let email = user.profile.email else { return }
        guard let fullName = user.profile.name else { return } 
    }
    
    func sign(_ signIn: GIDSignIn!, didDisconnectWith user: GIDGoogleUser!, withError error: Error!) {
        if let error = error {
            print(error.localizedDescription)
        }
    }
}
```

5. 로그아웃

```Swift
func logOut() {
    GIDSignIn.sharedInstance().signOut()
}
```
