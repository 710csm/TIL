# Combine

1. Publisher는 값이나 completion event를 Subscriber, Subscription에게 전달
2. Subscriber는 Subscription을 통해 Publisher에게 값을 요청
3. Subscription은 Publisher와 Subscrriber 사이를 연결
4. Subscriotion은 cancel을 통해 취소할 수 있으며 이 때 호출될 클로저를 설정할 수 있다.


## 사용 예제
- Subscriber를 상속 받는다.
- Input, Failure 및 receive 메소드를 필수로 구현해야한다.

```swift
class IntSubscriber: Subscriber {
    typealias Input = Int
    typealias Failure = Never
    
    /*
     Publisher가 Subscription을 주면 호출된다.
     Publisher에게 받은 Subscription의 request를 호출하여 Publisher에게 값을 요청한다.
     Demand는 아까 말한 대로 Publisher에게 값을 몇번 달라고 요청하는것
     
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

### cancel 예제 1
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

### cancel 예제 2
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
