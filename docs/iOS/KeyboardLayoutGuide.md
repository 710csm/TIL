# Keyboard Layout Guide
- 기존 iOS에서는 키보드 때문에 Layout을 조정을 해야할 때가 있었다. iOS 15 부터는 UIKeyboardLayoutGuide를 통해 쉽게 레이아웃을 관리할 수 있다.

- [WWDC 21 ) Your guide to keyboard layout](https://developer.apple.com/videos/play/wwdc2021/10259/)

## 기존 방법
1. notification 등록

```Swift
NotificationCenter.default.addOBserver(self, selector: #selector(keyboardWillShow(_ :)), name: UIResponder.keyboardWillShowNotification, object: nil)
```

2. Notification에서 프레임, animation duration 등을 가져온뒤 처리

```Swift
@objc func keyboardWillShow(_ notification: Notification) {
    let keyboardFrame = notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey]
    let duration = notification.userInfo?[UIResponder.keyboardAnimationDurationIserInfoKey]
    // do something 
}
```

3. 레이아웃 조정

## UIKeyboardLayoutGuide
- 기존에 사용하던 notification 등록 등을 할 필요 없다
- 키보드가 안나왔을 때는 safe area bottom에 걸린다
- 키보드 타입별(이모티콘, 자동완성 등) 높이에 자동으로 맞춤

![img1 daumcdn](https://user-images.githubusercontent.com/45002556/140458318-dc45414d-20f9-4c36-9c45-8381b8d5cb25.png)

```Swift
var okButton: UIButton = {
    let button = UIButton()
    button.setTitle("확인", for: .normal)
    button.backgroundColor = .blue
    return button
}()

override func viewDidLoad() {
    super.viewDidLoad()
    
    self.view.addSubview(okButton)
    self.okButton.snp.makeConstraints {
        $0.leading.trailing.equalToSuperview()
        $0.bottom.equalTo(self.view.keyboardLayoutGuide.snp.top)
    }
}
```
## followsUndockedKeyboard
- 키보드가 화면 하단에서 도킹 해제됐을 때 레이아웃이 키보드를 따라가는지 여부를 나타내는 값

```Swift
self.view.keyboardLayoutGuide.followsUndockedKeyboard = true
```

