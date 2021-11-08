# Swift - try? try!

try는 예외 상황을 다루기 위한 방법의 do ~ catch 문을 사용해야 온전히 에러 처리를 할 수 있습니다. 기능은 try ~ catch 방식과 동일하고 do문 내에서 try한 메서드에 에[러가 발생하면 앱이 종료되지 않고 catch 문에서 처리합니다.

do와 catch 문을 모두 적어주지 않아도 간편히 에러처리 가능한 try?와 try!를 사용하는 것이 더 효과적입니다.

```swift
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
```

**try?**

에러 발생시 nil을 리턴합니다.

에러가 발생하지 않으면 반환 타입은 옵셔널입니다.

반환 타입이 없어도 사용 가능합니다.

do~catch문 없이 사용가능합니다.

```swift
if let data = try? getData(){
    return data
}
```

**try!**

에러 발생시 런타임이 종료됩니다.

반환 타입은 언래핑입니다.

예외가 발생하지 않는다는 것을 보장하고 해당 함수를 호출하는 것으로 보시면 됩니다.

```swift
let image = try! loadImg(atPath: "./Resources/mainLogo.jpg")
```
