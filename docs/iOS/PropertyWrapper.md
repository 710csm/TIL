# Property Wrapper
- Swift5.1에 추가된 기능
- Property Wrapper를 사용하면 반복되는 코드를 줄일 수 있다. 코드의 재사용성이 올라간다. 
- SwiftUI에서 사용되는 @Published, @ObservedObject, @State 등도 Property Wrapper이다.
- 프로퍼티를 가질 수 있는 타입 앞에 붙힐 수 있다 (class, struct, enum)

## 사용법
- 다음과 같이 @propertyWrapper를 타입 앞에 붙혀야한다.
<img width="194" alt="스크린샷 2021-10-21 오후 5 52 34" src="https://user-images.githubusercontent.com/45002556/138244703-e99d1203-2dc4-4039-9b49-62506fe53835.png">

- wrappedValue를 선언하여 반복되는 로직을 넣지 않으면 에러가 난다
<img width="834" alt="스크린샷 2021-10-21 오후 5 54 55" src="https://user-images.githubusercontent.com/45002556/138245083-ee6ea009-3acd-4c42-843b-7ae5a4c26951.png">

- 예시로 Person과 SuperHero의 name 변수의 get, set은 같은 코드를 반복하여 작성할 수 있는 코드이기 때문에 다음과 같이 PropertyWrapper를 사용하여 코드를 재사용해서 반복되는 코드를 줄일 수 있다.
<img width="394" alt="스크린샷 2021-10-21 오후 6 00 46" src="https://user-images.githubusercontent.com/45002556/138245953-a25aa7e5-6862-4513-981a-3273a032a6af.png">


## 활용법
- WWDC 19에서도 사용된 UserDefaults 예제가 적절하다
1. 일반적인 코드

```Swift
class UserManager { 
    static var wifiOnly: Bool { 
        get { return UserDefaults.standard.bool(forKey: "wifiOnly") } 
        set { UserDefaults.standard.set(newValue, forKey: "wifiOnly") } 
        
    } 

    static var userEmail: String? { 
        get { return UserDefaults.standard.string(forKey: "userEmail") } 
        set { UserDefaults.standard.set(newValue, forKey: "userEmail") } 
    } 
        
    static var isLogIn: Bool { 
        get { return UserDefaults.standard.bool(forKey: "isLogIn") } 
        set { UserDefaults.standard.set(newValue, forKey: "isLogIn") } 
    } 
}
```


2. Property Wrapper를 활용한 경우

```Swift
@propertyWrapper
struct UserDefault<T> {
    let key: String
    let defaultValue: T
    
    var wrappedValue: T {
        get { UserDefaults.standard.object(forKey: self.key) as? T ?? self.defaultValue }
        set { UserDefaults.standard.set(newValue, forKey: self.key) }
    }
}

class UserDefaultManager { 
    @UserDefault(key: "wifiOnly", defaultValue: false) 
    static var wifiOnly: Bool 
    
    @UserDefault(key: "userEmail", defaultValue: "") 
    static var userEmail: String
    
    @UserDefault(key: "isLogIn", defaultValue: false) 
    static var isLogIn: Bool
}
```
