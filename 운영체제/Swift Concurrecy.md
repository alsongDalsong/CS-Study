## "스위프트는 왜 새로운 동시성 모델이 필요했는가?"

### 배경

애플이 비동기 프레임워크에 대해 마지막으로 큰 문제를 제기한 것은 2009년 맥 OS X 스노우 레오파드와 함께 GCD가 나왔을 때였다.

GCD는 첫날부터 동시성 및 비동기성 지원을 통해 2014년 Swift 출시를 도왔지만, 그 지원은 네이티브가 아니었고 Objective-C의 요구와 능력을 중심으로 설계되었습니다. 

스위프트는 언어를 위해 특별히 설계된 자체 메커니즘을 가질 때까지 그 동시성을 "빌렸다". 비동기 동시성 코드를 작성하기 위한 새로운 네이티브 모델을 도입한 Swift 5.5와 함께 모든 것이 변경되었습니다.

### asynchronous 및 concurrent의 이해

대부분의 코드들은 위에서 아래로 한 줄 씩 진행한다. 이는 주어진 코드가 언제 실행되는지 쉽게 결정 가능합니다.

코드가 동기식으로 실행된다면 실행이 순차적으로 발생하고, 동기식 컨텍스트에서 코드는 단일 CPU 코어에서 하나의 실행 스레드에서 실행된다. 예시로 단일 차선 도로의 자동차와 같이 상상할 수 있고, 한 대의 차량이 근무 중인 구급차처럼 우선순위가 더 높더라도 나머지 교통 체증을 무시하고 더 빠르게 운전할 수 없습니다.

<img width="960" alt="image" src="https://github.com/user-attachments/assets/5f392885-2eae-4c20-9f19-e23c7385a4dd" />

비동기 실행을 통해 프로그램이 하나의 스레드에서 어떤 순서로든 실행 가능 하지만 정확한 순서를 말하기가 어렵습니다. 신호등이 있는 도로와 교통 체증이 필요한 곳에서 운전하는 것과 마찬가지로, 계속할 차례가 될 때까지 기다리거나, 진행하기 위해 초록불이 켜질 때까지 멈춰야 합니다.

<img width="958" alt="image" src="https://github.com/user-attachments/assets/23d917c5-14de-47b6-a94d-66e19200d1b5" />

프로그램의 일부를 병렬로 실행하면 무수한 동시성 관련 문제를 야기한다. 예를 들어, 프로그램의 다른 부분이 서로의 실행을 차단하거나, 두 개 이상의 기능이 동시에 동일한 변수에 액세스하여 앱을 충돌시키거나 예기치 않게 앱의 상태를 손상시키는 data-race 발생 가능.

그러나 주의 깊게 사용하면 동시성은 여러 CPU 코어에서 서로 다른 기능을 동시에 실행함으로써 프로그램이 더 빠르게 실행되도록 도울 수 있으며, 다중 차선 고속도로에서 훨씬 빠르게 이동할 수 있는 것과 같습니다.

<img width="962" alt="image" src="https://github.com/user-attachments/assets/513d5074-b890-41ac-ab29-cab77cf5e8e0" />

Swift 5.5이전에는 GCD, Operation, Thread, C 기반 pthread 라이브러리와 직접 상호 작용하는 오래된 API를 사용했습니다.

### GCD에서 겪었던 문제들

1. **스레드 폭발(Thread explosion)**
    - 한꺼번에 많은 작업이 들어오면 무제한으로 스레드를 늘릴 수 있어, 오히려 문맥 전환 비용이 커지고 성능 저하
2. **우선순위 역전(Priority inversion)**
    - 하나의 직렬 큐에 낮은 우선순위 작업이 깔려 있는데, 높은 우선순위 작업도 같이 줄 서 있어 제대로 먼저 실행되지 못함
3. **실행 계층 구조 부재**
    - 비동기 코드를 취소(cancel)하거나, 작업 간 트리 구조를 만들기 어려움 → 복잡한 로직이 필요한 경우 직접 구현해야 함

### Mordern Swift Concurrency Model 4가지 장점들

1. 협동 스레드 풀

새로운 Concurrency 모델은 사용 가능한 CPU 코어 수를 초과하지 않도록 스레드 풀을 관리합니다. 런타임은 스레드를 생성하고 파괴하거나 고가의 스레드 전환을 지속적으로 수행할 필요가 없습니다. 대신, 당신의 코드는 풀의 사용 가능한 스레드에서 매우 빠르게 일시 중지되고, 나중에 재개될 수 있습니다.

