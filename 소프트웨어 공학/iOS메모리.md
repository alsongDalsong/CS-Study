## 메모리를 왜 줄여야 할까?

한 줄로 말하면 사용자 경험이 더 좋아지기 때문에 메모리를 줄여야 합니다.

더 구체적으로 설명하면 메모리가 낮으면 앱이 더 오래 메모리에 유지되고 다른 앱들도 메모리에 더 오래 남아있을 수 있게 됩니다.

그러면 앱이 빠르게 실행되고, 성능도 좋아지겠죠? 그렇기 때문에 우리는 메모리를 열심히 줄여야 합니다.

## 메모리를 줄인다는 것은 무엇을 의미할까?

이 말은 사실 메모리 풋프린트를 줄인다는 것을 의미합니다. 

풋프린트?는 영어로 공간이라는 의미인데요.

이 단어가 메모리에 붙으면 ‘프로그램이 차지하는 메모리의 크기를 지칭합니다’

메모리에는 여러가지 종류가 있기 때문에 그것들을 구별해야 하는데요.

이것을 이해하기 위해 page개념을 알아야 합니다.

## 메모리 페이지란?

> [시원님이 정리해 주신 메모리 관련 내용을 보면 더 잘 이해할 수 있습니다!]()
> 

페이징 기법을 사용하는 운영체제는 메모리를 페이지 단위로 관리합니다.

운영체제가 메모리 페이지를 할당하며, 해당 페이지는 힙에서 여러 객체를 보관하는 데 사용될 수 있습니다.


