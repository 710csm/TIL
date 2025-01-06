# Swift의 2진수 변환

## String(value: BinaryInteger, radix: Int)
- value에 바꾸고자 하는 값을 넣고 radix에 2를 넣으면 String 타입에 2진수로 변환된 값을 리턴해준다.
- radix의 기본 값은 10이며 16을 넣으면 16진수로 변환 시킨다.

```Swift
let number = 78
let binaryNumber = Sring(value: 78, radix: 2)
print(binaryNumber)

// 결과
1001110
```

## nonzeroBitCount
- 2진수로 변환 후 가지고 있는 1의 개수를 리턴해준다 

```Swift
let number = 3 // 11
print(number.nonzeroBitCount)

let number = 8 // 1000
print(number.nonzeroBitCount)

// 결과 
2
1
```
