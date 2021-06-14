# Swinject

## Swiject 사용을 해야하는 이유
### 1. 의존성이란?

```Swift
class someNetworkService {
    func fetchData() {
        print(#function, "이 먼저 실행되어야함")
    }
}

class SomeViewModel {
    let dependency = SomeNetworkService()
    
    // dependency.fetchData함수를 실행해야만 하는 함수
    func viewModelFunction() {
        dependency.fetchData()
        print("dependency에서 가져온 data를 business logic으로 처리")
    }
}

let viewModel = SomViewModel()
viewModel.viewModelFunction()
```

위의 예제를 보면 SomeViewModel class는 SomeNetworkService clsss를 인스턴스로 가지고 있다. 그 이유는 viewModelFunction에서 함수를 실행할 때 dependency.fetchData를 먼저 실행해서 얻은 데이터를 통해 비지니스 로직을에 따라 데이터를 처리하기 때문이다.

따라서 SomeViewModel은 SomeNetworkService 없이는 함수를 실행할 수 없기 때문에 SomeNetworkservice에 의존성을 가진다.

이렇게 의존성을 가지면 테스트가 불가능하고, 강한 결합을 가지게 된다.

그렇기 때문에 SOLID(객체 지향 설계)에 따라서 ***Dependency Inversion Principle(의존관계 역전 원칙)*** 에 따라 의존관계를 역전시켜야 한다.

<img width="758" alt="스크린샷 2021-06-14 오후 4 46 14" src="https://user-images.githubusercontent.com/45002556/121856940-09b65280-cd30-11eb-9f90-157cd0eed397.png">

### 2. Dependency Inversion Principle(의존관계 역전 원칙)
객체 지향 프로그래밍에서 의존관계 역전 원칙은 소프트웨어 모듈들을 분리하는 특정 형식을 지칭한다. 이 원칙을 따르면, 상위 계층이 하위 계층에 의존하는 전통적인 의존관계를 반전시킴으로써 상위 계층이 하위 계층의 구현으로부터 독립되게 할 수 있다. 이 원칙은 다음과 같은 내용을 담고 있다.

1. 상위 모듈은 하위 모듈에 의존해서는 안된다. 상위 모듈과 하위 모듈 모두 추상화에 의존해야 한다.
2. 추상화는 세부 사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야 한다.

이 원칙은 상위와 하위 객체 모두가 동일한 추상화에 의존해야 한다는 객체 지향적 설계의 대원칙을 제공한다.

### 3. Inversion of control(제어의 역전)
지금 SomeNetworkService는 추상화 되어있지 않다.  따라서 세부사항은 추상화에 의존해야하는데 이 원칙을 지키고 있지 않다.    
따라서 다음과 같이 SomeNetworkService를 추상화 한다.

```Swift
protocol SomeNetworkServiceType {
    func fetchData()
}

class SomeNetworkService: SomeNetworkServiceType {
    func fetchData() {
        print(#function, "이 먼저 실행되어야함")
    }
    
    func configureSomeNetworkDetails() {
        // do something
    }
}
```

SomeNetworkServiceType 프로토콜을 정의해줌으로써 SomeNetworkService가 맡아야할 책임을 정의한다.

```Swift
class SomeViewModel {
    let dependency: SomeNetworkServiceType = SomeNetworkService()
    
    func viewModelFunction() {
        dependency.fetchData()
        print("dependency에서 가져온 datafmf business logic으로 처리")
    }
}
```

바뀐 부분은 dependency가 SomeNetworkServiceType이 되었다는 것이다.
하지만 이로 인해 dependency.configureSomeNetworkDetails에 접근하지 못하고,
dependency.fetchData에만 접근할 수 있게 됐다.

즉, 제어의 주체가 SomeNetworkService(class - 세부 사항)에서 SomeNetworkServiceType(protocol - 추상화)로 바뀌었다.

<img width="779" alt="스크린샷 2021-06-14 오후 4 58 28" src="https://user-images.githubusercontent.com/45002556/121858599-bfce6c00-cd31-11eb-8e90-11aa0c0b5349.png">

위와같이 제어의 주체가 바뀌었다. 하지만 테스트가 가능해지거나 강한 결합이 느슨해지지는 않느다.     
아직도 의존성은 SomeViewModel에 존재한다. 따라서 외부에서 의존성을 주입시키는 방법을 써야한다.

### 4. Dependency Injection
#### 장점
1. 테스트에 용이함
2. 재사용성을 높여줌
3. 코드의 단순화
4. 종속적이던 코드의 제거
5. 결합도는 낮추면서 유연성과 확장성은 향상

