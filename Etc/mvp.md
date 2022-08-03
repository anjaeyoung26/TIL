## MVP

[*MVC*](./mvc.md)에서 뷰와 모델은 강하게 연결되어 있어 의존성을 띄고 있고, 애플리케이션이 커질수록 높아지므로 유지보수를 어렵게 만든다. 이러한 *MVC*의 단점을 해결하기 위해서 *MVP*는 컨트롤러 대신 프레젠터를 사용한다. 그리고 *MVC*와 다르게 컨트롤러가 아닌 뷰가 사용자의 입력을 받고, 모델과 뷰는 프레젠터와 상호 작용한다. 따라서 뷰와 모델은 서로를 모르기 때문에 뷰와 모델의 의존성이 없어지게 된다. 쉽게 이해하기 위해 *MVC*와 *MVP*의 동작 과정을 비교해보자. 

&nbsp;
### *MVC*의 동작 과정

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/182555524-f562a71f-11b7-44b1-b610-df1871fc9143.png" width="450">
</p>

1. 컨트롤러로 사용자의 입력이 들어온다.
2. 컨트롤러는 모델에 업데이트 요청을 한다.
3. 모델은 데이터를 업데이트하고 컨트롤러에게 전달한다.
4. 컨트롤러는 뷰를 선택해서 데이터를 전달한다.
5. 뷰는 데이터를 화면애 나타낸다.

&nbsp;
### *MVP*의 동작 과정

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/182567329-126afb4f-b11c-4673-9662-5b7d8f8ebae5.png" width="450">
</p>

1. 뷰로 사용자의 입력이 들어온다.
2. 뷰는 프레젠터에게 데이터를 요청한다.
3. 프레젠터는 모델에 업데이트 요청을 한다.
4. 모델은 데이터를 업데이트하고 프레젠터에게 전달한다.
5. 프레젠터는 뷰가 요청한 데이터를 응답한다.
6. 뷰는 프레젠터가 응답한 데이터를 화면에 나타낸다.

뷰와 모델은 직접 연결되어 있지 않고 프레젠터를 통해서 대화를 한다. 따라서 뷰와 모델의 의존성을 해결할 수 있다. 하지만 프레젠터와 뷰는 1:1 관계를 갖기 때문에 의존성을 띈다. 또한 애플리케이션의 크기가 커지면서 [*Massive ViewController*](./mvc.md/#단점) 대신 *Massive Presenter*가 된다. 서로 의존성을 띄고 Massive 문제가 발생하는 구성 요소만 바뀌었을 뿐, 여전히 문제는 남아있다. 그럼에도 불구하고 *MVP*를 사용하는 이유는 무엇일까? 뷰가 프레젠터를 직접 호출하지 않고 인터페이스를 통해 대화한다. 이는 Mock으로 대체하여 단위 테스트를 원활하게 할 수 있음을 의미한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/183071836-fa043178-4947-4a89-bd45-c2d9b18a1742.png" width="450">
</p>

&nbsp;
### vs Cocoa MVC

아래의 *MVP* 그림을 보면 [*Cocoa MVC*](./mvc.md/#cocoa-mvc)와 유사하다. *Cocoa MVC*도 마찬가지로 뷰와 모델은 직접적으로 연결되어 있지 않고 컨트롤러를 통해 대화하기 때문이다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/182568530-b2167fd2-dddc-43c4-9fd9-7899a6f2dfac.png">
</p>

아래의 그림은 *MVC*와 관련된 애플의 개발자 문서에서 소개한 *MVC*의 구조를 나타낸다. 하지만 그림을 살펴보면 우리가 흔히 알고 있는 *MVP*의 구조와 동일하다. 그렇다면 왜 애플은 *"iOS 애플리케이션이 *MVC* 패턴과 가장 적합하고, 많은 Cocoa 기술과 아키텍처는 MVC를 기반으로 한다."* 라고 말하면서 *MVP*와 같은 구조를 사용할까? 이유는 *Cocoa MVC*를 정립할 당시에는 *MVP*가 *MVC*와 비교해서 상대적으로 덜 알려져 있던 개념이기 때문이다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/182570666-aab85a3b-5e3a-4502-b957-6f481c273492.png" width="600">
<sub>https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html</sub>
</p>