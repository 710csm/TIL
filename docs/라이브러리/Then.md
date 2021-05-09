# Then
- Swift initializer를 간단하게 사용할 수 있게 해주는 라이브러리 

## 사용법
1. CocoaPods으로 Then 설치

```
pod 'Then'
```

2. 예제

```Swift
import Then

final class MyViewController: UIViewController {

  let titleLabel = UILabel().then {
    $0.textColor = .black
    $0.textAlignment = .center
  }

  let tableView = UITableView().then {
    $0.backgroundColor = .clear
    $0.separatorStyle = .none
    $0.register(MyCell.self, forCellReuseIdentifier: "myCell")
  }

  override func viewDidLoad() {
    super.viewDidLoad()
    self.view.addSubview(self.titleLabel)
    self.view.addSubview(self.tableView)
  }
}
```



## Then을 사용하지 않을때
```swift
private let textLabel2: UILabel = {
    let label = UILabel()
    label.text = "test"
    label.isUserInteractionEnabled = true
    label.textColor = .black
    return label
}()
```

## Then을 사용할 때
```swift
private let textLabel1 = UILabel().then {
    $0.text = "test1"
    $0.isUserInteractionEnabled = true
    $0.textColor = .black
}
```

## Then git 주소

https://github.com/devxoul/Then
