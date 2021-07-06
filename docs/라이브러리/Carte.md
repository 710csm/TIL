# Carte
- 오픈 소스 라이센스를 정리해서 보여주는 라이브러리

## 설치법

```
pod 'Carte'

# Podfile 제일 아래쪽에 다음을 적어야 CocoaPods에서 라이브러리를 불러올 수 있다
post_install do |installer|
  pods_dir = File.dirname(installer.pods_project.path)
  at_exit { `ruby #{pods_dir}/Carte/Sources/Carte/carte.rb configure` }
end
```

## 사용법
-  다음과 같이 작성하면 CarteViewController가 라이브러리를 불러와 테이블 뷰 를 만들어준다

```Swift
let carteViewcontroller = CarteViewController()
```

## 커스터마이징
- CarteItem을 만들어 이름과 라이센스 내용을 적어서 CarteViewController에 추가하면 된다

```Swift
var item = CarteItem(name: "Carte")
item.licenseText = "The MIT License (MIT) ...."

let carteViewController = CarteViewController()
carteViewController.items.append(item)
carteViewController.items.sort {$0.name < $1.name}
```


## Carte git 주소
https://github.com/devxoul/Carte
