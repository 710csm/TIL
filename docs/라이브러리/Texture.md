# Texture


## Texture?
- Texture(a.k.a AsyncDisplayKit)는 부드럽고 반응이 빠른 인터페이스를 위한 iOS UI 프레임워크이다. 사용자 인터페이스를 스레드로 부터 안전하게하는 것이 주요 핵심이다. 즉, UI 구성요소에 대한 측정 및 렌더링이 백그라운드 스레드에서 동시에 발생할 수 있습니다.

## Texture의 장점
- Fast: Texture Layout API를 이용하여 코드로 작성하며 Auto-Layout 보다 레이아웃을 설계하는데 있어서 빠릅니다. 
- Asynchronous & Concurrent: 어떠한 레이아웃이든 백그라운드 스레드에서 처리하며 사용자 인터렉션을 방해하지 않으며 메인스레드에 대한 오버헤드를 줄여줍니다. 
- Declarative: 레이아웃은 변경 불가능한 데이터 구조로 선언됩니다. 따라서 레이아웃 코드를 보다 쉽게 ​​개발, 문서화, 코드 검토, 테스트, 디버그, 프로파일링 및 유지보수 관리를 할 수 ​​있습니다.
- Cacheable: 레이아웃 결과물은 변경 불가능한 데이터 구조이므로 백그라운드에서 미리 계산되고 캐싱되어 사용자인터페이스 성능을 향상시킬 수 있습니다.
- Extensible: 클래스 간 코드 공유가 용이합니다.

## Node?

<img width="738" alt="스크린샷 2021-11-09 오후 2 17 09" src="https://user-images.githubusercontent.com/45002556/140866785-b932a494-b9d4-4b56-b7b7-3ed646625c8b.png">

- 디바이스 화면상 나타나는 모든 객체는 CALayer로 표현되며, CALayer에 대한 터치 이벤트와 각종 편의기능 들을 객체로 감싼 형태가 우리가 알고 있는 UIView이다

- 하지만 View와 Layer는 Main-Thread에서 주로 작동하기 때문에 피드처럼 다양한 컨텐츠가 담긴 스크롤 형태의 UI에서 Frame Drop은 불가피합니다. 뿐만 아니라 이미지 다운로드 및 attributed text decoding와 관련 로직을 주니어 개발자들이 개발하기에는 어려움이 있다.

- Texture는 이러한 로직들과 Frame Drop을 개선하기 위해 View 객체를 감싼 Node라는 객체를 만들었고 ,화면상에 나타날 수 있는 모든 구성요소를 스레드 안정성을 유지하면서 유연하고 신속하게 처리한다.

## ASDiplayNode 서브노드 구조
<img width="793" alt="스크린샷 2021-11-09 오후 2 20 22" src="https://user-images.githubusercontent.com/45002556/140867088-faccfd03-cc92-4ad1-94e3-deb016579419.png">


## Texture Nodes
<img width="762" alt="스크린샷 2021-11-09 오후 2 21 31" src="https://user-images.githubusercontent.com/45002556/140867189-3066a921-1d30-46cf-b92a-201d5548a154.png">

- Texture는 UIKit에서 제공하는것을 동일하게 제공하고 특수한 편의기능을 제공한다 

## Texture Node Containers
<img width="767" alt="스크린샷 2021-11-09 오후 2 23 08" src="https://user-images.githubusercontent.com/45002556/140867329-db8d0b11-234c-43da-b15d-e715f0dab4c5.png">

- Node Container는 Node의 Intelligent Preloading을 자동으로 관리합니다.
- Intelligent Preloading은 모든 Node의 Layout Measurement, Data Fetching, Decoding, Rendering 이 비동기적으로 수행됨을 의미합니다.

## Node의 기본 메서드와 Life Cycle
<img width="765" alt="스크린샷 2021-11-09 오후 2 27 07" src="https://user-images.githubusercontent.com/45002556/140867642-7d0ef094-d8bc-45fc-bcc7-08fb31ec7149.png">

**Init:**
- UIKit 과는 다르게 Main Thread 가 아닌 Background Thread 에서 동작합니다.
- layer, view, gesture recognizer 등 Main Thread 에서 접근 가능한 속성들은 didLoad 메소드에서 접근해야 한다.

**didLoad:**
- initialization 이 끝이나고 실질적으로 Node 객체가 load 됐음을 의미합니다. 한번만 호출되며, 여기서 주로 Main Thread 에서 접근 가능한 속성들을 사용할 수 있다. (ex. gesture recognizer나 또는 layer, view property 접근 등등.)

