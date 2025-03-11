## 오류 제어(**Error Control**)

TCP는 잘못된 세그먼트를 재전송해 신뢰성을 보장한다. TCP가 잘못된 세그먼트를 다음과 같은 방법을 통해 감지한다.

### 중복된 ACK 세그먼트를 수신했을 때

> 출처: [https://velog.io/@jjjoina/네트워크-전송-계층](https://velog.io/@jjjoina/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%A0%84%EC%86%A1-%EA%B3%84%EC%B8%B5)

세그먼트 전송 과정 중 세그먼트의 순서 번호 일부가 누락되었다면 중복된 ACK 세그먼트를 수신 받게된다. 이 때 송신 호스트는 ACK 세그먼트의 응답 번호를 통해 해당 세그먼트가 손실되었음을 감지한다.

### 타임아웃이 발생했을 때

TCP는 세그먼트를 보낸 후 일정 시간 동안 ACK를 수신 받지 못하면, 타임아웃이 발생하고, 타임 아웃이 발생하면 세그먼트가 손실되었음을 감지한다. 때문에 TCP 세그먼트를 송신할 때 재전송 타이머를 시작한다. 타이머의 카운트가 끝난 상황을 타임아웃이라고 한다.

### ARQ(Automatic Repeat Request)

TCP는 오류 제어를 통해 문제를 진단하고, 문제가 생긴 세그먼트를 재전송함으로써 신뢰성을 보장한다. 이를 **ARQ**라고 한다.

ARQ는 세 가지 방식이 존재하는데

- Stop-And-Wait ARQ
    - Stop-And-Wait ARQ는 송신한 세그먼트가 정상적으로 도착해 ACK를 수신받기 전까지 새로운 메시지를 보내지 않는 방식
    - 단순하면서 높은 신뢰성을 보장한다는 장점이 있음
    - 하나의 세그먼트를 전송할 때 마다 ACK를 수신 받아야하기 때문에 네트워크 이용 효율 및 성능 저하 문제가 있음
- Go-Back-N ARQ
    
    ![출처: https://techdifferences.com/difference-between-go-back-n-and-selective-repeat-protocol.html](attachment:b4c13c4c-6ad2-43f5-a80a-25ad004dc2fa:image.png)
    
    출처: https://techdifferences.com/difference-between-go-back-n-and-selective-repeat-protocol.html
    
    - 연속해서 세그먼트를 전송하는 **파이프라이닝(pipelining) 기술**을 사용해 여러 세그먼트를 한번에 전송
    - 만약 0,1,2,3,4 세그먼트를 동시에 전송 중에 2번 세그먼트가 문제가 발생한 경우 3,4 번이 정상적으로 도착했더라도 이를 폐기 후 2,3,4 세그먼트를 재전송한다.
    - 한번에 여러 세그먼트를 보낼 수 있어 네트워크 효율과 성능이 Stop-And-Wait ARQ 보다 높지만, 세그먼트에 오류 발생 시 이후 세그먼트를 모두 전송해야한다는 단점이 있음
- Selective Repeat ARQ
    
    ![출처: https://techdifferences.com/difference-between-go-back-n-and-selective-repeat-protocol.html](attachment:9f45224a-0bfd-4cbd-ad0f-aa2c9fda8848:image.png)
    
    출처: https://techdifferences.com/difference-between-go-back-n-and-selective-repeat-protocol.html
    
    - Go-Back-N ARQ와 동일하게 파이프라이닝 기술을 사용해 연속적으로 세그먼트 전송 가능
    - Selective Repeat ARQ는 Go-Back-N ARQ 단점을 보안한 ARQ 방식
    - 문제가 발생한 세그먼트만 재전송
    - 현대에 가장 널리 사용되는 ARQ 하지만, 만약 두 호스트 중 한 호스트라도 Selective Repeat ARQ를 지원하지 않을 시 Go-Back-N ARQ으로 동작
    - Selective Repeat ARQ의 지원 여부는 세그먼트 옵션 필드에 속한 SACK(Selective Acknowledgment) 필드를 통해 알 수 있다.

## 흐름 제어(**Flow Control**)

앞써 TCP는 Stop-And-Wait ARQ 방식의 네트워크 이용 효율 및 성능 저하 문제를 해결하기 위해 파이프라이닝 기반의 Go-Back-N ARQ 혹은 Selective Repeat ARQ를 사용한다고 언급했다. 하지만 두 ARQ 방식을 사용한다고 해도, 호스트는 한 번에 처리할 수 있는 세그먼트의 양이 있고, 그 이상의 세그먼트를 한번에 전송하면, 처리하지 못하고 손실될 수 있다.

수신 호스트는 **수신 버퍼**를 통해 수신된 세그먼트를 프로세스에 의해 처리되기 전에 임시로 저장한다. 즉, 수신 버퍼가 수용할 수 있는 크기보다 더 많은 데이터가 한번에 전송되면 손실 발생 가능성이 커진다. 이를 `버퍼 오버플로`라고 한다. 

<aside>
☝

수신 버퍼가 존재하는 것 처럼 송신할 세그먼트를 임시 저장하는 **송신 버퍼**도 존재한다.

</aside>

**흐름 제어**란, 송신 호스트가 수신 호스트의 처리 속도를 고려해 송수신 속도(한 번에 전송할 세그먼트의 수)를 적절하게 조절하는 것을 의미한다.

<aside>
📌

**정리**

- 파이프라이닝 기반으로 동작하는 ARQ는 수신 호스트의 처리 속도에 의해 무한한 데이터를 연속해서 전송할 수 없다.
- 때문에 송신 속도 즉, 한 번에 전송할 세그먼트 수를 흐름 제어를 통해 조절한다.
- 수신 호스트가 처리 가능한 세그먼트 수를 초과해 데이터가 손실되는 것을 `버퍼 오버플로라`고 한다.
</aside>

현대에서 흐름 제어를 위해 슬라이딩 윈도우를 사용한다.

### Sliding **Window**

윈도우란, 송신 호스트가 한번에 송신할 수 있는 세그먼트의 최대량(혹은 크기)을 의미한다. 즉, 윈도우 크기만큼의 세그먼트는 확인 응답을 받지 않고도 한 번에 전송 가능하다는 뜻이 된다.

![출처: https://sw-test.tistory.com/18](attachment:a7405456-1258-4d3b-ac0c-e7fb37ca44cf:image.png)

출처: https://sw-test.tistory.com/18

윈도우가 세그먼트가 전송 후 그에 대한 ACK를 받으면, 윈도우는 한칸씩 앞으로 이동하면서, 다음 세그먼트가 전송될 수 있도록 한다. 이처럼 미끄러지듯 움직인다고해서 `슬라이딩 윈도우`이다.

윈도우의 크기는 TCP 세그먼트의 헤더 중 `Window`필드를 통해 원도우 사이즈를 결정한다. 수신 호스트는 송신 호스트에게 자신의 처리 가능한 윈도우 사이즈를 알리고, 송신 호스트는 해당 값일 토대로 윈도우 사이즈를 결정하게 된다. 이때 수신 측이 알리는 윈도우를 수신 `윈도우(Receiver ****Window)`라고 한다.

## 혼잡 제어(Congestion Control)

혼잡(Congestion)이란, 많은 트래픽으로 인해 데이터 처리 속도가 늦어지거나 유실될 가능성이 있는 네트워크 상황을 뜻한다. 쉽게 생각해 한 WiFi를 다수에 이용자가 사용했을 때 네트워크 속도가 느려지는 상황이 혼잡 상황이라 할 수 있다.

즉, 라우터는 한번에 너무 많은 양의 데이터를 수신받으면, 이를 한번에 처리하지 못하고, 오히려 처리해야할 데이터가 계속 쌓이면서 처리 속도가 점점 악화될 수 있다. 이러한 문제를 해결하기 위해 **혼잡 제어**를 통해 전송량을 조절한다.

혼잡을 제어하기 위해 사용되는 윈도우를 `Congestion Window`라고 하고, 혼잡 정도에 따라 윈도우의 크기를 결정하는 알고리즘을 `혼잡 제어 알고리즘`이라고 한다.

어떻게 보면 전송량은 앞써 언급한 수신 윈도우와 혼잡 윈도우를 종합해 결정된다.
(`min(Congestion Window, Receiver Window)`)

### 혼잡 제어 알고리즘

가장 기본적인 혼잡 제어 알고리즘으로 **AIMD(Additive Increase Multiplicative Decrease)**가 있다.

AIMD는 뜻 그대로 “합으로 증가 곱으로 감소”의미 이다. 즉, 혼잡이 감지되지 않을 경우 윈도우의 사이즈를 RTT 마다 1씩 선형적으로 증가시키고, 혼잡이 감지되면 윈도우 사이즈를 절반으로 감소시키는 동작을 반복하는 알고리즘이다.

![image.png](attachment:1655b16c-6daf-4867-8d5a-884d9ecce0e3:image.png)

<aside>
☝

**RTT(Round Trip Time)**란,

RTT(Round Trip Time)이 사용된다. RTT는 데이터를 전송한 후, 그에 대한 응답(ACK)을 받을 때 까지 걸리는 시간을 의미한다.

</aside>

두 번째는 Show Start 알고리즘이 있다. **Show Start**는 윈도우의 크기를 1로 시작해 수신받은 ACK 세그먼트 하나당 1씩 증가시킨다.

![출처: https://ys-cs17.tistory.com/70](attachment:ed59c271-61e6-424d-a2c3-8a6190304a1e:image.png)

출처: https://ys-cs17.tistory.com/70

즉, 결과적으로 2배씩 지수의 형태로 증가하게 된다. Show Start는 AIMD보다 초기에 빠르게 윈도우 크기를 증가시켜 원활한 통신이 가능하게 해준다.

하지만, 윈도우 사이즈를 계속 지수형태로 증가시키면, 그 만큼 빠르게 혼잡 상황을 마주할 수 있기 때문에 `show start threshold`라는 임계치를 두어 윈도우의 사이즈가 임계치를 넘어가면, **혼잡 회피(Congestion avoidance)** 알고리즘을 통해 AIMD와 동일하게 RTT 마다 1씩 증가시켜 원도우 증가 속도를 낮춘다.

Show Start 중 세번의 중복 ACK가 발생하거나 타임아웃이 발생하면, 혼잡 상황이라 인지하고, 이를 처리하기 위해 상황에 따라 다음과 같이 처리한다.

- 세번의 중복 ACK 발생(3 ACK Duplicated)
    1. 손실된 세그먼트에 대해 빠른 재전송(fast retransmit) 수행
    2. 윈도우 사이즈와 ssthresh(show start threshold)를 절반으로 감소시키고, 해당 시점부터 혼잡 회피(윈도우 크기를 1씩 증가) 수행, 이를 `빠른 회복(fast recovery)`라고 한다.
- 타임아웃
    1. 윈도우 사이즈를 1로 ssthresh를 감지 시점의 절반으로 조정
    2. 다시 Show Start(원도우 사이즈를 지수형태로 증가) 수행

![image.png](attachment:04f83db5-7869-4c29-b7d4-87c9784d720c:image.png)

<aside>
🤔

**왜 3 ACK Duplicated와 타임아웃에 따라 다른 알고리즘을 수행할까?** 

3 ACK Duplicated는 어떻게 보면 3번의 ACK를 수신 받았음과 동일하다. 즉, 혼잡 상황보다는 단순 패킷 손실 가능성이 크기 때문에 원도우 크기를 1로 감소시키는 것이 아닌 Fast Recovery 알고리즘을 사용해 빠르게 원도우를 회복시킨다.

하지만, 타임아웃의 경우 혼잡 상황일 가능성이 더 크기 때문에 원도우 사이즈를 초기화하는 것이다.

이 처럼 둘을 구분해서 처리하는 것을 `TCP Reno` 방식이라고 하며, 둘을 구분하지 않고, 동일하게 타임아웃 상황처럼 처리하는 것을 `TCP Tahoe` 방식이라고 한다.

</aside>

<aside>
☝

빠른 재전송(fast retransmit)란,

빠른 재전송은 타임아웃 전이라도 세 번의 동일한 ACK 세그먼트(3 ACK Duplicated)가 수신 되었을 때 오류라고 인지하고, 해당 세그먼트를 곧바로 재전송하는 것이다.

</aside>

하지만, 현대에는 앞써 설명한 알고리즘은 사실 1980년대에 개발되어 현재와는 대역폭의 차이가 크다. 즉, 대역폭이 커짐에 따라 더 빠르게 Window 사이즈를 조절하는 알고리즘이 계속적으로 등장하고 있다.

- **CUBIC**
    - **TCP 혼잡 제어 알고리즘** (주로 Linux에서 기본 사용)
    - 3차 함수 기반 윈도우 증가(매우 빠르게 증가하다가 특정 지점(변곡점)에 접근하면서 속도가 느려지고, 그 지점을 통과한 후에는 다시 빠르게 증가)
    - **고속·장거리(High BDP) 네트워크**에서 성능 향상
- **BBR (Bottleneck Bandwidth and RTT)**
    - 구글(Google)에서 개발한 알고리즘
    - 기존 Reno/CUBIC 계열이 손실 패킷을 혼잡 신호로 활용하는 것에 비해, 
    BBR은 실제 병목 구간의 대역폭 측정 및 RTT 분석을 통해 혼잡을 제어

또한, ECN(Explicit Congestion Notification)를 통해 타임아웃과 같은 패킷손실을 통해 혼잡을 감지하는 것이 아닌 라우터가 혼잡을 감지해 빠르게 혼잡을 감지할 수 있다. 혼잡을 감지한 라우터는 IP 헤더 중 Type of Service의 마지막 2비트를 통해 수신 호스트에게 알린 후 수신 호스트는 TCP 헤더의 제어비트 중 ECE 비트를 세팅해 송신 호스트에게 알림.

ECE 비트 세팅된 세그먼트를 전달받은 송신 호스트는 제어비트 중 CWR 비트를 세팅 후 윈도우 사이즈를 조절한다.
