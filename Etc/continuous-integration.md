## 지속적 통합

**지속적 통합이란,** 자동화된 빌드 및 테스트가 수행된 후, 개발자가 코드 변경 사항을 중앙 레포지토리에 정기적으로 병합하는 DevOps 이다. 처음에는 지속적 통합의 설명을 보고 의문이 들었다. 일반적으로 개발 단계에서 결과물을 확인하기 위해 빌드 및 테스트를 하고, 코드 변경 사항을 중앙 레포지토리에 반영하기 위해 PR - 코드 리뷰 단계를 거칠 것이다. 그런데 지속적 통합은 어떤 목적으로 소프트웨어 릴리즈 프로세스에 추가될까? 팀 단위로 개발하는 프로젝트에서 지속적 통합이 필요한 상황은 아래와 같다.

지속적 통합을 도입하기 전에는 팀의 개발자가 장기간 격리된 상태로 작업하고, 작업이 완료된 후에야 변경 사항을 마스터 브랜치에 병합했다. 이로 인해 코드를 병합하는데 발생하는 충돌로 시간이 소모되고, 수정 없이 오랫동안 버그가 축적되는 결과가 나타났다. 이러한 요소 때문에 클라이언트에게 업데이트를 신속하게 제공하기가 더 어려워진다. **지속적 통합의 핵심 목표**는 버그를 신속하게 찾아 해결하고, 소프트웨어 품질을 개선하고, 새로운 소프트웨어 업데이트를 검증 및 릴리즈하는 데 걸리는 시간을 단축하는 것이다.

### 지속적 통합의 이점

1. **생산성 향상** : 클라이언트에게 제공하는 서비스의 오류 및 버그 수를 줄이는 데 도움이 되는 기능을 활용함으로써, 팀의 생산성을 높일 수 있다. 또한 수작업의 상당 부분을 자동화하면 개발자가 더 높은 수준의 테스트 활동에 집중할 수 있어서 소프트웨어의 품질을 높힌다.
2. **신뢰성 향상** : 테스트를 좀 더 빈번하게 수행함으로써, 팀에서는 이후에 더 큰 문제로 발전하기 전에 버그를 조기에 발견하고 해결할 수 있다. 자동화된 소프트웨어의 테스트를 통해 다음 개발 단계에서 버그 수정이 적어지게 된다. 이는 최종 제품에서 버그와 오류를 줄여서 사용자의 만족도가 높아진다.
3. **짧은 배포 주기** : 지속적 통합을 사용하면 팀이 좀 더 빠르고 빈번하게 클라이언트에게 업데이트를 제공할 수 있다. 클라이언트가 사용하는 소프트웨어에서 문제가 발생하면 이를 해결하기 위한 개발 주기가 훨씬 빨라진다. 짧은 시간 안에 작은 변경 사항을 수정하고 테스트한 뒤에 새로운 버전을 출시할 수 있다.

### 지속적 통합의 동작 과정

지속적 통합에서는 개발자가 Git과 같은 버전 관리 시스템을 사용하여 중앙 레포지토리에 빈번하게 커밋하게 된다. 각 커밋에 앞서, 개발자는 코드 변경 사항을 중앙 레포지토리에 통합하기 전에 추가적으로 검증하는 단계로써 로컬 유닛 테스트를 수행할 수 있다. 지속적 통합 서비스는 새로운 코드 변화에 대한 유닛 테스트를 자동으로 구축하고 실행하여 모든 오류를 즉시 표면화한다.

<img src="https://user-images.githubusercontent.com/61190690/231404114-b1874982-6e9f-48de-899c-5f8f61acc240.png">

위 이미지는 CI/CD를 적용한 소프트웨어의 릴리즈 프로세스를 나타낸다. 릴리즈 프로세스 단계에서 지속적 통합은 빌드 및 유닛 테스트 단계를 지칭한다. 수정 버전이 커밋될 때마다 자동화된 빌드 및 테스트가 수행된다.

### 참고

- https://aws.amazon.com/ko/devops/continuous-integration
- https://www.tibco.com/ko/reference-center/what-is-continuous-integration
- https://www.jetbrains.com/ko-kr/teamcity/ci-cd-guide/continuous-integration/