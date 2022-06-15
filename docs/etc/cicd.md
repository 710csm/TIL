# Jenkins 및 Fastlane을 통한 CICD

## Jenkins 설치
- Jenkins 홈페이지에서 Mac OS용 Jenkins를 설치한다. 
- 무난하게 LTS 버전을 설치하면 된다.
- https://www.jenkins.io/download/

### 자바 설치 확인
- Jenkins를 사용하기 위해 자바 설치 확인

```
java -version
```

- 설치가 안된경우 오라클 홈페이지에서 자바 설치
- Mac OS용 x64 DMG installer를 사용해 쉽게 설치 가능
https://www.oracle.com/java/technologies/downloads/#jdk18-mac

### Jenkins 설정

```
 $ brew services start jenkins-lts 
```

- 위의 명령어로 서비스 시작
- http://localhost:8080/ 에 접속
- 접속하면 다음과 같이 비밀번호를 입력하라는 화면이 나온다

<img width="987" alt="스크린샷 2022-06-15 오전 10 57 11" src="https://user-images.githubusercontent.com/45002556/173719961-be460ef2-6cf6-48ab-b7bd-017af5c912cf.png">

- cat 명령어를 사용해 화면 가운데 나오는 주소를 입력하여 비밀번호를 알 수 있다.

```
// 예시 코드
cat /Users/choeseungmyeong/.jenkins/secrets/initialAdminPassword

```

- 기본 플러그인 설치 선택

![jenkins-install-plugins](https://user-images.githubusercontent.com/45002556/173719176-a482b5a4-038a-49b3-87e4-6bf00d112019.png)

- not now 선택

![jenkins-instance-config](https://user-images.githubusercontent.com/45002556/173720087-8bccb97a-462f-4c88-ad3d-91908464b60b.png)

- Jenkins 사용 시작 선택

<img width="934" alt="스크린샷 2022-06-15 오전 10 58 20" src="https://user-images.githubusercontent.com/45002556/173720147-8af2d089-de22-42e3-aa67-9c7ad70a14a9.png">

### 새로운 프로젝트 만들기
- create a job 또는 새로운 Item 선택

<img width="711" alt="스크린샷 2022-06-15 오전 11 00 38" src="https://user-images.githubusercontent.com/45002556/173720382-8bd28726-38e6-4b61-89ec-42693f570e18.png">

- 프로젝트명 입력 및 프리스타일 프로젝트 선택

<img width="966" alt="스크린샷 2022-06-15 오전 11 02 14" src="https://user-images.githubusercontent.com/45002556/173720492-f007f7aa-8124-47e4-ad27-7a2df92dfd6b.png">

### 환경 설정
- 설명 입력
- 깃허브 주소 입력
- 암호 및 브랜치 설정
- 빌드 환경
    1. Add timestamps to the Console Output 체크
    2. Terminate a build if it's stuck 체크 (180초)
- 빌드
    1. Execute shell 선택
    2. 커맨드 입력
        - cocoapods, carthage 등 의존성 관리도구 설치
        - iOS 프로젝트 빌드 테스트 코드 작성

```
# 코코아 팟 설치     
/usr/local/bin/pod install
or
pod install

# 빌드 테스트
xcodebuild clean test -workspace smartlearning.ios.xcworkspace -scheme smartlearning.ios -destination 'platform=iOS Simulator,name=iPhone 8'
```

- 좌측 메뉴에 Build now를 선택해 빌드에 성공과 실패 여부 확인

<img width="331" alt="스크린샷 2022-06-15 오후 1 18 40" src="https://user-images.githubusercontent.com/45002556/173737536-07f9c16a-df9b-4ed9-8f91-6402e3064f26.png">

### 빌드 트리거 설정
- 설정에 빌드 트리거 에서 GitHub hook trigger for GITScm polling 체크
- Gitlab의 경우 플러그인을 설치해야 설정할 수 있다.

<img width="799" alt="스크린샷 2022-06-15 오후 1 38 57" src="https://user-images.githubusercontent.com/45002556/173737906-cd7d66f8-1bd7-45ec-a8f6-3c87e5435dbb.png">

- 깃에서 웹훅 설정
- Jenkins 서버의 URL인 Payload URL이 필요하다. 그러나 적절한 외부 URL을 사용하여 Jenkins를 설정하지 않는 한 인스턴스 구성 단계에서 이 튜토리얼을 수행하는 동안 localhost를 하기 때문에 실제 URL이 필요하다.
- ngrok을 사용하여 해결...

### ngrok 설치
- https://ngrok.com/download
- 설치 및 add auth token
- 다음 커맨드를 통해 Forwarding url을 얻을 수 있다.

```
ngrok http 8080
```

<img width="561" alt="스크린샷 2022-06-15 오후 1 59 37" src="https://user-images.githubusercontent.com/45002556/173758483-232f4bb8-2aa2-4c80-bef0-c2e6e6cede61.png">


### Gitlab의 Integrations
- Gitlab에서는 Integrations 방법을 권장하고 있다.
- Jenkins Integreations 가이드 
    - https://docs.gitlab.com/ee/integration/jenkins.html#doc-nav
- 설정 방법
    1. gitlab 프로젝트 -> 설정 -> Integrations
    2. server URL 등록 -> ngrok에서 얻은 forwarding url을 입력
    3. 프로젝트 이름 설정
    4. 젠킨스 계정 정보 및 비밀번호를 입력
    5. 저장 및 테스트

### Gitlab의 Integrations를 사용하기 위한 Jenkins 설정
1. Jenkins 관리 -> 시스템 설정 -> Gitlab 탭
2. Enable authentication for '/project' end-point 체크
3. 커넥션 이름과 깃 url 설정
4. credentals을 추가 -> gitlab의 액세스 토큰 필요..

### 트리거 테스트
- Jenkins와 git의 설정이 모두 끝나고 테스트를 하면 자동으로 빌드 테스트를 진행하는것을 확인할 수 있다.

<img width="330" alt="스크린샷 2022-06-15 오후 3 25 46" src="https://user-images.githubusercontent.com/45002556/173758419-3c558cf4-94a6-44fb-864b-0c8d8818a273.png">

---

## Fastlane 설치를 위한 Homebrew 설치
```
/usr/bin/ruby -e \
  "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## Fastlane 설치
```
gem install fastlane
```

## Fastlane 설치 오류 (Gem::FilePermissionError)
- 참고 https://jojoldu.tistory.com/288
