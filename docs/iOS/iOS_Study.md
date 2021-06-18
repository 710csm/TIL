# iOS 스터디

## ARC
- Auto Reference Counting 이란 자동으로 메모리를 관리해주는 방식을 말한다. 참조 카운팅이 0이 될때 메모리에서 해제한다

---

### ARC의 동작 원리
- 클래스의 새로운 인스턴트를 만들때 ARC는 인스턴스의 정보를 저장하기 위해 메모리를 할당한다. 또한 ARC는 인스턴스가 더 이상 사용되지 않는다고 판단하면 메모리를 해제합니다. 레퍼런스 프로퍼티에 인스턴스를 할당하면 ARC는 참조되는 프로퍼티의 갯수를 카운팅하여 참조하는 모든 변수가 인스턴스를 해제하기 전에 ARC는 인스턴스를 메모리에서 해제하지 않습니다.

---

### ARC의 동작 시점
- 컴파일 시점에 동작한다.
![arc](https://user-images.githubusercontent.com/45002556/108618480-1bfb4080-7462-11eb-8b11-bad943d5f212.png)

---

## Frame과 Bounds의 차이는 무엇인가요?
**Frame**
- SuperView 좌표 시스템 내에서의 view의 위치(origin)와 크기(size)

**Bounds**
- View 자기 자신의 좌표시스템에서의 위치와 크기, 부모와의 위치 관계와는 아무런 관계가 없다.
- bounds의 origin을 변경한다는 것은 곧, subview들이 화면상에서 drawing 되는 위치가 변경됨을 의미한다
- 이게 subview들의 frame 값을 변화 시키는게 아니다. 부모뷰 자표측이 변하면서 subview가 그려져야하는 위치가 달라졌기 때문이다.
- ScrollView/TableView 등을 스크롤 할 때, scrollView.bounds가 변하고, 그리하여 subview 들의 그려지는 위치가 달라지는 것이 대표적인 예이다.

---

## Autolayout이란?
뷰에 주어진 제약조건에 따라(뷰들 간의 관계를 ) 뷰의 크기와 위치를 동적으로 계산해 배치하는 것으로, 외부 또는 내부의 변화에 동적으로 반응하여 유저 인터페이스를 구성합니다.

## Autolayout Constraint의 Priority의 개념이 무엇이고, 어떤 상황에 사용하나요?
말그대로 제약들간의 우선순위를 말한다.

다수의 뷰들에 여러 제약이 걸려있을 때, 보통은 제약간의 충돌이 일어나지 않게끔 제약들을 설계하는게 일반적이지만, 상황에 따라서는 뷰들의 크기가 유동적으로 변하는 경우가 있는데, 이럴때 어떤 제약들이 서로간에 충돌이 일어나는 경우가 있을 수 있다.

이럴때에는 어떤 제약의 우선순위를 더 우위에 둘것이냐를 결정해서 이러한 충돌을 해결할 수 있다.

---

## UITableView를 구성할때 셀의 컨텐츠에 따라 높이를 설정하고 싶다면 어떻게 해야하나요?
델리게이트 메서드 rowHeight에서는 UITableView.automaticDimension 값을 리턴하고,
estimatedRowHeight에서는 셀의 예측 높이값을 리턴한다. 이렇게 하면 오토레이아웃 테이블뷰 셀 구현이 가능하다.
마찬가지로 테이블뷰 셀은, 고정 높이가 아닌, 셀 안의 서브뷰들의 제약 구성으로 셀의 크기가 결정될 수 있도록 해야한다.

---

## Delgate 장점
객체의 행동을 단순화하면서 객체 간의 결합성을 최소화시키는데 있다.

---

## ViewController의 생명주기
![viewLifeCycle](https://user-images.githubusercontent.com/45002556/108618534-7399ac00-7462-11eb-9753-2c66dca88bd9.png)

**loadView**
- 컨트롤러가 관리하는 뷰를 만든다. 뷰컨트롤러가 생성되고 순차적으로 완성됐을 때만 호출된다.

**viewDidLoad**
- 컨트롤러의 뷰가 메모리에 올라간 뒤에 호출된다. 뷰가 생성될 때만 호출된다.

**viewWillAppear**
- 화면에 뷰가 표시될 때마다 호출된다. 이 단계는 뷰는 정의된 바운드를 가지고 있지만 화면. 회전은 적용되지 않는다.

**viewWillLayoutSubviews**
- 뷰컨트롤러에게 그 자식뷰의 레이아웃을 조정하는 것에 대한 것을 알려주기 위해 호출된다. 이 메소드는 frame이 바뀔때마다 호출된다.

**viewDidLayoutSubview**
- 뷰가 그 자식 뷰의 레이아웃에 영향을 준 것을 뷰컨트롤러에게 알려주기 위해 허출된다. 뷰가 그 잣긱 view의 레이아웃을 바꾸고난 뒤에 추가적인 변경을 하고 싶을때 사용하는 이벤트 함수

**viewDidAppear**
- 뷰가 나타났다는 것을 컨트롤러에게 알리는 역할을 한다. 호출되는 시점으로는 뷰가 화면에 나타난 직후에 실행된다.

**viewWillDisAppear**
- 뷰가 사라지기 직전에 호출되는 함수이다. 뷰가 삭제 되려고 하고있는 것을 viewController에게 알린다.

**viewDidDisappear**
- ViewController에게 view가 제거되었음을 알린다. 호출 시점은 viewWillDisAppear 다음에 호출된다.

---

## Xib
- iOS 앱에서 UI를 만드는 방법중 하나
- segue 방식을 사용하지 않을때 사용
- 화면을 재사용 할 수 있다

### 사용법
1. xib파일 생성
<img width="507" alt="스크린샷 2021-05-16 오후 5 35 56" src="https://user-images.githubusercontent.com/45002556/118391030-3ada2f00-b66d-11eb-8cd7-df88dcb3de71.png">
- Also create XIB file 체크

2. 사용할 cell을 Register 시킨다

```Swift
tableView.register(
    UINib(nibName: UserTableViewCell.identifier, bundle: nil),
    forCellReuseIdentifier: UserTableViewCell.identifier
)

tableView.register(
    UINib(nibName: DeviceTableViewCell.identifier, bundle: nil),
    forCellReuseIdentifier: DeviceTableViewCell.identifier
)
```

3. identifier 값 설정    
<img width="262" alt="스크린샷 2021-05-16 오후 5 49 07" src="https://user-images.githubusercontent.com/45002556/118391393-06677280-b66f-11eb-8b9c-70b2416110e2.png">


4. 테이블 뷰에서 설정
- 기존의 방법과 같은 방식

```Swift
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    switch indexPath {
    case IndexPath(row: 0, section: 0):
        let cell = tableView.dequeueReusableCell(
            withIdentifier: UserTableViewCell.identifier,
            for: indexPath
        ) as UserTableViewCell
        // do something
        return cell
    case IndexPath(row: 0, section: 1):
        let cell = tableView.dequeueReusableCell(
            withIdentifier: DeviceTableViewCell.identifier,
            for: indexPath
        ) as DeviceTableViewCell
        // do something
        return cell
    default: 
        UITableViewCell()
    }
}
```

---

## ViewController의 생명주기

![viewLifeCycle](https://user-images.githubusercontent.com/45002556/108618534-7399ac00-7462-11eb-9753-2c66dca88bd9.png)

**loadView**
- 컨트롤러가 관리하는 뷰를 만든다. 뷰컨트롤러가 생성되고 순차적으로 완성됐을 때만 호출된다.

**viewDidLoad**
- 컨트롤러의 뷰가 메모리에 올라간 뒤에 호출된다. 뷰가 생성될 때만 호출된다.

**viewWillAppear**
- 화면에 뷰가 표시될 때마다 호출된다. 이 단계는 뷰는 정의된 바운드를 가지고 있지만 화면. 회전은 적용되지 않는다.

**viewWillLayoutSubviews**
- 뷰컨트롤러에게 그 자식뷰의 레이아웃을 조정하는 것에 대한 것을 알려주기 위해 호출된다. 이 메소드는 frame이 바뀔때마다 호출된다.

**viewDidLayoutSubview**
- 뷰가 그 자식 뷰의 레이아웃에 영향을 준 것을 뷰컨트롤러에게 알려주기 위해 허출된다. 뷰가 그 잣긱 view의 레이아웃을 바꾸고난 뒤에 추가적인 변경을 하고 싶을때 사용하는 이벤트 함수

**viewDidAppear**
- 뷰가 나타났다는 것을 컨트롤러에게 알리는 역할을 한다. 호출되는 시점으로는 뷰가 화면에 나타난 직후에 실행된다.

**viewWillDisAppear**
- 뷰가 사라지기 직전에 호출되는 함수이다. 뷰가 삭제 되려고 하고있는 것을 viewController에게 알린다.

**viewDidDisappear**
- ViewController에게 view가 제거되었음을 알린다. 호출 시점은 viewWillDisAppear 다음에 호출된다.

---

## Layout 구현
- 코드로 AutoLayout을 구현할 때 알아보기가 힘들고 유지보수에 어려움이 있다
- SnapKit, FlexLayout과 같은 라이브러리를 사용해서 보기에 좋고 성능도 더 좋게 layout을 구현할 수 있다.
- 협업을 할 때 FlexLayout과 같은 새로운 기술을 도입할때는 러닝커브가 있어서 주의가 필요하다  (FlexLayout, Texture 등 yoga 관련 프레임워크)

### AutoLayout

<img width="833" alt="스크린샷 2021-05-19 오후 6 48 36" src="https://user-images.githubusercontent.com/45002556/118792787-f2bc4600-b8d2-11eb-8b55-1e413af64001.png">

### SnapKit

<img width="515" alt="스크린샷 2021-05-19 오후 6 48 51" src="https://user-images.githubusercontent.com/45002556/118792804-f64fcd00-b8d2-11eb-8984-83656cf3102e.png">

### FlexLayout

<img width="815" alt="스크린샷 2021-05-19 오후 6 49 04" src="https://user-images.githubusercontent.com/45002556/118792810-f6e86380-b8d2-11eb-8013-0b6a5d5fe231.png">

--- 

## Storyboards vs Xib
- 규모가 큰 프로젝트를 진행할 때는 Storyboards 보다 Xib가 더 좋다. 규모가 큰 프로젝트에 Storyboards를 사용할 경우 뷰가 많아져서 보기 힘들고 merge에 어려움을 겪을 수 있다. 
- 혼자 개발하거나 규모가 작은 프로젝트를 진행하는 경우 Storyboards를 사용하는게 좋다. Storyboards를 사용하면 개발 시간을 줄일 수 있다.

### Storyboards
- Storyboards: 스토리보드는 여러 뷰와 뷰 사이의 전환을 배치하기 위한 시각적 도구.
- 앱을 빌드하지 않고 뷰의 flow를 확인하고 싶을 때 사용하면 좋다. 
- 코드의 양을 줄이는 경우 도움이 될 수 있다.
- 빠르게 프로토타입을 만들어야 할 때 쉽게 만들 수 있다.
- 코드로 오토 레이아웃을 만드는 것보다 쉽다.

### XIBs
- XIBs(or Nibs): 각 XIB 파일은 단일 뷰 요소에 해당하며 인터페이스 빌더에 배치될 수 있으므로 시작적 도구로도 사용할 수 있다.
- 각각의 뷰가 xib를 가지고 있어서 파일이 많아진다. 하지만 개발하기 쉽고 테스트와 디버그에 용이하다.
- 모달 보기, 로그인/회원가입 화면, 뷰를 재사용 할 때(table view cell)에 사용하면 좋다. 개발 시간을 줄일 수 있다.
- 스토리보드와 마찬가지로 시작적인 도구를 사용해 무엇을 만들고 있는지 쉽게 파악 가능하다.
- 스토리보드보다 쉽게 오토 레이아웃을 사용 할 수 있다.

