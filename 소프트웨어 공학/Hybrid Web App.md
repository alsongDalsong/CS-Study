## 궁금증의 시작
![image](https://github.com/user-attachments/assets/19d3ec46-83d8-4bed-8914-21c5cb604add)

생각보다 많은 애플리케이션들이 네이티브 앱에다가 웹 화면을 띄우는 하이브리드 웹앱 형태로 존재합니다. 이러한 하이브리드 웹앱 형태는 여전히 크고 작은 회사들로 부터의 꾸준한 수요를 확인할 수 있습니다.

따라서 웹앱은 무엇이고, iOS에서 웹을 화면에 띄우는 방법은 무엇인지, 비슷하지만 다른 개념은 무엇인지, iOS에서 하이드리드 웹앱은 어떻게 구현되는지 알아보고자 합니다.

## WebRTC
처음 생각이 난 개념은 마스터클래스에서 스쳐 지나갔던 것 같은 `WebRTC` 입니다.

### 사전지식
#### 1. IP Routing
- 라우팅: OSI(Open Systems Interconnection) 중 3 계층, Network Layer에서 라우팅을 사용합니다. 용어 그대로 전기적 신호의 경로를 결정하는 것이 라우팅입니다.

![image](https://github.com/user-attachments/assets/3fd89ed3-c8c2-47d3-b30b-e6491dab5701)

- 인터넷을 사용하는 기기는 고유한 IP를 부여받고, 라우팅에서 이 IP를 가지고 경로를 결정합니다. 여기서 IP는 MAC 주소와는 다르게 고유하지 않고 변경이 가능합니다.
- 노트북과 핸드폰 모두 IP 주소를 가지고 있지만, 보안을 위해서 오픈하지 않고 중간 중재자 공유기를 통해 관리합니다.
- 공유기는 Public IP를, 노트북과 핸드폰과 같은 EndPoint Device는 Private IP를 가지고 있습니다.

![image](https://github.com/user-attachments/assets/7e2b57e9-f719-48dd-a9f9-39ca0525661a)

#### 2. WebSocket
- 웹 소켓: 서버와 클라이언트 간의 메세지 교환을 위한 통신 규약(프로토콜)입니다.

![image](https://github.com/user-attachments/assets/a4cdaca8-3219-4769-a8ae-ed8708cd825e)

- 보통 서버와 클라이언트가 있을 때 `Request/ Response`를 통해 서로 데이터를 교환하지만, 느린 방식이므로 아예 서버와 클라이언트 사이 Connection을 두어 하나의 link를 통해 데이터를 주고 받습니다.
- 웹 소켓은 양방향 통신(Full-Duplex)이 가능하고, 실시간 네트워킹(Real Time Networking)이 가능합니다.

### WebRTC 개념
- Web(or App) Real Time Communication
- WebRTC는 통신하는 동안 서버가 중간에 없이 P2P(peer 2 peer)가 가능한 인터넷 프로토콜입니다.

![image](https://github.com/user-attachments/assets/d13fd7ca-8b9d-4a9c-8abb-587686cad5f6)

- 제 3자 소프트웨어 설치 없이 종단 간 데이터 공유 및 화상 회의를 가능하게 하는 방법의 하나 입니다.
- 실시간 음성, 화상 회의, 파일 교환등에 사용됩니다.

#### Signaling
- P2P 연결을 위해서 처음에는 서버 중개자가 존재합니다.

![image](https://github.com/user-attachments/assets/a7e0b61f-8dad-4c7a-b014-ca615560f321)

- A가 B와 통신하고 싶은 경우, 서버에 Video인지 Chat인지 결정하고 서버에 요청하게 됩니다.
- 이러한 서버 중개자를 `Signaling Server`라 하고, A와 B가 OK를 하게 되면 IP 정보를 교환하게 됩니다.
- 여기서 원래 IP 정보는 보안에 민감하여 서로 바로 교환하면 안되기에, Signaling Server가 이를 관리해주는 역할을 담당하게 됩니다.
- 실제 코드에서는 `PeerConnection`이라 부르고, 이를 통해 `SessionDescription을` 서로 주고 받은 뒤, 다음부터는 서버 없이 연결이 되는 구조입니다.

#### Signaling Server
- Signaling Server와의 연결 과정에는 `Stun, NAT, Turn`이 있습니다.

![image](https://github.com/user-attachments/assets/70f35259-373d-46f4-9024-22470d98cad0)

- `Stun Server`는 Public IP를 저장하고 있고, Peer 간의 직접 연결을 막는 등의 라우터 제한을 결정하는 프로토콜입니다. 클라이언트는 Stun Server에 접근이 가능한지 물어보는 형태로 동작합니다.
- `NAT(Network Address Translation)`은 라우터가 가지고있는 Public IP와 각 단말들의 Private IP 중 다른 단말들에게 Public IP를 알려줄 때 사용되는 용어입니다.
- `Turn Server`는 일종의 보험 용도를 하는 서버로, Public IP를 각 단말기가 가지고 있고, P2P 연결을 시도하려고 했지만 실패한 경우 `Turn Server`가 중개하여 다시 연결할 수 있도록 하는 역할을 담당합니다.

### WebRTC 핵심 API
#### MediaStream
 - 카메라와 마이크 등의 데이터 스트림에 접근하는 API
 - getUserMedia() 메서드를 통해 액세스합니다.

#### RTCPeerConnection
- 암호화 및 대역폭을 관리하고, 오디오와 비디오를 연결하는 API
- getUSerMedia에서 스트림을 연결한 뒤, peer connection으로 연결합니다.
- RTCPeerConnection을 이용하요 getUserMedia로 호출한 비디오를 전달하게 됩니다.

#### RTCDataChannel
- WebSocket과 같은 비슷한 역할로 데이터를 P2P 통신으로 전달하는 API


## Web App
네이티브 앱 처럼 보이고, 기능 또한 앱과 동일하게 구현되지만 웹 기술을 활용하여 만들어진 앱입니다. 네이티브 앱과 잘리 웹 앱은 기본적으로 웹 기반의 HTML, CSS, Javascript를 활용하여 인터넷 브라우저를 기반으로 작동됩니다.

![image](https://github.com/user-attachments/assets/10eb840a-8193-450a-8a1a-a9498add235d)

앱 없이 인터넷을 통해 구동되는 것을 보면 일반 모바일 웹과 똑같다 느낄 수 있지만, 차이점이 있습니다. 모바일 웹의 경우 PC를 기준으로 제작된 뒤 모바일 화면 규격에 맞게 폰트나 이미지등을 바꾼 것이지만, 웹앱의 경우는 처음부터 모바일을 기준으로 제작되기에 사용자에게 훨씬 편안한 환경을 제공합니다.

또한, 모바일 웹은 화면의 일부분이 바뀔 때 전체를 서버에서 새롭게 불러오는 풀 브라우저(Full Browsing) 방식을 사용하고, 웹 앱은 변경이 필요한 부분만 바꾸는 단일 페이지 방식(SPA, Single Page Application)을 사용합니다. 따라서, 모바일 웹이 속도가 더 느립니다.

#### 웹 앱의 장점
- 인터넷 브라우저를 기반으로 작동하기 때문에 별도의 앱을 설치하지 않아도 됩니다.
- 표준 웹 언어로 만들 수 있기 때문에 상대적으로 제작 비용이 저렴하고 개발 기간도 짧습니다.
- 수정사항이 생겨도 앱 마켓의 심사를 거치지 않아도 되기 때문에 업데이트 속도가 빠릅니다.

#### 웹 앱의 단점
- 디바이스에 접근 권한이 없기 때문에 카메라나 음성 인식 등의 기능을 활용할 수 없습니다.
- 앱 설치 대신 브라우저 실행 및 URL 입력이나 별도의 링크 클릭 등의 과정을 거쳐야 합니다.
- 네이티브 앱에 비해 상대적으로 구동 속도가 느리고 안정성도 떨어집니다.


## Hybrid App
하이브리드 앱은 네이티브 앱과 웹 앱의 방식을 모두 사용합니다. 앱의 화면이나 기능 등 콘텐츠 영역은 웹 기반으로 제작하되 겉모습은 앱 마켓 등록과 설치를 위해 네이티브 앱으로 포장합니다. 두 가지 개발 방식을 모두 이용하므로 웹과 앱의 API를 모두 사용할 수 있습니다. 따라서, 웹 앱에서는 불가능했던 디바이스 자체 기능에 접근할 수 있습니다.

![image](https://github.com/user-attachments/assets/672a4840-07fa-4953-9abd-06fda824fdb1)

#### 하이브리드 앱의 장점
- 웹 기술을 기반으로 제작되지만, 모바일 API도 사용할 수 있으므로 디바이스 자체 기능을 활용할 수 있습니다.
- 네이티브 앱에 비해 개발 비용 및 시간을 절약할 수 있습니다.
- 한 번 개발해두면 패키징을 바꾸는 방식으로 여러 플랫폼에 대응할 수 있습니다.

#### 하이브리드 앱의 단점
- 네이티브 앱 개발 지식이 필요합니다.
- 브라우저 성능이 떨어지면 앱 구동 속도가 저하됩니다.
- 네이티브 앱에 비해 디자인의 자유도가 떨어집니다.


## Native & Hybrid App
React Native, Flutter, XAMARIN, NativeScript 등 하이브리드 앱을 만드는 프레임워크는 많습니다.

![image](https://github.com/user-attachments/assets/c95bd46f-72a2-41ec-ac12-57ec2ad65c82)

이런 계열의 프레임워크로 하이브리드 앱을 만들게 되면, 기본적으로 플러그인 형태로 웹뷰와 네이티브 디바이스를 연결해서 접근할 수 있게 지원해줍니다. 하지만, 이런 프레임워크를 사용하게 되면 몇 가지 문제 상황을 마주하게 됩니다.

### 1. OS 버전이 최신 업데이트 될 때 마다 해당 플러그인 역시 업데이트되야 합니다.
사용자가 OS 업데이트를 진행한 뒤 발생한 문제를 해결하려 해도, 플러그인 패치가 되지 않으면 문제를 해결하지 못하고 기다려야 합니다.

### 2. 하위호환 OS 버전을 고려해야 하고, 하위 버전이 특정 해 이전에 출시된 버전인 경우 플러그인을 쓰지 못하는 경우가 발생합니다.
결국 플러그인 마다 버그가 존재하고, 버그에 따라 특정 버전 이하에서는 해당 플러그인을 사용하지 못하는 경우가 발생합니다. 결국 하위 호환을 포기하거나 하위 버전을 강제로 올려야 하는 경우가 발생합니다.

### 3. 경우에 따라 새로운 플러그인 형태의 브릿지가 필요할 수 있습니다.
프레임워크에 맞게 플러그인을 개발해야하는데, 결국 이 부분은 네이티브 개발자가 작업을 해줘야 합니다. 즉, 네이티브 개발자 없이 앱 개발을 하기위해 하이브리드 앱 프레임워크를 선택했는데 결국 네이티브 개발이 필요한 상황이 발생합니다.


## 웹 뷰와 네이티브를 연결하는 방법
#### 1. Scheme을 이용한 방법
- Scheme을 정의하고 해당 scheme을 클릭하면 앱이 구동되게 하거나 앱 내에서 동작을 호출하는 방식입니다. (`location.href = “fitpet://fitpetmall/main”`)
- 웹뷰 내에서 Scheme을 호출하면 네이티브 내부 동작(페이지 이동, 카메라 호출 등)을 호출할 수 있습니다.또한 Scheme에 Query String을 붙여 데이터도 전달이 가능합니다.
- 하지만, 호출 이후 결과를 받을 방법이 없습니다. 동작이 제대로 되었는지, 동작 후 별도의 수행을 하는지 알 수가 없습니다.

#### 2. 웹뷰와 네이티브를 연결하는 브릿지를 만드는 방법
- 모든 네이티브 동작을 정의할 수 있고, 호출할 수 있습니다.
- Scheme과 달리 쌍방향 통신(결과를 받는 통신)도 가능합니다.
- 다만, 웹뷰와 네이티브 둘 다 동작에 대한 정의가 구현되어 있어야 합니다. 그리고 만약 하나의 앱 안에서 웹뷰의 Domain이 여러 개인 경우 같은 기능을 다른 이름으로 구현하는 경우를 방지하기 위해 브릿지가 정의된 파일을 공통으로 관리해야 합니다.

 ![image](https://github.com/user-attachments/assets/253d8184-611d-4ad4-a491-6ee5438dee1f)

> [하이브리드 앱 브릿지(Bridge) 리팩토링 후기](https://fitpet.medium.com/%ED%95%98%EC%9D%B4%EB%B8%8C%EB%A6%AC%EB%93%9C-%EC%95%B1-%EB%B8%8C%EB%A6%BF%EC%A7%80-bridge-%EB%A6%AC%ED%8C%A9%ED%86%A0%EB%A7%81-%ED%9B%84%EA%B8%B0-fac22fffec81)
> 
> [웹앱 브릿지 통신 JS to iOS](https://spencer.tistory.com/37)

## iOS WebView
SwiftUI에서 제공하는 단순 `WebView`, 과거에 사용하던 UIKit의 `UIWebView`, WebKit에서 제공하는 `WKWebView`, Safari의 확장판인 `SFSafariViewController` 등 iOS는 다양한 웹뷰를 제공합니다.

여기서 UIWebView는 Deprecated 되었고, SwiftUI의 WebView는 기능이 한정적입니다. Safari는 좋지만, 기존 네이티브 앱과 분리된 느낌이 들고 Safari의 기본 특징을 유지해야 하기 때문에 하이브리드 웹 앱에서 사용하기에는 적절치 않습니다.

따라서, 커스텀이 자유로운 WKWebView가 많이 사용됩니다.

![image](https://github.com/user-attachments/assets/5dfc4869-4b6b-4467-9660-75e466028850)

### WKWebView
![image](https://github.com/user-attachments/assets/4ea4f52c-da6e-431d-8dee-9a83eb135fde)

> [WKWebView를 통해 iOS Native와 웹뷰 사이 통신하기](https://medium.com/hcleedev/swift-react-wkwebview%EB%A5%BC-%ED%86%B5%ED%95%B4-native%EC%99%80-%EC%9B%B9%EB%B7%B0-%EC%82%AC%EC%9D%B4-%ED%86%B5%EC%8B%A0%ED%95%98%EA%B8%B0-23f36984c5af)
> 
> [WKWebView를 사용해서 웹과 통신하기](https://hilily.tistory.com/78)
>
> [웹뷰<->네이티브 통신 구현하기](https://lxxyeon.tistory.com/139)
