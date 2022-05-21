# Escaping Closure

직역하자면 '탈출하는 클로저'이다. 무엇으로부터 탈출할까? 

> 클로저에 대해 낯설다면 [Swift Closure](https://fromdave.medium.com/closure-5884c4652479) 에서 기본적인 개념을 이해하자.

&nbsp;
## Non-Escaping

Escaping 클로저를 알아보기 전에 고차 함수에서 클로저가 사용되는 예시를 살펴보자. 

```swift
func higherOrderFunction(closure: () -> Void) {
  closure()
}
```

위와 같은 고차 함수에서 전달 인자로 받은 클로저는 함수가 종료되기 전에 실행된다. 중요한 점은 클로저가 실행되는 시점이 함수가 종료되기 전이므로 클로저는 함수의 스코프 내에서만 동작할 수 있다. 이를 Non-Escaping 클로저라고 하며, 특별히 어노테이션을 명시하지 않는다. 

Swift의 `map`, `filter`, `reduce`와 같은 대표적인 고차 함수는 모두 Non-Escaping 클로저를 사용한다. `map`의 선언부를 살펴보자.

```swift
func map<T>(_ transform: (Element) throws -> T) rethrows -> [T]
````

전달 인자로 `(Element) throws -> T` 라는 클로저를 받는다. 함수 내부에서 시퀸스의 각 요소들을 클로저의 내용대로 변형시키고, 변형된 요소들이 담긴 배열을 반환한다. 즉 함수의 결과를 반환시키기 전에 클로저를 실행한다.

&nbsp;
## Escaping

반면에 클로저에 `@escaping` 어노테이션을 명시하면 클로저는 함수의 스코프 밖에서 동작할 수 있다. 아래의 예시를 살펴보자.

```swift
var completions: [(() -> Void)] = []

func foo(completion: @escaping () -> Void) {
  completions.append(completion)
}
```

`foo` 함수가 전달받은 클로저를 함수의 외부에 존재하는 `completions` 배열에 저장했다. `foo` 함수가 종료된 후 전달받은 클로저는 아직 실행되지 않았다. `@escaping` 어노테이션을 명시해서 클로저를 함수 밖에서 실행할 수 있게 되었다.

또한 `@escaping`가 붙은 클로저는 반드시 함수의 외부에서 사용해야 하지 않고, Non-Escaping 클로저와 같이 함수 내부에서 사용할 수 있다.

```swift
func foo(completion: @escaping () -> Void) {
  completion()
}
```