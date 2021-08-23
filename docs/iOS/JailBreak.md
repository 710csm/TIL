# JailBreak

- 탈옥 및 루팅시 개인정보 유출 등의 보안 위험이 있기 때문에 탈옥과 루팅시 앱을 사용할 수 없도록 조치를 취해야한다.
- 탈옥 시 사용하는 앱이 설치되어있는지 확인하고 접근 불가한 경로를 열어보는 시도로 탈옥 여부 즉 루트 권한이 있는지 체크한다

## 사용법
```swift
import UIKit
import Foundation

class JailBreak: NSObject {
    
    public func hasJailbreak() -> Bool {
        guard let cydiaUrlScheme = NSURL(string: "cydia://package/com.example.package") else {
            return false
        }
        
        if UIApplication.shared.canOpenURL(cydiaUrlScheme as URL) {
            return true
        }
        
        #if arch(i386) || arch(x86_64)
        return false
        #endif
        let fileManager = FileManager.default
        if fileManager.fileExists(atPath: "/Applications/Cydia.app") ||
            fileManager.fileExists(atPath: "/Library/MobileSubstrate/MobileSubstrate.dylib") ||
            fileManager.fileExists(atPath: "/bin/bash") ||
            fileManager.fileExists(atPath: "/usr/sbin/sshd") ||
            fileManager.fileExists(atPath: "/etc/apt") ||
            fileManager.fileExists(atPath: "/usr/bin/ssh") ||
            fileManager.fileExists(atPath: "/private/var/lib/apt") {
            return true
        }
        
        if canOpen(path: "/Applications/Cydia.app") ||
            canOpen(path: "/Library/MobileSubstrate/MobileSubstrate.dylib") ||
            canOpen(path: "/bin/bash") ||
            canOpen(path: "/usr/sbin/sshd") ||
            canOpen(path: "/etc/apt") ||
            canOpen(path: "/usr/bin/ssh") {
            return true
        }
        
        let path = "/private/" + NSUUID().uuidString
        do {
            try "anyString".write(toFile: path, atomically: true, encoding: String.Encoding.utf8)
            try fileManager.removeItem(atPath: path)
            return true
        } catch {
            return false
        }
    }
    
    private func canOpen(path: String) -> Bool {
        let file = fopen(path, "r")
        guard file != nil else { return false }
        fclose(file)
        return true
    }
}
```

```Swift
let jailBreak = JailBreak()
if jailBreak.hasJailbreak {
    exit(0)
}
```
