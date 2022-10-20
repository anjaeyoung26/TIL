# SwiftLint

## 개념

커뮤니티 혹은 팀에서 정한 스타일을 따르지 않는 코드를 식별하고 표시하는 것을 돕는 코드 컨벤션이다. 75개가 넘는 규칙이 있고 지속적인 Pull Request를 통해 수정•보안되고 있다. 코딩 스타일을 정해두면 여러 개발자가 작업을 할 때 일관성을 유지할 수 있고 새로운 개발자가 합류하면 예전에 작성된 코드를 읽을 때 도움이 된다. 기본적인 규칙은 Apple의 공식 가이드와 [raywenderlich의 스타일 가이드](https://github.com/raywenderlich/swift-style-guide)를 따른다.

&nbsp;
## 규칙 조정

기본적으로 적용되는 규칙과 사용자에 의해 적용되는 규칙이 있다. 이러한 규칙들을 세부적으로 적용하기 위해서는 `.swiftlint.yml` 파일을 프로젝트에 추가하여 명시해야 한다. `.swiftlint.yml` 파일을 생성하지 않으면 SwiftLint에서 지정한 기본적인 규칙이 적용된다.

`.swiftlint.yml`에 적용 여부를 설정하는 파라미터는 다섯 가지가 있다.
1. **disabled_rules** : 기본 활성화된 룰 중에 제외할 룰을 지정한다.
2. **opt_in_rules** : 기본적으로 적용되는 규칙이 아닌 룰을 활성화한다.
3. **whitelist_rules** : 지정한 룰만 활성화 되도록 지정한다. `disabled_rules`, `opt_in_rules` 와는 같이 사용할 수 없다.
4. **included** : lint 과정에서 포함할 파일 경로를 지정한다.
5. **excluded** : lint 과정에서 제외할 파일 경로를 지정한다. `included`보다 우선순위가 높다.

  ```
  disabled_rules:
    - colon
    - comma
    - control_statement

  opt_in_rules:
    - empty_count
    - missing_docs

  included:
    - Source

  excluded:
    - Carthage
    - Pods
    - Source/ExcludedFolder
    - Source/ExcludedFile.swift
  ```

[https://realm.github.io/SwiftLint/rule-directory.html](https://realm.github.io/SwiftLint/rule-directory.html) 에서 각 규칙에 대한 자세한 내용을 참고하고 Identifier를 `.swiftlint.yml`에 추가하여 적용한다.

&nbsp;
## 사용자 정의 규칙

`.swiftlint.yml`에 사용자 정의 규칙을 추가하여 적용할 수 있다.

```
custom_rules:
 my_rules:
 included: ".*.swift"
 ame: "name"
 regex: "([n, N])"
 match_kinds:
	- comment
	- identifier
 message: "message"
 serverity: error		
```

&nbsp;
## 적용

1. CocoaPods 혹은 Homebrew를 통해 설치한다.

    - CocoaPods : Podfile에서 SwiftLint를 추가하고 설치한다.
    
      ```ruby
      pod 'SwiftLint'
      ```

    - Homebrew : 터미널에서 아래의 명령어를 실행해서 설치한다.

      ```
      brew install swiftlint
      ```
    

2. Target > [Build Phase] > + > 'New Run Script Phase'

    - CocoaPods

      ```ruby
      ${PODS_ROOT}/SwiftLint/swiftlint
      ```

    - Homebrew

      ```sh
      export PATH="$PATH:/opt/homebrew/bin"
      if which swiftlint > /dev/null; then
        swiftlint
      else
        echo "warning: SwiftLint not installed, download from https://github.com/realm/SwiftLint"
      fi
      ```

3. 세부적인 규칙을 적용하고 싶은 경우 `.swiftlint.yml` 이름의 Empty 파일 추가 후 규칙 조정을 참고하여 적용한다. Empty 형식은 두 가지가 있으므로 주의한다.

    <img src="https://user-images.githubusercontent.com/61190690/167349151-b8276be1-b709-4c12-82b3-1ce76b518a9c.png" width="500">