# Designated init vs convenience init

**Designated init**

Designated init은 swift의 초기화 이니셜라이져이다. 이 init은 클래스의 모든 프로퍼티가 초기화 될 수 있도록 해야 한다. 이름은 Designated init이지만 init으로 쓴다.

```swift
class Person {
    var name: String
    var age: Int
    var gender: String

    init(name: String, age: Int, gender: String) {
        self.name = name
        self.age = age
        self.gender = gender
    }
}
```

위에 init에서 프로퍼티가 하나라도 빠지면 에러 발생

<img width="831" alt="f1" src="https://user-images.githubusercontent.com/45002556/108619299-1b18dd80-7467-11eb-85be-ff97c3b73f61.png">

**convenience init**

convenience init은 보조 이니셜라이저이다. 클래스의 원래 이니셜라이저인 init을 도와주는 역할을 한다. Designated init의 파라미터 중 일부를 기본값으로 설정해서, 이 convenience init 안에서 Designated init을 호출해 초기화를 진행할 수 있다.

따라서 convenience init을 사용하려면 Designated init이 먼저 선언되어야 한다.

```swift
class Person {
    var name: String
    var age: Int
    var gender: String

    init(name: String, age: Int, gender: String) {
        self.name = name
        self.age = age
        self.gender = gender
    }

    convenience init(age: Int, gender: String) {
        self.init(name: "zedd", age: age, gender: gender)

    }
}
```
