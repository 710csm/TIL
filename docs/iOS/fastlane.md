# Fastlane
- 루비 기반의 클라이언트 자동 빌드 오픈소스 라이브러리

## Fastlane을 통해 할 수 있는 작업
1. 스크린샷 및 앱스토어 업로드
2. 코드 사이닝 관리,
3. TestFlight 배포
4. 앱스토어 업로드
5. cocoapods 인스톨 과정 등

## Fastlane 설치법
- Ruby 또는 Homebrew로 설치할 수 있다.

```
// ruby
sudo gem install fastlane

// homebrew
brew install fastlane
```

## Fastlane 셋팅
fastlane을 셋팅할 프로젝트로 이동하여 다음과 같은 명령어를 입력한다.

```
fastlane init
```

명령어를 입력하면 다음과 같은 화면이 콘솔에 나타난다.

<img width="764" alt="스크린샷 2021-06-24 오후 3 26 32" src="https://user-images.githubusercontent.com/45002556/123213113-95826880-d500-11eb-81d5-6aafffdb6541.png">

1, 2, 3 을 선택하면 자동으로 Fastfile을 설정해준다.    
하지만 코드 사이닝 등의 문제가 생길 수 있어서 4번을 눌러서 우선 Fastfile을 작성하는 방법도 있다.

TestFlight에 자동으로 배포를 위해 다음과 같이 코드를 작성한다.

```
default_platform(: ios)

platform :ios do
    desc "Push a new beta build to TestFlight"
    lane :beta do
        increment_build_number(xcodeproj: "example.xcodeproj")             # 빌드번호를 자동으로 1 올려주는 코드
        build_app(workspace: "example.xcworkspace", scheme: "scheme 이름")  # 빌드
        upload_to_testflight                                               # TestFlight 업로드
    end
end
```

## 코드 사이닝
1. Using Xcode's code signing feature

```
lane :release dol
    sync_code_signing
    disable_autoatic_code_signing(path: "example.xcodeproj")
    build_app
    enable_automatic_code_signing(path: "example.xcodeproj")
    upload_to_testflight
end
```

2. Using cert and sigh
- cert will make sure you have a valid certificate and its private key installed on the local machine(cert를 통해 유효한 인증서와 해당 개인 키가 로컬 시스템에 설치되어 있는지 확인합니다.)

- sigh will make sure you have a valid provisioning profile installled locally, that matches the installed certificate(sigh을 사용해 설치된 인증서와 일치하는 올바른 프로비저닝 프로파일이 로컬에 설치되어있는지 확인할 수 있습니다.)

```
lane :beta do
    get_certificates            # invokes cert
    get_provisioning_profile    # invokes sigh
    build_app
end
```

3. Using match
- With match you store your private keys and certificates in a git repo to sync them across machines. This makes it easy to onboard new team-members and set up new Mac machines. This approach is secure and uses technology you already use.(match를 사용해 개인 키와 인증서를 git 저장소에 저장해 시스템간 동기화를 합니다. 이를 통해 새로운 팀원을 쉽게 합류시키고 새로운 Mac 머신을 설정할 수 있습니다. 이 방법은 안전하고 이미 당신이 사용중인 기술을 사용한다.)

- 코드에 match(type:) 을 추가  

```
lane :beta do
    match(type: "appstore")
    build_app(workspace: "example.xcworkspace", scheme: "scheme 이름")
    upload_to_testflight
end
```


## 참고
1. https://codesigning.guide/
2. https://littleshark.tistory.com/35
3. https://sesang06.tistory.com/124
4. https://nashorn.tistory.com/entry/Fastlane%EA%B3%BC-Match%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%95%84%EC%9D%B4%ED%8F%B0-%EC%9D%B8%EC%A6%9D%EC%84%9C-%EC%B2%98%EB%A6%AC
