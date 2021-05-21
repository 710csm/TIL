# 인앱결제 (In-App-Purchase)

## 인앱결제 방식
1. 소모품(Consumable): 사용후 소모되므로 계속 사용을 원하는 경우 다시 구입해야한다.
2. 비소모품(Non-Consumable): 구매 후 계속 사용하여도 만료되거나 소모되지 않는다.
3. 자동갱신 구독(Non-Renewing Subscription): 사용자가 일정기간 동안 서비스를 구입할 수 있게 합니다. 이 구독 유형은 사용자가 취소하지 않는 한 자동으로 갱신된다.(스트리밍 서비스를 지원하는 앱 월간 구독)
4. 비자동 갱신구독(Auto-Renewing Subscription): 사용자각 제한된 기간 동안 서비스를 구입할 수 있게 합니다. 이 앱 내 구입의 콘텐츠는 고정적일 수 있습니다. 이구독 유형은 자동으로 갱신되지 않습니다.

## 기본 설정
- 식별정보: App Store에서 식별하는 인앱결제 상품의 이름
- 제품ID: 상품을 식별하는 유니크한 문자열(ex. net.testproject.product)와 같이 작성 

<img width="1119" alt="스크린샷 2021-05-21 오전 9 15 32" src="https://user-images.githubusercontent.com/45002556/119063966-3b7c1800-ba15-11eb-9c56-12b0ae329c58.png">

- 가격: 판매할 상품의 가격을 설정

<img width="1098" alt="스크린샷 2021-05-21 오전 9 15 47" src="https://user-images.githubusercontent.com/45002556/119063968-3d45db80-ba15-11eb-8a87-1d6e83fef7db.png">

- 표시이름: 앱에서 보여질 상품의 이름
- 설명: 판매할 상품의 설명을 등록

<img width="1102" alt="스크린샷 2021-05-21 오전 9 16 00" src="https://user-images.githubusercontent.com/45002556/119063969-3dde7200-ba15-11eb-9c44-48e8e5b04c7f.png">

- 스크린샷: 심사에 사용될 사진으로 App Store에는 표시되지 않는다
- 심사용 추가정보: 심사에 사용될 추가적인 설명을 적는다.

<img width="1117" alt="스크린샷 2021-05-21 오전 9 16 13" src="https://user-images.githubusercontent.com/45002556/119063972-3e770880-ba15-11eb-98ff-b4574a26809e.png">

- 프로젝트 설정에서 signing & Capabilities에서 In-App-Purchase를 추가한다.

<img width="946" alt="스크린샷 2021-05-21 오전 9 22 01" src="https://user-images.githubusercontent.com/45002556/119064292-03290980-ba16-11eb-87fa-97a9c4b7a6a3.png">



## 사용법
- StoreKit을 import하여 인앱결제를 구현한다.

```Swift
import StoreKit

public struct InAppProducts {
    public static let product = "net.megastudy.Test.thTest"
    private static let productIdentifiers: Set<String> = [InAppProducts.product]
    public static let store = IAPHelper(productIds: InAppProducts.productIdentifiers)
}
```

- typealias 를 사용하여 별칭을 사용해 코드를 읽기 쉽게 작성할 수 있다.

```
public typealias ProductIdentifier = String
public typealias ProductsRequestCompletionHandler = (_ success: Bool, _ products: [SKProduct]?) -> Void
```

- StoreKit을 import해서 init, requestProducts, buyProducts, restorePurchase를 만들어준다. 상품 구매, 복원을 용청하는 기능들이다.

