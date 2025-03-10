# Swift

## Swift 언어의 탄생

<img width="600" src="https://github.com/user-attachments/assets/81767771-3079-421b-90f7-d10f731b9f3a" />

- Swift는 [2014년 WWDC](https://www.youtube.com/watch?v=w87fOAG8fjk)에서 공개되었습니다. 타임라인은 1시간 45분입니다.
- 2010년 크리스 라트너라는 분이 만들었고, 2015년 12월에 [오픈소스](https://github.com/swiftlang/swift)가 되었습니다.

| WWDC | 캡쳐 |
|--|--|
|<img width="600" src="https://github.com/user-attachments/assets/103aa141-0c87-45af-9bf9-84e8a11863a4" />|<img width="600" src="https://github.com/user-attachments/assets/82be5cc5-7c44-483f-b96c-763eec57217c" />|
|<img width="600" src="https://github.com/user-attachments/assets/358d65bd-4104-4854-93fc-b45fa1cb26d0" />|<img width="600" src="https://github.com/user-attachments/assets/00e88f2f-dd75-412b-a557-f79c26b329f0" />|
|<img width="600" src="https://github.com/user-attachments/assets/47d57297-b10b-4957-a88f-1409314fd506" />|<img width="600" src="https://github.com/user-attachments/assets/c59492e0-d597-4c3f-be2d-4703191c72dd" />|

- [크리스 라트너의 홈페이지](https://nondot.org/sabre/)를 보면 다양한 언어에서 아이디어를 얻어 만들어진 현대적 언어라고 볼 수 있습니다.

<img width="1237" src="https://github.com/user-attachments/assets/80efd2d8-3696-45cb-8983-66cbd22c395a" />

### Swift 언어의 탄생 이유

- **Objective-C** 의 경우 C언어를 기반으로 스몰토크 스타일의 메세지 구문을 추가한 객체 지향 언어입니다. [위키피디아](https://en.wikipedia.org/wiki/Objective-C)
- **Objective-C** 는 메서드를 호출하는 것이 아닌 메세지를 전달하는 방식으로 작동한다고 합니다. `obj.doSomething(); -> [obj doSomething];`

<img width="400" src="https://github.com/user-attachments/assets/d251761c-9daf-4c39-93d5-f470309aa971" />

- 스위프트의 경우 클로저, 제네릭, 타입에 대한 안정성, 메모리 관리, 복잡한 비동기 처리를 쉽게 하기 위해 애플은 개발자들이 조금더 쉽게 프로그래밍을(앱을 만들기를)원했고, 그렇게 해서 Swift가 탄생하게 되었다. 정도로 생각하면 좋을 것 같습니다.

<img width="878" alt="스크린샷 2025-03-10 19 44 35" src="https://github.com/user-attachments/assets/7978dcad-1f56-4d5e-a8c0-20f6edfb41c6" />

- 패러다임의 변화도 돋보입니다. Objective-C의 경우 Reflective(반영형), 클래스, 객체 지향 프로그래밍이고 스위프트의 경우 프로토콜, 객체 지향, 함수형, 명령형, 선언형 등등 여러가지 패러다임을 구현할 수 있다는 점이 큰 장점인 것 같습니다.

<img width="800" src="https://github.com/user-attachments/assets/0502f64b-2966-4b02-b0ab-b9632091a993" />
<img width="800" src="https://github.com/user-attachments/assets/cdfc0351-0023-423b-93b8-53e0ace36db9" />

- 반영형의 경우 궁금하실 수도 있는데 런타임에 메서드의 구현을 변경 (Method Swizzling) 하는 경우가 반영형의 예시로 볼 수 있겠습니다.
- 당연히 동적으로 변경되기 때문에 안정성에서 위험요소가 있겠죠? 기존 동작하는 함수를 바꿔버리는 것이니까요...

```Swift
import UIKit

extension UIViewController {
    // 스위즐링을 적용할 메서드를 정의
    @objc func swizzled_viewDidAppear(_ animated: Bool) {
        // 원래 동작을 유지하기 위해 기존 메서드를 호출
        self.swizzled_viewDidAppear(animated)
        
        // 추가적인 동작 (예: 로그 출력)
        print("\(self) 화면이 나타났습니다!")
    }

    // 스위즐링 적용 메서드
    static func swizzleViewDidAppear() {
        let originalSelector = #selector(viewDidAppear(_:))
        let swizzledSelector = #selector(swizzled_viewDidAppear(_:))

        guard let originalMethod = class_getInstanceMethod(self, originalSelector),
              let swizzledMethod = class_getInstanceMethod(self, swizzledSelector) else {
            return
        }

        // 메서드 교체 (Swizzling)
        method_exchangeImplementations(originalMethod, swizzledMethod)
    }
}
```

## Swift 언어의 특징

- 만약 누가 Swift의 특징이 뭐야? 라고 물어보신다면 뭐라고 답하실건가요?
- 저의 경우 가장 먼저 생각나는 특징은 Swift로 코드를 작성하면 `macOS`, `iOS`, `iPadOS`, `watchOS`, `Widget` 등 애플 기기에서 사용할 수 있는 앱을 만들 수 있다는 점을 가장 먼저 생각할 것 같습니다.
- 하지만 우리는 개발자니까... 언어 자체의 특징에 대해서도 알아봐야 겠죠...?

- [공식 홈페이지](https://developer.apple.com/kr/swift/)에서는 이렇게 소개하고 있습니다!

<img width="800" src="https://github.com/user-attachments/assets/3ed7a706-be3a-4aaf-948b-f2a2e8241aeb" />

- [swift.org](https://www.swift.org/)에서 볼 수 있는 키워드는 `fast`, `modern`, `safe`, `joy`라는 단어가 눈에 띕니다.

<img width="800" src="https://github.com/user-attachments/assets/aaf3d8ed-c844-4d56-9b20-524887ef3d3a" />

### Fast

- LLVM(Low Level Virtual Machine) 기반의 고성능 컴파일러를 사용하여 정적 타입 언어의 특징을 가지고 있어 컴파일 시점에 많은 최적화가 적용된다고 합니다.
- ARC를 활용하여 수동으로 메모리 관리를 할 필요 없습니다. GC 기반 언어보다 오버헤드가 적다고 하구요.
- struct가 Stack에 저장되어 Heap보다 빠르게 접근가능하고, COW 기법을 활용하여 불필요한 복사를 최소화 한다고 합니다.

### Modern

- 위키피디아에서 확인할 수 있듯이 다양한 프로그래밍 패러다임을 구현할 수 있다는 장점이 있습니다.
- 특히 SwiftUI에서는 선언적 프로그래밍, 전체적으로 프로토콜 지향 프로그래밍 패러다임을 따를 수 있습니다.
- async/await, actor 등 동기적 프로그래밍에서 중요한 이슈인 Mutable Shared State 관리를 효율적으로 가능하게 합니다.

### Safe

- Swift는 컴파일 단계에서 타입을 확정시키기 때문에 타입에 정말 엄격합니다. 이로 인해 런타임 단계에서 발생할 수 있는 오류를 방지할 수 있습니다. 또한 타입 추론을 통해 쉽게 변수를 선언할 수 있게 합니다.
- ARC 메모리 관리로 개발자가 메모리 관리를 해줄 필요가 없는 것도 안정성에 들어갈 수 있겠지만 이의 경우 unowned, weak를 잘 사용하여 구할 수 있어야 하겠습니다.

### Joy

<img width="300" src="https://github.com/user-attachments/assets/a87d5185-4ce8-489d-bcc9-53d12a2918bc" />

- 즐거우신가요?
- 아래처럼 문장을 작성하듯 코드를 작성할 수 있다는 점은 정말 큰 매력으로 생각됩니다. (개인적)
- 세미콜론 생략, Playground를 통한 코딩, WWDC에서의 공식 강의, 공식 홈페이지에서 다양한 코드 샘플 등 확실히 개발자가 편하고 즐겁게(?) 코딩할 수 있도록 많은 노력을 하고 있는 것 같습니다.

```swift
func greet(to name: String) {
    print("Hello, \(name)!")
}

greet(to: "Swift")
```
