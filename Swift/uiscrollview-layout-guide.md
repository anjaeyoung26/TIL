## UIScrollView.contentLayoutGuide

`contentLayoutGuide`는 iOS 11 버전부터 추가된 프로퍼티로, 스크롤 뷰의 컨텐츠를 스크롤 할 수 있는 영역을 의미한다.

```swift
self.scrollView.frame.size = CGSize(width: 450, height: 450)
self.scrollView.contentSize = CGSize(width: 2000, height: 2000)
```

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/219945152-56ced93e-c651-4222-a170-376e1aab5be6.png">
</p>

&nbsp;
## UIScrollView.frameLayoutGuide

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/219948053-565ff2cf-e8bb-4167-a84c-b5a8d28d978f.png">
</p>

`frameLayoutGuide`도 마찬가지로 iOS 11 버전부터 추가된 프로퍼티로, 스크롤 뷰가 화면에 표시되는 영역을 의미한다. 주로 `frameLayoutGuide`는 두 가지 용도로 사용된다. 스크롤 뷰의 컨텐츠를 담기 위한 컨테이너 뷰를 사용한다고 가정하자.

1. 세로 스크롤이 필요할 때 컨테이너 뷰의 width를 `frameLayoutGuide`와 같게 한다.

    ```swift
    self.containerView.widthAnchor.constraint(equalTo: self.scrollView.frameLayoutGuide.widthAnchor).isActive = true
    ```

2. 가로 스크롤이 필요할 때 컨테이너 뷰의 height를 `frameLayougGuide`와 같게 한다.

    ```swift
    self.containerView.heightAnchor.constraint(equalTo: self.scrollView.frameLayoutGuide.heightAnchor).isActive = true
    ```