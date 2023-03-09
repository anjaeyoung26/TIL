# SPM

SPM은 Swift Package Manager의 줄임말로, CocoaPods과 Carthage와 같이 **의존성을 관리하기 위한 도구**이며, 애플에서 자체적으로 지원하는 **1st-party** 도구이다. 애플에서 SPM을 만든 이유는 Swift 생태계의 성장을 위해서이다. Swift는 cross-platform 언어이고, 해당 언어를 지원하는 플랫폼에서 일관된 방식으로 코드를 구성하고 실행할 수 있는 툴이 필요했다. 그리고 Swift로 작성된 라이브러리가 쉽게 배포되고 사용되면서 성장하는 Swift 언어의 미래를 위해 SPM이 만들어졌다.

SPM은 Swift 3.0 이상부터 Swift에 포함되서 별다른 설치가 필요 없고, Xcode가 없는 리눅스와 같은 플랫폼에서 사용할 수 있다. 그리고 Xcode 11.0 버전 이상부터 Xcode 자체에 통합되어 있기 때문에 GUI를 통해 사용할 수 있다는 장점을 갖는다. 또한 SPM은 상호 의존하는 여러 라이브러리를 사용할 때 발생할 수 있는 의존성 문제를 해결한다. 만약 모듈화된 프로젝트 내부의 A, B 프로젝트에 대한 종속성을 CocoaPods으로 관리할 때, A 프레임워크에서 사용하던 pod을 B 프레임워크에서 사용하려면, Podfile에 B 프레임워크를 대상으로  pod 의존성을 수동으로 추가해야 한다. 하지만 SPM은 내부 모듈에 사용되는 의존성을 자동으로 관리해주고, 의존성이 추가되거나 변경될 때마다 `pod install`이라는 부가 작업을 하지 않아도 된다.

&nbsp;
## Swift Package

SPM이 관리하는 Swift Package란 무엇일까? Swift Package는 Swift 소스 파일과 manifest 파일을 포함한다. manifest 파일은 `Package.swift`라는 이름을 가지며, `PackageDescription` 모듈을 이용해 패키지의 이름과 콘텐츠를 정의한다.

### Package Manifest

`Package.swift` 파일의 소스이다. 패키지의 이름과 콘텐츠를 정의하기 위해 `PackageDescription` 모듈을 import 한다. 패키지의 내부 구조를 살펴보자.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/172773802-1ccb3c5b-3b84-4e8b-8764-7efdf5a92172.png">
</p>

