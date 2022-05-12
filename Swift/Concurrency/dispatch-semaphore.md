# Dispatch Semaphore

공유 자원에 접근할 수 있는 작업의 수를 조절한다. `NSLock`과 다르게 공유 자원에 하나 이상의 프로세스나 스레드가 접근할 수 있다.

&nbsp;
## 초기화

```swift
let semaphore = DispatchSemaphore(value: 0)
```

초기값으로 0을 가지는 세마포어를 선언했다. `value`는 공유 자원에 접근할 수 있는 작업의 수를 의미하며, `signal`과 `wait` 함수를 통해 값을 증감시켜 조절한다.

&nbsp;
## wait

```swift
semaphore.wait()
```

`wait`을 호출하게 되면 `value`의 값을 감소시킨다. 만약 `value`의 값이 0이라면 `signal()`이 호출될 때까지 대기한다. 이는 교착상태를 유발할 수 있어 주의해야 한다.

&nbsp;
## signal

```swift
semaphore.signal()
```

`signal`을 호출하게 되면 `value`의 값을 증가시킨다.

&nbsp;
## 예시

### 동시 작업 수 제한

```swift
let queue = DispatchQueue(label: "semaphore", attributes: .concurrent)
let semaphore = DispatchSemaphore(value: 3)

for index in 0..<100 {
    queue.async {
        semaphore.wait()
        print("\(Date()) index")
        sleep(1)
        semaphore.signal()
        print("---------")
    }
}
```

Concurrent Queue에 0부터 99까지의 숫자를 출력하는 100개의 작업을 추가했다. 이때 시스템 환경에 따라 배정된 물리적 스레드의 갯수만큼 작업이 비동기적으로 실행된다. 하지만 위처럼 `value`의 값이 3인 세마포어를 사용해 숫자를 출력하는 `print(index)` 작업의 수를 제한하면 어떻게 출력될까?

> 2022-05-12 07:38:19 +0000 0   
> 2022-05-12 07:38:19 +0000 1   
> 2022-05-12 07:38:19 +0000 2   
> 2022-05-12 07:38:20 +0000 3   
> 2022-05-12 07:38:20 +0000 4   
> 2022-05-12 07:38:20 +0000 5   
> 2022-05-12 07:38:21 +0000 6   
> 2022-05-12 07:38:21 +0000 7   
> 2022-05-12 07:38:21 +0000 8   
> 2022-05-12 07:38:22 +0000 9

결과를 살펴보면 1초에 3번만 숫자를 출력한다. 이처럼 세마포어의 값에 따라 공유 자원에 접근할 수 있는 작업의 수를 정할 수 있다.

&nbsp;
### 두 스레드의 동기화

만약 스레드 A의 작업이 끝나고, 이어서 스레드 B의 작업을 시작하려고 할 때 세마포어를 사용하여 동기화할 수 있다. 세마포어를 동기화 목적으로 사용할 때는 초기값을 0으로 설정한다. 아래의 코드를 살펴보자.

```swift
let semamphore = DispatchSemaphore(value: 0)

Thread.detachNewThread {
    print("1 - start")
    // task ..
    sleep(1)
    semaphore.signal()
    print("1 - end")
}

Thread.detachNewThread {
    semaphore.wait()
    print("2 - start")
    // task ..
    sleep(2)
    print("2 - end")
}
```

> 2022-05-12 07:49:53 +0000 1 - start   
> 2022-05-12 07:49:54 +0000 1 - end   
> 2022-05-12 07:49:54 +0000 2 - start   
> 2022-05-12 07:49:55 +0000 2 - end   

앞서 말했듯이 `wait()`은 세마포어의 값이 0이라면 `signal()`이 호출될 때까지 기다리므로, 첫 번째 스레드가 작업을 끝낸 뒤 `signal()`을 호출해야 두 번째 스레드의 작업이 시작할 수 있다. 이처럼 세마포어를 사용해 두 개의 스레드를 쉽게 동기화할 수 있다. 주의해야할 점은 첫 번째 스레드가 `signal()`을 호출하지 않는다면 두 번째 스레드의 작업을 시작할 수 없으므로 교착 상태가 발생할 수 있다.
