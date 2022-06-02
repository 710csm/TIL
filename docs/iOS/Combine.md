# Combine
- 시간에 따라 값을 처리하기위한 선언적 Swift API를 제공한다. 애플에서 만든 RxSwift와 같은 개념으로 볼 수 있다.
- Combine은 발생한 이벤트에 대해 어떻게 가공하고 소비해줄지에 초점을 맞췄다. 비동기와 관련한 코드를 작성할 때 사용하던 불필요한 보일러 플레이트를 줄여주며 런타임 오류를 효율적으로 처리할 수 있고 데이터의 제공자가 아닌 데이터 요청자 측에서 처리할 수 있다.

<img width="712" alt="스크린샷 2022-06-02 오전 10 12 57" src="https://user-images.githubusercontent.com/45002556/171526594-24351560-9780-464d-bef1-965745389128.png">

1. Publisher는 값이나 completion event를 Subscriber, Subscription에게 전달
2. Subscriber는 Subscription을 통해 Publisher에게 값을 요청
3. Subscription은 Publisher와 Subscrriber 사이를 연결
4. Subscriotion은 cancel을 통해 취소할 수 있으며 이 때 호출될 클로저를 설정할 수 있다.

---

## Subscriber
- Publisher로 부터 input을 받을 수 있는 타입을 선언하는 프로토콜

### Subscriber 구성
1. Input
    - Publisher에게 받는 값의 타입
2. Failure
    - Publisher에게 받는 Error타입
    - 만약 Error를 수신하고 싶지 않다면 Never로 설정하면 된다.
3. receive(subscription:)
    - Publisher가 만들어서 주는 subscription을 받는다.
4. receive(input:)
    - Publisher가 주는 값을 받는다.
    - Denamd를 반환하는데 이는 값을 더 원하는지에 대한 여부이다.
5. receive(compltion:)
    - Publisher가 주는 completion event를 받는다.

### Subscriber 구현 예제
- Subscriber를 상속 받는다.
- Input, Failure 및 receive 메소드를 필수로 구현해야한다.

```swift
class IntSubscriber: Subscriber {
    typealias Input = Int
    typealias Failure = Never
    
    /*
     Publisher가 Subscription을 주면 호출된다.
     Publisher에게 받은 Subscription의 request를 호출하여 Publisher에게 값을 요청한다.
     Demand는 Publisher에게 값을 몇번 달라고 요청하는것
     
     즉 Subscriber가 Publisher에게 값을 요청할 때 Subscription을 사용한다고 볼 수 있다.
     
     또한 Subscription은 특정 Subscriber가 Publisher에 연결된 순간 설정되는 Identity가 있어서 한 번만 cancel 할 수 있다.
     cancel하면 이전에 연결된 Subscriber들이 모두 해제합니다. 이 과정은 스레드로부터 안전해야한다.
     cancel 할 수 있는 이유는 Subscriber 프로토콜이 Cancellable 프로토콜을 채택했기 때문
     
     cancel을 호출 하지 않으면 Publisher가 complete 될 때까지 메모리에서 subscription이 해제될 때까지 Subscription이 계속된다.
    */
    func receive(subscription: Subscription) {
        subscription.request(.max(1))
    }
    
    // Publisher가 주는 값을 처리
    func receive(_ input: Int) -> Subscribers.Demand {
        print("Received value: \(input)")
        return .max(1)
        // Publisher에게 값을 더 이상 안줘도 된다고 알림
        // return .none
        // Publisher에게 끝없이 값을 달라고 요청
        // return .unlimited
    }
    
    func receive(completion: Subscribers.Completion<Never>) {
        print("Received completion: \(completion)")
    }
}

let intArray = [1, 2, 3, 4, 5]
let intSubscriber = IntSubscriber()

intArray.publisher.subscribe(intSubscriber)
```

### cancel
```swift
let subject = PassthroughSubject<Int, Never>()
let anycancellable = subject
    .sink(receiveCompletion: { completion in
        print("received compltion: \(completion)")

    }, receiveValue: { value in
        print("received value: \(value)")

    })

subject.send(1)
anycancellable.cancel()
subject.send(2)
```

```swift
let subject = PassthroughSubject<Int, Never>()
let anyCancellable = subject
    .handleEvents(receiveCompletion: { completion in
        print("Received compltion: \(completion)")
    }, receiveCancel: {
        print("Received cacel")
    }).sink(receiveValue: { value in
        print("Received value: \(value)")
    })

subject.send(1)
anyCancellable.cancel()
```

## Subscriber의 종류
1. Demand
2. Compltion
3. Sink
4. Assign

