## 이 이야기는 Rendering (Render Loop)부터 시작되어...
이전에 말씀드렸듯이 `rendering` (구체적으로는 `render loop`)에 대해 학습하며 딥다이브를 하다보니
바로 `render loop`에 대한 설명을 드리는 것 보다는 하위? 개념이라 할 수 있는 View 관련 Cycles에 대해 먼저 설명드리는게 우선이라 생각했습니다.

### 🗺️ ROAD MAP TO RENDER LOOP
1. Main RunLoop
2. Update Cycle
3. Layout Cycle
4. Drawing Cycle
5. Render Loop (Core Animation + CATransation + Rendering Pipeline)

오늘은 1 ~ 4번까지를 준비했습니다.

# 👀 톺아보기
## ✅ Main RunLoop
> 이벤트 처리 + 시스템 루프 (앱의 이벤트 루프 전반 구조 이해)

![image](https://github.com/user-attachments/assets/07488152-dae2-4dd3-ae1b-c4fd67b25734)

1. App이 실행되면 UIApplication이 Main Thread에서 Main RunLoop을 실행하고
2. Main RunLoop은 Loop를 돌며 이벤트를 처리힙니다.
3. 이 처리 과정에서 각 이벤트에 맞는 핸들러를 찾아
4. 해당 핸들러에게 처리 권한을 위임합니다.
5. 권한을 위임받은 핸들러가 이벤트를 처리하고
6. 다시 Main RunLoop에 권한을 돌려줍니다.

## ✅ Update Cycle
> 상태 갱신 (상태 변화 -> 뷰 변경 감지 과정 파악)

![image](https://github.com/user-attachments/assets/c4f4f087-7833-4f57-bf11-78a83711f780)

1. 만약 Main RunLoop에서 처리하는 이벤트가 뷰의 Layout이나 Position을 변경하는 이벤트일 경우
2. Main RunLoop에서 뷰 업데이트를 예약(트리거)합니다.
3. 이후 Main RunLoop의 2 ~ 6번 과정 이후 Update Cycle이 시작되고
4. 이떄 해당 뷰의 값을 변경하는 등의 처리를 하게 됩니다.

> [!NOTE]
> 쉽게 말해 RunLoop에서 뷰의 layout이나 position 코드 처리가 예약되고, Update Cycle 시점에서 해당 코드를 직접적으로 처리하게됩니다.
> 
> 여기서 원하는 핸들러를 제대로 구현하기 위해 인지해야할 사항은 코드와 화면에서 반영되는 시점이 다르다는 점입니다.

### 업데이트 트리거 종류
1. 뷰를 부분적으로 가린 타 뷰의 이동 및 제거
2. 히든 뷰 노출
3. 뷰를 화면 밖 또는 안으로 이동 및 스크롤
4. UIView의 `setNeedsDisplay()` 메서드 호출

> [!NOTE]
> ### setNeedsDisplay()
> 결국 뷰에 컨텐츠를 그리는건 `draw` 메서드입니다. 하지만 `draw`를 직접 호출하는건 지양됩니다.
>
> 1. setNeedsDisplay()를 사용하여 뷰 업데이트 예약
> 2. setNeedsDisplay()는 즉시 리턴
> 3. 다음 Update Cycle에서 draw 메서드가 호출되어 뷰가 실제로 업데이트

## 💡 지금 시점에서 미리 짚고 넘어가는 UIView Layout 메서드
### 1. layoutSubViews()
* 뷰의 값을 호출한 즉시 변경시켜주는 메서드
* 호출되면 해당 뷰의 모든 서브뷰들의 layoutSubViews() 또한 연달아 호출
* 따라서 소요되는 비용이 클 수 있고, 그렇기 때문에 직접 호출 지양
* 직접 호출이 아닌 다른 메서드를 통해 layoutSubViews()가 호출되도록 하는 방법이 적절

### 2. setNeedsLayout()
> receiver(수신자)의 현재 레이아웃을 무효화하고, 다음 업데이트 주기 동안 레이아웃 업데이트를 트리거 합니다.

* layoutSubViews()를 호출하기 위한 예약 방법 중 비용이 가장 적음
* setNeedsLayout() 호출 시 해당 뷰가 재계산되어야 하는 뷰라고 수동 체크가 되며(`needsLayout` Flag == true) 업데이트 주기에서 layoutSubViews가 호출됨
* setNeedsLayout()은 비동기적으로 작동하기 떄문에 호출되고 바로 반환

### 3. layoutIfNeeded()
> 레이아웃 업데이트가 보류중인 경우(예약되고 기다리는 경우), 하위 뷰를 즉시 레이아웃(업데이트) 합니다.

* setNeedsLayout()과 같이 수동으로 layoutSubViews를 RunLoop 단계에서 예약하는 메서드
* 하지만 예약을 바로 실행시키는 동기적으로 작동하는 메서드
* 즉, 업데이트 주기가 올 때까지 기다리는 것이 아닌 그 즉시 layoutSubViews를 발동시키는 메서드 (애니메이션에 주로 사용)

## ❓ UIView Layout 메서드 관련 궁금증
### 1. 하나의 뷰가 Main RunLoop에서 setNeedsLayout과 layoutIfNeeded를 둘 다 호출할 경우
* layoutIfNeeded 호출과 동시에 RunLoop 단계에서 뷰의 값들이 재계산되고 화면에 반영 (필요 시)
* 이미 반영됐으므로 setNeedsLayout이 예약한 변경 값은 반영되지 않음. (layoutSubViews 호출 x)

### 2. layoutSubViews와 layoutIfNeeded의 차이점
* layoutSubViews는 비싼 작업으로 지양 하지만, layoutIfNeeded는 실제로 많이 쓰이고 지양 대상이 아님
* layoutSubViews는 직접적으로 시스템을 무시하고 바로 뷰와 하위 뷰의 업데이트를 즉시 반영하다보니 UIKit의 레이아웃 시스템을 망가뜨릴 수 있음
* 하지만, layoutIfNeeded는 동일하게 layoutSubViews를 호출하여 업데이트를 즉시 반영하지만, 이는 필요할 때만 호출됨
* 즉, layoutIfNeeded는 layoutSubViews 호출 이전에 내부 최적화가 구현되어 있음. 예를 들어, `view.needsLayout` flag가 true일 때만 (레이아웃이 필요한지 확인) layoutSubViews를 호출
* 단, 너무 많은 호출은 동일하게 지양

### 3. view.needsLayout flag
* 오토 레이아웃 제약을 변경하거나
* 수동으로 프레임을 조정했을 때 해당 flag가 true로 변경, 이후 layoutIfNeeded를 호출하면 바로 반영
* setNeedsLayout을 호출했을 때 해당 flag가 true로 변경, 이후 업데이트 주기에서 반영
* 뷰 내부에서 커스텀 뷰 레이아웃을 사용하고 있고 시스템이 자동으로 감지를 하지 못하는 상황에서는 UIView 메서드를 사용하여 수동 예약 필요

## ✅ Layout Cycle
> 뷰의 위치와 크기 계산 (위치/크기 계산 방식 이해)

![image](https://github.com/user-attachments/assets/d710958a-3ddc-4592-9bc3-e3a5dcabc8d7)

Layout은 Black Box일 때가 많습니다. 따라서 해당 주기를 이해하는 것은 매우 중요합니다.

![image](https://github.com/user-attachments/assets/9384831d-3529-4ffd-b901-860e2cfe833f)

1. Main RunLoop이 돌고 있을 때
2. Constraint가 변하면
3. Deferred Layout Pass가 발생합니다. (Update Cycle 단계)

> [!Note]
> Constraint가 변하는 상황
> 1. activate or deactivate
> 2. setting constants or priority
> 3. adding or removing views
>
> Constraint Change가 발생하면 Layout Engine(핸들러)은 Layout을 다시 계산합니다.
> 
> 이때 뷰들은 자신의 superview가 layout을 다시 그려야 한다고 예약합니다. (`superview.setNeedsLayout()`)
> 
> 이 과정에서 deferred layout pass를 스케쥴하게 됩니다.
>
> Deferred Layout Pass는 2 가지 단계를 거칩니다.
> 1. Update Pass
> 
> 필요시 constraints를 업데이트 합니다.
> > 시스템은 뷰 계층을 돌며 뷰컨트롤러에 updateViewConstraints를 호출하고 뷰에 updateConstraints를 호출합니다.
> > 
> > 만약 constraints 업데이트가 느리거나, 뷰가 불필요하게 바뀌는 퍼포먼스 이슈 발생하여 constraints를 일괄적으로 업데이트 하고 싶을때는 이 함수들을 오버라이드 할 수 있습니다.
> > 
> > 하지만 WWDC나 아래 스택오버플로우 답글에서는 오버라이드하지 않고 그때그때 constraints를 업데이트하는 것을 추천합니다.
> 
> 2. Layout Pass
>
> 필요시 뷰의 프레임을 다시 재배치합니다. (Bounds는 자기 자신을 기준으로 결정하는 고유값, Frame은 superview를 기준으로 잡은 좌표계)
> > 시스템은 뷰 계층을 돌며 레이아웃이 필요하다고 마킹(예약)된 뷰컨트롤러에 viewWillLayoutSubviews를 호출하고, 뷰에 layoutSubviews를 호출합니다.
> > 
> > 이 함수들을 오버라이드하여 커스텀한 레이아웃을 줄 수 있으나, 레이아웃이 constraints 자체만으로 표현될수 없을때만 하는게 좋습니다.
> > 
> > 자신의 subtree 바깥의 뷰를 invalidate하지 않는게 좋습니다. <- 이해 못함
> > 
> > 이 과정에서 Layout Engine으로부터 서브뷰의 프레임을 복사해와 서브뷰들이 위치하게 됩니다.

## ✅ Drawing Cycle
> 뷰의 콘텐츠 그리기 (실제 화면에 그리는 방식 파악)

![image](https://github.com/user-attachments/assets/e7a7f343-6ec1-477e-a4d6-328ea6001600)

### 1. updateConstraints: autolayout이 업데이트 되야할 때만 호출
* 오토레이아웃을 업데이트(제약) 합니다.
* 현재 기기의 화면 크기를 기준으로 제약을 업데이트 합니다.
* 동적인 오토레이아웃 변경이 일어날 경우 제약조건을 변경합니다.
* 명시적으로 호출할 수 없습니다.
* 오토레이아웃으로 인한 제약이 확정되는 시기입니다.
* setNeedsUpdateConstraints / updateConstraintsIfNeeded로 간접적으로 호출할 수 있습니다.

### 2. draw: 실제로 화면을 다시 그리는 단계 (실제 내부의 컨텐츠를 다시 그립니다.)

![image](https://github.com/user-attachments/assets/a98eaf56-46aa-491b-8b55-c9e42cad2c7b)

## 🧹 정리하자면...
1. 이벤트 발생 (터치, 상태 변경 등)

     ↓

2. Main RunLoop 처리 (예약 및 계산)

     ↓

3. 상태 변화 → update cycle (트리거에 따른 애니메이션, 데이터 변경)

     ↓

4. 뷰 변경 감지 → layout cycle (필요시 layoutSubviews 호출)
   
     ↓

5. 뷰 변경 감지 → drawing cycle (필요시 draw(_:) 호출)

     ↓
   
6. Render loop (디스플레이 동기화, VSync 타이밍)

     ↓

7. 실제 GPU에서 화면 렌더링

# ✚ 추가로
### 제약조건이 변경되면 레이아웃 재계산이 대부분 발생. 하지만, 항상은 아니다.
> 제약조건 변경 → updateConstraints() → layoutSubviews() → draw(_:) 호출로 이어짐

* 제약은 바뀌었지만 실제 위치나 크기가 달라지지 않은 경우 (예: leading = 0 -> leading = 0 즉, 동일한 제약으로 다시 설정한 경우)
* setNeedsUpdateConstraints()만 호출하고 setNeedsLayout()을 호출하지 않은 경우 -> updateConstraints()는 불리지만, 레이아웃 자체를 다시 계산할 필요는 없다고 판단
* translatesAutoresizingMaskIntoConstraints = true 상태에서 프레임 기반 레이아웃만 적용한 경우 -> 오토레이아웃 시스템 개입 X
* 시스템이 최적화한 경우 -> 불필요한 연산 생략
* draw도 뷰를 다시 그릴 필요가 있을 경우에만 호출 -> setNeedsDisplay 또는 setNeedsDisplayInRect를 호출해야 다시 그려짐

### 뷰가 이미 렌더링된 후에 NSLayoutConstraint.activate()를 호출해서 제약을 수정
> UIView는 생명주기 중 언제든지 제약을 변경할 수 있음

* needsLayout = true로 예약
* 다음 RunLoop에서 layout pass가 일어남

### NSLayoutConstraint.activate() vs setNeedsUpdateConstraints()의 차이
<img width="667" alt="image" src="https://github.com/user-attachments/assets/228864de-efdd-446f-b99a-5ce4d58d1960" />

* NSLayoutConstraint.activate()는 "제약을 진짜로 바꾼다"
* setNeedsUpdateConstraints()는 "내부 로직에서 제약을 갱신할 준비를 한다"
* 둘 다 layout pass로 이어질 수 있지만, 트리거 방식과 사용 목적이 다름

### constraint.constant = 100만 한거랑 constraint.constant = 100 view.setNeedsLayout()을 한거랑 차이
> 기본적으로는 차이 없다

* constraint.constant = 100만 해도, 내부적으로 오토레이아웃 엔진이 바뀐 걸 감지해서 자동으로 setNeedsLayout()을 호출 -> 다음 RunLoop 사이클에서 자동으로 layoutSubviews가 호출되고 레이아웃이 반영
* 다만, 즉시 레이아웃 변경을 감지하거나 적용하고 싶을 때 layoutIfNeeded 호출

### SwiftUI와 UIKit의 화면 렌더링 방식 차이
> SwiftUI는 선언적(Declarative), UIKit은 명령형(Imperative)이기 때문에 내부 렌더링 로직도 다름

#### 공통점
* 둘 다 RunLoop, Main Thread, 그리고 Core Animation을 통해 화면을 렌더링
* 결국은 UIKit이든 SwiftUI든 최종적으로는 CALayer를 통해 화면이 그려짐
* display link, layout pass, drawing pass 등 일반적인 iOS 렌더링 사이클의 큰 구조는 공유

#### 차이점
1. 렌더링 트리거 방식
* UIKit은 setNeedsLayout(), setNeedsDisplay()를 직접 호출해서 수동으로 트리거
* SwiftUI는 상태 값이 변하면 시스템이 자동으로 뷰를 "diffing" 하면서 변경된 부분만 다시 렌더링

2. View의 생명주기
* UIKit의 UIView는 명확한 생명주기 메서드가 있음 (viewDidLoad, viewWillAppear, layoutSubviews, ...).
* SwiftUI는 명확한 생명주기 없이 상태 기반으로 작동하고, 뷰는 필요할 때마다 재생성되기 때문에 onAppear, onDisappear 같은 후킹 포인트만 제공

3. 그리기 방식
* UIKit은 직접적으로 Core Graphics를 써서 커스텀 드로잉 (draw(_:) override).
* SwiftUI에서는 Canvas 뷰나 GraphicsContext 같은 걸 사용해서 고수준 API로 드로잉
