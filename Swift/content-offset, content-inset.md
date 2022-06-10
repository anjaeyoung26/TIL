## contentOffset

`bounds`의 x, y 좌표를 의미한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/166966615-396c6bb2-b2e8-4ab4-9c6a-439da2fd0c8b.png">
</p>

스크롤 뷰에서 스크롤은 `contentOffset`이 변하는 동작이다. 즉 `contentOffset`은 사용자가 현재 스크롤 뷰 내에서 스크롤한 위치이다. 스크롤은 스크롤 뷰의 `contentSize`는 유지되지만 `contentOffset`은 사용자의 상호 작용에 따라 변경된다. 

`contentOffset`은 주로 페이징을 위해 테이블 뷰나 컬렉션 뷰가 최하단까지 스크롤 됐는지 확인할 때 사용된다.

```swift
func scrollViewDidScroll(_ scrollView: UIScrollView) {
	if scrollView.contentOffset.y >= (scrollView.contentSize.height - scrollView.frame.size.height) {
		// Reach bottom
	}

	if scrollView.contentOffset.y < 0 {
		// Reach top
	}

	if (scrollView.contentOffset.y >= 0 && 
		scrollView.contentOffset.y < (scrollView.contentSize.height - scrollView.frame.size.height)) {
		// Not top and not bottom
	}
}
```

&nbsp;
## contentInset

**컨텐츠의 상・하・좌・우 여백**이다. 바깥 쪽이 아닌 안 쪽으로 공간을 주어 하위 뷰를 둘러싸는 공간을 확장시킨다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/166967917-9f5d8c19-025a-4739-9f72-d841ce3d3547.png">
</p>

`top`, `left`, `right`, `bottom` 네 가지 방향으로 적용된다. 스크롤 뷰의 컨텐츠에 아래와 같이 여백을 추가할 수 있다.

```swift
scrollView.contentInset = UIEdgeInsets(top: 10, left: 10, bottom: 10, right: 10)
```