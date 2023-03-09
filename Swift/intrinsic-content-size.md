# intrinsicContentSize

`intrinsicContentSize`는 컨텐츠의 본질적인 사이즈로 몇몇 뷰는 자신의 컨텐츠에 따라 고유의 크기를 가진다. 예를들어, `UILabel`은 텍스트, 폰트, 마진 등 몇 가지 요소에 의해 `intrinsicContentSize`가 결정된다. 아래와 같이 라벨의 width, height 오토 레이아웃을 설정하지 않고 center x, y만 설정해도 텍스트의 내용에 따라 크기가 다르다.

```swift
NSLayoutConstraint.activate([
  self.label.centerXAnchor.constraint(equalTo: self.view.centerXAnchor),
  self.label.centerYAnchor.constraint(equalTo: self.view.centerYAnchor)
])
```

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/224099393-9a75da45-acc3-4f54-bc89-093401b6aa27.png" width="300" height="90"> <img src="https://user-images.githubusercontent.com/61190690/224099400-e2936377-e4ab-48de-8645-58ee262fb0ca.png" width="300" height="90">
</p>

만약 width, height의 오토 레이아웃이 설정돼 있다면 `intrinsicContentSize`의 값은 그대로지만 제약사항에 따라 라벨의 크기가 정해진다.

```swift
NSLayoutConstraint.activate([
  self.label.widthAnchor.constraint(equalToConstant: 50)
])

self.label.intrinsicContentSize.width // 328.3333333333333 
```

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/224099408-f8291ad0-aca8-461d-9d3f-55aea91a5310.png" width="300" height="90">
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

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/167232169-885844d7-4618-4854-8420-c250a03249cc.png">
</p>

`intrinsicContentSize`를 결정하는 요소에는 `contentHugging`, `compressionResistance`가 추가적으로 있다. 이는 [contentHugging, compressionResistance](./content-hugging%2C%20compression-resistance.md) 문서에서 확인하자.
