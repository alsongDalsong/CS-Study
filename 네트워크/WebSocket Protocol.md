# Socket이란

네트워크 통신을 통해 데이터를 주고 받을 때 종착지(EndPoint)를 소켓이라고 한다. 소켓은 두 호스트가 특정 프로세스를 통해 통신할 때 호스트를 특정하기 위한 **IP 주소**와 프로세스를 특정하기 위한 **Port 넘버**로 정의된다. 소켓을 통해 데이터가 전달되는 것을 **소켓 통신**이라고 한다. 

## 소켓 통신 과정

TCP, UDP 둘다 소켓을 통해 데이터를 전달하는 소켓 통신 방식을 사용하지만, 두 프로토콜은 연결형/비연결형의 차이가 있기 때문에 소켓을 통한 통신 과정 또한 차이가 있다.

### TCP를 통한 소켓 통신

<img width=500 src="https://github.com/user-attachments/assets/b5b290cb-1dbd-4b63-8811-77ae7e81e685">

> TCP의 소켓 통신 과정

먼저, 서버와 클라이언트는 `socket()`을 통해 통신할 소켓을 생성한다. 이후 서버는 `bind()`를 호출해 생성한 소켓에 IP 주소와 포트 번호를 할당하고, `listen()`을 호출해 연결을 기다린다.(이때 연결 요청이 들어오는 소켓을 리스닝 소켓이라고 한다.) 클라이언트가 서버에게 `connect()`를 통해 연결을 요청하면, 이때 3-way handshake를 통해 연결을 수립 후 서버는 `accept()`를 통해 실제 클라이언트와 메시지를 주고받을 소켓을 동적으로 생성한다. 이후 클라이언트와 서버는 데이터를 주고받은 후 완료되면, `close()`를 통해 소켓을 소멸시킨다.

### UDP를 통한 소켓 통신

<img width=500 src="https://github.com/user-attachments/assets/c3bd4291-e6d0-426d-893e-ec0c8cdd09b1">

> UDP의 소켓 통신 과정

UDP의 경우 비연결형 프로토콜이기 때문에 연결 수립 과정이 생략된다. `listen()`, `connect()`, `accept()` 과정 없이 바로 데이터를 주고 받는다. 또한 UDP를 통한 소켓 통신에서는 TCP와 달리 하나의 소켓 통해서만 데이터를 전달받는다.

# WebSocket Protocol

WebSocket에 대해 설명하기 전 Socket 통신에 대해 먼저 얘기한 이유는 둘다 Socket이라는 단어를 사용하지만, 완전히 다른 개념이기 때문이다. 앞써 설명한 Socket의 개념은 전송 계층에 좀더 가깝고, 앞으로 얘기할 WebSocket은 응용 계층에서 사용되는 프로토콜 중 하나이다.

또한, WebSocket의 경우 **TCP 기반으로 동작**하며, 실시간 **양방향 통신**을 위한 프로토콜이다.

## WebSocket 등장 이유와 이전 통신 방식

<img width=500 src="https://github.com/user-attachments/assets/69b01c21-ad80-47a6-9bbe-1e9f4c5dbda3">

