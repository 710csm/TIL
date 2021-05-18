# Crashlytics 
## 사용법
1. 앱등록
- 앱 번들 아이디, 앱 이름(선택), 앱스토어 아이디(선택) 등 입력

2. 구성 파일 다운로드
- GoogleService-Info.plist 다운로드 및 프로젝트의 루트로 이동

3. Firebase SDK 추가

```
// 1. pod 초기화
pod init

// 2. Firebase SDK 추가
pod 'Firebase/Analytics'

// 3. pod 설치
pod install
```

4. 초기화 코드 추가
- AppDelegate에 다음과 같이 추가 

```Swift
import UIKit
import Firebase

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        FirebaseApp.configure()
        return true
    }
}
```

## Crashlytics 초기화
1. Xcode에서 프로젝트를 열고 왼쪽 탐색기에서 프로젝트 파일을 선택
2. Select a project or target 드롭다운에서 기본 빌드 대상을 선택
3. Build Phases 탭을 선택한 다음 + > New Run Script Phase를 클릭
4. 표시되는 새 Run Script 섹션을 펼치고, Shell 필드 아래에 있는 스크립트 필드에서 새 실행 스크립트를 추가

```
${PODS_ROOT}/FirebaseCrashlytics/run
```

5. 앱의 dSYM 위치를 입력 파일로 추가하면 Crashlytics에서 대용량 앱의 dSYM을 더 빠르게 자도으로 생성할 수 있습니다. 

```
${DWARF_DSYM_FOLDER_PATH}/${DWARF_DSYM_FILE_NAME}/Contents/Resources/DWARF/${TARGET_NAME}
```

빌드 단계의 Input Files 필드에 앱의 빌드된 Info.plist 위치를 제공할 수도 있습니다.

```
$(SRCROOT)/$(BUILT_PRODUCTS_DIR)/$(INFORLIST_PATH)
```

## Firebase Dashborad 및 crashlytics
- 다음과 같이 앱이 강제 종료된 케이스와 사용자, 사용중인 플랫폼 등을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/45002556/118591244-d0490080-b7de-11eb-9f82-7ba7b9c29289.png)

![image (1)](https://user-images.githubusercontent.com/45002556/118591246-d212c400-b7de-11eb-9b41-1f1d627cf7cc.png)

![image (2)](https://user-images.githubusercontent.com/45002556/118591252-d3dc8780-b7de-11eb-8e87-2704a69fd48c.png)
