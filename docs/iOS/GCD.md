# GCD(Grand Central Dispatch)
- iOS에서 멀티코어 프로세서에서 코드를 동시에 실행시키게 해주는 프레임워크
- Task를 Dispatch Queue를 사용해 시스템이 처리하고 실행시킨다

## Dispatch queue가 내부적으로 어떻게 동작 되는지
1. iOS의 디스패치 큐는 작업을 병렬로 수행하기 위한 효율적이고 오버헤드가 낮은 기술인 GCD(Grand Central Dispatch) 프레임워크의 일부다. 발송 대기열은 work thread pool에서 "태스크"라 불리는 작업을 예약하는 방식으로 동작한다. 태스크는 대기열에 추가된 순서대로 실행된다.
2. 각 발송 대기열에는 태스크 실행 방법을 결정하는 직렬 또는 동시(serial or concurrent) 유형이 있다. serial 대기열에서 태스크는 대기열에 추가된 순서대로 한 번에 하나씩 실행된다. concurrent 대기열에서 여러 작업을 동시에 실행할 수 있지만 실행할 정확한 순서는 지정되지 않는다.
3. 내부적으로 GCD 프레임워크는 work thread pool을 관리하며, 각 스레드는 모든 큐에서 작업을 실행할 수 있다. work thread는 시스템에 의해 관리되므로 앱 개발자는 스레드를 만들거나 관리하는 것에 대해 걱정할 필요가 없다. 시스템은 CPU 리소스의 가용성, 대기열의 작업 수 및 작업 우선 순위와 같은 요소를 기반으로 실행할 작업과 시기를 결정한다.
4. GCD 프레임워크는 또한 여러 작은 작업을 하나의 더 큰 작업으로 일괄 처리하고 가능한 경우 새 작업자 스레드 생성을 피하는 것과 같이 디스패치 대기열이 효율적인지 확인하기 위한 여러 최적화를 제공한다.

## Serial Queue
- Serial Queue는 큐에 추가된 순서대로 한번에 하나의 task를 실행한다 

## Concurrent Queue
- Concurrent Queue는 동시에 하나 이상의 task를 실행하지만 큐에 추가됐을 시 추가된 순서대로 작업을 계속 진행합니다.

## Sync
- sync 메소드를 호출한 caller는 전달된 작업이 끝날 때까지 기다린다. sync는 작업이 끝나야 return하기 때문이다. 

## Async
- sync와는 달리 작업을 전달하자마자 return한다. 즉 caller는 async를 호출한 뒤에, 다음 작업을 바로 수행한다.

## 사용법
- Main
    - UI와 관련된 작업은 모두 main큐를 통해서 수행하며 Serial Queue에 해당된다.
    - MainQueue를 sync 메서드로 동작시키면 Dead Lock 상태에 빠진다.

```Swift
DispatchQueue.main.async { }
```

- Global
    - UI를 제외한 작업에서 사용하며 Concurrent Queue에 해당한다.
    - sync와 async 메서드 모두 사용 가능하다.
    - Qos(Quality of service)를 지정해 우선순위 설정이 가능하다.
    
```Swift
DispatchQueue.global()async { }
DispatchQueue.global(qus: .utility).sync { }
```

## QOS(Quality of service)
- 스케쥴링, CPU 및 I/O 처리량, 타이머 대기 시간 등의 우선 순위 조정
- unspecified -> background -> utility -> default -> userInitiated -> userInteractive 순서로 우선순위가 높다

1. User Interactive
- 즉각 반응해야 하는 작업으로 반응성 및 성능에 중점
- main thread에서 동작하는 인터페이스 새로고침, 애니메이션 작업 등 즉각 수행되는 유저와의 상호작용 작업에 할당

2. User Initiated
- 몇 초 이내의 짧은 시간 내 수행해야하는 작업으로 반응성 및 성능에 중점
- 문서를 열거나, 버튼을 클릭해 액션을 수행하는 것 처럼 빠른 결과를 요구하는 유저와의 상호작용 작업에 할당

3. Default
- Qos를 별도로 지정하지 않으면 기본값으로 사용되는 형태이며 User Initiated와 Utility의 중간
- CGD global queue의 기본 동작 형태

4. Utility
- 수초에서 수분에 걸쳐 수행되는 작업으로 반응성, 성능, 그리고 에너지 효율성 간에 균형을 유지하는데 중점
- 데이터를 읽어들이거나 다운로드 하는 등 작업을 완료하는데 어느 정도 시간이 걸릴 수 있으며 보통 진행 표시줄로 표현한다
- 수분에서 수시간에 걸쳐 수행되는 작업으로 에너지 효율성에 중점. NSOperation 클래스 사용 시 기본값
- background에 동작하며 색인 생성, 동기화, 백업 같이  사용자가 볼 수 없는 작업에 할당
- 저전력 모드에서는 네트워킹을 포함하여 백그라운드 작업은 일시 중지