**layoutSpecThatFits:**
- Texture는 UIKit 다르게 Auto-Layout 을 사용하지 않는다. 대신, Yoga Layout Flex-Box 기반으로 Layout 을 설계합니다. 해당 method 는 Background Thread 에서 동작하며, 레이아웃 설계에 대한 무거운 로직들을 처리하고 개발자 의도에 따라 설계된 레이아웃을 빌드한다.
- 순수하게 Texture에서 제공해주는 LayoutSpec 및 Layout Elements Properties 만 사용해야하며 그 이외 Main Thread에서 사용가능한 API나 GCD, Lock과 같이 Layout Building 과정에 방해되는 요소를 사용해서는 안된다.

**layout:**
- layoutSpecThatFits 에서 Layout Building 이 끝난 이후 Main Thread 에서 호출되는 메서드이다. layout 이 변경될 때 마다 호출되며, 여기선 주로 Layout Build 가 완료된 UI 를 Clip Corner한다던가 Gradient 를 추가하는 등 layout 변화에 따른 업데이트가 필요한 추가적인 요소를 처리한다.

```Swift
class ExampleNode: ASDisplayNode {   
    let imageNode = ASImageNode()   
    
    override init() {      
        super.init()      
        // TODO: Background Thread에서 동작      
        self.addSubnode(imageNode)   
    }   
    
    override func didLoad() {       
        super.didLoad()       
        // TODO: Main Thread에서 접근 가능한 Property를 사용   

    }   
    
    override func layout() {       
        super.layout()       
        // TODO: layout변화에 따른 업데이트가 필요한 추가적인 요소를 처리합니다.   
    }   

    override func layoutSpecThatFits(_ constraintedSize: ASSizeRange) -> ASLayoutSpec { 
        // TODO: 순수하게 Texture에서 제공해주는 LayoutSpec 및 Layout Elements Properties 만 사용  
        let imgLayout = ASInsetLayoutSpec(insets: .zero, child: imageNode)      
        imgLayout.style.height = .init(unit: .points, value: 500.0)      
        return imgLayout   
    }
}
```

## UI 구성 방법에 따른 레이아웃 수정 비교
**Xib 및 Storyboard의 경우**
1. Xib 및 Storyboard 파일을 엽니다. (파일을 열고 난후 랜더링하는데 무거워서 사양에 따라서 여는데 시간이 다소 걸립니다.) 
2. constraints관계를 파악합니다.
3. IBOutlet을 연결하고 constraints를 추가합니다. 
4. 코드리뷰 아니 xib 변경사항에 대해서 리뷰를 받습니다. (방법은 다양합니다. snapshot 또는 동료개발자가 직접열어 본다던가 앱에서 실행해서 검증하는 방법 etc.. )

**Autolayout의 경우**
1. constraints를 다루는 method를 찾습니다. 
2. 코드상 constraints관계를 파악합니다.
3. constraints추가 및 수정합니다. 
4. 동료로 부터 코드리뷰를 받습니다. (동료 역시 리뷰시 constraints관계를 파악해야합니다. )

**Texture Layout API의 경우**
1. 해당 UI의 layoutSpecThatsFit method로 갑니다. 
2. 수정해야하는 LayoutSpec 추적 및 파악합니다.
3. UI Components 추가합니다. 
4. 코드리뷰를 받습니다.  

## 사용법
1. Single Screen Example
- ViewController에 들어간 화면 구성요소들을 모듈화 시켜서 ViewController가 커지는것을 피할 수 있다.

```Swift
import AsyncDisplayKit

final class TestNode: ASDisplayNode {
    
    // MARK: UI
    private let imageNode: ASImageNode = {
        let node = ASImageNode()
        node.image = UIImage(named: "image")
        node.borderColor = UIColor.gray.cgColor
        node.borderWidth = 1.0
        node.cornerRadius = 15.0
        node.contentMode = .scaleAspectFit
        return node
    }()
    
    private let titleNode: ASTextNode = {
        let node = ASTextNode()
        let paragraphStyle = NSMutableParagraphStyle()
        paragraphStyle.alignment = .center
        node.attributedText = NSAttributedString(
            string: "Welcome to Texture-KR",
            attributes: [
                .font: UIFont.boldSystemFont(ofSize: 15.0),
                .foregroundColor: UIColor.gray,
                .paragraphStyle: paragraphStyle
            ]
        )
        return node
    }()
    
    
    // MARK: Initializing
    override init() {
        super.init()
        self.automaticallyManagesSubnodes = true
        self.automaticallyRelayoutOnSafeAreaChanges = true
    }
    
    // MARK: Node Life Cycle
    override func layout() {
        super.layout()
        self.imageNode.cornerRadius = 15.0
    }
    
    // MARK: Layout
    override func layoutSpecThatFits(_ constraintedSize: ASSizeRange) -> ASLayoutSpec {
        var containerInsets: UIEdgeInsets = self.safeAreaInsets
        containerInsets.left += 15.0
        containerInsets.right += 15.0
        containerInsets.top = containerInsets.bottom
        return ASInsetLayoutSpec(
            insets: containerInsets,
            child: self.contentLayoutSpec()
        )
    }
    
    private func contentLayoutSpec() -> ASLayoutSpec {
        return ASStackLayoutSpec(
            direction: .vertical,
            spacing: 10.0,
            justifyContent: .center,
            alignItems: .center,
            children: [
                self.imageLayoutSpec(),
                self.titleNode
            ]
        )
    }
    
    private func imageLayoutSpec() -> ASLayoutSpec {
        return ASRatioLayoutSpec(ratio: 1.0, child: self.imageNode).styled {
            $0.flexShrink = 1.0
        }
    }
}

```

