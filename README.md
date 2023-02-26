## 정의
```
두 프로그램 간의 메시지를 교환하기 위한 통신 방법 중에 하나

HTML5에서 많이 사용됨

```


## 특징
#### 1. 양방향 통신
	데이터 송수신을 동시에 처리
    클라와 서버가 서로에게 원할 떄 데이터를 주고 받을 수 있음
    기존의 http통신은 클라가 요청을 보내는 경우에만 서버가 응답할 수 있었음
    커넥션이 open, close 된 여부를 따짐
#### 2. 실시간 네트워킹 Real Time Networking
	웹환경에서 연속된 데이터를 빠르게 노출시켜야 할 때 곧잘 사용
	채팅, 주식, 비디오 데이터
    친구들과 채팅을 한다면 친구들과 연결된 것이 아니라 같은 websocket 서버에 들어가 있는 상태인거
    여기서 브라우저 끼리 연결을 시켜버리는 개념이 WebRTC. P2P 커뮤니케이션
	여러 단말기에 빠르게 데이터를 교환
	
#### 3. Polling, Long Polling, Streaming과의 차이
	서버로 일정 주기로 요청을 보냄
	real time 통신에 비해서 불필요한 요청을 보내게 됨 
    header가 불필요하게 큼
    
## 동작방식
#### 핸드 쉐이킹

![](https://images.velog.io/images/rainbowweb/post/5a28097a-db1a-409d-afe2-a7c31356042f/image.png)

1. 빨간 박스 Opening Handshake
```
  HTTP80/443을 사용
  응답코드는 101
```
```
    요청
    GET /chat HTTP/1.1     ##반드시 GET 메서드
    Host: localhost:8080  
    Upgrade: websocket     ##웹소켓 전환을 위해서 고정값
    Connection: Upgrade    ##현재의 전송이 완료된 후 네트워크 접속을 유지할 것인가에 대한 정보. 웹소켓 요청 시에는 반드시 Upgrade라는 값 고정
    Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==  ##유효한 요청인지 확인하기 위해 사용하는 키 값. base64로 인코딩
    Sec-WebSocket-Protocol: chat, superchat  
    Sec-WebSocket-Version: 13 
    Origin: http://localhost:9000
```
```
    응답
    HTTP/1.1 101 Switching Protocols ##응답코드가 101. 101은 '프로토콜 전환' 으로 요청자가 서버에 프로토콜 전환을 요청했으며 서버는 이를 승인하는 상태
    Server: Apache-Coyote/1.1 
    Upgrade: websocket 
    Connection: upgrade 
    Sec-WebSocket-Accept: y0C2sLRjQhdq3geJIKYeRVUgtFg= ##보안을 위한 응답 키로 Sec-WebSocket-Key를 base64로 인코딩. 클라에서 보낸값과 일치해야함

	
```
2. 노란 박스 Data Transfer
```
    데이터 전송파트
    메세지라는 개념으로 데이터를 주고 받음
    여기서 메세지는 프레임단위로 되어있음. 
    프레임은 communication에서 가장 작은 단위. 작은헤더 + payload로 구성
    서버와 클라이언트는 서로가 살아 있는지 확인하기 위해 heartbeat 패킷을 보내며, 주기적으로 ping을 보내 체크

    프로토콜이 ws로 변경됨. ws(80/443)
```
3. 보라 박스 Closing HandShake
```
    커넥션을 종료하기 위한 컨트롤 프레임을 전송
```

## 한계
#### SockJS
```
    웹소켓은 HTML5 이후에 나왔기 때문에
    HTML5이전의 기술로 구현된 서비스에서 웹소켓 처럼 사용할 수 있도록 도와줌

    자바스크립트를 사용하여 실시간 웹을 구현
```



## 참고
https://www.youtube.com/watch?v=rvss-_t6gzg
https://kellis.tistory.com/65
https://ws-pace.tistory.com/105?category=968973
https://dev-gorany.tistory.com/212?category=901854
https://dydtjr1128.github.io/spring/2019/05/26/Springboot-react-chatting.html

# Stomp

## (Simple Text Oriented Message Protocol)

    메시지 전송을 효율적으로 하기 위한 프로토콜

    웹소켓 위에 얹어 함께 사용할 수 있음

    PUB / SUB 구조로 되어있음

    클라이언트와 서버가 전송할 메세지의 유형, 형식, 내용들을 정의하는 매커니즘

    메세지의 헤더에 값을 줘서 인증 처리를 구현하는 것도 가능

### PUB / SUB 

    메시지를 공급하는 주체와 소비하는 주체를 분리해 제공하는 메세징 방법

    채팅방 생성 - 우체통(Topic)
    채팅방에 글을 씀 - 집배원(Pub)
    채팅방에 들어감 - 구독자(Sub)


## Message Broker 

    발신자의 메세지를 받아와서 수신자들에게 메세지를 전달하는 어떤 것

    Spring에서 지원하는 STOMP를 사용하면 Simple In-Memory Broker를 이용

    RabbitMQ, ActiveMQ같은 외부 메세징 시스템을 STOMP Broker로 사용할 수 있도록 지원

    스프링은 메세지를 외부 Broker에게 전달하고, Broker는 WebSocket으로 연결된 클라이언트에게 메세지를 전달
    
## 프레임 구조

    웹소켓과 다르게 보내는 형식이 정해져있음

    COMMAND          (SEND(메세지 전송)/SUBSCRIBE(메세지 구독))
    header1:value1 : 어디로 보낼지 destination이 됨
    header2:value2

    Body^@ : payload

    

```
예시

<ClientA가 5번채팅방에 대해 구독>

SUBSCRIBE
destination: /topic/chat/room/5
id: sub-1

^@
    
<ClientB에서 채팅메세지 보냄>

SEND
destination: /pub/chat
content-type: application/json

{"chatRoomId": 5, "type": "MESSAGE", "writer": "clientB"} ^@

<받은 메세지를 모든 구독자에게 보냄>

MESSAGE
destination: /topic/chat/room/5
message-id: d4c0d7f6-1
subscription: sub-1


{"chatRoomId": 5, "type": "MESSAGE", "writer": "clientB"} ^@
```

##  Pub/Sub 메시징 흐름
</br>

![](https://images.velog.io/images/rainbowweb/post/15faab06-edf1-4a21-bd8e-c081d9a5eeb8/image.png)

SEND : 발신자. 구독자에게 메세지를 보내고 싶어함.

	1. 클라이언트에서 메세지를 보내면 일단 먼저 @MessageMapping이 붙은 Controller가 받는다.
	2. /topic을 destination 헤더로 넣어서 메세지를 바로 송신할 수 도 있고
    3. /app을 destination 헤더로 넣어서 서버 내에서 가공을 줄 수 있음
    4. 서버가 가공을 완료했으면, 메시지를 /topic이란 경로를 담아서 다시 전송하면
    5. SimpleBroker에게 전달되고
    6. simpleBroker는 전달받은 메세지를 구독자들에게 최종적으로 전달하게 됨.

MESSAGE : 구독.  /topic이란 경로를 구독중

## 출처
https://github.com/namusik/TIL-SampleProject

    

