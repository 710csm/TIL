# iOS 및 Swift 기초

## ARC
- Auto Reference Counting 이란 자동으로 메모리를 관리해주는 방식을 말한다. 참조 카운팅이 0이 될때 메모리에서 해제한다

### ARC의 동작 원리
- 클래스의 새로운 인스턴트를 만들때 ARC는 인스턴스의 정보를 저장하기 위해 메모리를 할당한다. 또한 ARC는 인스턴스가 더 이상 사용되지 않는다고 판단하면 메모리를 해제합니다. 레퍼런스 프로퍼티에 인스턴스를 할당하면 ARC는 참조되는 프로퍼티의 갯수를 카운팅하여 참조하는 모든 변수가 인스턴스를 해제하기 전에 ARC는 인스턴스를 메모리에서 해제하지 않습니다.

### ARC의 동작 시점
- 컴파일 시점에 동작한다.
![arc](https://user-images.githubusercontent.com/45002556/108618480-1bfb4080-7462-11eb-8b11-bad943d5f212.png)

## Frame과 Bounds의 차이는 무엇인가요?
**Frame**
- SuperView 좌표 시스템 내에서의 view의 위치(origin)와 크기(size)

**Bounds**
- View 자기 자신의 좌표시스템에서의 위치와 크기, 부모와의 위치 관계와는 아무런 관계가 없다.
- bounds의 origin을 변경한다는 것은 곧, subview들이 화면상에서 drawing 되는 위치가 변경됨을 의미한다
- 이게 subview들의 frame 값을 변화 시키는게 아니다. 부모뷰 자표측이 변하면서 subview가 그려져야하는 위치가 달라졌기 때문이다.
- ScrollView/TableView 등을 스크롤 할 때, scrollView.bounds가 변하고, 그리하여 subview 들의 그려지는 위치가 달라지는 것이 대표적인 예이다.

## Autolayout이란?
뷰에 주어진 제약조건에 따라(뷰들 간의 관계를 ) 뷰의 크기와 위치를 동적으로 계산해 배치하는 것으로, 외부 또는 내부의 변화에 동적으로 반응하여 유저 인터페이스를 구성합니다.

## Autolayout Constraint의 Priority의 개념이 무엇이고, 어떤 상황에 사용하나요?
말그대로 제약들간의 우선순위를 말한다.

다수의 뷰들에 여러 제약이 걸려있을 때, 보통은 제약간의 충돌이 일어나지 않게끔 제약들을 설계하는게 일반적이지만, 상황에 따라서는 뷰들의 크기가 유동적으로 변하는 경우가 있는데, 이럴때 어떤 제약들이 서로간에 충돌이 일어나는 경우가 있을 수 있다.

이럴때에는 어떤 제약의 우선순위를 더 우위에 둘것이냐를 결정해서 이러한 충돌을 해결할 수 있다.

## UICollectionViewLayout 클래스에 prepare 메소드는 어떤 역할을 하나요?
레이아웃관련 연산이 일어날 때마다 가장 먼저 호출된다. 이 메소드에서 셀의 위치/크기 등을 계산하기 위한 사전 처리를 할 수 있다.

UICollectionViewLayout을 상속받아 Custom한 CollectionViewLayout을 구성하고자 할때, 데이터소스를 참조하여 셀의 위치 및 크기를 미리 계산하여 캐싱해두고, CollectionView로 부터 셀의 위치 및 크기 요청이 들어올때, 미리 계산하여 캐싱해둔 데이터를 전달해주는 방식으로 커스텀 레이아웃을 구성하는 방식이 있겠다.

## UITableView를 구성할때 셀의 컨텐츠에 따라 높이를 설정하고 싶다면 어떻게 해야하나요?
델리게이트 메서드 rowHeight에서는 UITableView.automaticDimension 값을 리턴하고,
estimatedRowHeight에서는 셀의 예측 높이값을 리턴한다. 이렇게 하면 오토레이아웃 테이블뷰 셀 구현이 가능하다.
마찬가지로 테이블뷰 셀은, 고정 높이가 아닌, 셀 안의 서브뷰들의 제약 구성으로 셀의 크기가 결정될 수 있도록 해야한다.

## Delgate 장점
객체의 행동을 단순화하면서 객체 간의 결합성을 최소화시키는데 있습니다.

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

## weak와 strong에 대한 설명
- strong(강한 참조): strong은 객체를 소유하여 레퍼런스 카운터가 증가하는 프로퍼티이다. 값 지정 시점에 retain이 되고 참조가 종료되는 시점에 release가 된다. 객체에 강한 참조가 남아 있는한 해당 객체는 메모리가 해제되지않는다.
- weak(약한 참조): weak는 객체를 소유하지 않고 주소값만을 가지고 있는 포인터 개념이다. 자신이 참조는 하지만 weak 메모리를 해제시킬 수 있는 권한은 다른 클래스에 있다. 값 지정시 리테인이 발생하지 않는다. 따라서 릴리즈도 발생하지 않는다. 그래서 언제 어떻게 메모리가 해제되는지 알 수 없다. 다만 메모리가 해제될 경우 자동으로 레퍼런스가 nil 로 초기화를 해준다. 그렇기 때문에 weak 속성을 사용하는 객체는 항상 옵셔널 타입이다.
- owned: 객체가 할당될 때 레퍼런스 카운터를 증가시키지 않는다. 그러나 Non-Optional 타입으로 선언되어야 하며, 객체가 ARC에 의해 메모리가 해제가 되더라도, 해당 객체 값을 존재하는 것으로 인지하며, 해당 객체에 액세스 할 경우 런타임 오류를 발생시킨다. 객체의 라이프 사이클이 명확하고 개발자의 의해 제어 기능이 명확한 경우, weak Optional 타입 대신 사용하여 좀 더 간결한 코딩이 가능하다.

## Escaping Closure의 개념
 메소드 파라미터로 전달 받은 closure를 메소드의 라이프 사이클 내에서 실행하여 끝내지 않고, 메소드 scope의 외부에 전달하려 할 때는 해당 closure를 escaping 해야한다. 해당 메소드의 호출이 끝난 이후에도 closure는 메모리 어딘가에 저장되어야 하며, 이는 closure 안에서 사용된 outer object(self와 같은)에 weak와 같은 레퍼런스 타입을 사용해야 할 수 있음을 주의 하도록 한다. escaping이 명시되어 있지 않으면 기본적으로 non-escaping이며, 이는 메소드의 실행이 끝나기 전에 closure의 사용이 모두 완료됨을 보장한다. 따라서 closure 내에서 weak를 굳이 사용하지 않아도 안전할 수 있음을 의미하기도 한다.

## 타입 캐스팅을 할 때 사용하는 키워드인 as, as?, as! 이 셋의 차이는 무엇인가요?
- as: 컴파일러가 타입 변환의 성공을 보장. 컴파일 타임에 가능/불가능 여부를 알 수 있음
- as?: 타입 변환에 실패하는 경우 nil을 리턴. 컴파일 타임에 가능/불가능 여부를 알 수 없음
- as!: 타입 변환에 실패하는 경우 실행시간(Runtime) 오류를 발생시킨다. 가능/불가능 여부를 알 수 없음

## Swift에서 Class와 struct의 차이는 무엇인가요?
**Class-Referentce type**
- 객체화 시 힙 메모리 영역에 저장되며 ARC로 객체의 메모리 해제가 관리된다.
- 대입 연산 시 레퍼런스가 복사되어 할당됨
- 멀티 스레딩 시 적절한 Lock 활용이 필요
- 상속 가능
![classReference](https://user-images.githubusercontent.com/45002556/108618550-844a2200-7462-11eb-9830-68d30ea83d65.png)

**Struct-Value type**
- 대입 연산시 값 자체가 복제되어 할당됨(공유가 불가)
- 불변성 구현에 유리
- 멀티스레딩에 안전함
- 상속이 불가능 (프로토콜은 사용 가능)
![structValue](https://user-images.githubusercontent.com/45002556/108618553-86ac7c00-7462-11eb-83fe-60679c27cac3.png)

## 고차함수 (Map, Reduce, Filter)에 대해    
Swift에서 함수는 일급 시민으로 취급되기 때문에 다른 함수의 전달인자로 사용될 수 있다. 고차 함수란 매개 변수로 함수를 받는 함수를 말한다.

- Map: 데이터를 변형하고자 할 때 사용된다. 기존 컨테이너의 값들은 변경되지 않고 새로운 컨테이너를 생성하여 반환한다.
    1. 장점: 코드 재사용 용이 / 컴파일러 최적화 측면에서 성능이 좋다 / 다중 스레드 환경에서 하나의 컨테이너에 여러 스레드들이 동시에 변경을 하려고 할 때 예측하지 못한 결과 발생을 방지.
    2. 배열의 값 하나하나에 함수를 적용할 때 사용
![map](https://user-images.githubusercontent.com/45002556/108618563-975cf200-7462-11eb-8277-b90cc41f2138.png)

- Filter: 컨테이너 내부의 값들을 걸러서 추출하고자 할 때 사용한다. Filter의 매개변수로 전달되는 함수의 반환 타입은 Bool이다. true라면 값을 포함하고 false면 배제하여, map과 마찬가지로 새로운 컨테이너를 생성하여 반환한다. 조건에 맞는 항목만 찾아내기

![filter1](https://user-images.githubusercontent.com/45002556/108618624-ef93f400-7462-11eb-92b5-19b4a62c8dee.png)
![filter2](https://user-images.githubusercontent.com/45002556/108618625-f15db780-7462-11eb-85c6-657392ba9aaf.png)

- Reduce: 컨테이너 내부를 하나로 합쳐주는 기능을 한다. 정수 배열이라면 전달받은 함수의 연산 결과로 합쳐주고, 문자열 배열이라면 문자열을 하나로 합쳐준다. 첫 번째 매개변수를 통해 초기값을 지정할 수 있다. 이 초기값이 최초의 $0으로 사용된다. 컬렉션의 값들을 조건에 따라 하나의 값으로 만든다.
![reduce1](https://user-images.githubusercontent.com/45002556/108618634-020e2d80-7463-11eb-9526-e29a60ca680e.png)

![reduce2](https://user-images.githubusercontent.com/45002556/108618635-03d7f100-7463-11eb-9dab-c625b22b9c4e.png)
      
map, filter, reduce vs for in loop 비교
https://www.skoumal.com/en/performance-of-built-in-higher-order-functions-map-filter-reduce-and-flatmap-vs-for-in-loop-in-swift/
