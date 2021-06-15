# Nimble   
- Unit test 라이브러리로 많이 쓰이는 Quick과 Nimble이 있다.      
- Nimble은 다양한 assertion을 제공해주고 가독성 좋은 에러 메시지를 제공해준다.      
<img width="880" alt="스크린샷 2021-03-12 오후 1 35 32" src="https://user-images.githubusercontent.com/45002556/110892706-e5656700-8337-11eb-89ad-1dc9a8ee6931.png">

## TDD(Test-driven-development)란?
테스트 주도 개발(Test-driven-development)은 매우 짧은 개발 사이클을 반복하는 소프트웨어 개발 프로세스 중 하나이다.    
우선 개발자는 바라는 향상 또는 새로운 함수를 정의하는 자동화된 테스트 케이스를 작성한다.     
그 후에, 케이스를 통과하기 위한 최소한의 양의 코드를 생성한다. 그리고 마지막으로 그 새 코드를 표준에 맞도록 리팩토링한다.

<img width="733" alt="스크린샷 2021-06-15 오전 9 22 42" src="https://user-images.githubusercontent.com/45002556/121974847-44190180-cdbb-11eb-9674-a86db16571cd.png">

## TDD의 장점
1. 객체지향적인 코드 개발
2. 설계 수정 시간의 단축
3. 디버깅 시간의 단축
4. 유지 보수의 용이성
5. 테스트 문서의 대체 가능

## TDD의 단점
1. 프로토타이핑을 빠른 시간 안에 마치고 결과를 보여줘야 하는 상황에는 부적합
2. 익숙해지는데 시간이 걸린다

## 설치 방법
```swift
// CocoaPods
pod 'Nimble'

// then
pod install
```

## 사용법
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

- Type, class형에 관한 테스트

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

## Nimble git 주소
https://github.com/Quick/Nimble

## 참고
https://academy.realm.io/kr/posts/ios-tdd-test-driven-development/   
https://m.blog.naver.com/suresofttech/221569611618   
