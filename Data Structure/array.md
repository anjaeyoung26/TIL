# Array

가장 기본적인 자료구조로 논리적 저장 순서와 물리적 저장 순서가 일치해서 인덱스로 원소에 접근할 수 있다. 즉 비순차적 접근(Random Access)가 가능하다.

## 시간 복잡도

### **접근**

접근하려는 원소의 위치(인덱스)를 알고 있다면 시간 복잡도는 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(1)">이다. 하지만 원소의 위치를 모른다면 배열에 있는 모든 원소를 탐색해야 하므로 시간 복잡도는 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(n)">이다.

```c
int num = arr[index];
```

### **삽입, 삭제**

만약 모든 인덱스에 원소가 존재하는 정적인 배열(길이가 고정된)이라면 더 이상 원소를 삽입할 수 없기 때문에 길이가 1만큼 더 긴 배열을 선언하여 삽입해야 한다. 기존에 배열의 모든 원소를 새롭게 선언한 배열에 복사해야 하므로 시간복잡도는 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(n)">이다. 정적인 배열과는 다르게 동적인 배열(길이가 가변적인)이라면 새로운 배열을 선언해서 원소를 복사할 필요가 없다.

### **i 또는 처음에 값 삽입, 삭제**

배열의 연속적인 특징으로 인해 i보다 더 큰 인덱스를 갖는 원소들을 Shift 해야하므로 시간 복잡도는 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(n)">이다. 삽입, 삭제 간 Shift 연산으로 인해 시간복잡도가 올라가는 문제점을 해결하기 위해 **Linked List**란 자료구조를 사용한다.

### **마지막에 값 삽입, 삭제**

Shift 연산을 할 필요 없이 배열의 마지막 인덱스에 접근하여 삽입, 삭제 연산을 수행하므로 시간복잡도는 <img src="https://render.githubusercontent.com/render/math?math=\color{gray}O(1)">이다.