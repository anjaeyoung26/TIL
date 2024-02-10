## Double, Float

우선 부동소수점의 정밀도에 대해서 알아보자. 정밀도란 부동소수점으로 표현할 수 있는 소수의 유효 숫자 개수를 의미한다. 예를 들어, 32비트 부동소수점은 보통 6-9 자리의 정밀도를 가지며, 64비트 부동소수점은 15-17 자리의 정밀도를 가지므로, 정밀도가 높을수록 더 작은 차이를 표현할 수 있다. **`Double`과 `Float` 타입은 각각 32비트와 64비트 부동소수점을 표현**한다. 따라서 더 높은 정밀도가 요구되는 연산에는 `Double`을 사용하고, 메모리 사용량을 줄이거나 정밀도가 덜 중요한 연산에는 `Float`을 사용하는 것이 일반적이지만, Swift 가이드에서는 두 자료형 모두 사용할 수 있는 상황에서 **Double 자료형을 선호**한다고 되어있다.

```
Double has a precision of at least 15 decimal digits, whereas 
the precision of Float can be as little as 6 decimal digits. 

The appropriate floating-point type to use depends on the nature 
and range of values you need to work with in your code. 

In situations where either type would be appropriate, Double is preferred.
```
The Swift Programming Language [Floating-Point Numbers](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics/)

&nbsp;
## CGFloat

`CGFloat`은 어떤 타입일까? 우선 [여기](https://github.com/apple/swift-corelibs-foundation/blob/ee856f110177289af602c4040a996507f7d1b3ce/Sources/Foundation/CGFloat.swift)에서 `CGFloat`의 구현부를 살펴보자.

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

`CGFloat`은 성능의 이점을 얻기 위해서 빌드하는 아키텍처에 따른 `NativeType`의 값을 설정하고 있다. iPhone 5S 이전까지는 32비트 아키텍처만 지원했지만, iPhone 5S의 A7 칩부터 64비트 아키텍처를 지원하면서 Apple은 애플리케이션이 64비트를 지원하도록 변환하여 메모리 성능을 높힐 수 있게 가이드했다. 관련 가이드 문서는 [여기](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Cocoa64BitGuide/64BitChangesCocoa/64BitChangesCocoa.html#//apple_ref/doc/uid/TP40004247-CH4-SW9)에서 볼 수 있다. 

- *i386*, *arm*, *wasm32*(32bit) : `Float`
- *x86_64*, *arm64*, *s390x*, *powerpc64*, *powerpc64le*(64bit) : `Double`

`CGFloat`은 앞에 붙은 CG에서 알 수 있듯이, `CoreGraphics` 프레임워크에서 사용된다. `CoreGraphics`는 모든 그래픽 작업을 하면서 수 많은 부동 소수점 계산을 수행하는데, 정밀도가 높을수록 그래픽 작업에 대해 더 넓은 범위와 정확도를 제공한다. 따라서 64bit 아키텍처는 그래픽 성능을 높히기 위하여 더 높은 정밀도를 사용한다. 하지만 오늘날 사용되는 대부분의 iPhone이 64비트 아키텍처만 지원하니 *"`CGFloat`은 32bit 에서는 `Float`으로, 64bit 에서는 `Double`로 사용된다."* 정도만 알고있으면 될 것 같다.