- `name` : 패키지의 이름이다.
- `products` : 패키지에 포함되는 라이브러리 또는 실팽 파일이다. 라이브러리에는 다른 Swift 코드에서 가져올 수 있는 모듈이 포함되어 있으며, 실행 파일은 운영 체제에서 실행할 수 있는 프로그램이다. 패키지로부터 어떠한 타겟을 노출시키고, 다른 패키지에서 사용할 수 있도록 한다. [RxSwift](https://github.com/ReactiveX/RxSwift/blob/main/Package.swift)의 manifest 파일을 살펴보자.

    ```swift
    products: [
      .library(name: "RxSwift", targets: ["RxSwift"]),
      .library(name: "RxCocoa", targets: ["RxCocoa"]),
      .library(name: "RxRelay", targets: ["RxRelay"]),
      ...
    ]
    ```

    `products`안에 *RxSwift* 외에 *RxCocoa*, *RxRelay* 등이 추가되어 있기 때문에, *RxSwift*의 패키지를 추가할 때, `product`를 선택할 수 있다.

    <p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/172792012-23e482bd-c570-426e-b11f-37fc3a15d994.png">
    </p>

- `dependencies` : 패키지의 코드에 필요한 모듈이다. 패키지 소스에 대한 상대 또는 절대 URL과 사용할 수 있는 패키지 버전에 대한 요구 사항의 집합으로 구성된다. 예를 들어 [Moya](https://github.com/Moya/Moya/blob/master/Package.swift)의 manifest 파일을 살펴보면 *Alamofire*를 의존성으로 추가하고 있고, *Alamofire* 5.0.0 버전 이상으로 최소 버전을 제한하고 있다.

    ```swift
    dependencies: [
      .package(url: "https://github.com/Alamofire/Alamofire.git", upToNextMajor(from: "5.0.0")),
        ...
    ]
    ```

- `targets` : 타겟은 모듈로서, 패키지의 내부적인 모듈을 구성하는 방법이다. 위 `Package.swift`과 같이 세 개의 타겟을 추가하면 패키지의 구조를 살펴보자. Sources 디렉터리에 타겟의 모든 파일이 위치하고, 각 타겟은 타겟 이름과 동일한 이름으로 하위 디렉터리에 위치한다. 또한 `testTarget`은 따로 Test 디렉터리의 하위 디렉터리에 위치한다.

    ```
    dealeer
    |-- Package.swift
    |-- Sources
    |    ∟ libdealer
    |       ∟ ...
    |    ∟ dealer
    |       ∟ ...
    |-- Tests
    |    ∟ dealearTests
    |       ∟ ...
    ```

    만약 타겟에 리소스가 있을 경우 `resources` 파라미터에 추가한다. Xcode 12부터 `xcassets`와 `storyboard`와 같은 파일은 컴파일러가 목적이 분명하다고 판단해 자동으로 번들화를 하고, png 파일이나 디렉터리는 명시적으로 추가를 해야한다. 아래는 `MyTarget` 타겟에 포함되어 있는 `Icon.png` 파일과 `Data` 디렉터리를 번들화하기 위해 추가한 소스이다.

    ```swift
    targets: [
      .target(
        name: "MyTarget",
        reserouces: [
          .process("Icon.png"),
          .copy("Data")
        ]
      )
    ]
    ```

    타겟에서 `Icon.png` 파일을 사용하고자 할 때, SPM에서 제공하는 `Bundle.module`을 통해 접근한다.

    ```swift
    let image = UIImage(named: "Icon", in: Bundle.module)
    ```

&nbsp;
## Swift Package 의존성 추가

실제로 프로젝트 타겟에 Package 의존성을 추가하기 위한 과정을 살펴보자.

1. Package 의존성을 추가할 프로젝트의 타겟을 선택한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173229941-991755d8-bcee-45c9-a8e0-75804c6d4f1c.png" height="250">
</p>

&nbsp;

2. `[General]` - `Frameworks, Libraries, and Embedded Content`에서 +를 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173229942-16abaeae-758a-4231-a948-9667babb8014.png">
</p>

&nbsp;

3. 좌측 하단에 `Add Other...` -> `Add Package Dependency...`를 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173229943-f67a29d2-6cf0-4579-a0a7-aeaf15b96c4d.png" height="400">
</p>

&nbsp;

4. Package 저장소의 URL을 입력해서 원하는 Package를 찾는다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173229946-acf06dce-1632-4af2-80a6-a68607744428.png">
</p>

&nbsp;

5. `Dependency Rule`을 통해 원하는 종속성의 새 버전을 지정할 수 있다. Package 저장소의 특정 버전, 브랜치, 커밋을 기준으로 종속성을 추가할 수 있다. `Dependency Rule`은 Package를 사용하면서 언제든지 변경할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173229940-397fdb74-7c6d-46b0-91eb-bc44c6adad50.png">
</p>

`Dependency Rule`은 총 6개의 옵션이 있다.

- *Up to Next Major Version* : 지정한 범위에서 최대 버전으로 설정한다. 만약 범위에 속하는 새로운 버전이 올라오면 자동으로 업데이트된다.
- *Up to Next Minor Version* : 지정한 범위에서 최소 버전으로 설정한다.
- *Range of Versions* : 직접 원하는 버전을 설정한다.
- *Exact Version* : 하나의 버전만을 설정한다.
- *Branch* : 저장소의 특정 브랜치으로 설정한다. 만약 브랜치에서 새로운 커밋이 올라오면 Package 의존성이 자동으로 업데이트된다.
- *Commit* : 저장소의 특정 커밋으로 설정한다.

&nbsp;

6. 원하는 Package Product를 선택한다. *Kingfisher* 같은 경우에는 패키지에 하나의 Product만을 포함하고 있지만, 여러 개의 Product 중에서 원하는 Product만을 선택할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173229947-badde624-d962-4db5-be8f-66361e2fa0a7.png">
</p>

&nbsp;

7. 다시 `Frameworks, Libraries, and Embedded Content`를 보면 정상적으로 Embed된 것을 확인할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173229948-8c56595c-1bad-4e49-a756-e62e68fcb50f.png">
</p>

&nbsp;

8. `Project Navigator`를 살펴보면 Package 의존성이 추가되었다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/173229950-7f201839-3540-4a99-8997-252b5996a6c6.png" height="300">
</p>