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

## Swift의 async / await과 다른 언어에서 비슷한 개념
### async/await을 사용하는 이유
- 아래와 같은 문제를 해결하기 위해 async await은 코틀린에 있는 코루틴 모델을 도입한 것
1. Swift 개발에서 Closure 및 completion handlers를 사용하는 asynchronous(비동기) 프로그래밍을 하는 경우가 많다.
2. 비동기 작업에 deeply-nested closures가 필요하다.
3. 콜백은 오류처리를 어렵고 장황하게 만든다.

### 코루틴이란?
- 코루틴은 실행과 재개를 허용함으로써 비선점형 멀티태스킹을 위한 서브루틴을 일반화한 컴퓨터 프로그램의 구성 요소이다.
    1. 루틴: 컴퓨토 프로그램에서 하나의 정리된 task이다. 프로그램은 보통 크고 작은 루틴을 결합하여 만든다.
    2. 메인루틴: 프로그램 전체의 개괄적인 동작 절차를 표시하는 루틴
    3. 서브루틴: 반복되는 특정 기능을 모아놓고 여기에 이름을 붙여 놓은 것. 코틀린에서는 함수가 대표적인 서브루틴의 예시이다.
    4. 비선점형 멀티태스킹: 특정 Task가 CPU의 사용권을 얻었을 때, 이 사용권을 강제로 뺏을 수 없는 경우
    5. 선점형 멀티태스킹: 특정 Task가 CPU의 사용권을 얻었을 때, 이 사용권을 강제로 뺏을 수 있는 경우


### 코틀린의 코루틴 
- 코틀린에서 코루틴은 비선점형 멀티태스킹이며 쓰레드는 선점형 멀티태스킹이다. -> 코틀린은 동시성을 제공하지만 병렬성을 제공하지는 않는다.
1. suspend
    - 현재 코루틴 실행을 일시중지하고 모든 로컬 변수를 저장한다.
2. resume
    - 정지된 위치부터 정지된 코루틴을 계속 실행한다.   

### 자바스크립트의 async / await
- 자바스크립트에서 비동기 처리 패턴으로 기존의 비동기 처리 방식인 콜백 함수와 프로미스의 단점을 보완하고 개발자가 읽기 좋은 코드를 작성할 수 있게 도와준다.
1. async
    - async가 붙은 함수는 프라미스를 반환하고, 프라미스가 아닌 것은 프라미스로 감싸서 반환한다.
2. await
    - await 키워드를 만나면 프라미스가 처리될 때까지 기다린다. 


### Swift의 async / await
1. async
    - 함수를 비동기 함수로 만들겠다고 알린다.
2. await
    - 비동기 함수 호출시 잠재적인 일시 중단 지점(potential suspension point)을 지정 

## 이미지 확대 방법
1. 스크롤뷰 사용
- UIScrollViewDelegate를 상속받아 maximumZoomScale 및 minimumZoomScale 값을 설정하여 이미지 확대를 구현할 수 있다. 

```swift
class ViewController: UIViewController, UIScrollViewDelegate {

    @IBOutlet weak var scrollView: UIScrollView!
    @IBOutlet weak var imgPhoto: UIImageView!
      
    override func viewDidLoad() {
        
        super.viewDidLoad()
        scrollView.delegate = self
            
        scrollView.minimumZoomScale = 1.0
        scrollView.maximumZoomScale = 6.0        
    }  
        
    func viewForZooming(in scrollView: UIScrollView) -> UIView? {
        return imgPhoto
    }
```

2. CGAffineTransformMakeScale 사용
- 
