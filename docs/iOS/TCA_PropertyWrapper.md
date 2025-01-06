# TCA PropertyWrapper

## @ObservableState

### 사용 목적
- TCA에서 사용되는 PropertyWrapper로, 상태를 전역적으로 관리할 때 사용됩니다.
- Reducer와 결합하여 액션에 따라 상태를 변경하고, 그 변경 사항을 뷰에 반영합니다.
- TCA의 상태 모델에 속하므로, 상태의 생명 주기는 리듀서에 의해 관리됩니다. 

### 상태 변경 처리
- 상태의 변경은 Reducer의 액션을 통해 발생하며, 이 액션은 외부에서 전달됩니다.
- 상태 변경이 발생할 때, TCA의 Reducer가 호출되어 상태를 업데이트하고, 자동으로 UI가 업데이트 됩니다.

### 데이터 흐름
- TCA의 아키텍처에 따라 데이터 흐름이 더 명확한 구조를 가집니다. 상태는 Reducer에 의해 관리되며, 뷰는 액션을 통해 상태 변경을 요청합니다.

### SwiftUI의 @State와 비교
- SwiftUI에서 로컬 상태를 관리하는 데 사용됩니다.
- 주로 뷰 내에서 직접적으로 상태를 관리하고 변경할 때 사용됩니다.
- 뷰와 상태간의 데이터 흐름이 단방향이고, 뷰에서 상태를 직접 변경한다.
- @State는 간단한 로컬 상태 관리를 위한 것이고, @ObservableState는 TCA와 함께 사용하여 더 구조화된 상태 관리를 제공하는 속성 래퍼입니다. TCA를 사용할 때는 @ObservableState를 통해 상태를 관리하고, 액션을 통해 뷰와 상태 간의 관계를 명확하게 설정할 수 있습니다.

---

## @Bindable

### 사용 목적
- @Bindable은 TCA에서 사용되는 PropertyWrapper로, 주로 SwiftUI의 뷰에서 상태의 바인딩을 간편하게 처리하는데 사용됩니다. 이 PropertyWrapper는 주로 UI와 상태 간의 양방향 데이터 바인딩을 지원합니다.

### 주요 기능
- @Bindable은 UI에서 상태를 직접 수정할 수 있도록 해줍니다. 즉, 사용자가 UI 요소와 상호작용할 때 상태가 자동으로 업데이트되고, 반대로 상태가 변경되면 UI도 자동으로 업데이트됩니다.
- @Bindable을 통해서 UI요소에 변경 사항을 TCA의 액션으로 전파할 수 있습니다. 이는 상태 변경을 Reducer가 처리하도록 하여, 상태 관리가 더욱 일관되고 예층 가능하게됩니다.