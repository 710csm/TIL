# Quick + Nimble   
- Unit test 라이브러리로 많이 쓰이는 Quick과 Nimble이 있다.      
- Quick은 BDD(Behavior-Driven Development)를 위한 프레임워크이다. description과 함께 클로저로 블록단위 묶음으로 코딩하게끔 하여 가독성 상승을 위해서 사용한다.
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

## Quick, Nimble 설치 방법
1. CocoaPods

```
pod 'Nimble'
pod 'Quick'

// then
pod install
```

2. 또는 Swift Package Manager로도 설치를 할 수 있다.

## Quick 사용법
- 기존의 XCTestCase 상속을 제거하고 QuickSpec을 상속받는다.

```swift
class MyProjectTests: QuickSpec {

}
```

- 이후 기존에 생성된 메소드를 모드 지우고 spec 메소드를 작성한다.

```swift
override func spec() { ... }
```

- describe 작성, describe란 BDD에서 Given을 의미한다. 즉 어떤 환경에 있는지를 정의
- beforeEach 작성, beforeEach 블록은 테스트가 실행되기 전에 미리 환경을 설정하는 것, 기존 XCTest의 setUpWithError과 비슷한 것
- context 작성, context는 BDD에서 When을 의미한다. 즉 어떤 행위가 일어날 때를 정의
- it 작성, it은 BDD에서 Then을 의미한다. 즉 예상되는 결과를 정의  

```swift
override func spec() { 
var subject: SomeViewController!
           
describe("특정 뷰컨트롤러에서") {
    beforeEach {
        subject = UIStoryboard(
                name: "Main", 
                bundle: nil
            ).instantiateViewController(
                withIdentifier: "SomeViewController"
            ) as? SomeViewController   
                _ = subject.view
    }
               
    context("뷰가 로드 될 때") {
        it("어떤 작업이 진행돼서 특정 결과가 나와야한다.") {
            expect(subject.table.numberOfRows(inSection: 0)).to(equal(1))
        }
    }
}
```

## Nimble 사용법
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

## Quick git 주소
https://github.com/Quick/Quick

## 참고
https://academy.realm.io/kr/posts/ios-tdd-test-driven-development/   
https://m.blog.naver.com/suresofttech/221569611618   
