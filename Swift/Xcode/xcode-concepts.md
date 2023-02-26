# Xcode Concepts

[Xcode Concepts](https://developer.apple.com/library/archive/featuredarticles/XcodeConcepts/Concept-Targets.html)는 프로젝트, 타겟, 스키마, 워크스페이스 등 다양한 개념을 소개하고 있다.

&nbsp;
## Xcode 프로젝트

Xcode 프로젝트는 하나 이상의 소프트웨어 제품을 빌드하는 데 필요한 모든 파일, 리소스 및 정보의 저장소이다. 또한 제품을 빌드하는 방법을 지정하는 하나 이상의 타겟이 포함되어 있다. 프로젝트는 모든 타겟에 대한 기본 Build Settings를 정의한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221257610-ed6d6f0e-dec2-490a-95f0-49a25c953483.png">
</p>

각 타겟은 프로젝트의 Build Settings를 오버라이딩할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221257661-a396508d-1207-495e-ab64-54be945eef89.png">
</p>

프로젝트는 독립적인 실행 파일이거나 워크스페이스에 포함될 수 있다. 또한 Xcode 스키마를 통해 지정된 시간에 활성화되는 타겟, 빌드 구성, 실행 가능한 구성을 지정할 수 있다.

&nbsp;
## Xcode 타겟

Xcode 타겟은 빌드할 제품을 지정하고 프로젝트나 워크스페이스의 파일에서 제품을 빌드하기 위한 지침을 포함한다. 해당 지침에는 Build Settings, Build Phase가 있으며, Xcode 프로젝트 편집기에서 편집할 수 있다. 

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221403075-9b09d64c-d9e7-4298-9119-0e3927780d0b.png">
</p>

앞서 언급했듯이 각 타겟은 프로젝트의 Build Settings를 오버라이딩할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221257661-a396508d-1207-495e-ab64-54be945eef89.png">
</p>

활성화할 타겟은 한 번에 하나만 있을 수 있으며, Xcode 스키마가 활성 대상을 지정한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221403329-762d2837-3b0c-4659-beed-a4812700c7e5.png">
</p>

타겟은 하나의 제품을 정의하며, 프로젝트는 하나 이상의 타겟을 포함할 수 있다. 일반적으로 개발, 릴리즈, 인하우스 등 다양한 환경에서 테스트하기 위해 타겟을 분리한다. 이와 관련된 글은 [여기](./xcode-manage-target.md)에 정리할 예정이다.

&nbsp;
## Xcode 스키마

Xcode 스키마는 빌드할 타겟과 빌드할 때 사용할 빌드 구성 및 실행할 테스트 모음이다. 여러 개의 스키마를 가질 수 있지만, 한 번에 하나만 활성화할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221401797-5a14a74f-0a10-40d5-88f5-821ba55244ab.png" width="400">
</p>

활성화할 스키마를 선택할 때 제품이 빌드되는 하드웨어의 아키텍처도 선택한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221402300-3c10a095-628a-4c9f-a91e-ddf1f96a859e.png">
</p>

&nbsp;
## Xcode 워크스페이스

Xcode 3 이전 버전에서는 프로젝트가 다른 프로젝트를 참조할 수 있지만 상호 연관된 프로젝트를 작업하는 것이 복잡해서 대부분의 워크플로우는 단일 프로젝트로 제한됐다. Xcode 4 버전에서는 하나 이상의 프로젝트와 다른 파일을 저장하는 워크스페이스를 만들면서 Xcode 워크플로우의 범위를 확장했다. 워크스페이스는 포함된 프로젝트의 모든 파일에 대한 액새스를 제공한다.(CocoaPods으로 설치한 라이브러리의 소스 코드를 인덱싱할 수 있는 것처럼)

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221408014-80000a22-98fe-4596-b571-6757dda7e04a.png">
</p>

위 그림은 Splashing, Pods 두 개의 프로젝트가 포함된 워크스페이스이다. 워크스페이스에 포함된 프로젝트들은 워크스페이스 빌드 디렉토리라는 동일한 디렉토리에 빌드된다. 따라서 두 개 이상의 프로젝트에서 동일한 라이브러리를 사용하는 경우 각 프로젝트 폴더에 개별적으로 복사할 필요가 없다. 워크스페이스의 워크플로우를 확인해보자. 먼저 워크스페이스를 생성한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221409193-fabc1579-c471-4969-810c-5f2b6a7fabea.png" width="400">
</p>

처음 워크스페이스를 생성하면 Project Navigator가 비어있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221409308-8cf5fc87-c14f-4686-8543-98ece8afce1d.png" height="500">
</p>

이제 워크스페이스에 포함시킬 Main, Sub 프로젝트를 생성한다.

<p align="center"> <img src="https://user-images.githubusercontent.com/61190690/221409440-8bc1d423-e1f1-4c30-9110-ac09b30c2580.png" width="350" height="200">
<img src="https://user-images.githubusercontent.com/61190690/221409430-058eb52a-c58d-409a-a669-ebd319201623.png" width="350" height="200">
</p>

Sub 프로젝트에는 `public` 접근 제한자를 가진 `publicFunction`를 정의했다. 이제 워크스페이스에 Main, Sub 프로젝트를 드래그 앤 드롭으로 포함시킨다. 중요한 점은 두 프로젝트의 depth가 동일해야 한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221409672-a3a3ba0f-0448-43e8-8768-00f27ddfc2db.png" width="400">
</p>

이제 워크스페이스에서 Main, Sub 프로젝트를 한 번씩 빌드한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221409722-80b6cca2-bd78-4e38-ad51-6b30d2a60aa1.png" width="400">
</p>

Main 프로젝트에서 Sub 프로젝트를 Import 해보자. 그러면 Sub 프로젝트에서 정의했던 `publicFunction`을 사용할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/221409780-a89545ba-6b74-4db4-93ef-44f2ae81d0e1.png" width="400">
</p>