(Task를 사용해서 비동기 작업을 추가할 때 최대한 같은 쓰레드를 재사용 하려고 시도)

2. async/await

Swift의 새로운 async/await 구문은 컴파일러와 런타임에게 “이 코드는 한 번 이상 실행을 일시 중지하고 재개할 수 있음”을 알려줍니다. 런타임은 이것을 매끄럽게 처리하므로 스레드와 코어에 대해 걱정할 필요가 없습니다. 새로운 언어 구문은 escape closure같은 걸 사용할 필요가 없기 때문에 자기 또는 다른 변수를 약하게 또는 강하게 캡처할 필요성을 제거합니다.

3. 구조화된 동시성.

각 비동기 작업은 이제 상위 작업과 주어진 실행 우선 순위가 있는 계층 구조의 일부입니다. 이 계층은 부모가 취소될 때 런타임이 모든 자식 작업을 취소할 수 있도록 합니다. 게다가, 그것은 런타임이 부모가 완료하기 전에 모든 자식이 완료될 때까지 기다릴 수 있게 해준다. 

이 계층은 계층 구조에서 우선 순위가 낮은 작업보다 우선 순위가 높은 작업이 먼저 실행되는 큰 이점과 더 명백한 결과를 제공합니다.

4. 문맥 인식 코드 편집.

컴파일러는 주어진 코드가 비동기적으로 실행될 수 있는지 여부를 추적합니다. 만약 그렇다면, 공유 상태를 변경하는 것과 같이 잠재적으로 안전하지 않은 코드를 작성할 수 없습니다. 이러한 높은 수준의 컴파일러 인식은 컴파일 시 상태에 대한 동기 및 비동기 액세스를 구분하고 안전하지 않은 코드를 작성하기 어렵게 하여 실수로 데이터가 손상되는 것을 방지하는 행위자와 같은 정교한 새로운 기능을 가능하게 합니다. 

Swift 언어의 철학이 살짝 떠올랐습니다 .

- Safe(안정성), Fast(신속성), Expressive(표현성)

책을 읽고 여러 비동기 API들에 대해서 배우게 되었습니다.

- async/await과 async let (동시에 여러 비동기 작업)
- AsyncSequence(비동기 결과를 스위프트 시퀀스를 반복하는 것처럼 간단하게 소비)
- AsyncStream(비동기 시퀀스에 대한 완전한 제어)
- Continuation(기존 비동기 API들과 어떻게 연동이 가능한지?)
- TaskGroup(작업을 병렬로 실행하여 장치에서 여러 스레드와 CPU 코어 효율적으로 사용)
- Testing Asynchronous Code(비동기 코드 테스팅 방법,, Task Timeout 사용)
- Actor
- Global Actor
- Actor in Distributed System (MultiPeer Connectivity에서 Actor를 사용하여 Task 전달)

이 비동기 API를 사용하는 방법들에 대해서의 추상화 레벨로 가르치고 있어서 내부 동작 방식같은걸 더 깊게 알아보려면 직접 알아보는게 더 좋을 것 같습니다.

사실 가장 원했던 주제인 Actor에 대해서 알아봅시다.

위에서 설명한 Concurrency API들을 사용하면 같은 메모리에 동시 접근할 수 있는 문제, 즉 data race가 발생할 수 있는 문제를 해결하기 위해 나온게 Actor,  이전 에는 스레드 안전으로 표시하는 방법이 없었고, 이게 safe한지 알기위해서는 apple document에 의존해야만 합니다.

알아볼 내용 키워드들은 `Actor`와 `seiral Excutor` , `@MainActor` `@Sendable`   입니다. `Global Actor`

### Actor?

```swift
class Counter {
	private var count = 0
	
	func increment() {
		count += 1
	}
}
```

카운터 코드를 볼 때 특별히 눈에 띄는 것은 없습니다. 

그럼에도 불구하고, 병렬로 실행되는 두 개의 스레드가 모두 Counter.increment()를 호출하면, 

카운트가 정확히 2 증가하지 않을 수 있습니다. 

더 나쁜 것은, Counter.increment()에 대한 두 번의 호출이 정확히 같은 순간에 발생하면 앱이 충돌할 것입니다.

더욱 걱정스러운 것은 디버깅을 위해 앱을 컴파일할 때 충돌이 거의 발생하지 않는다는 것입니다. 

동시 액세스로부터 공유된 변경 가능한 상태를 보호하기 위한 사전 조치를 취하지 않는 코드는 본질적으로 스레드 안전하지 않다고 말할 수 있습니다.

