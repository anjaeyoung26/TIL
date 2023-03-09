# @discardableResult

함수의 반환 값을 상수, 변수에 저장하지 않는 등 값을 사용하지 않을 때 컴파일러가 '*Result of call to '~'is unused*' 경고를 표시한다. 하지만 함수의 반환 값을 상황에 따라 사용할 수도, 사용하지 않을 수도 있다. 이때 함수에 `discardableResult` 키워드를 붙여 컴파일러에게 '함수의 결과 값을 사용하지 않을 수 있다' 라고 명시한다. "결과 값이 사용되지 않았습니다." 라는 경고가 표시되지 않도록 설정한다.

```swift
@discardableResult
func log(_ msg: String) -> String {
  return "\(msg) is entered."
}
```