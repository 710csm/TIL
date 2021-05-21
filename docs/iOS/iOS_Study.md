# iOS 스터디

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

## Performance of Map, Filter, Reduce vs for-in loop in Swift
### 1. for 문과 map의 성능 비교

```swift
// Map
let celsius = fahrenheit.map { (degreesFahrenheit) -> Double in
    return (degreesFahrenheit - 32.0) / 1.8
}

// For-in loop
var celsius = [Double]()
for degreesFahrenheit in fahrenheit {
    celsius.append((degreesFahrenheit - 32.0) / 1.8)
}
```

<img width="618" alt="스크린샷 2021-05-08 오후 4 54 58" src="https://user-images.githubusercontent.com/45002556/117531594-21f2cd80-b01e-11eb-907e-0ee25c6ecf52.png">
- map의 성능이 더 좋은것을 알 수 있다.

### 2. for 문과 filter의 성능 비교

```swift
// filter
let colds = fahrenheit.filter { (degreesFahrenheit) -> Bool in
    return degreesFahrenheit <= 68.0
}

// For-in loop
var colds = [Double]()     
for degreesFahrenheit in fahrenheit {
    if degreesFahrenheit <= 68.0 {
        colds.append(degreesFahrenheit)
    }
}
```
<img width="632" alt="스크린샷 2021-05-08 오후 4 56 50" src="https://user-images.githubusercontent.com/45002556/117531630-641c0f00-b01e-11eb-9764-e9ac5fdc1aa7.png">
- filter의 성능이 좀 더 좋은것을 알 수 있다.

### 3. for 문과 reduce의 성능 비교

```Swift
// reduce
let sum = fahrenheit.reduce(0.0) { (result, degreesFahrenheit) -> Double in
    return result + degreesFahrenheit
}


// For-in loop
var sum: Double = 0.0
        
for degreesFahrenheit in fahrenheit {
    sum += degreesFahrenheit
}
```

<img width="632" alt="스크린샷 2021-05-08 오후 4 59 44" src="https://user-images.githubusercontent.com/45002556/117531706-cb39c380-b01e-11eb-92d9-493c55185afe.png">
- reduce의 성능은 for문보다 좋지 않다

### 4. 함수 chaining (map + filter + reduce)의 성능

```Swift
// map + filter + reduce
let sum = fahrenheit.map({ (degreesFahrenheit) -> Double in
    return (degreesFahrenheit - 32.0) / 1.8
}).filter({ (degreesCelsius) -> Bool in
    return degreesCelsius <= 20.0
}).reduce(0.0) { (result, degreesCelsius) -> Double in
    return result + degreesCelsius
}

// For-in loop
var sum: Double = 0.0
        
for degreesFahrenheit in fahrenheit {
    let degreesCelsius = (degreesFahrenheit - 32.0) / 1.8
    if degreesCelsius <= 20.0 {
        sum += degreesCelsius
    }
}
```

<img width="649" alt="스크린샷 2021-05-08 오후 5 03 43" src="https://user-images.githubusercontent.com/45002556/117531790-5a46db80-b01f-11eb-8027-04344d81f0ed.png">

- for 문이 성능이 더 좋다
- 이전 함수의 각 결과에 대해 반복돼서 for 문보다 성능이 좋지 않다.
- map의 시간 복잡도 = O(n), filter의 시간 복잡도 = O(n), reduce의 시간 복잡도 = O(n)이기 때문에 3개의 시간 복잡도는 O(3n)

### 5. RxSwift에서의 함수 Chaining 성능

```Swift
Observable.from(fahrenheit)
    .map({ (degreesFahrenheit) -> Double in
        return (degreesFahrenheit - 32.0) / 1.8
    })
    .filter({ (degreesCelsius) -> Bool in
        return degreesCelsius <= 20.0
    })
    .reduce(0.0, accumulator: ({ (result, degreesCelsius) -> Double in
        return result + degreesCelsius
    }))
    .subscribe(onNext: { sum in
        print(sum)
    })
    .disposed(by: disposeBag)
```

<img width="635" alt="스크린샷 2021-05-08 오후 5 12 05" src="https://user-images.githubusercontent.com/45002556/117532044-857dfa80-b020-11eb-8686-8961b8ed675e.png">
- 성능이 매우 좋지 않다는것을 확인할 수 있다.

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
- - @escaping 를 사용하는 클로저에서는 self를 명시적으로 언급해야 한다.
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

