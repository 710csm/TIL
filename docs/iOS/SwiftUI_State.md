# SwiftUI의 State

## SwiftUI에서 앱의 state를 저장하는 방법
1. @State
2. @Binding
3. @ObservedObject
4. @StateObject
5. @EnvironmentObject

## @State
- view들은 struct이고 이는 view가 바뀔 수 없다는 것을 의미한다. 하지만 @State는 해당 프로퍼티의 값이 앞으로 계속해서 바뀔 수 있음을 의미한다.
- @State를 사용하여 SwiftUI가 해당 메모리를 관리하도록 하고 있다. 프로퍼티에 @State를 명시하는 것은 해당 프로퍼티가 해당 view가 없어지기 전까지 메모리에서 영원히 존재할 수 있도록 프로퍼티에 대한 통제권을 SwiftUI에게 념겨주는 것이다.
- state가 변경이 되면 SwiftUI는 view가 state의 정보를 반영할 수 있도록 자동으로 최신 변경사항을 반영하여 view를 reload한다.
- 특정 뷰에 종속하는 단순 프로퍼티와 외부에서 절대 사용되지 않는 프로퍼티에 적합하다. 따라서 @State 프로퍼티에는 private 접근 제한자를 사용함으로써 state들이 해당 뷰의 밖으로 나가지 못하도록 고안되어있다
- @State private var classInstance = referenceTypeObject() -> 불가, 클래스의 인스턴스를 값으로 사용하려면 @ObservedObject를 사용해야 한다.
- @State 앞에 $를 붙이지 않고 사용하면 단순히 값을 읽기만 가능하고, $를 붙이면 읽기와 쓰기가 모두 가능하다.  

```swift
struct ContentView: View {
    @State private var score = 0
    
    var body: some View {
        VStack {
            TextField(score)     // 읽기만 가능
            
            TextField($score)    // 읽기와 쓰기 가능
        }
    }
}
```
## @Binding
- @Binding은 SwiftUI에서 상태를 공유하고 두 개의 뷰 간에 데이터를 연결하는 데 사용되는 프로퍼티래퍼이다. 이 프로퍼티래퍼는 주로 부모 뷰와 자식 뷰 간에 상태를 공유할 때 유용하다.
- @Binding은 뷰모 뷰의 상태를 자식 뷰에서 읽고 쓸 수 있게 해줍니다. 즉, 부모 뷰의 속성을 자식 뷰에서 직접 수정할 수 있다.
- @Binding을 사용하면 부모 뷰와 자식 뷰 간에 양방향 데이터 흐름을 쉽게 관리할 수 있다.

```swift
// 부모 뷰에서 설정
struct ParentView: View {
    @State private var isToggled: Bool = false

    var body: some View {
        VStack {
            ToggleView(isToggled: $isToggled) // Binding 전달
            Text("Toggle is \(isToggled ? "ON" : "OFF")")
        }
    }
}

// 자식 뷰에서 설정
struct ToggleView: View {
    @Binding var isToggled: Bool // Binding 선언

    var body: some View {
        Toggle("Toggle Me", isOn: $isToggled) // Binding 사용
            .padding()
    }
}
```
### @State와 @Binding의 차이점
- 둘 다 모두 SwiftUI에서 상태를 관리하기 위해 사용되는 두 가지 프로퍼티래퍼이다. 비슷한 역할을 하지만, 사용 목적과 상황에 따라 다르다.
- @State는 뷰가 상태의 소유자임을 나타낸다. 즉, @State는 해당 상태를 정의하고 관리하는 뷰에서 사용된다. 일반적으로 뷰 내부에서 초기화되며, 뷰가 소멸될 때 상태도 함께 소멸한다.

```swift
struct ExampleView: View {
    @State private var counter: Int = 0 // 상태의 소유자

    var body: some View {
        Button("Increment") {
            counter += 1
        }
    }
}
```

- @Binding은 뷰가 상태의 관찰자임을 나타낸다. 즉, 부모 뷰에서 전달된 상태를 참조하며, 부모 뷰의 상태를 변경할 수 있다. 일반적으로 자식 뷰에서 사용되며 부모 뷰로부터 주입받는 상태를 수정하는 데 사용된다.

```swift
struct ParentView: View {
    @State private var counter: Int = 0

    var body: some View {
        ChildView(counter: $counter) // Binding 전달
    }
}

struct ChildView: View {
    @Binding var counter: Int // 부모에서 전달받은 상태

    var body: some View {
        Button("Increment") {
            counter += 1 // 부모의 상태를 변경
        }
    }
}
```

