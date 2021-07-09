# 페이스북 로그인

## 사용법

1. CocoaPods 설치

```
pod 'FBSDKLoginKit'

// then
pod install
```

2. 프로젝트 설정
- 페이스북 Developers에서 앱 등록
- Info.plist에 다음 코드 등록

```
<key>CFBundleURLTypes</key>
<array>
  <dict>
  <key>CFBundleURLSchemes</key>
  <array>
    <string>fbAPP-ID</string>
  </array>
  </dict>
</array>
<key>FacebookAppID</key>
<string>APP-ID</string>
<key>FacebookClientToken</key>
<string>CLIENT-TOKEN</string>
<key>FacebookDisplayName</key>
<string>APP-NAME</string>
```

- 페이스북 앱으로 앱 전환을 수행할 수 있는 코드를 등록

```
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>fbapi</string>
  <string>fbapi20130214</string>
  <string>fbapi20130410</string>
  <string>fbapi20130702</string>
  <string>fbapi20131010</string>
  <string>fbapi20131219</string>
  <string>fbapi20140410</string>
  <string>fbapi20140116</string>
  <string>fbapi20150313</string>
  <string>fbapi20150629</string>
  <string>fbapi20160328</string>
  <string>fbauth</string>
  <string>fb-messenger-share-api</string>
  <string>fbauth2</string>
  <string>fbshareextension</string>
</array>
```

3. AppDelegate 연결

```Swift
ApplicationDelegate.shared.application(application, didFinishLaunchingWithOptions: launchOptions)
```

- iOS 13 미만

```Swift
// AppDelegate
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey: Any] = [:]) -> Bool {
    ApplicationDelegate.shared.application(
        app,
        open: url,
        sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String,
        annotation: options[UIApplication.OpenURLOptionsKey.annotation]
    )
}
```

- iOS 13 이상

```Swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
    if let url = URLContexts.first?.url {
        ApplicationDelegate.shared.application(
            UIApplication.shared,
            open: url,
            sourceApplication: nil,
            annotation: [UIApplication.OpenURLOptionsKey.annotation]
        )
    }
}
```

4. 페이스북 로그인 실행

```Swift
import FBSDKLoginKit

class ViewController {
    // facebook 로그인 메니저 생성
    let loginManager: LoginManager = LoginManager()
    
    func login() {
        if AccessToken.current != nil {
            // User already logged in. perform log out if needed
            loginManager.logOut()
        }
        
        loginManager.logIn(permissions: ["public_profile", "email"], from: self) { (result, error) in
            if let error = error {
                print(error.localizedDescription)
                return
            }
            
            guard let result = result, !result.isCancelled else { return }
            
            self.getUserData(with: result.token!)
        }
    }
    
    func getUserData(with token: AccessToken) {
        GraphRequest(
            graphPath: "me",
            parameters: ["fields": "id, name, email"]
        )
        .start { (_, result, error) in
            if let error = error {
                print(error.localizedDescription)
                return 
            }
            
            guard let facebook = result as? [String: AnyObject] else { return }
            
            let accessToken = token.tokenString
            let expiresIn = token.expirationDate
            
            guard let id = facebook["id"] as? String else { return }
            guard let name = facebook["name"] as? String else { return }
            guard let email = facebook["email"] as? String else { return }
        }
    }
}
```

5. 로그아웃 

```Swift
func logOut() {
    LoginManager().logOut()
}
```