5. Background
- Progress Indicator와 함께 길게 사용되는 작업
- 지속적인 데티어 feed, Networking에 사용

6. Unspecified
- QoS 정보가 없으므로 시스템이 추론해야 한다는 것을 의미한다

## NSOperationQueue 와 GCD Queue 의 차이점을 설명하시오.
- NSOperationQueue
    1. GCD에서 할 수 없는 기능들을 제공하지만 구현이 복잡하고 무겁다. 
    2. 모든 작업이 끝나지 않은 상태에서 Operation queue를 중지시키면 메모리 릭이 발생할 수 있다.

## Dispatch Queue를 사용했을 때 thread 간의 동기화 방법
- DispatchSemaphore를 사용

**Semaphore란?**
> 정수 변수로서, 멀티프로그래밍 환경에서 공유 자원에 대한 접근을 제한하는 방법으로 사용된다.   
스레드가 공유 자원의 배타적인 사용을 보장받기 위해서 임계 구역에 들어가거나 나올때는 세마포어 같은 동기화 매커니즘이 사용된다.

**DispatchSemaphore**
1. 동시 작업 개수 제한
- iOS에서는 세마포어를 위해 DispatchSemaphore라는 객체를 이용합니다.
- 공유 자원에 접근 가능한 작업 수를 명시하고, 임계 구역에 들어갈 때는 semaphore의 wait()을, 나올때는 signal()을 호출합니다.

```Swift
// 공유 자원에 접근 가능한 작업 수를 2개로 제한
let semaphore = DispatchSemaphore(value: 2)

for i in 1...3 {
    semaphore.wait() // semaphore 감소
    
    DispatchQueue.global().async() {
        // 임계 구역(critical section)
        print("공유 자원 접근 시작 \(i)")
        sleep(3)
        
        print("공유 자원 접근 종료 \(i)")
        semaphore.signal() // semaphore 증가
    }
}
```

2. 두 스레드의 특정 이벤트 완료 상태 동기화
- 두 스레드가 특정 이벤트의 완료 상태를 동기화 하는 경우에 유용하다

```Swift
//DispatchSemaphore 초기값 0으로 설정
let semaphore = DispatchSemaphore(value: 0)

print("task A가 끝나길 기다림")

// 다른 스레드에서 task A 실행
DispatchQueue.global(qos: .background).async {
    //task A
    print("task A 시작!")
    print("task A 진행중..")
    print("task A 끝!")

    //task A 끝났다고 알려줌
    semaphore.signal()
}

// task A 끝날때까지는 value 가 0이라, task A 종료까지 block
semaphore.wait()        
print("task A 완료됨")
```

3. DispatchSemaphore 예시

```Swift
let q1 = DispatchQueue(label: "q1", qos: .backgruond, attributes: .concurrent, autoreleaseFrequency: .workItem, target: nil)
let q2 = DispatchQueue(label: "q2", qos: .backgruond, attributes: .concurrent, autoreleaseFrequency: .workItem, target: nil)
let semaphore = DispatchSemaphore(value: 1) // value를 통해 공유 자원에 접근 가능한 작업 수를 설정 1이면 한개만 접근할 수 있다

var count = 0

func increment(queue: DispatchQueue) {
    count = count + 1
    print("write count: \(count) in queue: \(queue.label)")
}

func read(queue: DispatchQueue) {
    print("read count: \(count) in queue: \(queue.label)")
}

func perform(queue: DispatchQueue) {
    semaphore.wait() // wait()은 액세스를 요청하는데 사용된다.
    increment(queue: queue)
    read(queue: queue)
    semaphore.signal()  // signal()은 액세스를 해제하는데 사용된다.
}

for _ in 1...5 {
    q1.async {
        perform(queue: q1)    
    }

    q2.async {
        perform(queue: q2)    
    }
}

// 결과
write count: 1 in queue q1
read count: 1 in queue q1
write count: 2 in queue q2
read count: 2 in queue q2
write count: 3 in queue q1
read count: 3 in queue q1
write count: 4 in queue q2
read count: 4 in queue q2
write count: 5 in queue q1
read count: 5 in queue q1
write count: 6 in queue q2
read count: 6 in queue q2
write count: 7 in queue q1
read count: 7 in queue q1
write count: 8 in queue q2
read count: 8 in queue q2
write count: 9 in queue q1
read count: 9 in queue q1
write count: 10 in queue q2
read count: 10 in queue q2
```
