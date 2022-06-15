# 접근 제어

접근 제어는 특정 코드의 접근을 다른 소스 파일이나 모듈에서 제한함으로써 코드의 세부적인 구현을 감추고, 필요만 만큼만 공개하여 사용할 수 있도록 한다. 접근 제어는 클래스, 구조체, 열거형 등 개별적인 타입 뿐만 아니라, 해당 타입에 속하는 프로퍼티, 메서드, 이니셜라이저에도 적용할 수 있다. Swift에서는 기본 접근 레벨을 제공하여 단일 타겟 앱에서는 특별한 접근 레벨을 명시하지 않아도 된다.

&nbsp;
## 모듈과 소스 파일

Swift의 접근 제어는 모듈과 소스 파일에 기반을 두고 있다. 

- 모듈이란 코드를 배포하는 단일 단위로 하나의 프레임워크나 앱이 모듈 단위로 배포되고, 다른 모듈에서 Swift의 `import` 키워드를 사용해서 import 된다. 우리가 사용하는 `UIKit`, `Foundation` 등이 모듈에 해당한다. 또한 Xcode의 각 빌드 타겟은 Swift에서 분리된 단일 모듈로 취급된다. 

- 소스 파일은 모듈 내부의 코드가 작성된 파일을 의미한다. 각 소스 파일에 여러가지 타입을 선언하여 사용할 수 있다. 예를 들어, `AppDelegate.swift`, `ViewController.swift`와 같은 파일이 소스 파일이다.

&nbsp;
## 접근 레벨

Swift에서는 다섯 개의 접근 레벨을 제공한다. 특정 코드에 접근 레벨을 명시하여 다른 소스 파일이나 모듈의 접근을 제한할 수 있다.

1. `open` : `open` 레벨의 코드는 외부 모듈에서 접근할 수 있으며, 재정의와 상속도 가능하다.
2. `public` : `open`과 마찬가지로 외부 모듈에서 접근할 수 있지만, 재정의와 상속이 불가능하다.
3. `internal` : `internal` 레벨로 명시된 코드는 해당 모듈에서 접근할 수 있지만, 외부 모듈에서는 접근할 수 없다.
4. `fileprivate` : `fileprivate` 레벨은 코드가 선언되어 있는 소스 파일 내부에서만 접근할 수 있다.
5. `private` : `private` 레벨은 코드가 선언되어 있는 블록({}) 내부에서만 접근할 수 있으며, Swift의 다섯 가지 접근 레벨 중 가장 높은 레벨이다.

만약 프레임워크를 개발한다면 `open` 혹은 `public` 레벨을 적절히 사용하여 다른 모듈에게 제공하려는 코드에 접근할 수 있도록 만들어야 한다. 또한 단일 타겟 앱에서는 대부분 접근 레벨을 명시할 필요가 없지만, 필요 시 `fileprivate` 혹은 `private` 레벨을 사용하여 특정 코드의 세부 사항을 숨길 수 있다.

&nbsp;

### 가이드 원칙 및 팁

1. 특정 엔티티의 내부에 더 높은 접근 레벨의 엔티티는 존재할 수 없다. 예를 들어, `public` 변수는 다른 `internal`, `fileprivate` 혹은 `private` 타입의 내부에 정의할 수 없다. 왜냐하면 해당 타입은 `public` 변수가 사용되는 곳에서 사용될 수 없기 때문이다.

    ```swift
    internal class MyPublicClass {
    public var myPublicInt: Int = 0 // X
    private var myPrivateInt: Int = 0 // O
    }
    ```

2. 앞서 Xcode의 각 빌드 타겟은 Swift에서 분리된 단일 모듈로 취급한다고 말했듯이, 유닛 테스트 타겟도 하나의 모듈이다. 그렇다면 `open`과 `public`이 아닌 다른 접근 레벨의 코드를 테스트하고 싶다면 어떻게 해야될까? 유닛 테스트에서는 테스트의 대상인 모듈을 `@testable` 애트리뷰트를 통해 import 하여, 해당 모듈을 테스트가 가능한 모듈로 컴파일한다.

    ```swift
    @testable import MyTargets
    ```

3. `getter`와 `setter`는 자동으로 상수, 변수, 프로퍼티 그리고 서브 스크립트가 갖는 접근 레벨을 갖는다. 하지만 필요에 따라 `setter`를 `getter`보다 더 제한적으로 설정할 수 있다. 아래의 코드를 살펴보자.

    ```swift
    struct TrackedString {
    public private(set) var numberOfEdits = 0
    var value: String = "" {
        didSet {
            numberOfEdits += 1
        }
    }
    }
    ```

    `numberOfEdits`는 `private(set)` 접근 레벨을 명시하여 `getter`는 `public` 접근 레벨을 가지며, `setter`는 `private` 접근 레벨을 갖도록 한다. 따라서 아래와 같이 `getter` 만을 이용해 변경된 숫자를 확인할 수 있다.

    ```swift
    var stringToEdit = TrackedString()
    stringToEdit.value = "This string will be tracked."
    stringToEdit.value = "This edit will increment numberOfEdits."
    print("The number of edits is \(stringToEdit.numberOfEdits)")
    // "The number of edits is 3"
    ```

4. 프로토콜에서 선언된 변수의 접근 레벨은 조건을 만족한 경우에만 사용할 수 있다. 아래의 코드에서 `Car` 프로토콜이 선언되어 있고, 이를 준수하는 `CarModel` 구조체가 있다. 만약 `Car` 프로토콜이 `private(set)` 레벨의 변수를 요구해야 한다면 `Car` 프로토콜에서 어떻게 정의해야 할까?

    ```swift
    protocol Car {
    // ?
    }

    struct CarModel: Car {
    private(set) var engine: String
    }
    ```

    이는 프로토콜의 변수가 *"gettable, settable 하다."* 라는 말에 *"외부 엔티티에서 gettable, settable 하다."* 라는 의미를 추가하면 문제를 해결할 수 있다. 즉, `private(set)`은 외부에서 settable 하지 않기 때문에 프로토콜에는 gettable만 들어가게 된다.

    ```swift
    protocol Car {
    var engine: String { get }
    }

    struct CarModel: Car {
    private(set) var engine: String
    }
    ```

5. 특정 코드의 접근 레벨을 명시하지 않으면 `internal` 레벨로 간주된다. 아래의 코드에서 `internalInt` 변수는 아무런 접근 레벨을 명시하지 않아서 `internal` 레벨을 갖는다.

    ```swift
    public class MyPublicClass {
    var internalInt: Int = 0
    }
    ```