Swift 5.5 이전에 개발자들은 공유 상태에 대한 독점 액세스를 보장하기 위해 Locks 또는 serial dispatch queues 대기열을 사용했습니다. 

lock을 사용하면 스레드가 공유 리소스에 대한 액세스를 잠그고, 

다른 스레드는 동일한 리소스를 읽거나 쓰기 전에 잠금이 해제될 때까지 기다려야 합니다.

효과적으로, 스레드는 리소스에 대한 독점 액세스를 보장하기 위해 서로를 잠급니다

<img width="959" alt="image" src="https://github.com/user-attachments/assets/987116f7-61cf-48f5-8cfc-0eca6296297d" />

Os_unfair_lock과 같은 Lock API를 사용하는 코드는 잘 작성되면 상당히 빠르고 안전합니다.

```swift
class Counter {
	private var lock = os_unfair_lock_s()
	private var count = 0
	
	func increment() {
		os_unfair_lock_lock(&lock)
		count += 1
		os_unfair_lock_unlock(&lock)
	}
}
```

코드는 꽤 간단해 보입니다.

 lock은 카운트를 보호하기 위한 실행 가능한 해결책이다.  하지만 .. 이 API를 사용하는 개발자로서, Counter.increment()를 호출하는 것이 스레드 안전인지 아닌지 알 수 있을까요? 게다가, 어떻게 컴파일러는 코드가 스레드 안전하다는 것을 알 수 있을까요,,?

만약 당신이 코드에 접근할 수 없거나, 그것을 철저히 읽을 자유 시간이 없다면, 그것이 정말로 안전한지 알 수 있는 방법이 없습니다.

엑터가 이를 해결해줄 수 있습니다.

액터는 열거형, 구조, 클래스 등과 같은 프로그래밍 유형입니다. 좀 더 구체적으로 말하자면, 클래스와 같은 참조 유형입니다.  클래스를 액터로 대체하여 스레드 안전을 만듭니다.

```swift
Actor Counter {
	private var count = 0
	
	func increment() {
		count += 1
	}
}
```