```
extension Notification.Name {
    static let IAPHelperPurchaseNotification = Notification.Name("IAPHelperPurchaseNotification")
}
open class IAPHelper: NSObject {
    private let productIdentifiers: Set<ProductIdentifier>
    private var purchasedProductIdentifiers: Set<ProductIdentifier> = []
    private var productsRequest: SKProductsRequest?
    private var productsRequestCompletionHandler: ProductsRequestCompletionHandler?
    
    public init(productIds: Set<ProductIdentifier>) {
        productIdentifiers = productIds
        
        for productIdentifier in productIds {
            let purchased = UserDefaults.standard.bool(forKey: productIdentifier)
            
            if purchased {
                purchasedProductIdentifiers.insert(productIdentifier)
                print("Previously purchased: \(productIdentifier)")
            } else {
                print("Not purchased: \(productIdentifier)")
            }
        }
        
        super.init()
        SKPaymentQueue.default().add(self) // App Store와 지불 정보를 동기화하기 위한 Observer 추가
    }
    
    // App Store connect에서 등록한 인앱결쩨 상품들을 가져올 때
    public func requestProducts(_ completionHandler: @escaping ProductsRequestCompletionHandler) {
        productsRequest?.cancel()
        productsRequestCompletionHandler = completionHandler
        productsRequest = SKProductsRequest(productIdentifiers: productIdentifiers)
        productsRequest?.delegate = self
        productsRequest?.start()
    }
    
    // 인앱결제 상품을 구입할 때
    public func buyProduct(_ product: SKProduct) {
        let payment = SKPayment(product: product)
        SKPaymentQueue.default().add(payment)
    }
    
    public func isProductPurchased(_ productIdentifier: ProductIdentifier) -> Bool {
        return purchasedProductIdentifiers.contains(productIdentifier)
    }
    
    public class func canMakePayments() -> Bool {
        return SKPaymentQueue.canMakePayments()
    }
    
    // 구입 내역을 복원할 때
    public func restorePurchases() {
        SKPaymentQueue.default().restoreCompletedTransactions()
    }
}
```

- 위에 있는 requestProducts로 delegate를 선언해서 SKProductsRequestDelegate를 구현한다. App Store에 만들어둔 prduct 리스트를 가져오고, 실패시 수행되는 delegate이다.

```
extension IAPHelper: SKProductsRequestDelegate {
    // 인앱결제 상품 리스트를 가져온다
    public func productsRequest(_ request: SKProductsRequest, didReceive response: SKProductsResponse) {
        let products = response.products
        productsRequestCompletionHandler?(true, products)
        clearRequestAndHandler()
    }
    
    // 상품 리스트 가져오기 실패한 경우
    func request() {
        productsRequestCompletionHandler?(false, nil)
        clearRequestAndHandler()
    }
    
    func clearRequestAndHandler() {
        productsRequest = nil
        productsRequestCompletionHandler = nil
    }
}
```

- SKPaymentTransactionObserver를 통해 product를 결제 했을 때나 결제에 실패했을 경우 App Store 응답을 처리하는 Observer
```
extension IAPHelper: SKPaymentTransactionObserver {
    public func paymentQueue(_ queue: SKPaymentQueue, updatedTransactions transactions: [SKPaymentTransaction]) {
        for transaction in transactions {
            switch transaction.transactionState {
            case .purchased:
                complete(transaction: transaction)
            case .failed:
                fail(transaction: transaction)
            case .restored:
                restore(transaction: transaction)
            case .deferred:
                break
            default:
                fatalError()
            }
        }
    }
    
    // 구입 서공
    private func complete(transaction: SKPaymentTransaction) {
        deliverPurchaseNotificationFor(identifier: transaction.payment.productIdentifier)
        SKPaymentQueue.default().finishTransaction(transaction)
    }
    
    // 복원 서공
    private func restore(transaction: SKPaymentTransaction) {
        guard let productIdentifier = transaction.original?.payment.productIdentifier else { return }
        deliverPurchaseNotificationFor(identifier: productIdentifier)
        SKPaymentQueue.default().finishTransaction(transaction)
    }
    
    // 구매 실패
    private func fail(transaction: SKPaymentTransaction) {
        if let transactionError = transaction.error as NSError?,
           let localizedDescription = transaction.error?.localizedDescription,
            transactionError.code != SKError.paymentCancelled.rawValue {
                print("Transcation Error: \(localizedDescription)")
            }
            SKPaymentQueue.default().finishTransaction(transaction)
    }
    
    // 구입한 인앱 상품 키를 UserDefaults로 로컬에 저장
    private func deliverPurchaseNotificationFor(identifier: String?) {
        guard let identifier = identifier else { return }
        
        purchasedProductIdentifiers.insert(identifier)
        UserDefaults.standard.set(true, forKey: identifier)
        NotificationCenter.default.post(name: .IAPHelperPurchaseNotification, object: identifier)
    }
}
```
