# Chapter 1 학습 문서

## 작성/검증 원칙
- 출처 우선순위: IETF 표준/BCP 및 RFC를 1차 기준으로 두고, IBM/Cisco 공식 문서로 개념 설명을 교차검증한다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- 추정 배제: 수치, 정의, 프로토콜 동작은 근거가 확인된 내용만 기록한다.
- 용어 일관성: `호스트(End System)`, `라우터(Gateway/IP Router)`, `지연(Delay/Latency)`, `손실(Loss)`, `처리율(Throughput)`을 구분해 사용한다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([RFC 7680](https://www.rfc-editor.org/rfc/rfc7680))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))
- 업데이트 기준일: 2026-04-22 (KST).

## 1.1 인터넷이란 무엇인가?
### 학습목표
- 인터넷을 "상호연결된 패킷 네트워크들의 집합"으로 정의할 수 있다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))
- 인터넷 통신이 프로토콜(규칙) 기반 상호운용으로 성립함을 설명할 수 있다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))
- IP가 인터넷 계층에서 담당하는 최소 핵심 역할(주소 기반 전달, 데이터그램 단위 전달)을 설명할 수 있다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))

### 핵심개념
- 인터넷은 여러 패킷 교환 네트워크를 상호연결해 호스트 간 통신을 제공하는 구조다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))
- 네트워크 장치와 애플리케이션은 공통 프로토콜을 통해 데이터 교환 규칙을 맞춘다.([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))
- IP는 연결이 아닌 데이터그램 단위 전달을 제공하며, 상위 계층이 신뢰성/순서 보장을 보완한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))

### 심화 포인트
- 인터넷 아키텍처는 변화에 적응하도록 설계되어 왔고, 기술 변화는 상수에 가깝다.([RFC 1958](https://www.rfc-editor.org/rfc/rfc1958))
- 단일 벤더 기술보다 공개된 프로토콜 합의(IETF RFC)가 장기 상호운용성과 확장성을 만든다.([RFC 1958](https://www.rfc-editor.org/rfc/rfc1958))([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))

### 오해하기 쉬운 포인트
- "인터넷 = 웹(WWW)"은 정확하지 않다. 웹은 인터넷 위에서 동작하는 여러 애플리케이션 중 하나다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))
- "IP가 신뢰 전송까지 보장한다"는 오해가 많다. IP 자체는 신뢰성/순서/흐름제어를 제공하지 않는다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))

### 체크 질문
1. 인터넷을 "네트워크의 네트워크"라고 부를 수 있는 근거는 무엇인가?
2. IP와 TCP/UDP의 역할 분리는 왜 필요한가?
3. 상호운용성 관점에서 공개 표준(RFC)이 중요한 이유는 무엇인가?

### 한 줄 요약
- 인터넷은 공개 프로토콜에 기반한 상호연결 패킷 네트워크이며, IP는 그 전달의 최소 공통 계층이다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))

## 1.2 네트워크의 가장자리
### 학습목표
- 네트워크 가장자리(edge)의 주체를 `호스트/종단 시스템` 중심으로 정의할 수 있다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))
- 라우터/게이트웨이와 종단 호스트의 역할 차이를 설명할 수 있다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([Cisco: What is a router?](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))
- 사용자 단말, 서버, 애플리케이션이 가장자리에서 어떻게 서비스를 형성하는지 설명할 수 있다.([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))

### 핵심개념
- RFC 1122는 인터넷 호스트를 통신 서비스를 사용하는 종단 시스템으로 정의한다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))
- 가장자리에는 사용자 디바이스와 애플리케이션 서버가 위치하고, 이들이 실제 서비스 요청/응답의 출발점과 종착점이 된다.([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))
- 라우터는 패킷을 목적지 쪽으로 전달하는 중간 장치이고, 호스트는 애플리케이션 실행 주체다.([Cisco: What is a router?](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))

### 심화 포인트
- 성능 체감은 주로 가장자리 애플리케이션 응답시간(지연)에 반영된다. 같은 코어라도 edge 배치/서비스 구조에 따라 체감 품질이 달라진다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))([Cisco: What is low latency?](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))
- 엣지 근접 배치(CDN/엣지 컴퓨팅)는 물리적 거리와 홉 수를 줄여 지연 완화에 유리하다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 오해하기 쉬운 포인트
- "가장자리 장비 = 라우터"는 부정확하다. 라우터는 경계 연결 지점일 수 있지만, 가장자리의 핵심은 종단 호스트/서비스다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([Cisco: What is a router?](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))
- "네트워크가 빠르면 앱도 무조건 빠르다"는 단순화는 위험하다. 앱 처리 경로/서버 위치/혼잡이 함께 영향을 준다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 체크 질문
1. 호스트와 라우터의 책임을 계층/기능 관점에서 구분해 설명할 수 있는가?
2. 엣지 배치를 조정하면 어떤 지연 요인을 직접 줄일 수 있는가?
3. 사용자 체감 품질을 가장자리 관점에서 측정하려면 어떤 지표가 필요한가?

