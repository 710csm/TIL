# Swift Package Manager
- CocoaPods, Carthage같은 dependency관리 도구
- 써드파티 앱이 아닌 애플에서 지원해주는 관리 도구이기 때문에 장기적으로 SPM을  
- 빌드시 Object 파일을 만들어, Static Library 형태로 연결됨. Static과 Dynamic 의 차이를 잘 알고 사용해야 한다.

## 사용법 1
1. File -> Add Package를 누른다
<img width="404" alt="스크린샷 2021-12-07 오전 9 26 58" src="https://user-images.githubusercontent.com/45002556/144943920-103c1d27-7aeb-4e46-a4fa-0f2eb869f284.png">

2. 검색창에 추가하려는 라이브러리 git 주소를 입력한다.
<img width="1074" alt="스크린샷 2021-12-07 오전 9 32 38" src="https://user-images.githubusercontent.com/45002556/144944353-c50345ff-4ae9-4ba3-9505-c92575ca2196.png">

3. Dependency Rule은 보통 Up to Next Major Version으로 선택해서 자동으로 설정될 수 있도록 하면된다. 이후 Add to Project에는 라이브러리를 추가하기를 원하는 프로젝트를 설정하면 된다.
<img width="452" alt="스크린샷 2021-12-07 오전 9 33 27" src="https://user-images.githubusercontent.com/45002556/144944415-05097270-79ec-40fa-bb1b-08f5b9625a65.png">

4. 필요한 라이브러리 선택 및 추가
<img width="654" alt="스크린샷 2021-12-07 오전 9 35 46" src="https://user-images.githubusercontent.com/45002556/144944632-4196bcf8-c1b7-41d8-9da4-d08b614cada7.png">

5. 프로젝트 목록에 Package Dependencies 항목이 추가되고 라이브러리가 추가되는것을 확인할 수 있다.
<img width="241" alt="스크린샷 2021-12-07 오전 9 37 16" src="https://user-images.githubusercontent.com/45002556/144944747-e050640e-988f-4c6d-851d-642e0cf87f5b.png">

## 사용법 2
- 프로젝트 설정 -> Build Phases -> Link Binary With Libraries -> "+"을 눌러서 위에 방법과 똑같이 추가할 수 있다.
<img width="398" alt="스크린샷 2021-12-07 오전 9 43 31" src="https://user-images.githubusercontent.com/45002556/144945360-b0dd68be-94f9-4b4a-aaf1-ab0595be2232.png">
  
## 사용법 3

### 기존 프로젝트의 타겟과 라이브러리 구성
- 타겟과 라이브러리가 많아질수록 n*m의 연결을 가지게된다. 따라서 라이브러리를 업데이트하거나 추가할 때 어려움이 생길 수 있다.
<img width="826" alt="스크린샷 2021-12-07 오전 10 28 35" src="https://user-images.githubusercontent.com/45002556/144949133-88803a52-29e1-4e81-b2e6-54662b6116e5.png">

### Framework Project 기반
- Swift Package Manager와 Framework의 특성을 이용해 타겟과 라이브러리 간의 연결을 훨씬 간결하게 만들 수 있습니다.
- 타겟과 라이브러리 사이에 Proxy 역할을 하는 Framework를 추가하여 다음과 같은 구조로 만들 수 있습니다. 

<img width="784" alt="스크린샷 2021-12-07 오전 10 02 01" src="https://user-images.githubusercontent.com/45002556/144946927-f276fac5-412f-4bb7-b988-1e1348c44db3.png">

- 위와 같이 구조를 만들게되면 디버깅에 필요한 라이브러리와 기능 개발에 필요한 라이브러리 등, 목적에 맞게 라이브러리를 관리 할 수 있습니다.
- 기능 개발에 필요한 라이브러리가 제거되거나 추가되더라도 쉽게 작업이 가능합니다. 

