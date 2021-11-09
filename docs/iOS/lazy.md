# lazy variables
- 처음 사용되기 전까지 초기값이 계산되지 않는 프로퍼티
- 필요하지만 사용하기 전에 불러오기 부담스러운 것들을 lazy로 선언 

## lazy의 특징
1. lazy는 반드시 var와 함께 사용된다
    - 초기에는 값이 없고 이후에 값이 생기는 것이기 때문에 let으로 선언될 수 없다.
2. stuct, class 
    - lazy는 stuct와 class에 사용할 수 있다.
3. lazy vs computed property
    - computed property에는 lazy 키워드를 사용할 수 없다. lazy는 처음 사용될 때 메모리에 값을 올리고 그 값을 계속 사용하기 때문에 사용할 때마다 값을 연산하는 computed property에 사용할 수 없다.

```Swift
class Person {
    var name: String
    
    lazy var greeting: String = {
        return "Hello my name is \(self.name)"
    }()
}

var me = Person(name: "John")
print(me.greeting) // Hello my name is John

me.name = "James"
print(me.greeting) // Hello my name is John
```

- name 프로퍼티가 james로 변경 되어도 처음 사용할 때 메모리에 John이 올라갔기 때문에 이후에도 John이 출력된것을 확인할 수 있다. 
