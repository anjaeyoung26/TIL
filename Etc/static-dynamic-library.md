# 링킹

라이브러리는 링킹하는 방법에 따라 정적(Static) 라이브러리와 동적(Dynamic) 라이브러리로 나뉜다. 우선 링킹이 어떤 것인지 알아보자. 

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221513233-6e6052e6-2768-41e4-9782-2d370f842739.png">
</p>

위 그림은 C 언어의 빌드 과정이다. 전처리, 컴파일, 어셈블리 과정을 거쳐서 목적 파일이 생성된다. 해당 목적 파일을 실행하기 위해 실행 파일로 만드는 과정을 **링킹**이라고 한다. 또한 프로그램 내에서 링킹을 수행하는 **링커**가 존재한다. 링킹은 여러 개의 목적 파일과 라이브러리 파일을 합치는데, 라이브러리 파일을 합치는 방법에 따라 정적 링킹과 동적 링킹으로 나뉜다.

&nbsp;
## 정적 링킹

소스 코드를 컴파일할 때 라이브러리를 실행 파일에 복사하는 방식으로, 정적 링킹을 통해 연결된 라이브러리를 정적 라이브러리라고 한다. 

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220241546-8f2f4608-d2c8-4eaa-9561-0129dc33d583.png" height="300">
</p>

### 정적 라이브러리의 장단점

실행 파일이 라이브러리를 가지고 있으므로 라이브러리와 함께 배포하지 않아도 되고, 런타임 시 외부 참조가 없어서 **속도가 빠르다.** 하지만 실행 파일의 크기가 커지고, 라이브러리에서 수정할 부분이 있으면 파일 전체를 다시 컴파일하고 재배포해야 한다. 또한 동일한 라이브러리를 가진 여러 프로그램을 실행하면 코드가 중복되서 메모리가 낭비된다.

&nbsp;
## 동적 링킹

라이브러리의 주소를 가지고 있다가 런타임에 해당 주소에서 필요한 내용을 가져오는 방식으로, 동적 링킹을 통해 연결된 라이브러리를 동적 라이브러리라고 한다. 

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220241542-f90f7138-bc5a-4a92-bc1e-fa94e6f3b964.png" height="300">
</p>

### 동적 라이브러리의 장단점

동적 라이브러리는 정적 라이브러리에 비하여 실행 파일의 크기가 작고, 여러 프로그램이 동적 라이브러리를 공유할 수 있어서 메모리를 효율적으로 사용할 수 있다. 또한 라이브러리에서 수정할 부분이 있어도 실행 파일을 다시 컴파일하지 않고, 수정한 라이브러리만 다시 컴파일하여 재배포할 수 있다. 하지만 정상적으로 링크되어 있지 않거나 잘못된 버전을 사용하는 등 문제가 발생할 수 있다. 따라서 **외부 의존성이 생기고 이식성이 낮다.** 그리고 런타임에 라이브러리가 저장된 주소로 이동해야 하므로 약간의 성능 감소가 있다.