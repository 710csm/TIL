# RxSwift
- 관찰 가능한 연속성 형태와 함수형태의 연산자를 이용해서 비동기&이벤트를 위한 코드로 구성하고 있는 라이브러리

## 비동기 프로그래밍이 필요할 때
1. 버튼을 눌렀을 때의 반응
2. 텍스트 필드에 포커스가 잡힌 경우
3. 인터넷에서 크기가 큰 이미지 파일을 받는 경우
4. 오디오를 실행하는 경우

## iOS 프로그래밍에서 UIKit을 통해 비동기가 필요할 때
1. NotificationCenter: 백그라운드 진입 후 몇초 있다가 메시지 알림
2. tableView의 didSelectRowAt과 같은 메소드
3. Closure

## RxSwift를 사용하는 이유
1. RxSwift없이 비동기적 측면에서 접근
    - 비동기 실행 코드를 이해하기 힘들다: 애플의 API에서 제공하는 delegate 패턴을 그대로 사용할 뿐, 그 코드 자체는 속에 내장되어 있다
    - 비동기 실행에 관한 명확한 추론을 하기가 힘들다
    
2. MVVM과 밀접한 연관
    - MVVM의 배경 데이터 바인딩을 제공하는 플렛폼에서 만들어진 이벤트 중심 프로그램을 위해 특별히 개발 RxSwift는 이와 연관성이 높다

## RxSwift의 필요성
1. 명시적으로 공유된 mutable state를 알기 위해서
2. Imperative Programming(명시적 코드)의 한계 극복
    - 명시적 코드: 일반적으로 작성했던 프로그래밍 방식 run(), getInstance()와 같은 컴퓨터에게 알려주는 방식
    - 사람이 명시적 코드를 통해 비동기적 앱을 구현할 때 어려움이 따른다, 아래의 코드와 같은 경우, 순서를 바꾸면 전혀 다른 메소드가 될 수 있다       
    
<img width="456" alt="스크린샷 2021-04-04 오후 4 42 30" src="https://user-images.githubusercontent.com/45002556/113502069-c0de6280-9564-11eb-87c3-fe942974abdf.png">
    
3. Side Effects를 통해 state를 파악
    - Side Effects는 코드의 현재 흐름에 대한 state를 표현
    - Side Effects가 발생한다면, 그 메소드가 실행되기 전의 작업ㅇ르 직접 지정해줄 수 있다
    
4. Declarative code(선언적 코드)
    - 절차지향 프로그램과 함수형 프로그램의 균형성을 갖춘 형태가 RxSwift, Declarative code는 이벤트가 일어날 때 그 데이터에 대한 대한 처리가 가능하며, for loop와 같은 작업 역시 가능
    - 절차지향 프로그램은 마음대로 상태를 변경하며, 함수형 프로그램은 side effect를 최소화 하는 코드를 지향
    
5. Reactive System
    - Responsive: 언제나 UI를 최신 상태로 유지
    - Resilient: 각 behavior들은 독립적이고 에러가 발생할 때 회복에 유연한 탄력적 특성
    - Elastic: 다양한 workload를 처리(데이터 모음, 자원 공유 등)
    - Message driven: components들은 메세지 기반으로 상호작용 하므로 재사용성의 장점과 lifecycle과 클래스의 구현을 분리

---

## Rx의 3요소
1. Observables<Data>    
    - 객체에 이벤트나 값 추가, 수정등과 같은 것을 가능하게끔 해준다.    

2.  Operators
    - 마치 연산자와 같이 "비동기들은 highly decoupled and composable" 옵저버에서 나온 결과를 Rx 연산자를 통해 입력 미치 출력이 가능 -> side effect 발생 가능
    
3. Schedulers
    - 기존에 사용하던 DispatchQueue와 동일한 기능
    
---

## RxCocoa
<img width="690" alt="스크린샷 2021-04-04 오후 4 55 15" src="https://user-images.githubusercontent.com/45002556/113502311-88d81f00-9566-11eb-97d7-55a8a496da98.png">