> 출처: [https://velog.io/@sj_yun/Web-Socket이란](https://velog.io/@sj_yun/Web-Socket%EC%9D%B4%EB%9E%80)

WebSocket은 2011년 [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455)에서 처음 소개되었다. 

클라이언트-서버 간의 통신을 위해 현재까지도 HTTP가 대중적으로 사용된다. HTTP의 경우 요청-응답 프로토콜로 클라이언트 요청에 의해 서버는 그에 맞는 응답을 보낸다. 즉, 통신의 주체는 클라이언트가 된다. 하지만, 서비스에 따라 클라이언트, 서버 모두 통신에 주체가되는 서비스들이 존재한다. 대표적으로 채팅, 주식 등이 있다.

이때 HTTP는 한계가 존재한다. 물론, HTTP로 실시간 통신이 불가능한 것은 아니다. 대표적으로 다음과 같은 방법을 통해 실시간 통신을 모방(?)했다.

### 폴링(Polling)

<img width=500 src="https://github.com/user-attachments/assets/61db805a-2935-4cd0-adaa-a4133aa68d2e">

> 출처: [https://yong-nyong.tistory.com/90#article-2-3--웹-소켓(web-socket)의-한계점](https://yong-nyong.tistory.com/90#article-2-3--%EC%9B%B9-%EC%86%8C%EC%BC%93(web-socket)%EC%9D%98-%ED%95%9C%EA%B3%84%EC%A0%90)

폴링은 클라이언트가 일정 시간을 두고 주기적으로 서버에게 요청을 보내 그에 따른 응답을 받는 방법이다. 

업데이트된 데이터가 없더라도 클라이언트는 주기적으로 요청을 보내야하기 때문에 불필요한 요청이 생긴다는 단점이 있다. 또한, 짧은 주기로 지속적인 요청을 보내야하기 때문에 트래픽이 증가하며, 필요 이상의 리소스가 발생한다.

### 롱 폴링(Long Polling)

<img width=500 src="https://github.com/user-attachments/assets/b446b10b-c0e7-4346-9e6b-d25bf5456ce2">

> 출처: [https://yong-nyong.tistory.com/90#article-2-3--웹-소켓(web-socket)의-한계점](https://yong-nyong.tistory.com/90#article-2-3--%EC%9B%B9-%EC%86%8C%EC%BC%93(web-socket)%EC%9D%98-%ED%95%9C%EA%B3%84%EC%A0%90)

롱 폴링은 기존 폴링 방식의 짧은 주기로 반복적으로 요청해야했던 단점을 개선하고자 등장했다. 롱 폴링의 경우 클라이언트가 요청을 보냈을 때 서버가 바로 응답을 주는 것이 아닌 타임아웃될 때까지 기다리면서 그 사이에 데이터가 업데이트가 되면 응답을 보낸다. 

하지만, 롱 폴링의 경우에도 결국 클라이언트가 주기적으로 요청을 보내야하며, 데이터가 빠르게 변화할 경우 결국 폴링 방식과 큰 차이가 없게된다.

### SSE(Server-Sent Event)

<img width=500 src="https://github.com/user-attachments/assets/8c3de64e-323d-4375-8420-5062a5ee56fa">

> 출처: [https://yong-nyong.tistory.com/90#article-2-3--웹-소켓(web-socket)의-한계점](https://yong-nyong.tistory.com/90#article-2-3--%EC%9B%B9-%EC%86%8C%EC%BC%93(web-socket)%EC%9D%98-%ED%95%9C%EA%B3%84%EC%A0%90)

SSE의 경우에는 클라이언트가 최초로 요청을 보내면, 서버는 응답 후 연결을 종료하는 것이 아닌 연결을 유지한체 지속적으로 데이터를 전송한다. 때문에 클라이언트는 실시간으로 업데이트된 데이터를 서버로 부터 전달받을 수 있다. 

하지만, SSE의 경우 서버가 최초 요청 이후엔 서버만 일방적으로 메시지를 전송하게된다. 즉, SSE는 통신의 주체가 서버가 되며, 실시간 통신은 가능하지만 양방향 통신은 불가능하다.

### 추가적인 문제

추가로, 폴링, 롱 폴링, SSE 모두 결국 HTTP 프로토콜을 사용해 메시지를 전달한다. 즉, HTTP 헤더로 인해 필요 이상의 데이터를 지속적으로 전송해야함으로 부하가 발생할 수 있고, 네트워크 통신 효율이 떨어진다. 

결국, WebSocket은 실시간으로 양방향 통신이 가능하면서, 가벼운 헤더(최소 2바이트)를 통해 통신 효율을 높이기 위해 탄생한 것이다.

## WebSocket의 통신 과정

<img width=500 src="https://github.com/user-attachments/assets/4e956382-0307-4438-8c71-8f1556139ea0">

> 출처: https://mag1c.tistory.com/285

1. 클라이언트가 서버에게 연결 요청을 위해 request 전송
    - 이땐 HTTP가 사용되며, 핸드쉐이크가 이뤄진다.
2. HTTP에서 WebSocket(ws or wss)으로 프로토콜 변경
3. 양방향 통신을 시작하고, 끝나면 연결을 종료

> [!NOTE]
> 💡 **연결 수립을 위한 Request & Response의 HTTP 헤더**
> 
> ```swift
> <!-- Request 헤더 -->
> 
> GET /chat HTTP/1.1
> Host: localhost:8080
> Upgrade: websocket // 현재 프로토콜에서 다른 프로토콜로 변경될시 변결할 프로토콜
> Connection: Upgrade // 프로토콜이 변경된다는 것을 의미
> Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw== // 인증을 위한 키값
> Sec-WebSocket-Protocol: chat, superchat
> Sec-WebSocket-Version: 13
> Origin: [http://localhost:9090](http://localhost:9090/)
> 
> <!-- Response 헤더 -->
> 
> HTTP/1.1 101 Switching Protocols
> Upgrade: websocket
> Connection: Upgrade
> Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
> Sec-WebSocket-Protocol: chat
> ```

## WebSocket의 한계

1. WebSocket은 HTML5 이후에 나온 프로토콜로 HTML5를 지원하지 않는 브라우저에서는 사용할 수 없다.
2. 지속적으로 연결을 유지해야함으로 서버의 메모리 사용 증가 및 부하가 발생할 수 있음
3. 장기간 통신이 발생하지 않으면, 타임아웃으로 연결이 끊어질 수 있음, 연결 유지를 위해 한번 씩 `ping/pong` 프레임을 전송해야함
4. WebSocket은 연결이 끊긴 경우, 재연결을 지원하지 않음. 직접 처리해야함
5. WebSocket을 통해 전송 가능한 메시지는 텍스트와 바이너리 뿐이다.
    - 이를 해석하기 위해 추가적인 규약(프로토콜)이 사용됨

# 참고

- [RFC 6455](https://datatracker.ietf.org/doc/html/rfc6455#autoid-21)
- [우아한테크 Web Socket 유튜브](https://www.youtube.com/watch?v=MPQHvwPxDUw&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=99)
- [https://velog.io/@newdana01/소켓이란-종류-통신-흐름-HTTP통신과의-차이](https://velog.io/@newdana01/%EC%86%8C%EC%BC%93%EC%9D%B4%EB%9E%80-%EC%A2%85%EB%A5%98-%ED%86%B5%EC%8B%A0-%ED%9D%90%EB%A6%84-HTTP%ED%86%B5%EC%8B%A0%EA%B3%BC%EC%9D%98-%EC%B0%A8%EC%9D%B4)
