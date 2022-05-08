# Linked List

노드를 이용해 자료의 주소 값으로 서로 연결되어 있는 자료구조로 배열의 삽입, 삭제 간 Shift 연산에 대한 비효율성을 해결하고자 고안된 자료구조이다.

## 특징

1. 순차적 접근 방식이다. 즉 특정 원소에 접근하기 위해서 처음 부터 검색한다.

2. 크기가 고정적이지 않고 새로운 요소가 추가될 때 런타임에 동적으로 메모리를 할당한다.

3. 인덱스와 같이 물리적 배치를 사용하지 않고 참조 시스템(다음 노드 연결에 대한 포인터)을 사용한다.

4. 노드라는 구조체의 집합이다. 일반적으로 노드는 데이터와 다음 노드를 가리키는 포인터를 갖는다.

    - 연결 리스트의 첫 번째 노드를 Head라고 하며, 나머지 원소들을 탐색하거나 데이터를 삽입/삭제 하는 연산을 수행하기 위해 Head의 주소는 반드시 기억해야 한다.(1번 참고)
    
    - 연결 리스트의 마지막 노드를 Tail라고 한다.
    
&nbsp;
## vs 배열

### 시간복잡도

|  | 배열 | 연결 리스트 |
| --- | :---: | :---: |
| 맨 앞에 삽입, 삭제 | O(1) | O(1) |
| 맨 뒤에 삽입 | O(1) | O(1) |
| 맨 뒤에 삭제 | O(1) | 단일, 원형 : O(n) </br>이중 : O(1) |   
| 중간에 삽입, 삭제 | O(n) | O(n) |

&nbsp;
### 장단점

|  | 배열 | 연결 리스트 |
| --- | --- | --- |
| **장점** | 1. 인덱스로 원하는 요소에 빠르게 접근할 수 있다.</br>2. 참조를 위한 추가적인 메모리 할당이 필요없다.</br>3. 구현이 간편하다. | 1. 동적으로 메모리를 할당해서 메모리를 효율적으로 사용한다.</br>2. 삽입, 삭제 시 노드 간의 연결을 변경해서 간편하다.
| **단점** | 1. 고정적인 크기</br>&nbsp;&nbsp;&nbsp;&nbsp;- 크기를 변경하려면 기존 원소들을 복사해서 옮겨야 한다.</br>&nbsp;&nbsp;&nbsp;&nbsp;- 메모리의 낭비가 발생할 수 있다.</br>2. 연속적인 특징으로 인해 물리적, 논리적 순서를 맞춰야한다.</br>&nbsp;&nbsp;&nbsp;&nbsp;- 원소의 삽입/삭제 시 Shift가 필요하다. | 1. 노드 연결에 사용하는 포인터를 위한 공간을 사용한다.</br>2. 순차적 접근으로 속도가 느리다.</br>3. 작은 데이터를 저장하기에 오버헤드가 발생한다. |
| **용도** | 데이터 ‘접근’이 주 업무일 경우 | 데이터 ‘수정’이 주 업무일 경우 |

&nbsp;
## 구현 방식

연결 리스트를 구현할 때 구현의 용이함을 위해 더미 노드를 사용할 수 있다. Head가 가리키는 첫 번째 노드를 아무런 데이터를 갖지 않는 더미 노드로 구현한다.

&nbsp;

### 일반적인 방식
먼저 더미 노드를 사용하지 않는 방식은 아래와 같다.

