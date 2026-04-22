# Chapter 3 학습 문서

## 작성/검증 원칙
- 출처 우선순위: IETF 표준/BCP 및 RFC를 1차 기준으로 두고, IBM/Cisco 공식 문서로 개념 설명을 교차검증한다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))([Cisco: Configuring TCP](https://www.cisco.com/en/US/docs/ios-xml/ios/ipapp/configuration/15-0sy/iap-tcp.html))
- 추정 배제: 트랜스포트 계층 동작, 혼잡 제어 규칙, 타이머 알고리즘은 근거가 확인된 내용만 기록한다.
- 용어 일관성: `세그먼트`, `포트`, `혼잡 윈도우(cwnd)`, `재전송 타임아웃(RTO)`, `RTT`, `ACK`를 구분해 사용한다.
- 업데이트 기준일: 2026-04-22 (KST).

## 3.1 트랜스포트 계층 서비스 및 개요
### 학습목표
- 트랜스포트 계층이 종단 간 프로세스 통신을 제공하는 역할을 설명할 수 있다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))
- TCP와 UDP가 제공하는 서비스 모델 차이를 설명할 수 있다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))
- 애플리케이션 요구사항에 따라 전송 프로토콜을 선택하는 기준을 정리할 수 있다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))([IBM: What is the OSI model?](https://www.ibm.com/think/topics/osi-model))

### 핵심개념
- 트랜스포트 계층은 애플리케이션 간 데이터 전달을 위한 종단 간 기능(다중화, 오류/흐름 제어 등)을 담당한다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))
- TCP는 연결지향 신뢰 전송을 제공하고, UDP는 최소 오버헤드 데이터그램 전달을 제공한다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))
- 트랜스포트 계층의 선택은 지연 민감도, 손실 허용도, 구현 복잡도에 직접 영향을 준다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 심화 포인트
- 네트워크 계층(IP)이 "호스트 간 전달"이라면, 트랜스포트 계층은 "프로세스 간 전달" 문제를 해결한다.
- 인터넷 안정성 관점에서 UDP 사용 시에도 혼잡 제어 원칙 준수가 필수다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 오해하기 쉬운 포인트
- "트랜스포트 계층 = TCP"는 부정확하다. UDP 및 UDP 기반 신규 전송 프로토콜도 같은 계층 문제를 다룬다.([RFC 768](https://www.rfc-editor.org/rfc/rfc768))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))
- "신뢰성은 네트워크가 보장한다"는 오해가 있다. 인터넷에서는 주로 종단 호스트의 전송 계층이 이를 구현한다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))

### 체크 질문
1. 네트워크 계층과 트랜스포트 계층의 책임 경계를 한 문장으로 구분할 수 있는가?
2. 같은 애플리케이션이라도 TCP와 UDP 선택이 달라지는 상황은 무엇인가?
3. UDP 기반 설계에서 반드시 추가해야 할 제어 기능은 무엇인가?

### 한 줄 요약
- 트랜스포트 계층은 프로세스 간 전달 품질을 결정하는 계층이며, TCP/UDP 선택은 서비스 특성 자체를 바꾼다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))

