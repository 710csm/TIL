# CAGradientLayer
- CAGradientLayer를 사용하여 view 배경에 gradienrt 효과를 넣어줄 수 있다.

## 사용법
- frame: gradient가 나타날 frame 크기
- startPoint: gradient가 시작할 위치
- endPoint: gradient가 끝나는 위치
- colors: gradient에 나타날 color들을 지정
- locations: 각각의 색이 어디 위치까지 나타나게 하는지 설정

```Swift
func setGradient() {
    let gradient = CAGradientLayer()
    gradient.frame = self.view.bounds
    gradient.locations = [0.5, 1.0]
    gradient.startPoint = CGPoint(x: 0.5, y: 1.0)
    gradient.endPoint = CGPoint(x: 0.5, y: 0.0)
    gradient.colors = [
        hexStringToUIColor(hex: "#a18cd1").cgColor,
        hexStringToUIColor(hex: "#fbc2eb").cgColor
    ]
    self.view.layer.insertSublayer(gradient, at: 0)
}

// hex 값을 color로 바꿔주는 코드
func hexStringToUIColor (hex: String, alpha: CGFloat = 1.0) -> UIColor {
    var cString = hex.trimmingCharacters(in: .whitespacesAndNewlines).uppercased()
    if cString.hasPrefix("#") {
        cString.remove(at: cString.startIndex)
    }
    
    if (cString.count) != 6 {
        return UIColor.gray
    }
    
    var rgbValue: UInt32 = 0
    Scanner(string: cString).scanHexInt32(&rgbValue)
    
    return UIColor(
        red: CGFloat((rgbValue & 0xFF0000) >> 16) / 255.0,
        green: CGFloat((rgbValue & 0x00FF00) >> 8) / 255.0,
        blue: CGFloat(rgbValue & 0x0000FF) / 255.0,
        alpha: CGFloat(alpha)
    )
}
```
