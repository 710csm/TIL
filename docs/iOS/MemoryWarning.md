# Memory Warning시 대처 방법

## 메모리 부족 경고가 일어났을 때 
- iOS는 메모리 사용량이 사용 가능한 메모리의 한계의 도달하면 앱이 경고를 보낸다. 메모리 경고를 트리거하는 메모리 사용량은 Xcode의 메모리 게이지의 노란색 영역에 해당한다. 운영체제는 최선의 방법을 사용하여 메모리 부족 경고를 보내고, 우리의 앱은 가능한 빨리 이에 응답해야 한다.

### 응답 방법
1. App delegate에 의한 [applicationDidReceiveMemoryWarning(_:)](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623063-applicationdidreceivememorywarni)
2. UIViewController에 의한 [didReceiveMemoryWarning()](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621409-didreceivememorywarning) 호출
3. notification center에 [didReceiveMemoryWarningNotification](https://developer.apple.com/documentation/uikit/uiapplication/1622920-didreceivememorywarningnotificat) post
4. Dispatch Queue가 [DISPATCH_SOURCE_TYPE_MEMORYPRESSURE](https://developer.apple.com/documentation/dispatch/dispatch_source_type_memorypressure) 타입의 이벤트를 수신
    

### 대응 방법 
1. UI Property 선언을 let -> lazy var와 같은 방식을 변경
2. 이미지 또는 대용량 리소스를 줄인다.
3. NSPurgeableData의 사용
4. 뷰와 관련된 자원해제: 뷰 컨트롤러가 메모리를 많이 사용할 때, 뷰를 해제하고 관련된 자원(ex: 이미지, 데이터 등)을 해제하여 메모리를 해제할 수 있다.
5. 비활성화된 뷰 제거: 앱에서 현재 보이지 않는 뷰는 비활성화하여 메모리 사용량을 줄일 수 있다. 
6. 캐시 데이터 제거: 앱에서 사용하지 않는 캐시 데이터를 제거하여 메모리를 해제할 수 있다.
7. 메모리 사용량 모니터링: 앱의 메모리 사용량을 모니터링하여 불필요한 메모리 사용을 줄일 수 있습니다. Instruments와 같은 프로파일링 도구를 사용하여 앱의 메모리 사용을 분석할 수 있다.
8. 메모리 관리 개선: 개발자는 코드에서 불필요한 객체 생성, 불필요한 반복문, 불필요한 블록 등을 최소화하여 메모리 사용량을 줄일 수 있다. 

## NSPurgeableData
- 메모리 부족 시 자동으로 더 이상 필요하지 않는 데이터를 지우고, 메모리를 확보하는 방식의 데이터 클래스이다. 
- NSPurgeableData는 NSData의 서브 클래스이며, 데이터에 대한 참조 카운팅을 사용하여 데이터를 사용하는 모든 객체가 데이터를 공유할 수 있다. 이 클래스는 데이터를 캐시하거나, 임시 파일을 저장하거나, 다운로드한 데이터를 처리하는 등의 일반적인 용도로 사용할 수 있다.
- NSPurgeableData 클래스는 NSDiscardableContent 프로토콜을 준수한다. 이 프로토콜은 데이터를 메모리에서 지우고 다시 로드할 수 있는 객체로 만들기 위한 몇가지 메서드를 정의한다. 이를 통해 앱은 메모리 부족 시 자동으로 불필요한 데이터를 지우고, 필요할 때 다시 로드할 수 있습니다.

### NSPurgeableData 제공 메소드
- beginContentAceess: 데이터에 대한 액세스를 시작합니다. 액세스를 시작하면, 데이터는 캐시로부터 로드됩니다.
- endContentAceess: 데이터에 대한 액세스가 종료됩니다.
- discardContentIfPossible: 데이터를 삭제할 수 있는지 여부를 확인하고, 가능하다면 데이터를 삭제합니다.
-isContentDiscarded: 데이터가 삭제되었는지 여부를 확인합니다.

- NSPurgeableData는 iOS 앱에서 메모리 관리를 개선하는데 사용될 수 있다. 이 클래스를 사용하면 메모리 부족 시 시스템이 자동으로 불필요한 데이터를 삭제하므로, 앱이 충돌하는 상황을 방지할 수 있습니다. 그러나 NSPurgeableData를 사용하면서도, 데이터에 대한 액세스가 종료되기 전에 앱이 종료되는 경우 데이터가 삭제되지 않을 수 있으므로 주의해야 한다.