![1](https://github.com/user-attachments/assets/553d99ef-10bb-4bd7-8cfe-73e536e76dda)


그런데 객체 하나가 꼭 페이지 하나에만 있는 것은 아닙니다.

아래처럼 하나의 객체가 여러 페이지에 걸쳐 있을 수도 있습니다.

![2](https://github.com/user-attachments/assets/460a8cbf-a9f4-4d84-9fad-70e2b07d1739)

보통 페이지 크기는 16KB(1,000MB)고, Clean과 Dirty 페이지로 구분됩니다.

> 시원님이 설명해 주신 자료에는 4KB라고 되어있는데 WWDC에서는 16KB라고 설명하고 있어서 찾아보니 보통 4~16KB라고 합니다!
> 

## Clean vs Dirty Page

Clean과 Dirty가 무엇인지 알아봅시다.

예를 들어 20,000개 정수로 이루어진 배열을 할당한다고 가정해 보겠습니다.

이럴 경우 생성된 각 페이지들은 처음엔 Clean한 상태입니다.

![3](https://github.com/user-attachments/assets/bdec92cc-5dc4-4b06-a776-c47ed9cd4e0b)


하지만 데이터를 쓰기 시작하면 그 페이지는 더러워지게 됩니다.

```c
int *arr = malloc(20000 * sizeof(int));
arr[0] = 32
arr[19999] = 64
```

![4](https://github.com/user-attachments/assets/1bb0186f-f255-4f66-be04-a45fbfe72943)


> 반적으로 정수는 4바이트를 차지하니깐 20,000 * 4 = 80,000
즉 80KB 메모리를 차지하게 됨.
> 

읽기 전용 파일을 사용할 경우, 이런 페이지는 항상 Clean페이지입니다.

## 메모리 맵 파일(Memory-mapped files)

디스크에 있지만 메모리에 로드된 파일을 메모리 맵파일이라고 합니다.

예를 들어 50KB인 JPEG가 있고 메모리에 매핑되면 실제로 4페이지의 메모리에 매핑됩니다.


![5](https://github.com/user-attachments/assets/7df60f7a-7432-4840-9476-d3bc158a72c3)

> 16KB가 16,384 bytes니깐 51,200 / 16,384를 하면 3.xx가 나오니깐 마지막 페이지는 일부만 사용하게 됩니다.
> 

4번째 페이지는 완전히 가득 찬 상태가 아니기 때문에 다른 용도로 사용할 수 있습니다.

반면에 앞에 3개 페이지는 가득 찼기 때문에 운영체제에서 메모리가 부족하다고 판단되면 언제든 지워버릴 수 있습니다. 그리고 메모리 맵 파일의 경우 나중에 다시 필요하면 디스크에서 불러올 수 있습니다.

## Clean vs Dirty vs Compressed

일반적인 앱에 대해 이야기할 때 메모리(their footprint and profile)는 dirty, compressed, clean 세 부분으로 나눠집니다.

> WWDC에서는 “their footprint and profile”라고 말했는데 결국 메모리 사용량과 메모리 특성이기 때문에 “메모리”로 해석했습니다.


![6](https://github.com/user-attachments/assets/ebca5f48-3cac-428d-9162-f7e11a04e363)


### **Clean**

**깨끗한 메모리**는 시스템에서 페이지 아웃될 수 있는 데이터입니다.

앞에서 본 메모리 매핑 파일입니다.

![7](https://github.com/user-attachments/assets/ddd1c98f-d285-4ade-88e0-5840d1c1ae06)

![8](https://github.com/user-attachments/assets/9e22bb4f-1fca-46b5-8137-ccccdfaba5e2)

또 다른 예시로는 이미지파일, 데이터 Blobs파일, 트레이닝 모델, 프레임워크 등이 있을 수가 있습니다.

프레임 워크에는 DATA CONST 섹션이 있는데, 운영체제 입장에서 이 데이터들은 변경되지 않으므로 필요할 때 페에지 아웃을 할 수 있습니다. 즉, clean 상태로 간주됩니다.

그런데 만약에 런타임에 swizzling같은 작업을 한다면? 

> 현준님이 정리해 주신 내용에서 상세하게 확인할 수 있습니다.
> 

DATA CONST 섹션의 일부 데이터가 변경될 수 있습니다. 따라서 clean 메모리가 아니라 dirty 메모리로 바뀝니다

**정리**

- **클린 페이지**는 현재 메모리에 로드되어 있지만, 디스크에 있는 데이터와 동일한 상태입니다. 즉, 수정되지 않았습니다.
- **페이지 아웃 가능 여부**:
    - 클린 페이지는 디스크에 저장된 데이터와 동일하므로, 추가 작업 없이 메모리에서 제거할 수 있습니다.
    - 따라서 **페이지 아웃이 가능합니다**.
    - 운영 체제는 클린 페이지를 쉽게 버퍼 캐시나 페이지 캐시에서 제거할 수 있습니다.

---

### **Dirty**

더티 메모리는 앱에서 한 번이라도 write한 모든 메모리입니다.

여기에는 objects, malloc을 통해 할당된 모든 데이터(string 등), 디코딩된 이미지 버퍼, 프레임워크가 포함됩니다.

![9](https://github.com/user-attachments/assets/fb38659d-537c-4b89-8ec7-9f5927360905)

프레임워크에는 데이터 섹션(data)과 데이터 더티 섹션(data dirty)도 있으며 이는 항상 dirty 메모리로 계산됩니다.

`프레임워크`

clean에서도 프레임워크를 언급했고, dirty에서도 프레임 워크를 언급한 것처럼 프레임워크는 메모리를 사용하게 되고, 그중 일부는 dirty로 분류됩니다.

프레임워크를 링크하는 과정에서 dirty 메모리가 발생하는 것은 불가피하지만 만약에 직접 프레임워크를 관리하고 있다면 싱글톤이나 global initializers를 활용하는 것이 dirty 메모리 사용량을 줄이는 좋은 방법이 될 수도 있습니다.

**왜?**

일단 싱글톤은 한 번 생성하면 계속 메모리에 남아있고 이걸 재사용하기 때문에 더러운 메모리가 추가적으로 생기는 것을 막을 수 있음.

global initializers 역시 비슷한 맥락

**정리**

- **더티 페이지**는 메모리에 로드된 후 수정된 상태이며, 디스크에 있는 데이터와 다릅니다.
- **페이지 아웃 가능 여부**:
    - 더티 페이지를 메모리에서 제거하려면 먼저 디스크에 수정된 데이터를 저장해야 합니다.
    - 더티 페이지는 **디스크로 저장되지 않으면 페이지 아웃이 불가능합니다**.
    - 즉, 더티 페이지는 먼저 **클린 상태로 변환한 후**에야 페이지 아웃이 가능합니다.

---

### compressed

iOS는 전통적인 swap 시스템을 가지고 있지 않습니다. 대신에 메모리 압축기를 활용하는 기능이 iOS7부터 도입됐습니다.

메모리 압축기는 액세스되지 않은 페이지를 압축하여 실제로 더 많은 공간을 확보할 수 있게 합니다. 

하지만 엑세스한다면? 압축기는 압축을 해제해서 메모리를 읽을 수 있도록 합니다.

딕셔너리가 있다고 가정해 보겠습니다. 지금은 3개의 페이지 메모리를 사용하지만

![10](https://github.com/user-attachments/assets/56916d9d-b1e4-4f00-95cb-b033c3578ada)

잠시동안 엑세스하지 않았다면 압축을 하게 될 수도 있습니다. 그러면 기존에 3개의 페이지를 사용하던게 1개의 페이지만 사용하게 되고 2개의 페이지를 추가로 확보할 수 있게 됩니다.

![11](https://github.com/user-attachments/assets/459cb6fb-eb09-4a69-b705-dc0588565b93)


그 이후에 딕셔너리가 다시 엑세스되면 원래 크기로 돌아가게 됩니다.

## Memory warnings

앱에서 뜨는 Memory Warning이 항상 해당 앱이 원인인 것은 아닙니다. 메모리가 부족한 기기에서 전화를 받거나 메시지를 보내거나 등등해도 해당 경고가 나타날 수 있습니다.

그러면 다 날리면 되는 거 아닌가..? 생각할 수 있지만..

```swift
override func didReceiveMemoryWarning() {
	cache.removeAllObjects()
	super.didReceiveMemoryWarning()
}
```

위 상황을 예시로 들면 다 지우기 위해 딕셔너리를 엑세스하는 순간

![11](https://github.com/user-attachments/assets/7c7ee848-7894-4e91-9720-2271b0d8a941)

아래처럼 더 많은 페이지를 사용하게 됩니다.

![10](https://github.com/user-attachments/assets/e5673b93-eb04-4a07-9ae8-e4fa1b1cec84)

메모리가 제한된 환경에서 오히려 메모리를 더 쓰는 상황이 되어버리죠..

캐싱에 대해서 중요한 점을 언급해자면..

1. 메모리 압축기와 캐싱 간의 균형 유지

캐싱을 할 때 우리는 실제로 CPU가 반복 작업을 하지 않도록 하는 것이지만, 너무 많이 캐싱하면 모든 메모리를 사용하게 되고 결국 시스템에 문제가 생길 수 있습니다. 메모리 압축기와 캐싱 간의 균형을 유지하며 캐싱할 데이터와 다시 계산할 데이터를 적절하게 조율해야 합니다.

**압축을 너무 많이 하면?**

- 메모리는 절약되지만, 압축 해제 시 오히려 더 많은 메모리를 사용할 수 있음.
- CPU 사용량도 증가할 수 있음.

**캐시를 너무 많이 하면?**

- CPU 작업은 줄어들지만, 캐시가 메모리를 너무 많이 차지해서 시스템이 느려질 수 있음.

1. 딕셔너리 대신 NSCache 사용

NSCache는 캐시된 객체를 저장하는 스레드 안전한 방법이므로 더 권장됩니다. 그리고 NSCache는 메모리를 자동으로 제거할 수 있어(purgeable) 메모리가 제한된 환경에서 더욱 효과적으로 동작합니다.

처음에 Dirty, Compressed, Clean에 대해서 설명을 했습니다.

![12](https://github.com/user-attachments/assets/9daacdf6-a02d-441b-a0a8-2ccc60ef6fd0)

그런데 사실 Memory footprint에 대해서 이야기할 때는 Clean은 중요하지 않습니다. (큰 영향을 미치지 않습니다)

![13](https://github.com/user-attachments/assets/cbbc4592-377e-4d35-a9a4-bca6f8217c35)

모든 앱에는 Footprint Limit가 있습니다.

사실 이 Limit는 상당히 높긴 하지만 기기에 따라서 Limit가 변경됩니다.

1GB 기기에서는 4GB기기에서처럼 많은 메모리를 사용할 수 없기 때문이죠!

Extension 프로그램도 Limit가 있지만 Footprint가 훨씬 낮으므로 Extension App을 사용할 때는 항상 주의해야 합니다.

풋프린트를 초과하면 `EXC RESOURCE EXCEPTION` 예외가 발생합니다.

<img width="759" alt="14" src="https://github.com/user-attachments/assets/d58218b8-ecb2-480c-a058-5f3829db8499" />

## Footprint 프로파일링
![15](https://github.com/user-attachments/assets/eb839a0d-1f61-4ef4-a3bd-a66a743fb12c)

가장 흔하게 많이 사용하는 도구는 Xcode 메모리 게이지입니다.

다음으로 소개하는 도구는 당연히 Instruments입니다.

![16](https://github.com/user-attachments/assets/e9c8fc78-aaf8-4210-8d69-17c041499b5e)

Instruments는 footprint를 조사하는 여러 가지 방법을 제공합니다.

(Allocations, Leaks, VM Tracker, Virtual memory trace)

이름에서 알 수 있는 것처럼 Allocation은 힙 할당을 프로파일링하고 Leak은 메모리 누수를 확인할 수 있는 것처럼 느껴지는데,, 뒤에 두 개는 익숙하지 않을 수 있다.


VM Tracker은 앞에서 설명한 Dirty 메모리와 Compressed 메모리를 프로파일링하는 방법을 제공한다.

(여기서 Swap이라고 뜨는데 이게 Compressed를 말한다)

Virtual memory trace은 앱과 관련된 가상 메모리 시스템의 성능을 자세하게 보여줍니다.

![17](https://github.com/user-attachments/assets/15238421-7c8a-4e69-b7f2-fb7e94fb83c0)

자세히 보면 zero fill 같은 것을 확인할 수 있다.

XCode 내부적으로 Memgraph 파일 형식을 사용해서 앱의 메모리 사용에 대한 정보를 저장하는데, 이걸 추출해서 터미널에서도 사용할 수 있습니다.


![18](https://github.com/user-attachments/assets/64cd17b4-5861-4be5-846c-712af373627f)


```
vmmap --summary App.memgraph
```

![19](https://github.com/user-attachments/assets/8f6bb3c7-930b-4b6c-868a-c5c72f238e43)

그러면 이렇게 세부 정보를 출력하게 됩니다.

여기서 Dirty 메모리와 Swapped 메모리가 중요한데 Swapped 메모리는 데이터를 압축한 후 크기가 아니라 압축되기 전 크기를 나타냅니다.

더 딥하게 파고 싶다면 —summary를 제외하고 다음과 같이 입력하면 거의 모든 내용을 얻을 수 있습니다.

```
vmmap App.memgraph
```

## Image

이미지에 대해서 들어가기 전에 가장 중요한 것은 이미지는 메모리 사용량이 파일 크기가 아니라 해상도랑 관련이 있다는 것이다.

예를 들어서 아래와 같은 이미지가 있다. 아래 이미지는 파일 크기는 590KB다.

![20](https://github.com/user-attachments/assets/240ce0bb-240f-47ed-b9b8-6491f87d7cce)

하지만 실제로 얼마나 많은 메모리를 사용할까?

정답은 10MB다.

그 이유는 너비 픽셀 수에 높이 픽셀 수를 곱하면 2048x1536이고 픽셀당 4바이트((Red, Green, Blue, Alpha) 4채널)를 곱하면 약 10메가 바이트가 되기 때문..

iOS에서 이미지가 작동하는 방식을 알아보자.

여기에는 로드 → 디코드 → 렌더 단계가 있다.

<img width="876" alt="21" src="https://github.com/user-attachments/assets/b22ecb2f-d301-4940-b10a-7b12bc343142" />

### 로드(Load)

이 단계에서는 압축된 590KB JPEG 파일을 메모리에 로드합니다.

### 디코드(Decode)

이 단계에서는 해당 JPEG 파일을 GPU가 읽을 수 있는 형식으로 변환합니다.

이제 압축을 풀어야 하니깐 10MB가 됩니다. 디코딩까지 완료되었다면 언제든 렌더링할 수 있는 상태가 됩니다.

![22](https://github.com/user-attachments/assets/8e7be655-e948-40e9-998c-fb2757f84fc0)

위에서 4바이트를 Red, Green, Blue, Alpha에서 얻었다고 했는데 이건 가장 일반적인 포맷입니다. 그러나 더 큰 포맷도 사용할 수 있습니다.

![23](https://github.com/user-attachments/assets/8e2bacb6-1932-4229-b340-a9234e293526)

iOS는 와이드 포맷으로 렌더링할 수 있고 이를 위해 픽셀당 8바이트를 사용하므로 이미지 크기가 두 배로 증가하게 됩니다.

반대로 더 작은 것도 있습니다.

![24](https://github.com/user-attachments/assets/6dae4361-9d1c-42f4-98a5-0669421d0509)

이 포맷은 휘도와 Alpha 8 포맷이 있습니다.

이 포맷은 회색조와 알파값만 저장할 수 있습니다.

이것보다 더 작은 것도 있고 이를 Alpha 8 Format이라고 합니다.

![25](https://github.com/user-attachments/assets/d89f2885-fb99-4705-8ec5-b9c8f917b4f1)

픽셀 하나당 1바이트 즉 1개의 채널만 존재합니다.

따라서 SRGB보다 75% 적은 메모리를 차지합니다.

mask나 단색 텍스트에 적합하며 메모리를 크게 절약할 수 있습니다.

이렇듯 1바이트에서 최대 8바이트까지 범위가 상당히 넓은데 어떻게 올바르게 선택할 수 있을까?

사실 아무것도 선택하지 않는게 방법일 수 있다.

즉, 형식 자체가 사진을 선택할 수 있게 해야 한다.

iOS가 시작될 때부터 있었던 UIGraphics BeginImageContext With Options API(항상 픽셀당 4바이트 형식이기 때문에) 사용하지 않고 대신 UIGraphics ImageRenderer (사용자에게 적합한 그래픽 형식을 자동으로 선택) 형식으로 변경하면 용량을 줄일 수 있다.

마스크에 원을 그린다고 가정해 보겠습니다.

![26](https://github.com/user-attachments/assets/abe580f1-35c2-4c4d-ba30-697ca233abcb)

검은색 원을 그리기 위해서 픽셀당 4Byte 형식의 데이터를 얻습니다.

대신에 새로운 API로 바꾸면 픽셀당 1Byte 이미지를 얻습니다.

![27](https://github.com/user-attachments/assets/3ddec3d9-9dbd-4c26-8cd1-88ca23d7250d)

즉 메모리 사용량이 75% 감소 된다는 의미입니다.

```swift
// Make circle render blue, but stay at 1 byte-per-pixel image
let imageView = UIImageView(image: image)
imageView.tintColor = .blue
```

또한, 이 마스크를 재사용할 때, 색상을 변경하면 메모리를 추가로 할당하지 않고도 색상을 변경할 수 있습니다.

> 원래 만든 컬러가 1바이트 포맷이고 이 이미지를 UIImage에 넣고 이미지를 변경하는게 아니라 렌더링할 때 색만 곱해서 보이는 색만 바꾸는 것이기 때문.
그러니깐… GPU가 렌더링할 때 마치 파란색처럼 보이게 처리하는 것…
새로운 이미지를 만들지 않으니깐 메모리를 추가적으로 사용하지 않음!
> 

즉, 검은색 원뿐만 아니라 파란색, 빨간색, 녹색 원으로도 사용할 수 있습니다.

## Image downsample

우리는 썸네일같은걸 만들때는 보통 이미지 크기를 줄이고 싶어 한다.

단지 UIImage를 사용해서 ‘축소’하는건 비효율적입니다.

그 이유는 UIImage가 내부적으로 좌표 변환(Coordinate Space Transform)을 수행하기 때문입니다.

그리고 앞에서 본 것처럼 UIImage를 사용하면 메모리에 있는 전체 이미지를 압축 해제하게 됩니다.

대신에 우린 ImageIO 프레임워크를 사용할 수 있습니다.

### ImageIO

ImageIO를 사용하면 스트리밍 API를 활용하여 이미지를 다운샘플링할 수 있습니다.

그리고 결과 이미지의 Dirty Memory비용만 지불하게 됩니다.

만약에 아래처럼 디스크에서 파일을 가져오는 코드와 UIImage를 이용하여 더 작은 사각형을 그리는 코드가 있을 때 이 코드는 큰 스파이크가 발생(메모리 사용량이 급증)하게 됩니다.

![28](https://github.com/user-attachments/assets/dcfa5850-d8d3-4972-bd3f-c776c9b2b6d1)

하지만 ImageIO로 전환하면 파일을 디스크에서 로드하는건 동일하지만, 스파이크는 발생하지 않습니다. 실시간 스트리밍 API를 활용하기 때문이죠!


![29](https://github.com/user-attachments/assets/35e0988a-b871-4aa7-bccc-94d117b54762)

### **Background**

일반적으로 보이지 않는 큰 리소스는 언로드(unload)하는 것이 좋습니다.

이를 수행하는 방벙이 두 가지 있습니다.

1. 앱 수명 주기 활용하기

앱이 백그라운드로 전환되거나 다시 포그라운드로 복귀할 때 앱 라이프 사이클 이벤트를 활용하면 효과적으로 리소스를 관리할 수 있습니다.

주로 View에서 적용되는데 그 이유가 ViewController는 화면이 바뀔 때 감지할 수 있지만 뷰자체는 감지를 못하기 때문에 개발자가 직접 메모리에서 제거해야하기 때문! 따라서 Noti를 받아서 관리해 주거나 등등..처리가 필요합니다.

1. 뷰컨트롤러 라이프 사이클 활용

이건 위와 비슷하니 생략하겠습니다!
