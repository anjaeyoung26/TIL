## Interface Builder

Interface Builder는 현재 Mac OS의 조상인 NeXTSTEP OS에 처음으로 탑재되었던 툴이며, 사용자가 프로그램과 상호 작용하는 버튼, 메뉴, 이미지 및 텍스트와 같이 프로그램의 그래픽 인터페이스를 배치하는 부분이다. Interface Builder는 Xcode의 일부로 그래픽 도구를 사용하여 프로그램의 인터페이스를 레이아웃한 다음 Swift 코드와 쉽게 연결하여 작동하게 한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/170985785-b6643b50-e45d-46d8-9d7d-04935cefaa1e.png">
</p>

우리가 스토리보드나 .xib 파일을 편집할 때 사용하는 것이 Interface Builder이며, 여기에 내장되어 있는 시스템으로 편리하게 인터페이스를 디자인할 수 있다. Interface Builder의 기능에 대해 대략적으로 알려주는 [공식 문서](https://developer.apple.com/kr/xcode/interface-builder/)가 있으니 참고하자.

&nbsp;
## NIB

NIB는 "NeXTSTEP Interface Builder"에서 따온 약어이다. 이는 `UIView`의 레이아웃과 같은 비시각적 요소와 배경색과 같은 시각적 요소를 Object Graph로 만들어서 직렬화한 파일이다. `.nib` 파일은 바이너리 기반으로 컴퓨터가 읽기 쉽지만 사용자가 읽고 어렵다. 그리고 소스 컨트롤에 친숙하지 않아 SVN, Git과 같이 사용할 때 문제가 발생한다.

&nbsp;
## XIB

XIB는 "Xcode Interface Builder"에서 따온 약자이다. Xcode에서 Interface Builder로 만드는 모든 UI의 구성 요소는 XIB 파일의 형태로 만들어진다. XIB는 xml 기반으로 소스 컨트롤이 원활하고 사용자에게 친화적이다. XIB는 xml 기반이라는 것을 제외하고 NIB와 비슷한 개념으로 쓰인다. XIB 파일은 컴파일 시 바이너리 형태인 NIB 파일로 바뀌게 된다. 여기서 NIB 파일 내에는 Interface Builder에서 만든 모든 UI 관련 객체와 속성, 객체 간의 연결 정보가 포함된다. 그리고 앱을 실행시키면 NIB 파일이 로드되고 NIB 파일에 포함되어 있는 각각의 객체들이 인스턴스화 된다.

> Archiving : Interface Builder -> XIB   
> Unarchiving : XIB -> NIB