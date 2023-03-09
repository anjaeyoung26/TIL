# 제어 전송문

제어 전송문은 코드의 진행이 계속 될지 결정하거나, 실행되는 코드의 흐름을 바꾸기 위해 사용한다. Swift에서는 아래의 다섯 가지 전송 제어문을 제공한다.

- [`continue`](#continue)
- [`break`](#break)
- [`fallThrough`](#fallthrough)
- `return`
- `throw`

&nbsp;
## continue

현재 loop에서 수행 중인 작업을 중지하고, 다음 반복의 시작 부분에서 다시 시작한다. 다음 예제에서는 소문자 문자열에서 모든 모음과 공백을 제거하여 수수께끼 같은 퍼즐 구문을 만든다.

```swift
let puzzleInput = "great minds think alike"
var puzzleOutput = ""
let charactersToRemove: [Character] = ["a", "e", "i", "o", "u", " "]
for character in puzzleInput {
  if charactersToRemove.contains(character) {
    continue
  }
  puzzleOutput.append(character)
}
print(puzzleOutput) 
// Prints "grtmndsthnklk"
```

`puzzleInput`의 문자에 대한 for loop에서 `charactersToRemote`에 포함되어 있다면 loop의 반복이 즉시 종료되고 다음 반복의 시작 부분으로 바로 넘어간다.

&nbsp;
## break

현재 loop에서 수행 중인 작업을 중지하고, loop를 완전히 종료한다. 따라서 `continue`와 다르게 다음 반복을 시작하지 않는다. 위 퍼즐 구문을 생성하는 예제에서 `continue` 대신 `break`를 사용한다면 `puzzleOutput`의 값은 `gr`이 된다.

`break`는 `switch`문 안에서 하나 이상의 케이스와 일치한다면 무시하는데 사용할 수 있다. 무시하고자 하는 case 안에 `break`를 넣어 `switch` 문의 실행을 즉시 종료한다. `switch`문 안에서 `break`가 어떻게 동작하는지 예시를 살펴보자.

```swift
let numberSymbol: Character = "三"
var possibleIntegerValue: Int?
switch numberSymbol {
case "1", "١", "一", "๑":
  possibleIntegerValue = 1
case "2", "٢", "二", "๒":
  possibleIntegerValue = 2
case "3", "٣", "三", "๓":
  possibleIntegerValue = 3
case "4", "٤", "四", "๔":
  possibleIntegerValue = 4
default:
  break
}
if let integerValue = possibleIntegerValue {
  print("The integer value of \(numberSymbol) is \(integerValue).")
} else {
  print("An integer value could not be found for \(numberSymbol).")
}
```

위 예제에서는 `numberOfSymbol`이 라틴, 아랍어, 중국어 또는 태국어 기호인지 확인하고 `possibleIntegerValue` 변수에 적절한 정수 값을 설정한다. 만약 `numberOfSymbol`이 1~4의 기호가 아닌 경우에는 `default: break`를 통해 `possibleIntegerValue`는 여전히 nil 값을 가지고 있다. 이처럼 `break`는 `switch` 문에서 특정 케이스에 대해 무시하는데 사용한다.

&nbsp;
## fallthrough

C 언어의 `switch` 문은 모든 케이스에 `break` 구문을 넣어서 특정 `case`를 타면 `switch`문은 종료되게 한다.

```
char input = 'A';
switch(input) {
  case 'A':
    printf("input value is A.")
    break;
  case 'B':
    printf("input value is B.")
    break;
  default:
    printf("input value is not A or B.")
}
```

만약 `case` 안에 `break`를 넣지 않는다면 출력은 아래와 같다.

```
input value is A.
input value is B.
input value is not A or B.
```

일치하는 `case` 뿐만 아니라 `switch`문의 모든 `case`를 거쳐가는데, 이를 **fall through**라고 한다. Swift에서는 기본적으로 `case` 안에 `break`가 추가되어 있는 것과 같이 동작한다. 만약 C 언어의 `switch`문과 같이 fall through한 동작이 필요하다면 `fallThrough` 키워드를 사용한다.

```swift
let integerToDescribe = 5
var description = "The number \(integerToDescribe) is"
switch integerToDescribe {
case 2, 3, 5, 7, 11, 13, 17, 19:
  description += " a prime number, and also"
  fallthrough
default:
  description += " an integer."
}
print(description)
// Prints "The number 5 is a prime number, and also an integer."
```