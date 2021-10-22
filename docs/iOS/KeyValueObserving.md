# Key Value Observing
- 객체의 프로퍼티의 변경사항을 다른 객체에 알리기 위해 사용하는 코코아 프로그래밍 패턴
- 특정 키의 값의 변화를 감지하기 위한 기능
- NSObject를 상속한 클래스에서만 KVO를 사용할 수 있다.


## 사용법
- NSObject를 상속
- observe하려는 프로퍼티에 @objc dynamic modifier를 추가 

```Swift
class Test {
    @objc dynamic var value: String
    
    intit(value: String) {
        self.value = value
    }
}
```

## Observer 정의
- KeyPath를 사용하여 프로퍼티 KeyPath에 observe를 추가할 수 있다 (ex. `\.value`)

```Swift
var test = Test(value "hello")

test.observe(\.value, option: [.old, .new]) { (object, change) in
    print(change.oldValue, change.newValue)
}
```

이후 test의 value값을 변경하면 프로퍼티에 변경사항이 생겨서 observer의 change handler가 호출된다. handler 내에서 oldValue와 newValue를 가져올 수 있다.

```Swift
var test = Test(value "hello")

test.observe(\.value, option: [.old, .new]) { (object, change) in
    print(change.oldValue, change.newValue) // Optional("hello") Optional("hello world")
}

test.value = "hello world"
```

## Observer option 종류
- old
- new 
- initial: 초기화시에도 observer handler를 호출할 때 사용
- prior: 이전과 바뀐 상태를 호출하는 옵션


## KVO의 장단점
 **장점**
 1. 두 객체간의 동기화를 달성 가능. Model과 View와 같이 논리적을 분리된 파트간의 변경사항을 전달 -> 동기화 가능
 2. 객체의 구현을 변경하지 않고 내부 객체의 상태 변화에 대응할 수 있다. (SDK 객체의 경우)
 3. 관찰된 프로퍼티의 이전값과 최신값을 제공
 4. KeyPath를 사용하여 프로퍼티를 관찰하므로 nested 프로퍼티도 관찰 가능
 5. 따로 옵저버를 해제해주지 않아도 시스템에서 옵저버를 제거해준다.
 
 **단점**
 1. NSObject를 상속해야한다 -> Objective-C 런타임에 의존하게 된다.
 
 ## willSet, didSet과 KVO의 차의
 - 우리가 타입을 만드는 경우에는 자유롭게 willSet, didSet을 구현할 수 있지만, 다른 사람 혹은 외부 라이브러리에서 정의한 타입이라면 내부 소스를 마음대로 변경 할 수 없기 때문에 이런 경우에 그타입의 프로퍼티 등의 값이 변경되는지 KVO 방식으로 변화를 관찰할 수 있다.