액터는 동시 계산을 위한 기존에 잘 확립된 모델이다. 위키피디아(https://en.wikipedia.org/wiki/Actor_model) 기사에서 그들에 대해 자세히 읽을 수 있습니다.

<img width="959" alt="image" src="https://github.com/user-attachments/assets/abdb8d78-7b71-4c18-8308-70dff49e5946" />

액터는 내부 상태의 안전을 보장할 수 있는 몇 가지 기본 규칙에 따라 행동합니다.  

가장 핵심은 “엑터는 자신의 개인 상태를 수정할 수 있지만 메시징을 통해 간접적으로만 서로에게 영향을 미칠 수 있습니다.”

다른 구현은 언어에 따라 다르므로, Actor들이 특히 Swift에서 어떻게 작동하는지에 대해 알아봅시다.

모든 액터가 준수하는 액터 프로토콜을 찾아보면, 단 하나의 요구 사항이 있다는 것을 알 수 있습니다. 

<img width="956" alt="image" src="https://github.com/user-attachments/assets/d2dbc658-3fab-4ed0-8fca-30c8479e90ce" />

모든 Actor는 Actor의 상태에 대한 접근을 직렬화하여 처리하는 unownedExecutor라는 속성을 가져야 합니다.

런타임이 관리하는 serial executor라고 불리는 특수 타입은 액터의 members에 대한 모든 호출을 동기화합니다. 직렬 실행자는 GCD의 직렬 디스패치 대기열과 마찬가지로 작업을 차례로 실행합니다. 이렇게 함으로써, 그것은 동시 접근으로부터 의 actor 상태를 보호합니다.

<img width="948" alt="image" src="https://github.com/user-attachments/assets/45931137-4779-43cf-aa93-44ff8e89fb62" />

하지만 데이터 경쟁의 진짜 원인은 어떻습니까?  다른 타입이 동시에 여러 스레드에서 당신의 Actor를 불러 충돌을 일으키지 않을 것이라고 어떻게 보장하기 위해 스위프트 컴파일러와 특별한 거래를 했다. 

다른 타입의 Actor에 대한 액세스는 자동으로 비동기적으로 수행되며 Actor의 serial executor에서 예약됩니다.

이것은 state isolation layer이라고 불리고

<img width="961" alt="image" src="https://github.com/user-attachments/assets/d83a400b-52ab-4583-ba79-76be08234036" />

state isolation layer은 모든 state mutation이 스레드 안전임을 보장합니다. 

Actor 자체가 컴파일러 및 런타임의 소비자에게 스레드 안전을 보장합니다.

### Actor Model

대부분 Actor들은 전형적인 Class 행동을 많이 나타내기 때문에, 대체 가능함. 하지만 컴파일러는 Actor에 대한 큐칙을 따르지만 마법처럼 문제가 해결하지는 않고 동시에 컨텍스트가 안전하게 작동하도록 코드를 변경하는 방법을 제안합니다.

<img width="961" alt="image" src="https://github.com/user-attachments/assets/a85c0c3f-e6a8-459b-8ab0-3f3dd7177729" />

컴파일러는 코드에서 안전하지 않은 스레드 접근을 만들지 않도록 오류를 낸다.

“Actor-isolated property ‘verifiedCount’ can not be mutated from a Sendable closure”

 Sendable에 대해 배우게 될 것입니다.

현재로서는, 직접  "외부"에서 액터 상태를 업데이트할 수 없기 때문에 오류가 발생한다는 것만 알고있으면 됩니다. 즉 Actor의 Serial Executor에게 국한되지 않는 모든 코드는 외부 접근입니다.

이 경우 TaskGroup과 같은 다른 타입의 호출에 동시 작업을 포함한다는 것을 의미합니다.

이를 해결하기 위해서는 Actor내부에서 property에 접근하는 코드를 추출한 다음, 

비동기적으로 호출합니다. 이를 통해 Actor는 해당 메서드에 대한 호출을 직렬화할 수 있습니다.

<img width="964" alt="image" src="https://github.com/user-attachments/assets/7d726340-c69c-41e2-b4ef-05512229a4bc" />

새 코드는 serial하게 increaseVerifiedCount() 호출을 할 것입니다;

이것은 당신이 공유 상태를 안전하게 변경하도록 보장합니다. 

하지만,, 일단 그 오류를 해결하면, 당신은 여전히 많은 컴파일러 오류가 발생합니다.

이제  Main Actor에서 그 속성에 접근할 수 없습니다. .. 사실 간단하게 해결 가능함.

해당 모델에 접근할 때 @Mainactor 를 붙이고 

```swift
@Publisherd @MainActor private(set) var imageFeed: [ImageFile] = []
await MainActor.run {
	imageFeed = list
}
```

<img width="957" alt="image" src="https://github.com/user-attachments/assets/8f9816b9-9c16-45d5-899d-44bc56694a87" />

### Sendable 알아보기

Sendable은 주어진 값이 동시 코드에서 사용하기에 안전하다는 것을 나타내는 프로토콜입니다. 

이 프로토콜에는 요구 사항이 없다는 점에 유의하십시오. 스레드 간에 사용하기에 안전하다고 알고 있는 유형에 주석을 달 때만 사용합니다.

상속 섹션으로 조금 아래로 스크롤하면 Sendable에서 몇 가지 프로토콜이 상속되는 것을 볼 수 있습니다. 예를 들어, 액터 프로토콜은 Sendable입니다

따라서 액터 인스턴스는 동시 코드에서 사용하기에 안전합니다. 

다음 섹션인  Conforming Types 에서는 Swift의 많은 유형이 기본적으로 sendable한 것을 볼 수 있습니다. 

예를 들어 Bool, Double, Int, StaticString, UnsafePointer는 모두 동시 코드에서 사용하기에 안전합니다.  일반적으로 value types은  동일한 개체에 대한 공유 참조를 실수로 변경하는 것을 방지하기 때문에 동시 코드에서 사용하기에 안전합니다. 

클래스는 일반적으로 참조를 통해 메모리에서 동일한 인스턴스를 변경할 수 있기 때문에 Sendable 할 수 없습니다. (Immutable 을 보장할 수 있어야함. 모든 프로퍼티들이 let으로 선언되어있고, 참조하는 타입들도 모두 Sendable이면 가능) @unchecked Sendable로,, 개발자가 스레드 안전성을 보장가능하다면 할수는 있음.)

Task에 init도 operation의 클로저도 Sendable을 만족해야함.

<img width="956" alt="image" src="https://github.com/user-attachments/assets/f84da6ca-638f-4b79-a705-be7117f11c65" />

### Global Actor

<img width="959" alt="image" src="https://github.com/user-attachments/assets/43beaef8-25e1-4773-a784-d79f72402c37" />

GlobalActor에는 단 하나의 요구 사항이 있습니다

귀하가 전 세계적으로 액세스할 수 있도록 하는 액터 인스턴스를 노출하는 공유라는 정적 속성(싱글톤 패턴 처럼 static let shared)이 있어야 합니다.
