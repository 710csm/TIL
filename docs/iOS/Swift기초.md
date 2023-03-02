# Swift 기초

## for문
1. 일반적인 사용법

```swift
let arr = [1,2,3,4,5,6,7,8,9,10]

for i in arr {
    if i % 2 == 0 {
        print(i)
    }
}
```

2. where 문을 사용했을 때

```swift
for i in arr where i % 2 == 0 {
    print(i)
}
```

- depth가 깊지 않게 코드를 작성할 수 있다(가독성이 좋아진다) 

3. forEach

```swift
arr.forEach {
    print($0)
}
```
- closure 방식

4. enumerate

```swift
let dict = [
    [1: "apple"],
    [2: "banana"],
    [3: "grape"],
    [4: "watermelon"],
]

for (key, value) in dict.enumerated() {
    print("key: \(key)")
    print("value: \(value)")
}
```

---

## 조건문
1. 스위프트에서는 조건문의 괄호를 제거해도 된다(권장)

```swift
// 1번
if (flag) {
    print("true")
}
// 2번
if flag {
    print("true")
}

// 3번
if (flag && (num1 == num2)) {
    print("true")
}
// 4번
if flag && (num1 == num2) {
    print("true")
}
```
- 여러개의 조건을 썻을 때 가독성이 좋아진다

2. ,(콤마)로 조건문 표현

```swift
// 1번
if flag1 && flag2 {...}

// 2번
if flag1, flag2 {...}
```
- 두 조건문의 의미는 똑같지만 2번 조건문은 앞에 조건(flag1)이 false일 경우 뒤에 있는 조건문은 건너뛸 수 있다.(속도 향상)

---

## switch문
1. 조건문과 똑같이 괄호 생략 가능

```swift
// 1번
switch (value) {
case pattern:
    code
default:
    code
}

// 2번
switch value {
case pattern:
    code
default:
    code
}
```

2. break를 안적어도 된다

```swift
switch grade {
case "A":
    print("A")
    break
case "B":
    print("B")
    break
case "C":
    print("C")
    break
default: 
    print("D")
    break
}

switch grade {
case "A":
    print("A")
case "B":
    print("B")
case "C":
    print("C")
default: 
    print("D")
}
```

3. case에 여러 패턴을 명시할 수 있다

```swift
switch grade {
case "A", "B":
    print("A, B")
case "C", "D", "E", "F":
    print("C, D, E, F")
default:
    break
}
```

4. if 문과 비교

```swift
if grade == "A" {
    print("A")
} else if grade == "B" {
    print("B")
} else if grade == "C" {
    print("C")
} else {
    print("D")
}
    
switch grade {
case "A":
    print("A")
case "B":
    print("B")
case "C":
    print("C")
```
- 조건이 3개 이상부터는  switch문의 성능이 더  좋다

---

## 배열
```swift
// 1번
let arr1 = Array<String>()
// 2번
let arr2 = [String]()
```
- 2번 코드가 더 권장되는 코드

---

## guard let, if let
- optional 값을 사용할 때 안전하게 사용할 수 있도록 해준다

```swift
var value: Int?
value = 1

// 1번
func test1() {
    print("value: \(value!)")
    
    print("value: \(value ?? 0)")
}

// 2번 guard let
func test2() {
    guard let value = value else {
        print("value is empty")
        return
    }
    print(value)
}

// 3번 if let
func test3() {
    if let value = value {
        print(value)
    } else {
        print("value is empty")
    }
}
```
- 1번 방법과 같이 사용하면 컴파일 에러가 날 수 있다.
- 코드에 !가 많으면 좋지 않다.
- guard let 을 사용할 경우 if 와 다르게 괄호 밖에서도 값을 사용할 수 있다.

---

## _의 의미
1. 값을 받아서 사용하지 않을 때 사용

```swift
guard let _ = value else {
    return
}

let _ = sumNum(1,2)
```

2. 함수 호출시 파라미터의 이름을 생략할 때 사용

```swift
// 1번
func underbarTest(name: String, age: Int) {
    print("name: \(name), age: \(age)")
}
underbarTest(name: "Example", age: 10)

// 2번
func underbarTest(_ name: String, _ age: Int) {
    print("name: \(name), age: \(age)")
}
underbarTest("Example", 10)
```