### Sink
- 횟수의 제한 없이 Subscrip을 통해 값을 요청하는 간단한 Subscription이다.

```swift
let intArrayPublisher = [1,2,3,4,5].publisher

let sink = Subscribers.Sink<Int, Never>(receiveCompletion: { completion in
    print("received completion: \(completion)")
}, receiveValue: { value in
    print("received value: \(value)")
})

intArrayPublisher.subscribe(sink)

// 또는
intArrayPublisher.sink(receiveCompletion: { completion in
    print("received completion: \(completion)")
}, receiveValue: { value in
    print("received value: \(value)")
})

// 결과
received value: 1
received value: 2
received value: 3
received value: 4
received value: 5
received completion: finished
```

### Assign
- key path로 표시된 프로퍼티에 수신된 값을 할당하는 간단한 Subscriber 이다. 값을 받아 저장하는 Suabscriber
- object
    - 프로퍼티를 포함하는 객체. Subscriber는 새로운 값을 받을 때마다 여기에 할당을 한다.
    - Subscriber는 upstream publisher가 Subscriber의 receive(completion:)을 호출할 때까지 object에 대한 강한 참조를 유지하고, 호출된 이후에 nil로 설정된다.
- keyPath
    - 할당할 프로퍼티를 나타내는 key-path

```swift
class SampleObject  {
    var intValue: Int {
        didSet {
            print("intValue changed: \(intValue)")
        }
    }
    
    init(intValue: Int) {
        self.intValue = intValue
    }
    
    deinit {
        print("sample object deinit")
    }
}

let myObject = SampleObject(intValue: 1)
let assign = Subscribers.Assign<SampleObject, Int>(object: myObject, keyPath: \.intValue)

let intArrayPublisher = [2,3,4,5].publisher
intArrayPublisher.subscribe(assign)

// 또는
intArrayPublisher
    .assign(to: \.intValue, on: myObject)


// 결과
intValue changed: 2
intValue changed: 3
intValue changed: 4
intValue changed: 5
```

### Demand
- Subscriber가 subscription을 통해 Publisher에게 요청한 item의 수
- unlimited
    - 계속해서 값을 받겠다는 의미
- none
    - max(0)과 같은 의미로 요청을 추가하지 않는다는 뜻
- max(_ value: Int)
    - 매개변수로 받은 값만큼 추가로 요청

### Completion
- Demand와 동일하게 Subscriber가 아닌 Subscriber를 사용할 때 필요한 값

---

## Publisher
- 타입이 시간에 따라 일련의 값을 전송할 수 있음을 선언한다. 
- Publisher는 하나 이상의 Subscriber 인스턴스에게 element를 제공한다.
- Publisher는 receive 메소드를 구현해 subscriber를 accept한다.

## Publisher의 종류
1. Just
2. Future
3. Deferred
4. Empty
5. Fail
6. Record
7. AnyPublisher
 
### Just 
- 가장 간단한 Publisher로 자신을 Subscribe하는 Subscriber들에게 한 번에 값을 내보낸 뒤 finish 이벤트를 보내는 Publisher
- 값을 한번에 보내고 finish 이벤트를 보낸다
- Output, Failure를 설정하지 않아도 된다. -> Never로 이미 설정되어 있기 때문에

```swift
let just = Just("Test OutPut")
just
    .sink(receiveCompletion: { completion in
        print("received completion \(completion)")
    }, receiveValue: { value in
        print("received value \(value)")
    })
```

### Future
- 하나의 결과를 비동기로 생성한 뒤 completion event를 보낸다.
- promise는 Future가 값을 내보낼 때 호출되는 클로저이다. 
- init에서 Promise 클로저를 매개변수로 받는다. 다른 Publisher들은 struct이지만 Future만 비동기로 작동할 때 상태 저장 동작을 가능하게하기 위해서 Class로 구현되어 있다.
- 즉 Future는 생성할 때 값을 내보낼 때 호출할 클로저를 매개변수로 받아서 그걸 값을 내보낼 때마다 계속 호출하는 Publisher이다.

```swift
var subscriotions = Set<AnyCancellable>()
var emitValue: String = "Output Value"
var delay: TimeInterval = 3

let future = Future<String, Never> { promise in
    delay -= 1
    DispatchQueue.main.asyncAfter(deadline: .now() + delay) {
        promise(.success(emitValue))
    }
}

future.sink(receiveCompletion: {
    print("첫번째 Subscrber Completion: \($0)")
}, receiveValue: {
    print("첫번째 Subscrber value: \($0)")
}).store(in: &subscriotions)

future.sink(receiveCompletion: {
    print("두번째 Subscrber Completion: \($0)")
}, receiveValue: {
    print("두번째 Subscrber value: \($0)")
}).store(in: &subscriotions)

// 결과
두번째 Subscriber value: 내보내진 값
두번째 Subscriber completion: finished
첫번째 Subscriber value: 내보내진 값
첫번째 Subsrciber Completion: finished
```

