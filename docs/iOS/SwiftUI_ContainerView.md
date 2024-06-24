# SwiftUI의 ContainerView

## Group
- 여러개의 View들에 동일한 속성을 부여하고 싶을 때 사용
- VStack, HStack, LazyVStack 등은 View를 최대 10개 까지 가질 수 있다, 근데 Group를 사용하면 11개 이상의 View를 가질 수 있다.

```swift
// 동일한 속성을 부여
struct ContentView: View {
    var body: some View {
        Group {
            Text("SwiftUI")
            Text("Combine")
            Text("Swift System")
        }
        .font(.headline)
        Text("Some Text")
    }
}

struct ContentView: View {
    var body: some View {
        NavigationView {
            VStack {
                Group {
                    Text("1")   
                    Text("2")
                    Text("3")      
                    Text("4")   
                    Text("5")
                    Text("6")
                    Text("7")   
                    Text("8")
                    Text("9")
                    Text("10")   
                }
        
                Group {
                    Text("11")
                }
            }
        }
    }
}
```

## GroupBox
- GropBox는 연관있는 내용을 하나로 묶은 UI이다.

```swift
struct ContentView: View {
    var body: some View {
        GroupBox {
          Text("Content")
        }
        GroupBox("title") {
          Text("Content")
        }
    }
}
```

<img width="390" alt="스크린샷 2024-06-24 오전 9 12 19" src="https://github.com/710csm/TIL/assets/45002556/e70b8d6c-b225-4601-b3c9-7c915bfef449">

```swift
struct ContentView: View {
    
    @State private var state: Bool = false
    var body: some View {
        GroupBox(label: Label("End-User Agreement", systemImage: "building.columns")) {
          ScrollView(.vertical, showsIndicators: true) {
            Text("Title")
              .font(.footnote)
          }
          .frame(height: 100)
          Toggle(isOn: $state) {
            Text("I agree to the above terms")
          }
        }
    }
}
```

<img width="388" alt="스크린샷 2024-06-24 오전 9 14 18" src="https://github.com/710csm/TIL/assets/45002556/5fd73c1c-ed0d-4c17-a8fb-1f3a68e0bd46">

## Form
- Setting 또는 Inspector 화면과 같이 View들을 그룹화할 때 사용
- Section과 같이 사용되면 쉽게 그룹화가 가능

## Section
- 계층 구조를 나타내는 컨테이너 뷰
- 직관적으로 사용 가능
- 생성자는 header와 content이며 header에는 text를 넣고 content에는 서브뷰를 배치

```swift
// Form + Section
struct ContentView: View {
    
    @State private var state: Bool = false
    var body: some View {
        Form {
            Section(header: Text("Form_Section1")) {
                Toggle("Play notification sounds", isOn: $state)
                Toggle("Send read receipts", isOn: $state)
            }
            
            Section(header: Text("Form_Section2")) {
                Text("Section2 Contents")
            }
        }
    }
}
```

<img width="377" alt="스크린샷 2024-06-24 오전 9 20 26" src="https://github.com/710csm/TIL/assets/45002556/9295b21d-d852-4e5d-bba3-c6e0be91f5b3">

```swift
// List + Section 코드
struct ContentView: View {
    
    @State private var state: Bool = false    
    var body: some View {
        List {
            Section("List_Section1") {
                Text("Content1")
            }
            Section("List_Section2") {
                Text("Content2")
            }
        }
    }
}
```

<img width="373" alt="스크린샷 2024-06-24 오전 9 21 49" src="https://github.com/710csm/TIL/assets/45002556/6cdd06d4-20c0-46d7-bc48-cae8696aa117">

```swift
// Picker + Section 코드
struct ContentView: View {
    
    enum Flavor: String, CaseIterable, Identifiable {
        case chocolate, vanilla, strawberry
        var id: Self { self }
    }
    
    @State private var selectedFlavor = Flavor.chocolate
    var body: some View {
        Picker("Flavor", selection: $selectedFlavor) {
            Section(header: Text("Picker_Section1")) {
                Text("Chocolate")
            }
            Section(header: Text("Picker_Section2")) {
                Text("Vanilla")
                Text("Strawberry")
            }
        }
    }
}
```

<img width="310" alt="스크린샷 2024-06-24 오전 9 24 42" src="https://github.com/710csm/TIL/assets/45002556/032e363e-6b42-4548-ac5f-4d243d8fdeb1">


### 참고 
- https://ios-development.tistory.com/1097
- https://seons-dev.tistory.com/entry/SwiftUI-Form-Group-GroupBox
- https://zeddios.tistory.com/1178 [ZeddiOS:티스토리]