1. 개념
    - RxCocoa란 "UIKit 및 Cocoa 개발을 지원하는 클래스"를 보유하고 있는 RxSwift 동반 라이브러리 
    - 다양한 UI 구성요소에 대한 반응형 확장(reactive extensions) 기능을 추가하여 UI 이벤트를 추가 가능

---

# Observables
- Observable이란 여러 이벤트들을 생성(emit)할 수 있는 대상
- Observable == Observable sequence == sequence
- observable을 sequence라고 부르는 이유    

<img width="446" alt="스크린샷 2021-04-04 오후 4 59 31" src="https://user-images.githubusercontent.com/45002556/113502402-22073580-9567-11eb-93bc-31b8823f4d61.png">

1. next: 구성 요소를 계속해서 방출시킬 수 있는 기능(=observable 구독자에게 데이터 전달)
2. completed: 이벤트를 종료시킬 수 있는 기능 (=observable 구독자에게 완료되었음을 알림)
    - 그림에서 끝에 막대기가 세로로 있는 것(일이 이미 끝난 상태)
    
    <img width="453" alt="스크린샷 2021-04-04 오후 5 01 36" src="https://user-images.githubusercontent.com/45002556/113502463-6c88b200-9567-11eb-9406-f6724687c638.png">    
    
3. error: 이벤트에 오류가 있음을 알고 중간에 종료시킬 수 있는 기능 (observable 구독자에게 오류를 알림)
    - 이벤트에 오류를 포함하고 있는 경우 "X"로 표현 
    
    <img width="454" alt="스크린샷 2021-04-04 오후 5 03 02" src="https://user-images.githubusercontent.com/45002556/113502504-9f32aa80-9567-11eb-9d20-46e1f58eb6be.png">   

## Observable(sequence) 생성
1. Observable.just(): 오직 하나의 요소를 포함하는 "Sequence" 생성
- [one, two, three] 처럼 하나의 배열로도 삽입 가능    

```swift
// 1
let one = 1
let two = 2
let three = 3

// 2
let observable = Observable<Int>.just(one)
```

2. Obserbable.of(): 타입 추론을 이용하여 "Sequence" 생성
- 3개의 integer 값에 대한 sequence를 생성할 때

```swift
let observable = Observable.of(one, two, three)
```
- of() 로 단일요소 만들기 (.just()와 동일한 결과)

```swift
let observable_1 = Observable.of([one, two, three])
let observable_2 = Observable.just([one, two
```

3. Observable.from(): 오직 array 타입만 처리하며 각각 요소들을 하나씩 "emit"하는 기능

```swift
let observable = Observable.from([one, two, three])
```

4. Observable.create(): 클로저 형식이며 다양한(onNext, onCompleted, ...)을 생성할 수 있음 

```swift
let disposeBag = DisposeBag()
let observable = Observable<String>.create({ (observer) -> Disposable in
    observer.onNext("1")
    observer.onCompleted()
    observer.onNext("?")
    return Disposables.create()
})

observable.subscribe{(event) in
    print(event)
}

/* 
prints
next(1)
completed
*/
```

---

# Observable subscribing
- "옵저버에 대한 구독" 즉, 옵저버에 담긴 이벤트들을 방출(emit) 하는 것이 subscribe 메소드를 사용한는 것

1. observable.subscribe()
- 사전 작업: Observable에 요소 추가

```swift
let one = 1
let two = 2
let three = 3

let observable = Observable.of(one, two, three)
```

- 구독하기: subscribe

```swift
observable.subscribe { event in
  print(event)
  
  /*
  --- Example of: subscribe ---
    next(1)
    next(2)
    next(3)
    completed
  */
}
```

- .subscribe는 정수의 이벤트 객체를 파라미터로 하는 escaping 클로저 형식 메소드, 반환값은 Disposable

2. observable.subscribe(onNext:)
- next 요소만 처리한다는 의미

```swift
observable.subscribe(onNext: { element in
    print(element)
    
    /* prints
     1
     2
     3
     */
})
```

3. empty()로 설정된 Observable
- subscribe() 시, "completed"만 출력

