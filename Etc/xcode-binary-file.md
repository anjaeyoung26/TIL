# Xcode 바이너리 파일

Xcode의 실행 파일은 모두 바이너리화 돼있으며 bin 폴더에서 찾을 수 있다.

```
open /bin
```

실행 파일이 어떻게 구성됐는지 알아보기 위해 아래의 명령어를 입력한다. 터미널에서 자주 사용하는 ls 파일에 대한 바이너리 정보를 확인하자.

```
file /bin/ls
```

명령어를 입력하면 바이너리 정보가 출력된다. x86_64, arm64 등 익숙한 단어들이 눈에 띈다.

```
/bin/ls: Mach-O universal binary with 2 architectures: [x86_64:Mach-O 64-bit executable x86_64
- Mach-O 64-bit executable x86_64] [arm64e:Mach-O 64-bit executable arm64e
- Mach-O 64-bit executable arm64e]
/bin/ls (for architecture x86_64):	Mach-O 64-bit executable x86_64
/bin/ls (for architecture arm64e):	Mach-O 64-bit executable arm64e
```

1. Mach-O : 실행 파일의 포멧을 의미한다. [Mach 기반의 커널](https://namu.wiki/w/Mach(%EC%BB%A4%EB%84%90))에서 사용하는 실행 파일, 목적 파일, 라이브러리 파일의 포멧을 의미한다. executable은 앱을 실행하기 위한 메인 바이너리를 나타낸다.

2. Universal binary : 두 가지 이상의 아키텍처에서 지원하는 실행 파일을 의미한다. 흔히 아이패드와 아이폰에서 모두 지원하는 앱이라는 뜻에서 Universal app 라고도 한다.

3. x86_64 : x86은 인텔이 개발한 32비트 아키텍처이며, x64는 AMD가 개발하고 인텔이 채택한 64비트 아키텍처이다. x86_64는 두 아키텍처가 모두 호환된다는 것을 의미한다.

4. arm64 : arm은 [ARM Holdings](https://www.arm.com/)에서 개발한 CPU 아키텍처이며, arm64는 64비트 칩이 탑재된 arm이다. 위 바이너리 정보에서 arm64는 정확히 arm64 기반으로 애플에서 자체 설계한 Apple Silicon 칩을 의미한다.

&nbsp;
### 실제 라이브러리의 바이너리 정보를 확인해보자

Kingfisher 라이브러리를 CocoaPods로 설치해서 사용한다고 가정한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220318546-8fca8f4d-7d6e-4282-8c99-232868809732.png" width="200" height="150"> <img src="https://user-images.githubusercontent.com/61190690/220298350-756877e4-bc39-4126-a798-e227fc60461d.png", width="200" height="150">
</p>

라이브러리가 설치된 경로를 복사해서 터미널에 아래와 같이 입력한다. 경로 뒤에 라이브러리 이름을 붙여야한다.

```
file /Users/anjaeyeong/Library/Developer/Xcode/DerivedData/Splashing-dmgacwpbhmjuwdecoziankacofiv/Build/Products/Debug-iphonesimulator/Kingfisher/Kingfisher.framework/Kingfisher
```

그러면 Kingfisher 라이브러리의 실행 파일에 대한 바이너리 정보가 출력된다.

```
/Users/anjaeyeong/Library/Developer/Xcode/DerivedData/Splashing-dmgacwpbhmjuwdecoziankacofiv/Build/Products/Debug-iphonesimulator/Kingfisher/Kingfisher.framework/Kingfisher: 
Mach-O 64-bit dynamically linked shared library x86_64
```

출력된 내용으로 동적 라이브러리이며 x86_64 아키텍처를 지원하는 것을 알 수 있다. 이번에는 시뮬레이터에서 실제 디바이스로 빌드해보자.

```
/Users/anjaeyeong/Library/Developer/Xcode/DerivedData/Splashing-dmgacwpbhmjuwdecoziankacofiv/Build/Products/Debug-iphoneos/Kingfisher/Kingfisher.framework/Kingfisher: 
Mach-O 64-bit dynamically linked shared library arm64
```

빌드하는 디바이스에 따라 라이브러리의 경로와 바이너리 정보가 변경된 것을 알 수 있다.

