# XCFramework를 통한 모듈화

하나의 프로젝트를 XCFramework로 모듈화하고 다른 프로젝트에서 사용해보자. 전체적인 시나리오는 다음과 같다.

1. XCFramework의 목적에 맞게 프레임워크 프로젝트를 여러 디바이스 타겟으로 설정한다.
2. 각 타겟별로 아카이브해서 바이너리 파일(.xarchive)를 생성한다.
3. 각 바이너리 파일을 XCFramework로 묵는다.
4. 클라이언트 프로젝트에 XCFramework를 Embed 한다.

&nbsp;
## 프로젝트 생성

Framework 프로젝트를 생성한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220838556-16577c7a-4554-490c-ae8e-e471d17ec457.png" width="500">
</p>

Framework에 `public`, `internal`, `private` 접근 제한자를 가진 함수를 정의하자.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220838851-f41e357c-3425-4dfb-98a6-cdcc2d73fb7d.png" width="500">
</p>

&nbsp;
## 아카이브

Xcode Command Line으로 타겟별 바이너리 파일로 아카이브 하자. 터미널에 아래와 같이 입력해서 iOS 타겟부터 아카이브 한다.

```
xcodebuild archive \
-scheme SampleFramework \
-archivePath "./build/ios.xcarchive" \
-sdk iphoneos \
SKIP_INSTALL=NO \
BUILD_LIBRARY_FOR_DISTRIBUTION=YES
```

- scheme : 아카이브할 프로젝트의 스키마이다. `xcodebuild -list -project SampleFramework.xcodeproj`으로 타겟, 스키마를 확인할 수 있다.

- archivePath : 아카이브한 바이너리 파일의 위치이다. 루트 디렉토리에는 build 라는 서브 디렉토리가 없어도 경로를 입력하면 추가된다.

- sdk : 아카이브할 타겟이다. `xcodebuild -showsdks`으로 목록을 확인할 수 있다.

- [SKIP-INSTALL](https://developer.apple.com/library/archive/documentation/DeveloperTools/Reference/XcodeBuildSettingRef/1-Build_Setting_Reference/build_setting_ref.html#//apple_ref/doc/uid/TP40003931-CH3-SW36:~:text=Xcode.501/SharedPrecompiledHeaders-,SKIP_INSTALL,-Description%3A) : YES일 경우 설치하지 않고 빌드만 수행한다. NO일 경우 [&DSTROOT](https://developer.apple.com/library/archive/documentation/DeveloperTools/Reference/XcodeBuildSettingRef/1-Build_Setting_Reference/build_setting_ref.html#//apple_ref/doc/uid/TP40003931-CH3-SW22:~:text=DERIVED_FILES_DIR%2C%20DERIVED_SOURCES_DIR-,DSTROOT%20(Installation%20Build%20Products%20Location),-Description%3A) 경로에 설치한다.

- [BUILD_LIBRARY_FOR_DISTRIBUTION](https://xcodebuildsettings.com/#build_library_for_distribution:~:text=Build%20Libraries%20for%20Distribution) : YES일 경우 라이브러리의 진화 및 모듈 인터페이스를 생성할 수 있다. 

> `BUILD_LIBRARY_FOR_DISTRIBUTION`는 클라이언트와 프레임워크의 바이너리 호환성을 유지하면서 프레임워크의 기능을 변경, 개선하는 유연성을 위해 YES로 설정할 것을 권장한다. 해당 내용은 WWDC19의 [Binary Frameworks in Swift](https://developer.apple.com/videos/play/wwdc2019/416/) 세션에서 확인할 수 있는데, 정확하게 이해하지 못해서 `SKIP-INSTALL`, `BUILD_LIBRARY_FOR_DISTRIBUTION`과 관련된 Xcode Build Settings은 다시 학습할 예정이다.

아카이브가 성공했다면 `** ARCHIVE SUCCEEDED **`가 보인다. 그리고 `-archivePath`를 설정한대로 루트 디렉토리에 build 서브 디렉토리가 생겼다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220839834-8232676f-cfba-4d30-a276-fdf16e24ae4e.png" width="500">
</p>

`ios.xcarchive`라는 바이너리 파일이 생성된 것으로 보아 iOS 타겟이 성공적으로 아카이브 됐다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220839844-6b08f4a0-dce1-4893-959e-37f14e38c683.png" width="500">
</p>

이제 시뮬레이터 타겟도 마찬가지로 아카이브 해보자.

```
xcodebuild archive \
-scheme SampleFramework \
-archivePath "./build/ios_sim.xcarchive" \
-sdk iphonesimulator \
SKIP_INSTALL=NO \
BUILD_LIBRARY_FOR_DISTRIBUTION=YES
```

build 디렉토리에 시뮬레이터 타겟을 아카이브한 바이너리 파일(`ios_sim.xcarchive`)가 보인다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220840733-d803728b-615a-4d3b-941a-2c84251cf611.png" width="500">
</p>

&nbsp;
## XCFramework 생성

XCFramework를 생성하기 위해 터미널에 아래와 같이 입력한다.

```
xcodebuild -create-xcframework \
-framework "./build/ios.xcarchive/Products/Library/Frameworks/SampleFramework.framework" \
-framework "./build/ios_sim.xcarchive/Products/Library/Frameworks/SampleFramework.framework" \
-output "./build/SampleFramework.xcframework"
```
명령어를 살펴보면 iOS와 시뮬레이터 타겟을 아카이브 했던 바이너리 파일을 포함시켜 XCFramework를 생성한다. build 디렉토리를 확인하면 XCFramework가 생성된 것을 확인할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220850692-d59bc80d-fac0-4278-88da-d8d7e36f9197.png" width="500">
</p>

&nbsp;
## XCFramework Import

생성한 XCFramework를 클라이언트 프로젝트에서 사용해보자. 클라이언트 프로젝트에 XCFramework 파일을 드래그 앤 드롭으로 추가한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220852362-3f83661c-4bd0-450d-be35-c1a07d834a67.png" width="400">
</p>

Dynamic Framework 이므로 Embed & Sign으로 설정한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220852618-dc960bc8-035f-45da-8259-58690df82206.png" width="500">
</p>

클라이언트 프로젝트의 [Build Phase] - [Link Binary With Libraries]에서 프레임워크가 링킹된 것을 확인할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220853228-e3ec1fa7-8975-4f35-bcf5-262068b405e4.png" width="500">
</p>

클라이언트 프로젝트를 빌드하면 프레임워크를 Import 해서 기능을 사용할 수 있다. 프레임워크의 구현대로 `public` 함수만 사용할 수 있으며, `internal`과 `private` 함수는 사용할 수 없다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220854622-80644634-5753-4f0b-bc03-ff1991539fe2.png" width="400">
</p>

클라이언트 프로젝트에서 프레임워크에 대한 내용을 살펴보면 `internalFunction`과 `privateFunction`의 구현체는 숨겨져 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/220855478-5f135879-19e9-4b5d-9fbe-43245831d107.png" width="400">
</p>
