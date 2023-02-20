# intrinsicContentSize

`intrinsicContentSize`는 컨텐츠의 본질적인 사이즈로 몇몇 뷰는 자신의 컨텐츠에 따라 고유의 크기를 가진다. 예를들어, `UILabel`은 텍스트, 폰트, 마진 등 여러가지 요소에 의해 `intrinsicContentSize`가 결정된다.

```swift
NSLayoutConstraint.activate([
  self.label.centerXAnchor.constraint(equalTo: self.view.centerXAnchor),
  self.label.centerYAnchor.constraint(equalTo: self.view.centerYAnchor)
])
```

라벨의 width, height 오토 레이아웃을 설정하지 않아도 레이아웃이 자동으로 설정된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/219992045-f7e44e75-e5cd-48be-8985-b0e302f5039c.png" width="300"> <img src="https://user-images.githubusercontent.com/61190690/219992042-401dfb5d-c99f-4b6c-8b45-f3da996c9d2f.png" width="300">
</p>

만약 width, height의 오토 레이아웃이 설정돼 있다면 `intrinsicContentSize`의 값은 그대로지만 제약사항에 따라 라벨의 크기가 정해진다.

```swift
NSLayoutConstraint.activate([
  self.label.widthAnchor.constraint(equalToConstant: 50)
])

self.label.intrinsicContentSize.width // 328.3333333333333 
```

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/219993016-4177790e-0ced-428f-960e-7f15f4c30309.png" width="300">
</p>

&nbsp;
## invalidateIntrinsicContentSize

앞서 라벨의 텍스트가 변경되면 시스템이 `intrinsicContentSize`을 변경시킨다고 했다. 그렇다면 시스템은 라벨의 `intrinsicContentSize`를 다시 계산해야할 순간을 어떻게 알아차릴까? 바로 `invalidateIntrinsicContentSize()` 함수를 호출하는 것이다. 기본적으로 `UILabel` 클래스는 몇가지 요소가 변경될 때 `invalidateIntrinsicContentSize()`를 호출하는 매커니즘이 있다.

```swift
final class MyLabel: UILabel {
  override func invalidateIntrinsicContentSize() {
    // super.invalidateIntrinsicContentSize()
  }
}
```

위와 같이 `invalidateIntrinsicContentSize()`를 오버라이딩해서 `super.invalidateIntrinsicContentSize()`를 호출하지 않도록 하면 라벨의 텍스트에 따라 레이아웃이 변경되지 않는다.

&nbsp;
## contentHugging, compressionResistance

`intrinsicContentSize`를 결졍하는 요소에는 `contentHugging`, `compressionResistance`가 추가적으로 있다. 이는 [contentHugging, compressionResistance](./content-hugging%2C%20compression-resistance.md) 문서에서 확인하자.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/167232169-885844d7-4618-4854-8420-c250a03249cc.png">
</p>