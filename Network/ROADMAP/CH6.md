# Chapter 6 학습 문서

## 작성/검증 원칙
- 출처 우선순위: IETF 표준/BCP 및 RFC를 1차 기준으로 두고, IBM/Cisco 공식 문서로 개념 설명을 교차검증한다.([RFC 894](https://www.rfc-editor.org/rfc/rfc894))([RFC 3031](https://www.rfc-editor.org/rfc/rfc3031))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- 추정 배제: 링크 계층 기능(프레이밍, MAC, 오류 검출, 스위칭, 링크 가상화)은 RFC와 공식 문서로 확인된 사실만 기록한다.
- 용어 일관성: `프레임`, `MAC 주소`, `오류 검출(Error Detection)`, `재전송`, `스위칭`, `브로드캐스트 도메인`, `오버레이/가상화`를 구분해 사용한다.([RFC 826](https://www.rfc-editor.org/rfc/rfc826))([RFC 2464](https://www.rfc-editor.org/rfc/rfc2464))([RFC 3031](https://www.rfc-editor.org/rfc/rfc3031))
- 업데이트 기준일: 2026-04-22 (KST).

## 6.1 링크 계층 소개
### 학습목표
- 링크 계층의 기본 역할(인접 노드 간 프레임 전달)을 설명할 수 있다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([RFC 3819](https://www.rfc-editor.org/rfc/rfc3819))
- 패킷(IP 데이터그램)과 프레임(링크 계층 PDU)의 차이를 설명할 수 있다.([RFC 894](https://www.rfc-editor.org/rfc/rfc894))([RFC 2464](https://www.rfc-editor.org/rfc/rfc2464))
- 링크 계층 기능이 상위 계층 지연/손실에 어떤 영향을 주는지 설명할 수 있다.([RFC 3819](https://www.rfc-editor.org/rfc/rfc3819))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 핵심개념
- 링크 계층은 물리적으로 인접한 노드 사이에서 프레임 캡슐화/전달/오류 검출을 담당한다.([RFC 3819](https://www.rfc-editor.org/rfc/rfc3819))
- IP 패킷은 링크 계층 프레임에 실려 홉 단위로 전달되며, 홉마다 링크 기술(Ethernet, Wi-Fi 등)이 달라질 수 있다.([RFC 894](https://www.rfc-editor.org/rfc/rfc894))([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))
- 링크 계층의 대역폭, 큐잉, 오류율은 종단 간 처리율과 지연 품질에 직접 반영된다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 심화 포인트
- 네트워크 성능 문제는 상위 계층만 분석해서는 놓치기 쉽고, 실제 병목은 링크 계층 큐/드롭에서 시작되는 경우가 많다.
- 동일 IP 계층 위에서도 링크 기술 특성(MTU, 오버헤드, 오류 검출 방식)이 달라 체감 품질이 변한다.([RFC 3819](https://www.rfc-editor.org/rfc/rfc3819))

### 오해하기 쉬운 포인트
- "링크 계층은 단순 전기/물리 전달만 담당한다"는 오해가 있다. 실제로는 프레이밍, 주소화, 오류 검출 같은 논리 기능을 포함한다.
- "IP가 있으니 링크 계층 차이는 중요하지 않다"는 단정은 부정확하다. 링크 계층 품질은 상위 계층 성능의 바닥 조건이다.

### 체크 질문
1. 패킷과 프레임을 전송 단위 관점에서 명확히 구분할 수 있는가?
2. 링크 계층의 드롭 증가가 TCP 처리율에 어떻게 연결되는가?
3. 같은 서비스라도 링크 기술이 달라지면 어떤 운영 지표가 변하는가?

### 한 줄 요약
- 링크 계층은 홉 단위 전달의 실행 계층이며, 프레이밍과 오류 검출 품질이 상위 계층 성능을 결정한다.([RFC 3819](https://www.rfc-editor.org/rfc/rfc3819))([RFC 894](https://www.rfc-editor.org/rfc/rfc894))

## 6.2 오류 검출 및 정정 기술
### 학습목표
- 오류 검출과 오류 정정의 차이를 설명할 수 있다.([RFC 1662](https://www.rfc-editor.org/rfc/rfc1662))([RFC 1071](https://www.rfc-editor.org/rfc/rfc1071))
- 체크섬/CRC/FEC/재전송의 역할과 적용 지점을 설명할 수 있다.([RFC 1662](https://www.rfc-editor.org/rfc/rfc1662))([RFC 6363](https://www.rfc-editor.org/rfc/rfc6363))
- 링크 계층 오류 처리와 전송 계층 재전송 간 관계를 설명할 수 있다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 핵심개념
- 오류 검출(Error Detection)은 손상 여부를 식별하고, 오류 정정(Error Correction)은 추가 정보를 이용해 손상을 복구한다.([RFC 6363](https://www.rfc-editor.org/rfc/rfc6363))
- PPP 같은 링크 계층 프로토콜은 FCS(프레임 체크 시퀀스)를 사용해 프레임 손상을 검출한다.([RFC 1662](https://www.rfc-editor.org/rfc/rfc1662))
- 인터넷 체크섬은 헤더/데이터 무결성 검증에 사용되지만, 강한 오류 정정 기능을 대체하지는 않는다.([RFC 1071](https://www.rfc-editor.org/rfc/rfc1071))
- 링크 계층 손실이 증가하면 상위 계층(TCP/애플리케이션)이 재전송 부담을 더 많이 지게 된다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))

### 심화 포인트
- 오류 검출을 강하게 하면 계산 오버헤드가 늘고, 약하게 하면 손상 프레임 통과 가능성이 커져 상위 계층 비용이 증가한다.
- FEC는 재전송 지연을 줄일 수 있지만 추가 패리티 오버헤드를 요구하므로 지연/대역폭 균형 설계가 필요하다.([RFC 6363](https://www.rfc-editor.org/rfc/rfc6363))

### 오해하기 쉬운 포인트
- "체크섬이 있으면 데이터 무결성이 완전 보장된다"는 오해가 있다. 검출 강도와 계층별 보호 범위는 다르다.
- "오류 정정은 항상 재전송보다 유리하다"는 단정은 부정확하다. 트래픽 특성과 지연 요구에 따라 최적점이 다르다.

### 체크 질문
1. 오류 검출과 오류 정정을 각각 어떤 시나리오에서 우선 적용해야 하는가?
2. 링크 계층 FCS와 전송 계층 체크섬은 왜 동시에 필요한가?
3. 지연 민감 트래픽에서 FEC와 재전송의 trade-off를 어떻게 판단할 수 있는가?

### 한 줄 요약
- 오류 검출/정정은 링크 품질과 지연 특성을 함께 조절하는 도구이며, 상위 계층 재전송과의 역할 분담이 핵심이다.([RFC 1662](https://www.rfc-editor.org/rfc/rfc1662))([RFC 6363](https://www.rfc-editor.org/rfc/rfc6363))

## 6.3 다중 접속 링크와 프로토콜
### 학습목표
- 다중 접속 링크에서 매체 공유 문제가 왜 발생하는지 설명할 수 있다.([RFC 3819](https://www.rfc-editor.org/rfc/rfc3819))
- LAN 환경의 핵심 주소 해석 동작(ARP/Neighbor Discovery)을 설명할 수 있다.([RFC 826](https://www.rfc-editor.org/rfc/rfc826))([RFC 4861](https://www.rfc-editor.org/rfc/rfc4861))
- 브로드캐스트/멀티캐스트/유니캐스트의 링크 계층 의미를 설명할 수 있다.([RFC 1112](https://www.rfc-editor.org/rfc/rfc1112))([RFC 4541](https://www.rfc-editor.org/rfc/rfc4541))

### 핵심개념
- 다중 접속 링크는 여러 노드가 동일 매체를 공유하므로 접근 충돌 회피와 공정성 확보가 필요하다.
- IPv4 LAN에서는 ARP가 IP 주소를 링크 계층 주소(MAC)로 해석한다.([RFC 826](https://www.rfc-editor.org/rfc/rfc826))
- IPv6 LAN에서는 Neighbor Discovery가 주소 해석과 이웃 도달성 확인 기능을 제공한다.([RFC 4861](https://www.rfc-editor.org/rfc/rfc4861))
- 멀티캐스트 처리 품질은 링크/스위치 동작에 크게 좌우되며, IGMP snooping 같은 운영 권고가 존재한다.([RFC 4541](https://www.rfc-editor.org/rfc/rfc4541))

### 심화 포인트
- 다중 접속 설계에서는 단순 링크 속도보다 브로드캐스트 도메인 크기와 제어 트래픽 비율 관리가 중요하다.
- ARP/ND 이상 동작은 패킷 손실보다 먼저 "연결 불가" 증상으로 나타나는 경우가 많다.

### 오해하기 쉬운 포인트
- "다중 접속 문제는 무선에서만 발생한다"는 오해가 있다. 유선 LAN도 브로드캐스트/제어 트래픽 관리가 필수다.
- "IPv6에서는 ARP가 그대로 사용된다"는 오해가 있다. IPv6는 ND(ICMPv6 기반)를 사용한다.([RFC 4861](https://www.rfc-editor.org/rfc/rfc4861))

### 체크 질문
1. ARP와 ND의 공통점과 차이를 운영 관점에서 설명할 수 있는가?
2. 브로드캐스트 도메인이 커질 때 어떤 부작용이 증가하는가?
3. 멀티캐스트 트래픽 제어가 왜 스위치 설계와 연결되는가?

### 한 줄 요약
- 다중 접속 링크의 핵심은 매체 공유 제어와 주소 해석 안정성 확보이며, ARP/ND가 그 기반을 이룬다.([RFC 826](https://www.rfc-editor.org/rfc/rfc826))([RFC 4861](https://www.rfc-editor.org/rfc/rfc4861))

## 6.4 스위치 근거리 네트워크
### 학습목표
- L2 스위치의 기본 기능(MAC 학습, 포워딩, 플러딩)을 설명할 수 있다.([RFC 894](https://www.rfc-editor.org/rfc/rfc894))([Cisco: What is a router?](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))
- 스위치 기반 LAN에서 루프와 브로드캐스트 폭주 위험을 설명할 수 있다.
- VLAN 분할이 브로드캐스트 도메인 제어에 미치는 영향을 설명할 수 있다.

### 핵심개념
- 스위치는 프레임의 목적지 MAC 주소를 기반으로 출력 포트를 선택해 전달한다.
- 미학습 목적지나 브로드캐스트 프레임은 플러딩되어 전달되므로, 도메인 설계가 중요하다.
- L2 루프는 브로드캐스트 폭주와 MAC 테이블 불안정을 유발할 수 있어 루프 방지 설계가 필수다.
- VLAN은 하나의 물리 스위칭 인프라를 다수 논리 브로드캐스트 도메인으로 분리하는 대표 기법이다.

### 심화 포인트
- 스위치 성능은 단순 포트 속도가 아니라 버퍼, 큐 스케줄링, 테이블 크기, 오버서브스크립션 구조의 영향을 받는다.
- 운영 문제의 상당수는 경로 자체가 아니라 L2 도메인 설계 미흡(과도한 브로드캐스트, 루프, 불균형 트렁크)에서 시작된다.

### 오해하기 쉬운 포인트
- "스위치는 충돌이 없으니 항상 지연 문제가 없다"는 오해가 있다. 큐잉, 버퍼 드롭, 마이크로버스트는 여전히 발생한다.([Cisco: Troubleshooting Network Latency and Packet Drops](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html))
- "VLAN은 보안 기능을 자동 제공한다"는 단정은 부정확하다. VLAN은 분리 도구이며, 보안 정책은 별도로 설계해야 한다.

### 체크 질문
1. 스위치의 MAC 학습 실패/플러딩 증가는 어떤 장애 징후로 나타나는가?
2. 브로드캐스트 도메인 크기를 줄이는 설계가 왜 중요한가?
3. L2 루프와 L3 라우팅 루프의 영향 차이를 설명할 수 있는가?

### 한 줄 요약
- 스위치 LAN의 핵심은 MAC 기반 고속 전달이지만, 루프/브로드캐스트/큐잉을 함께 제어해야 안정성이 확보된다.

## 6.5 링크 가상화: 링크 계층으로서의 네트워크
### 학습목표
- 링크 가상화의 목적(분리, 확장성, 운영 유연성)을 설명할 수 있다.([RFC 3031](https://www.rfc-editor.org/rfc/rfc3031))([RFC 4664](https://www.rfc-editor.org/rfc/rfc4664))
- MPLS, Pseudowire, EVPN 같은 가상화 기술의 역할을 개괄할 수 있다.([RFC 3031](https://www.rfc-editor.org/rfc/rfc3031))([RFC 3985](https://www.rfc-editor.org/rfc/rfc3985))([RFC 7432](https://www.rfc-editor.org/rfc/rfc7432))
- 오버레이 네트워크가 물리 네트워크 위에서 어떻게 동작하는지 설명할 수 있다.([RFC 7348](https://www.rfc-editor.org/rfc/rfc7348))

### 핵심개념
- 링크 가상화는 물리 링크와 독립적으로 논리 연결성을 구성해 서비스 분리와 확장성을 확보한다.([RFC 4664](https://www.rfc-editor.org/rfc/rfc4664))
- MPLS는 레이블 기반 전달로 트래픽 엔지니어링과 서비스 분리를 지원한다.([RFC 3031](https://www.rfc-editor.org/rfc/rfc3031))
- Pseudowire/L2VPN은 패킷망 위에서 L2 회선을 에뮬레이션해 이기종 사이트 간 L2 연동을 제공한다.([RFC 3985](https://www.rfc-editor.org/rfc/rfc3985))([RFC 4664](https://www.rfc-editor.org/rfc/rfc4664))
- EVPN/VXLAN 계열은 데이터센터 및 멀티테넌트 환경에서 대규모 L2/L3 오버레이 운영에 활용된다.([RFC 7432](https://www.rfc-editor.org/rfc/rfc7432))([RFC 7348](https://www.rfc-editor.org/rfc/rfc7348))

### 심화 포인트
- 가상화는 유연성을 높이지만, 언더레이-오버레이 상태 불일치가 발생하면 장애 분석 난이도가 급격히 상승한다.
- 캡슐화 계층이 늘어날수록 MTU 설계와 PMTU 검증이 중요해진다.([RFC 8201](https://www.rfc-editor.org/rfc/rfc8201))

### 오해하기 쉬운 포인트
- "가상화하면 물리 네트워크 제약이 사라진다"는 오해가 있다. 실제 성능 한계와 장애는 여전히 물리 계층에 종속된다.
- "MPLS와 VXLAN은 완전 대체 관계"라는 단정은 부정확하다. 운영 목적과 배치 맥락이 다르다.

### 체크 질문
1. 오버레이와 언더레이를 분리 설계해야 하는 이유는 무엇인가?
2. 캡슐화 오버헤드가 MTU/분절 문제로 이어지는 경로를 설명할 수 있는가?
3. 서비스 분리 요구사항에 따라 MPLS와 EVPN/VXLAN을 어떻게 선택할 수 있는가?

### 한 줄 요약
- 링크 가상화는 물리 인프라 위에 논리 연결성을 유연하게 제공하지만, MTU·운영 가시성·장애 분석 체계를 함께 설계해야 한다.([RFC 3031](https://www.rfc-editor.org/rfc/rfc3031))([RFC 7432](https://www.rfc-editor.org/rfc/rfc7432))

## 6.6 데이터 센터 네트워킹
### 학습목표
- 데이터센터 네트워크의 트래픽 특성(동서 트래픽, 다중 경로)을 설명할 수 있다.([RFC 7938](https://www.rfc-editor.org/rfc/rfc7938))
- Leaf-Spine/ECMP 기반 구조가 확장성에 유리한 이유를 설명할 수 있다.([RFC 7938](https://www.rfc-editor.org/rfc/rfc7938))([RFC 2992](https://www.rfc-editor.org/rfc/rfc2992))
- 데이터센터에서 오버레이(VXLAN/EVPN) 사용 이유를 설명할 수 있다.([RFC 7348](https://www.rfc-editor.org/rfc/rfc7348))([RFC 7432](https://www.rfc-editor.org/rfc/rfc7432))

### 핵심개념
- 데이터센터는 서버 간 동서(East-West) 트래픽 비중이 높아, 다중 경로와 빠른 수렴 설계가 중요하다.([RFC 7938](https://www.rfc-editor.org/rfc/rfc7938))
- ECMP는 동일 비용 경로에 트래픽을 분산해 링크 활용도를 높이고 병목을 완화한다.([RFC 2992](https://www.rfc-editor.org/rfc/rfc2992))
- BGP 기반 패브릭 설계는 대규모 데이터센터에서 운영 단순성과 확장성을 제공할 수 있다.([RFC 7938](https://www.rfc-editor.org/rfc/rfc7938))
- 오버레이 기술은 테넌트 분리와 주소 독립성을 높여 클라우드형 운영에 적합하다.([RFC 7348](https://www.rfc-editor.org/rfc/rfc7348))

### 심화 포인트
- 데이터센터 네트워크 품질은 평균 처리율보다 tail latency(상위 지연 구간) 관리가 서비스 체감에 더 중요할 수 있다.
- ECMP 해시 편향이나 마이크로버스트는 대역폭이 충분해도 국부적 큐 포화를 유발할 수 있어 관측 체계가 필요하다.

### 오해하기 쉬운 포인트
- "데이터센터는 내부망이므로 라우팅 정책이 단순하다"는 오해가 있다. 실제로는 빠른 자동화와 장애 격리가 핵심이다.
- "고속 링크를 추가하면 병목이 자동 해결된다"는 단정은 부정확하다. 토폴로지, 해시 분산, 큐 관리가 함께 최적화되어야 한다.

### 체크 질문
1. 데이터센터에서 ECMP가 필수에 가까운 이유를 설명할 수 있는가?
2. Leaf-Spine 구조가 전통 3계층 구조 대비 가지는 운영 장점은 무엇인가?
3. 오버레이 도입 시 관측·장애 분석에서 반드시 추가해야 할 지표는 무엇인가?

### 한 줄 요약
- 데이터센터 네트워킹은 다중 경로(ECMP), 자동화된 라우팅, 오버레이 분리를 결합해 확장성과 가용성을 확보한다.([RFC 7938](https://www.rfc-editor.org/rfc/rfc7938))([RFC 2992](https://www.rfc-editor.org/rfc/rfc2992))

## 6.7 총정리: 웹페이지 요청에 대한 처리
### 학습목표
- 웹페이지 요청의 전체 처리 흐름(DNS -> 전송 연결 -> HTTP 응답)을 링크 계층까지 포함해 설명할 수 있다.([RFC 1034](https://www.rfc-editor.org/rfc/rfc1034))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))
- 각 홉에서 프레임/패킷/세그먼트가 어떻게 변환되는지 설명할 수 있다.([RFC 894](https://www.rfc-editor.org/rfc/rfc894))([RFC 791](https://www.rfc-editor.org/rfc/rfc791))
- 링크 계층 문제가 애플리케이션 지연으로 연결되는 경로를 설명할 수 있다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 핵심개념
- 클라이언트는 먼저 DNS 질의를 통해 목적지 IP를 확인하고, 이후 전송 계층 연결(TCP 또는 QUIC 기반)을 수립해 HTTP 요청을 보낸다.([RFC 1034](https://www.rfc-editor.org/rfc/rfc1034))([RFC 1035](https://www.rfc-editor.org/rfc/rfc1035))([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))
- 로컬 링크에서는 ARP/ND로 다음 홉의 링크 계층 주소를 해석하고 프레임을 전송한다.([RFC 826](https://www.rfc-editor.org/rfc/rfc826))([RFC 4861](https://www.rfc-editor.org/rfc/rfc4861))
- 라우터를 지날 때마다 프레임은 재구성되지만 IP 패킷의 종단 의미는 유지된다.([RFC 894](https://www.rfc-editor.org/rfc/rfc894))([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))
- HTTPS의 경우 TLS가 전송 계층 위에서 기밀성/무결성을 제공한 뒤, HTTP 의미론이 적용된다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))

### 심화 포인트
- 실제 사용자 체감 지연은 단일 단계가 아니라 DNS 지연, 연결 수립, 서버 처리, 링크 큐잉의 합으로 결정된다.
- 링크 계층의 국소 손실이나 재전송이 누적되면 애플리케이션은 "서버가 느리다"로 보일 수 있어 계층별 분해 진단이 중요하다.

### 오해하기 쉬운 포인트
- "웹 요청은 HTTP만 알면 된다"는 오해가 있다. 실제 처리는 DNS, 링크 주소 해석, 라우팅, 전송 제어를 모두 포함한다.
- "응답 지연은 서버 문제"라는 단정은 부정확하다. 링크 계층 큐/손실도 체감 지연의 주요 원인이 된다.([Cisco: Troubleshooting Network Latency and Packet Drops](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html))

### 체크 질문
1. 브라우저에서 URL 입력 후 첫 바이트 수신까지의 계층별 단계를 순서대로 설명할 수 있는가?
2. ARP/ND 실패가 HTTP 타임아웃으로 이어지는 경로를 설명할 수 있는가?
3. 성능 저하 분석 시 DNS, 전송, 링크 계층을 어떤 순서로 분리 측정할 것인가?

### 한 줄 요약
- 웹페이지 요청 처리는 DNS, 전송 연결, 링크 계층 전달이 결합된 다계층 파이프라인이며, 어느 계층의 병목도 사용자 경험을 저하시킨다.([RFC 1034](https://www.rfc-editor.org/rfc/rfc1034))([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))

## 6.8 요약
### 학습목표
- 6.1~6.7의 내용을 링크 계층 관점으로 통합 설명할 수 있다.
- 오류 검출, 매체 공유, 스위칭, 가상화가 네트워크 전체 품질에 미치는 영향을 연결해 설명할 수 있다.

### 핵심개념
- 링크 계층은 프레임 전달·오류 검출·주소 해석·스위칭·가상화까지 포함하는 실전 실행 계층이다.([RFC 3819](https://www.rfc-editor.org/rfc/rfc3819))([RFC 826](https://www.rfc-editor.org/rfc/rfc826))
- 현대 네트워크 품질은 물리 링크 품질만이 아니라 L2 도메인 설계, 오버레이 구조, 데이터센터 경로 분산 정책에 의해 결정된다.([RFC 7938](https://www.rfc-editor.org/rfc/rfc7938))([RFC 7432](https://www.rfc-editor.org/rfc/rfc7432))
- 웹/애플리케이션 성능 문제는 링크 계층 병목에서 시작될 수 있으므로 계층별 진단 체계가 필수다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 심화 포인트
- Chapter 6의 핵심은 "링크 계층은 하위 계층이라 단순하다"는 인식을 버리고, 실전 장애의 주요 원인이 되는 제어/가상화 요소까지 함께 보는 것이다.

### 오해하기 쉬운 포인트
- 요약 섹션은 새 주장 추가가 아니라, 앞선 검증 사실을 링크 계층 운영 관점으로 재구성하는 단계다.

### 체크 질문
1. 링크 계층에서 먼저 확인할 운영 지표(오류율, 드롭, 큐 점유 등)는 무엇인가?
2. 오버레이-언더레이 불일치가 장애 분석을 어렵게 만드는 이유는 무엇인가?
3. Chapter 7(무선/이동 네트워크) 이전에 고정해야 할 링크 계층 핵심 개념은 무엇인가?

### 한 줄 요약
- Chapter 6은 링크 계층이 네트워크 성능과 안정성의 실질적 기반이며, 스위칭·가상화·데이터센터 설계까지 포함해 이해해야 함을 다룬다.

## Chapter 6 핵심 연결 요약
- 링크 계층은 프레임 단위 전달과 오류 검출을 담당하며, 상위 계층 성능의 바닥 품질을 만든다.([RFC 3819](https://www.rfc-editor.org/rfc/rfc3819))([RFC 1662](https://www.rfc-editor.org/rfc/rfc1662))
- 다중 접속 환경에서는 ARP/ND와 브로드캐스트 도메인 관리가 안정적 통신의 핵심이다.([RFC 826](https://www.rfc-editor.org/rfc/rfc826))([RFC 4861](https://www.rfc-editor.org/rfc/rfc4861))
- 스위치 LAN은 고속 전달을 제공하지만 루프/플러딩/큐잉 제어가 부족하면 대규모 장애로 확대될 수 있다.
- MPLS, L2VPN, EVPN/VXLAN 같은 링크 가상화는 확장성과 분리를 높이지만 MTU·가시성·운영 복잡도 관리가 필요하다.([RFC 3031](https://www.rfc-editor.org/rfc/rfc3031))([RFC 4664](https://www.rfc-editor.org/rfc/rfc4664))([RFC 7432](https://www.rfc-editor.org/rfc/rfc7432))
- 웹 요청 처리도 링크 계층 동작(주소 해석, 프레임 전달, 큐잉 품질)에 직접 영향을 받는다.([RFC 1034](https://www.rfc-editor.org/rfc/rfc1034))([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))

## 참고문헌
- IETF RFC 1122, *Requirements for Internet Hosts -- Communication Layers*. ([링크](https://www.rfc-editor.org/rfc/rfc1122))
- IETF RFC 3819, *Advice for Internet Subnetwork Designers*. ([링크](https://www.rfc-editor.org/rfc/rfc3819))
- IETF RFC 894, *A Standard for the Transmission of IP Datagrams over Ethernet Networks*. ([링크](https://www.rfc-editor.org/rfc/rfc894))
- IETF RFC 2464, *Transmission of IPv6 Packets over Ethernet Networks*. ([링크](https://www.rfc-editor.org/rfc/rfc2464))
- IETF RFC 1662, *PPP in HDLC-like Framing*. ([링크](https://www.rfc-editor.org/rfc/rfc1662))
- IETF RFC 1071, *Computing the Internet Checksum*. ([링크](https://www.rfc-editor.org/rfc/rfc1071))
- IETF RFC 6363, *Forward Error Correction (FEC) Framework*. ([링크](https://www.rfc-editor.org/rfc/rfc6363))
- IETF RFC 9293, *Transmission Control Protocol (TCP)*. ([링크](https://www.rfc-editor.org/rfc/rfc9293))
- IETF RFC 8085, *UDP Usage Guidelines*. ([링크](https://www.rfc-editor.org/rfc/rfc8085))
- IETF RFC 826, *An Ethernet Address Resolution Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc826))
- IETF RFC 4861, *Neighbor Discovery for IP version 6 (IPv6)*. ([링크](https://www.rfc-editor.org/rfc/rfc4861))
- IETF RFC 1112, *Host Extensions for IP Multicasting*. ([링크](https://www.rfc-editor.org/rfc/rfc1112))
- IETF RFC 4541, *Considerations for Internet Group Management Protocol (IGMP) and Multicast Listener Discovery (MLD) Snooping Switches*. ([링크](https://www.rfc-editor.org/rfc/rfc4541))
- IETF RFC 3031, *Multiprotocol Label Switching Architecture*. ([링크](https://www.rfc-editor.org/rfc/rfc3031))
- IETF RFC 3985, *Pseudo Wire Emulation Edge-to-Edge (PWE3) Architecture*. ([링크](https://www.rfc-editor.org/rfc/rfc3985))
- IETF RFC 4664, *Framework for Layer 2 Virtual Private Networks (L2VPNs)*. ([링크](https://www.rfc-editor.org/rfc/rfc4664))
- IETF RFC 7432, *BGP MPLS-Based Ethernet VPN*. ([링크](https://www.rfc-editor.org/rfc/rfc7432))
- IETF RFC 7348, *Virtual eXtensible Local Area Network (VXLAN)*. ([링크](https://www.rfc-editor.org/rfc/rfc7348))
- IETF RFC 8201, *Path MTU Discovery for IP version 6*. ([링크](https://www.rfc-editor.org/rfc/rfc8201))
- IETF RFC 7938, *Use of BGP for Routing in Large-Scale Data Centers*. ([링크](https://www.rfc-editor.org/rfc/rfc7938))
- IETF RFC 2992, *Analysis of an Equal-Cost Multi-Path Algorithm*. ([링크](https://www.rfc-editor.org/rfc/rfc2992))
- IETF RFC 1034, *Domain Names - Concepts and Facilities*. ([링크](https://www.rfc-editor.org/rfc/rfc1034))
- IETF RFC 1035, *Domain Names - Implementation and Specification*. ([링크](https://www.rfc-editor.org/rfc/rfc1035))
- IETF RFC 9000, *QUIC: A UDP-Based Multiplexed and Secure Transport*. ([링크](https://www.rfc-editor.org/rfc/rfc9000))
- IETF RFC 1812, *Requirements for IP Version 4 Routers*. ([링크](https://www.rfc-editor.org/rfc/rfc1812))
- IETF RFC 8446, *The Transport Layer Security (TLS) Protocol Version 1.3*. ([링크](https://www.rfc-editor.org/rfc/rfc8446))
- IETF RFC 9110, *HTTP Semantics*. ([링크](https://www.rfc-editor.org/rfc/rfc9110))
- IBM, *What is computer networking?* ([링크](https://www.ibm.com/think/topics/networking))
- IBM, *What is latency?* ([링크](https://www.ibm.com/think/topics/latency))
- Cisco, *What is routing?* ([링크](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- Cisco, *Troubleshooting Network Latency and Packet Drops*. ([링크](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html))
- Cisco, *What is a router?* ([링크](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))
