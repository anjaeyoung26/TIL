# Coordinator가 필요한 이유

Coordinator 패턴은 [Soroush Khanlou](https://www.notion.so/Coordinator-05d78727d3824f8a8eedf438aefb7434) 라는 개발자의 블로그에 [The Coordinator](https://khanlou.com/2015/01/the-coordinator/) 글에서 시작되었다. 그는 아래의 코드를 예시로 들면서, 뷰 컨트롤러에 많은 흐름(Flow) 로직과 비즈니스 로직이 얽혀 있어서 Massive 해지는 것을 문제로 언급했다.

```swift
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
  let object = self.dataSource[indexPath] // 1
  let detailViewController = SKDetailViewController(with: object) // 2
  self.navigationController?.present(detailViewController, animated: true, completion: nil) // 3
}
```

1. 선택한 셀의 데이터를 가져온다.
2. 뷰 컨트롤러를 만든다.
3. 뷰 컨트롤러를 푸시한다.

간단한 앱이나 뷰 컨트롤러에서는 문제 없이 동작한다. 하지만 앱이 복잡해지면서, 위 동작이 다른 곳에서도 필요할 수 있다. 1번은 뷰 컨트롤러가 데이터소스를 소유하고 있기 때문에 문제 없다. 하지만 2번에서 뷰 컨트롤러는 다음 단계의 Flow를 인식하게 된다. 또한 3번에서 뷰 컨트롤러는 부모(네비게이션 컨트롤러)에게 동작을 지시한다. Khanlou는 이러한 뷰 컨트롤러가 사용자 흐름을 처리하는 행위는 범위(Scope)를 벗어난다고 주장한다. 왜냐하면 뷰 컨트롤러의 기본 클래스는 UI로 시작되며 View 객체이기 때문이다. 대신 Coordinator 라는 높은 수준의 객체로 관리하기를 권장한다.

### Coordinator 패턴의 이점

1. **뷰 컨트롤러의 고립** : 뷰 컨트롤러는 데이터를 표시하기만 한다. 어떤 동작이 발생할 때마다 Delegate에게 알리지만, Delegate가 누군지는 알 수 없다.

2. **뷰 컨트롤러의 재사용** : 다른 흐름(Flow)에서 사용하려면 Coordinator만 교체하면 된다.

&nbsp;
## Coordinator 패턴의 구조

Khanlou는 Coordinator 패턴을 제대로 실행하기 위해서, **앱의 전반적인 흐름을 관리하는 더 높은 수준의 Coordinator 가 필요하다고 한다.** 많은 예제 프로젝트에서 `AppDelegate` 혹은 `SceneDelegate`가 소유하고 있는 `AppCoordinator`를 볼 수 있다. 또한 **모든 Coordinator는 하위 Coordinator를 소유한다.** 이는 하위 Coordinator가 할당 해제되는 것을 방지하기 위해 배열을 사용한다. 또한 배열을 통해 Coordinator 트리를 얻을 수 있으며, 하위 Coordinator가 완료됐다는 신호를 보내면 할당을 해제한다.

### 예제 프로젝트 구현

만약 로그인, 메인 화면으로 구성된 앱이 있다면, Coordinator 트리는 아래와 같은 구조로 만들어진다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220254500-e9ae1f33-8727-44b3-bc5b-f01da4453233.png">
</p>

1. `Coordinator` 프로토콜을 구현한다.
    
    ```swift
    protocol Coordinator: class {
      var childCoordinators: [Coordinator] { get set }
      func start()
    }
    ```
    
2. `Coordinator` 프로토콜을 채택하는 `AppCoordinator`를 구현한다. `AppCoordinator`는 로그인 유무에 따라 로그인 화면 혹은 메인 화면을 표시한다.
    
    ```swift
    final class AppCoordinator: Coordinator {
      private var navigationController: UINavigationController?
      var childCoordinators: [Coordinator] = []
    	var isLoggedIn: Bool = false
    
      init(navigationController: UINavigationController) {
        self.navigationController = navigationController
      }
    
      func start() {
        if self.isLoggedIn {
          self.showMainViewController()
        } else {
          self.showLoginViewController()
        }
      }
    
      private func showMainViewController() {
    	
      }
    
      private func showLoginViewController() {
    		
      }
    }
    ```
    
3. `AppDelegate`에서 `AppCoordinator`를 구성한다.
    
    ```swift
    class AppDelegate: UIResponder, UIApplicationDelegate {
      var window: UIWindow?
    
      func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]? = nil
      ) -> Bool {
        let window = UIWindow(frame: UIScreen.main.bounds)
        self.window = window
    
        let navigationController = UINavigationController()
        self.window?.rootViewController = navigationController
    
        let coordinator = AppCoordinator(navigationController: navigationController)
        coordinator.start()
    
        self.window?.makeKeyAndVisible()
      }
    }
    ```
    
4. `Coordinator` 프로토콜을 채택하는 `LoginCoordinator`를 구현한다.
    
    ```swift
    final class LoginCoordinator: Coordinator {
      var childCoordinators: [Coordinator] = []
        
      private var navigationController: UINavigationController
        
      init(navigationController: UINavigationController) {
        self.navigationController = navigationController
      }
        
      func start() {
        let viewController = LoginViewController()
        self.navigationController.viewControllers = [viewController]
      }
    }
    ```
    
5. `LoginViewController`를 구현한다. 로그인 화면에서 로그인을 완료하면, 이를 `LoginCoordinator`에게 알리기 위해 `LoginViewControllerDelegate`도 구현한다.
    
    ```swift
    protocol LoginViewControllerDelegate {
      func login()
    }
    
    final class LoginViewController: UIViewController {
      var delegate: LoginViewControllerDelegate?
        
      override func viewDidLoad() {
        super.viewDidLoad()
        let item = UIBarButtonItem(title: "로그인", style: .plain, target: self, action: #selector(self.loginButtonDidTap))
        self.navigationItem.rightBarButtonItem = item
      }
        
      @objc
      func loginButtonDidTap() {
        self.delegate?.login()
      }
    }
    ```
    
6. `LoginCoordinator`는 `AppCoordinator`에게 로그인 완료를 알리기 위해 `LoginCoordinatorDelegate`를 구현하고 `LoginViewControllerDelegate`를 채택한다.
    
    ```swift
    protocol LoginCoordinatorDelegate {
      func didLoggedIn(_ coordinator: LoginCoordinator)
    }
    
    final class LoginCoordinator: Coordinator, LoginViewControllerDelegate {
      var delegate: LoginCoordinatorDelegate?
    
      ...
    
      func start() {
        let viewController = LoginViewController()
        viewController.delegate = self
    
        ...
      }
    
      func login() {
        self.delegate?.didLoggedIn(self)
      }
    }
    ```
    
7. `AppCoordinator`는 `LoginCoordinatorDelegate`를 채택하여 로그인이 완료되면 메인 화면을 표시한다.
    
    ```swift
    final class AppCoordinator: Coordinator, LoginCoordinatorDelegate {
    
      ...
    
      private func showLoginViewController() {
        let coordinator = LoginCoordinator(navigationController: self.navigationController)
        coordinator.delegate = self // LoginCoordinatorDelegate 채택
        coordinator.start()
        self.childCoordinators.append(coordinator)
      }
    
      func didLoggedIn(_ coordinator: LoginCoordinator) {
        // AppCoordinator가 가지고 있는 LoginCoordinator를 지운다.
        self.childCoordinators = self.childCoordinators.filter { $0 != coordinator }
        self.showMainViewController()
      }
    }
    ```
    
&nbsp;
# Reference

[Coordinator Pattern](https://zeddios.medium.com/coordinator-pattern-bf4a1bc46930)