## Dependency Container란
의존성을 주입 할 때는 밖에서 인스턴스를 만들어서 주입하고 인스턴스를 만들어서 주입해주는 곳은 앱에서 여러군데입니다.   
즉 인스턴스를 만드는 위치가 분산되어 있습니다.

따라서 Container를 만들어 모든 인스턴스를 가지고 있고 관리하게 합니다.

<img width="688" alt="스크린샷 2021-06-14 오후 5 03 17" src="https://user-images.githubusercontent.com/45002556/121859234-6d417f80-cd32-11eb-958a-fe4cf03b141f.png">

## 설치방법
1. Carthage

```
// to install Swinject with Carthage, add the following line to your Cartfile
github "Swinject/Swinject" ~> 1.1.4

// then run 
carthage update --no-use-binaries
// or
carthage update
```

2. CocoaPods

```
pod 'Swinject'
// then run
pod install
```

## 기본 사용법
- 정의된 protocol과 class

```Swift
protocol Animal {
    var name: String? { get }
}

class Cat: Animal {
    let name: String?
    
    init(name: String?) {
        self.name = name
    }
}

// and
protocol Person {
    func play()
}

class PetOwner: Person {
    let pet: Animal
    
    init(pet: Animal) {
        self.pet = pet
    }
    
    func play() {
        let name = pet.name ?? "someone"
        print("I'm playing with \(name).")
    )
}
```

- 먼저 Service 및 Component를 컨테이너에 등록합니다. 컨테이너는 등록된 closure에 의해 컴포넌트가 만들어지는곳을 말합니다. 그 예시로 Cat과 PetOwner는 각각 Animal 및 Person 프로토콜을 구현하는 클래스입니다. 

```Swift
let container = Container()
container.register(Animal.self) { _ in Cat(name: "Mimi") }
container.register(Person.self) { r in
    PetOwner(pet: r.resolve(Animal.self)!)
}
```

- 그런 다음 컨테이너에서 만들어둔 인스턴스를 가져옵니다. 다음고 같은 동작을 실행할 수 있습니다. 

```Swift
let person = container.resolve(Person.self)!
person.play() // prints "I'm playing with Mimi."
```

## Services들을 register 할 곳
- Services들은 무조건 사용되기 전에 컨테이너에 등록되어야 한다. 등록 방법은 SwinjectStoryboard를 사용하냐에 따라 결정된다.

### With SwinjectStoryboard
- 다음 코드는 위의 예시가 적용되어 있는 view controller이다.

```Swift
class PersonViewController: UIViewController {
    var person: Person?
}
```

- 만약 Swinject v2 와 Swift 3을 사용한다면 소스 코드 맨위에 SwinjectStoryboard를 import 해야한다.

```Swift
// only Swinject v2 in Swift 3
import SwinjectStoryboard
```

- 만약 SwinjectStoryboard를 사용한다면 Services들은 SwinjectStoryboard 의 extension에서 등록해야 한다.  

```Swift
extension Swinjectstoryboard {
    @objc class func setup() {
        defaultcontainer.register(Animal.self) { _ in Cat(name: "Mimi") }
        defaultContainer.register(Person.self) { r in 
            PetOwner(pet: r.resolve(Animal.self)!)
        }
        defaultContainer.register(PersonViewController.self) { r in
            let controller = PersonViewController(0
            controller.person = r.resolve(Person.self)
            return controller
        }
    }
}
```

### Without SwinjectStoryboard
- 만약 SwinjectStoryboard를 사용하지 않고 view controller를 만든다면, services는 AppDelegate에서 컨테이너에 등록되면 된다. application didFinishLaunchingWithOptions 메소드가 끝나기전에  등록하면 services들이 사용되기 전에 등록되는 것을 보장할 수 있다.   

```Swift
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    let container: Container = {
        let container = Container()
        container.register(Animal.self) { _ in Cat(name: "Mimi") }
        container.register(Person.self) { r in
            PetOwner(pet: r.resolve(Animal.self)!)
        }
        container.register(PersonViewController.self) { r in
            let controller = PersonViewController()
            controller.person = r.resolve(Person.self)
            return controller
        }
        return container
    }()
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey : Any]? = nil) -> Bool {
        let window = UIWindow(frame: UIScreen.main.bounds)
        window.makeKeyAndVisible()
        self.window = window
        
        window.rootViewController = container.resolve(PersonViewController.self)
        
        return true
    }
}
```

## Swinject git 주소
https://github.com/Swinject/Swinject


## 참고
1. https://velog.io/@hansangjin96/Swinject-%EC%9D%98%EC%A1%B4%EC%84%B1-%EA%B4%80%EB%A6%AC%EB%9E%80
2. https://eunjin3786.tistory.com/233
3. https://doodledevnote.tistory.com/30
