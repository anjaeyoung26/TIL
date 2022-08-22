## instrinsicContentSize
우선 `intrinsicContentSize`에 대해 알아야한다. 이는 컨텐츠의 본질적인 사이즈로 몇몇 뷰는 자신의 컨텐츠에 따라 고유의 크기를 가진다. 예를들어 `UILabel`과 `UIButton`은 텍스트의 크기와 약간의 마진을 `instrinsicContentSize`로 갖는다. 하지만 모든 뷰가 `instrinsicContentSize`를 갖는 것이 아니며, `instrinsicContentSize`로 높이, 너비를 정의할 수 있는지 여부가 다르다. 그런데 `contentHugging`, `compressionResistance`와 무슨 관계가 있을까?

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/167232169-885844d7-4618-4854-8420-c250a03249cc.png">
</p>

위 그림에서 보다시피 `instrinsicContentSize`는 `contentHugging`과 `compressionResistance`에 의해 결정된다. `contentHugging`은 양쪽 팔로 껴안는 것처럼 밖에서 안으로, `compressionResistance`는 hugging으로 컨텐츠가 압축되는 걸 저항하는 것처럼 안에서 밖으로 화살표가 향한다. 그럼 각각이 의미하는 바를 알아보자.

&nbsp;
## contentHugging

뷰의 최대 크기에 대한 제한으로 우선순위가 높을수록 커지기를 거부한다. 또한 `axis`를 통해 `horizontal`, `vertical` 중 어떤 축에 대해 제한을 설정할지 결정한다. 기본적으로 뷰는 `contentHugging`에 대해 250의 우선순위를 가지고 있고, 각 뷰의 `contentHugging`에 대한 우선순위를 비교하여 레이아웃이 적용된다.

<img src="https://user-images.githubusercontent.com/61190690/167232165-6a4fe9bf-fb7a-4478-935f-78978a78d2d5.png" width="400" height="100">

|  | redLabel | blueLabel |
| --- | :---: | :---: |
| contentHugging | 251 | 251 |

<img src="https://user-images.githubusercontent.com/61190690/167232166-9fe58f3d-08ba-4b42-b930-4e4021d1a4eb.png" width="400" height="100">

|  | redLabel | blueLabel |
| --- | :---: | :---: |
| contentHugging | 250 | 251 |

&nbsp;
## compressionResistance

뷰의 최소 크기에 대한 제한으로 우선순위가 높을수록 작아지기를 거부한다. `contentHugging`과 마찬가지로 `axis`를 통해 축을 결정하고 우선순위에 따라 레이아웃이 적용된다. 또한 기본적으로 뷰는 `compressionsResistance`에 대해 750의 우선순위를 가지고 있다.

<img src="https://user-images.githubusercontent.com/61190690/167232167-4b25c288-8594-495c-9c22-b42aa7cbe2b4.png" width="400" height="100">

|  | width constraint | compressionResistance |
| --- | :---: | :---: |
| priority | 1000 | 1000 |

<img src="https://user-images.githubusercontent.com/61190690/167232168-f395c062-011c-481d-a149-ace3856c9bed.png" width="400" height="100">

|  | width constraint | compressionResistance |
| --- | :---: | :---: |
| priority | 999 | 1000 |