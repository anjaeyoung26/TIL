## Github Actions

Github Actions는 Github에서 제공하는 빌드, 테스트, 배포 파이프라인을 자동화할 수 있는 CI/CD 플랫폼이다. Github 레포지토리의 모든 Pull Request를 빌드, 테스트하고 병합된 코드를 프로덕션에 배포하는 워크플로우를 생성할 수 있다. Github Actions는 단순히 DevOps를 뛰어 넘어 레포지토리에서 다른 이벤트가 발생할 때 워크플로우를 실행시킬 수 있다. 예를 들어, 누군가 레포지토리에 이슈를 오픈하면 자동적으로 적절한 라벨을 추가하는 워크플로우를 실행시킬 수 있다. 워크플로우는 리눅스, 윈도우, macOS 등 다양한 운영체제에서 실행시킬 수 있고, 자체 데이터 센터 또는 클라우드 인프라에서 self-hosted runner를 호스팅할 수 있다.

&nbsp;
## 구성 요소

레포지토리에서 Pull Request를 생성하거나 이슈를 오픈하는 것과 같이 특정 이벤트가 발생할 때 트리거되는 워크플로우를 구성할 수 있다. 워크플로우는 순차적으로 혹은 병럴적으로 실행시킬 수 있는 하나 이상의 *job*를 포함한다. 각 *job*은 자체 가상 머신 *runner* 혹은 컨테이너 내부에서 실행되며, 워크플로우를 단순화할 수 있는 재사용 가능한 extension인 *action* 혹은 스크립트를 실행하는 하나 이상의 *step*을 가지고 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/232941313-795b09d9-0115-4470-8762-edfd121a0999.png">
</p>

### Workflows

워크플로우는 하나 이상의 *job*을 실행시킬 수 있는 구성 가능한 자동화 프로세스이다. 워크플로우는 레포지토리의 `.github/workflows` 디렉토리에 YAML 파일로 정의할 수 있고, 레포지토리의 이벤트에 의해 트리거될 때 실행되거나 수동으로 또는 정의된 스케쥴에 따라 트리거될 수 있다. 레포지토리는 각각 다른 작업을 수행할 수 있는 여러 개의 워크플로우를 가질 수 있다. 예를 들어, Pull Request를 빌드, 테스트하는 워크플로우와 릴리즈가 생성될 때마다 애플리케이션을 배포하는 또 다른 워크플로우, 그리고 누군가 새로운 이슈를 오픈할 때마다 레이블을 추가하는 또 다른 워크플로우이다.

- [Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) : 워크플로우 내부에서 다른 워크플로우를 참조할 수 있다.
- [Using workflows](https://docs.github.com/en/actions/using-workflows) : 워크플로우에 대해 자세히 알아볼 수 있다.

### Events

이벤트는 워크플로우 실행을 트리거하는 레포지토리의 특정 activity이다. 예를 들어, activity는 누군가 Pull Request를 생성하거나 이슈를 오픈하거나 혹은 커밋을 푸시할 때 시작될 수 있다.

- [posting to a REST API](https://docs.github.com/en/rest/repos/repos?apiVersion=2022-11-28#create-a-repository-dispatch-event) : 직접 혹은 스케쥴에 따라 실행되도록 워크플로우를 트리거할 수도 있다.
- [Events that trigger workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows) : 워크플로우를 트리거하는 데 사용할 수 있는 이벤트 목록이 나열돼있다.

### Jobs

*Job*은 워크플로우의 동일한 *runner*에서 실행되는 *step*의 모음이다. 각 *step*은 실행할 shell script 혹은 실행할 *action*이다. *Step*은 순서대로 실행되며 서로 의존적이다. 각 *step*은 동일한 *runner*에서 실행되므로 한 *step*에서 다른 *step*으로 데이터를 공유할 수 있다. 예를 들어, 애플리케이션을 빌드하는 *step* 뒤에 빌드된 애플리케이션을 테스트하는 *step*을 설정할 수 있다. *Job*은 기본적으로 서로 의존성이 없으며 병렬로 실행되지만 다른 *job*과 의존성을 구성할 수 있다. *Job*이 다른 *job*에 종속되면 의존하는 *job*이 완료될 때 까지 기다렸다가 실행할 수 있다. 예를 들어, 서로 다른 아키텍처에 대한 빌드 작업이 모두 완료되고 실행되는 패키징 작업이 있을 수 있다.

- [Using jobs](https://docs.github.com/en/actions/using-jobs) : *Job*에 대해 자세한 내용을 확인할 수 있다.

### Runners

*Runner*는 이벤트가 트리거될 때 워크플로우를 실행하는 서버이다. 각 *runner*는 한 번에 하나의 *job*을 실행할 수 있다. Github는 리눅스, 윈도우 및 macOS 운영 체제와 호환되는 *runner*를 제공하여 워크플로우를 실행한다. 각 워크플로우의 실행은 새로 프로비저닝된 가상 머신에서 실행된다.

- [Using larger runners](https://docs.github.com/en/actions/using-github-hosted-runners/using-larger-runners) : Github는 더 큰 구성에서 사용할 수 있는 더 큰 *runner*를 제공한다.
- [Hosting own runners](https://docs.github.com/en/actions/hosting-your-own-runners) : 다른 운영 체제가 필요하거나 특정 하드웨어 구성이 필요한 경우 자체 *runner*를 호스트할 수 있다.