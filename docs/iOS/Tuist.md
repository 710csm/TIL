# Tuist

## 설치법

```
bash <(curl -Ls https://install.tuist.io)
```

## 프로젝트 생성
- init 명령은 Info.plist 파일, AppDelegate.swift, 테스트 파일 및 프로젝트의 정의를 포함하는 Project.swift를 포함하는 iOS 응용 프로그램을 부트스트랩합니다.

```
mkdir MyApp
cd MyApp
tuist init --platform ios
```

```
tuist generate

// 프로젝트 파일만 생성하고 싶을 때
tuist generate --project-only
```

## 기존 프로젝트에서 생성할 경우
1. xcodeproj파일과 xcworkspace 파일을 제거한다.
2. 이후 비어있는 Tuist 매니페스트 파일을 생성한다.

```
touch Project.swift
```

3. tuist edit 명령어를 통해 편집 가능하다. 명령어를 터미널에 실행하면 메시지들과 함께 Tuist 다운로드와 업데이트가 진행된다. 프로젝트 파일 편집을 시작할 수 있는 생성된 환경에서 Xcode가 열립니다. 시작하려면 프로젝트 탐색기에서 Project.swift를 선택합니다.

```
tuist edit 
```

4. ProjectDescription 임포트하기

```Swift
import ProjectDescription
```

5. 프로젝트 정의하기
- 기본 프로젝트 및 작업 공간 컨테이너를 나타내는 프로젝트를 만듭니다. 원하는 이름으로 지정할 수 있지만 Tuist는 project라는 이름을 추천합니다.
- name은 프로젝트와 워크스페이스 파일들의 이름을 나타낸다.
- organizationName은 각 파일들의 맨위에 추가되는 저작권을 보여줍니다. organizationName을 조직 이름으로 수정합니다.
- setting은 다른 프로젝트 설정을 지정할 수 있게해줍니다.
- target은 프로젝트와 관련된 target 리스트를 나타냅니다.

```Swift
let project = Project(
    name: "MyApp",
    organizationName: "<YOUR_ORG_NAME_HERE>",
    settings: nil,
    targets: []
)
```

6. targets 배열에 다음 내용을 추가하기
- infoPlist에 경로 또는 .default를 설정
- sources에 와일드카드 패턴을 사용해 원본 디렉터리의 모든 원본 파일을 지정합니다. 또한 이러한 디렉토리는 프로젝트 생성 후 Xcode의 그룹이 됩니다.
- resource에도 와일드카드 패턴을 사용하여 리소스 디렉토리의 모든 원본 파일을 지정합니다. 또한 이러한 디렉토리는 프로젝트 생성 후 Xcode의 그룹이 됩니다.
- dependencies에는 종속성을 지정합니다.

```Swift
Target(
  name: "MyApp",
  platform: .iOS,
  product: .app,
  bundleId: "<YOUR_BUNDLE_ID_HERE>",
  infoPlist: "MyApp/Info.plist",
  sources: ["MyApp/Source/**"],
  resources: ["MyApp/Resources/**"],
  dependencies: [],
  settings: nil
)
```

7. 프로젝트 생성 전에 tuist lint project 명령어를 통해 유효성을 확인할 수 있다

```
tuist lint project
```

8. 프로젝트 생성
```
tuist generate
```

## 프로젝트 구조
1. Project.swift
 - Project.swift파일은 Tuist에게 Xcode 프로젝트를 어떻게 구성하면 되는지 알려주는 매니페스트이다.
 - Project.app(): Project+Templates.swift에 정의된 함수
 
 ![1_HI6qyuO6xM0cdHOWqTXYZA](https://user-images.githubusercontent.com/45002556/141253176-c4e09636-303e-4cd7-87a4-a842f1ce642d.png)

Tuist는 Project description helper를 지원합니다. 기본으로 정의된 템플릿 코드들은 활용하기 어려워서, 제거 후 원하는 프로젝트 구성에 맞게 템플릿 코드를 작성하여 활용하는것이 좋다.


2. Derived 폴더
- Info.plist 파일과 Bundle 관련 코드가 들어있는 폴더

## Add Tuist badge
[![Tuist badge](https://img.shields.io/badge/Powered%20by-Tuist-blue)](https://tuist.io) 

## 참고
[Tuist 문서](https://docs.tuist.io)
