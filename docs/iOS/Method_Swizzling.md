# Method Swizzling

## Method Swizzling이란?
- Runtime 시점에 기존 메서드를 다른 메서드로 바꾸어 실행하는것
- Objective-C에 의해 만들어진 Swift 언어도 Objective-C의 성격을 가지고 있으므로 메소드의 실행은 런타임에 결정된다. 메소드의 실행은 런타임 때 결정되므로, 런타임에서 메소드가 실행되기전에 원하는 내용으로 바꿀 수 있다.
- 특정 SDK의 메소드가 있을 때, 이 메소드를 변경하고 싶은 경우에 매우 유용하게 사용할 수 있다.

## Method Swizzling 사용 이유
- ViewController마다 ViewWillAppear 메서드를 오버라이딩 해서 할 수 있다. 하지만 이런 경우 ViewController가 많아질수록 똑같은 내용의 코드를 매번 작성해야한다.
- 또는 UIVieWController를 상속받는 서브 클래스를 만들고, viewWillAppear를 오버라이딩 ㅎ한 후, 이후 ViewController에서 이 서브 클래스를 상속받게 할 수 있다. 이렇게 ViewWillAppear 메소드 하나를 바꾸기위해 서브 클래싱을 하고 앞으로의 ViewController의 상속을 다 바꿔줘야하기 때문에 복잡하고 귀찮아진다.
- 따라서 이럴 때 사용하는 것이 Method Swizzling이다. Method Swizzling을 사용하면 서브 클래스를 만들 필요 없이 단순히 메서드를 바꿔주는 것이다.

## Method Swizzling 사용 방법  
1. UIViewController의 메서드를 바꿔주는 것이기 때문에 UIViewController를 extension 하고 교체할 메서드를 추가해준다.

```Swift
extension UIViewController {
    @objc public func swizzlingViewWillAppear(animated: Bool) {
        print("swizzleViewWillAppear")
    }
}
```

2. 교체할 메서드를 추가한 이후 Method Swizzling을 적용하는 타입 메서드를 추가하고 Selector를 이용해 가져온다

```Swift
extension UIViewController {
    class func swizzleMethod() {
        let originalSelector = #selector(viewWillAppear)
        let swizzleSelector = #selector(swizzleViewWillAppear)
        
        // class_getInstanceMethod 메서드는 첫 번째 파라미터로 바꿔줄 메서드를 가진 클래스를 넣고, 두 번째 파라미터는 가져올 메서드를 Selector로 넣어준다
        guard let originMethod = class_getInstanceMethod(UIViewController.self, originalSelector) else { return )
         
        guard let swizzleMethod = class_getInstanceMethod(UIViewController.self, swizzleSelector) else { return }
    }
    
    @objc public func swizzleViewWillAppear(animated: Bool) {
        print("swizzleViewWillAppear")    
    }
}
```

3. 메소드를 바꿔줄 코드를 작성하고 AppDelegate에서 실행해준다.

```Swift
extension UIViewController {
    class func swizzleMethod() {
        let originalSelector = #selector(viewWillAppear)
        let swizzleSelector = #selector(swizzleViewWillAppear)
        
        // class_getInstanceMethod 메서드는 첫 번째 파라미터로 바꿔줄 메서드를 가진 클래스를 넣고, 두 번째 파라미터는 가져올 메서드를 Selector로 넣어준다
        guard let originMethod = class_getInstanceMethod(UIViewController.self, originalSelector) else { return )
         
        guard let swizzleMethod = class_getInstanceMethod(UIViewController.self, swizzleSelector) else { return }
        
        method_exchangeImplementations(originMethod, swizzleMethod)
    }
    
    @objc public func swizzleViewWillAppear(animated: Bool) {
        print("swizzleViewWillAppear")    
    }
}
```

```Swift
@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        UIViewController.swizzleMethod()
        return true
    }
}
```

## Method Swizzling 사용시 주의사항
- Method Swizzling은 런타임 시점에 메서드를 임의로 바꾸는 것이기 때문에 에러가 날 가능성이 있다. iOS 버전이 올라가며 우리가 Swizzling한 메서드가 바뀌면 문제가 될 수 있다. 따라서 꼭 필요한 경우에 사용해야한다.
