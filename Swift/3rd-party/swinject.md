# Swinject 란?

간편하게 의존성 주입을 할 수 있는 프레임워크 이다. 앱을 느슨하게 결합된 Component로 분할해서 보다 쉽게 개발, 테스트 및 유지 관리할 수 있도록 도와준다. 또한 Swift 타입을 지원하고 제너릭 타입 시스텝 및 일급 함수로 구동되기 때문에, 앱의 개체 종속성을 간단하게 정의할 수 있다.

&nbsp;
## [Basics](https://github.com/Swinject/Swinject/blob/master/Documentation/README.md#user-content-pure-swift-type-support:~:text=Table%20of%20Contents-,Basics,-DI%20Container)

### [1. Registration](https://github.com/Swinject/Swinject/blob/master/Documentation/DIContainer.md#registration-in-a-di-container:~:text=are%20used%20interchangeably.-,Registration%20in%20a%20DI%20Container,-A%20service%20and)

Swinject는 의존성 주입 컨테이너에 간단하게 등록하는 `register` 메서드를 제공한다. Component가 다른 서비스에 의존하는 경우 팩토리 메서드 내부에서 `resolve` 해서 주입할 수 있다.

```swift
let container = Container()
container.register(Animal.self) { _ in Cat(name: "Mimi") }
container.register(Person.self) { r in
  PetOwner(name: "Stephen", pet: r.resolve(Animal.self)!)
}
```

컨테이너에 등록된 Component만 `resolve` 될 수 있으므로, 반환 타입은 `Optional` 이다.

```swift
let animal = container.resolve(Animal.self) // Optional<Animal>
```
&nbsp;
### [2. Named Registration](https://github.com/Swinject/Swinject/blob/master/Documentation/DIContainer.md#registration-in-a-di-container:~:text=Named%20Registration%20in%20a%20DI%20Container)

이름을 지정해서 하나 이상의 Component를 등록할 수 있다.

```swift
container.register(Animal.self, name: "cat") { _ in Cat(name: "Mimi") }
container.register(Animal.self, name: "dog") { _ in Dog(name: "Hachi") }
```
&nbsp;
### [3. Registration with Arguments](https://github.com/Swinject/Swinject/blob/master/Documentation/DIContainer.md#registration-in-a-di-container:~:text=Registration%20with%20Arguments%20in%20a%20DI%20Container)

`register` 메서드는 인수를 전달해서 Component를 등록할 수 있다.

```swift
container.register(Animal.self) { _, name in
  Horse(name: name)
}
```

그리고 Component를 `resolve` 할 때 런타임 인수를 전달한다.

```swift
let animal1 = container.resolve(Animal.self, argument: "Spirit")!
```
&nbsp;
### [4. Circular Dependencies](https://github.com/Swinject/Swinject/blob/master/Documentation/CircularDependencies.md#:~:text=Blame-,Circular%20Dependencies,-Circular%20dependencies%20are)

서로 의존하는 두 개의 Component를 컨테이너에 등록할 때 무한 재귀가 발생한다.

```swift
protocol ParentProtocol: AnyObject { }
protocol ChildProtocol: AnyObject { }

class Parent: ParentProtocol {
  let child: ChildProtocol?

  init(child: ChildProtocol?) {
    self.child = child
  }
}

class Child: ChildProtocol {
  weak var parent: ParentProtocol?
}
```

Swinject는 아래와 같이 `initCompleted`를 통해 무한 재귀를 방지할 수 있다.

```swift
container.register(ParentProtocol.self) { r in
  Parent(child: r.resolve(ChildProtocol.self)!)
}

container.register(ChildProtocol.self) { _ in Child() }
  .initCompleted { r, c in
    let child = c as! Child
    child.parent = r.resolve(ParentProtocol.self)
  }
```

&nbsp;
### [5. Object Scopes](https://github.com/Swinject/Swinject/blob/master/Documentation/ObjectScopes.md#:~:text=Blame-,Object%20Scopes,-Object%20scope%20is)

Component를 컨테이너에 등록할 때 공유할 수 있는 범위를 결정할 수 있다. 만약 Component가 값 유형을 반환하는 경우 Object Scope는 무시된다.

