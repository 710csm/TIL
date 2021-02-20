# SwiftLint
- 코드 정리 및 일관된 코드를 작성하게 해주는 라이브러리

**사용법**

1. Podfile에 pod 'SwiftLint' 추가
2. Target > build phase > new run script phase
3. run script에 ${PODS_ROOT}/SwiftLint/swiftlint를 추가

**예외처리 방법**

프로젝트에 empty 파일 생성
![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ac75fc4-0b52-4fca-814b-0564d5eed69a/_2021-02-16__10.56.46.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ac75fc4-0b52-4fca-814b-0564d5eed69a/_2021-02-16__10.56.46.png)   
파일 이름은 무조건 .swiftlint.yml로 지정    

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d564948c-0c69-4ccf-b64f-d5144f45b17c/_2021-02-16__10.58.46.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d564948c-0c69-4ccf-b64f-d5144f45b17c/_2021-02-16__10.58.46.png)
disabled_rules: 사용하지 않을 규칙   
included: 포함할 파일 정의       
excluded: 포함하지 않을 파일 정의     

예시     
disabled_rules:
- trailing_whitespace
- file_length
- identifier_name
- unused_closure_parameter

excluded:
- Pods
- smartlearning.ios/Player
- smartlearning.ios/KollusSDK
- smartlearning.iosTests
- AppDelegate.swift
- SceneDelegate.swift