### Deferred
- Deferred는 새로운 Subscriber의 Publisher를 만들기 위해 제공된 클로저를 실행하기 전에 Subscription을 기다리는 Publisher
- lazy와 비슷하게 Publisher가 실제로 사용될 때 Publisher를 생성하여 메모리를 효율적으로 사용할 수 있다.

```swift
struct TestPublisher: Publisher {
    typealias Output = String
    typealias Failure = Never
    
    func receive<S>(subscriber: S) where S : Subscriber, Never == S.Failure, String == S.Input {
        subscriber.receive("test publisher")
        subscriber.receive(completion: .finished)
    }
}

let deferred = Deferred {  () -> TestPublisher in
    print("init TestPublisher")
    return TestPublisher()
}

deferred
    .sink(receiveCompletion: { completion in
        print("received completion: \(completion)")
    }, receiveValue: { value in
        print("received value: \(value)")
    })

// 결과
init TestPublisher
received value: test publisher
received completion: finished
```

### AnyPublisher
- Upstream Publisher의 값들을 전달하는 Publisher입니다.
- 어떤 Publisher에서도 EraseToAnyPublisher()를 호출하면 AnyPublisher로 래핑 됩니다.
- Combine을 사용하다 보면 여러가지 Operator를 사용하면서 여러 Publisher 타입이 생성될 수 있는데 AnyPublisher를 사용하면 간단하게 처리할 수 있다.

```swift
let originalPublisher = [1, nil, 3].publisher
let anyPublisher = originalPublisher.eraseToAnyPublisher()

anyPublisher
    .sink(receiveCompletion: { completion in
        print("received compltion: \(completion)")
    }, receiveValue: { value in
        print("received value: \(String(describing: value))")
    })

// 결과
received value: Optional(1)
received value: nil
received value: Optional(3)
received compltion: finished
```

### Empty
- 어떤 값도 내보내지 않고 즉시 completion 이벤트를 보낼지 선택할 수 있는 Publisher입니다.
- init(completeImmediately: Bool = true)
    1. completion 이벤트를 바로 보낼지만 결정합니다.
    2. init(completeImmediately: Bool = true, outputType: Output.Type, failureType: Failure.Type)

```swift
let empty = Empty<String, Never>()
empty.sink(receiveCompletion: { completion in
    print("received completion: \(completion)")
}, receiveValue: { value in
    print("received value: \(value)")
})

// 결과
received completion: finished
```

```swift
let anyPublisher = [1, nil,  3]
    .publisher
    .flatMap { value -> AnyPublisher<Int, Never> in
        if let value = value {
            return Just(value).eraseToAnyPublisher()
        } else {
            return Empty().eraseToAnyPublisher()
        }
    }.eraseToAnyPublisher()

anyPublisher
    .sink(receiveCompletion: { completion in
        print("received comption: \(completion)")
    }, receiveValue: { value in
        print("received value: \(value)")
    })

// 결과
received value: 1
received value: 3
received comption: finished
```

### Fail
- Error를 즉시 보낼 수 있는 Publisher이다.

```swift
enum CustomError: Error {
    case unknownError
}

let fail = Fail<String, CustomError>(error: .unknownError)

fail.sink(receiveCompletion: { completion in
    print("received completion: \(completion)")
}, receiveValue: { value in
    print("received value: \(value)")
})

// 결과
received completion: failure(__lldb_expr_14.CustomError.unknownError)
```

```swift
enum CustomError: Error {
    case unknownError
}

let anyPublisher = [1, nil, 3]
    .publisher
    .flatMap { value -> AnyPublisher<Int, CustomError> in
        if let value = value {
            let just = Just(value).setFailureType(to: CustomError.self)
            return just.eraseToAnyPublisher()
        } else {
            return Fail<Int, CustomError>(error: .unknownError).eraseToAnyPublisher()
        }
    }.sink(receiveCompletion: { compltion in
        print("received compltion: \(compltion)")
    }, receiveValue: { value in
        print("received value: \(value)")
    })

// 결과
received value: 1
received compltion: failure(__lldb_expr_22.CustomError.unknownError)
```