### 한 줄 요약
- 네트워크 가장자리는 서비스를 실제로 생성·소비하는 호스트 영역이며, 라우터는 이를 연결하는 전달 인프라다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([Cisco: What is a router?](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))

## 1.3 네트워크 코어
### 학습목표
- 네트워크 코어를 라우팅/포워딩 중심의 중간 전달 영역으로 정의할 수 있다.([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))
- 코어에서의 데이터그램 전달 원리를 설명할 수 있다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))
- 경로 선택(routing)과 실제 전달(forwarding)의 역할을 구분할 수 있다.([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))

### 핵심개념
- 코어는 다수의 라우터와 링크가 결합된 전달망이며, 목적지까지 패킷 경로를 형성한다.([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- RFC 791의 IP 모델은 데이터그램을 독립적으로 취급하며 연결 상태를 기본 가정하지 않는다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))
- IPv6에서도 라우터는 자신에게 명시적으로 주소 지정되지 않은 패킷을 전달하는 노드로 정의된다.([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))

### 심화 포인트
- 코어 확장은 단순 장비 추가만으로 해결되지 않으며, 라우팅 정책과 운영 복잡도가 함께 증가한다.([RFC 3439](https://www.rfc-editor.org/rfc/rfc3439))
- 코어의 품질은 지연/손실/혼잡 관리와 직결되고, 이는 상위 계층 성능(TCP 처리량 등)에 연쇄 영향을 준다.([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([RFC 7680](https://www.rfc-editor.org/rfc/rfc7680))

### 오해하기 쉬운 포인트
- "코어는 항상 고정 경로로만 전달한다"는 오해가 있다. 실제 경로는 정책/상태에 따라 달라질 수 있다.([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- "포워딩 = 라우팅"은 부정확하다. 라우팅은 경로 결정, 포워딩은 패킷의 실제 전송 실행이다.([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))

### 체크 질문
1. 코어 관점에서 라우팅과 포워딩의 책임을 어떻게 분리할 수 있는가?
2. IP 데이터그램의 독립성은 어떤 장점과 제약을 만드는가?
3. 코어 복잡도 증가가 운영 난이도에 미치는 영향은 무엇인가?

### 한 줄 요약
- 네트워크 코어는 라우터 기반의 경로 선택과 패킷 전달로 구성된 인터넷의 운송 계층(중간망)이다.([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))([RFC 791](https://www.rfc-editor.org/rfc/rfc791))

## 1.4 패킷 교환 네트워크에서의 지연, 손실과 처리율
### 학습목표
- 지연(delay), 손실(loss), 처리율(throughput)의 정의를 구분할 수 있다.([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([RFC 7680](https://www.rfc-editor.org/rfc/rfc7680))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))
- RTT와 단방향 지연의 차이를 설명할 수 있다.([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))
- 성능 저하 원인(거리, 홉, 혼잡, 자원제약)을 사례로 설명할 수 있다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))([Cisco: What is low latency?](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))

### 핵심개념
- IETF IPPM은 단방향 지연(Type-P-One-way-Delay)과 단방향 손실(Type-P-One-way-Loss)을 측정 지표로 정의한다.([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([RFC 7680](https://www.rfc-editor.org/rfc/rfc7680))
- IBM 기준으로 지연은 전송 지연 시간, 처리율은 실제 전달된 데이터량, 대역폭은 이론적 용량이다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))
- `ping`은 주로 RTT를 제공하며, 단방향 지연을 직접 측정하는 값과는 다르다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 심화 포인트
- 지연이 증가하면 ACK 기반 전송(TCP)의 효율이 떨어져 체감 처리율이 저하될 수 있다.([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- 지연 변동(jitter)과 손실은 실시간 트래픽 품질(음성/영상)에 직접 악영향을 준다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))([Cisco: What is low latency?](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))

### 오해하기 쉬운 포인트
- "대역폭이 크면 무조건 빠르다"는 오해가 많다. 높은 대역폭도 높은 지연/손실 환경에서는 기대 처리율을 보장하지 못한다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))
- "RTT/2 = 항상 단방향 지연"은 근사에 불과하다. 왕복 경로 비대칭이 있으면 오차가 커진다.([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 체크 질문
1. 지연/손실/처리율을 각각 어떤 상황에서 우선 지표로 봐야 하는가?
2. RTT가 낮아도 처리율이 낮을 수 있는 이유는 무엇인가?
3. 단방향 측정이 필요한 운영 시나리오는 무엇인가?

### 한 줄 요약
- 지연, 손실, 처리율은 서로 연동되지만 동일 개념이 아니며, 문제 진단에는 단위와 측정 방법을 분리해 해석해야 한다.([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([RFC 7680](https://www.rfc-editor.org/rfc/rfc7680))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

## 1.5 프로토콜 계층과 서비스 모델
### 학습목표
- TCP/IP 계층 구조와 각 계층 책임을 설명할 수 있다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))
- IP, TCP, UDP의 서비스 모델 차이를 설명할 수 있다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))
- IPv4/IPv6 공존 맥락에서 인터넷 계층의 핵심 차이를 이해한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))

### 핵심개념
- 인터넷 프로토콜 스위트는 계층형 구조를 따르며, 계층 간 인터페이스를 통해 상위 서비스가 구성된다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))
- IP는 주소 기반 패킷 전달을 담당하고, TCP/UDP는 전송 계층에서 애플리케이션 전달 방식을 달리 제공한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))
- IPv6는 128비트 주소와 확장 헤더 체계를 도입해 IPv4 한계를 보완하도록 설계되었다.([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))

### 심화 포인트
- TCP는 연결지향 전송 규약으로서 신뢰 전달을 위한 상태 기반 절차를 가진다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- UDP는 최소 메커니즘의 비연결 데이터그램 전송으로, 신뢰성/순서 보장을 애플리케이션에 위임한다.([RFC 768](https://www.rfc-editor.org/rfc/rfc768))
- 서비스 모델 선택은 지연 민감도, 손실 허용도, 구현 복잡도의 트레이드오프다.([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 오해하기 쉬운 포인트
- "OSI 7계층 = 인터넷 구현 그대로"는 아니다. 실무 인터넷은 TCP/IP 스택 중심으로 운영된다.([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))
- "TCP가 항상 UDP보다 좋다"는 단정은 틀리다. 요구사항(신뢰성/지연/오버헤드)에 따라 선택이 달라진다.([RFC 768](https://www.rfc-editor.org/rfc/rfc768))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))

### 체크 질문
1. 같은 애플리케이션이라도 TCP와 UDP 선택 기준이 달라지는 이유는?
2. IP가 제공하지 않는 기능을 전송 계층이 왜 보완해야 하는가?
3. IPv6 도입의 기술적 동기를 2가지 이상 설명할 수 있는가?

### 한 줄 요약
- 계층 모델은 복잡성을 분리하고, 서비스 모델(IP/TCP/UDP 선택)은 애플리케이션 요구사항에 맞게 조합된다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))

## 1.6 공격받는 네트워크
### 학습목표
- IETF 관점의 네트워크 위협 모델(도청, 변조, MITM, DoS 등)을 설명할 수 있다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))
- 대규모 감시(pervasive monitoring)가 왜 공격으로 간주되는지 설명할 수 있다.([RFC 7258](https://www.rfc-editor.org/rfc/rfc7258))
- 네트워크 보안의 핵심 목표(CIA)와 계층형 방어 개념을 정리할 수 있다.([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))([IBM: What is network security?](https://www.ibm.com/think/topics/network-security))

### 핵심개념
- RFC 3552는 보안 고려사항에서 최소한 도청/재전송/삽입/삭제/변조/MITM/DoS를 검토해야 함을 명시한다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))
- RFC 7258은 대규모 감시를 인터넷 사용자 프라이버시에 대한 공격으로 규정한다.([RFC 7258](https://www.rfc-editor.org/rfc/rfc7258))
- Cisco/IBM 모두 네트워크 보안을 다계층 방어(정책+기술)로 설명하며, 기밀성·무결성·가용성 유지가 핵심이다.([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))([IBM: What is network security?](https://www.ibm.com/think/topics/network-security))

### 심화 포인트
- DDoS는 다수 출처에서 트래픽을 집중시켜 정상 서비스 가용성을 떨어뜨리는 공격 형태다.([IBM: What is a Distributed Denial-of-Service (DDoS) attack?](https://www.ibm.com/think/topics/ddos))
- 보안 통제(검사/암호화/세분화)는 성능(지연/처리율)과 균형 설계가 필요하다.([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 오해하기 쉬운 포인트
- "방화벽만 있으면 충분하다"는 오해가 있다. 실제로는 세분화, 접근통제, 모니터링, 암호화가 함께 필요하다.([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))([IBM: What is network security?](https://www.ibm.com/think/topics/network-security))
- "네트워크 내부는 기본적으로 신뢰 가능"이라는 가정은 현대 분산 환경에서 성립하기 어렵다.([IBM: What is zero trust?](https://www.ibm.com/think/topics/zero-trust))([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))

### 체크 질문
1. RFC 3552가 요구하는 최소 위협 항목을 나열할 수 있는가?
2. 가용성 보호와 성능 유지 사이의 충돌을 어떻게 완화할 수 있는가?
3. Zero Trust 관점이 전통 perimeter 모델과 다른 핵심은 무엇인가?

### 한 줄 요약
- 네트워크 보안은 단일 장비가 아니라 위협 모델 기반의 다계층 통제로 구현되며, 성능과 보안을 함께 최적화해야 한다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))([IBM: What is network security?](https://www.ibm.com/think/topics/network-security))

## 1.7 컴퓨터 네트워킹과 인터넷의 역사
### 학습목표
- 초기 인터넷 핵심 이정표를 연대 순으로 설명할 수 있다.([RFC 2235](https://www.rfc-editor.org/rfc/rfc2235))
- RFC 문서가 인터넷 기술 진화를 추적하는 기준점임을 이해한다.([RFC 2235](https://www.rfc-editor.org/rfc/rfc2235))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- "역사는 끝난 사실"이 아니라 현재 표준으로 계속 갱신된다는 점을 인식한다.([RFC 1958](https://www.rfc-editor.org/rfc/rfc1958))

### 핵심개념
- RFC 2235 타임라인은 1970s~1990s 핵심 사건을 정리하며, 1983-01-01 NCP에서 TCP/IP로 전환된 사건을 포함한다.([RFC 2235](https://www.rfc-editor.org/rfc/rfc2235))
- RFC 2235에는 1986년 IETF 출범, 1990년 ARPANET 종료 등 인터넷 형성기의 중요 이벤트가 기록되어 있다.([RFC 2235](https://www.rfc-editor.org/rfc/rfc2235))
- 이후 인터넷은 IPv6 표준화(RFC 8200), TCP 현대화 통합 규격(RFC 9293)처럼 RFC 체계로 계속 진화했다.([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))

### 심화 포인트
- 역사 학습의 목적은 연도 암기가 아니라 "왜 그런 설계가 필요했는지"를 이해하는 데 있다.
- 기술 선택은 항상 규모, 상호운용성, 보안, 운영 복잡도의 균형 문제였다.([RFC 1958](https://www.rfc-editor.org/rfc/rfc1958))([RFC 3439](https://www.rfc-editor.org/rfc/rfc3439))

### 오해하기 쉬운 포인트
- "인터넷 표준은 한 번 정해지면 고정"이라는 오해가 있다. 실제로는 업데이트/폐기/대체가 반복된다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))
- RFC 2235는 1997년 시점의 역사 요약이므로, 최근 변화까지 모두 담고 있지는 않다.([RFC 2235](https://www.rfc-editor.org/rfc/rfc2235))

### 체크 질문
1. 1983년 전환 사건(NCP -> TCP/IP)의 의미를 상호연결 관점에서 설명할 수 있는가?
2. RFC 업데이트가 필요한 이유를 기술/운영 관점에서 말할 수 있는가?
3. 인터넷 역사를 현재 아키텍처 의사결정에 어떻게 연결할 수 있는가?

### 한 줄 요약
- 인터넷 역사는 공개 표준의 누적 진화 과정이며, 핵심은 변화 대응과 상호운용성의 지속적 확보다.([RFC 2235](https://www.rfc-editor.org/rfc/rfc2235))([RFC 1958](https://www.rfc-editor.org/rfc/rfc1958))

## 1.8 요약
### 학습목표
- 1.1~1.7의 개념을 하나의 시스템 시각으로 연결할 수 있다.
- 코어/가장자리/계층/성능/보안을 분리해서 설명하고 다시 통합할 수 있다.

### 핵심개념
- 인터넷은 호스트(가장자리)와 라우터(코어), 그리고 계층형 프로토콜 모델이 결합된 구조다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- 성능 이슈(지연/손실/처리율)와 보안 이슈(기밀성/무결성/가용성)는 별개가 아니라 운영에서 동시에 관리해야 할 축이다.([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([RFC 7680](https://www.rfc-editor.org/rfc/rfc7680))([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))

### 심화 포인트
- Chapter 1의 핵심은 "정의 암기"보다 "구조적 연결"이다:  
  인터넷 정의 -> 가장자리/코어 분리 -> 계층 서비스 모델 -> 성능/보안 운영.

### 오해하기 쉬운 포인트
- 요약 섹션은 새로운 주장 추가가 아니라, 앞서 검증된 개념을 재구성하는 단계다.

### 체크 질문
1. 인터넷 동작을 `호스트-코어-계층-성능-보안` 순으로 3분 내 설명할 수 있는가?
2. 성능 최적화와 보안 강화가 충돌할 때 판단 기준을 무엇으로 둘 것인가?
3. Chapter 2(애플리케이션 계층)로 넘어가기 전에 반드시 고정해야 할 선행 개념은 무엇인가?

### 한 줄 요약
- Chapter 1은 인터넷을 구성요소별로 분해해 이해하고, 다시 운영 가능한 하나의 시스템으로 통합하는 장이다.

## Chapter 1 핵심 연결 요약
- 인터넷은 공개 프로토콜 기반의 상호연결 시스템이며, 호스트와 라우터의 역할 분리가 기본 구조다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([Cisco: What is a router?](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))
- 코어는 라우팅/포워딩으로 패킷을 전달하고, 가장자리는 애플리케이션 가치를 생성·소비한다.([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))
- 계층 모델은 복잡성을 분리하고(IP/TCP/UDP), 성능·신뢰성·지연 요구를 서비스 모델로 선택하게 한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))
- 지연·손실·처리율은 함께 봐야 하며, 측정 정의를 엄밀히 구분해야 정확한 진단이 가능하다.([RFC 7679](https://www.rfc-editor.org/rfc/rfc7679))([RFC 7680](https://www.rfc-editor.org/rfc/rfc7680))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))
- 보안은 위협 모델 기반의 다계층 방어이며, 운영 목표(CIA)와 성능 목표를 동시에 설계해야 한다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))

## 참고문헌

- IETF RFC 1958, *Architectural Principles of the Internet*. ([링크](https://www.rfc-editor.org/rfc/rfc1958))
- IETF RFC 1122, *Requirements for Internet Hosts -- Communication Layers*. ([링크](https://www.rfc-editor.org/rfc/rfc1122))
- IETF RFC 791, *Internet Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc791))
- IETF RFC 8200, *Internet Protocol, Version 6 (IPv6) Specification*. ([링크](https://www.rfc-editor.org/rfc/rfc8200))
- IETF RFC 768, *User Datagram Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc768))
- IETF RFC 9293, *Transmission Control Protocol (TCP)*. ([링크](https://www.rfc-editor.org/rfc/rfc9293))
- IETF RFC 7679, *A One-Way Delay Metric for IP Performance Metrics (IPPM)*. ([링크](https://www.rfc-editor.org/rfc/rfc7679))
- IETF RFC 7680, *A One-Way Loss Metric for IP Performance Metrics (IPPM)*. ([링크](https://www.rfc-editor.org/rfc/rfc7680))
- IETF RFC 4443, *Internet Control Message Protocol (ICMPv6) for IPv6*. ([링크](https://www.rfc-editor.org/rfc/rfc4443))
- IETF RFC 7258, *Pervasive Monitoring Is an Attack*. ([링크](https://www.rfc-editor.org/rfc/rfc7258))
- IETF RFC 3552, *Guidelines for Writing RFC Text on Security Considerations*. ([링크](https://www.rfc-editor.org/rfc/rfc3552))
- IETF RFC 2235, *Hobbes' Internet Timeline*. ([링크](https://www.rfc-editor.org/rfc/rfc2235))
- IETF RFC 3439, *Some Internet Architectural Guidelines and Philosophy*. ([링크](https://www.rfc-editor.org/rfc/rfc3439))
- IBM, *What is computer networking?* ([링크](https://www.ibm.com/think/topics/networking))
- IBM, *What is latency?* ([링크](https://www.ibm.com/think/topics/latency))
- IBM, *What is network security?* ([링크](https://www.ibm.com/think/topics/network-security))
- IBM, *What is a Distributed Denial-of-Service (DDoS) attack?* ([링크](https://www.ibm.com/think/topics/ddos))
- IBM, *What is zero trust?* ([링크](https://www.ibm.com/think/topics/zero-trust))
- Cisco, *What is a router?* ([링크](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))
- Cisco, *What is routing?* ([링크](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- Cisco, *What is network security?* ([링크](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))
- Cisco, *What is a cyberattack?* ([링크](https://www.cisco.com/site/us/en/learn/topics/security/what-is-a-cyberattack.html))
- Cisco, *What is low latency?* ([링크](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))
