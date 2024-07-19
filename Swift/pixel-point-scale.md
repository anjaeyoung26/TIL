## Pixel

화면을 구성하는 가장 작은 단위로, 디스플레이의 해상도를 나타냅니다. 이는 화소라고도 부르는데, 모니터의 해상도를 나타낼 때도 사용합니다. 예를 들어, FHD 모니터는 1920x1080 = 2,073,600개의 pixel로 이루어진 해상도를 의미합니다. 하지만 모니터의 해상도가 높다고해서 무조건 디스플레이가 선명하지 않고 PPI(Pixel Per Inch) 지표로 알 수 있습니다. 이는 1 inch에 몇개의 pixel이 표시되는지 나타내는 지표로, PPI가 높은 모니터일수록 디스플레이가 선명하게 보입니다.

<p align="center">
<img src="https://github.com/user-attachments/assets/a2b72a2a-15df-4803-9561-54dec218c606" width="600">
</p>

## Point

iOS의 frame, autolayout, origin 등 모든 곳에서 사용되는 논리적 단위로, 다양한 디바이스에서 일관된 레이아웃을 유지하기 위해 사용됩니다. 이를 실제로 화면에 표시할 때 물리적 단위인 pixel로 변환하는데, 각 디바이스의 scale을 고려해서 변환합니다.

<p align="center">
<img src="https://github.com/user-attachments/assets/7a5f0fa4-c7c2-442d-a0d8-7e39db250b73" width="600">
</p>

## Scale

scale은 pixel과 point의 비율을 나타내며, 디바이스마다 1x, 2x, 3x 중에서 정해진 scale factor(스케일 계수)가 적용됩니다.

- 1x: 1pt = 1px
- 2x: 1pt = (2x2)px
- 3x: 1pt = (3x3)px

따라서 40pt 이미지를 설정해놓으면 scale factor에 따라 다른 크기로 표시됩니다.

```swift
let imageView = UIImageView()
imageView.image = UIImage(named: "some_image")
imageView.frame = CGRect(x: 0, y: 0, width: 40, height: 40)
```

- iPhone SE(1세대), 1x: (40x40)px
- iPhone 8, 2x: (80x80)px
- iPhone 11 Pro, 3x: (120x120x)

이러한 스케일링으로 인해서 이미지의 실제 물리적 크기는 디바이스마다 차이가 있습니다. pixel 밀도가 높은 디바이스에서는 한 point에 더 많은 pixel이 표시되므로, 물리적으로 더 작은 공간에 같은 논리적 크기(point)가 표시됩니다. 따라서 scale factor가 같은 디바이스 간에도 디스플레이 크기에 따라 실제 크기가 다르게 보일 수 있습니다. 예를 들어, 40pt 이미지를 iPhone 15 Pro Max와 iPhone 13 mini에서 표시하려고 할 때, 비교적 큰 화면 크기와 해상도를 가진 iPhone 15 Pro Max에서 이미지가 더 작아보일 수 있습니다.