## 목차
- [Library와 Framework](#library와-framework)
  - [Library란](#library란)
    - [Static Library](#static-library)
    - [Dynamic Library](#dynamic-library)
    - [Library 정리](#library-정리)
  - [Framework란](#framework란)
    - [Static Framework](#static-framework)
    - [Dynamic Framework](#dynamic-framework)
    - [Framework 정리](#framework-정리)
  - [Library와 Framework 선택하는 나만의 선택 기준](#library와-framework-선택하는-나만의-선택-기준)
- [모듈화와 빌드 시간의 관계](#모듈화와-빌드-시간의-관계)
- [참고](#참고)

# Library와 Framework

Library와 Framework는 **공통된 목적의 소스 코드를 묶어 독립적으로 배포**할 수 있다는 공톰점이 있다. 때문에 둘은 모듈화의 도구로 사용된다. 하지만, 둘이 완전이 동일한 동작을 한다면 Apple은 둘을 분리하지 않았을 것이다.

둘의 차이는 천천히 알아보도록 하고, Library 먼저 살펴보겠다.

## Library란

Library는 소스 코드와 데이터들의 집합이다. 독립적으로 배포 가능하기 때문에 재사용성이 높은 소스 코드를 Library를 통해 관리할 수 있다. 또한, Library는 이미지와 같은 리소스 자원을 포함할 수 없다.

Library는 두가지 타입으로 나뉜다.

- Static Library
- Dynamic Library

### Static Library

<img src="https://github.com/user-attachments/assets/7c2b4cca-1850-4757-a809-fe3594881f82" width=800>

Static Library는 정적 라이브러리로 파일 확장자는 `.a`이며, 앱 컴파일 시점에 위 이미지와 같이 앱 소스 코드에 함께 포함되어 빌드된다.

이를 직접 확인해 볼 수 있는데

<img width="500" alt="스크린샷 2025-01-13 오후 7 32 30" src="https://github.com/user-attachments/assets/62988025-48d3-4507-98a0-bb1a26c1e3f2" />
<img width="1021" alt="스크린샷 2025-01-13 오후 7 31 06" src="https://github.com/user-attachments/assets/69375b9b-11e1-4c6e-8ec9-88fbcdd88cca" />

라이브러리를 직접 생성 및 App 프로젝트에 추가 후 빌드해보면, 

<img width="500" src="https://github.com/user-attachments/assets/94cabc8d-f1ed-4e85-ab38-16cb3cf671f5">

위와 같이 MyApp이라는 Bundle이 생성되고, 내부에는 MyApp의 실행 파일과 plist와 같은 앱 실행에 필요한 리소스 파일들이 존재한다.

```bash
nm --debug-syms MyApp | grep "\.o"
```

위 명령어를 통해 MyApp의 실행 파일 내부를 확인해보면, 

<img width="943" alt="image" src="https://github.com/user-attachments/assets/45632ba1-cded-4f13-9f7f-33d2ea39b8ae" />

내부에 MyLibrary의 오브젝트 파일이 포함되어 있는 것을 확인할 수 있다.

정적 라이브러리는 이 처럼 앱 실행 파일에 함께 포함되어 배포되기 때문에 정적 라이브러리를 많이 사용하거나 큰 라이브러리를 정적으로 사용하면 앱 사이즈가 그만큼 커지고, 이는 곧 **앱의 메모리 사용량이 증가하고 실행 시간이 느리다**는 뜻이다. 

또한, 정적 라이브러리의 수정 발생하면, 다음과 같이 App도 다시 빌드되어야 한다.

<img width="930" alt="스크린샷 2025-01-13 오후 7 57 51" src="https://github.com/user-attachments/assets/52b31abf-a6cf-4027-a929-c7aef9cae14c" />

하지만, 소스 코드가 실행 파일에 포함되어 있기 때문에 접근 속도가 빠르다.

### Dynamic Library

<img src="https://github.com/user-attachments/assets/1bae143f-ab1f-4840-b61c-9573ac1da43c" width=800>

Dynamic Library는 동적 라이브러리로 확장자는 `.dylib`이다. 동적 라이브러리는 정적 라이브러리와 다르게 앱 소스 코드에 라이브러리 코드가 포함되는 것이 아닌 참조 값을 가지고, 라이브러리의 **소스 코드가 호출될 때 해당 코드를 Stack 영역에 로드** 후 사용한다.

때문에. 정적 라이브러리에 비해 **메모리 사용량이 줄어들고**, **App 실행 시간이 빠르다**. 또한, 동적 라이브러리의 경우 수정이 발생했을 때 **수정된 라이브러리만 빌드**되며, App에서 수정된 소스 코드를 불러올 수 있다.

모듈화를 진행하는 대표적인 이유로 `빌드 시간 감소`가 있는데 이는 Dynamic Library를 사용했을 때만 해당한다.

동일하게 App의 실행 파일 내부를 확인해 보면,

<img width="860" alt="스크린샷 2025-01-13 오후 8 09 57" src="https://github.com/user-attachments/assets/f8e9b64a-bb7d-449f-bda3-f28226ef8929" />

정적 라이브러리 때와 다르게 위와 같이 실행 파일에 라이브러리 코드가 포함되어 있지 않은 것을 확인할 수 있다. 

대신, 아래 명령어를 통해 동적 라이브러리 목록과 각 라이브러리의 경로, 버전 정보 등을 확인할 수 있다.

```bash
otool -L MyApp
```

![image (2)](https://github.com/user-attachments/assets/55bdf33a-9c6f-4e50-8a55-081abb376c64)

동적 라이브러리의 소스 코드가 포함된 경로를 확인할 수 있다. 

하지만, iOS OS에선 동적 라이브러리를 사용하지 못한다. 실제로 라이브러리를 생성할 때도

![스크린샷 2025-01-13 오후 8 32 35](https://github.com/user-attachments/assets/1e5cb392-8214-4179-8a0d-80b01aaea8ba)

위와 같이 정적 라이브러리만 생성 가능하다. 하지만, 정적 라이브러리 생성 후 

![스크린샷 2025-01-13 오후 8 34 22](https://github.com/user-attachments/assets/11175790-001d-41d8-8299-1bb5a804aad4)

Build Settings에서 동적 라이브러리로 변경할 수 있는데 동적 라이브러리로 변경할 경우 실 기기에서 크레쉬가 발생한다.(하지만, 시뮬레이터에서는 동작…이유는 찾지 못했음…😓)

iOS에서 동적 라이브러리가 동작하지 않는 이유를 몇가지 실험을 통해 추측해 봤을 땐,

> [!WARNING]  
> 개인적인 생각입니다. 사실과 다를 수 있음

iOS에서 동적 라이브러리는 아래와 같이 번들에 포함되지 않는다.

![스크린샷 2025-01-13 오후 8 43 24](https://github.com/user-attachments/assets/8fa3c45d-45aa-4aca-aace-be199712edbc)

때문에, 참조를 통해 주소 값을 알지만, 접근할 수 없기 때문이라고 생각된다.

실제로, 동적 라이브러리를 지원하는 MacOS의 경우 

![스크린샷 2025-01-13 오후 8 45 00](https://github.com/user-attachments/assets/c3b7d044-21ea-47d7-955b-48c2cdc32ad2)

위와 같이 번들 내부에 동적 라이브러리 파일이 포함되어 있다.

[해당 글](https://codedstuff.com/xcode-modules-framework-and-libraries-comparison/)에서도 동적 라이브러리는 System iOS와 macOS를 제외하고는 지원하지 않는다고 소개하고 있음.

> [!TIP]
> 추가로, iOS의 경우 Mach-O Type을 동적 라이브러리로 변경해도 확장자가 정적 라이브러리의 확장자인 `.a`로 고정되어 있는데 라이브러리의 확장자는 Mach-O Type에 따라 달라지는 것이 아닌 라이브러리를 생성할 시점의 Type을 따라가는 것 같다.
> 
> 동적 라이브러리를 지원하는 MacOS에서도 정적 라이브러리로 생성 후 Mach-O Type을 동적 라이브러리로 변경했을 때도 빌드 결과 `.a`로 확장자가 변경되지 않았다.

### Library 정리

> [!NOTE]  
> - 🏛️ Library
>    - 소스 코드와 데이터의 집합
>    - 이미지와 같은 리소스 자원은 포함할 수 없음
> - Static Library
>    - 컴파일 시 App 실행 파일에 소스 코드가 포함됨
>    - App의 실행 파일의 사이즈가 커지고, 실행 시간이 느림
>    - 라이브러리 수정 시 App 또한 다시 빌드 되어야하기 때문에 빌드 효율성이 떨어짐
>    - 소스 코드 접근 속도는 빠름
> - Dynamic Library
>    - 참조를 통해 런타임 시점에 소스 코드를 Stack에 할당하여 사용
>    - 메모리와 실행 시간 측면에서 정적 라이브러리에 보다 이점이 있음
>    - 수정이 발생했을 때 App까지 빌드할 필요가 없음
>    - 참조를 통해 실제 코드에 접근해야 함으로 접근 속도는 다소 떨어짐
>    - iOS에서는 지원하지 않음

> [!WARNING]  
> 추가로, 정적 라이브러리의 경우
> 
> <img src="https://github.com/user-attachments/assets/7609e70b-d86d-4d77-a174-e2b1a0702425" width=400>
>
> 위와 같이 모듈끼리 의존한다고 가정했을 때 모듈 C의 소스 코드가 모듈 A와 모듈 B에게 복사되기 때문에 최종적으로 App에서 모듈 C의 소스 코드가 중복되는 문제가 발생할 수 있다. 이를 해결하기 위해선 모듈 C를 동적 라이브러리로 변경해줘야 한다.


사실 정리한 것만 봐도 동적 라이브러리가 훨씬 이점이 많다는 것을 알 수 있다. iOS에서 지원하지 않는다는 큰 문제만 있을 뿐…

하지만, 우리에겐 **Framework**가 있다.

## Framework란

Framework는 계층 구조를 갖는 디렉토리 형태를 띄고 있다. `.a` 확장자 파일 딸랑 하나있는 라이브러리와 다르게 Framework는 아래와 같이 헤더 파일, plist 등 다양한 정보를 담고있다.

![스크린샷 2025-01-13 오후 9 40 57](https://github.com/user-attachments/assets/90d4239d-b780-4298-8f13-4e1b446a3443)

또한, Framework의 경우 Library와 다르게 이미지와 같은 리소스 파일을 포함할 수 있고, 특이하게 실행 파일이 존재한다. 

Framework에 관련된 글을 찾아보면 `Framework의 경우 코드 호출 및 흐름 제어권을 갖는다.` 라는 말을 많이 하는데 이러한 특징 때문에 가능한 것 같다.

Framework 또한 Library와 동일하게 Static, Dynamic 두 가지 타입을 갖는다. 특성 또한 비슷하기 때문에 어렵지 않다.

### Static Framework

Framework를 생성했을 때 기본값은 Dynamic이기 때문에 Build Settings에서 Mach-O Type을 `Static Library`로 변경해주어야 한다.

특징은 정적 라이브러리와 동일하게 컴파일 시 앱 실행 파일에 소스코드가 포함된다. Framework의 차이점은 

![스크린샷 2025-01-13 오후 10 01 19](https://github.com/user-attachments/assets/5554ca21-dee6-495f-8c44-52a7a7950578)

App Target에서 Embed를 설정할 수 있다. Embed를 `Embed & Sign`으로 설정하게 되면 App 번들에 Framework가 포함되게 된다. 

반대로 `Do Not Embed`로 설정하면 번들에 Framework가 포함되지 않는다.

정적 프레임워크의 경우 App 실행 파일에 이미 프레임워크의 소스 코드가 포함되어 있기 때문에 번들에 포함시킬 필요가 없다. 때문에 Embed를 `Do Not Embed` 설정하는 것을 권장한다.

> [!WARNING]  
> 만약, 프레임워크에 리소스가 포함되어 있는 경우 `Embed & Sign`로 설정하여 번들에 포함시켜야함.
>
> App 실행 파일에는 소스 코드만 포함되기 때문

라이브러리 때와 동일하게 번들 내부를 확인해 보면,

![스크린샷 2025-01-13 오후 10 19 02](https://github.com/user-attachments/assets/03cf236d-576b-4748-9fd1-e46692f54722)


plist와 실행 파일이 있고, 실행 파일 내부를 보면,

![image](https://github.com/user-attachments/assets/62bbd1ad-e6cd-40c6-aae7-f78931cf5d20)

프레임워크의 소스 파일이 포함되어 있는 것을 확인할 수 있다.

### Dynamic Framework

Dynamic Framework 역시 동작 원리는 Dynamic Library와 동일하다.

또한, 앞서 Dynamic Library에서 언급했던 변경에 따른 빌드 결과를 보면, **수정된 Framework만 빌드**되고 App은 따로 빌드되지 않는다. 이는 상황에 따라 결과가 달라질 수 있다.

![스크린샷 2025-01-13 오후 11 06 31](https://github.com/user-attachments/assets/e6349e1d-fc58-4c91-b385-ee341d3d6ed8)

하지만, Dynamic Framework는 iOS에서도 사용이 가능하다. 가능한 이유는 앞서 언급한 Embed를 통해 App 번들에 포함 시킬 수 있기 때문이다.

![스크린샷 2025-01-13 오후 10 22 48](https://github.com/user-attachments/assets/e521cc24-c0d9-471b-a927-eb65dddb0bcb)

Embed를 `Embed & Sign`으로 설정 및 프레임워크의 Mach-O Type을 Dynamic Library로 변경 후  빌드해보면,

![스크린샷 2025-01-13 오후 10 24 56](https://github.com/user-attachments/assets/b9cca475-ca86-41a5-b150-2da261e70eb9)

App 실행 파일 내부에 프레임워크의 소스 코드가 사리지고, 

![스크린샷 2025-01-13 오후 10 27 04](https://github.com/user-attachments/assets/861185f5-70e4-42bd-b0e9-3ca5a34a4a3d)

번들 내부에 `Freamworks`라는 디렉토리가 생성된다. 또한, 라이브러리 때와 동일하게 `otool -L MyApp`를 통해 확인해 보면,

![image (1)](https://github.com/user-attachments/assets/3f26afd6-9fed-4676-a541-265ce50c84ec)

동적 프레임워크의 경로를 확인할 수 있다. 프레임워크의 소스 코드가 호출됐을 때 해당 경로를 통해 소스 코드를 Stack에 할당 후 사용할 수 있게된다.

> [!IMPORTANT]  
> **Dynamic Framework지만, `Do Not Embed`로 설정하면 어떻게 되나요?**
>
> `Do Not Embed`로 설정하면 앞서 언급했던 대로 번들에 포함되지 않는다. 때문에 경로를 통해 접근하려 해도 해당 경로엔 프레임워크가 존재하지 않기 때문에 크래쉬가 발생한다.
> (하지만, 이또한 시뮬레이터에서는 동작함…ㅠ 왜일까??)


### Framework 정리

> [!NOTE]  
> - 🧰 Framework
>    - 디렉토리 형태의 계층 구조를 가짐
>    - 소스 코드 외에도 이미지와 같은 리소스 파일을 포함할 수 있음
> - Static Framework
>    - 동작 원리는 Static Library와 동일
>    - Embed를 `Do Not Embed` 설정하는 것 권장
>    - 하지만, 리소스가 포함되어 있는 경우 `Embed & Sign`으로 설정해야함 (대신 소스 코드 중복이 발생)
> - Dynamic Framework
>    - 동작 원리는 Dynamic Library와 동일
>    - Embed를 `Embed & Sign` 설정해야함 안 그럼 크래쉬 발생
>    - iOS에서도 지원함 (유일한 Dynamic 타입의 모듈을 사용할 수 있는 방법)


Framework의 경우에는 iOS에서도 Dynamic 타입을 지원하기 때문에 Dynamic Framework를 통해 Dynamic Library의 성능적 이점을 가져갈 수 있다.

## Library와 Framework 선택하는 나만의 선택 기준

- 사이즈가 큰 모듈 → Dynamic Framework
- 변경이 자주 발생하는 모듈 → Dynamic Framework
- 크기가 작고, 변경이 자주 발생되지 않는 모듈 → Static Library
- 다수의 상위 모듈이 의존해야하는 모듈 → Dynamic Framework

이와 같이 나름의 기준을 정해보았다.

# 모듈화와 빌드 시간의 관계

모듈화를 진행할 때 모듈화의 이점으로 대표적으로 언급되는 것이 빌드 시간 감소이다. Dynamic Library를 사용할 경우 변경된 모듈만 빌드되기 때문에 상황에 따라 빌드 시간을 최적화 할 수 있지만, 나아가 모듈 관계에 따라 빌드 시간이 달라질 수 있다.

기본적으로 모듈화된 프로젝트의 경우 하위 모듈이 먼저 빌드된 후 상위 모듈이 빌드된다. 

<img width=150 src="https://github.com/user-attachments/assets/2f57f4f7-cdbb-4c15-95f7-b56788acb268">

만약, 위와 같이 `App` 모듈이 빌드되기 위해선 `모듈 A`가 먼저 빌드되어야 한다는 뜻이다. 모든 모듈의 빌드 시간이 각 5초라고 가정했을 때 프로젝트가 빌드되기 위해서 약 10초의 시간이 소요된다. 

<img width=150 src="https://github.com/user-attachments/assets/1dd6a609-250a-4494-8cc9-9579f32e77bb">

위와 같은 구조의 프로젝트 경우 `모듈 A`가 빌드되기 위해선 `모듈 B`의 빌드가 선행되어야 하기 때문에 총 15초의 빌드 시간이 소요된다. 아래의 이미지가 위와 같은 구조의 프로젝트를 빌드했을 때 컴파일 결과이다.

<img width=100% src="https://github.com/user-attachments/assets/b35cd7c5-b583-40a7-af3a-ce4c1604cf66">

하지만, 위와 같은 구조를 

<img width=300 src="https://github.com/user-attachments/assets/9b0aef31-2d81-49e9-910b-23e608a2a9b4">

이와 같이 `모듈 A`와 `모듈 B`를 같은 계층에 위치 시킨다면, 빌드 시간을 최소화할 수 있다. 그 이유는 빌드는 병렬적으로 동작하기 때문이다. 실제로 CPU의 코어 수 만큼 작업을 처리할 수 있다. 이는 [WWDC 2022](https://developer.apple.com/kr/videos/play/wwdc2022/110364/)를 참고하는 것을 추천한다.

때문에 위와 같은 구조일 경우 `모듈 A`와 `모듈 B`는 병렬적으로 컴파일된다. 때문에 15초에서 약 10초로 빌드 시간을 최적화 할 수 있다.

<img width=100% src="https://github.com/user-attachments/assets/2eb51ecd-e4a9-4f32-8862-11d8835bb69b">

하지만, 모듈 A가 모듈 B에 접근해야한다면, 위와 같은 구조 접근이 불가하다. 이를 해결하기 위해 Interface Target 추가해 Interface를 통해 접근이 가능하도록 하는 것이다.

<img width=300 src="https://github.com/user-attachments/assets/b8946894-a5cc-46cf-93d4-b0e6fc6624c0">

IInterface Target에 Protocol을 구현해 소통할 경우, `모듈 A`는 `모듈 B`의 Interface에 접근할 수 있게 된다. Interface Target은 크기가 작아 빌드 시간도 그만큼 짧다. 또한, 모듈 간 순환 참조가 발생하면 무한 루프에 빠져 컴파일 오류가 발생할 수 있다. Interface Target은 이러한 순환 참조 문제를 미연에 방지할 수 있는 장점이 있다.

이러한 모듈 구조를 아키텍처화한 것이 [Tuist가 소개하는 TMA(The Modular Architecture)](https://docs.tuist.dev/en/guides/develop/projects/tma-architecture#the-modular-architecture-tma)이다.

# 참고

[Overview of Dynamic Libraries](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1)

[Xcode Modules, Framework and Libraries Comparison - Justin Eghtedari](https://codedstuff.com/xcode-modules-framework-and-libraries-comparison/)

[iOS Library, Framework, Swift Package,,, 알아보자 차이점](https://medium.com/delightroom/ios-library-framework-swift-package-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-%EC%B0%A8%EC%9D%B4%EC%A0%90-1f42c7848771)

[[iOS] 빌드 결과로 보는 Static Framework 와 Dynamic Framework](https://sujinnaljin.medium.com/ios-%EB%B9%8C%EB%93%9C-%EA%B2%B0%EA%B3%BC%EB%A1%9C-%EB%B3%B4%EB%8A%94-static-framework-%EC%99%80-dynamic-framework-8568c5840e59)