1. File -> New -> Project를 누르고 Framework를 선택해 생성합니다.
<img width="644" alt="스크린샷 2021-12-07 오전 10 11 52" src="https://user-images.githubusercontent.com/45002556/144947744-44254ec0-d61f-476a-b186-9dbda8affb22.png">

2. 이후 앞서 했던거 처럼 Framework에 사용할 라이브러리를 추가합니다.

3. 기존앱 -> 프로젝트 설정 -> General -> Framework, Libraries, embedded content에서 framework를 연결합니다
<img width="726" alt="스크린샷 2021-12-07 오전 10 17 57" src="https://user-images.githubusercontent.com/45002556/144948185-8daffe24-021d-4377-8677-2fe01c86d3b1.png">

### Local Swift Package Manager
- Xcode는 Local Swift Package도 지원합니다. Local Package를 이용하여 Package를 만들고 사용할 라이브러리를 추가 하면 이 Package를 사용하는곳에서 추가한 라이브러리들을 사용할 수 있습니다.

1. File -> new -> Package를 통해 새로운 Package를 생성한다.
<img width="529" alt="스크린샷 2021-12-07 오전 9 45 38" src="https://user-images.githubusercontent.com/45002556/144945500-3ca31a03-0062-473f-b352-2388dd58e6ab.png">

2. 생성된 Package.swift 파일에 다음과 같이 사용할 라이브러리를 추가할 수 있다.
- 특정 라이브러리는 포함된 라이브러리들 중 일부를 사용하기 위해 target에 .product(name: "FirebaseAnalytics", package: "Firebase")를 추가해야 한다.

```Swift
import PackageDescription

let package = Package(
    name: "MyLibrary",
    platforms: [.iOS(.v12)], // pltforms을 통해 OS 버전을 지정할 수 있다 
    products: [
        // Products define the executables and libraries a package produces, and make them visible to other packages.
        .library(
            name: "MyLibrary",
            targets: ["MyLibrary"]),
    ],
    dependencies: [
        // Dependencies declare other packages that this package depends on.
        .package(url: "https://github.com/onevcat/Kingfisher.git", .upToNextMajor(from: "7.0.0")),
        .package(url: "https://github.com/ReactiveX/RxSwift.git", .exact("6.2.0")),
        .package(name: "Firebase", url: "https://github.com/firebase/firebase-ios-sdk.git", .upToNextMajor(from: "8.10.0")), // name을 통해 Package 이름을 설저할 수 있다
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages this package depends on.
        .target(
            name: "MyLibrary",
            dependencies: [
                "Kingfisher",
                "RxSwift",
                .product(name: "RxCocoa", package: "RxSwift"),
                .product(name: "FirebaseAnalytics", package: "Firebase"),
                .product(name: "FirebaseCrashlytics", package: "Firebase"),
            ]
        ),
    ]
)

```
3. 이후 기존앱 -> 프로젝트 설정 -> General -> Framework, Libraries, embedded content에서 framework를 연결합니다
<img width="387" alt="스크린샷 2021-12-07 오전 10 26 33" src="https://user-images.githubusercontent.com/45002556/144948969-9378edc5-afc3-4b32-a065-5ce7358a8c36.png">

## 사용후기
- RxSwift나 Alamofire와 같은 유명하고 많이 사용되는 라이브러리들은 Swift Package Manager를 지원해주지만 아직까지는 지원하지 않는 라이브러리들이 많이 있다.
- 따라서 CocoaPods과 Swift Package Manager를 같이 쓰기로 결정하고 SPM을 지원하는 대부분의 라이브러리를 모두 CocoaPods -> SPM으로 이전했다.
- Package.swift 파일에 사용할 라이브러리를 추가할 때는 코드를 두줄 정도 작성하면 추가되고 반대로 지울 때도 간단하게 작성한 코드를 지워주고 저장하면 자동으로 라이브러리가 추가되고 제거가 되는것을 확인했다. CocoaPods은 Podfile을 작성하고 cmd 창에서 pod install 또는 pod update를 했어야했는데 SPM을 사용하니 이러한 번거로움이 제거됐다.
