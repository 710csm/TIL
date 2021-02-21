# Realm

## 설명
- 오픈 소스로 모바일에 최적화된 데이터베이스 라이브러리
- ORM이 아닌 데이터 컨테이너 모델을 사용하고 데이터 객체는 Realm에 객체로 저장됩니다.
- 컨테이너란 OS상에 논리적인 구획(컨테이너)을 만들고, 어플리케이션을 작동시키기 위해 필요한 라이브러리나 어플리케이션 등을 하나로 모아, 마치 별도의 서버인 것처럼 사용할 수 있게 만든 것
- 호스트 OS의 리소스를 논리적으로 분리시키고, 여러 개의 컨테이너가 공유하여 사용한다. 컨테이너는 오버헤드가 적기 때문에 가볍고 고속으로 작동하는 것이 특징입니다.
- 기존의 데이터베이스처럼 Realm의 데이터는 쿼리, 필터링, 상호 연결이 가능하고 저장이 된다.

## Realm 객체
- 반면 기존 데이터베이스와는 달리 Realm의 객체는 라이브 오브젝트이고 완전히 반응형이다. Realm은 기기와 어플리케이션 사이에서 매끄럽게 동기화되며 쓰레드에서 안전하게 접근이 가능하다.
- 이는 기존 쿼리문으로 테이블에 데이터를 읽어오던 DB와는 다른 방식으로 기존에 쓰던 데이터베이스들은 가져온 데이터를 바로 사용할 수 없고, 애플리케이션에서 사용이 가능하도록 가공하는 과정이 필요합니다. 이 과정이 잘못되면 코드가 복잡해지는 문제가 있습니다.
- Realm은 객체 형태로 데이터를 읽어오고 객체 형태로써 바로 DB에 저장을 가능하게 함으로 위와 같은 문제를 해결한 솔루션이다.

## 성능
![f1](https://user-images.githubusercontent.com/45002556/108619724-0722ab00-746a-11eb-9210-d39838375a3e.png)

## 사용 방법
- Realm은 객체 컨테이너로 동작하기 때문에 object를 새성해주어야 한다
![f2](https://user-images.githubusercontent.com/45002556/108619726-08ec6e80-746a-11eb-8e71-4b4b37f23cb4.png)

    - class로 구성
    - 각 변수 앞에 @objc dynamic을 적어준다

- Realm 추가         
![f3](https://user-images.githubusercontent.com/45002556/108619727-09850500-746a-11eb-8d3c-6923fe986039.png)

    - realm.write{realm.add(Object)}

- Realm 삭제       
![f4](https://user-images.githubusercontent.com/45002556/108619728-0a1d9b80-746a-11eb-8f35-83221d71f1db.png)
    - realm.write{realm.delete(Object)}

- Realm 업데이트        
![f6](https://user-images.githubusercontent.com/45002556/108619729-0a1d9b80-746a-11eb-9d36-7243c8c891e3.png)

    - Realm의 업데이트시 Object 객체는 데이터를 바로 반영하고 자동 업데이트하기 때문에 객체를 새로고침 할 필요가 없다. 한 객체의 속성을 고치면 동일 객체를 참조하는 다른 객체에도 즉각적으로 반영된다.

- Realm 조회      
![f7](https://user-images.githubusercontent.com/45002556/108619730-0ab63200-746a-11eb-9d2f-6ca5df6fc649.png)

    - Realm에 저장된 Object 형태로 저장되며 조회할 때도 저장된 Object의 값으로 불러올 수 있다. 정렬 및 필터를 이용해 데이터를 정리하여 호출 가능하다.

- NotificationToken을 활용한 view update

    ```swift
    var exampleList: Results<ExampleEntity>?
      var notificationToken: NotificationToken?
      var realm = try! Realm()
      deinit {
        notificationToken?.invalidate()
      }

    notificationToken = exampleList!.observe { changes in
          switch changes {
          case .initial( _):
            self.testTableView.reloadData()
            
          case .update( _, let deletions, let insertions, let updates)
            self.storageTableView.beginUpdates()
            self.storageTableView.deleteRows(
              at: deletions.map({IndexPath(row: $0, section: 0)}), with: .automatic)
            self.storageTableView.insertRows(
              at: insertions.map({IndexPath(row: $0, section: 0)}), with: .automatic)
            self.storageTableView.reloadRows(
              at: updates.map({IndexPath(row: $0, section: 0)}), with: .automatic)
            self.storageTableView.endUpdates()
            
          case .error:
            break
          }
          
          self.adjustTableView()
        }
    ```
