# iOS에서 앱을 실행하면 무슨 일이 벌어지는가?
iOS의 앱은 Objective-C (C언어 기반) 기반에서 돌아가기 때문에 앱은 Main 메서드에서 시작합니다. 다만, 다른 C언어 기반 언어와는 다르게 iOS는 핵심 라이브러리인 UIKit이 Main 메서드를 관리하여 앱 개발자들이 직접 Main을 구현할 필요는 없습니다.

UIKit은 Main 메서드를 다루는 과정에서 `UIApplicationMain` 메서드를 실행합니다. 이 메서드를 통해 `UIApplication` 객체가 생성되는데, 이 객체를 통해 앱 개발자는 필요 시 앱의 실행에 부분적으로는 관여할 수 있습니다.

## UIApplication
- 정의: Creates the application object and the application delegate and sets up the event cycle.

UIApplication 객체는 싱글톤 형태로 생성되어 `UIApplication.shared` 형태로 앱 전역에서 사용할 수 있습니다. 이 객체의 가장 중요한 역할은 사용자의 이벤트(터치, 리모트 컨트롤, 가속도계, 자이로스코프 등)에 반응하여 앱의 초기 설정(Routing)을 하는 것입니다. 예를 들어, 앱이 Background에 진입한 상태에서 추가적인 작업을 할 수 있도록 만들어주거나, 푸쉬 알림을 받았을 때 어떤 방식으로 이를 처리할지 등에 대한 것을 다룹니다.

## Main RunLoop
- 정의: 사용자가 일으키는 이벤트를 처리하는 프로세스

UIApplication 객체는 앱이 실행될 때 Main RunLoop를 실행하고, 이를 View와 관련된 이벤트나 업데이트에 활용합니다. 여기서 Main RunLoop는 View와 관련되어 있기 떄문에 메인 쓰레드에서 실행됩니다.

