# 고차함수

## 고차함수 (Map, Reduce, Filter)에 대해    
Swift에서 함수는 일급 시민으로 취급되기 때문에 다른 함수의 전달인자로 사용될 수 있다. 고차 함수란 매개 변수로 함수를 받는 함수를 말한다.

1. Map: 데이터를 변형하고자 할 때 사용된다. 기존 컨테이너의 값들은 변경되지 않고 새로운 컨테이너를 생성하여 반환한다.
    - 코드 재사용 용이 / 컴파일러 최적화 측면에서 성능이 좋다 / 다중 스레드 환경에서 하나의 컨테이너에 여러 스레드들이 동시에 변경을 하려고 할 때 예측하지 못한 결과 발생을 방지.
    - 배열의 값 하나하나에 함수를 적용할 때 사용

```Swift
let numbers = [0, 1, 2, 3, 4]

var doubleNumbers = [Int]()
var strings = [String]()

// for-in
for number in numbers {
    doubledNumbers.append(number * 2)
    strings.append("\(number)")
}

// map
doubledNumbers = numbers.map {$0 * 2}
strings = numbers.map {"\($0)"}
```

2. Filter: 컨테이너 내부의 값들을 걸러서 추출하고자 할 때 사용한다. Filter의 매개변수로 전달되는 함수의 반환 타입은 Bool이다. true라면 값을 포함하고 false면 배제하여, map과 마찬가지로 새로운 컨테이너를 생성하여 반환한다. 조건에 맞는 항목만 찾아내기

```Swift
let numbers = [0, 1, 2, 3, 4, 5]

let evens = numbers.filter {$0 % 2 == 0} // [0, 2, 4]
let odds = numbers.map {$ + 3}.filter {$0 % 2 != 0} // [3, 5, 7]
```

3. Reduce: 컨테이너 내부를 하나로 합쳐주는 기능을 한다. 정수 배열이라면 전달받은 함수의 연산 결과로 합쳐주고, 문자열 배열이라면 문자열을 하나로 합쳐준다. 첫 번째 매개변수를 통해 초기값을 지정할 수 있다. 이 초기값이 최초의 $0으로 사용된다. 컬렉션의 값들을 조건에 따라 하나의 값으로 만든다.

```Swift
array.reduce(초기값, combine: +)를 하면 배열의 모든 값이 더해진 하나의 값을 리턴

let numbers = [1, 2, 3]
var sum = numbers.reduce(10) {$0 + $1} // 16
```

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

## 고차함수2 (flatMap, CompactMap)
    
```Swift
let array = [1, nil, 3, nil, 5, 6, 7]
let flatMapTest = array.flatMap{ $0 }
let compactMapTest = array.compactMap { $0 }

print("flatMapTest :", flatMapTest)
print("compactMapTest :", compactMapTest)

<출력>
flatMapTest : [1, 3, 5, 6, 7]
compactMapTest : [1, 3, 5, 6, 7]
```

- 1차원 배열에서는 flatMap과 compactMap 둘다 똑같은 결과가 나온다.
- 스위프트 4.1부터는 두개를 구분해서 사용해야 한다.
- 2차원 배열에서는 둘다 nil을 제거하지 않고 1차원 배열일대만 nil 제거를 합니다.

### 1. compactMap
- 1차원 배열에서 nil을 제거하고 옵셔널 바인딩을 할 때 사용한다. 

### 2. flatMap
- 2차원 배열을 1차원 배열로 flatten하게 만들 때 사용한다.  