```swift
let observable = Observable<Void>.empty()
observable.subscribe(
    onNext: { (element) in
        print(element)
},
onCompleted: {
    print("Completed")
})

/* Prints:
Completed
*/
```

4. never() 로 설정된 Observable
- subscribe() 시, "completed"도 출력되지 않음

```swift
let observable = Observable<Any>.never()
observable.subscribe( onNext: { (element) in
    print(element)
}, onCompleted: {
    print("Completed")
})
```

5. range()로 설정된 Observable
- 클로저에서 인수를 주목"(i) in "

```swift
let observable = Observable<Int>.range(start: 1, count: 10)
observable.subscribe(onNext: { (i) in
    let n = Double(i)
    let fibonacci = Int(((pow(1.61803, n) - pow(0.61803, n)) / 2.23606).rounded())
    print(fibonacci)
})
```

## disposing
- subscribe가 Observables안에 있는 이벤트들을 방출하는 것이라면, disposing은 subscribe를 취소하는 것

1. dispose()
- 지금은 Observable의 이벤트가 3개 있어서 3개 출력후 "completed"를 출력하지만, Observable의 이벤트가 무한대가 등록되어 있다면 일정 수준에서 dispose()에서 종료후 "completed" 출력

```swift
let observable = Observable.of("A", "B", "C")
let subscription = observable.subscribe({ (event) in
    print(event)
})
subscription.dispose()
```

2. disposeBag
- dispose에 대한 리턴값을 담는 객체

```swift    
let disposeBag = DisposeBag()
Observable.of("A", "B", "C").subscribe{
    print($0)
}
.disposed(by: disposeBag) // subscribe로부터 방출된 리턴 값을 disposeBag에 추가

print(disposeBag)
// prints : RxSwift.DisposeBag
```

3. Observable.create에서의 dispose
- completed는 작동하지 않음

```swift
enum MyError: Error {
     case anError
}
          
let disposeBag = DisposeBag()
Observable<String>.create({ (observer) -> Disposable in
    observer.onNext("1")
    observer.onError(MyError.anError)
    observer.onCompleted()
    observer.onNext("?")
    return Disposables.create()
}).subscribe(
    onNext: { print($0) },
    onError: { print($0) },
    onCompleted: { print("Completed") },
    onDisposed: { print("Disposed") }
).disposed(by: disposeBag) // 이 구문이 있는 이유 : 메로리 제거함으로써 메모리 효율확보
    
    
/* Prints:
1
anError
Disposed
*/
```
- 코드의 흐름: 동기가 아닌 비동기이므로 주의 

4. subscribe와 bind의 차이점
bind(onNext)는 에러를 emit하지 않고 item event에 관해서만 emit하게 된다.    
그래서 error / complete / disposed를 emit 하려면 subscrive(onNext: ...)를 써야한다.

---

# Operator
- Operator는 Observables에 적용할 수 있는 변환 함수들을 의미합니다. Operator를 이용하여 Observables를 조작하면서 데이터를 처리하고 변환할 수 있습니다.

## Operator의 종류

### Transforming Operators
- Observable을 다른 Observable로 변환하는 함수로, map, flatMap, concatMap, scan 등이 있습니다.
    - startWith: Observable 시퀀스 앞에 다른 값을 추가해주는 기능을 합니다.
    - concat: startWith와 매우 흡사한 것으로, startWith는 하나의 element가 맨 앞에 추가되지만 concat은 여러개의 element가 추가됩니다.
    - concatMap: flatMap과 concat이 합쳐진 것으로 flatMap과 다른점은 concatMap은 순서를 보장해줍니다.
    - flatMap: map과는 다르게 스트림에서 방출되는 아이템들을 다른 Observable로 만드는 역할을 하며 각각 만들어진 Observable 또한 아이템을 방출하게 됩니다.
    
    ```Swift
    let sequenceInt = Observable.of(1, 2, 3)    // Int 타입 시퀀스
    let sequenceString = Observable.of("A", "B", "C", "D")    // String 타입 시퀀스

    sequenceInt
        .flatMap { (x: Int) -> Observable<String> in
            print("Emit Int Item : \(x)")
            return sequenceString.map{ "\(x):\($0)"}
        }
        .subscribe(onNext: {
            self.arr.append($0)
        })

    print(arr)

    // Print
    Emit Int Item : 1
    Emit Int Item : 2
    Emit Int Item : 3
    ["1:A", "1:B", "2:A", "1:C", "2:B", "3:A", "1:D", "2:C", "3:B", "2:D", "3:C", "3:D"]
    ```
    
    - flatMap을 통해 변환되는 각각 Observable들을 평평하게 flat한 스트림을 생성하여 방출합니다.
    
    <img width="723" alt="스크린샷 2023-05-15 오전 10 27 22" src="https://github.com/710csm/TIL/assets/45002556/59ff70da-0a83-4d7a-8407-ee7cb1b52cf8">

