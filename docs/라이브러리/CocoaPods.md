# CocoaPods
- CocoaPod, Swift Package Manager 와 같은 라이브러리 관리 도구
- 라이브러리를 더 편하게 설치하고 사용할 수 있게 도와준다

## 사용법
**1. CocoaPods 설치**

```
$ sudo gem install cocoapods
```

**2. CocoaPods을 설치할 프로젝트 경로로 이동후 Podfile 생성**

```
$ pod init
```

<img width="589" alt="스크린샷 2021-05-16 오후 5 58 26" src="https://user-images.githubusercontent.com/45002556/118391644-5266e700-b670-11eb-965b-aec4d2794844.png">

- Podfile이 생긴것을 확인할 수 있다.

**3. 추가하고싶은 라이브러리를 Podfile에 입력** 

<img width="672" alt="스크린샷 2021-05-16 오후 5 59 17" src="https://user-images.githubusercontent.com/45002556/118391662-70cce280-b670-11eb-83bb-3967878e3aae.png">

- 특정 버전을 따로 지정할 수 있다.

```
pod 'RxSwift', '6.2.0'
pod 'RxCocoa', '6.2.0'
```



**4. Pod 설치**

```
$ pod install
```

**5. Pod 사용**
- xcodeproj 파일이 아닌 .xcworkspace 파일로 프로젝트를 실행해야 사용할 수 있다.

<img width="589" alt="스크린샷 2021-05-16 오후 5 58 26" src="https://user-images.githubusercontent.com/45002556/118391644-5266e700-b670-11eb-965b-aec4d2794844.png">


**6. Pod 업데이트**
- 업데이트된 버전이 있으면 최신 버전으로 업데이트

```
$ pod update
```

## 협업
- Podfile.lock을 사용하여 pod 버전을 모두 동일하게 유지할 수 있다.
