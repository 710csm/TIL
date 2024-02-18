# Preview
- #Preview는 SwiftUI의 preview 기능을 UIKit에서도 간편하게 사용할 수 있는 메크로이다. #Preview는 매크로로 externalMacro로 생성이 된다. #Preview 키워드를 사용하여 간편하게 사용할 수 있다.

![ray-so-export](https://github.com/710csm/TIL/assets/45002556/0865c9d7-ad9b-4c3a-9725-573e5e84132a)

## 사용법

```swift
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
    }
}

#Preview {
    let viewController = ViewController()
    return viewController
}
```