---

## 함수
1. 여러개의 값을 리턴할 수 있다.

```swift
func printAvg(num1: Int, num2: Int, num3: Int) -> (total: Int, avg: Int) {
    let total = num1 + num2 + num3
    let avg = total/3
    return (total, avg)
}
```

2. 여러개의 인자를 받을 수 있다.

```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
```

3. 읽는 방법
- 함수타입은 일반적으로 right-associative 
- Int -> Int -> Int == Int -> (Int -> Int)

```Swift
func stock(_ money: Int) -> Int {
    return money - 1
}

func bitcoin(_ money: Int) -> Int {
    return money - 2
}

func investment(highRisk: Bool) -> (Int) -> Int {
    return highRisk ? bitcoin : stock
}

let myChoice = investment(highRisk: true)
let currentMoney = 3
print(myChoice(currentMoney))
```

---

## 프로퍼티 get, set, didSet, willSet

### 1. 기본적인 사용법
```Swift
public var someValue: Int {
    get {
        return someValue
    }
    set(newValue) {
        someValue = newValue
    }
}
```
<img width="777" alt="스크린샷 2021-05-08 오후 6 09 14" src="https://user-images.githubusercontent.com/45002556/117533572-81ee7180-b028-11eb-9076-757c57e14e1c.png">

- 위와 같이 경고나 표시되는것을 확인할 수 있다.

### 2. 올바른 사용법
```Swift
private var realValue: Int = 0
public var tempValue: Int {
    get {
        return realValue
    }
    set(newValue) {
        realValue = newValue
    }
}
```
- 프로퍼티에 값이 할당 될 때 적절한 값인지 검증하기 위해
- 다른 프로퍼티 값에 의존적인 프로퍼티를 관리할 때
- 프로퍼티를 private하게 사용하기 위해

### 3. 응용
```Swift
private var realValue: Int = 0
public var tempValue: Int {
    get {
        return realValue
    }
    set(newValue) {
        if newValue < 1 {
            print("값은 1보다 작을 수 없습니다.")
        } else {
            realValue = newValue
        }
    }
}
```

### 4. didSet, willSet
- 스위프트는 프로퍼티 옵저버로 didSet, willSet을 제공한다. 값이 변경되기 직전, 직후를 감지
- 따라서 이때 원하는 작업을 수행 할 수 있습니다. 기본적인 syntax는 다음과 같다.

```Swift
var someValue: Int = 10 {
   didSet(oldVal) {
      //someValue의 값이 변경된 직후에 호출, oldVal은 변경 전 someValue의 값
   }
   willSet(newVal) {
      //someValue의 값이 변경되기 직전에 호출, newVal은 변경 될 새로운 값
   }
}
```
### 5. 응용

```Swift
var score: Int = 0 {
   didSet {
      scoreLabel.text = "Score: \(score)"
   }
```
- 값이 변경되고 ui에 변경된 값을 적용할 때 사용할 수 있다.

---

## class func vs static func

1. 두개 다 똑같이 객체를 생성하지 않고 호출할 수 있다.

```swift
class MyClass {
    static func test1() {
        print("static func")
    }
    
    class func test2() {
        print("class func")
    }
}

class SubClass {
    func printSomething() {
        MyClass.test1()
        MyClass.test2()
    }
}
```


2. 상속에서의 차이

<img width="772" alt="스크린샷 2021-05-08 오후 5 24 19" src="https://user-images.githubusercontent.com/45002556/117532377-3a64e700-b022-11eb-918c-e433281e517a.png">

---

## Closure
### 1. 기본적인 Closure

```Swift
{ (매개변수 목록) -> 반환타입 in
    실행 코드
}

let sum: (Int, Int) -> Int = { (num1: Int, num2: Int) in
    return num1 + num2
}

let sumResult: Int = sum(1, 2)
print(sumResult)
```