### Filtering Operators
- Observable에서 특정 조건을 만족하는 항목만 걸러내는 함수로, filter, take, skip, distinctUntilChanged 등이 있습니다.

### Combining Operators 
- 두 개 이상의 Observable을 조합하는 함수로, merge, zip, combineLatest 등이 있습니다.
    - combineLatest: 최소 두개의 스트림에서 각각 하나씩 이벤트를 받아온 뒤부터 두개의 스트림 중 한쪽에서만 이벤트가 발생해도 새로운 스트림에 이벤트를 받아올 수 있습니다.
    
    <img width="510" alt="스크린샷 2023-05-15 오전 9 51 47" src="https://github.com/710csm/TIL/assets/45002556/2d68eea9-49a7-4526-a9e4-8ecb8000fb28">
    
    - merge: 두개의 Observable을 합쳐주는 연산자로 스트림을 합쳐서 하나의 스트림으로 만들어 줍니다. Observable의 이벤트가 모두 들어올 때까지 기다리지 않고 바로바로 하나씩 방출해줍니다. 연산자는 각 스트림의 이벤트에 접근할 수 없고 그냥 바로 반환되고 새로운 스트림을 만들어준다.
    
    <img width="465" alt="스크린샷 2023-05-15 오전 9 52 26" src="https://github.com/710csm/TIL/assets/45002556/6743bc96-ecda-45b1-b732-dae706dad604">

    - zip: combineLatest와 비슷하지만 다른 개념으로 combineLatest는 각 스트림에서 이벤트가 발생하면 바로 새로운 이벤트가 내려왔지만 zip은 모든 스트림에서 같은 횟수의 이벤트가 들어올 때 실행됩니다.
    
    <img width="495" alt="스크린샷 2023-05-15 오전 9 53 53" src="https://github.com/710csm/TIL/assets/45002556/9cb03e8f-bf65-4392-a3aa-1ff842c472f1">
    
    - witLatestFrom: combineLatest와 비슷한 것으로 새로운 이벤트가 발생한 스트림에서 지정한 스트림의 가장 최신의 아이템을 얻을 수 있다는 접입니다.
    
    <img width="745" alt="스크린샷 2023-05-15 오전 10 31 07" src="https://github.com/710csm/TIL/assets/45002556/f2124760-0d8a-4912-a0f1-8c07c6fd6f4a">      

### Error Handling Operators
- 에러를 처리하는 함수로, catchError, retry 등이 있습니다.
    - catchError: 에러가 발생 됐을 때 onError로 종료되지 않도록 하고, 이벤트를 발생하고 onComplete 될 수 있도록 합니다.
    - catchErrorJustReturn: 에러가 발생 했을 때 설정해둔 단일 이벤트를 전달하도록 하는 연산자입니다. subscribe에서 에러를 감지 하는 것이 아닌, Observable에서 에러에 대한 기본 이벤트를 설정하는 것 입니다.
    - retry: 에러가 발생 했을 때 성공을 기대하며 Observable을 다시 시도하는 연산자입니다. 재시도 횟수를 지정해 에러를 처리하고 다시한번 수행하도록 합니다.
    - retryWhen: retry 하는 시점을 지정할 수 있습니다. 재시도는 한번만 수행됩니다. 

