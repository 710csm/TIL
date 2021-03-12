# Nimble   
- Unit test 라이브러리로 많이 쓰이는 Quick과 Nimble이 있다.      
- Nimble은 다양한 assertion을 제공해주고 가독성 좋은 에러 메시지를 제공해준다.      
<img width="880" alt="스크린샷 2021-03-12 오후 1 35 32" src="https://user-images.githubusercontent.com/45002556/110892706-e5656700-8337-11eb-89ad-1dc9a8ee6931.png">

## 사용법
```swift
pod 'Nimble'
```

- XCTAssert가 아닌 expect().to() 로 더 간결한 사용
```swift
func testExample() thorws {
    let result = convert?.starPrint(number: 1)
    // XCTAssertEqual(result, "1", "1에 대한 테스트 실패")
    expect(result).to(contain("1"))
}
```    

- description 사용 가능
```swift
func testExample() thorws {
    expect(1+1).to(equal(3), description: "failed - 덧셈 기능을 테스트")
}
```
<img width="466" alt="스크린샷 2021-03-12 오후 1 40 18" src="https://user-images.githubusercontent.com/45002556/110893003-7fc5aa80-8338-11eb-8aae-553d830ce1eb.png">     

- async 테스트 - toEventually, toEventuallyNot      
```swift
DispatchQueue.main.async {
    ocean.add("dolphins")
    ocean.add("wahles")
}
expect(ocean).toEventually(contain("dolphins", "wales"))
```     

- time out에 관한 테스트
```swift
// Waits three seconds for ocean to contain "starfish":
expect(ocean).toEventually(contain("starfish"), timeout: .seconds(3))   
// Evaluate someValue every 0.2 seconds repeatedly until it equals 100, or fails if it timeouts after 5.5 seconds.
expect(someValue).toEventually(equal(100), timeout: .milliseconds(5500), pollInterval: .milliseconds(200))
```

- Type, clas형에 관한 테스트
```swift
// passes if 'instance' is an instance of 'aClass':
expect(instance).to(beAnInstanceOf(aClass))
// passes if 'instance' is an instance of 'aClass' or any of its subckasses:
expect(instance).to(beAKindOf(aClass))
```

- true/false, nil 여부 테스트    
```swift
// passes if 'actual' is not nil, true, or an object with a boolean value of true:
expect(actual).to(beTruthy())
// passes if 'acual' is only true (not nil or an object conforming to Boolean true):
expect(actual).to(beTrue())
// passes if 'actual' is nil, false, or an object with a boolean value of false:
expect(actual).to(beFalsy())
// passes if 'actual' is only false (not nil or an object conforming to Boolean false):
expect(actual).to(beFalse())
// passes if 'actual' is nil:
expect(actual).to(beNil())
```