```Swift
final class TestNodeController: ASDKViewController<TestNode> {
    
    // MARK: Initializing
    override init() {
        super.init(node: TestNode())
        self.node.backgroundColor = .white
    }
 
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```
    

2. List Screen Example
- ASViewController는 제네릭형태로 ASDisplayNode의 모든 Subclass를 받아서 사용할 수 있습니다. ASTableNode는 ASDisplayNode의 subclass이므로 ASViewController를 만들때 ASVIewController<ASTableNode> 를 상속받아 ViewController를 만듭니다.

```Swift
import AsyncDisplayKit

final class TestCellNode: ASCellNode {
    // MARK: - UI
    private let imageNode: ASImageNode = {
        let node = ASImageNode()
        node.image = UIImage(named: "Image")
        node.borderColor = UIColor.gray.cgColor
        node.borderWidth = 1.0
        node.contentMode = .scaleAspectFit
        return node
    }()
    
    private let titleNode: ASTextNode = {
        let node = ASTextNode()
        node.maximumNumberOfLines = 1
        return node
    }()
    
    // MARK: Initializing
    init(item: String) {
        super.init()
        self.automaticallyManagesSubnodes = true
        self.selectionStyle = .none
        self.backgroundColor = .white
        self.titleNode.attributedText = NSAttributedString(
            string: item,
            attributes: [
                .font: UIFont.boldSystemFont(ofSize: 15.0),
                .foregroundColor: UIColor.gray
            ]
        )
    }
    // MARK: Node Life Cycle
    override func layout() {
        super.layout()
        self.imageNode.cornerRadius = 15.0
    }
    // MARK: Layout
    override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
        return ASInsetLayoutSpec(
            insets: UIEdgeInsets(top: 15.0, left: 15.0, bottom: 15.0, right: 15.0),
            child: self.contentLayoutSpec()
        )
    }
    
    private func contentLayoutSpec() -> ASLayoutSpec {
        return ASStackLayoutSpec(
            direction: .horizontal,
            spacing: 10.0,
            justifyContent: .start,
            alignItems: .stretch,
            children: [
                self.imageLayoutSpec().styled {
                    $0.flexBasis =  ASDimension(unit: .fraction, value: 0.3)
                },
                self.titleNode.styled {
                    $0.flexBasis =  ASDimension(unit: .fraction, value: 0.7)
                }
            ]
        )
    }
    
    private func imageLayoutSpec() -> ASLayoutSpec {
        return ASRatioLayoutSpec(ratio: 1.0, child: self.imageNode)
    }
}

 ```
 
 ```Swift
import AsyncDisplayKit

final class TestNodeController: ASDKViewController<ASTableNode> {
    
    // MARK: Properties
    var items: [String] = [
        "Welcome to Texture-KR",
        "Welcome to Texture-KR",
        "Welcome to Texture-KR, long test!!!!!!!!!!!!!!!!!!!!!!!!!!!!!"
    ]
    
    // MARK: Initializing
    override init() {
        super.init(node: ASTableNode(style: .plain))
        self.node.backgroundColor = .white
        self.node.dataSource = self
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}

// MARK: -  ASTableDataSource
extension TestNodeController: ASTableDataSource {
    func numberOfSections(in tableNode: ASTableNode) -> Int {
        return 1
    }
    
    func tableNode(_ tableNode: ASTableNode, numberOfRowsInSection section: Int) -> Int {
        return items.count
    }
    
    func tableNode(_ tableNode: ASTableNode, nodeBlockForRowAt indexPath: IndexPath) -> ASCellNodeBlock {
        let item = self.items[indexPath.row]
        return {
            return TestCellNode(item: item)
        }
    }
}
 ```

<img width="535" alt="스크린샷 2021-11-09 오후 3 12 32" src="https://user-images.githubusercontent.com/45002556/140871873-4aafb332-bd0e-4e33-a58f-2528f094bae1.png">

- Texture를 사용하여 만든 Table View

## Texture 기반 라이브러리
- [RxTextre](https://github.com/OhKanghoon/RxTexture)
- [RxDataSource-Texture](https://github.com/OhKanghoon/RxTexture)

## 참고
- [Texture-KR](https://texture-kr.gitbook.io/wiki/)