### 2. Trailing Closures
- Closure 표현식을 함수의 인자들 중 마지막 인자로 호출하여 넘길때 사용한다. 
- 마지막 인자 라벨은 사용하지 않는다. Closure 표현식이 길 때 유용하다.
- 함수에서 여러 후행 클로저가 올때, 첫번째 인자 라벨은 생략하지만, 나머지 클로저들은 인자라벨을 남겨둔다

```Swift
func loadPicture(from server: Server, completion: (Picture) -> Void, onFailure: () -> Void) {
    if let picture = download("photo.jpg", from: server) { 
        completion(picture)
    } else {
        onFailure() 
    }
}

loadPicture(from: someServer) { picture in 
    someView.currentPicture = picture
} onFailure: {
    print("Couldn't download the next picture.") ß
}
```

- 함수의 파라미터가 클로저 1개만 제공 되면 함수 뒤의 괄호를 생략할 수 있음

```Swift
func someClosure(completion: () -> Void) {
    print("someClosure")
}

someClosure {
    // do somethins
}
```

### 3. Escaping Closures
- 클로저를 함수의 파라미터로 넣을 수 있는데, 함수가 끝나고 실행되는 클로저이다.
- 비동기로 실행되거나 completionHandler로 사용되는 클로저는 파라미터 타입 앞에 @escaping이라는 키워드를 명시해야 한다.
- @escaping 를 사용하는 클로저에서는 self를 명시적으로 언급해야 한다.

```Swift
class SomeClass {
    var x = 10
    
    func doSomething() {
        // 클로저 안의 self가 class의 instance를 참조하면 강한 참조 사이클을 유발할 수 있다.
        withEscapingClosure {
            self.x = 100
        }
        // self implicity
        withNonescapingClosure {
            x = 200
        }
    }
}
```

- 위 함수에서 인자로 전달된 completionHandler는 someFunctionWithEscapingClosure 함수가 끝나고 나중에 처리 된다. 만약 함수가 끝나고 실행되는 클로저에 @escaping 키워드를 붙이지 않으면 컴파일시 오류가 발생.

---

## weak와 strong에 대한 설명
- strong(강한 참조): strong은 객체를 소유하여 레퍼런스 카운터가 증가하는 프로퍼티이다. 값 지정 시점에 retain이 되고 참조가 종료되는 시점에 release가 된다. 객체에 강한 참조가 남아 있는한 해당 객체는 메모리가 해제되지않는다.
- weak(약한 참조): weak는 객체를 소유하지 않고 주소값만을 가지고 있는 포인터 개념이다. 자신이 참조는 하지만 weak 메모리를 해제시킬 수 있는 권한은 다른 클래스에 있다. 값 지정시 리테인이 발생하지 않는다. 따라서 릴리즈도 발생하지 않는다. 그래서 언제 어떻게 메모리가 해제되는지 알 수 없다. 다만 메모리가 해제될 경우 자동으로 레퍼런스가 nil 로 초기화를 해준다. 그렇기 때문에 weak 속성을 사용하는 객체는 항상 옵셔널 타입이다.
- owned: 객체가 할당될 때 레퍼런스 카운터를 증가시키지 않는다. 그러나 Non-Optional 타입으로 선언되어야 하며, 객체가 ARC에 의해 메모리가 해제가 되더라도, 해당 객체 값을 존재하는 것으로 인지하며, 해당 객체에 액세스 할 경우 런타임 오류를 발생시킨다. 객체의 라이프 사이클이 명확하고 개발자의 의해 제어 기능이 명확한 경우, weak Optional 타입 대신 사용하여 좀 더 간결한 코딩이 가능하다.

---

## Escaping Closure의 개념
 메소드 파라미터로 전달 받은 closure를 메소드의 라이프 사이클 내에서 실행하여 끝내지 않고, 메소드 scope의 외부에 전달하려 할 때는 해당 closure를 escaping 해야한다. 해당 메소드의 호출이 끝난 이후에도 closure는 메모리 어딘가에 저장되어야 하며, 이는 closure 안에서 사용된 outer object(self와 같은)에 weak와 같은 레퍼런스 타입을 사용해야 할 수 있음을 주의 하도록 한다. escaping이 명시되어 있지 않으면 기본적으로 non-escaping이며, 이는 메소드의 실행이 끝나기 전에 closure의 사용이 모두 완료됨을 보장한다. 따라서 closure 내에서 weak를 굳이 사용하지 않아도 안전할 수 있음을 의미하기도 한다.

