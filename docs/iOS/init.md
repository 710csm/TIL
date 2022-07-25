# Init

## Designated init vs convenience init

### Designated init
Designated init은 swift의 초기화 이니셜라이져이다. 이 init은 클래스의 모든 프로퍼티가 초기화 될 수 있도록 해야 한다. 이름은 Designated init이지만 init으로 쓴다.

```swift
class Person {
    var name: String
    var age: Int
    var gender: String

    init(name: String, age: Int, gender: String) {
        self.name = name
        self.age = age
        self.gender = gender
    }
}
```

위에 init에서 프로퍼티가 하나라도 빠지면 에러 발생

<img width="831" alt="f1" src="https://user-images.githubusercontent.com/45002556/108619299-1b18dd80-7467-11eb-85be-ff97c3b73f61.png">

### convenience init
convenience init은 보조 이니셜라이저이다. 클래스의 원래 이니셜라이저인 init을 도와주는 역할을 한다. Designated init의 파라미터 중 일부를 기본값으로 설정해서, 이 convenience init 안에서 Designated init을 호출해 초기화를 진행할 수 있다.

따라서 convenience init을 사용하려면 Designated init이 먼저 선언되어야 한다.

```swift
class Person {
    var name: String
    var age: Int
    var gender: String

    init(name: String, age: Int, gender: String) {
        self.name = name
        self.age = age
        self.gender = gender
    }

    convenience init(age: Int, gender: String) {
        self.init(name: "zedd", age: age, gender: gender)

    }
}
```

## UIView에서 Init
### init(frame: CGRect)
- interface Builder에서 쓰이지 않고, 코딩으로 UIView를 상속받은 클래스를 만들 때 사용
- ex) let button = UIButton(frame: CGRect(x:0, y:0, width:100, height:100))

### required init?(coder aDecoder: NSCoder)
- interface Builder에서 생성되는 초기화 구문

- View나 Buttton과 같은 ui를 커스텀 할 경우, 위에 두가지 초기화 구문이 필수이다.
```swift
class CustomView: UIView {
    @IBOutlet weak var title: UILabel!
    @IBOutlet weak var subTitle: UILabel!
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        setupView()
    }
    
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
    }
    
    func setupView() {
        // do something...
    }
}
```

### awakeFromNib()
- interface Builder에서 객체가 init(coder:)로 초기화된 후 호출
- IB에서 inspector와 같은 방법으로 값을 조정하면 위 커스텀 클래스는 아카이브되어 있다가 언아카이브로 변화 되는데, (이 때 init(coder:)가 호출되며 내부 속성이 초기화 -> init시점에는 frame과 관련된 크기, 위치 등이 정해지지 않은 상태)
- 위 단계에서 IB는 연결된 객체의 변수를 사용하려고 할 때 awakeFromNib()을 사용

### prepareForInterfaceBuilder() 
- @IBDesignable을 지정하여 커스텀 뷰를 만든 경우에 선언하여 사용
- 인터페이스 빌더에서 디자인 타임에 초기화되는 함수 