## Xib
- iOS 앱에서 UI를 만드는 방법중 하나
- segue 방식을 사용하지 않을때 사용
- 화면을 재사용 할 수 있다

### 사용법
1. xib파일 생성
<img width="507" alt="스크린샷 2021-05-16 오후 5 35 56" src="https://user-images.githubusercontent.com/45002556/118391030-3ada2f00-b66d-11eb-8cd7-df88dcb3de71.png">
- Also create XIB file 체크

2. 사용할 cell을 Register 시킨다

```Swift
tableView.register(
    UINib(nibName: UserTableViewCell.identifier, bundle: nil),
    forCellReuseIdentifier: UserTableViewCell.identifier
)

tableView.register(
    UINib(nibName: DeviceTableViewCell.identifier, bundle: nil),
    forCellReuseIdentifier: DeviceTableViewCell.identifier
)
```

3. identifier 값 설정    
<img width="262" alt="스크린샷 2021-05-16 오후 5 49 07" src="https://user-images.githubusercontent.com/45002556/118391393-06677280-b66f-11eb-8b9c-70b2416110e2.png">


4. 테이블 뷰에서 설정
- 기존의 방법과 같은 방식

```Swift
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    switch indexPath {
    case IndexPath(row: 0, section: 0):
        let cell = tableView.dequeueReusableCell(
            withIdentifier: UserTableViewCell.identifier,
            for: indexPath
        ) as UserTableViewCell
        // do something
        return cell
    case IndexPath(row: 0, section: 1):
        let cell = tableView.dequeueReusableCell(
            withIdentifier: DeviceTableViewCell.identifier,
            for: indexPath
        ) as DeviceTableViewCell
        // do something
        return cell
    default: 
        UITableViewCell()
    }
}
```

---

## ViewController의 생명주기

