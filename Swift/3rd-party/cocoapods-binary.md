# CocoaPods Binary

[CocoaPods](https://cocoapods.org/)을 사용하다 보면 프로젝트에서 Pod을 변경하지 않았는데도 라이브러리를 다시 컴파일하는 경우가 있다. 예를 들어 클린빌드 이후 프로젝트를 다시 빌드하면 CocoaPods의 라이브러리까지 빌드해야 되서 많은 시간이 소요된다. 작은 프로젝트나 가벼운 라이브러리를 사용하는 프로젝트는 문제가 없지만, 대규모 프로젝트에서는 다시 빌드하는데 오랜 시간이 걸린다. 최근 Xcode 자체적으로 빌드 속도를 개선하고 있고, M1 맥북을 사용하면서 빌드 시간이 많이 단축됐지만, 대규모 프로젝트는 여전히 골치 아프다.

이에 대한 해결책으로 CocoaPods 공식 홈페이지에서 [Pre-compiling dependencies](https://guides.cocoapods.org/plugins/pre-compiling-dependencies.html?utm_source=Postype&utm_medium=iframely) 가이드를 살펴볼 수 있다. Pre-compiling dependencies는 위와 같이 라이브러리를 컴파일할 필요가 없는 경우에도 다시 컴파일하는 문제를 해결하기 위해 CocoaPods Binary를 사용하라는 내용이다. CocoaPods Binary는 [cocoapods-binary](https://github.com/leavez/cocoapods-binary)에서 자세한 내용을 살펴볼 수 있다. CocoaPods Binary는 Podfile에 별도로 지정한 pod에 대해서 `pod install` 시점에 미리 컴파일을 수행하고, `.framework`와 같은 바이너리 파일을 만든다. 그리고 사용자의 프로젝트가 pod에 대해 소스 코드가 아닌 바이너리를 참조하도록 하여 아무리 클린 빌드를 한다고 해도 pod에 대한 소스 코드를 빌드할 필요가 없어서 빌드 속도를 줄일 수 있다.

> `cocoapods-binary`의 마지막 릴리즈가 2019년 4월이고, 프로젝트의 관리가 원활하지 않는 것 같다. 또한 간혈적으로 pod이 깨져서 다시 pre-build를 해야하는 등 여러가지 문제점이 발생한다. `cocoapods-binary`의 대안으로 Grab의 iOS 개발팀에서 만든 [cocoapods-binary-cache](https://github.com/grab/cocoapods-binary-cache)가 있다. CLI를 통해 명령어를 제공해서 터미널에서 동작시킬 수 있고, `validate` 등의 유용한 옵션을 제공하여 `cocoapods-binary`에 비해 많이 개선되었다.

&nbsp;
## 설치

```
gem install cocoapods-binary
```

&nbsp;
## 사용법

Podfile에 `cocoapods-binary` 플러그인을 추가하고, pre-build 하려는 pod에 대해 `:binary => true` 플래그를 사용한다.

```
plugin `cocoapods-binary`
use_frameworks!

target "MyApp" do
  pod "Library1", :binary => true
  pod "Library2"
end
```

만약 모든 pod에 대해 pre-build를 하려는 경우 `all_binary!`를 사용한다. 제외하고 싶은 pod에 대해서는 `:binary => false` 플래그를 사용한다.

```
plugin `cocoapods-binary`
use_frameworks!
all_binary!

target "MyApp" do
  pod "Library1"
  pod "Library2", :binary => false
end
```
