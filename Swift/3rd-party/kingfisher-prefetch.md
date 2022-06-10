# Kingfisher Prefetch

이미지가 캐싱되어 있지 않아서 다운로드를 받아야할 때 약간의 딜레이가 생길 수 있다. Kingfisher의 Prefetch 기능은 [2.1.0](https://github.com/onevcat/Kingfisher/releases/tag/2.1.0) 버전에서 추가됐으며, 이미지를 미리 저장하고 화면에 표시해야할 때 캐시로부터 가져와서 앱의 성능을 향상시켜 준다. 우선 Kingfisher의 Prefetch 기능을 제공하는 `ImagePrefetcher` 클래스에 대해 살펴보자.

&nbsp;
## ImagePrefetcher

`ImagePrefetcher`는 이미지 리소스 목록을 가지고 있을 때 화면에 표시하기 전에 다운로드를 할 수 있게 해주는 클래스이다. 어떻게 동작하는지 살펴보자.

[Cheet Sheet](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#prefetch) 예제 소스:

```swift
let urls = ["https://example.com/image1.jpg", "https://example.com/image2.jpg"].map { URL(string: $0)! }
let prefetcher = ImagePrefetcher(urls: urls) {
  skippedResources, failedResources, completedResources in
  print("These resources are prefetched: \(completedResources)")
}
prefetcher.start()

// Later when you need to display these images:
imageView.kf.setImage(with: urls[0])
anotherImageView.kf.setImage(with: urls[1])
```

`ImagePrefetcher`는 이미지 리소스 목록과 prefetch가 완료된 뒤 호출되는 클로저를 파라미터로 받아 초기화된다. 클로저의 세 가지 파라미터에 대해 알아보자.

1. `skippedResources` : prefetch를 시작하기 전 이미 캐싱되어 있는 리소스의 목록
2. `failedResources` : prefetch를 시도했지만 다운로드 혹은 캐싱에 실패한 리소스의 목록
3. `completedResources` : prefetch를 성공한 리소스의 목록

이제 `start()` 함수를 통해 prefetch를 시작할 수 있다. `start()` 함수는 아래의 세 가지 조건을 확인하고 조건이 모두 충족되면 prefetch를 시작한다. 이후 prefetch가 모두 완료되면 클로저를 실행하여 결과를 반환한다.

1. `ImagePrefetcher`는 한 번 prefetch가 중지됐다면 다시 시작할 수 없기 때문에 중지 여부를 확인한다.
2. `maxConcurrentDownloads`의 값이 0보다 큰지 확인한다. 이는 동시에 다운로드할 수 있는 갯수를 의미하며 기본 값은 5이다.
3. prefetch할 이미지 리소스 목록이 비어있는지 확인한다.

이미지를 다운로드하는 비동기 작업의 딜레이를 줄일 수 있는 게 가장 큰 장점으로 보인다. 하지만 어떻게 활용해야 할까? Kingfisher의 Prefetch 기능은 컬렉션 뷰 혹은 테이블 뷰와 함께 사용하면 진가를 발휘할 수 있다.

&nbsp;
## With UICollectionView or UITableView

이미지의 URL이 저장되어 있는 배열을 컬렉션 뷰의 데이터 소스로 사용한다고 했을 때, 컬렉션 뷰의 셀을 화면에 나타내면서 이미지를 다운로드 한다면 이미지가 나타나기까지 시간이 걸릴 수도 있다. 

이를 개선하기 위해 WWDC 16에서 Pre-fetching이 등장했다. 기존에는 무거운 데이터를 로딩하는 작업을 `cellForItemAtIndexPath`에서 수행했다. 따라서 이미지를 다운로드하는 것과 같이 비동기 네트워크 요청을 하는 작업은 응답이 오기까지 빈 셀을 보여줘야 했다. 하지만 미리 셀을 초기화 시켜놓고 생명 주기에 따라 표시를 해주는 Pre-fetching을 통해 개선할 수 있게 됐다. 

> Pre-fetching에 대해서 따로 정리할 예정이며 [What's New in UICollectionView in iOS 10](https://developer.apple.com/videos/play/wwdc2016/219/?time=273) 에서 자세한 내용을 확인할 수 있다.

Kingfisher의 `ImagePrefetcher`를 컬렉션 뷰 혹은 테이블 뷰의 Pre-fetching과 함께 사용할 수 있다.

```swift
extension ViewController: UICollectionViewDataSourcePrefetching {
  func collectionView(_ collectionView: UICollectionView, prefetchItemsAt indexPaths: [IndexPath]) {
    let urls = indexPaths.flatMap { URL(string: $0.urlString) }
    ImagePrefetcher(urls: urls).start()
  }
}
```

만약 사용자가 빠른 속도로 스크롤을 해서 prefetch할 시간을 확보하지 못할 수 있다. 이러한 경우에는 prefetch를 완료하는 것 보다 사용자에게 보여질 셀을 구성하는 작업이 우선시 되어야한다.

`UICollectionViewDataSourcePrefetching`에는 컬렉션 뷰의 셀이 화면 밖으로 스크롤될 때 불필요한 prefetch 작업을 취소하기 위해 `cancelPrefetchingForItemsAt` 함수가 호출된다. 여기서 적절히 `ImagePrefetcher().stop()` 함수를 호출해서 prefetch 작업을 취소할 수 있다.