![viewLifeCycle](https://user-images.githubusercontent.com/45002556/108618534-7399ac00-7462-11eb-9753-2c66dca88bd9.png)

**loadView**
- 컨트롤러가 관리하는 뷰를 만든다. 뷰컨트롤러가 생성되고 순차적으로 완성됐을 때만 호출된다.

**viewDidLoad**
- 컨트롤러의 뷰가 메모리에 올라간 뒤에 호출된다. 뷰가 생성될 때만 호출된다.

**viewWillAppear**
- 화면에 뷰가 표시될 때마다 호출된다. 이 단계는 뷰는 정의된 바운드를 가지고 있지만 화면. 회전은 적용되지 않는다.

**viewWillLayoutSubviews**
- 뷰컨트롤러에게 그 자식뷰의 레이아웃을 조정하는 것에 대한 것을 알려주기 위해 호출된다. 이 메소드는 frame이 바뀔때마다 호출된다.

**viewDidLayoutSubview**
- 뷰가 그 자식 뷰의 레이아웃에 영향을 준 것을 뷰컨트롤러에게 알려주기 위해 허출된다. 뷰가 그 잣긱 view의 레이아웃을 바꾸고난 뒤에 추가적인 변경을 하고 싶을때 사용하는 이벤트 함수

**viewDidAppear**
- 뷰가 나타났다는 것을 컨트롤러에게 알리는 역할을 한다. 호출되는 시점으로는 뷰가 화면에 나타난 직후에 실행된다.

**viewWillDisAppear**
- 뷰가 사라지기 직전에 호출되는 함수이다. 뷰가 삭제 되려고 하고있는 것을 viewController에게 알린다.

**viewDidDisappear**
- ViewController에게 view가 제거되었음을 알린다. 호출 시점은 viewWillDisAppear 다음에 호출된다.

---

## MVC와 MVVM

### MVC
- MVC 패턴은 Model + View + Controller를 합친 용어입니다.   

1. 구조    
![f1](https://user-images.githubusercontent.com/45002556/108619957-e0fe0a80-746b-11eb-8afa-692ce309c4e1.png)
    - Model: 애플리케이션에서 사용되는 데이터와 그 데이터를 처리하는 부분
    - View: 사용자에서 보여지는 UI 부분입니다
    - Controller: 사용자의 입력을 받고 처리하는 부분     
2. 동작
    - 사용자의 action들은 controller에 들어오게 됩니다.
    - controller는 사용자의 action을 확인하고, model을 업데이트 합니다.
    - controller는 model을 나타낼 view를 선택합니다.
    - view는 model을 이용하여 화면을 나타냅니다
3. 특징
    - controller는 여러개의 view를 선택할 수 있는 1:n 구조입니다. controller는 view를 선택할 뿐 직접 업데이트 하지 않습니다.(view는 controller를 알지 못한다)
4. 장점
    - MVC 패턴의 장점은 널리 사용되고 있는 패턴이라는 점에 걸맞게 가장 단순합니다. 단순하다 보니 보편적으로 많이 사용되는 디자인 패턴입니다.
5. 단점
    - MVC 패턴의 단점은 view와 model 사이의 의존성이 높다는 것입니다. view와 model의 높은 의존성은 어플리케이션이 커질수록 복잡해지고 유지보수가 어렵게 만들 수 있습니다.

### MVVM
- MVVM 패턴은 Model + View + View Model을 합친 용어입니다. Model과 View는 다른 패턴과 동일합니다.

1. 구조
![f3](https://user-images.githubusercontent.com/45002556/108619960-e22f3780-746b-11eb-9c30-a6e35fec36ad.png)

    - Model: 어플리케이션에서 사용되는 데이터와 그 데이터를 처리하는 부분입니다.
    - View: 사용자에서 보여지는 UI 부분입니다.
    - View Model: View를 표현하기 위해 만든 Model입니다. View를 나타내 주기 위한 Model이자 View를 나타내기 위한 데이터 처리를 하는 부분입니다.
2. 동작
    - 사용자의 action들은 view를 통해 들어오게 됩니다.
    - view에 action이 들어오면, command 패턴으로 view model에 action을 전달합니다.
    - View model은 model에게 데이터를 요청합니다.
    - model은 view model에게 요청받은 데이터를 응답합니다.
    - View model은 응답 받은 데이터를 가공하여 저장합니다.
    - view는 view model과 Data Binding하여 화면을 나타냅니다.
3. 특징
    - MVVM 패턴은 Command 패턴과 Data Binding 두 가지 패턴을 사용하여 구현되었습니다.
    - Command 패턴과 Data Binding을 이용하여 view와 view model 사이의 의존성을 없앴습니다.
    - View model과 view는 1:n 관계입니다.
4. 장점
    - MVVM 패턴은 view와 model 사이의 의존성이 없습니다. 또한 Command 패턴과 Data Binding을 사용하여 view와 view model 사이의 의존성 또한 없앤 디자인 패턴입니다. 각각의 부분은 독립적이기 때문에 모듈화 화여 개발할 수 있습니다.
5. 단점
    - MVVM 패턴의 단점은 view model의 설계가 쉽지 않다는 점입니다.
    
### 차이점
**MVC**
- view controller
![스크린샷 2021-05-17 오후 10 04 10](https://user-images.githubusercontent.com/45002556/118493251-dea20880-b75b-11eb-915a-ad1c55d71160.png)

**MVVM**
- View
![스크린샷 2021-05-17 오후 9 47 13](https://user-images.githubusercontent.com/45002556/118490973-7fdb8f80-b759-11eb-93d9-722870b71414.png)
- ViewModel
![스크린샷 2021-05-17 오후 9 46 38](https://user-images.githubusercontent.com/45002556/118490985-823de980-b759-11eb-99ee-badf4cc8da11.png)

---

## Layout 구현
- 코드로 AutoLayout을 구현할 때 알아보기가 힘들고 유지보수에 어려움이 있다
- SnapKit, FlexLayout과 같은 라이브러리를 사용해서 보기에 좋고 성능도 더 좋게 layout을 구현할 수 있다.
- 협업을 할 때 FlexLayout과 같은 새로운 기술을 도입할때는 러닝커브가 있어서 주의가 필요하다  (FlexLayout, Texture 등 yoga 관련 프레임워크)

### AutoLayout

<img width="833" alt="스크린샷 2021-05-19 오후 6 48 36" src="https://user-images.githubusercontent.com/45002556/118792787-f2bc4600-b8d2-11eb-8b55-1e413af64001.png">

### SnapKit

<img width="515" alt="스크린샷 2021-05-19 오후 6 48 51" src="https://user-images.githubusercontent.com/45002556/118792804-f64fcd00-b8d2-11eb-8984-83656cf3102e.png">

### FlexLayout

<img width="815" alt="스크린샷 2021-05-19 오후 6 49 04" src="https://user-images.githubusercontent.com/45002556/118792810-f6e86380-b8d2-11eb-8013-0b6a5d5fe231.png">


