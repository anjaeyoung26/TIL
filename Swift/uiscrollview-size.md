# 스크롤 뷰의 사이즈를 정하는 방법

## 1. `contentSize`의 값을 설정

```swift
self.scrollView.contentSize = .init(width: 200, height: 200)
```

위 방법은 스크롤 뷰가 `contentSize` 만큼만 스크롤돼서 서브 뷰의 일부가 안 보일 수 있다. 또한 서브 뷰를 추가할 때마다 값을 매번 변경해야 한다.

&nbsp;
## 2. 내부 컨텐츠의 사이즈에 따라

먼저 스크롤 뷰는 서브 뷰의 [`intrinsicContentSize`](./contentHugging%2C%20compressionResistance.md)로 크기를 가늠한다. `intrinsicContentSize`는 뷰의 컨텐츠에 대한 본질적인 사이즈를 나타내는 프로퍼티이며, 뷰의 컨텐츠에 따라 값이 정해진다. 예를들어, 라벨의 `intrinsicContentSize`는 폰트의 크기와 텍스트의 길이 등을 고려해서 사이즈가 정해진다. 따라서 화면에 라벨을 추가하고 사이즈를 정하지 않아도 적절하게 표시되는 것이다. 만약 Autolayout으로 사이즈를 명시하면 Constraint에 따라 사이즈가 정해진다.

```swift
NSLayoutConstraint.activate([
  self.mySubview.heightAnchor.contsraint(equalToConstant: 200),
  self.mySubview.widthAnchor.constraint(equalToConstant: 200)
])
```

### 동적인 사이즈의 컨텐츠

하지만 위 방법은 동적인 사이즈를 갖는 서브 뷰에는 적절하지 않다. 만약 동적인 사이즈의 셀을 표시하는 컬렉션 뷰는 새로고침 되거나 셀이 추가되면 `contentSize`가 변경되는데, 이를 스크롤 뷰에게 알려야 컬렉션 뷰가 적절하게 표시된다.

```swift
class MyCollectionView: UICollectionView {
  override var intrinsicContentSize: CGSize {
    return self.contentSize // 1
  }

  override var contentSize: CGSize 
    didSet {
      self.invalidateIntrinsicContentSize() // 2
    }
  }
}
```

1: 스크롤 뷰가 컬렉션 뷰의 컨텐츠에 따라 사이즈를 가늠할 수 있도록 `contentSize`를 반환한다.

2: 컬렉션 뷰의 셀이 추가, 삭제되서 컨텐츠 사이즈가 변경되면 스크롤 뷰가 알 수 있도록 `invalidateIntrinsicContentSize`를 호출한다. `invalidateIntrinsicContentSize`는 현재 뷰가 가지고 있는 `intrinsicContentSize`를 갱신하기 위해 다시 계산하도록 시킨다.
