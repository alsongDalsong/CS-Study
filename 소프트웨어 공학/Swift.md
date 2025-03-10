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

