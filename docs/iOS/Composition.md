# 상속과 Composition

## 상속을 사용하는 이유
1. 코도를 재사용함으로써 중복을 줄일 수 있다.
2. 변화에 대한 유연성 및 확장성이 증가한다.
3. 개발 시간이 단축된다.

## 상속의 단점
- 캡슐화를 깨뜨린다, 상위 클래스의 구현이 하위 클래스에게 노출되는 상속은 캡슈로하를 깨뜨린다. 캡슐화가 깨짐으로써 하위 클래스가 상위 클래스에 강하게 결합, 의존하게 되고 강한 결합, 의존은 변화에 유연하게 대처하기 어려워진다.

## 상속의 단점의 예제
- 아래 Lotto 클래스에서 lottoNumbers의 타입이 바뀌게 된다면 부모와 강한 의존을 맺은 WinningLotto 클래스는 강한 영향을 받는다. 즉, Lotto 클래스를 상속한 하위 클래스가 몇 개가 있든 전부 깨지게 되는 것이다. 그리고 해결법은 모든 하위 클래스에서 일일이 수정을 해주는 방법이다.
- 또한 상위 클래스의 메서드 이름과 매개변수의 변화는 하위 클래스 전체의 변경을 야기하기도 한다. 

```Swift
class Lotto {
    var lottoNumbers: [Int]
    
    init(lottoNumbers: [Int]) {
        self.lottoNumbers = lottoNumbers
    }
    
    public func contains(integer: Int) -> Bool {
        return self.lottoNumbers.contains(integer)
    }
    
    ...
}
```

```Swift
class WinningLotto: Lotto {
    private var bonusBall: BonusBall
    
    init(lottoNumbers: [Int], bonusBall: BonusBall) {
        self.bonusBall = bonusBall
        
        super.init(lottoNumbers: lottoNumbers)
    }
    
    func compareArrays(lotto: Lotto) -> Bool {
        return zip(lottoNumbers, lotto.lottoNumbers).allSatisfy { $0 == $1 }
    }
    
    ...
}
```

## 조합(Composition)
- 기존 클래스가 새로운 클래스의 구성 요소로 쓰인다. 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조한다.

## 조합의 예시
- 앞에서 봤던 WinningLotto 클래스가 Lotto를 상속받는 것이 아닌 조합을 사용하면 다음과 같다. 

```Swift
class WinningLotto {
    private var lotto: Lotto
    private var bonusBall: BonusBall
    
    init(lotto: Lotto, bonusBall: BonusBall) {
        self.lotto = lotto
        self.bonusBall = bonusBall
    }
}
```

- 이처럼 WinningLotto 클래스에서 인스턴스 변수로 Lotto 클래스를 가지는것이 조합이다. WinningLotto 클래스는 Lotto 클래스의 메서드를 호출하는 방식으로 동작하게 된다.

## 조합의 장점
1. 메서드를 호출하는 방식으로 동작하기 때문에 캡슐화를 깨뜨리지 않는다.
2. Lotto 클래스와 같은 기존 클래스의 변화에 영향이 적어지며, 안전하다.


## 결론
- 캡슐화를 깨뜨리고, 상위 클래스에 의존하게 돼서 변화에 유연하지 못한 상속을 사용하기보다는 조합을 사용한다.
- 조합이 상속보다 무조건 좋은것은 아니다. 아래 조건을 만족하면 상속이 적절하게 사용되면 조합보다 강력하고, 개발하기도 편리하다. 

1. 확장성을 고려하고 설계한 확실한 is-a 관계일 때
2. API에 아무런 결함이 없는 경우, 결함이 있다면 하위 클래스까지 전파돼도 괜찮은 경우