![image](https://github.com/user-attachments/assets/6ebcd884-7b7a-4df4-b738-88330b2a9510)

사용자가 일으키는 이벤트의 처리 과정은 다음과 같이 정리할 수 있습니다.

1. 사용자가 이벤트 발생
2. 시스템을 통해 이벤트 생성
3. UIKit을 통해 생성된 port로 해당 이벤트가 앱으로 전달
4. 이벤트는 앱 내부적으로 Queue의 형태로 정리되고, Main RunLoop에 하나씩 매핑
5. UIApplication 객체는 이때 가장 먼저 이벤드를 받아 어느 Core Object가 실행되어야 하는지 결정

## Application LifeCycle
애플에서 정의하는 앱의 상태는 크게 5 가지로 구분됩니다.

- 앱의 상태(State):
  - Not Running: 앱이 실행되지 않은 상태
  - Foreground: 앱의 실행화면(Scene)을 사용자에게 보여주는 상태(영역), 시스템 리소스에 높은 우선순위를 가지며 운영체제는 Foreground 앱을 위해 Background 앱을 종료시키기도 함
    - Inactive: 앱이 foreground에 있지만, 이벤트를 받지 않는 상태, 앱의 상태 전환 과정에서 잠깐 머무는 단계 혹은 앱 사용 중 전화 또는 문자와 같은 외부 이벤트가 접근하여 앱이 잠시 멈추는 단계
    - Active: 앱이 foreground에서 활성 상태로, 사용자 이벤트를 처리하는 상태, 사용자가 앱을 100% 컨트롤할 수 있는 단계
  - Background: 앱이 background에서 실행 중인 상태, Suspended 되기 전 거치는 단계 (단, 애플은 기본적으로 보안 등의 이유로 Background 영역에서의 앱 작동을 허용하지 않지만, 일부 음악 앱 등에서는 가능), 가능한 적은 메모리 공간을 사용해야하며 대부분 바로 Suspended 상태가 됨
  - Suspended: 앱이 background에서 실행을 중지하고 메모리에 유지된 상태, 시스템이 임의로 만든 대기 단계

![image](https://github.com/user-attachments/assets/95efc3a1-b499-47ec-83bd-91496f1c2f80)

- 참고로,
  1. Background 상태에서는 일부 필요한 추가 작업을 수행할 수 있습니다. 또한, Background 상태에서 앱을 실행하면 InActive 상태를 거치지 않고 앱이 실행됩니다. (앱이 재시작 되지 않는다면 Background 상태 였던 앱)
  2. Suspended에서 Not Running 상태로 진입할 때는 아무런 알림을 받을 수 없지만, Background에서 Suspended로 진입할 때는 `willTerminate` 메소드가 실행됩니다. 하지만, 기기를 재부팅하는 경우에는 실행되지 않습니다.

## View LifeCycle
- UIViewController의 생명주기 메서드:
  - `loadView()`: 뷰 컨트롤러의 뷰를 생성 및 초기화합니다.​
  - `viewDidLoad()`: 뷰가 메모리에 로드된 후 호출됩니다.​
  - `viewWillAppear()`: 뷰가 화면에 나타나기 직전에 호출됩니다.​
  - `viewDidAppear()`: 뷰가 화면에 나타난 후 호출됩니다.​
  - `viewWillDisappear()`: 뷰가 화면에서 사라지기 직전에 호출됩니다.​
  - `viewDidDisappear()`: 뷰가 화면에서 사라진 후 호출됩니다.

![image](https://github.com/user-attachments/assets/18542504-4f5d-48a1-adf3-1a62483f2c63)

## App Delegate
### iOS 12 and earlier
- 역할: 앱의 전반적인 동작을 관리하며, 앱의 생명주기 이벤트와 시스템 이벤트를 처리합니다. (iOS 13 이전)

앱의 각 상태에 접근하기 위해 사용되는 파일이 AppDelegate.swift입니다. @UIApplicationMain이라는 annotation을 통해 앱에서 이를 앱과 시스템을 연결하기 위한 파일로 인식하게 됩니다.

AppDelegate 객체는 UIResponder, UIApplicationDelegate를 상속 및 참조하고 있습니다. 여기서 `UIResponder`는 앱에서 발생하는 이벤트들을 담고있는 추상형 인터페이스 객체로 View와 사용자의 이벤트 간의 연결을 관리하는 역할을 합니다. `UIApplicationDelegate`는 UIApplication 객체의 작업에 개발자가 접근할 수 있도록 하는 메서드들을 담고 있습니다.

![image](https://github.com/user-attachments/assets/e8c5a3c3-7b92-4cc9-aa63-20bf5b1cbc38)

  - Process LifeCycle: 프로세스의 실행 및 종료를 파악
  - UI LifeCycle: UI의 State를 파악

> "앱은 오직 하나의 프로세스와 그에 맞는 하나의 UI만 가지기 때문"

- 주요 메서드:
  - `application(_:didFinishLaunchingWithOptions:)`: 앱이 시작될 때 호출됩니다.​
  - `applicationWillResignActive(_)`: 앱이 활성 상태에서 벗어날 때 호출됩니다.​
  - `applicationDidEnterBackground(_)`: 앱이 백그라운드로 진입할 때 호출됩니다.​
  - `applicationWillEnterForeground(_)`: 앱이 포그라운드로 복귀할 때 호출됩니다.​
  - `applicationDidBecomeActive(_)`: 앱이 활성 상태로 전환될 때 호출됩니다.​
  - `applicationWillTerminate(_`): 앱이 종료될 때 호출됩니다.

## Scene Delegate
- 역할: iOS 13 이후부터 멀티 윈도우 기능이 도입되면서 UI 관련 생명주기 관리가 Scene Delegate로 이전, 기존의 App Delegate는 주로 앱의 전반적인 설정 및 앱 레벨 이벤트를 처리하게 되었습니다.

Window의 개념이 Scene으로 대체되고 하나의 앱이 여러개의 Scene을 가질 수 있게 되었습니다. 이에 따라 UI의 상태를 알 수 있는 UI LifeCycle에 대한 책임을 SceneDelegate로 이전하게 되었습니다.

![image](https://github.com/user-attachments/assets/a2c48d41-6367-4b71-b42c-9b291fdf1a61)

대신 App Delegate에는 Session LifeCycle에 대한 역할이 추가되며 Scene Session이 생성되거나 삭제될 때 App Delegate에 알리게 됩니다. 여기서 Scene Session은 앱에서 생성한 모든 Scene의 정보를 관리하게 됩니다.

![image](https://github.com/user-attachments/assets/03b662d7-4938-461f-8111-10bad9acbfd5)

이에 따라 App Delegate의 역할은 다음과 같이 변화하고

  1) 앱의 가장 중요한 데이터 구조를 초기화
  2) 앱의 scene을 환경 설정(Configuration)
  3) 앱 밖에서 발생한 알림(배터리 부족, 다운로드 완료 등)에 대응
  4) 특정한 scene, views, view controllers에 한정되지 않고 앱 자체를 타겟하는 이벤트에 대응
  5) 애플 푸시 알림 서비스와 같이 실행시 요구되는 모든 서비스를 등록

