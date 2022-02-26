# defer
- defer란 함수 안에서 작성되는 클로저로 함수 안에 작성된 위치에 상관없이 함수 종료 직전에 실행된다.
- 함수를 종료하기 직전에 정리해야 하는 변수나 상수를 처리하는 용도로 사용한다.

## defer의 사용법
- 간단하게 defer 키워드를 작성해서 사용할 수 있다.

```Swift
defer {
    // 코드 작성
}
```

## defer 테스트

```Swift
func deferTest() {
    print("test 1")
    
    defer {
        print("defer test")
    }
    
    print("test 2")
}

// 실행 결과
test 1
test 2
defer test
```

## defer의 특징
1. defer가 읽히기 전에 함수가 종료되면 defer가 실행되지 않는다

```Swift
func deferTest() {
    print("test 1")

    return
    
    defer {
        print("defer test")
    }
    
    print("test 2")
}

// 실행 결과
test 1
```

2. 한 함수 안에서 여러개의 defer가 여러번 호출 가능하고, 실행 순서는 마지막 defer부터 실행된다. stack에 처음 호출된 defer가 순서대로 쌓여서 실행된다.

```Swift
func deferTest() {
    defer {
        print("defer test 1")
    }
    
    defer {
        print("defer test 2")
    }
    
    defer {
        print("defer test 3")
    }
}

// 실행 결과
defer test 3
defer test 2
defer test 1
```

3. defer를 중첩해서 사용 가능하며 가장 밖에 있는 defer부터 실행이된다.

```Swift
func deferTest() { 
    defer {
        defer {
            defer {
                print("defer test 3")
            }
            print("defer test 2")
        }
        print("defer test 1")
    }
}

// 실행 결과
defer test 1
defer test 2
defer test 3
```

4. 함수에서 값을 리턴한 이후에 defer가 실행된다.

```Swift
func deferTest() -> Int {
    var value = 1
    
    defer {
        value += 1 // 값이 return된 이후에 value는 2가 된다.
    }
    
    return value
}

// 실행 결과
1
```
