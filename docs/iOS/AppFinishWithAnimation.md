# 앱 종료 애니메이션

## exit()

```swift
DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
    exit(0)
}
```

- exit(0) 코드만 사용하게 되면 보기 안좋게 앱이 종료되면서 홈화면으로 돌아간다.

![img](https://user-images.githubusercontent.com/45002556/140460983-cc1c83ef-dce0-413d-b74f-7d3fd0d0e4de.gif)

## NSXPCConnection.suspend

```swift
UIApplication.shared.perform(#selector(NSXPCConnection.suspend))
DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
    exit(0)
}
```

- 위 코드처럼 작성하면 애니메이션과 함계 홈화면으로 돌아간다.

![ncJR3](https://user-images.githubusercontent.com/45002556/140460778-03973cce-2a85-497a-813e-4924250cb652.gif)
