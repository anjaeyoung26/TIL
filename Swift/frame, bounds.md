# frame vs bounds

```swift
open var frame: CGRect
open var bounds: CGRect
```

`frame`과 `bounds` 모두 [CGRect](./cgsize-cgrect.md) 타입이다. 즉 높이와 너비를 가진 사각형으로 그려질 수 있고 좌표로 위치를 나타낸다. 하지만 `frame`과 `bounds`는 좌표의 기준이 다르다. **`frame`은 `superview`를 기준**으로, **`bounds`는 자신의 좌표계를 기준**으로 위치 및 크기를 나타낸다.

&nbsp;

아래의 그림은 각 A, B, C의 `frame`과 `bounds`를 나타내고 있다. A는 B의 `superview`이며, B는 C의 `superview`이다. `bounds`는 자신의 좌표계를 기준으로 기본적인 값은 $(0, 0)$이다. 반면에 `frame`은 `superview`의 좌표계를 기준으로 나타내기 때문에 B는 A의 좌표계를 기준으로, C는 B의 좌표계를 기준으로 나타내고 있다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/167057995-2877e435-3651-475c-904f-a001f04daa7b.png" alt="그림1" width="500" height="300">
    <p align="center" style="color:gray">
        그림1
    </p>
</p>

&nbsp;

아래의 그림에서 B의 `frame`을 $(10, 10)$만큼 이동시켰다. B의 현재 위치에서 $(10, 10)$만큼 이동한 $(20, 30)$에 다시 그려졌다. C의 좌표를 살펴보면 `frame`의 값은 변하지 않았지만 `superview`인 B의 좌표가 변한만큼 이동했다. 반면에 `bounds`는 자신의 좌표계가 기준이므로 `superview`의 좌표와 상관없이 일정하다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/167059701-dc87070f-e10a-4ec0-aaa4-22c62e75182a.png" alt="그림2" width="500" height="300">
    <p align="center" style="color:gray">
        그림2
    </p>
</p>

&nbsp;

그렇다면 B의 `bounds`를 이동시키면 어떻게 될까? *그림1*에서 B의 `bounds`를 $(10, 10)$만큼 이동시키면 아래의 그림과 같다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/167060815-0c0b365d-e45c-42a8-ba68-12108a9c144c.png" alt="그림3" width="500" height="300">
    <p align="center" style="color:gray">
        그림3
    </p>
</p>

&nbsp;

`frame`과 `bounds`는 사이즈를 나타낼 때도 차이점이 있다. `frame`은 `superview`의 영역을 얼마만큼 차지하는지 나타내고, `bounds`는 자신의 영역을 나타낸다. 아래의 그림은 높이가 70, 너비가 50인 B를 회전시켰을 때 `frame.size`와 `bounds.size`의 차이를 보여준다.

<p align="center">
    <img src="https://user-images.githubusercontent.com/61190690/167062544-7f0e5444-c41f-43c0-8eef-2c5adeb1b00f.png" alt="그림4" width="500" height="300">
    <p align="center" style="color:gray">
        그림3
    </p>
</p>
