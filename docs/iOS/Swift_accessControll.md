# Access Control
open -> public -> internal -> file-private -> private로 갈수록 더 제한적이다

### private 
- class 내에서만 접근 가능

### fileprivate
- 같은 소스파일 내에서만 접근 가능

### internal
- 같은 모듈 또는 앱에서 접근 가능, 해당 모듈 외부의 소스파일에서는 사용되지 않도록 한다.
- 일반적으로 앱이나 framework의 내부 구조를 정의할 때 사용된다.

### public
- 어디서든 접근 가능, 외부 모듈에서는 서브 클래싱을 할 수 없고 정의된 모듈 안에서만 서브 클래싱을 할 수 있다.

### open
- 외부 모듈  어디에서나 사용할 수 있을 뿐 아니라 외부에서 하위 분류하거나 오버라이드할 수 있다.
- open은 class에만 붙힐 수 있다. Stuct나 Enum은 public부터 가능하다.
