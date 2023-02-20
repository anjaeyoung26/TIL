## contentHugging

컨텐츠의 고유한 크기보다 커지는 경우를 방지하기 위한 우선순위로, 우선순위가 높을수록 자신의 사이즈를 유지하고 낮을수록 사이즈가 커진다.

<img src="https://user-images.githubusercontent.com/61190690/167232165-6a4fe9bf-fb7a-4478-935f-78978a78d2d5.png" width="400" height="100">

|  | redLabel | blueLabel |
| --- | :---: | :---: |
| contentHugging | 251 | 251 |

<img src="https://user-images.githubusercontent.com/61190690/167232166-9fe58f3d-08ba-4b42-b930-4e4021d1a4eb.png" width="400" height="100">

|  | redLabel | blueLabel |
| --- | :---: | :---: |
| contentHugging | 250 | 251 |

기본적으로 뷰는 `contentHugging`에 대해 250의 우선순위를 가지고 있고, 각 뷰의 `contentHugging`에 대한 우선순위를 비교하여 레이아웃이 적용된다.

&nbsp;
## compressionResistance

컨텐츠의 고유한 크기보다 작아지는 경우를 방지하기 위한 우선순위로, 우선순위가 높을수록 자신의 사이즈를 유지하고 낮을수록 사이즈가 커진다.

<img src="https://user-images.githubusercontent.com/61190690/167232167-4b25c288-8594-495c-9c22-b42aa7cbe2b4.png" width="400" height="100">

|  | width constraint | compressionResistance |
| --- | :---: | :---: |
| priority | 1000 | 1000 |

<img src="https://user-images.githubusercontent.com/61190690/167232168-f395c062-011c-481d-a149-ace3856c9bed.png" width="400" height="100">

|  | width constraint | compressionResistance |
| --- | :---: | :---: |
| priority | 999 | 1000 |

기본적으로 뷰는 `compressionsResistance`에 대해 750의 우선순위를 가지고 있다.
