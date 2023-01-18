# LazySequence
 

## eager order(step-by-step)
- 요소 전체를 대상으로 연산을 차근차근 적용한다.
- 각 단계에서 연산이 이루어져 새로운 컬렉션을 만들어낸다.

```Kotlin
// 이펙티브 코틀린에 나온 예시
// 전체적으로 2개의 컬렉션이 만들어진다.

let numbers = [1,2,3,4,5,6,7,8,9,10] 
numbers
    .filter { it % 10 == 0 } // 여기에서 컬렉션 하나
    .map { it * 2 } // 여기에서 컬렉션 하나
    .sum() 
```

## lazy order(element-by-element)
- 요소 하나하나에 지정한 연산을 한꺼번에 적용한다.
- 최종 연산이 이루어지기 전까지는 각 단계에서 연산이 일어나지 않는다.

```Kotlin
// 이펙티브 코틀린에 나온 예시
// 컬렉션이 만들어지지 않는다.

let numbers = [1,2,3,4,5,6,7,8,9,10]
numbers
    .asSequence()
    .filter { it % 10 == 0 }
    .map { it * 2 }
    .sum()  
```

### eager vs lazy
- 컬렉션 처리 함수를 사용하지 않고, 고전적인 반복문과 조건문을 활용해서 코드를 구현한다면 이는 lazy order와 같다. 그래서 lazy가 훨씬 자연스러운 처리라고 할 수 있다.
- eager은 각각의 단계에서 만들어진 결과를 활용하거나 저장할 수 있다는 장점이 있지만, 각각의 단계에서 결과가 만들어지면서 공간을 차지하는 비용이든다는 단점이 있다. 
- 크거나 무거운 컬렉션을 처리할 때, 메모리 낭비를 줄이고 싶은 경우 lazy를 활용하면 좋다. 중간처리 단계를 모든 요소에 적용할 필요가 없다면 최소연산만 할 수 있또록 lazy를 활용하면 좋다.

## LazySequenceProtocol / LazySequence
- LazySequenceProtocol은 lazy라는 property를 가지고 있다. 이를 통해 LazySequence를 얻을 수 있다.
- LazySequence은 struct이고, LazySequenceProtocol과 Sequence를 받고있다.

## LazySequence 예제
- map과 filter같은 sequence operation은 eager 방식이다. 즉 closure를 즉각적으로 사용해서 새로운 array를 리턴합니다. lazy 프로퍼티를 사용하면 standard library에 closure과 sequence를 저장할 수 있는 명시적 권한을 부여하고 필요할 때까지 계산을 연기할 수 있다.

```Swift
let value = [1, 2, 3].lazy.map { $0 * 2 }
```

- 1에서 1000까지 숫자 중에 짝수인 숫자들을 걸러 2를 곱하고, prefix 2개를 구할 때, lazy sequence를 사용해서 적은 연산량으로 이 작업을 수행할 수 있다.
- 만약 lazy를 사용하지 않으면 filter에서 1000번 연산, map에서 500번 연산이 이루어진다.
- lazy를 사용하면 연산 횟수가 8번으로 줄어든다.

```Swift
(1...1000)
    .lazy
    .filter { $0 % 2 == 0 }
    .map { $0 * 2 }
    .prefix(2)
```
