## Github Actions 워크플로우

Github Actions는 YAML 문법을 사용해서 워크플로우를 정의한다. 각 워크플로우는 레포지토리의 `.github/workflows`라는 디렉토리에 별도의 YAML 파일로 저장된다. 본 글에서는 레포지토리에 코드가 푸시될 때마다 특정 명령을 자동으로 트리거하는 워크플로우를 생성한다. 이 워크플로우에서 Github Actions는 푸시된 코드를 확인하고, bats 테스트 프레임워크를 설치해서 bats 버전을 출력하는 기본 명령인 `bats -v`를 실행한다.

&nbsp;
## 워크플로우 생성

레포지토리에 `./github/workflows` 디렉토리를 생성하고, 아래와 같은 YAML 파일을 추가한다.

```yaml
name: learn-github-actions
run-name: ${{ github.actor }} is learning Github Actions
on: [push]
jobs:
  check-bats-version:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '14'
      - run: npm install -g bats
      - run: bats -v
```

YAML 파일을 커밋하고 푸시한다. 이제 레포지토리에 커밋이 푸시되면 워크플로우가 명령을 실행한다. 워크플로우의 동작은 Github 레포지토리 상단 메뉴의 [Actions]를 클릭해서 좌측 사이드바에 해당 워크플로우를 클릭해서 확인할 수 있다. 자세한 내용은 [Viewing the activity for a workflow run](https://docs.github.com/ko/actions/learn-github-actions/understanding-github-actions#viewing-the-activity-for-a-workflow-run)에서 확인할 수 있다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/233305559-cae8bf95-42bc-4ada-a85a-87e72adf5ad7.png">
</p>

&nbsp;
## 워크플로우 YAML 문법의 이해

```yaml
name: learn-github-actions
```

*Optional* - 워크플로우의 이름이다. Github 레포지토리의 Actions 탭에서 표시되는 이름이며, 만약 이름을 설정하지 않는 경우 "./github/workflows/{파일명}"으로 표시된다.

```yaml
run-name: ${{ github.actor }} is learning Github Actions
```

*Optional* - 레포지토리의 Actions 탭에 있는 워크플로우의 실행 목록에 표시되는 이름이다. `github.actor` 컨텍스트는 워크플로우의 실행을 트리거한 사용자 이름을 표시한다. 컨텍스트에 대한 자세한 내용은 [Workflow syntax run-name](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#run-name)에서 확인할 수 있다.

```yaml
on: [push]
```

워크플로우의 트리거를 명시한다. 위 코드는 누군가 레포지토리에 커밋을 푸시하거나 Pull Request를 병합할 때마다 워크플로우 실행이 트리거된다. 특정 브랜치, 태크, 경로에만 실행되는 구문을 사용할 수 있다. 예를 들어, `main` 브랜치에서 커밋이 푸시될 때만 트리거하려면 아래와 같이 입력한다.

```yaml
on:
  push:
    branches:
      - main
```

자세한 내용은 [Workflow syntax on](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#on:~:text=github.actor%20%7D%7D-,on,-To%20automatically%20trigger)에서 확인할 수 있다.

```yaml
jobs:
  check-bats-version:
```

워크플로우에서 실행되는 모든 *job*의 그룹이다. `learn-github-actions` 워크플로우는 `check-bats-version`이라는 하나의 *job*이 포함되어 있다.

```yaml
  runs-on: ubuntu-latest
```

최신 버전의 Ubuntu Linux *runner*에서 실행되도록 *job*을 구성한다. 이는 *job*이 Github에서 호스팅하는 새로운 가상 머신에서 실행됨을 의미한다. `macos-latest`와 같이 다른 운영 체제나 `macos-12`와 같이 특정 버전의 운영 체제에서 실행되도록 구성할 수 있다. 자세한 내용은 [Choosing Github-hosted runners](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idruns-on)에서 확인할 수 있다.

```yaml
  steps:
```

`check-bats-version` *job*에서 실행되는 모든 *step*의 그룹이다. 각 *step*은 별도의 *action*이나 shell script 이다.

```yaml
    - uses: actions/checkout@v3
```

`uses` 키워드는 해당 *step*이 `actions/checkout` *action*의 `v3`를 실행하도록 지정한다. `actions/checkout`은 레포지토리에 접근하는 *action*으로, 만약 현재 *job*에서 레포지토리에 있는 스크립트나 빌드 및 테스트 도구를 실행해야 한다면, 실행하기 전에 레포지토리에 체크아웃을 해야 한다.

```yaml
    - uses: actions/setup-node@v3
      with:
        node-version: '14'
```

이 *step*은 `actions/setup-node@v3` *action*을 사용해서 Node.js 14 버전을 설치한다. 이렇게 하면 node 및 npm 명령이 모두 `PATH`에 저장된다.

```yaml
    - run: npm install -g bats
```

`run` 키워드는 *runner*에게 명령을 실행하도록 작업을 지시한다. 여기서는 npm을 사용하여 bats 소프트웨어 테스트 패키지를 설치한다.