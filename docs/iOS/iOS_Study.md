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
