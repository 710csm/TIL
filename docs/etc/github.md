# github

1. 원하는 디렉토리 이동
2. git init : 현재 디렉토리를 로컬 git 저장소라고 알려주는 것
3. git add .
4. git commit -m "commit message"
5. git remote add origin [https://github.com/710csm/RealmTest.git](https://github.com/710csm/RealmTest.git) (git 주소는 해당 레파지토리꺼로)
6. git push -u origin master(main)


## git add 취소
1. git reset (전체 파일 add 취소)
2. git reset Head 파일 (특정 파일 add 취소)

## git commit 취소
**1. git reset Head^ (가장 최신 커밋 1개 취소)**
- 가장 최근의 commit을 취소하고 워킹 디렉터리를 되돌린다.

```
// 가장 최근의 commit을 취소 (기본 옵션: --mixed)
$ git reset HEAD^
```
   
**2. git reset Head^^ (가장 최신 커밋 2개 취소)**

```
$ git commit -m "Write commit messages"
```

**3. 꺽쇠 갯수에 따라 최신 커밋을 필요한만큼 순서대로 삭제할 수 있다**

```
$ git push origin [branch name] -f
또는
$ git push origin +[branch name]
```
```
// Ex) master branch를 원격 저장소(origin)에 강제로 push
$ git push origin +[branch name]
```

## git push 취소
- 이 명령을 사용하면 자신의 local의 내용을 remote에 강제로 덮어쓰기를 하는 것이기 때문에 주의해야 한다.
- 되돌아간 commit 이후의 모든 commit 정보가 사라지기 때문에 주의해야 한다.
- 특히, 협업 프로젝트에서는 동기화 문제가 발생할 수 있으므로 팀원과 상의 후 진행하는 것이 좋다.   
    
1. 워킹 디렉터리에서 commit 되돌린다.
2. 되돌려진 상태에서 다시 commit을 한다.
3. 원격 저장소에 강제로 push 한다.

## git ignore 적용
1. git ignore 생성

```
$ touch .gitignore
```

2. ignore 파일 생성
- 필요에 따라 수정해서 사용한다


```
 ## User settings
 xcuserdata/

 ## Obj-C/Swift specific
 *.hmap

 ## App packaging
 *.ipa
 *.dSYM.zip
 *.dSYM

 ## Playgrounds
 timeline.xctimeline
 playground.xcworkspace

 # Swift Package Manager
 Packages/
 Package.pins
 Package.resolved

 # Xcode automatically generates this directory with a .xcworkspacedata file and xcuserdata
 # hence it is not needed unless you have added a package configuration file to your project
 .swiftpm

 .build/

 # CocoaPods
 Pods/

 # Carthage
 Carthage/Checkouts
 Carthage/Build/

 # Accio dependency management
 Dependencies/
 .accio/

 # fastlane
 fastlane/report.xml
 fastlane/Preview.html
 fastlane/screenshots/**/*.png
 fastlane/test_output

 # Code Injection
 #
 # After new code Injection tools there's a generated folder /iOSInjectionProject
 # https://github.com/johnno1962/injectionforxcode

 iOSInjectionProject/
 .DS_Store
 ```
