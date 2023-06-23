# VIPER

MVC, MVP과 같은 기존의 패턴들은 대규모 애플리케이션에서 복잡성이 증가하면 코드의 가독성이 떨어지고 유지보수가 어려워지는 문제가 발생하였다. 이를 개선하고자 MVVM 아키텍처 패턴이 소개되었지만 매한가지였다. VIPER 패턴은 2014년에 Mutual Mobile의 엔지니어인 Jeff Gilber와 Conrad Stoll이 소개한 아키텍처 패턴으로, **Clean Architecture**와 **SOLID 원칙**을 따른다. 앱의 각 기능을 **독립적인 모듈로 분리**하는 방식을 제안해서 코드의 테스트 용이성과 유지보수성을 크게 향상시킨다. 하지만 VIPER 패턴은 다른 아키텍처 패턴에 비해 구성 요소가 많아서 대형 프로젝트에 어울린다. 만약 **작은 프로젝트**에서 채택한다면 **오버 엔지니어링**이 되서 비효율적일 수 있다.

&nbsp;
## 구성 요소

<p align="center">
<img src="https://github.com/anjaeyoung26/GithubActions/assets/61190690/d3cc8cb7-059f-48eb-808f-ba3cd95976cc">
</p>

- **View**: Presenter가 제공하는 데이터를 표시하고 사용자의 행동을 Presenter에게 전달한다.
- **Interactor**: 비즈니스 로직을 수행한다. Presenter로부터 요청을 받아, 데이터를 처리하고 결과를 Presenter에게 전달한다.
- **Presenter**: Interactor로부터 데이터를 받아 View에게 전달하고, View의 사용자 입력을 Interactor에게 전달한다.
- **Entity**: 앱의 기본 모델 객체를 나타낸다. 앱에서 사용되는 가장 기본적인 데이터 단위이다.
- **Router**: 화면 전환 로직을 관리한다. 어떤 화면으로 이동할지 결정하고 그에 해당하는 로직을 수행한다.
- **Wireframe**: 각 모듈을 생성하고, 전체적인 화면 흐름을 관리한다.

### Router vs Wireframe

**Router**는 VIPER 모듈 내에서의 화면 전환을 담당한다. 예를 들어, 같은 모듈 내에서 상세 화면으로 전환하거나, 모달 창을 닫는 등의 작업을 처리한다. 반면에 **Wireframe**은 각 VIPER 모듈을 생성하는 역할과 더불어, 애플리케이션 전체의 화면 흐름을 관리하는 역할을 수행한다. **Wireframe**은 **Router** 보다 앱의 구조에 대한 더 넓은 지식을 가지고 있으며, 여러 VIPER 모듈 사이에서 화면 전환을 처리한다. 다만, 이는 VIPER 패턴의 일반적인 적용 방식이며, 실제로는 **애플리케이션의 요구 사항과 구조에 따라** **Router**와 **Wireframe**의 역할을 다르게 정의하거나, 이 둘을 합쳐서 하나의 구성 요소로 만드는 등 다양한 방식으로 적용할 수 있다.

&nbsp;
## 예제 코드

### 프로토콜

```swift
protocol ListViewProtocol: AnyObject {
  var presenter: ListPresenterProtocol! { get set }

  // Presenter -> View
  func showItems(_ items: [Item])
}

protocol ListPresenterProtocol: AnyObject {
  var view: ListViewProtocol? { get set }
  var interactor: ListInteractorInputProtocol? { get set }
  var wireframe: ListWireframeProtocol? { get set }

  // View -> Presenter
  func viewDidLoad()
  func didSelectItem(_ item: Item)
}

protocol ListInteractorInputProtocol: AnyObject {
  var presenter: ListInteractorOutputProtocol? { get set }

  // Presenter -> Interactor
  func retrieveItems()
}

protocol ListInteractorOutputProtocol: AnyObject {
  // Interactor -> Presenter
  func didRetrieveItems(_ items: [Item])
}

protocol ListWireframeProtocol: AnyObject {
  // Presenter -> Wireframe
  func showDetail(for item: Item)
}
```

### 사용자가 리스트의 아이템을 클릭해서 상세 화면으로 진입

1. **View**: 사용자의 아이템 선택을 감지하고 이를 **Presenter**에게 전달한다.
    
    ```swift
    extension ListView: UITableViewDelegate {
      func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        presenter.didSelectItem(items[indexPath.row])
      }
    }
    ```
    
2. **Presenter**: **View**로부터 아이템 선택 이벤트를 전달받으면, 해당 아이템을 **Wire frame**에게 전달하고 상세 화면으로 전환을 요청한다.
    
    ```swift
    extension ListPresenter: ListViewOutputProtocol {
      func didSelectItem(_ item: Item) {
        wireframe?.showDetail(for: item)
      }
    }
    ```
    
3. **Wireframe**: **Presenter**로부터 상세 화면 전환을 요청받으면 해당 아이템을 이용해 상세 화면을 생성하고 화면을 전환한다.
    
    ```swift
    class ListWireframe: ListWireframeProtocol {
      func showDetail(for item: Item) {
        let detailViewController = DetailWireframe.createDetailModule(for: item)
        navigationController.pushViewController(detailViewController, animated: true)
      }
    }
    ```
    
    이때 상세 화면 모듈은 **DetailWireframe**을 통해 생성한다.
    
    ```swift
    class DetailWireframe: DetailWireframeProtocol {
      class func createDetailModule(for item: Item) -> UIViewController {
        let view = DetailView()
        let presenter = DetailPresenter()
        let interactor = DetailInteractor()
        let wireframe = DetailWireframe()
    
        view.presenter = presenter
        presenter.view = view
        presenter.interactor = interactor
        presenter.wireframe = wireframe
        interactor.presenter = presenter
        presenter.item = item

        return view
      }
    }
    ```
    

### Mock object를 통한 테스트

위 ‘사용자가 리스트의 아이템을 클릭해서 상세 화면으로 진입’ 플로우에서 `ListPresenter`를 테스트하는 코드

```swift
class MockListWireframe: ListWireframeProtocol {
  var showDetailCalled = false
  var lastItemPassed: Item?

  func showDetail(for item: Item) {
    showDetailCalled = true
    lastItemPassed = item
  }
}

class ListPresenterTests: XCTestCase {
  var presenter: ListPresenter!
  var mockWireframe: MockListWireframe!

  override func setup() {
    super.setup()
		
    mockWireframe = MockListWireframe()
    presenter = ListPresenter()
    presenter.wireframe = mockWireframe
  }

  func testDidSelectItem {
    let item = Item()

    presenter.didSelectItem(item)

    XCTAssertTrue(mockWireframe.showDetailCalled)
    XCTAssertEqual(mockWireframe.lastItemPassed, item)
  }
}
```