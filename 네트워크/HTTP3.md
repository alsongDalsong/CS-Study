오늘 이야기할 주제는 HTTP 데이터 전송과 관련된 이야기입니다.

## 기본 개념

HTTP는 애플리케이션계층에 속하는 프로토콜이기 때문에 전송 계층을 통과해야 한다.

이때 이론상으로는 전송 프로토콜은 뭘 써도 상관없다. (신뢰성 있는 연결만 된다면..)

그래서 보통 신뢰성이 높은 TCP를 사용한다.

아니 사용했다!

HTTP/2까지는..!

## 기존에는.. (~HTTP/1.1)

연결 하나당 요청 1개 & 응답 한 개인 기존 방식이었다.

그러면 요청마다 연결이 만들어져야 하니깐 성능이 저하된다.

그래서 HTTP/1.1부터 지정한 시간 동안 연결을 닫지 않도록 하는 기능이 도입된다.

즉, 여러 요청이 하나의 커넥션을 사용할 수 있게 된다.

→ Persistent Connection

그런데 더 나아가서 연결을 유지한 상태로 응답을 기다릴 필요 없이 여러 개의 리퀘스트를 바로 보낼 수 있도록 하는 기능까지 도입된다.

→ Pipelining

![3333](https://github.com/user-attachments/assets/220d0732-a896-47a8-9408-af64d471cac5)

이미지 출처: wiki

## Pipelining 문제

이렇게 성능 저하 문제가 해결된 것처럼 보이지만 사실 다른 문제가 있다. 

Pipelining은 리스폰스가 순서대로 도착해야 하므로 이전 리퀘스트가 막혔을 때 **Blocking**이 일어난다.

→ Head Of Line Blocking

![http_hol_blocking](https://github.com/user-attachments/assets/d8bd4579-263d-4f97-9cf7-ec5130d4547b)

```
요청1 → 요청2 → 요청3 //클라이언트는 요청을 응답과 상관없이 보냄
```

```
응답1 (지연) → 응답2 (대기) → 응답3 (대기) //첫번째 응답에서 오래걸리면 결국 대기해야함
```

한마디로 요약하면 요청만 병렬적으로 가능한 느낌이다.

## HTTP/2 등장으로 해결

위에서 본 문제를 해결한 HTTP/2가 등장했다.

어떻게 해결했는지 알기 위해서는 HTTP/2를 조금 이해할 필요가 있다.

HTTP/2의 가장 큰 특징은 **바이너리 프레임**이다!

기존에는 텍스트로 요청을 보냈다면

```
GET /upload HTTP/1.1
//생략
```

HTTP/2는 바이너리로 인코딩을 해서 처리 속도를 개선하는 것이다.

컴퓨터는 바이너리를 좋아하니깐 성능이 개선되는 것이다.

→ 사실 이건 중복헤더 문제와 밀접한 관련이 있다.

![12](https://github.com/user-attachments/assets/af1c0ddd-7ec5-4608-8ac5-3d9235d20356)

위 이미지처럼 Frame에는 Header 혹은 Data가 들어간다.

그리고 이렇게 Frame이 여러 개 모여져 있는 것을 Message라고 한다.

요청 혹은 응답의 단위라고 생각하면 이해가 쉬울 것 같다.

그리고 이러한 Message는 특정 Stream에 속하게 된다.

`Stream`은 양방향으로 Message를 주고받는 하나의 흐름이라고 생각하면 된다.

![34](https://github.com/user-attachments/assets/3caef76c-6981-41aa-88be-2edfce17a9c8)


마지막으로 여러 개의 Stream이 하나의 Connection에 속하는 구조가 된다.
![78](https://github.com/user-attachments/assets/73024224-134f-4e01-b6f2-930132c1fe33)

하나의 요청과 응답 단위는 하나의 Stream에서 이루어지기 때문에 스트림별로 독립적인 송수신이 가능한 방식이다.

→ 멀티플렉싱(Multiplexing)

이렇게되면 하나의 스트림에서 지연되고 있다고 해도 다른 스트림에서 요청-응답이 가능하기 때문에 병렬적으로 주고받을 수 있게 된다.

## 근데 사실은..

멀티플렉싱을 통해 문제를 어느 정보 해결했지만 이것 역시 문제가 있다.

`TCP`는 패킷을 전송할 때, 전달을 보장하기 때문에 패킷이 손실되면 재전송하게 하는 구조다.

그리고 재전송이 발생하면 패킷의 순서가 역전되지 않도록 후속 패킷이 대기하게 되기 때문에 패킷 손실이 일어날 경우 Blocking이 발생할 수 있다.

(물론 이건 TCP자체의 문제라서 HTTP/1도 가지고 있는 문제다)

→ TCP Head Of Line Blocking

![123](https://github.com/user-attachments/assets/8b5cd79d-2371-4cff-b78f-cc62713a039d)

결론은.. TCP자체가 문제다.

신뢰성을 확보하기 위해서 연결하는 과정에서 시간이 들고, 패킷이 유실되었을 때 TCP HOL가 발생한다.

그래서 결국.. UDP 기반으로 사용하게 된다

## UDP 기반이라고?

HTTP/3은 QUIC을 사용한다.

QUIC는 구글에서 UDP를 기반으로 만든 프로토콜이다.

QUIC는 각각의 패킷 간의 순서가 존재하지 않는 독립적인 패킷을 사용한다.

잠깐..! HTTP는 신뢰성이 중요하다고 했는데 UDP는 신뢰성이 떨어지는 거 아니야?

하지만 UDP는 별도의 기능이 없기 때문에 조금 더 자유롭게 커스텀이 가능해진다.

<img width="620" alt="tcp-header" src="https://github.com/user-attachments/assets/0755f8d7-8ed4-4ba3-b578-fd0f2f98a544">
<img width="620" alt="udp-header" src="https://github.com/user-attachments/assets/65df07db-3205-436c-9679-629ccb9c5590">

그래서 UDP를 기반으로 TCP만큼 신뢰성 확보가 가능하도록 만든 것이 QUIC다.

![3333](https://github.com/user-attachments/assets/085898ee-5b5e-4d8c-915b-f0a9b015eae6)

QUIC는 첫 번째 연결을 할 때, 성공했다면 연결 설정에 필요한 정보와 함께 데이터를 보낸다.

이렇게 되면 클라이언트가 서버로 첫 요청을 보낼 때 세션 키를 모르는 상태인데 어떻게 처리할까?

목적지인 서버의 Connection ID를 기반으로 생성한 `초기화 키`를 사용해 암호화한다.

그리고 이후에는 서버는 설정을 캐싱해 두고, 다음 연결 때 캐싱해 놓은 설정을 사용해서 바로 연결을 성립시킨다.

근데 사실 진짜 중요한 건 HTTP/3도 멀티플렉싱을 지원한다는 것이다.

그리고 TCP기반이 아니기 때문에 하나의 패킷에 문제가 발생해도 이후 패킷에는 문제가 없다!

![555](https://github.com/user-attachments/assets/a1a07e6d-c227-4e39-984a-4d9d3e5c2a1b)

## 앱에서는 어떻게 확인할까?

iOS 15, macOS Monterey는 HTTP3를 default로 지원하기 때문에 서버만 HTTP/3을 지원한다면 가능하다.

궁금하다! 우리앱이 HTTP/3을 사용하고 있을까? 라는 생각이 든다면 Instrument를 통해 확인할 수 있다.

Network를 누르고 녹화를 시작한 다음 HTTP에서 List:HTTP Transactions로 변경하면 다음과 같이 확인할 수 있다.

![list](https://github.com/user-attachments/assets/1111dc48-7380-4049-966a-d384416a0756)

![list2](https://github.com/user-attachments/assets/aaa8c802-98cd-4d26-88af-bbd7bbb3d2df)

