# Builder Pattern
- 빌더 패턴이란 복잡한 객체의 생성 절차는 항상 동일하되 결과는 다르게 만드는 패턴이다. 
- Swift에서는 빌더 패턴을 잘 사용하지 않지만 코드로 ui를 만들 때 속성 값을 지정해야한다. 주로 아래와 같은 방식으로 코드를 작성해 label을 만들게 된다. 빌드 패턴을 사용하면 이러한 속성을 체이닝 형식으로 만들 수 있다.

```swift
let textLabel: UILabel = {
    let label = UILabel()
    label.text = "example"
    label.textColor = .black
    label.font = .systemFont(sizeOf: 10)
    return label 
}()
```
## Builder Pattern의 역할
1. Director: 객체 생성 방식에 대한 책임
2. Builder: 객체를 생성하는 추상 인터페이스
    - 추상화 작업이 필요하다, Protocol을 통해 객체가 무엇을 할 수 있는지 나타내주는 역할을 한다.
3. ConCreateBuilder: Builder의 구현 클래스, 객체 생성 결과에 대한 구체적인 구현 책임 
4. Product: Builder를 이용해서 Director가 만들어낸 최종 객체

## Builder 작성
- protocol로 구현, return type을 Builder로 해준다

```swift
protocol Builder {
    var label: UILable { get }
    func setText(with text: String) -> Builder
    func setTextColor(with textColor: UIColor) -> Builder
    func setFontSize(with textFontSize: CGFloat) -> Builder
}
```

## ConCreateBuilder 작성
- ConCreateBuilder는 Builder를 채택 후 상세한 구현 내용을 구현해야한다. 

```swift
class ConCreateBuilder: Builder {
    var label: UILabel = UILabel()
    
    func setText(with text: String) -> Builder {
        label.text = text
        return self
    }
    
    func setTextColor(with textColor: UIColor) -> Builder {
        label.textColor = textColor
        return self
    }
    
    func setFontSize(with textFontSize: CGFloat) -> Builder {
        label.font = .systemFont(ofSize: textfontSize)
        return self
    }
}
```

## Director 작성
- Director는 값을 Builder를 파라미터로 받아 기본 세팅을 하여 넘겨주는 형태이다. 즉 객체를 생성해 return을 해주는 책임을 가지게 된다.

```swift
class Director {
    func makelabel(builder: Builder) -> UILabel {
        let build = builder
        build.setText(with: "example")
        build.setTextColor(with: .black)
        build.setText(with: 40)
        return build.label
    }
}
```
## 사용법
- 간단한 사용법
```swift
class ViewController: UIViewController {
    private let director: Director = Director()
    
    override func viewDidLoad() {
        super. viewDidLoad
        
        let label = director.makeLabel(builder: ConCreateBuilder())
        self.view.addSubview(label)
        label.frame = CGRect(x: 0, y: 0, width: 100, height: 100)
    }
}
```

- 체인형태의 사용법

```swift
class ViewController: UIViewController {
    private let director: Director = Director()
    
    private let tempLabel: UILabel = ConCreateBuilder()
        .setText(with: "example")
        .setTextColro(with: .black)
        .setFontSize(with: 20)
        .label

    override func viewDidLoad() {
        super.viewDidLoad()
        self.view.addSubview(tempLabel)
        tempLabel.frame = CGRect(x: 0, y: 200, width: 100, height: 100)
    }
}
```
