# Xcode Merge conflict

## Merge conflicts project.pbxproj 에러
project.pbxproj은 Xcode 구성 Bundle을 가지고 있는 파일이다. Project에 Link된 파일과 Framework, Build Setting 등을 유지하도록 하므로 .gitignore 에 추가 할 수 없다.

여러팀원들과 로직이나 기능별로 나눠서 작업을 할때 리소스 추가나 설정 등을 변경하면 Conflict가 생길 수 밖에 없어서 이것을 해결하고 Merge 하는것이 쉽지 않다.

이를 해결하기 위해 .gitattributes 파일을 만들고 project.pbxproj 파일을 Binary 처럼 인식하도록 만들어 해결한다.

## 해결방법
```
# .gitattributes 파일을 만든다
$ vim .gitattributes

# .gitattributes 파일에 다음 코드를 추가
*.pbxproj binary merge=union
```