![image](https://github.com/user-attachments/assets/6c616939-f298-4af9-8eac-4b4526828178)

Scene Delegate는 UI LifeCycle에 대한 역할을 담당하게 됩니다. 역할이 분리된 대신 App Delegate에서 Scene Session을 통해 Scene에 대한 정보를 Scene Delegate로 부터 업데이트 받기 됩니다.

- 주요 메서드:
  - `scene(_:willConnectTo:options:)`: 새로운 씬이 연결될 때 호출됩니다.
  - `sceneDidDisconnect(_)`: 씬이 시스템에 의해 해제될 때 호출됩니다.
  - `sceneDidBecomeActive(_)`:
  - `sceneWillResignActive(_)`:
  - `sceneDidEnterBackground(_)`:
  - `sceneWillEnterForeground(_)`:

### Scene
- iOS 13 부터는 ‘window’의 개념이 ‘scene’으로 대체
- UIKit은 UIWindowScene 객체를 사용하는 앱 UI의 각 인스턴스를 관리
- Scene에는 UI 하나의 인스턴스를 나타내는 windows와 view controllers가 들어있음
  - 또한 각 scene에 해당하는 UIWindowSceneDelegate 객체를 가지고 있음
    - 이 객체는 UIKit과 앱 간의 상호 작용을 조정하는데 사용
- Scene들은 같은 메모리와 앱 프로세스 공간을 공유하면서 서로 동시에 실행
- 결과적으로 하나의 앱은 여러 scene와 scene delegate 객체를 동시에 활성화 할 수 있음

### Scene Session
- UISceneSession 객체는 scene 고유의 런타임 인스턴스를 관리
- 사용자가 앱에 새로운 scene을 추가하거나 프로그래밍적으로 scene을 요청하면, 시스템은 그 scene을 추적하는 session 객체를 생성
- Session에는 고유한 식별자와 scene의 구성 세부사항(configuration details)가 들어있음
- UIKit은 session 정보를 그 scene 자체의 생애(life time)동안 유지하고 app switcher에서 사용자가 그 scene을 클로징하는 것에 대응하여 그 session을 파괴
- session 객체는 직접 생성하지 않고 UIKit이 앱의 사용자 인터페이스에 대응하여 생성
- 또한, application(:configurationForConnecting:option:) -> UISceneConfiguration 과 application(:didDiscardSceneSessions:) 이 두 메소드를 통해 UIKit에 새로운 scene과 session을 프로그래밍적 방식으로 생성할 수 있음

# App LifeCycle의 실행 시점에 따른 메서드 호출은 어떻게 되는가?

![image](https://github.com/user-attachments/assets/a841dd90-18e8-4503-9cfc-97426a071b73)

### 🔵 파란색 1번: 앱이 처음 실행 될 때

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        return true
}
```

Not Running 상태의 초기 앱을 실행할 때 사용자에게 화면이 보일 수 있도록 Foreground 영역에 접근하는 메서드로 앱 화면이 보이기 직전에 호출됩니다.

### 🔵 파란색 2번: 새로운 화면(Scene)을 생성할 때

```swift
func application(_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration {
        // Called when a new scene session is being created.
        // Use this method to select a configuration to create the new scene with.
        return UISceneConfiguration(name: "Default Configuration", sessionRole: connectingSceneSession.role)
}
```

iOS 13 이후부터 하나의 디바이스(iPad)에서 새로운 화면(여러 화면)을 생성할 때 호출됩니다.

### 🔵 파란색 3번: 화면의 세션이 제거되기 직전

```swift
func application(_ application: UIApplication, didDiscardSceneSessions sceneSessions: Set<UISceneSession>) {
        // Called when the user discards a scene session.
        // If any sessions were discarded while the application was not running, this will be called shortly after application:didFinishLaunchingWithOptions.
        // Use this method to release any resources that were specific to the discarded scenes, as they will not return.
}
```

화면 + 세션이 영구적으로 메모리에서 해제될 때만 해당 메서드가 호출됩니다.

### 🔴 빨간색 0번: 화면이 앱에 추가될 때

```swift
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        // Use this method to optionally configure and attach the UIWindow `window` to the provided UIWindowScene `scene`.
        // If using a storyboard, the `window` property will automatically be initialized and attached to the scene.
        // This delegate does not imply the connecting scene or session are new (see `application:configurationForConnectingSceneSession` instead).
        guard let _ = (scene as? UIWindowScene) else { return }
}
```

그림에는 나와있지 않지만 화면이 해당 앱에 추가(연결)될 때 호출됩니다. 초기 화면을 지정하거나 Window를 설정합니다.

### 🔴 빨간색 1번: 화면이 InActive에서 Active로 전환될 때

```swift
func sceneDidBecomeActive(_ scene: UIScene) {
        // Called when the scene has moved from an inactive state to an active state.
        // Use this method to restart any tasks that were paused (or not yet started) when the scene was inactive.
}
```

앱을 처음 실행할 때 해당 앱을 100% 조작할 수 있게 하는 메서드이며 Foreground에서만 작동합니다.

### 🔴 빨간색 2번: 화면이 Active에서 InActive로 전환될 때

```swift
func sceneWillResignActive(_ scene: UIScene) {
        // Called when the scene will move from an active state to an inactive state.
        // This may occur due to temporary interruptions (ex. an incoming phone call).
}
```

앱 사용중 전화 또는 문자등의 외부 이벤트가 발생하면 잠사 InActive 상태가 되며 해당 메서드가 호출됩니다. 마찬가지로 Foreground에서만 동작합니다.

### 🔴 빨간색 3번: 화면이 InActive에서 Background로 전환될 떄

```swift
func sceneDidEnterBackground(_ scene: UIScene) {
        // Called as the scene transitions from the foreground to the background.
        // Use this method to save data, release shared resources, and store enough scene-specific state information
        // to restore the scene back to its current state.
}
```

### 🔴 빨간색 4번: 화면이 Background에서 Foreground로 전환될 때

```swift
func sceneWillEnterForeground(_ scene: UIScene) {
        // Called as the scene transitions from the background to the foreground.
        // Use this method to undo the changes made on entering the background.
}
```

### 🔴 빨간색 5번: 화면이 메모리에서 해제될 때

```swift
func sceneDidDisconnect(_ scene: UIScene) {
        // Called as the scene is being released by the system.
        // This occurs shortly after the scene enters the background, or when its session is discarded.
        // Release any resources associated with this scene that can be re-created the next time the scene connects.
        // The scene may re-connect later, as its session was not necessarily discarded (see `application:didDiscardSceneSessions` instead).
}
```

앱의 화면이 Background에 들어간 직후 또는 화면의 세션이 해제될 때 호출됩니다. 단, Background의 경우 메모리로부터 영구적으로 버려지는건(파란색 3번) 아닙니다. 

# 관련 면접 질문
## 1. Active(Foreground) → Background 전환 시 필요한 작업 처리에는 무엇이 있는가?

<details> <summary>정답 보기</summary>
  
  - 현재 상태의 데이터 저장
  - 진행 중인 작업 일시 정지 또는 중단
  - 타이머 중지, 리소스 사용 최소화
  - 민감한 사용자 정보 화면 숨김 처리
  - 필요한 경우 백그라운드 작업 등록 (beginBackgroundTask)

</details>

## 2. Background → Active(Foreground) 전환 시 필요한 작업 처리에는 무엇이 있는가?

<details> <summary>정답 보기</summary>
  
  - UI 데이터 갱신 및 새로고침
  - 백그라운드에서 중지된 작업 재개
  - 백그라운드 상태에서의 변경사항을 확인하여 화면 갱신
  - 사용자에게 보여줄 데이터를 최신 상태로 업데이트

</details>

## 3. 메모리 경고(didReceiveMemoryWarning) 발생 시 리소스를 어떻게 관리할 수 있는가?

<details> <summary>정답 보기</summary>

  - 사용하지 않는 객체와 캐시 데이터 제거
  - 이미지, 비디오와 같은 대용량 리소스 즉시 해제
  - 불필요하게 메모리에 올라간 뷰 컨트롤러의 뷰 정리
  - 필요 시 메모리 사용량을 재점검 및 최적화 수행

</details>

## 4. 앱의 상태에 따라 AppDelegate와 SceneDelegate 메서드의 호출 순서는 어떻게 되는가?

<details> <summary>정답 보기</summary>
  
  ### AppDelegate(iOS 13 이상):
  - didFinishLaunchingWithOptions → configurationForConnecting

  ### SceneDelegate:
  - willConnectTo → sceneDidBecomeActive → sceneWillResignActive → sceneDidEnterBackground → sceneWillEnterForeground → sceneDidBecomeActive → sceneDidDisconnect

  ### AppDelegate(iOS 13 미만 또는 Scene 미사용):
  - didFinishLaunchingWithOptions → applicationDidBecomeActive → applicationWillResignActive → applicationDidEnterBackground → applicationWillEnterForeground → applicationDidBecomeActive → applicationWillTerminate

</details>

## 5. 시스템의 리소스 관리 방식과 이에 대응하는 기본적인 방법은?

<details> <summary>정답 보기</summary>
  
  - 시스템은 사용량이 많은 리소스를 관리하기 위해 앱을 자동으로 종료할 수 있음
  - 앱은 메모리 경고를 받으면 미사용 리소스를 신속하게 해제해야 함
  - 캐시 정책 설정, 이미지 압축, Lazy Loading 적용
  - 적절한 메모리 관리(ARC, weak 참조 활용) 및 메모리 누수 방지

</details>

## 6. View Life Cycle에서 생명주기 메서드 내에서 적절히 수행할 작업 예시를 들어 설명하라.

<details> <summary>정답 보기</summary>

  - viewDidLoad: 초기 UI 설정, 데이터 초기화, 네트워크 요청 시작
  - viewWillAppear: 화면이 나타나기 전 데이터 최신화, UI 업데이트
  - viewDidAppear: 애니메이션 시작, 위치정보 서비스 시작
  - viewWillDisappear: 데이터 저장 및 네트워크 요청 중지
  - viewDidDisappear: 키보드, 알림 옵저버 제거, 자원 정리

</details>

## 7. ViewController의 생명주기 메서드 호출 순서는?

<details> <summary>정답 보기</summary>

  ```swift
  loadView → viewDidLoad → viewWillAppear → viewDidAppear → viewWillDisappear → viewDidDisappear
  ```

</details>

## 8. Push 알림, URL Scheme 처리 등 외부 이벤트 대응 방법은 무엇인가?

<details> <summary>정답 보기</summary>
  
  ### Push 알림 처리:
  - application(_:didReceiveRemoteNotification:) (AppDelegate)에서 처리
  
  ### URL Scheme 처리:
  - application(_:open:options:) (AppDelegate) 또는
  - scene(_:openURLContexts:) (SceneDelegate)에서 URL 확인 후 처리

</details>

## 9. 백그라운드 작업 및 타이머, 데이터 보호 전략 등 시스템 레벨 대응 방법은?

<details> <summary>정답 보기</summary>

  ### 백그라운드 작업:
  - beginBackgroundTask(expirationHandler:), endBackgroundTask(_:) 사용
  
  ### 백그라운드 타이머:
  - Background Modes (Capability)를 활성화하고 적절한 모드 설정
  
  ### 데이터 보호 전략:
  - Keychain, UserDefaults 보호 설정, 데이터 암호화 사용

</details>

## 10. Scene 별 데이터 관리 및 상태 복원 방법은?

<details> <summary>정답 보기</summary>

  - NSUserActivity 객체를 이용한 상태 저장 및 복원
  - stateRestorationActivity를 통해 상태를 Scene 별로 저장 및 복원
  - Scene Delegate의 stateRestorationActivity(for:), scene(_:willConnectTo:options:) 사용
  
</details>

## 11. Background에서 작업을 완료하기 위한 방법으로는 어떤 것이 있는가?

<details> <summary>정답 보기</summary>
  
  - beginBackgroundTask를 통해 제한된 시간 내 백그라운드 작업 수행
  - Background Fetch 기능 활용 (Background Fetch Capability)
  - Push Notifications, Silent Push, Background URLSession 활용
    
</details>

## 12. 앱의 각 상태에서 가능한 작업은 무엇인가?

<details> <summary>정답 보기</summary>

  - Active: 사용자와 상호작용, UI 업데이트, 실시간 데이터 처리
  - Inactive: 작업 일시정지, 중요한 데이터 저장
  - Background: 제한된 백그라운드 작업(다운로드, 위치 추적 등), 데이터 저장
  - Suspended: 작업 수행 불가, 앱 상태 메모리에 유지, 리소스 해제 가능
  
</details>

## 13. 앱 LifeCycle 흐름에서 Delegate 메서드 호출 예시

<details> <summary>정답 보기</summary>

  ### 상황	호출 메서드 (iOS 13 이상 기준)
  - 앱 최초 실행	AppDelegate: didFinishLaunchingWithOptions
    - SceneDelegate: willConnectTo, sceneDidBecomeActive
  - 홈 버튼 눌렀을 때	sceneWillResignActive → sceneDidEnterBackground
  - 다시 앱으로 복귀할 때	sceneWillEnterForeground, sceneDidBecomeActive
  - 앱 전환 화면	sceneWillResignActive (Inactive 상태 진입)
  - 강제 종료 시	sceneDidEnterBackground → 앱이 즉시 종료

</details>

## 14. Child ViewController와 Parent ViewController의 생명주기 관계는?

<details> <summary>정답 보기</summary>

  - 부모 컨트롤러가 생명주기 메서드를 호출하면 자식 컨트롤러의 대응 메서드도 자동 호출됨.
  - 부모의 viewWillAppear → 자식의 viewWillAppear
  - 부모 컨트롤러가 사라질 때도 동일한 방식으로 자식 컨트롤러의 생명주기가 연계되어 호출됨.

</details>

## 15. Push 알림 또는 외부 URL 처리 시 고려할 사항은?

<details> <summary>정답 보기</summary>

  - URL 스킴 처리 시 유효성 검사 필수
  - 앱이 실행되지 않은 상태에서 Push 알림 처리 (launchOptions 이용)
  - 알림 데이터를 통해 특정 화면으로 이동 또는 데이터 로직 처리 명확히 구현 필요

</details>
