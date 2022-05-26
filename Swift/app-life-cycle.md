## 목차
- [앱의 생명주기란](#앱의-생명주기란)
- [App-Based Life-Cycle](#app-based-life-cycle)
    - [앱의 다섯 가지 상태](#앱의-다섯-가지-상태)
- [Scene-Based Life-Cycle](#scene-based-life-cycle)
    - [Multiple Windows](#multiple-windows)
    - [UIScene](#uiscene)
    - [UIWindowScene](#uiwindowscene)
    - [UISceneSession](#uiscenesession)
    - [UISceneDelegate](#uiscenedelegate)
    - [Scene의 다섯 가지 상태](#scene의-다섯-가지-상태)
    - [Session Life-Cycle](#session-life-cycle)
- [Call Stack 비교](#call-stack-비교)
---
## 앱의 생명주기란

앱의 실행부터 종료까지의 주기를 뜻하며, 시스템 알림에 응답하고 기타 중요한 시스템과 관련된 이벤트를 처리하는 단계이다. 또한 `UIKit`과 관련된 `UIViewController`, `UITableViewCell` 등 여러가지 객체만의 생명주기가 존재한다. 그렇다면 앱의 생명주기는 왜 중요할까? iOS와 같은 모바일 환경에서는 하나의 CPU가 제한된 메모리와 시간을 여러 앱에 할당하게 된다. 이때 가장 우선순위가 높은 앱은 사용자에게 보여지는 앱이다. 따라서 나머지 앱은 가능한 적은 리소스를 사용해야 한다. 이를 위해 앱의 상태 변화를 아는 것이 중요하다.

`UIKit` 프레임워크는 앱의 상태가 변경되면 적절한 Delegate 객체의 메소드를 호출한다.

- iOS 13 이상 : `UISceneDelegate` 객체를 통해 Scene-Based life-cycle 이벤트에 응답한다.
- iOS 12 이하 : `UIApplicationDelegate` 객체를 통해 App-Based life-cycle 이벤트에 응답한다.

> 하지만 iOS 13 이상에서도 `UIApplicationDelegate` 객체를 통한 앱의 생명주기를 관리할 수 있다. Scene-Based의 용도는 iOS 13에서 추가된 iPad의 Multiple Windows를 사용하기 위함인데, Multiple Windows를 사용하지 않는다면 낯선 Scene-Based를 사용할 필요가 없다.

&nbsp;
## App-Based Life-Cycle

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170403945-95e91465-5d46-4943-bca1-6da2b178ab44.jpeg" width="600">
</p>

iOS 12 이하 혹은 Scene-Based life-cycle을 채택하지 않는 iOS 13 이상의 앱에서 `AppDelegate`는 두 가지 역할이 있다. 앱이 실행되거나 종료되는 **Process life-cycle**과 앱이 포그라운드, 백그라운드에 진입하는 **UI life-cycle**에 대한 이벤트에 응답하는 것이다. 추후 Scene-Based에서 `AppDelegate`의 역할과 비교해보자.

&nbsp;
### 앱의 다섯 가지 상태

앱의 생명주기에 따라 다섯 가지의 상태로 분류할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170414090-4ec68ef8-2ba5-4566-bedf-f6654c94762f.png" height="500">
</p>

1. **Foreground** : 앱이 화면에 표시되고 있어 사용자가 보고 있는 상태이다. 따라서 CPU를 비롯한 시스템 자원의 우선순위가 높다.

    ```swift
    func applicationWillEnterForeground(_ application: UIApplication)
    ```

    `Foreground` 상태는 앱이 이벤트를 받을 수 있을 때 `Active`, 받지 못할 때 `Inactive` 상태로 나뉜다.

    ```swift
    func applicationDidBecomeActive(_ application: UIApplication)
    func applicationWillResignActive(_ application: UIApplication)
    ```

    앱은 대부분의 경우 `Active` 상태이다. 하지만 언제 `Inactive` 상태가 되는지 짐작하기 어렵다. 일반적으로 `Inactive` 상태가 되는 경우를 살펴보자.

    - `UIAlertController`가 표시될 때
    - App Switcher를 사용할 때
    - 알림 센터, 제어 센터를 볼 때
    - 앱이 백그라운드로 전환될 때
    - 전화가 올 때

2. **Background** : 앱이 화면에서 내려가 사용자가 보고 있지 않는 상태이다. 그러나 음악이 재생되고 있거나 네트워크 통신 등 작업이 진행 중이다. 모바일 환경에서 한정된 리소스로 인해 백그라운드 상태의 앱은 가능한 적은 리소스를 사용해야 한다.

    ```swift
    func applicationDidEnterBackground(_ application: UIApplication)
    ```

3. **Not running** : 앱이 실행되지 않은 상태이다. 하지만 푸시와 같이 특정한 이벤트는 전달받을 수 있다.

4. **Suspended** : 앱이 백그라운드에 있지만 실행되는 코드가 없는 상태이다. 시스템은 메모리가 부족하다면 `Suspended` 상태의 앱을 메모리에서 제거한다.

&nbsp;
## Scene-Based Life-Cycle 

### Multiple Windows

`UISceneDelegate`는 iOS 13부터 iPad에서 Multiple Windows를 지원하게 되면서 추가되었다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170434815-8d04c491-7dc7-4520-baa3-d0a2125604e9.png" width="500">
</p>

위 그림에서 iPad의 메모장 앱을 사용 중이다. 언뜻보면 두 개의 메모장 앱이 실행되고 있는 것으로 보인다. 이는 iPad에서 Multiple Windows를 통해 메모장 앱에서 두 개의 Scene을 표시한 모습이다. 각 Scene에는 `UISceneDelegate`가 1:1로 매핑되며, `UISceneDelegate`에서 각 Scene의 UI life-cycle 이벤트를 응답한다. Multiple Windows 구조를 살펴보면 아래와 같다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170426136-457f53fa-b844-4091-a9a3-ab6be40170cb.png" width="600">
</p>

`UIScreen`과 `UIWindow` 사이에 낯선 객체가 보인다. Scene 개념과 함께 새롭게 추가된 객체들에 대해서 알아보자.

&nbsp;
### UIScene

```swift
@MainActor class UIScene : UIResponder
```

`UIScene`은 하나의 앱 UI instance를 의미한다. iPad 그림에서는 두 개의 `UIScene`이 있다라고 말할 수 있다. 각각의 `UIScene`은 같은 메모리와 앱의 프로세스 공간을 공유하면서 서로 동시에 실행된다. 이러한 성질때문에 여러 개의 `UIScene`과 `UISceneDelegate`을 동시에 사용하여 Multiple Windows를 제공한다.

모든 Scene 객체는 `UISceneDelegate` 프로토콜을 채택하는 `delegate` 프로퍼티가 있다. Scene의 상태가 변화하면 연결된 `delegate`에게 알리고 등록된 옵저버들에게 적절한 `NSNotification`을 게시한다. 만약 Scene이 백그라운드에 진입하면 두 개의 방식으로 우리에게 알린다.

```swift
didEngerBackgroundNotification
sceneDidEnterBackground(_:)
```

&nbsp;
### UIWindowScene

```swift
@MainActor class UIWindowScene : UIScene
```

`UIScene`을 상속받은 클래스로 앱에서 하나 이상의 윈도우를 관리한다. 또한 `UISceneDelegate` 프로토콜을 채택하는 `UIWindowSceneDelegate`와 적절한 `NSNotification`을 통해 Scene의 상태 변화를 알린다.

위 Multiple Windows의 구조를 보다시피 `UIKit`은 `UIScene` 대신 `UIWindowScene` 객체를 생성한다. 하지만 `UIWindowScene`은 `UIScene`을 상속받은 클래스로 결국에는 `UIScene` 클래스의 메소드와 프로퍼티를 사용해서 Scene의 정보에 접근한다.

&nbsp;
### UISceneSession

```swift
@MainActor class UISceneSession : NSObject
```

`UISceneSession` 객체는 Scene 인스턴스의 런타임을 관리한다. 사용자가 혹은 직접 programatically하게 새로운 Scene을 요청하면 시스템은 `UISceneSession` 개체를 생성한다. 세션에는 고유한 식별자와 Scene을 구성하기 위한 세부 정보가 포함된다.

&nbsp;
### UISceneDelegate

하나의 화면에 대한 UI life-cycle 이벤트를 응답하는 `UIApplication`의 역할이 분리되었다. Multiple Windows 환경에서 각 Scene과 매핑되는 `UISceneDelegate`가 맡는다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170422799-64cd1ad1-3c24-4b8e-8950-b87be56b46ba.jpeg" width="600">
</p>

그렇다면 `UIApplicationDelegate`의 UI life-cycle 관련 메소드들이 `UISceneDelegate`에서는 어떻게 대체됐을까?

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170406589-51b64c9b-b7ff-4d23-9eb3-85cf64b78399.jpeg" width="600">
</p>

&nbsp; 
### Scene의 다섯 가지 상태

각 Scene은 전달된 이벤트에 따라 다섯 가지의 상태로 분류된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170423529-76e34a93-3bdd-4f84-b405-e3ded7a4247a.png" height="500">
</p>

`Unattached` 상태를 제외하고 나머지 네 개는 App-Based life-cycle과 동일하다. 그럼 `Unattached`는 어떤 상태일까? 위치 상으로 동일한 App-Based의 `Not Running` 상태와 비교해보자. App-Based의 `Not Running` 상태는 `Background` 혹은 `Suspended` 상태로 전환될 수 있었지만, `Unattached`는 `Background` 상태와만 전환될 수 있다. 따라서 Scene-Based의 `Suspended`에서 `Unattached`로 변하기 위해서는 `Background` 상태를 거쳐야 한다.

사용자 또는 시스템이 앱에 새로운 Scene을 요청하게 되면 `UIKit`은 `Unattached` 상태의 Scene을 생성한다. 사용자가 요청한 Scene은 화면에 보이는 Foreground로 옮겨지고, 시스템이 요청한 Scene은 Background로 옮겨져서 위치 감지와 같은 이벤트를 처리한다. 이후 사용자가 앱을 종료하거나 Scene의 리소스를 회수할 때 다시 `Unattached` 상태가 된다.

&nbsp;
### Session Life-Cycle

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170405800-d84539b8-91fe-43fc-aca7-8fa990869523.jpeg" width="600">
</p>

앞서 `UIApplication` 역할의 일부가 각 `UISceneDelegate`에 분리된 대신에 추가된 점이 있다. 이는 새로운 `UISceneSession` 생성되거나 삭제될 때, 이를 시스템이 `UIApplicationDelegate`에게 알리는 Session life-cycle이다.

새로운 `UISceneSession`이 생성될 때:

```swift
func application(_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration
```

기존의 `UISceneSession`이 삭제될 때:

```swift
func application(_ application: UIApplication, didDiscardSceneSessions sceneSessions: Set<UISceneSession>)
```

&nbsp;
## Call Stack 비교

앱의 상태가 변하는 네 가지 시나리오에서 App-Based와 Scene-Based에서 호출되는 메소드를 알아보자. 

> Scene-Based 에서는 연결된 Scene 마다 생명주기와 관련된 메소드가 호출된다. 예를들어 두 개의 Scene을 사용하고 있는 앱이 백그라운드로 전환한다면 `sceneDidEnterBackground(_:)`가 두 번 호출된다.

**앱 실행:**

```swift
// App-Based
application(_:willFinishLaunchingWithOptions:)
application(_:didFinishLaunchingWithOptions:)
applicationDidBecomeActive(_:)

// Scene-Based
application(_:willFinishLaunchingWithOptions:)
application(_:didFinishLaunchingWithOptions:)
scene(_:willConnectTo:options:)
sceneWillEnterForeground(_:)
sceneDidBecomeActive(_:)
```

**앱 종료:**

```swift
// App-Based
applicationWillResignActive(_:)
applicationDidEngerBackground(_:)
applicationWillTerminate(_:)

// Scene-Based
sceneWillResignActive(_:)
sceneDidDisconnect(_:)
applicationWillTerminate(_:)
```

**백그라운드 이동:**

```swift
// App-Based
applicationWillResignActive(_:)
applicationDidEnterBackground(_:)

// Scene-Based
sceneWillResignActive(_:)
sceneDidEnterBackground(_:)
```

**백그라운드에서 앱으로:**

```swift
// App-Based
applicationWillEnterForeground(_:)
applicationDidBecomeActive(_:)

// Scene-Based
sceneWillEnterForeground(_:)
sceneDidBecomeActive(_:)
```

Scene-Based을 보면 앱이 실행되거나 종료되는 Process life-cycle은 여전히 `AppDelegate`가 수행한다.