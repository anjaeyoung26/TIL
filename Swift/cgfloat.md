## CGFloat

`CGFloat`은 Swift에서 부동 소수점 숫자를 나타낼 때 사용한다. 하지만 `CGFloat` 외에도 `Double`, `Float` 등 소수점을 표현할 수 있는 여러 가지 타입이 존재한다. 먼저 `Double`과 `Float`의 차이점은 [소수점 다루기](./%EC%86%8C%EC%88%98%EC%A0%90-%EB%8B%A4%EB%A3%A8%EA%B8%B0.md)에서 살펴볼 수 있듯이, '소수점 몇 번째 자리까지 나타낼 수 있는 정도'를 의미하는 정밀도가 다르다.

```swift
let randomFloat = Float.random(in: 0...10) // 2.959846
let randomDouble = Double.random(in: 0...10) // 5.137981249907981
```

그렇다면 `CGFloat`은 어떤 타입일까? 우선 [여기](https://github.com/apple/swift-corelibs-foundation/blob/ee856f110177289af602c4040a996507f7d1b3ce/Sources/Foundation/CGFloat.swift)에서 `CGFloat`의 구현부를 살펴보자.

```swift
public struct CGFloat {
#if arch(i386) || arch(arm) || arch(wasm32)
    public typealias NativeType = Float
#elseif arch(x86_64) || arch(arm64) || arch(s390x) || arch(powerpc64) || arch(powerpc64le)
    public typealias NativeType = Double
#else
    #error("This architecture isn't known. Add it to the 32-bit or 64-bit line.")
#endif
```

`CGFloat`은 빌드하는 아키텍처에 따른 `NativeType`의 값을 설정하고 있다.

- *i386*, *arm*, *wasm32*(32bit) : `Float`
- *x86_64*, *arm64*, *s390x*, *powerpc64*, *powerpc64le*(64bit) : `Double`

&nbsp;
### 왜 아키텍처에 따라 나뉠까?

결론부터 말하자면 성능 상의 이점을 얻기 위해서다. iPhone 5S 이전까지는 32비트 아키텍처만 지원했지만, iPhone 5S의 A7 칩부터 64비트 아키텍처를 지원하면서 Apple은 애플리케이션이 64비트를 지원하도록 변환하여 메모리 성능을 높힐 수 있게 가이드했다. 관련 가이드 문서는 [여기](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Cocoa64BitGuide/64BitChangesCocoa/64BitChangesCocoa.html#//apple_ref/doc/uid/TP40004247-CH4-SW9)에서 볼 수 있다. 

`CGFloat`은 앞에 붙은 CG에서 알 수 있듯이, `CoreGraphics` 프레임워크에서 사용된다. `CoreGraphics`는 모든 그래픽 작업을 하면서 수 많은 부동 소수점 계산을 수행하는데, 정밀도가 높을수록 그래픽 작업에 대해 더 넓은 범위와 정확도를 제공한다. 따라서 64bit 아키텍처는 그래픽 성능을 높히기 위하여 더 높은 정밀도를 사용한다. 하지만 오늘날 사용되는 대부분의 iPhone이 64비트 아키텍처만 지원하니 *"`CGFloat`은 32bit 에서는 `Float`으로, 64bit 에서는 `Double`로 사용된다."* 정도만 알고있으면 될 것 같다.