## @ObservedObject
- 여러 프로퍼티와 메소드를 가지는 커스텀 타입(reference type)이 필요할 때, 여러 view에 걸쳐서 공통으로 사용될 커스텀 타입이 필요할 때 사용한다.
- @State와 유사하지만 String, Integer와 같은 단순 로컬 프로퍼티 보다는 external reference type에 적합하다는 차이점이 있다. @ObservedObject로 쓸 타입은 ObservableObject 프로토콜을 준수해야 한다.
- observed object가 view에 중요한 데이터가 변경되었다고 알려주는 몇가지 방법들이 있지만 가장 쉬운 방법은 @Published를 사용하는 것이다.
- @Published는 objectWillChange.send()를 이용하기 위해서 사용한다. send() 함수는 변경된 사항이 있다고 알려주는 기능을 한다. @Published가 해당 변수가 변경되면 자동으로 이 함수를 호출해준다.

```swift
struct ContentView: View {
    @ObservedObject var viewModel: SomeViewModel
    
    init(viewModel: SomeViewModel) {
        _viewModel = ObservedObject(wrappedValue: viewModel)
    }
} 
```

## @StateObejct
- @StateObejct는 SwiftUI에서 상태 관리를 위해 사용하는 프로퍼티래퍼이다. 주로 뷰에서 상태를 관리할 필요가 있을 때 사용된다.
- @StateObejct는 뷰 모델과 같은 상태 객체를 선언할 때 사용된다. 이 객체는 ObservableObject 프로토콜을 준수해야 하며, 상태 변화에 따라 뷰를 자동으로 업데이트 한다.
- @StateObejct는 뷰가 해당 객체의 소유자임을 나타낸다. 즉, 뷰가 생성될 때 해당 객체를 초기화하고 뷰가 소멸할 때 같이 소멸한다.
- ObservableObject를 준수하는 클래스에서 @Published 속성을 사용하면, 해당 속성이 변경될 때 뷰가 자동으로 업데이트 된다.

```swift
struct ContentView: View {
    @StateObject var viewModel: SomeViewModel
    
    init(viewModel: SomeViewModel) {
        // 언더바(_)를 붙인 변수는 프로퍼티 래퍼의 내부 값에 접근하는 방법. 즉, _viewModel은 @StateObject 래퍼가 관리하는 실제 상태 객체를 가리킵니다. 반면, viewModel은 일반적으로 사용자가 접근하는 외부 프로퍼티입니다.
        _viewModel = StateObject(wrappedValue: viewModel)
    }
}
```

### @ObservedObject와 @StateObject의 차이점
- 둘 다 모두 SwiftUI에서 상태를 관리하는데 사용되지만, 사용 목적과 소유권 개념에 있어서 차이점이 있다
- @StateObject는 뷰가 상태 객체의 소유자임을 나타낸다. 즉, 뷰가 생성될 때 해당 객체를 초기화 하고, 뷰가 소멸할 때 객체도 같이 소멸된다.

```swift
class ViewModel: ObservableObject {
    @Published var value: String = ""
}

struct ContentView: View {
    @StateObject var viewModel = ViewModel() // 소유 및 초기화

    var body: some View {
        Text(viewModel.value)
    }
}
```

- @ObservedObject는 뷰가 상태 객체의 관찰자임을 나타낸다. 즉, 뷰는 객체의 상태 변화를 감지하지만 객체의 소유자는 아닙니다. 이미 존재하는 객체를 관찰할 때 사용, 다른 뷰에서 전달된 객체를 사용할 때 적합

```swift
class ViewModel: ObservableObject {
    @Published var value: String = ""
}

struct ContentView: View {
    @ObservedObject var viewModel: ViewModel // 관찰만 함

    var body: some View {
        Text(viewModel.value)
    }
}

struct ParentView: View {
    @StateObject var viewModel = ViewModel() // 소유 및 초기화

    var body: some View {
        ContentView(viewModel: viewModel) // 자식 뷰에 전달
    }
}
```

## @EnvironmentObject
- @EnvironmentObject는 앱 전체 view에서 사용 가능한 value이다. 또한 전체 view가 언제든지 읽어들일 수 있는 data를 공유한다.
- 앱 내 모든 view들이 읽어야 할 중요한 model data가 있다면. view에서 view로 또, 다른 view로 전달 하는 방법도 있겠지만 그냥 모든 view가 즉각 접근 할 수 있는 environment에 넣으면 된다.
- @EnvironmentObject는 앱 내에서 많은 data를 전달 할 필요가 있을 때 편리하다. 모든 뷰들이 동일한 model을 보고 있는 상태라 가정하고, 하나의 특정한 view가 해당 model을 바꾸면 나머지 view들이 즉시 업데이트 되기 때문에 싱크가 나간 데이터 걱정을 할 필요가 없다.
- 앱 전체에 공유될 shared data에 적합.
