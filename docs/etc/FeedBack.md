# 질문 피드백

## UIButton의 상위 클래스
1. UIControl
    - 터치 이벤트 및 터치 상태 등을 처리
    - 버튼의 액션을 설정 및 제거
    
2. UIView
    - 화면에 content 표시, 그리기 및 애니메이션, 오토레이아웃, 제스처 인식 등 화면에 관한  것들을 담당
    
3. UIResponder
    - 이벤트에 응답 및 이벤트를 처리하는 추상 인터페이스
    
4. NSObject
    - 대부분의 Objective-C 클래스 계층 구조의 루트 클래스로, 여기서 하위 클래스는 런타임 시스템에 대한 기본 인터페이스와 Objective-C 개체로 동작하는 기능을 상속

5. UIView가 상속받는 프로토콜
    - NSCoding
    - UIAppearance
    - UIAppearanceContainer
    - UIDynamicItem
    - UITraitEnvironment 
    - UICoordinateSpace
    - UIFocusItem
    - UIFocusItemContainer
    - CALayerDelegate

## UIScrollView의 Frame과 Content
- frame Layout Guide
    - 변형되지 않은 프레임을 기반으로 한 레이아웃 가이드
    - 스크롤 뷰 자체 프레임의 Auto Layout constraint를 설정할 때 이 레이아웃 가이드를 사용하면 된다.

- content Layout Guide
    - 변형되지 않은 스크롤 뷰의 사각형 콘텐츠
    - 스크롤 뷰의 컨텐츠 영역의 Auto Layout constraint를 설정할 때 이 레이아웃 가이드를 사용하면 된다. 


## Result의 내부 구조
- success와 failure 두 가지 case가 있는 enum이다.
- 두 케이스 모두 제네릭을 사용하여 구현되므로 개발자가 정한 타입의 연관값을 가질 수 있다. 그러나 failure의 연관값은 Swift의 Error 타입을 채택한 타입이어야 한다.
