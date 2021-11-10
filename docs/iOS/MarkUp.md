# XCode의 주석과 마크업

## 기본 주석
- 한줄의 주석을 작성하는 방법은 (// 주석) 방식으로 작성 

```Swift
class ViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // 기본 주석 방법
    }
}
```

- 한줄이상의 많은 내용의 주석을 작성할 때는 (/* 주석 */) 방식으로 작성

```Swift
class ViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    /*
    한 줄 이상의
    주석을 작성할 때
    사용
    */
}
```

## 마크업

### MARK
- (// MARK: 설명) 표시를 사용해서 구분을 할 수 있다

```Swift
class MarkUpTest {
    
    // MARK: Property
    let key = 1
    let value = ""
    
    // MARK: Method
    func test1() {
        
    }
    
    func test2() {
        
    }
}
```

- 위와 같이 코드를 작성할 경우 아래 사진처럼 구분을 할 수 있다.
<img width="99" alt="스크린샷 2021-11-10 오후 4 32 53" src="https://user-images.githubusercontent.com/45002556/141069393-381cf8ef-217c-426d-8348-694f11ccd373.png">

- 미니맵에서도 편하게 확인할 수 있다.
<img width="144" alt="스크린샷 2021-11-10 오후 4 34 59" src="https://user-images.githubusercontent.com/45002556/141069672-70b70512-1247-4527-a02c-a51c4de0f5f6.png">

- (// MARK: - 설명)와 같이 "-"을 추가하면 구분선이 추가된다
<img width="922" alt="스크린샷 2021-11-10 오후 4 36 44" src="https://user-images.githubusercontent.com/45002556/141069919-47765f22-0220-40a8-a8d6-08cd0bbe49cc.png">

- 또한 미니맵에서 확인 가능하다.
<img width="139" alt="스크린샷 2021-11-10 오후 4 37 09" src="https://user-images.githubusercontent.com/45002556/141069976-1459d84e-30b6-432e-8ca7-8be864e00a15.png">

### FIXME
- (FIXME: 설명) 표시를 통해 고쳐야 할 부분을 표시할 수 있다.

```Swift
class MarkUpTest {
    
    // MARK: - Property
    let key = 1
    let value = ""
    
    // MARK: - Method
    func test1() {
        
    }
    
    func test2() {
        
    }
    
    // FIXME: Bug
    func requestData() {
        
    }
}
```

- MARK와 아이콘이 다른것을 확인 할 수 있다.
<img width="126" alt="스크린샷 2021-11-10 오후 4 40 11" src="https://user-images.githubusercontent.com/45002556/141070416-ea2246dc-bad5-4aef-a030-0b8cf4453b8e.png">

### TODO
- (TODO: 설명) 표시를 통해 해야할 일을 표시할 수 있다.

```Swift
class MarkUpTest {
    
    // MARK: - Property
    let key = 1
    let value = ""
    
    // MARK: - Method
    func test1() {
        
    }
    
    func test2() {
        
    }
    
    // TODO: 할일
    func requestData() {
        
    }
}
```

- MARK와 FIXME와는 아이콘이 다른것을 확인 할 수 있다.
<img width="121" alt="스크린샷 2021-11-10 오후 4 41 51" src="https://user-images.githubusercontent.com/45002556/141070634-374e0566-1c3e-4905-b30f-fa5760116285.png">
