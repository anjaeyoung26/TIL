## Jenkins와 Github 레포지토리 연동

대부분의 공개 레포지토리는 Pull Request를 요청하면 작업한 코드가 테스트를 통과하고 리뷰어가 Approve를 해야 Merge 할 수 있는 Contribution 가이드가 있다. 이러한 자동화 작업은 레포지토리를 관리하는 개발자가 수동으로 확인해야 하는 수고를 덜어준다. 이를 Jenkins를 활용해서 구축해보려고 한다. Jenkins는 Github 레포지토리와 연동해서 설정해놓은 트리거가 감지되면 특정한 동작을 실행시킬 수 있다. 본 글에서는 Github 레포지토리에 Webhook을 추가해서 이벤트가 발생하면 Jenkins 파이프라인 스크립트를 실행하는 단계까지 구축한다. 전체적인 시나리오는 아래와 같다: 최종적으로 테스트 통과 여부에 따라 Pull Request의 Merge 버튼이 활성화되는 것이 목표이다. 시나리오는 Jenkins가 설치했다는 선행 조건이 있다. 설치 방법을 모른다면 [여기](./jenkins(1).md)를 참고하자.

1. [Github 액세스 토큰을 생성한다.](#github-액세스-토큰-생성)
2. [Github 레포지토리에 Webhook을 추가한다.](#github-webhook-추가)
3. Private 레포지토리인 경우 [Jenkins Credential을 생성한다.](#jenkins-credential-생성)
4. [Jenkins 파이프라인을 생성한다.](#jenkins-파이프라인-생성)
    - Github 레포지토리에 Pull Request가 생성된다.
    - Github Webhook을 통해 Jenkins에게 전달된다.
    - Jenkins 파이프라인에서 테스트 스크립트를 실행한다.

&nbsp;
## Github 액세스 토큰 생성

1. Github - [Settings] - [Developer Settings] - [Personal access tokens] - [Tokens (classic)] - [Generate new token]을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231744979-a7be0da4-ecc1-44ba-a18f-51541cd48cb9.png">
</p>

repo, admin:repo_hook을 체크하고 Generate token을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231745619-33dd03e4-4a5e-4944-9055-b73a694bf00d.png">
</p>

모자이크된 부분이 생성된 토큰 값이다. 페이지를 벗어나면 다시 확인할 수 없으니 메모를 해두자.

&nbsp;
## Github Webhook 추가

Github 레포지토리 - [Settings] - [Code and automation] - [Webhooks] - Add webhook을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231746173-a567dace-fb2e-4b1d-86f1-fc0a8f38562a.png">
</p>

- Payload URL : "젠킨스 서버 주소/github-webhook/"을 입력한다. 만약 젠킨스 서버 주소가 로컬 호스트라면 Github에서 이벤트를 제공할 수 있도록 로컬 개발 환경을 인터넷에 노출해야 한다. 이러한 작업은 ngrok을 사용한다. 자세한 과정은 Github Docs의 [웹후크 만들기](https://docs.github.com/ko/webhooks-and-events/webhooks/creating-webhooks)를 참고한다. 필자의 경우 "https://3ed0-61-254-189-113.ngrok-free.app"를 입력했다.
- Content Type : application/json 타입으로 설정한다.
- Just the Push Event 옵션을 설정한다.

&nbsp;
## Jenkins Credential 생성

Jenkins Dashboard - [Jenkins 관리] - [Manage Credentials]를 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231751937-bc763ac1-dccd-4ad4-b0e4-642aef1551ae.png">
</p>

(global)을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231750591-5fbb0409-8f59-45c2-b687-da868eae5080.png">
</p>

Add Credentials를 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231752091-5ab37f4b-dd0b-456d-a980-549b4f2571ef.png">
</p>

- Kind : Username with password를 선택한다.
- Username : Github 사용자 이름을 입력한다.
- Password : Github 액세스 토큰을 입력한다.
- ID : Credential의 이름을 입력한다.

모든 정보를 입력하고 OK를 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231752842-bf20d278-84f7-4554-981d-a5a26ff21268.png">
</p>

생성된 Credential을 확인할 수 있다.

&nbsp;
## Jenkins 파이프라인 생성

Jenkins Dashboard - [새로운 Item]을 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231753093-b4aa38a8-3b71-4f7b-a36e-ac1efd2a4def.png">
</p>

아이템의 이름을 입력하고 Pipeline을 선택한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231753335-126405fb-2094-4e87-9837-74ecbf45896e.png">
</p>

파이프라인의 정보를 입력하는 화면으로 넘어간다. 아래의 두 옵션을 설정한다.

- Github Project를 선택하고 Project url에는 Github 레포지토리의 주소를 입력한다.
- Build Triggers : Github hook trigger for GITScm polling을 선택한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231754678-9b6988a5-8a60-4ef4-b5d6-2da3f7963bc5.png">
</p>

파이프라인의 스크립트를 입력한다. 스크립트는 Github Webhook의 이벤트가 Jenkins로 전달되면 수행할 동작을 나타내는데, Checkout 단계에서 Github 레포지토리 소스를 얻기 위해 Github 액세스 토큰과 Github 레포지토리의 주소를 입력한다.

```
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'Github 액세스 토큰',
                    url: 'Github 레포지토리 주소'
            }
        }
    }
}
```

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231754895-355db4b1-fc4a-4f95-a538-5a04926fe2e1.png">
</p>

Github 레포지토리와 정상적으로 연결됐는지 확인하기 위해 Jenkins Dashboard - [지금 빌드]를 클릭한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231756156-042e6f02-adf9-4e3e-bfeb-9f9e0fe2effc.png">
</p>

정상적으로 빌드가 되면 Jenkins Dashboard 좌측 하단에 초록색 체크가 표시된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231756362-6bc2dc3e-10db-4839-8cd6-6689ca22e426.png">
</p>

&nbsp;
## 연동 확인

Github Webhook의 이벤트가 Jenkins로 전달되는지 확인해보자. Github 레포지토리에 커밋을 생성해서 푸시하면 Jenkins에 이벤트가 전달되서 자동으로 빌드된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/231968089-860a65eb-91da-4e5d-b704-b4d7d6743cc9.gif">
</p>