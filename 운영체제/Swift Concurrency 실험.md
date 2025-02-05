>> 아직 미완성..

### 동시성 코드 실험

이전에 Swift Concurrency를 학습하며 궁금했던 점 몇가지 들을 직접 테스트해보고 싶어서 한번 해봤습니당


### 1. Swift Concurrency 협력 스레드 풀

GCD의 문제점중 하나인 많은 작업을 비동기로 디스패치하면 thread explosion이 발생하고

컨텍스트 스위칭(context switching)이 많아지고 성능이 저하될 수 있습니다.

또한, 블록된 스레드가 어떤 자원을 잠그고(lock) 있을 때 데드락을 발생시킬 수도 있습니다.

Swift Concurrency는 구조화된 동시성과 협력적 스케줄러(cooperative scheduler)를 사용하여, 

기본적으로 시스템의 물리 코어 수(혹은 약간의 여유를 둔 수) 정도의 제한된 스레드 풀 내에서 작업이 실행되도록 합니다.

```swift
Task {
    await withTaskGroup(of: Void.self) { group in
        for _ in 0 ..< 1000 {
            group.addTask {
              let threadID = Thread.current
              print("Swift Concurrency Task running on: \(threadID)")
              // 약 0.1초간 바쁜 작업 (busy loop)
              let start = CFAbsoluteTimeGetCurrent()
              while CFAbsoluteTimeGetCurrent() - start < 0.1 {
                // SOME TASK      
                }
          }
        }
    }
}

let group = DispatchGroup()
  for _ in 0 ..< 1000 {
      group.enter()
      DispatchQueue.global().async {
          let thread = Thread.current
          print("GCD Task running on: \(thread)")
          let start = CFAbsoluteTimeGetCurrent()
          while CFAbsoluteTimeGetCurrent() - start < 0.1 { }
          group.leave()
      }
  }
group.wait()
```

결과
<img width="1400" alt="image" src="https://github.com/user-attachments/assets/1bfa2a70-91b7-42a8-ba39-ca2a49d63eb6" />
<img width="1791" alt="image" src="https://github.com/user-attachments/assets/f573d13a-7e89-4768-b532-9588fb7d6171" />
* 실제로 iPhone13의 기기의 코어 개수는 6개로 6개의 스레드에서 비동기 작업이 일어남 . (메인쓰레드와 사용하지 않는 쓰레드들도 존재하긴 하지만 실질적인 작업은 6개의 쓰레드에서 작동하므로 Context Switching이 줄어 듦)
* GCD에서도 여러 작업이 일어나도 Thread Explosion은 일어나지 않고 똑같이 6개의 쓰레드로 병렬적으로 돌아감,, 왜 ?

### 2. 우선순위 역전 
```swift
let high = DispatchQueue.global(qos: .userInteractive)
let low = DispatchQueue.global(qos: .utility)

let semaphore = DispatchSemaphore(value: 1)


low.async {
    semaphore.wait()
    defer { semaphore.signal() }

    print("Running long, lowest priority task")
    Thread.sleep(forTimeInterval: 5)
}

high.async {
    Thread.sleep(forTimeInterval: 2)
    semaphore.wait()
    defer { semaphore.signal() }

    print("High priority task is now running")
}

```
<img width="1026" alt="image" src="https://github.com/user-attachments/assets/aa054d9a-b1d5-4746-88ee-e2dd70a918f6" />

### 3. 중첩된 Async Await의 성능

많은 프로젝트에서는 모듈을 나누고 모듈마다 async await 콜을 중첩적으로 사용이 될 것같은데,

이는 성능상에 문제는 없을까 라는 고민이 생겼습니다.

왜냐하면 async/await는 함수는 await하는 순간 해당 함수가 suspend되어 현재 스레드를 블로킹하지 않고 런타임 스케줄러에게 제어를 넘깁니다.

만약 더 높은 우선순위의 작업들이 쓰레드에서 일어나고있다면, suspension과 resume 과정이 더 오래걸리는게 아닐까 ??

DispatchQueue의 callback 방식은 작업이 완료되는 즉시 클로저(또는 completion handler)를 호출하는데,

명시적으로 “작업 완료” 이벤트가 발생한 시점에 바로 후속 처리를 시작할 수 있는게 더 좋은게 아닐까 ?

 



