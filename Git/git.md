# Git

Git은 형상 관리 도구 혹은 버전 관리 시스템의 한 종류로, 소프트웨어를 개발하는 기업의 핵심 자산인 소스코드를 효과적으로 관리할 수 있게 해주는 공개 소프트웨어이다. 이전에는 SVN이라는 중앙 집중형 관리 시스템으로 소스코드를 관리했지만 로컬과 원격에 있는 저장소를 나눌 수 없는 단점이 있다. 로컬 PC에서 중앙 저장소로 커밋을 하는 순간 모든 사람에게 동기화된다. 반면에 Git은 로컬 저장소에서 작업한 내용을 원격 저장소에 합치는 방식으로 여러 명이 병렬적으로 개발이 가능하다. 따라서 각 개발자가 원격 저장소와 별개로 로컬 저장소를 독립적으로 관리할 수 있다.

## Workflow

일반적인 Git의 Workflow를 살펴보자:

<img src="https://user-images.githubusercontent.com/61190690/168989164-1fafbe59-ac0c-4672-9d84-b63ac2aecf29.png">

로컬 저장소에는 세 가지 영역이 있다. Git 명령어를 통해 수정된 파일의 상태를 변경시켜 특정한 영역으로 이동시킨다. 물리적으로 세 가지 영역이 모두 로컬 저장소에 존재하지만 개념적으로 단계를 나눈다. 각 영역이 어떤 곳인지 알아보자.

1. **Working Directory** : 실제로 작업하고 있는 프로젝트의 디렉토리이다.
2. **Staging Area** : 원격 저장소에 올릴 준비가 되어있는 파일들이 위치하는 영역이다.
3. **.git Directory** : 커밋되어 버전을 관리하는 파일들이 위치하는 영역이다.

&nbsp;
## File Status

<img src="https://user-images.githubusercontent.com/61190690/168988580-0337bc6b-b60d-437e-a9a3-66a79fba921f.png">

### Untracked

Working Directory에 존재하는 파일이지만 Git으로 버전 관리를 하지 않는 상태이다. 이미 버전 관리를 하고 있는 디렉토리 내에서 몇몇 파일을 제외하고 싶을 때, [.gitignore](./gitignore.md)에 Git이 버전 관리를 하지 않아도 되는 파일을 명시할 수 있다. mac-os 환경에서는 주로 .DS_Store와 같은 파일을 명시한다.

> **.DS_Store란?** Desktop Service Store의 약자로 Apple에서 정의한 파일 포멧이다. mac-os 시스템이 폴더에 접근할 때 자동으로 생성되며, 해당 폴더에 대한 메타데이터를 저장하는 파일이다. 각 개발자가 접근한 폴더에 대한 정보는 원격 저장소에 저장할 필요가 없으니 일반적으로 원격 저장소가 트래킹하지 않도록 구성한다.

&nbsp;

### Tracked

Working Directory에 존재하는 파일 중 Git으로 버전 관리를 하는 상태이다. `Tracked` 상태는 다시 `Unmodified`, `Modified`, `Staged` 상태로 나눌 수 있다. 

- **Unmodified:** 원격 저장소와 비교하여 수정된 사항이 없는 상태   
- **Modified:** 원격 저장소와 비교하여 수정돤 사항이 있는 상태   
- **Staged:** 원격 저장소에 저장할 준비가 되어있는 상태

Working Directory에 존재하는 `Modified` 상태의 파일을 Staging Area로 옮기면 `Staged` 상태가 된다. 그리고 원격 저장소에 올린 파일은 다시 `UnModified` 상태가 된다. Git을 처음 사용하면 `Staged` 상태에 의문을 가질 것이다. 왜 Working Directory에서 수정한 파일을 바로 원격 저장소로 올리지 않고 Staging Area를 거쳐가는지에 대한 의문이다. 아래의 포스팅에서 Staging Area의 필요성을 살펴보자.

> [Staging Area가 필요한 이유](https://blog.npcode.com/2012/10/23/git%EC%9D%98-staging-area%EB%8A%94-%EC%96%B4%EB%96%A4-%EC%A0%90%EC%9D%B4-%EC%9C%A0%EC%9A%A9%ED%95%9C%EA%B0%80/)
