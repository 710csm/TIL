# CustomStringConvertible
- Java에서 제공하는 toString 메소드와 같은 기능을 사용하게 해주는 프로토콜

## 사용방법
- CustomStringConvertible 프로토콜을 채택하고 description 변수를 정의하면된다.
 
```Swift
class MyClass: CustomStringConvertible {
    var value: Int
    var description: String { return "MyClass: \(value)" }
    
    init(value: Int) {
        self.value = value 
    }
}
```

- 호출 방법
 
```Swift
let myClass = Myclass(value: 1)
print(myClass)
// MyClass: 1
```
