## Call by value

값 타입의 인스턴스는 할당 또는 파라미터로 전달될 때 값이 복사된다. 복사된 인스턴스는 원본 인스턴스와 독립적으로 생성되어 값을 변경하여도 원본 인스턴스는 그대로 유지된다. 이는 의도치 않은 데이터의 변경을 방지할 수 있으며, 멀티 스레드 환경에서 발생할 수 있는 동시성 문제로부터 안전하다. 

&nbsp;
### Stack에 할당

값 타입은 인스턴스가 언제 사용되고, 사라지는지 컴파일 단계에서 알 수 있기 때문에 Stack 공간에 메모리가 할당된다. [Stack](../Data%20Structure/stack.md)은 선형적인 구조이며, 메모리의 할당과 해제가 편리한 자료구조이다. 단순히 Stack 포인터가 가리키고 있는 곳을 줄여서 필요한 메모리를 할당하고, 사용이 끝나면 Stack 포인터를 줄인 곳으로 돌아가서 다시 증가 시켜서 메모리의 할당을 해제한다. WWDC16의 [Understanding Swift Performance](https://developer.apple.com/videos/play/wwdc2016/416/)에 따르면, 이러한 스택의 할당은 모두 O(1)의 시간으로 굉장히 빠르다.

&nbsp;
### 값 타입의 동작을 이해해보자.

값이 복사되는 과정을 이해하기 위해 아래의 예시를 살펴보자.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/178634540-64f30a51-5eb5-4c9f-ba74-ff9a112ee03b.png">
</p>

`Point`는 두 개의 `Double` 타입 프로퍼티를 갖는 구조체이다. `point1`이라는 인스턴스를 생성하고, 이를 `point2`에 복사한 뒤 `point2.x`의 값을 5로 설정한다. 우측의 Stack을 살펴보면 `point1`과 `point2`에 대한 독립적인 공간이 할당됐다. 따라서 `point2.x`의 값이 변경돼도 `point1`에 반영되지 않는다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/178636857-cc7f9880-91e3-41d6-bbd5-405b5275af41.png">
</p>

이제 `point1`과 `point2`의 사용이 끝나면 Stack 포인터를 점차적으로 증가시켜서 두 인스턴스에 대한 메모리 할당을 쉽게 해제할 수 있다.

&nbsp;
## Call by reference

참조 타입의 인스턴스는 할당 또는 파라미터로 전달될 때 객체를 가리키고 있는 주소 값은 Stack에 할당하고, 실질적인 데이터는 Heap에 할당한다. 모든 인스턴스가 하나의 주소를 가리키고 있기 때문에, 하나의 인스턴스의 값을 변경하면 다른 인스턴스도 마찬가지로 값이 변경된다. 이는 멀티 스레드 환경에서 발생할 수 있는 동시성 문제로 인해 [NSLocking](./Concurrency/nslocking.md)과 같은 동기화 매커니즘을 사용하여 무결성을 보호해야 한다. 따라서 Stack에만 데이터를 저장하는 값 타입에 비해 더 많은 비용이 들어가며 속도가 느리다.

&nbsp;
### Heap에 할당

참조 타입은 런타임 단계에서 인스턴스의 생명 주기를 알 수 있기 때문에 Heap 공간에 메모리가 할당된다. [Heap](../Data%20Structure/heap.md)은 런타임에 크기가 결정되는 인스턴스에 대한 메모리를 동적으로 할당하는 영역이다. 또한 더 이상 사용하지 않는 데이터를 찾기 위해 참조 카운팅을 해야한다. Swift는 [ARC](./arc.md)가 참조 카운팅을 계산하여 더 이상 사용하지 않는 인스턴스에 대한 메모리를 자동으로 해제하지만, [Strong Reference Cycle](./arc.md/#strong-reference-cycles)이 발생하면  [weak, unknowed](./weak-unowned-reference.md) 키워드를 적절히 사용해야 한다.

&nbsp;
### 참조 타입의 동작을 이해해보자.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/178648383-9ae9cb66-0000-4e74-8829-278f7584669c.png">
</p>

`Point`를 구조체가 아닌 클래스로 구현한 코드이다. Stack에는 구조체와 다르게 프로퍼티가 아닌 `point1`과 `point2`에 대한 참조를 위해 메모리가 할당된다. 이는 Heap에 할당할 메모리에 대한 참조를 나타낸다. 따라서 `Point(x: 0, y: 0)`이라는 인스턴스를 생성하면 Swift가 무결성을 위해 Heap을 lock하고 크기에 맞는 메모리 블록을 탐색한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/178648385-972010d2-0159-419b-8a1c-1a02a846b095.png">
</p>

크기에 맞는 메모리 블록을 찾으면 Stack의 `point1`에 대한 참조를 Heap의 메모리 블록의 주소로 초기화한다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/178648387-ce1687a2-7858-4189-b55e-e373c4d77e8c.png">
</p>

Heap의 공간을 보면 구조체로 구현했을 때와 다르게 파란색 공간이 추가되어 있다. 이는 Swift가 우리를 대신하여 `Point`라는 클래스를 관리하기 위해 추가적으로 할당한 공간이다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/178648388-8dd039bf-76ef-458e-a116-fc51382d5860.png">
</p>

이제 `point2`에 `point1`을 할당해 줄 때 구조체와 다르게 `point1`의 내용을 복사하지 않는다. 클래스에서는 대신 `Point` 클래스의 참조를 복사한다. 그리하여 `point1`, `point2` 두 인스턴스가 Heap의 동일한 주소를 가르키게 된다.

<p align="center">
<img src="https://user-images.githubusercontent.com/61190690/178648390-6c9c340d-123b-4db0-a0fe-aaf42be70213.png">
</p>

따라서 `point2.x`에 5라는 값을 할당하게 되면 `point1`도 `point2`와 똑같은 곳을 참조하고 있기 때문에, `point1.x`의 값도 5가 된다. 위 동작에서 볼 수 있듯이 클래스는 Heap에서 추가적인 메모리의 할당이 필요하기 때문에 구조체보다 더 많은 비용이 든다.

&nbsp;
## 중요한 점!

값 타입은 모두 Stack에 메모리가 할당되고, 참조 타입은 모두 Heap에 메모리가 할당된다고 단정지을 수 없다. **나중에 정리할 예정**