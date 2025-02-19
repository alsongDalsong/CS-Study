

## Wireless 및 Mobility 네트워크

Mobile Network(Wireless Network) 라고도 함 . 

![image](https://github.com/user-attachments/assets/46b6ca52-d9a0-4a4a-b6db-76df540bf465)

## wireless hosts

![image](https://github.com/user-attachments/assets/a9cdb466-984d-4acb-989b-ea60c9358d21)

무선 네트워크는 유선 네트워크의 접속 지점이 무선으로 전환된 형태입니다.

* Access Point(AP): 기지국 또는 무선 송수신기로, 유선 네트워크와 연결되어 있으며 단말(노트북, 스마트폰 등)과 무선 통신을 담당합니다.
* 노트북, 스마트폰 등을 보고 wireless host라고 부릅니다. 노트북은 주로 한 장소에 고정되어 사용하는 반면, 스마트폰은 이동성이 뛰어납니다. 무선이라고 반드시 이동성을 의미하는 것은 아닙니다.
* Mobility(이동성): 단말이 네트워크 영역 내에서 이동하는 것을 의미합니다. 초기에는 이동에 따라 IP 주소가 변경될 것으로 예상하여 Mobile IP 개념이 제안되었지만,
                    현대의 셀룰러 네트워크에서는 동일 지역 내에서 하나의 IP 주소를 유지하는 경우가 많아 큰 문제가 되지 않습니다.



 

## wireless link

![image](https://github.com/user-attachments/assets/f5928501-7626-434a-9784-fc8bafaf7b87)


무선 링크는 단말(모바일 호스트)과 기지국(또는 AP) 사이의 무선 통신 경로를 의미합니다.

* 기지국이 커버하는 영역을 cell이라고 하며, 여러 단말이 동시에 접속할 때는 multiple access 프로토콜을 사용해 자원을 할당합니다.
* 무선 링크의 전송 속도는 단말의 위치나 주변 환경(예: 기상 상황, 동시에 사용하는 접속자의 수)에 따라 변화하며, 신호 대 잡음비(SNR)에 따라 최적의 전송률이 결정됩니다.

 
 

## 무선 네트워크 구분

![image](https://github.com/user-attachments/assets/cc66bbf0-ea30-4240-91ee-d3ac1a94f271)

무선 네트워크는 크게 Cellular Network(셀룰러 네트워크)와 WiFi로 구분할 수 있습니다.

WiFi는주로 실내 환경에서 사용되며, 일반적으로 약 10~30m 정도의 커버리지를 제공합니다.

* 802.15: 블루투스 등 개인 통신 영역(PAN)을 위한 표준으로, WiFi보다 짧은 범위를 지원합니다.
* 802.11ac 가 보통 우리가 쓰는 와이파이

셀룰러 네트워크는 5km에서 20km에 이르는 넓은 영역을 커버하며, 2G부터 5G까지 진화해 왔습니다.

* 2G: FDMA나 TDMA 기반의 음성 중심 네트워크
* 3G: 음성과 데이터를 동시에 지원하며 WCDMA, HSPA 등의 기술 사용
* 4G (LTE): 모든 트래픽이 IP 기반으로 전환되어 데이터 중심으로 운영되며, OFDM(또는 OFDMA) 기술을 채택
* 5G: 최대 20Gbps의 전송 속도를 제공 (이렇게 얘기하지만 기지국 한 개에서 단말 하나가 좋은 환경에서 달성하는 수치이다.)


(참고) 802라는 숫자는 국제전기전자 협회 IEEE가 통신 표준을 만들 때 802를 붙이고 뒤에 숫자를 붙인다.
이 때 숫자가 11이면 무선이다.
뒤에 알파벳이 붙는데 b는 최대 11Mbps 정도 나오는, 최초에 나왔던 무선랜이다.
그 다음이 a하고 g이다. 그 다음은 n (450Mbps). 
그 다음 ac(대략 1Gbps) 
지금 우리가 쓰고 있는 것이 대부분 802.11 ac이다.

 
 

## infrastructure mode

![image](https://github.com/user-attachments/assets/0cc5beb7-3a66-4e65-b596-a9e80d0ff337)

infrastructure mode란 기지국 통해서 통신하는 방식입니다.

이동 중에 단말이 다른 AP로 전환되는 과정을 핸드오버(handoff)라고 하며, 
옛날 2G 시절에는 전화하면서 이동하다보면 큰 네트워크가 바뀌는 영역에서 끊기는 지점이 있었고
지금은 LTE나 5G에서는 handoff가 거의 해결되었다.
 

## ad hoc mode

![image](https://github.com/user-attachments/assets/69307dd4-f023-4d5d-8994-6c739f85fac8)

기지국 없이 단말들이 서로 직접 네트워크를 구성하는 방식입니다.
 
스스로 망을 만들어서 통신하는 형태이다. 

상용화에는 여러 기술적, 경제적 이슈(배터리 소모 문제 등)로 인해 제한적인 사례만 존재합니다.

재난 상황이나 일부 자율주행 자동차 네트워크 등에서 한정적으로 활용될 가능성이 있습니다.
 

## IEEE 802.11 Wireless LAN


![image](https://github.com/user-attachments/assets/a34ee5a0-8750-4f14-aeed-27b69286ce0f)

WiFi에서는 기지국 대신에 Access Point (AP)라고 부릅니다. (집에 있는 유무선 공유기가 AP)
 
AP가 생성하는 하나의 무선 영역을 BSS라고 부릅니다.
 
AP들이 hub, switch, router와 연결되어 인터넷에 접속합니다.


## 802.11: Channels, association

처음에 WIFI에 접속하려면 AP를 살펴보는 채널스캐닝이 필요합니다.


## 802.11 : passive/active scanning

 ![image](https://github.com/user-attachments/assets/3aa9f0ea-ae59-4a3f-86db-123e182e8d97)

단말이 주기적(100ms)으로 전송되는 beacon frame을 수신하여 AP 목록을 확인합니다.


>  passive scanning : beacon을 하나 받아서 list가 보이는 것.

 
![image](https://github.com/user-attachments/assets/16cf6ef7-2010-481c-abbb-1e33bfb4c847)

 
> active scanning : 단말이 Probe Request(이러한 이름의 와이파이(AP)가 있는지 broadcast로 SSID(와이파이 이름)를 포함하여 질의)를 전송하여 AP로부터 Probe Response를 받아 네트워크를 탐색합니다.

 
탐색된 AP중에서 적당한 것을 내부적으로 선택한다. 

선택한 AP로부터의 Beacon메세지를 수신하여 이 AP가 지원하고 있는 동작값(속도, 변조방법, 암호방법 등)들을 추출 합니다.
 
[참고](https://wpgur.tistory.com/83)
 

## IEEE 802.11 : multiple access

802.11에서 multiple access는 CSMA(carrier sense multiple access)를 사용합니다.
 
![image](https://github.com/user-attachments/assets/d69d57ec-9875-4990-bede-93d4afdf2607)

CSMA는 전송하기 전에 sensing을 함으로써 누가 사용하고 있는가를 체크 해 보고

누군가 전송하고 있다면 데이터를 보내지 않는다는 것이었습니다. 그러더라도 충돌은 일어 날 수 있는데,

충돌이 일어나면 다시 재전송을 해서 통신을 완료하는 것이 CSMA, 그리고 CSMA/CD의 공통적인 특성이었습니다.

그런데 만약 sending 노드가 다른 노드의 통신을 들을 수 없는 경우라면 또는 sending 노드가 collision이 발생하는 것을 감지 할 수 없는 경우라면

CSMA와 CSMA/CD가 가지는 기본적인 원리가 지켜지지 못합니다. 이런 경우가 무선에서 발생합니다.

### 장애물에 의해서 발생

![image](https://github.com/user-attachments/assets/7bfadc16-91bf-40e8-8cbc-ca104f0211f4)

A와 C는 B와 통신 할 수 있는 거리에 있고, A와 C의 사이에는 장애물(산)이 있습니다.

그래서 A와 C는 서로 감지를 할 수 없습니다. C가 B한테 한창 전송 중이라 하더라도 

A는 감지하지 못하므로, B에게 전송을 한다면

B의 입장에서는 C에서 오는 신호와 A에서 오는 신호가 충돌이 발생합니다.

### 감쇠

무선 링크의 특징 중에서 어느 순간부터 급격하게 감쇄한다는 점이 있습니다.

A가 보내는 신호는 B까지는 시그널이 깨끗하게 도달하다가 B를 지나면서 Signal to noise ratio가 굉장히 낮아져서

C한테는 이 신호가 잡음처럼 들리는 그런 경우가 됩니다.

이런 경우를 보통 Hidden terminal Problem이라고 부릅니다.

CSMA와 CSMA/CD는 무선환경에서 적용하기 어렵습니다. 그래서 CSMA/CA라는 기술이 나오게 되었습니다.

## CSMA/CA

![image](https://github.com/user-attachments/assets/ef62e5e3-7a25-42b2-96eb-a6e97820767e)

채널이 idle한지 감지 하기 위해, DIFS라는 시간만큼 일단 frame을 보낸다.

만약 누군가가 보내고 있다고 판단되면, random backoff부터 시작한다.
채널이 idle해지면 random backoff time을 count down한다.
그래서 타이머가 0이 되면 전송한다.
성공적으로 보내면 receiver는 SIFS라는 시간동안 기다렸다가 ACK를 보낸다. 
전송했는데 ACK가 안 오면 충돌이 났다고 판단하여 random backoff를 2배 늘란다.
0~3 사이에서 뽑았던 것을 0~7 사이에서 뽑는다. 또 count down하고 전송한다.

 
![image](https://github.com/user-attachments/assets/6ea621de-3aa7-40b7-b8e7-094731a79efb)

![image](https://github.com/user-attachments/assets/e4185d7a-7343-4fd0-a275-e898c441a026)


 A하고 B가 AP한테 보내고 있는 상황이다. A하고 B는 서로 모른다.

1. A와 B가 AP에 RTS 보냈는데 충돌이 났고, 다시 A만 RTS를 보낸다. B는 이를 모른다.

2. AP가 CTS라고 하는 것을 뿌려준다.

3. B는 CTS를 받고 누군가가 보내고 있음을 알게 된다. ACK까지의 시간이 담겨있어, 그동안 기다리게 된다.

4. RTS, CTS 성공적으로 보냈으면, A는 AP에게 데이터를 보낸다.

5. AP는 ACK를 보낸다.


## Cellular Network

![image](https://github.com/user-attachments/assets/7f0fe8e1-b5ec-4cd1-8842-dc0b972f934f)

각각의 기지국들이 세포와 같이 있는 것 같아 기지국의 한 영역을 cell이라고 부릅니다. 
 
이런 기지국들이 모두 모여있는 것이 Mobile Switching Center이고  Mobile Switching Center는 유선망이 전화망과 연결되어 있는 구조

Multiple Acess에서는 하나의 채널에서 다른 신호를 어떻게 구분하는가가 가장 중요.
자원은 시간과 주파수, 코드로 이루어져 있는데 어떻게 분할해서 사용하느냐에따라서 FDMA인가 TDMA, CDMA로 나뉜다. 

FDMA가 1세대, TDMA가 2세대, CDMA가 3세대 셀룰라(우리나라의 경우 2세대에서도 쓰임), LTE부터는 OFDM이다이다. 

## 1G Network (FDMA 주파수 분할 다중 접속)


![image](https://github.com/user-attachments/assets/18b07f46-33db-4336-8938-4d7b4386ba68)

1세대(1G) 시스템은 음성 전용 통신 전용으로 설계된 아날로그 FDMA 시스템이었습니다.

인접 채널 간 간섭이 생길 수 있어 주파수 대역 사이에 보호 대역이 필요하여 효율적이진 못함


## 2G network architecture (TDMA 시분할 다중 접속)


![image](https://github.com/user-attachments/assets/9fac0fcd-91cc-4973-92cc-80508f121d88)

동일한 주파수 대역폭 안에서 시간축을 여러 구간으로 나누고 각 사용자가 자기에게 할당된 시간 구간을 다른 사용자의 시간 구간과 겹치지 않게 사용하는 방법

![image](https://github.com/user-attachments/assets/bbd0bc43-ad74-4c07-a2dc-77e5b7b53a84)

2G 네트워크의 구조는 기지국을 BTS라고 부르고, BTS들이 하나의 BSC에 묶여있고, MSC에 묶인 형태이다.


## 3G (voice + data) network architecture (CDMA 코드 분할 다중 접속)
![image](https://github.com/user-attachments/assets/ac259433-9456-4cf8-a5c7-27726671863e)

CDMA는 각 사용자가 고유의 확산 부호를 할당받아 신호를 부호화하여 전송하면 해당 확산 부호를 알고 있는 수신기에서만

부호화된 신호를 다시 복원할 수 있는 방식

![image](https://github.com/user-attachments/assets/9a327061-28bd-4390-8703-381abae58837)

3세대도 비슷하다. 기지국들이 controller에 묶여 있었고, MSC에 묶여있었다.
 
3세대부터는 Internet도 붙는다. 기존에는 전화망만 연결되었었는데 인터넷도 연결된다.


## 3G vs 4G LTE network architecture

![image](https://github.com/user-attachments/assets/08da65ea-eaf1-4439-9a9b-1a44f7fea51e)

 4세대 LTE가 되면서 전화망에 묶여있던 게 사라지고, 사실상 인터넷망이 되었다. 기지국들이 물려있는데,
 
 GateWay(GW) 라는 것이 기지국에 추가되었습니다.
 S-GW는 데이터 전송 경로를 형성하며,
 P-GW는 단말에 IP 주소 할당 및 QoS 관리 역할 수행한다.
 
 여러가지 이동단말들을 컨트롤하기 위한 MME같은 특별한 기능을 하는 서버가 들어간다.
 
 HSS는 모든 사용자의 이동성 관리와 보안 기능 담당합니다.

 
