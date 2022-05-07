# 시간 복잡도

알고리즘의 처리 시간을 의미하지만 실행 시간은 하드웨어의 성능과 프로그래밍 언어에 따라 값이 달라지기 때문에 실제 **명령어의 실행 횟수**를 뜻한다.

&nbsp;
## 시간복잡도 함수

알고리즘 수행에 필요한 전체 연산 수를 계산하면 실행 시간을 구할 수 있다. 이를 함수로 나타낸 것이 시간복잡도 함수이며 $T(n)$으로 표기한다.

```c
void f(int n){
	int cnt = 0;            // 1
	int sum = 0;            // 1
	for(int i=0; i<n; i++){
		cnt++;                // n
		sum += i;             // n
	}
}
```

`int cnt = 0;` 과 `int sum = 0;` 은 대입 연산으로 `n`의 값과 상관없이 1번만 실행된다. 그리고 반복문에서 `n`번 실행되는 연산이 두 개 있으므로 $2n+2$로 나타낸다.

&nbsp;
## Big-O 표기법

알고리즘의 시간복잡도와 공간복잡도를 표현하는 대표적인 방법으로 알고리즘 실행에 있어 **최악의 경우**를 나타낸다. 만약 배열 내의 x값을 찾기 위해 배열의 첫 번째 요소부터 순차적으로 찾는 알고리즘이 있을 때 최선의 경우는 x가 배열의 첫 번째 요소일 경우이고, 최악의 경우는 x가 배열의 마지막 요소일 경우이다. 따라서 해당 알고리즘의 최선의 경우는 $O(n)$이고, 최악의 경우는 $O(n)$이다. 아래의 시간 복잡도 함수를 Big-O 표기법으로 나타내보자.

$3n^2+10n+20$

입력값 n이 증가할수록 차수가 가장 큰 항($n^2$)이 결과값에 가장 큰 영향을 미치며, 다른 항들은 상대적으로 무시된다. 그래서 Big-O 표기법으로 나타내면 $O(n^2)$이다.

&nbsp;
### 종류 및 비교

<img src="https://user-images.githubusercontent.com/61190690/166917744-27f91719-3462-4ade-beeb-800a6f16deab.png" width="600" height="400">

$O(1) < O(log[n]) < O(n) < O(n * log[n]) < O(n^2) < O(2^n) < O(n!)$

입력값(n)이 작을 때는 차이가 적지만 입력값이 커질수록 급격하게 차이가 난다. 

&nbsp;
### $O(1)$

<img src="https://user-images.githubusercontent.com/61190690/166918062-cad11a68-16f6-4785-9c03-0c12c35b1556.png" width="600" height="400">

**상수 복잡도**라고 하며, 입력 값에 상관없이 일정한 시간이 소요된다.

```swift
print("Hello World!")
```

&nbsp;
### $O(logn)$

<img src="https://user-images.githubusercontent.com/61190690/166918076-a05c88c8-9710-4137-af8c-ca655d8de01d.png" width="600" height="400">

**로그 복잡도**라고 하며, 연산을 할 수록 횟수가 절반으로 줄어드는 구문이다. 대표적인 예로 이진 탐색 트리가 있다.

&nbsp;
### $O(n)$

<img src="https://user-images.githubusercontent.com/61190690/166918088-33cb84c9-a0ce-4da0-bb8b-fe630e12f589.png" width="600" height="400">


**선형 복잡도**라고 하며, 입력 값과 실행 횟수가 비례하는 구문이다.

```c
void func(int n){
	for(i=0; i<count; i++){
		print(n);
	}
}
```

&nbsp;
### $O(n^2)$

<img src="https://user-images.githubusercontent.com/61190690/166918099-56e97d51-4370-4a2a-b416-673c3d2d40d4.png" width="600" height="400">

**2차 복잡도**라고 하며, 입력 값이 증가함에 따라 소요되는 시간이 n의 제곱수의 비율로 늘어난다.

```java
function O_quadratic_algorithm(n) {
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      // do something for 1 second
    }
  }
}
```

&nbsp;
### $O(2^n)$

<img src="https://user-images.githubusercontent.com/61190690/166918119-bb90f12f-3948-4166-a377-421e720331dd.png" width="600" height="400">

<img src="https://user-images.githubusercontent.com/61190690/166918129-7651071a-fffb-450b-b0d1-adb868f9ab41.png" width="600" height="200">

**기하급수적 복잡도**라고 하며, 대표적인 예는 재귀로 구현한 피보나치 함수가 있다.

```java
function fibonacci(n) {
  if (n <= 1) {
    return 1;
  }
  return fibonacci(n - 1) + fibonacci(n - 2);
}
```