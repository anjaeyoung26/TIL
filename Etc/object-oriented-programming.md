## 객체 지향 프로그래밍

*객체 지향 프로그래밍*이란 [명령형 프로그래밍](./imperative-and-declarative-programming.md/#명령형-프로그래밍)에 속하는 방법론으로, 프로그램을 단순히 데이터와 처리 방법으로 나누는 것이 아닌 수 많은 '객체'라는 기본 단위로 나누고 이들의 상호 작용으로 서술하는 방식이다. 여기서 '객체'란 하나의 역할을 수행하는 메서드와 변수의 묶음이다.

&nbsp;
## 배경

초기 프로그래밍 방식은 *절차적 프로그래밍* 방식이었다. 단순히 프로그램은 입력을 받아 명시된 순서대로 처리된 다음 결과를 내는 것뿐이라는 생각이 지배적이었다. 즉, 프로그램 자체가 가지는 기능에 대해서만 신경을 썼으며, 이 프로그램이 어떤 데이터를 취급하는 것인지에 대해서는 관심이 없었다. 그러나, 프로그램이 복잡해지면서 이러한 방식이 순서도로 나타내는 것이 불가능할 정도로 꼬인 '스파게티 코드'를 만들게 된다. 점차 명령어의 양이 많아지고, 특정 코드는 어디에 사용되는지 또는 어디까지 연결되는지 흐름을 파악하기 어려워졌다. 그에 따라 중복 코드에 대한 대처도 골치 아파졌다.

이 문제를 해결하기 위해 [Edsger Wybe Dijkstra](https://namu.wiki/w/%EC%97%90%EC%B8%A0%ED%97%88%EB%A5%B4%20%EB%8B%A4%EC%9D%B5%EC%8A%A4%ED%8A%B8%EB%9D%BC)라는 네덜란드 출신의 컴퓨터 과학자가 [GOTO문의 해로움](https://homepages.cwi.nl/~storm/teaching/reader/Dijkstra68.pdf)이라는 논문에서 프로그램일 프로시저(`printf`와 같이 리턴값이 없는 함수를 의미) 단위로 나누고 프로시저끼리 호출하는 *구조적 프로그래밍* 방식을 제안한다. *구조적 프로그래밍*은 *절차적 프로그래밍*의 하위 개념으로, 프로그램이라는 큰 문제를 해결하기 위해 몇 개의 작은 문제들로 나누어 해결하는 *하향식(Top-down)* 방식이라고도 한다. **하지만 프로시저는 데이터의 처리 방법을 구조화했을 뿐, 데이터 자체는 구조화하지 못해서 너무 추상적이었다.** 예를 들어, 도서관의 도서 관리 프로그램을 개발하기 위해 작업할 내용을 생각해보자.

- '책'이라는 자료형을 구현
- '책'에 대한 함수를 구현

*구조적 프로그래밍*에서는 책을 대출하는 함수, 책을 반납하는 함수를 구현하지만 '책'이라는 데이터 자체를 구조화하지 못한다. 따라서 책에 관한 코드가 같은 소스 파일에 있더라도 이들의 연관성은 단 번에 알아차리기 어렵다. 즉, **논리적으로 묵일 수 없는 구조이기 때문에 너무 추상적이라는 문제가 있다.** 그로인해 전역 네임스페이스 포화 문제와 엉뚱한 데이터가 엉뚱한 함수에 전달돼서 데이터를 오염시키는 문제 등이 발생한다. 이를 함수가 접근하는 데이터의 범위에 명시적인 제한을 걸거나 지역 변수, 구조체 등으로 일부 제어를 했지만 더 근본적인 해결책이 필요했다.

이를 극복하기 위한 대안으로 *객체 지향 프로그래밍*이 등장했다. 큰 문제를 작은 문제로 쪼개는 *하향식* 방식이 아닌, 우선 작은 문제들을 해결할 수 있는 '객체'들을 만든 뒤, 해당 객체들을 조합해서 큰 문제를 해결하는 *상향식(Bottom-up)* 방식을 도입했다. 이 '객체'라는 것은 최초로 독립성/신뢰성있게 구현해 놓으면, 이후에 객체를 재사용할 수 있다. 또한 상속을 통해 기능을 확장할 수 있으므로 개발 기간과 비용이 대폭 줄어들게 된다.

&nbsp;
## 특징

### 장점

1. **유지보수가 원활하다.** *절차 지향 프로그래밍*에서는 처리의 순서가 정해져있기 때문에, 프로그램에 문제가 발생해서 코드의 일부분을 수정할 때 프로그램 전체에 영향을 미칠 수 있다. 하지만 *객체 지향 프로그래밍*에서는 문제가 발생하는 객체만 수정하면 된다.

2. **코드의 재사용이 용이하다.** 객체는 하나의 역할을 수행할 수 있기 때문에, 프로그램의 여러 곳에서 객체를 사용할 수 있으며 다른 프로그램에서 쉽게 사용할 수 있다. 또한 상속을 통해 객체의 기능을 쉽게 확장할 수 있다. 다만 상속의 목적이 클래스의 재사용이라면 [몇 가지 문제점](./object-oriented-programming.md/#2-상속)이 발생할 수 있다.

3. 클래스 혹은 객체 단위로 모듈화시켜서, 여러 명이 **병렬적으로 개발할 수 있다.**

### 단점

1. 다양한 객체들의 상호 작용을 통해 프로그램이 구성되므로 **설계에 많은 시간과 노력이 필요하다.**
2. 여러 객체를 사용하다보니 *절차 지향 프로그래밍*에 비해서 **실행 속도가 느리다.**
3. 다중 상속이 지원되는 C++ 같은 경우에는 코딩 난이도가 상승한다.

&nbsp;
## 특성

### 1. 캡슐화

변수와 함수를 하나의 단위로 묶는 데이터의 번들링(Bundling)이다. 대게 프로그래밍 언어에서 캡슐화는 클래스를 통해 구현되고, 해당 클래스의 인스턴스 생성을 통해 클래스 안의 멤버 변수와 메서드에 접근한다.

#### 1-1. 정보 은닉

또한 프로그램의 세부 구현을 외부로 드러나지 않도록 특정 모듈의 내부로 감추는 **정보 은닉**이 함께한다. 이는 모듈 내에서의 응집도를 높이며, 외부로의 노출을 최소화하여 모둘 간의 결합도를 떨어뜨려 유연함과 유지보수성을 높인다. 정보 은닉은 프로그래밍 언어에서 지원하는 접근 제한자를 통해 해당 클래스, 변수 또는 메서드를 외부에서 접근할 수 있는 범위를 명시한다. 일반적으로 세 종류의 접근 제한이 사용된다.

1. `public` : 클래스의 외부에서 사용 가능하도록 노출시킨다.
2. `protected` : 다른 클래스에게는 노출되지 않지만, 상속받은 자식 클래스에게는 노출된다.
3. `private` : 클래스의 내부에서만 사용되며 외부로 노출되지 않는다.

&nbsp;
### 2. 상속

자식 클래스가 부모 클래스의 특성과 기능을 그대로 물려받는다. 물려받은 기능의 일부분을 변경해야할 경우 자식 클래스에서 해당 기능만을 수정하는 작업을 '오버라이딩(Overriding)'이라고 한다. 이러한 상속은 캡슐화를 유지하면서 클래스의 재사용이 용이하도록 해준다. 하지만 상속을 통해 클래스를 재사용하면 몇 가지 문제점이 발생한다. 따라서 *객체 지향 프로그래밍*에서 코드의 재사용을 목적인 상속 행위는 엄격히 금하며, '기능의 확장'을 목적으로 한다. 아래는 '클래스의 재사용'을 목적으로 상속을 한다면 발생할 수 있는 문제점이다.

1. 부모와 자식 클래스가 *IS-A* 관계가 아니면 문제가 발생한다. *객체 지향 프로그래밍*에서 *IS-A*란, '고양이는 동물이다'와 같이 포함되는 관계를 의미한다.
2. 부모 클래스의 변경이 불편해진다. 만약 부모 클래스가 변경되면 부모 클래스를 의존하는 자식 클래스가 영향을 받는다.
3. 불필요한 클래스가 증가한다. 오버라이딩을 통해 유사한 기능을 확장하면 필요 이상의 클래스를 만들 수 있다.

&nbsp;
### 3. 다형성

하나의 변수 또는 함수가 상황에 따라 다른 의미로 해석될 수 있음을 의미한다. 즉, 서로 다른 클래스의 객체가 동일한 동작 명령을 받았을 때, 각자의 특성에 맞게 동작하는 것이다. 이는 *객체 지향 프로그래밍*의 핵심이며 상속과 어울리는 개념이다. 다형성의 예를 들면, `Guitar`라는 클래스가 있고, 하나의 음을 연주하는 메서드가 있다. 그리고 `AcousticGuitar`와 `ClassicGuitar` 클래스가 `Guitar`를 상속받는다. 만약 도(C)를 연주하는 메서드를 실행한다면 `AcousticGuitar`와 `ClassicGuitar`는 각각 다른 소리로 연주를 한다.

&nbsp;
### 4. 추상화

객체들이 공통적으로 필요로 하는 속성이나 동작을 하나로 추출한다. 이는 추상적인 개념에 의존하여 설계해야 유연함을 갖출 수 있음을 목표로 한다. 예를 들어, 파일을 저장하는 클래스를 구현하고자 한다. 로컬이나 Cloud 등 다양한 위치에 저장할 수 있지만, 프로그램 기획 초기에는 파일을 로컬에 저장하는 시나리오만 존재했다고 가정한다. 하지만 저장한 파일을 여러 기기에서 사용하고 싶다는 고객의 요구사항이 있자, Cloud에 저장하는 기능을 추가하고자 한다. 

만약 파일을 저장하는 클래스를 추상화하지 않았다면, 저장하는 메서드가 아래와 같이 복잡해지고, 요구 사항이 추가될수록 심해진다.

```swift
func save(file: File, to location: SaveLocation) {
  switch location {
  case .local:
    let fileManager = FileManager.default
    let documentDirectoryPath = fileManager.urls(for: .documentDirectory, in: .userDomainMask)[0]
    let filePath = documentDirectoryPath.appendingPathComponent(file.name)
    if fileManager.fileExists(atPath: filePath) {
      try? fileManager.removeItem(atPath: filePath)
    }
    ...
        
  case .cloud:
    let storageRef = storage.reference()
    let fileRef = storageRef.child("files/\(file.name)")
    let fileData = file.data
    let uploadTask = fileRef.putData(fileData, metadata: nil) { metadata, error in 
      ...
    }
  }
}
```

다운로드를 수행하는 클래스를 추상화하기 위해 아래와 같은 인터페이스를 만든다.

```swift
protocol FileSystem {
  func save(file: File)
}
```

그리고 파일을 로컬에 저장하는 `LocalFileSystem`과 클라우드에 저장하는 `CloudFileSystem`은 `FileSystem` 프로토콜을 채택한다.

```swift
class LocalFileSystem: FileSystem {
  func save(file: File) {
    let fileManager = FileManager.default
    let documentDirectoryPath = fileManager.urls(for: .documentDirectory, in: .userDomainMask)[0]
    let filePath = documentDirectoryPath.appendingPathComponent(file.name)
    if fileManager.fileExists(atPath: filePath) {
      try? fileManager.removeItem(atPath: filePath)
    }
    ...
  }
}

class CloudFileSystem: FileSystem {
  func save(file: File) {
    let storageRef = storage.reference()
    let fileRef = storageRef.child("files/\(file.name)")
    let fileData = file.data
    let uploadTask = fileRef.putData(fileData, metadata: nil) { metadata, error in 
      ...
    }
  }
}
```

이제 우리는 `FileSystem`이라는 인터페이스를 통해 유연하게 로컬 혹은 클라우드에 파일을 저장할 수 있다. 만약 Dropbox에 파일을 저장하는 새로운 요구사항이 있더라도 쉽게 대처할 수 있다.