![dummy-node2](https://user-images.githubusercontent.com/61190690/167282902-8742694e-cdb7-42d0-a1d5-e160f171f5a5.png)

추가할 노드가 연결 리스트의 첫 번째 노드인지 판단하는 1의 과정이 필요하다.

```c
Node *head;
Node *tail;

Node *newNode = (Node*)malloc(sizeof(Node));
newNode->data = data;
newNode->next = NULL;

if(head==NULL){ // 1
	head = newNode;
}else{
	tail->next = newNode;
}

tail = newNode
```

&nbsp;

### 더미 노드 사용
다음으로 더미 노드를 사용한 코드는 아래와 같다.

![dummy-node](https://user-images.githubusercontent.com/61190690/167282878-90bc1e9d-8ada-425a-9c7e-b9d4400fa41b.png)


이미 더미 노드가 추가됐으므로 첫 번째 노드인지 판단하지 않고 삽입한다. 즉 노드를 삽입할 때 위치와 상관없이 동일한 과정을 거친다.

```c
Node *head;
Node *tail;

head = (Node*)malloc(sizeof(Node));
tail = head;

Node *newNode = (Node*)malloc(sizeof(Node));
newNode->data = data;
newNode->next = NULL;

tail->next = newNode;
tail = newNode;
```

&nbsp;
## 종류

## Singly

<img src="https://user-images.githubusercontent.com/61190690/167282915-314d33a5-42cd-4298-8540-0d404d24018b.png" width="500">

&nbsp;
### 노드의 구성

각 노드는 다음 노드에 대한 포인터를 가지고 있다. 연결 리스트의 마지막 노드는 `NULL`을 갖는다.

```c
struct Node {
	struct Node * next;
	int data;
};
```

&nbsp;
### 삽입

<img src="https://user-images.githubusercontent.com/61190690/167282982-59532c0a-1f02-41c6-b435-c6081e0fb358.png" width="600">

```c
void add(struct Node *target, int data)
{
	struct Node *newNode = malloc(sizeof(struct Node));
	newNode->next = target->next;                       
	newNode->data = data;                                
	target->next = newNode;                             
}
```

위 처럼 삽입하려는 위치의 이전 노드(`target`)에 대한 정보를 알고 있다면 시간 복잡도는 $O(1)$이다. 하지만 이전 노드에 대한 정보를 모른다면 탐색이 필요하므로 시간 복잡도는 $O(n)$이다.

* 이전 노드에 대한 정보를 알 수 있는 경우는 머리 혹은 꼬리 노드와 관련된 연산이다. 즉 연결 리스트의 맨 앞 혹은 맨 뒤에 삽입하는 연산이다.

* 맨 뒤에 삽입하는 경우도 연결 리스트가 꼬리 노드에 대한 변수를 가지고 있지 않을 경우 마지막 노드에 접근하기 위해 탐색이 필요하다.

&nbsp;
### 삭제

<img src="https://user-images.githubusercontent.com/61190690/167282983-9c844a62-8f49-44d0-a8de-9f23da27889e.png" width="600">

```c
void remove(struct Node *target)
{
	struct Node *removeNode = target->next;
	target->next = removeNode->next;
	free(removeNode)
}
```

삽입과 마찬가지로 삭제하려는 위치의 이전 노드(`target`)에 대한 정보를 알고 있다면 시간 복잡도는 $O(1)$, 모른다면 $O(n)$이다. 또한 꼬리 노드를 삭제하는 연산의 경우 기존의 꼬리 노드를 가리키던 노드(A)의 다음(next)을 NULL로 변경하는 등 A를 변경해야 하므로 시간 복잡도는 $O(n)$이다.

&nbsp;
## Doubly

<img src="https://user-images.githubusercontent.com/61190690/167282986-71545bfb-4fd9-4d4e-ae31-1af13c78fcdd.png" width="500">

&nbsp;
### 노드의 구성

각 노드는 이전 노드와 다음 노드에 대한 포인터를 가지고 있다. 단방향 연결 리스트와 마찬가지로 마지막 노드의 다음(`next`)는 NULL 값을 갖는다.

```c
struct Node {
	struct Node * prev;
	struct Node * next;
	int data;
};
	
```

### 장점

$n$개의 노드를 갖는 단방향 연결 리스트에서 $n-1$번째 노드에 접근하기 위해서는 $n-2$번의 포인터 추적이 필요하다. 왜냐하면 단방향 연결 리스트는 머리 노드부터 순차적으로 접근해야 하기 때문이다. 하지만 양방향 연결 리스트는 꼬리 노드부터 반대로도 접근할 수 있다. $n/2$보다 작은 순서의 노드는 머리 노드부터, $n/2$보다 크거가 같은 순서의 노드는 꼬리 노드부터 탐색하도록 구현하여 단방향 연결 리스트에 비해 탐색 시간을 절반으로 줄일 수 있다.

### 단점

`prev`라는 포인터 변수를 사용하므로 단방향 연결 리스트에 비해 메모리를 더 많이 사용하고 구현이 복잡하다.

&nbsp;
## Circular

<img src="https://user-images.githubusercontent.com/61190690/167282985-c938b80b-2d2d-420b-9777-c105a1e6e028.png" width="500">

&nbsp;
### 노드의 구성

단방향 연결 리스트와 같이 다음 노드에 대한 포인터를 갖는다. 하지만 마지막 노드의 다음(`next`)는 `NULL`이 아닌 머리 노드를 가리킨다.

```c
struct Node {
	struct Node * next;
	int data;
};
```

### 장점

하나의 노드에서 모든 노드로의 접근이 가능하여 노드의 삽입, 삭제가 단순해진다. 또한 일반적인 원형 연결 리스트를 변형하여 단순 연결 리스트처럼 머리와 꼬리에 대한 각각의 포인터 변수를 두지 않고 하나의 포인터 변수를 사용할 수 있다.

- 꼬리를 가리키는 포인터 변수 : `tail`
- 머리를 가리키는 포인터 변수 : `tail→next`

### 단점

노드의 삽입, 삭제 시 선행 노드의 포인터가 필요하다.(일반적인 연결 리스트의 단점)