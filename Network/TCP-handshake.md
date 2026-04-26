# TCP handshake

## 개념
TCP 연결 수립과 해제 시 진행되는 절차
- 연결 시: 3-way handshake
- 해제 시: 4-way handshake

## 3-way handshake

<img width="963" height="671" alt="Image" src="https://github.com/user-attachments/assets/10096858-ac1c-4d03-ba0a-c5013af68e85" />

연결을 요청한 호스트를 A, 요청을 받는 호스트를 B라고 가정합니다.
1. A가 B로 SYN 전송(seq = x)
2. B가 SYN 수신, ACK 전송(ACK = x+1) 및 SYN 전송(seq = y)
3. A가 ACK 수신(연결 허용 확인) 및 SYN 수신, ACK 전송(ACK = y+1)


## 4-way handshake

양 호스트에서 모두 FIN을 전송해야 연결이 해제됩니다.<br>
*FIN: 현재 범위 이후로는 전송할 데이터가 없다는 의미*

<img width="963" height="949" alt="Image" src="https://github.com/user-attachments/assets/c277ce3f-026f-481a-ad12-1786c4a2c032" />

먼저 FIN을 전송한 호스트를 A, 이후에 전송한 호스트를 B라고 가정합니다.
1. A에서 B로 FIN 전송(seq = x(마지막 순서 데이터의 시퀀스))
2. B에서 A로 ACK 전송(ACK = x + 1), 이 시점에서 한쪽 방향만 닫혀 `단방향`과 유사한 상태로 전환<br>
.<br>
.<br>
.
3. B에서 A로 FIN 전송(seq = y(마지막 순서 데이터의 시퀀스))
4. A에서 B로 ACK 전송(ACK = y + 1)
5. A에서 `TIME-WAIT` 상태 진입: 일정 시간 대기 후 완전 연결 종료

**TIME-WAIT 상태란?**
- 먼저 FIN을 보낸 호스트가 마지막 ACK 후 진입하는 상태입니다.
- 상대 호스트의 FIN 이전에 받았어야 할 데이터를 받지 못해 유실되는 상황을 대비하기 위한 유예 기간입니다.

**FIN 전송 순서 판단 기준: 양 호스트가 어떻게 판단하는지?**
- 각 호스트의 로컬 상태(state)와 수신한 세그먼트 이벤트를 기준으로 판단합니다.
- FIN을 전송한 순서에 따라 두 호스트의 로컬 상태가 결정됩니다.
- 먼저 FIN을 보낸 호스트가(`FIN-WAIT-1` 상태일 때) ACK를 응답받지 못하면, 상대방에게 재시도 요청을 보냅니다.



**양 호스트가 동시에 서로에게 FIN을 전송하면 어떻게 되는지?**
- '동시 전송' 판단 기준: 서로가 모두 `ESTABLISHED` 상태일 때 서로에게 FIN을 전송한 상황
- 예외적인 상황으로, 두 호스트가 `TIME-WAIT` 상태로 돌입합니다.