[5-1. Transient](https://github.com/Swinject/Swinject/blob/master/Documentation/ObjectScopes.md#:~:text=Built%2Din%20scopes-,Transient,-If%20ObjectScope.transient)

컨테이너에서 제공하는 인스턴스는 공유되지 않으며 항상 새 인스턴스를 생성한다. 이 Scope를 지정하면 [순환 종속성 해결](https://www.notion.so/Swinject-3684f936f9d94d97aae93dab5ace66ee)이 제대로 작동하지 않는다.

[5-2. Graph](https://github.com/Swinject/Swinject/blob/master/Documentation/ObjectScopes.md#:~:text=Graph%20(the%20default%20scope))

Transient와 같이 인스턴스가 항상 생성되지만, `register`의 클로저에서 `resolve`된 인스턴스는 클로저 내부에서 공유된다. 인스턴스의 Scope를 직접 명시하지 않으면 Graph 이다.

```swift

container.register(Animal.self) { _ in Cat(name: "Mimi") }
container.register(Person.self) { r in
  let pet = r.resolve(Animal.self)! // 클로저 내부에서 공유
}
```

[5-3. Container](https://github.com/Swinject/Swinject/blob/master/Documentation/ObjectScopes.md#:~:text=the%20object%20graph.-,Container,-In%20ObjectScope.container)

인스턴스는 컨테이너와 해당 컨테이너의 자식 컨테이너 내에서 공유된다. 이 Scope는 다른 DI 프레임워크에서 Singleton 이라고도 한다.

[5-4. Weak](https://github.com/Swinject/Swinject/blob/master/Documentation/ObjectScopes.md#:~:text=other%20DI%20frameworks.-,Weak,-In%20ObjectScope.weak)

컨테이너가 제공하는 인스턴스는 강한 참조가 있는 한 컨테이너와 해당 자식 컨테이너 내에서 공유된다. 만약 인스턴스에 대한 강한 참조가 존재하지 않으면 더 이상 공유되지 않으며 새 인스턴스가 생성된다. 값 유형은 이 Scope에서 공유되지 않는다.

&nbsp;
## [Advanced Features](https://github.com/Swinject/Swinject/blob/master/Documentation/README.md#user-content-pure-swift-type-support:~:text=Misc-,Advanced%20Features,-Container%20Hierarchy)

### [Modularizing Service Registration](https://github.com/Swinject/Swinject/blob/master/Documentation/Assembler.md#:~:text=Modularizing%20Service%20Registration)

Swinject는 서비스를 그룹화할 수 있는 Assembly 기능을 제공한다.

1. 유사한 서비스를 한 곳에 보관하여 정리된 상태를 유지한다.
2. 그룹 내에서 컨테이너를 공유한다.
3. 다른 Assembly 구성을 등록할 수 있다. 이는 테스트 시 Mock Assembly로 대체하는데 편리하다.
4. 컨테이너가 완전히 구성되면 알림을 받는다.

&nbsp;
### [Assembly](https://github.com/Swinject/Swinject/blob/master/Documentation/Assembler.md#:~:text=to%20this%20feature.-,Assembly,-The%20Assembly%20is)

Assembly는 서비스를 등록할 수 있는 공유 컨테이너를 제공하는 프로토콜이다. 공유 컨테이너는 Assembler에 등록된 모든 Assembly의 서비스가 포함된다.

```swift
class ServiceAssembly: Assembly {
  func assemble(container: Container) {
    container.register(FooServiceProtocol.self) { r in
      return FooService()
    }
    container.register(BarServiceProtocol.self) { r in
      return BarService()
    }
  }
}

class ManagerAssembly: Assembly {
  func assemble(container: Container) {
    container.register(FooManagerProtocol.self) { r in
      return FooManager(service: r.resolve(FooServiceProtocol.self)!)
    }
    container.register(BarManagerProtocol.self) { r in
      return BarManager(service: r.resolve(BarServiceProtocol.self)!)
    }
  }
}
```

`ManagerAssembly`는 `ServiceAssembly`에서 등록된 Component를 활용한다. `FooServiceProtocol` 및 `BarServiceProtocol`이 등록된 위치를 신경쓰지 않고 다른 위치에 등록하면 된다. 또한 Assembly의 컨테이너가 완전히 구성되면 `loaded(resolver:)` 가 호출된다.

```swift
class ServiceAssembly: Assembly {
	
  ...

  func loaded(resolver: Resolver) {
		
  }
}
```

&nbsp;
### [Assembler](https://github.com/Swinject/Swinject/blob/master/Documentation/Assembler.md)

Assembler는 Assembly 인스턴스와 컨테이너 관리를 담당한다. Assembler를 사용하면 컨테이너는 Assembler에 등록된 Assembly에만 노출된다.

```swift
let assembler = Assembler([
  ServiceAssembly(),
  ManagerAssembly()
])

let fooManager = assembler.resolver.resolve(FooManagerProtocol.self)!
```