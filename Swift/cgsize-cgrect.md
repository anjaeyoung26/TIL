## CGSize

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/166910001-fbb25ef0-b933-40f9-8ed0-29f1f94cae20.png">
</p>

너비와 높이의 값을 포함한 구조체로, 사각형으로 간주되지 않는다.

```swift
struct CGSize {
    var width: CGFloat
    var height: CGfloat
}
```

&nbsp;
## CGRect

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/166909996-156f2086-b15a-4fd7-83ef-e2cd6423607c.png">
</p>

사각형의 위치와 크기를 포함한 구조체로, `CGSize`와 다르게 원점(`origin`)을 갖는다.

```swift
struct CGRect {
    var origin: CGPoint
    var size: CGSize
}
```

`CGPoint`는 x, y 좌표값을 가치고 있는 구조체로 위치를 나타낸다.

```swift
struct CGPoint {
    var x: CGFloat
    var y: CGFloat
}
```

너비와 높이의 값은 음수가 될 수 있다. 너비와 높이가 음수라는 것은 원점을 기준으로 아래와 같이 나타난다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/166909990-52bdd589-d34c-417e-80d2-9a620554ba17.png" width="600" height="400">
</p>