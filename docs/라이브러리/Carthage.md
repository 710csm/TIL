# Carthage
- CocoaPod, Swift Package Manager 와 같은 라이브러리 관리 도구
- CocoaPods를 사용할 경우 build 하거나 clean build folder를 할 때 시간이 오래걸린다
- 따라서 Carthage를 사용하여 사이즈가 큰 라이브러리를 관리하고 가벼운 라이브러리는 CocoaPods를 사용하여 관리

## 사용법
1. Carthage 설치
    - brew를 사용한 설치
```
brew install carthage
```

2.  Cartfile 생성
    - 다음과 같은 방법으로 파일 생성
```
// 1번
touch Cartfile
// 2번
vim Carfile
```

3. Cartfile 안에 사용할 라이브러리 입력
```
github "SwiftyJSON/SwiftyJSON" ~> 4.0
```

4. carthage update
```
carthage update
// option: iOS와 관련된것만 설치
carthage update --platform iOS
// 빌드 에러시
carthage update --use-scframeworks
```

5. 프로젝트에 라이브러리 연결
    - Link Binary With Libraries 에 Carthage -> Build -> iOS 안에 있는 .framework 파일 추가
    
6. Build Phase에 run script 명령어 추가
<img width="977" alt="스크린샷 2021-04-25 오후 6 06 37" src="https://user-images.githubusercontent.com/45002556/115987669-071d6380-a5f1-11eb-8568-a32386f9af92.png">

- /usr/local/bin/carthage copy-frameworks
- $(SRCROOT)/Carthage/Build/iOS/라이브러리명.framework

7. simulator 에러시
- Build Setting -> build option에 있는 Vaildate workspace 를 yes로 변경


## 참고 사이트
1. [우아한형제들 기술 블로그](https://woowabros.github.io/experience/2020/07/06/thiiing-ios.html)
2. [Cartage 설치 방법](https://medium.com/@jang.wangsu/ios-swift-%EC%B9%B4%EB%A5%B4%ED%83%80%EA%B3%A0-carthage-%EB%9E%80-%EC%82%AC%EC%9A%A9%EB%B2%95%EC%9D%80-%EC%98%88%EC%A0%9C-%EB%94%B0%EB%9D%BC%ED%95%98%EA%B8%B0%EA%B9%8C%EC%A7%80-127e71fdd253)