## 3.2 다중화와 역다중화
### 학습목표
- 포트 번호 기반 다중화/역다중화 원리를 설명할 수 있다.([RFC 6335](https://www.rfc-editor.org/rfc/rfc6335))([RFC 7605](https://www.rfc-editor.org/rfc/rfc7605))
- 전송 세션 식별이 왜 `IP + 포트` 조합으로 이뤄지는지 설명할 수 있다.([RFC 6335](https://www.rfc-editor.org/rfc/rfc6335))
- 시스템/등록/동적 포트 범위와 실무 의미를 설명할 수 있다.([RFC 6335](https://www.rfc-editor.org/rfc/rfc6335))

### 핵심개념
- 포트 번호는 전송 계층에서 프로세스 단위 식별과 역다중화를 수행하는 핵심 식별자다.([RFC 7605](https://www.rfc-editor.org/rfc/rfc7605))
- 송신 측은 여러 애플리케이션 데이터를 하나의 전송 경로로 다중화하고, 수신 측은 포트 정보로 목적 프로세스에 분배한다.
- IANA 포트 레지스트리는 시스템(0-1023), 사용자(1024-49151), 동적(49152-65535) 범위를 규정한다.([RFC 6335](https://www.rfc-editor.org/rfc/rfc6335))

### 심화 포인트
- 포트는 단순 숫자가 아니라 서비스 운영(방화벽 정책, NAT 동작, 관측/모니터링)과 강하게 결합된다.([RFC 7605](https://www.rfc-editor.org/rfc/rfc7605))
- 서비스 설계 시 불필요한 포트 소비를 줄이는 것은 장기 상호운용성에 유리하다.([RFC 7605](https://www.rfc-editor.org/rfc/rfc7605))

### 오해하기 쉬운 포인트
- "포트는 애플리케이션 이름과 1:1로 고정된다"는 오해가 있다. 같은 프로토콜도 구성/정책에 따라 다른 포트를 사용할 수 있다.
- "동적 포트는 임의라서 의미가 없다"는 오해가 있다. 클라이언트 세션 식별과 충돌 회피에 핵심적이다.([RFC 6335](https://www.rfc-editor.org/rfc/rfc6335))

### 체크 질문
1. 역다중화에서 포트가 없다면 어떤 문제가 생기는가?
2. 서비스 설계 시 고정 포트와 동적 포트를 각각 어디에 배치해야 하는가?
3. NAT/방화벽 환경에서 포트 설계가 중요한 이유는 무엇인가?

### 한 줄 요약
- 다중화/역다중화는 포트 기반 세션 식별로 구현되며, 전송 계층 확장성과 운영 안정성의 기반이다.([RFC 6335](https://www.rfc-editor.org/rfc/rfc6335))

## 3.3 비연결형 트랜스포트: UDP
### 학습목표
- UDP의 최소 기능 모델(비연결, 비신뢰, 메시지 지향)을 설명할 수 있다.([RFC 768](https://www.rfc-editor.org/rfc/rfc768))
- UDP 사용 시 애플리케이션 설계자가 감당해야 할 책임을 설명할 수 있다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))
- UDP가 선택되는 대표 시나리오(지연 민감, 커스텀 제어)를 설명할 수 있다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))

### 핵심개념
- UDP는 최소 메커니즘의 데이터그램 전송 프로토콜로, 순서·재전송·혼잡제어를 내장하지 않는다.([RFC 768](https://www.rfc-editor.org/rfc/rfc768))
- UDP 기반 애플리케이션은 메시지 크기, 손실 처리, 재전송 정책, 공정성 확보를 직접 설계해야 한다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))
- QUIC처럼 UDP 위에서 별도 신뢰/혼잡 제어를 구현하는 현대 전송 프로토콜도 존재한다.([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))([RFC 9002](https://www.rfc-editor.org/rfc/rfc9002))

### 심화 포인트
- UDP는 오버헤드 절감에 유리하지만, 네트워크 친화성(혼잡 반응) 부재는 전체 인터넷 안정성에 위험을 준다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))
- 큰 데이터그램/단편화는 신뢰성과 효율을 떨어뜨릴 수 있어 PMTU 고려가 중요하다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 오해하기 쉬운 포인트
- "UDP는 빠르기만 하고 제약이 없다"는 오해가 있다. 지연 이점 대신 신뢰성/혼잡제어 비용이 애플리케이션으로 이동한다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))
- "UDP는 스트리밍에만 쓴다"는 단정은 틀리다. 다양한 제어/데이터 프로토콜에서 목적에 따라 선택된다.

### 체크 질문
1. UDP 기반 프로토콜이 인터넷에서 안전하게 공존하려면 어떤 규칙을 따라야 하는가?
2. UDP와 TCP의 성능 비교에서 반드시 함께 봐야 할 지표는 무엇인가?
3. QUIC이 UDP를 선택한 기술적 이유를 2가지 이상 설명할 수 있는가?

### 한 줄 요약
- UDP는 빠른 최소 전송 도구지만, 신뢰성과 혼잡 제어를 애플리케이션/상위 프로토콜이 책임지는 구조다.([RFC 768](https://www.rfc-editor.org/rfc/rfc768))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

## 3.4 신뢰적인 데이터 전송의 원리
### 학습목표
- 신뢰 전송이 필요한 이유(손실, 중복, 순서 뒤바뀜)를 설명할 수 있다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- ACK, 시퀀스 번호, 재전송 타이머의 상호작용을 설명할 수 있다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))
- SACK/NewReno 같은 손실 복구 개선 메커니즘의 목적을 설명할 수 있다.([RFC 2018](https://www.rfc-editor.org/rfc/rfc2018))([RFC 6582](https://www.rfc-editor.org/rfc/rfc6582))

### 핵심개념
- 신뢰 전송은 누락 없는 전달뿐 아니라 올바른 순서와 중복 제거까지 포함한다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- RTT 기반 RTO 계산은 재전송 공격성을 제한하며, 타이머 백오프는 혼잡 악화를 방지한다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))
- SACK은 수신된 비연속 블록 정보를 제공해 불필요 재전송을 줄인다.([RFC 2018](https://www.rfc-editor.org/rfc/rfc2018))

### 심화 포인트
- 신뢰 전송은 성능과 상충한다. 너무 빠른 재전송은 혼잡을 악화시키고, 너무 늦은 재전송은 지연을 키운다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))
- 다중 손실 상황에서 NewReno/SACK 계열의 차이는 회복 속도와 불필요 타임아웃 빈도에 영향을 준다.([RFC 6582](https://www.rfc-editor.org/rfc/rfc6582))([RFC 2018](https://www.rfc-editor.org/rfc/rfc2018))

### 오해하기 쉬운 포인트
- "재전송만 하면 신뢰성은 해결된다"는 오해가 있다. 타이머·혼잡 반응·수신 확인 구조가 함께 필요하다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))
- "신뢰성은 전송 계층 내부 문제라 앱에는 영향이 없다"는 오해가 있다. 타임아웃/지터는 사용자 체감에 직접 반영된다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 체크 질문
1. RTO 계산에서 SRTT/RTTVAR를 분리해 추적하는 이유는 무엇인가?
2. SACK 부재 환경에서 다중 손실 복구가 어려운 이유는 무엇인가?
3. 신뢰성 강화와 지연 최소화가 충돌할 때 어떤 기준으로 조정해야 하는가?

### 한 줄 요약
- 신뢰 전송은 ACK·시퀀스·타이머·손실 복구 알고리즘의 균형 설계로 구현된다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))

## 3.5 연결지향형 트랜스포트: TCP
### 학습목표
- TCP의 연결 수립/종료와 상태 기반 동작을 설명할 수 있다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- TCP가 제공하는 핵심 서비스(신뢰성, 순서 보장, 흐름 제어, 다중화)를 설명할 수 있다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([Cisco: Configuring TCP](https://www.cisco.com/en/US/docs/ios-xml/ios/ipapp/configuration/15-0sy/iap-tcp.html))
- TCP 성능 확장 옵션(윈도우 스케일, 타임스탬프, SACK)의 역할을 설명할 수 있다.([RFC 7323](https://www.rfc-editor.org/rfc/rfc7323.html))([RFC 2018](https://www.rfc-editor.org/rfc/rfc2018))

### 핵심개념
- TCP는 바이트 스트림 기반의 연결지향 전송을 제공하며 송수신 상태를 유지한다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- 흐름 제어와 혼잡 제어는 모두 전송률을 조절하지만 목적이 다르다(수신자 보호 vs 네트워크 보호).([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 2914](https://www.rfc-editor.org/rfc/rfc2914))
- 고대역폭·장거리 환경에서는 확장 옵션이 실효 처리량과 안정성에 큰 영향을 준다.([RFC 7323](https://www.rfc-editor.org/rfc/rfc7323.html))

### 심화 포인트
- TCP는 단순 신뢰 전송이 아니라 다양한 손실/재정렬/혼잡 상황에서 안전하게 동작하도록 지속 확장되어 왔다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- 현대 구현은 NewReno, CUBIC, RACK-TLP 등 복합 알고리즘으로 운영 특성을 개선한다.([RFC 6582](https://www.rfc-editor.org/rfc/rfc6582))([RFC 9438](https://www.rfc-editor.org/rfc/rfc9438.html))([RFC 8985](https://www.rfc-editor.org/rfc/rfc8985.html))

### 오해하기 쉬운 포인트
- "TCP는 느리다"는 일반화는 부정확하다. 네트워크 조건과 혼잡 제어 알고리즘 선택에 따라 성능이 크게 달라진다.
- "TCP 신뢰성은 무조건 이득"도 틀리다. 지연 민감 서비스에서는 재전송/헤드오브라인 영향이 단점이 될 수 있다.

### 체크 질문
1. TCP의 흐름 제어와 혼잡 제어를 각각 어떤 신호로 판단하는가?
2. Window Scale/Timestamp/SACK 중 처리량에 가장 직접적인 영향을 주는 요소는 무엇인가?
3. TCP를 유지하면서 성능을 높이기 위한 현실적 튜닝 축은 무엇인가?

### 한 줄 요약
- TCP는 상태 기반 신뢰 바이트스트림 서비스이며, 확장 옵션과 복구 알고리즘이 실성능을 결정한다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))

## 3.6 혼잡 제어의 원리
### 학습목표
- 인터넷에서 혼잡 제어가 필수인 이유(혼잡 붕괴 방지, 공정성)를 설명할 수 있다.([RFC 2914](https://www.rfc-editor.org/rfc/rfc2914))
- 손실 기반 신호와 ECN 기반 신호의 차이를 설명할 수 있다.([RFC 3168](https://www.rfc-editor.org/rfc/rfc3168))([RFC 8311](https://www.rfc-editor.org/rfc/rfc8311.html))
- 혼잡 제어와 애플리케이션 품질(지연/처리율/손실)의 관계를 설명할 수 있다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))([Cisco: Troubleshooting Network Latency and Packet Drops](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html))

### 핵심개념
- 혼잡 제어의 1차 목적은 인터넷 안정성 유지와 혼잡 붕괴 방지다.([RFC 2914](https://www.rfc-editor.org/rfc/rfc2914))
- 혼잡 신호는 패킷 손실뿐 아니라 ECN 마킹으로도 전달될 수 있다.([RFC 3168](https://www.rfc-editor.org/rfc/rfc3168))
- 전송 프로토콜은 혼잡 발생 시 전송률을 줄여 네트워크와 공존해야 한다.([RFC 2914](https://www.rfc-editor.org/rfc/rfc2914))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 심화 포인트
- 혼잡 제어는 개별 흐름 성능 최적화 문제가 아니라 다수 흐름 공존 질서를 유지하는 시스템 문제다.
- ECN 기반 제어는 손실 발생 이전 신호를 활용해 지연/손실을 줄일 잠재력이 있다.([RFC 3168](https://www.rfc-editor.org/rfc/rfc3168))([RFC 8311](https://www.rfc-editor.org/rfc/rfc8311.html))

### 오해하기 쉬운 포인트
- "혼잡 제어는 라우터만의 책임"이 아니다. 종단 시스템의 반응이 핵심이다.([RFC 2914](https://www.rfc-editor.org/rfc/rfc2914))
- "대역폭이 크면 혼잡 제어 중요도가 낮다"는 오해가 있다. 고속망일수록 부적절한 제어가 더 큰 피해를 낸다.

### 체크 질문
1. 혼잡 제어가 없는 UDP 트래픽이 대규모로 유입되면 어떤 문제가 생기는가?
2. 손실 기반과 ECN 기반 제어의 관측 신호 차이는 무엇인가?
3. 혼잡 제어에서 공정성은 어떤 단위(흐름/호스트/서비스)로 바라봐야 하는가?

### 한 줄 요약
- 혼잡 제어는 성능 최적화 이전에 인터넷 안정성과 공정성을 보장하는 필수 메커니즘이다.([RFC 2914](https://www.rfc-editor.org/rfc/rfc2914))

## 3.7 TCP 혼잡 제어
### 학습목표
- TCP 혼잡 제어의 기본 4요소(슬로스타트, 혼잡회피, 빠른 재전송, 빠른 회복)를 설명할 수 있다.([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))
- RTO/손실 감지/ECN이 혼잡 윈도우 조정에 미치는 영향을 설명할 수 있다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))([RFC 3168](https://www.rfc-editor.org/rfc/rfc3168))
- 현대 TCP 알고리즘(CUBIC, RACK-TLP)의 의의를 설명할 수 있다.([RFC 9438](https://www.rfc-editor.org/rfc/rfc9438.html))([RFC 8985](https://www.rfc-editor.org/rfc/rfc8985.html))

### 핵심개념
- RFC 5681은 TCP 혼잡 제어의 표준 기반 동작을 정의한다.([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))
- RFC 6298은 재전송 타이머 계산을 표준화해 과도한 재전송 공격성을 억제한다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))
- CUBIC(RFC 9438)은 고대역폭·장거리 환경에서 Reno 대비 확장성과 안정성을 개선한다.([RFC 9438](https://www.rfc-editor.org/rfc/rfc9438.html))
- RACK-TLP(RFC 8985)는 시간 기반 손실 추론과 꼬리 손실 탐지로 회복 지연을 줄인다.([RFC 8985](https://www.rfc-editor.org/rfc/rfc8985.html))

### 심화 포인트
- TCP 혼잡 제어는 단일 알고리즘이 아니라, 혼잡 제어 + 손실 탐지 + 재전송 타이머의 결합 시스템이다.
- 운영 환경에서는 RTT 분포, 재정렬, ACK 정책이 알고리즘 체감 성능을 크게 바꾼다.([RFC 8985](https://www.rfc-editor.org/rfc/rfc8985.html))

### 오해하기 쉬운 포인트
- "TCP 혼잡 제어는 이미 완성된 고정 규칙"이 아니다. 표준은 유지되지만 구현/알고리즘은 계속 진화한다.([RFC 9438](https://www.rfc-editor.org/rfc/rfc9438.html))
- "손실 = 항상 혼잡"도 단순화다. 무선 구간/버스트 오류 등 다른 원인도 존재해 해석에 주의가 필요하다.

### 체크 질문
1. 슬로스타트와 혼잡회피의 전환 기준은 무엇인가?
2. RACK-TLP가 DupAck 임계 기반 접근 대비 유리한 경우는 언제인가?
3. CUBIC과 Reno를 같은 환경에서 비교할 때 주의할 지표는 무엇인가?

### 한 줄 요약
- TCP 혼잡 제어는 cwnd 제어, 손실 감지, 타이머 정책이 결합된 적응형 시스템이며, CUBIC/RACK-TLP로 지속 발전 중이다.([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))([RFC 9438](https://www.rfc-editor.org/rfc/rfc9438.html))

## 3.8 트랜스포트 계층 기능의 발전
### 학습목표
- TCP의 현대 확장(성능/복구/연결지연 완화)의 흐름을 설명할 수 있다.([RFC 7323](https://www.rfc-editor.org/rfc/rfc7323.html))([RFC 7413](https://www.rfc-editor.org/rfc/rfc7413.html))
- 멀티패스 TCP와 QUIC이 전통 TCP의 제약을 어떻게 보완하는지 설명할 수 있다.([RFC 8684](https://www.rfc-editor.org/info/rfc8684))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))
- 전송 계층 진화가 애플리케이션 개발/운영 전략에 주는 함의를 설명할 수 있다.

### 핵심개념
- TCP는 SACK, Window Scale, 타임스탬프, NewReno, CUBIC, RACK-TLP 등으로 점진 진화했다.([RFC 2018](https://www.rfc-editor.org/rfc/rfc2018))([RFC 7323](https://www.rfc-editor.org/rfc/rfc7323.html))([RFC 6582](https://www.rfc-editor.org/rfc/rfc6582))([RFC 9438](https://www.rfc-editor.org/rfc/rfc9438.html))([RFC 8985](https://www.rfc-editor.org/rfc/rfc8985.html))
- MPTCP는 여러 경로를 하나의 논리 연결에서 활용해 처리량/복원력을 개선한다.([RFC 8684](https://www.rfc-editor.org/info/rfc8684))
- QUIC은 UDP 위에서 보안·다중 스트림·혼잡 제어를 통합해 지연과 배포 유연성을 개선한다.([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))([RFC 9002](https://www.rfc-editor.org/rfc/rfc9002))

### 심화 포인트
- 전송 계층 진화의 공통 방향은 `지연 감소`, `손실 회복 고도화`, `경로 다양성 활용`, `배포 실용성 향상`이다.
- 애플리케이션 관점에서는 "TCP냐 UDP냐" 이분법보다, 상위에서 어떤 전송 기능 집합을 선택할지가 핵심이다.

### 오해하기 쉬운 포인트
- "새 프로토콜이 기존 TCP를 완전히 대체한다"는 관점은 과도하다. 실제 서비스는 환경별로 공존 전략을 취한다.
- "혁신 = 더 공격적인 전송"도 오해다. 인터넷 공정성/안정성 제약은 여전히 핵심이다.([RFC 2914](https://www.rfc-editor.org/rfc/rfc2914))

### 체크 질문
1. MPTCP와 QUIC이 각각 해결하려는 핵심 문제는 무엇인가?
2. 전송 계층 발전에서 호환성(중간장비, 운영 정책) 이슈가 중요한 이유는?
3. 서비스별로 전송 계층 전략을 선택할 때 우선순위는 무엇인가?

### 한 줄 요약
- 트랜스포트 계층은 TCP의 점진 진화와 QUIC/MPTCP 같은 확장 경로를 통해 성능·복원력·배포성을 함께 개선하고 있다.([RFC 8684](https://www.rfc-editor.org/info/rfc8684))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))

## 3.9 요약
### 학습목표
- 3.1~3.8의 내용을 전송 계층 설계 관점으로 통합 설명할 수 있다.
- 신뢰성, 지연, 공정성, 확장성의 균형 관계를 구조적으로 설명할 수 있다.

### 핵심개념
- 트랜스포트 계층은 단순 데이터 전달이 아니라 포트 기반 세션 식별, 신뢰 회복, 혼잡 반응을 통합 제공한다.([RFC 6335](https://www.rfc-editor.org/rfc/rfc6335))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- TCP와 UDP는 상반된 기본 철학을 가지며, 현대 전송은 이를 보완하는 방향(QUIC, MPTCP)으로 확장되고 있다.([RFC 768](https://www.rfc-editor.org/rfc/rfc768))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))([RFC 8684](https://www.rfc-editor.org/info/rfc8684))

### 심화 포인트
- Chapter 3의 핵심은 "알고리즘 이름 암기"가 아니라, 네트워크 조건 변화에 대해 전송 계층이 어떻게 적응하는지 이해하는 것이다.

### 오해하기 쉬운 포인트
- 요약 섹션은 새 주장 추가가 아니라, 앞선 검증 사실을 설계 관점으로 재구성하는 단계다.

### 체크 질문
1. TCP 혼잡 제어와 신뢰 전송을 분리해서 설명할 수 있는가?
2. UDP 기반 설계에서 인터넷 공존성을 확보하기 위한 최소 원칙은 무엇인가?
3. Chapter 4(네트워크 계층 데이터 평면) 학습 전에 반드시 고정해야 할 전송 계층 개념은 무엇인가?

### 한 줄 요약
- Chapter 3은 전송 계층이 신뢰성·혼잡제어·세션 식별을 결합해 인터넷 통신 품질을 조율하는 방식을 다루는 장이다.

## Chapter 3 핵심 연결 요약
- 트랜스포트 계층은 프로세스 간 통신을 위해 포트 기반 다중화/역다중화와 종단 간 제어 기능을 제공한다.([RFC 6335](https://www.rfc-editor.org/rfc/rfc6335))([RFC 7605](https://www.rfc-editor.org/rfc/rfc7605))
- UDP는 최소 기능 전달, TCP는 신뢰·순서·흐름 제어를 제공하며, 선택의 기준은 애플리케이션 요구사항이다.([RFC 768](https://www.rfc-editor.org/rfc/rfc768))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))
- 신뢰 전송은 ACK/시퀀스/타이머/RTO 계산 및 손실 복구(SACK, NewReno) 조합으로 구현된다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))([RFC 2018](https://www.rfc-editor.org/rfc/rfc2018))([RFC 6582](https://www.rfc-editor.org/rfc/rfc6582))
- 혼잡 제어는 인터넷 안정성의 핵심 원리이며, TCP는 이를 slow start/congestion avoidance 등으로 구체화한다.([RFC 2914](https://www.rfc-editor.org/rfc/rfc2914))([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))
- 현대 전송은 CUBIC, RACK-TLP, MPTCP, QUIC으로 진화하며 지연·복원력·확장성 개선을 추구한다.([RFC 9438](https://www.rfc-editor.org/rfc/rfc9438.html))([RFC 8985](https://www.rfc-editor.org/rfc/rfc8985.html))([RFC 8684](https://www.rfc-editor.org/info/rfc8684))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))

## 참고문헌
- IETF RFC 1122, *Requirements for Internet Hosts -- Communication Layers*. ([링크](https://www.rfc-editor.org/rfc/rfc1122))
- IETF RFC 9293, *Transmission Control Protocol (TCP)*. ([링크](https://www.rfc-editor.org/rfc/rfc9293))
- IETF RFC 768, *User Datagram Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc768))
- IETF RFC 8085, *UDP Usage Guidelines*. ([링크](https://www.rfc-editor.org/rfc/rfc8085))
- IETF RFC 6335, *IANA Procedures for Service Name and Port Number Registry*. ([링크](https://www.rfc-editor.org/rfc/rfc6335))
- IETF RFC 7605, *Recommendations on Using Assigned Transport Port Numbers*. ([링크](https://www.rfc-editor.org/rfc/rfc7605.html))
- IETF RFC 6298, *Computing TCP's Retransmission Timer*. ([링크](https://www.rfc-editor.org/rfc/rfc6298))
- IETF RFC 2018, *TCP Selective Acknowledgment Options*. ([링크](https://www.rfc-editor.org/rfc/rfc2018))
- IETF RFC 6582, *The NewReno Modification to TCP's Fast Recovery Algorithm*. ([링크](https://www.rfc-editor.org/rfc/rfc6582))
- IETF RFC 5681, *TCP Congestion Control*. ([링크](https://www.rfc-editor.org/rfc/rfc5681))
- IETF RFC 2914, *Congestion Control Principles*. ([링크](https://www.rfc-editor.org/rfc/rfc2914))
- IETF RFC 3168, *The Addition of Explicit Congestion Notification (ECN) to IP*. ([링크](https://www.rfc-editor.org/rfc/rfc3168))
- IETF RFC 8311, *Relaxing Restrictions on ECN Experimentation*. ([링크](https://www.rfc-editor.org/rfc/rfc8311.html))
- IETF RFC 7323, *TCP Extensions for High Performance*. ([링크](https://www.rfc-editor.org/rfc/rfc7323.html))
- IETF RFC 9438, *CUBIC for Fast and Long-Distance Networks*. ([링크](https://www.rfc-editor.org/rfc/rfc9438.html))
- IETF RFC 8985, *The RACK-TLP Loss Detection Algorithm for TCP*. ([링크](https://www.rfc-editor.org/rfc/rfc8985.html))
- IETF RFC 7413, *TCP Fast Open*. ([링크](https://www.rfc-editor.org/rfc/rfc7413.html))
- IETF RFC 8684, *TCP Extensions for Multipath Operation with Multiple Addresses*. ([링크](https://www.rfc-editor.org/info/rfc8684))
- IETF RFC 9000, *QUIC: A UDP-Based Multiplexed and Secure Transport*. ([링크](https://www.rfc-editor.org/rfc/rfc9000))
- IETF RFC 9002, *QUIC Loss Detection and Congestion Control*. ([링크](https://www.rfc-editor.org/rfc/rfc9002))
- IBM, *What is computer networking?*. ([링크](https://www.ibm.com/think/topics/networking))
- IBM, *What is the OSI model?*. ([링크](https://www.ibm.com/think/topics/osi-model))
- IBM, *What is latency?*. ([링크](https://www.ibm.com/think/topics/latency))
- Cisco, *Configuring TCP*. ([링크](https://www.cisco.com/en/US/docs/ios-xml/ios/ipapp/configuration/15-0sy/iap-tcp.html))
- Cisco, *Troubleshooting Network Latency and Packet Drops on Catalyst 9000 Switches*. ([링크](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html))
- Cisco, *What is low latency?*. ([링크](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))
