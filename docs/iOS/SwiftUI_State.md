# SwiftUI의 State

## SwiftUI에서 앱의 state를 저장하는 방법
1. @State
2. @ObservedObject
3. @EnvironmentObject

## @State

```swift
struct ContentView: View {
    @State private var score = 0
}
```

- view들은 struct이고 이는 view가 바뀔 수 없다는 것을 의미한다. 하지만 @State는 해당 프로퍼티의 값이 앞으로 계속해서 바뀔 수 있음을 의미한다.
- @State를 사용하여 SwiftUI가 해당 메모리를 관리하도록 하고 있다. 프로퍼티에 @State를 명시하는 것은 해당 프로퍼티가 해당 view가 없어지기 전까지 메모리에서 영원히 존재할 수 있도록 프로퍼티에 대한 통제권을 SwiftUI애개 념겨주는 것이다.
- state가 변경이 되면 SwiftUI는 view가 state의 정보를 반영할 수 있도록 자동으로 최신 변경사항을 반영하여 view를 reload한다.
- 특정 뷰에 종속하는 단순 프로퍼티와 외부에서 절대 사용되지 않는 프로퍼티에 적합하다. 따라서 @State 프로퍼티에는 private 접근 제한자를 사용함으로써 state들이 해당 뷰의 밖으로 나가지 못하도록 고안되어있다
- @State private var classInstance = referenceTypeObject() -> 불가, 클래스의 인스턴스를 값으로 사용하려면 @ObservedObject를 사용해야 한다.  

```swift
struct ContentView: View {
    @State private var score = 0
    var body: some View {
        VStack {
            TextField(score)    // 읽기만 가능
            
            TextField($score)    // 읽기와 쓰기 가능
        }
    }
}
```

- @State 앞에 $를 붙이지 않고 사용하면 단순히 값을 읽기만 가능하고, $를 붙이면 읽기와 쓰기가 모두 가능하다.

## @ObservedObject
- 여러 프로퍼티와 메소드를 가지는커스텀 타입(reference type)이 필요할 때, 여러 view에 걸쳐서 공통으로 사용될 커스텀 타입이 필요할 때 사용한다.
- @State와 유사하지만 String, Integer와 같은 단순 로컬 프로퍼티 보다는 external reference type에 적합하다는 차이점이 있다. @ObservedObject로 쓸 타입은 ObservableObject 프로토콜을 준수해야 한다.
- observed object가 view에 중요한 데이터가 변경되었다고 알려주는 몇가지 방법들이 있지만 가장 쉬운 방법은 @Published를 사용하는 것이다.
- @Published는 objectWillChange.send()를 이용하기 위해서 사용한다. send() 함수는 변경된 사항이 있다고 알려주는 기능을 한다. @Published가 해당 변수가 변경되면 자동으로 이 함수를 호출해준다. 

## @EnvironmentObject
- @EnvironmentObject는 앱 전체 view에서 사용 가능한 value이다. 또한 전체 view가 언제든지 읽어들일 수 있는 data를 공유한다.
- 앱 내 모든 view들이 읽어야 할 중요한 model data가 있다면. view에서 view로 또, 다른 view로 전달 하는 방법도 있겠지만 그냥 모든 view가 즉각 접근 할 수 있는 environment에 넣으면 된다.
- @EnvironmentObject는 앱 내에서 많은 data를 전달 할 필요가 있을 때 편리하다. 모든 뷰들이 동일한 model을 보고 있는 상태라 가정하고, 하나의 특정한 view가 해당 model을 바꾸면 나머지 view들이 즉시 업데이트 되기 때문에 싱크가 나간 데이터 걱정을 할 필요가 없다.
- 앱 전체에 공유될 shared data에 적합.
