# OSI 7계층과 TCP / IP 4계층

## OSI 7계층

| OSI 7 Layer        | 국문명          | PDU; Process Data Unit |
| ------------------ | --------------- | ---------------------- |
| Application Layer  | 응용 계층       | Data                   |
| Presentation Layer | 표현 계층       | Data                   |
| Session Layer      | 세션 계층       | Data                   |
| Transport Layer    | 전송 계층       | Segments               |
| Network Layer      | 네트워크 계층   | Packets                |
| Data-Link Layer    | 데이터링크 계층 | Frames                 |
| Phisical Layer     | 물리 계층       | Bits                   |

- Open System Iterconnection
  - 네트워크에서 통신이 일어나는 과정
  - 계층이 올라갈 때마다 헤더가 붙음



- 물리 계층
  - 0과 1의 데이터 통신
  - 신호는 이산적으로 끊을 수 없음, 아날로그 신호를 전달하게 됨
  - encoding
    - 0과 1의 나열을 아날로그 신호로 바꾸어 전선으로 흘려 보냄
  - decoding
    - 아날로그 신호를 0과 1의 나열로 해석
  - 물리 계층은 물리적으로 연결된 두 대의 컴퓨터가 0과 1의 나열을 주고받을 수 있게 해주는 모듈
  - 하드웨어로 구현 
- 막간: 컴퓨터 간 통신의 이해
  - 스위치
    - 메시지의 목적지를 확인하여 그 대상에게 전달시켜줌
    - 하나의 스위치로 연결되어 있는 인트라넷, 네트워크
  - 라우터
    - 스위치와 스위치를 연결하여 서로 다른 네트워크의 컴퓨터 사이의 통신이 가능하게 해줌
    - 인터넷
      - 라우터와 라우터를 연결할 수도 있음 -> 이런 식으로 전세계의 컴퓨터를 연결한 것
    - 라우팅: 목적지를 찾는 방법

- 데이터 링크 계층
  - 동 네트워크 내 다수 컴퓨터가 데이터를 주고받기 위해 필요
  - MAC 주소 통신
    - 물리적 주소
  - Framing
    - 각 데이터를 구별해줘야 함
  - LAN카드에 구현
- 네트워크 계층
  - IP 주소
    - 각 컴퓨터들이 갖는 고유한 주소
  - 패킷
    - 데이터에 IP주소 추가
  - inter-network 속에서 목적지로 데이터를 전송하기 위해
  - IP 주소로 길을 찾고(routing)
  - 자신 다음의 라우터에게 데이터를 넘김(forwarding)
  - 커널에 소프트웨어적으로 구현
- 전송 계층
  - 포트 번호
    - 어떤 데이터를 어느 프로세스에 줘야 할지를 해결
    - 송신자는 포트 번호를 붙여서 데이터를 보냄
  - 포트 번호를 이용하여 도착지 컴퓨터의 최종 도착지인 프로세스까지 데이터가 도달하게 만드는 모듈
  - 커널에 소프트웨어적으로 구현
  - 주로 TCP / UDP 사용
- 세션 계층
  - 통신 장치 간 상호작용, 동시화
  - 에러 발생시 복구
  - OS
- 표현 계층
  - 데이터 형식
  - 데이터 인코딩 / 디코딩
- 응용 계층
  - 사용자가 접하는 계층
  - HTTP, FTP 등등
- OSI vs TCP / IP 모델
  - 현대 인터넷은 TCP / IP 모델을 따름
  - TCP / IP 모델(Updated)의 경우
    - Application
      - Application / Presentation / Session이 뭉뚱그러짐
    - Transport
    - Network
    - Data Link
    - Physical