### Utility Operators
- Observable의 동작을 조작하는 함수로, delay, do, materialize, subscribeOn 등이 있습니다.

RxSwift의 Operator는 이 외에도 다양한 종류가 있습니다. 이러한 Operator들을 적절히 활용하여 Observable의 데이터를 원하는 대로 처리할 수 있습니다. 하지만 Operator를 과도하게 사용하면 코드가 복잡해질 수 있으므로, 필요한 경우에만 사용하는 것이 좋습니다.

---

# Subject
- 실제 앱 구동시(run time) Observable에 값을 추가하여 emit이 발생하게끔 해주는 대리인
- Observable에 값을 추가하는 대상은 Observer라 부르며 Observable과 Observer 기능을 둘 다 하는것이 바로 Subject(subscriber가 아님을 주의)

## Subject의 종류
### 1. PublishSubject: empty 상태로 시작, 새로운 이벤트만 subscriber에게 emit
- 구독된 순간 이벤트 수신을 알리고 싶을때 사용
- 시간에 민감한 데이터를 모델링 할 경우(실시간 경매 앱 - 10:00am 경매 시작일 경우, 10:01am에 접속했을 때 알림이 보내질 필요가 없는 경우)
- PublishSubject는 현재 구독 요청한 대상만 관심을 가진다.(subject.onNext(3)에서 subscriptionOn 관련된 호출하지 않음)
- 이벤트 추가할 땐 "subject"으로 접근하여 추가(onNext, on)
- subject.onComplete() 할 시 구독 종료
- subscriber에게 이벤트를 emit 시키는 방법
    1. 기존 subscriber가 있을 때, subject.onNext() 한 경우
    2. 새로운 subsciber가 subscirbe한 경우

<img width="652" alt="스크린샷 2021-04-04 오후 5 49 31" src="https://user-images.githubusercontent.com/45002556/113503632-1d924b00-956e-11eb-977a-af566bb3990b.png">

```swift
let subject = PublishSubject<String>()
subject.onNext("Is anyone listening?")

// 첫 번째 구독 요청
let subscriptionOne = subject.subscribe(onNext: { (string) in
    print(string)
})

subject.on(.next("1"))
subject.onNext("2")
// prints : 1, 2

// 두 번째 구독 요청
let subscriptionTwo = subject.subscribe({ (event) in
    print("2)", event.element ?? event)
})

subject.onNext("3") // print : 2) 3

subscriptionOne.dispose()
subject.onNext("4") // print : 2) 4

subject.onCompleted() // print : 2) completed

subject.onNext("5") // none of print

subscriptionTwo.dispose()

let disposeBag = DisposeBag()

subject.subscribe {
    print("3)", $0.element ?? $0) // print : 3) completed
}
.disposed(by: disposeBag)

subject.onNext("?") // none of print
```

### 2. BehaviorSubject: 하나의 초기값으로 시작, 최신 값(.next)만 새로운 subscriber에게 emit
- BehaviorSubject는 초기화 값이 필수이며, 항상 저장되어있는 최신값을 emit
- BehaviorSubject사용: 뷰를 가장 최신의 데이터로 미리 채우기에 용이 (유저 프로필 화면을 BehaviorSubject에 바인딩)

<img width="613" alt="스크린샷 2021-06-11 오후 2 01 39" src="https://user-images.githubusercontent.com/45002556/121633338-90afc480-cabd-11eb-80c0-0d3e2bb5c183.png">    

```swift
enum MyError: Error {
    case anError
}

// BehaviorSubject는 초기값이 필수 이므로 초기화값을 삽입
let subject = BehaviorSubject(value: "Initial value")
let disposeBag = DisposeBag()

subject.onNext("X")

subject.subscribe{
    print("1)", $0)
}
.disposed(by: disposeBag)
// print : 1) next(X)

subject.onError(MyError.anError)
// print : 1) error(anError)

subject.subscribe {
    print("2)", $0)
}
.disposed(by: disposeBag)
// print : 2) error(anError)
```

