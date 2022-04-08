# GCD(Grand Central Dispatch)
- iOS에서 멀티코어 프로세서에서 코드를 동시에 실행시키게 해주는 프레임워크
- Task를 Dispatch Queue를 사용해 시스템이 처리하고 실행시킨다

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

3. Utility
- 수초에서 수분에 걸쳐 수행되는 작업으로 반응성, 성능, 그리고 에너지 효율성 간에 균형을 유지하는데 중점
- 데이터를 읽어들이거나 다운로드 하는 등 작업을 완료하는데 어느 정도 시간이 걸릴 수 있으며 보통 진행 표시줄로 표현한다
- 수분에서 수시간에 걸쳐 수행되는 작업으로 에너지 효율성에 중점. NSOperation 클래스 사용 시 기본값
- background에 동작하며 색인 생성, 동기화, 백업 같이  사용자가 볼 수 없는 작업에 할당
- 저전력 모드에서는 네트워킹을 포함하여 백그라운드 작업은 일시 중지

4. Default
- Qos를 별도로 지정하지 않으면 기본값으로 사용되는 형태이며 User Initiated와 Utility의 중간
- CGD global queue의 기본 동작 형태

5. Unspecified
- QoS 정보가 없으므로 시스템이 추론해야 한다는 것을 의미한다