### Record
- 각각의 Publisher가 나중에 내보낼 수 있도록 input과 completion을 저장해두는 Publisher이다.

```swift
let stringArray = ["one", "two", "three"]
let record = Record<String, Never>(output: stringArray, completion: .finished)

record.sink(receiveCompletion: { compltion in
    print("received completion: \(compltion)")
}, receiveValue: { value in
    print("received value: \(value)")
})

// 결과
received value: one
received value: two
received value: three
received completion: finished
```

## Subject
- subject는 외부 발신자가 element를 publish할 수 있는 방법을 제공하는 Publisher이다.
- Publisher 프로토콜을 채택하고 있는 Publisher의 일종으로 특별한 점은 파이프라인 외부에서도 파이프라인 안으로 데이터를 보낼 수 있다.
- Subject는 PassthroughSubject과 CurrentValueSubject 두 가지의 빌트인 타입이 있다.
- 기존에 Combine을 사용하지 않던 코드에 Combine 모델을 적용하고 싶을 때 사용하면 좋다.

### currentValueSubject
- currentValueSubject는 PassthroughSubject와 다르게 가장 최근에 publish된 값의 버퍼를 유지한다.
- currentValueSubject의 send를 호출하면 현재 값도 업데이트돼서 값을 직접 업데이트하는거랑 동일한 효과를 얻을 수 있다.

```swift
currentValueSubject.sink(receiveCompletion: { completion in
    print("1 번째 sink completion: \(completion)")
}, receiveValue: { value in
    print("1 번째 sink value: \(value)")
})


currentValueSubject.sink(receiveCompletion: { completion in
    print("2 번째 sink completion: \(completion)")
}, receiveValue: { value in
    print("2 번째 sink value: \(value)")
})


currentValueSubject.sink(receiveCompletion: { completion in
    print("3 번째 sink completion: \(completion)")
}, receiveValue: { value in
    print("3 번째 sink value: \(value)")
})

currentValueSubject.send("second currentValue Subject Test")
currentValueSubject.send(completion: .finished)

// 결과
1 번째 sink value: first currentValue Subject Test
2 번째 sink value: first currentValue Subject Test
3 번째 sink value: first currentValue Subject Test
1 번째 sink value: second currentValue Subject Test
2 번째 sink value: second currentValue Subject Test
3 번째 sink value: second currentValue Subject Test
1 번째 sink completion: finished
2 번째 sink completion: finished
3 번째 sink completion: finished
```

- 중간에 cancel을 할 경우

```swift
let currentValueSubject = CurrentValueSubject<String, Never>("first currentValue Subject Test")

currentValueSubject.sink(receiveCompletion: { completion in
    print("1 번째 sink completion: \(completion)")
}, receiveValue: { value in
    print("1 번째 sink value: \(value)")
})

currentValueSubject.sink(receiveCompletion: { completion in
    print("2 번째 sink completion: \(completion)")
}, receiveValue: { value in
    print("2 번째 sink value: \(value)")
}).cancel()

currentValueSubject.sink(receiveCompletion: { completion in
    print("3 번째 sink completion: \(completion)")
}, receiveValue: { value in
    print("3 번째 sink value: \(value)")
})

currentValueSubject.send("second currentValue Subject Test")
currentValueSubject.send(completion: .finished)

//결과
1 번째 sink value: first currentValue Subject Test
2 번째 sink value: first currentValue Subject Test
3 번째 sink value: first currentValue Subject Test
3 번째 sink value: second currentValue Subject Test
1 번째 sink value: second currentValue Subject Test
3 번째 sink completion: finished
1 번째 sink completion: finished
```

### PassthroughSubject
- downstream의 subscriber들에게 값을 전파한다. 
- currentValueSubject와 다르게 생성할 때 딱히 초기값이 필요하지 않다.

```swift
let passthroughSubject = PassthroughSubject<String, Never>()

let firstSubscription = passthroughSubject
    .sink(receiveCompletion: { compltion in
        print("1번째 received compltion: \(compltion)")
    }, receiveValue: { value in
        print("1번째 received value: \(value)")
    })

passthroughSubject.send("first value")

let secondSubscription = passthroughSubject
    .sink(receiveCompletion: { compltion in
        print("2번째 received compltion: \(compltion)")
    }, receiveValue: { value in
        print("2번째 received value: \(value)")
    })

passthroughSubject.send("second value")

// 결과
1번째 received value: first value
1번째 received value: second value
2번째 received value: second value
```

## Subscription
- Cancellable을 채택한 프로토콜로 Subscriber와 Publisher의 연결을 나타내는 프로토콜이다.
