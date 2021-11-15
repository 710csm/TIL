# UIKeyCommands
- iOS에서 외부 키보드 입력을 감지할 때 사용       

## 입력키 종류
- UIKeyCommand.inputLeftArrow   // 왼쪽 방향키
- UIKeyCommand.inputRightArrow  // 오른쪽 방향키
- UIKeyCommand.inputUpArrow     // 위쪽 방향키
- UIKeyCommand.inputDownArrow   // 아래쪽 방향키
- UIKeyCommand.inputDownArrow.f1 ~ f12   // F1 ~ f12
- ","
- "."
- "\r" // 엔터
- " "  // 스페이스   
- 이 외에도 많은 입력키가 있다

## 사용법     
```Swift
let leftArrow = UIKeyCommand(
    input: UIKeyCommand.inputLeftArrow,
    modifierFlags: [],
    action: #selector(onClickedLeftArrow)
)
self.addKeyCommand(leftArrow)

@objc
func onClickedLeftArrow() {
    // do something..
}
```     

### input
- 입력받을 키

### modifierFlags
다른 키와 같이 눌렀을 경우 입력을 받을수 있도록 설정하는 값

- .alphaShift: Caps Lock key
- .shift: Shift key
- .control: Control key
- .alternate: Option key
- .command: Command key
- .numericPad: Key must be on numeric keypad

### action
- 입력받고 동작할 메소드 정의

## iOS 15 이후
- wantsPriorityOverSystemBehavior 값을 true로 설정하지 않으면 방향키의 입력이 인식되지 않을 수 있다

```Swift
let rightArrow = UIKeyCommand(
    input: UIKeyCommand.inputRightArrow,
    modifierFlags: []
    action: #selector(someAction)
)
rightArrow.wantsPriorityOverSystemBehavior = true
self.addKeyCommand(rightArrow)
```