###  3. ReplaySubject: 버퍼 사이즈를 지정하며, 버퍼 사이즈만큼 새로운 subscriber에게 emit
<img width="697" alt="스크린샷 2021-04-10 오후 3 53 05" src="https://user-images.githubusercontent.com/45002556/114261319-d811ca00-9a14-11eb-8fec-967cd0ed3b63.png">

```swift
let subject = ReplaySubject<String>.create(bufferSize: 2)
let disposeBag = DisposeBag()

subject.onNext("1")
subject.onNext("2")
subject.onNext("3")

subject.subscribe {
    print("1)", $0)
}
.disposed(by: disposeBag)
/* prints
1) next(2)
1) next(3)
*/

subject.subscribe {
    print("2)", $0)
}
.disposed(by: disposeBag)
/* prints
2) next(2)
2) next(3)
*/

subject.onNext("4")
/* prints
1) next(4)
2) next(4)
*/

subject.onError(MyError.anError)
/* prints
1) error(anError)
2) error(anError)
*/
// subject.dispose()

subject.subscribe {
    print("3)", $0)
}
.disposed(by: disposeBag)
/* prints:
3) next(3)
3) next(4)
3) error(anError)
*/
```

###  4. PublishRelay, BehaviorRelay: 오직 .next 이벤트만 emit 함 (.completed, .error 무시, non-terminating에서 유용하게 사용)

- PublishRelay: PublishSubject를 단순히 wrap한 것이며 .next만 가능하고 기능 동일
- BehaviorRelay: BehaviorSubject를 단순히 wrap한 것이며 .next만 가능하고 기능 동일
- Relay의 핵심은 절대 끝나지 않음을 보장하는 것
- UI를 처리할 때 좋다 (ex. TableView)

### PublishRelay 코드
- Relay의 추가는 .accept()로 접근
- error, onCompleted() 사용 불가

``` swift
import RxSwift
import RxCocoa // 필요

let relay = PublishRelay<String>()
let disposeBag = DisposeBag()

relay.accept("Knock knock, anyone home?")

relay.subscribe(onNext: {
    print($0)
})
.disposed(by: disposeBag)

relay.accept("1") // print: 1

// error와 onCompleted() 사용 불가
// relay.accept(MyError.anError)
// relay.onCompleted()
```

### BehaviorRelay 코드
- .accept() 로 다가가며, error, onCompleted를 못 쓴다는 것만 제외하면 BehaviorSubject와 동일하다

```swift
import RxSwift
import RxCocoa

let relay = BehaviorRelay(value: "Initial value")
let disposeBag = DisposeBag()

relay.accept("New initial value")

relay.subscribe {
    print("1)", $0)
}
.disposed(by: disposeBag)
// print : 1) next(New initial value)

// 1
relay.accept("1")
// print : 1) next(1)

// 2
relay.subscribe {
    print("2)", $0)
}
.disposed(by: disposeBag)
// print : 2) next(1)

// 3
relay.accept("2")
/* prints
2) next(2)
*/
```

### 5. AsyncSubject: 마지막의 이벤트만 subscriber에게 emit (단 .completed 이벤트를 받은 경우만)
- .onNext() 메소드도 사용 불가, 오직 accept(:_) 만 사용 가능

# RxSwift를 사용한 버튼 bind
```swift
import RxSwift
let disposeBag = DisposeBag()

button.rx.tap.bind { _ in
    // something do
}.disposed(by: disposeBag)
```

---

# Driver
- Driver는 UI Layer에 사용하기 좋다 
- Observable은 상황에 따라 MainSchedulaer와 BackgroundScheduler를 지정해줘야 하지만 Driver는 MainScheduler에서 사용한다.
- error를 return 하지 않는다.
- 스트림을 공유할 수 있어 불필요한 리소스 낭비를 막을 수 있다.

## Driver vs bind
bind는 Observer가 추가될 때마다 새로운 스트림이 실행된다.     
즉, Observable을 구동하는 Observer가  추가될 때 스트림도 같이 추가된다.      
불필요하게 리소스가 낭비된다.