---

## 타입 캐스팅을 할 때 사용하는 키워드인 as, as?, as! 이 셋의 차이는 무엇인가요?
- as: 컴파일러가 타입 변환의 성공을 보장. 컴파일 타임에 가능/불가능 여부를 알 수 있음
- as?: 타입 변환에 실패하는 경우 nil을 리턴. 컴파일 타임에 가능/불가능 여부를 알 수 없음
- as!: 타입 변환에 실패하는 경우 실행시간(Runtime) 오류를 발생시킨다. 가능/불가능 여부를 알 수 없음

---

## Swift에서 Class와 struct의 차이는 무엇인가요?
**공통점**
- 값을 저장할 프로퍼티를 선언할 수 있습니다.
- 함수적 기능을 하는 메소드를 선언할 수 있습니다.
- 내부 값에 "."을 사용하여 접근할 수 있습니다.
- 생성자를 사용해 초기 기능을 확장할 수 있습니다.
- extension을 사용하여 기능을 확장할 수 있습니다.
- protocol을 채택하여 기능을 설정할 수 있습니다.

**Class-Referentce type**
- 참조 타입이다.
- 상속이 가능가능하다.
- 대입 연산 시 레퍼런스가 복사되어 할당된다.
- 같은 클래스 인스턴스를 여러 개의 변수에 할당한 뒤 값을 변경시키면 할당한 모든 변수에 영향을 준다.
- 타입 캐스팅을 통해 런타임에서 클래스 인스턴스의 타입을 확인할 수 있다.
- 객체화 시 힙 메모리 영역에 저장되며 ARC로 객체의 메모리 해제가 관리된다.
- 또한 힙 영역은 한 프로세스의 스레드들이 함께 공유하는 영역이기 때문에 Thread-Safe를 보장하지 않는다.

```Swift
class Person {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

var jack = Person(name: "Jack")
var emma = jack
ema.name = "emma"

print(jack.name)
print(emma.name)

// 결과
emma
emma
```

**Struct-Value type**
- 대입 연산시 값 자체가 복제되어 할당됨(공유가 불가)
- 구조체 변수를 새로운 변수에 할당할 때마다 새로운 구조체가 할당됩니다.
- 즉 같은 구조체를 여러개의 변수에 할당한 뒤 값을 변경시키더라도 다른 변수에 영향을 주지 않는다.
- 메모리의 스택 영역을 사용한다. 스택 영역은 각 스레드가 독립적으로 사용하기 때문에 Thread-Safe 하다는 특징이 있다.
- 상속이 불가능 (프로토콜은 사용 가능)

```Swift
struct Person {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

var jack = Person(name: "Jack")
var emma = jack
ema.name = "emma"

print(jack.name)
print(emma.name)

// 결과
jack
emma
```

**구조체를 사용해야 하는 경우**
- 연관된 간단한 값의 집합을 캡슐화 하는 것만이 목적일 때
- 캡슐화한 값을 참조하는 것보다 복사하는 것이 합당할 때
- 다른 타입으로부터 상속받거나 자신을 상속할 필요가 없을 때
- identyfy를 제어하지 않을 때 구조체를 사용

---

## Swift - Codable

- Codable protocol은 Json, plist 등으로 이루어진 데이터를 편리하게 객체로 변환해주는 protocol입니다.


## Type Inference vs Type Annotaion
1. Type Inference
- 선언과 동시에 초기화를 통해 컴파일러가 초기화된 값으로 타입을 추론하는것
- 하지만 원하는 타입으로 지정되지 않을 수 있다

```Swift
let str = "abcedf"  // 컴파일이 String으로 타입을 지정
let num1 = 123      // 컴파일이 Int로 타입을 지정

let num2 = 15.0     // Float이 아닌 Double로 타입 지정, Float으로 지정하고 싶을때는 문제가 발생
```

2. Type Annotation
- 직접 자료형을 지정, 초기값이 없어도 된다

```Swift
var str: String
var num: Float
```