driver는 스트림을 공유할 수 있다.    
즉, driver 객체를 구독하는 Observer가 추가될 때 스트림도 같이 추가되는 것이 아니라   
스트림은 하나로 유지되고 이를 같이 구독해서 각자의 작업을 한다.

---

# Skip

## skip(_ count: Int)
- count의 수만큼 시퀀스에서 발생하는 이벤트를 받지 않고 넘어갈 수 있다.

```swift
let disposedBaG = DisposeBag()

Observable.of("A","B", "C", "D", "E", "F")
    .skip(3)
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposedBag)

// 결과
D
E
F
```

## skip(while: (T) throws -> Bool) -> Observable<T>
- skip(while:)은 filter와 유사하다. filter는 조건을 통과하는 element만 전달되는 반면, skip(while:)은 조건을 통과하지 못한 element를 전달하게 된다.

 ```swift
let disposedBaG = DisposeBag()

Observable.of(2, 2, 3, 4, 4)
    .skip(while: { integer in
        integer % 2 == 0
    })
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposedBag)

// 결과
3
4
4
```

## skip(until: ObservableType)
- 두개의 시퀀스를 사용하여 하나의 시퀀스에서 계속 이벤트가 발생하고 있고 두 번째 시퀀스를 첫 번째 시퀀스의 트리거로 사용한다. 즉 두 번째 시퀀스의 이벤트가 발생하면 첫 번째 시퀀스가 emit된다.

```swift
let disposedBag = DisposedBag()

let subject = PublishSubject<String>()
let trigger = PublishSubject<String>()

subject
    .skipUtil(trigger)
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposedBag)

subject.onNext("A")
subject.onNext("B")

trigger.onNext("trigger")

subject.onNext("C")

// 결과
C
```

---

# Take
- Observable에서 내보낸 아이템 중, 처음 n개의 아이템만 내보내준다.
- 아이템이 여러번 방출될 수 있는 Observable에서 한번만 방출 되도록 제한한다.

<img width="700" alt="스크린샷 2022-10-20 오후 2 50 23" src="https://user-images.githubusercontent.com/45002556/196866633-3ca70ab9-ecdd-4420-8696-28ac6ca2bae9.png">

```swift
let disposedBag = DisposedBag()

Observable.of(1,2,3,4)
    .take(2)
    .subscribe(onNext: {
        print($0)   
    })
    .disposed(by: disposedBag) 

// 결과
1
2
```
 
---

# RxSwift/RxCocoa를 사용한 TableView 설정
-  BehaviorRelay를 사용하여 TableView의 UI를 설정

```swift
import RxSwift
import RxCocoa

let dataList = [Data]()
let relayData: BehaviorRelay<[Data]>
let disposeBag = DisposeBag()

override func viewDidLoad() {
    super.viewDidLoad()
    
    relayData = BehaviorRelay.init(value: dataList)
    setTableViewItem(relayData)     // set tableView 
    setTableItemSelected()          // tableView item selected
    
    let items = Networking.getWeatherData()
    items.subscribe(onNext: { data in
        self.dataList.append(data)
        relay.accept(dataList)
        tableView.relaodData()
    }, onError: { error in
        print(error)
    }).disposed(by: disposeBag)    
}

func setTableViewItem(_ item: BehaviorRelay<[Data]>) {
    items.bind(
        to: tableView.rx.items(
            cellIdentifier: "Cell",
            cellType: UITableViewCell.self
        )
    ) { (row, element, cell) in
        cell.textLabel?.text = "\(element.main)"
    }
    .disposed(by: disposeBag)
}

private func setTableItemSelected() {
    Observable
        .zip(tableView.rx.itemSelected, tableView.rx.modelSelected(Data.self))
        .subscribe(onNext: { [weak self] (indexPath, data) in
            self?.tableView.deselectRow(at: indexPath, animated: false)
            print(data)
            // or do something
        })
        .disposed(by: disposeBag)
}
```
- 초기값이 있어야 하기 때문에 비어있는 값으로 테이블을 설정 후 네트워크 통신을 통해 dataList의 값이 바뀌면 바뀐 값으로 테이블 재설정
