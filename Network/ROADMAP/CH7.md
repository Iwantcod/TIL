# Chapter 7 학습 문서

## 작성/검증 원칙
- 출처 우선순위: IETF 표준/BCP 및 RFC를 1차 기준으로 두고, IBM/Cisco 공식 문서로 개념 설명을 교차검증한다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))([RFC 6275](https://www.rfc-editor.org/rfc/rfc6275))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))([Cisco: What is low latency?](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))
- 추정 배제: 무선 링크 성능, 이동성 관리, 상위 계층 영향은 RFC와 공식 문서로 확인 가능한 사실만 기록한다.
- 용어 일관성: `무선 링크`, `핸드오버`, `로밍`, `모바일 노드`, `홈 에이전트`, `케어오브 주소(CoA)`, `네트워크 기반 이동성`을 구분해 사용한다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))([RFC 6275](https://www.rfc-editor.org/rfc/rfc6275))([RFC 5213](https://www.rfc-editor.org/rfc/rfc5213))
- 범위 명시: 802.11/4G/5G의 무선 PHY/MAC 세부 규격은 주로 IEEE/3GPP 영역이며, 본 문서는 IETF의 IP·이동성·전송 영향 관점 중심으로 정리한다.([RFC 5416](https://www.rfc-editor.org/rfc/rfc5416))([RFC 6459](https://www.rfc-editor.org/rfc/rfc6459))
- 업데이트 기준일: 2026-04-22 (KST).

## 7.1 개요
### 학습목표
- 무선/이동 네트워크가 유선 고정 네트워크와 다른 운영 문제를 가지는 이유를 설명할 수 있다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))
- 무선 링크 품질 변화와 단말 이동성이 제어/데이터 평면에 미치는 영향을 설명할 수 있다.([RFC 3819](https://www.rfc-editor.org/rfc/rfc3819))([RFC 6275](https://www.rfc-editor.org/rfc/rfc6275))
- Chapter 7의 핵심 질문(무선 품질, 핸드오버, 상위 계층 적응)을 정리할 수 있다.

### 핵심개념
- 무선 네트워크는 링크 품질 변동(간섭, 신호 세기 변화)과 단말 이동으로 인해 경로/성능 상태가 빠르게 달라질 수 있다.
- 이동성 문제는 "연결 유지"와 "IP 연속성 유지"를 동시에 다루어야 하므로 단순 라우팅 문제보다 복합적이다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))
- 모바일 환경에서는 지연, 손실, 재정렬이 변동적으로 나타나 전송 계층 제어에 직접 영향을 준다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))([RFC 9002](https://www.rfc-editor.org/rfc/rfc9002))

### 심화 포인트
- Chapter 7은 링크 계층 기술 암기보다 "상태가 변하는 네트워크에서 종단 성능을 안정화하는 방법"에 초점을 둔다.
- 유선에서 유효한 고정 파라미터(고정 RTT 가정 등)는 무선·이동 환경에서 쉽게 깨질 수 있다.

### 오해하기 쉬운 포인트
- "무선 네트워크 문제는 속도 부족 하나로 설명된다"는 오해가 있다. 실제로는 변동성과 이동성이 핵심 변수다.
- "핸드오버는 링크 계층 내부 이슈"라는 단정은 부정확하다. 전송 연결, 세션 유지, 정책 제어까지 연쇄 영향을 준다.

### 체크 질문
1. 무선/이동 네트워크에서 유선 대비 추가로 고려해야 할 핵심 상태 변수는 무엇인가?
2. 단말 이동이 링크 계층 변화와 IP 계층 변화로 나뉘는 이유는 무엇인가?
3. Chapter 7에서 성능 진단을 위해 어떤 계층 지표를 함께 봐야 하는가?

### 한 줄 요약
- 무선·이동 네트워크의 핵심 난제는 낮은 속도 자체보다 빠르게 변하는 링크 상태와 이동성에 대한 종단 적응이다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))

## 7.2 무선 링크와 네트워크의 특징
### 학습목표
- 무선 링크에서 지연/손실/처리율 변동이 커지는 원리를 설명할 수 있다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))([Cisco: What is low latency?](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))
- 무선 구간의 손실과 혼잡 손실을 구분해야 하는 이유를 설명할 수 있다.([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))([RFC 9002](https://www.rfc-editor.org/rfc/rfc9002))
- 링크 변동성이 상위 계층 재전송/혼잡 제어에 미치는 영향을 설명할 수 있다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))

### 핵심개념
- 무선 링크는 동일 위치·시간에서도 품질이 변동할 수 있어 RTT와 손실률이 안정적으로 유지되기 어렵다.
- 전송 계층은 손실을 혼잡 신호로 해석하는 경향이 있어 무선 손실 환경에서 과도한 전송률 감소가 발생할 수 있다.([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))
- 이동 중 링크 전환 시 일시적인 패킷 재정렬/지연 급증이 발생할 수 있고, 이는 타임아웃·재전송을 유발한다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))

### 심화 포인트
- 무선 환경 최적화는 단순 평균 처리율보다 지연 분포(p95/p99), 지터, 손실 버스트 길이를 함께 봐야 효과적이다.
- 무선 링크 품질 변화에 대응하려면 전송 계층 파라미터와 큐 관리(AQM/ECN)까지 통합 조정이 필요할 수 있다.([RFC 7567](https://www.rfc-editor.org/rfc/rfc7567))([RFC 3168](https://www.rfc-editor.org/rfc/rfc3168))

### 오해하기 쉬운 포인트
- "손실이 있으면 항상 혼잡"이라는 가정은 무선 환경에서 오판을 만들 수 있다.
- "대역폭이 높으면 체감 지연 문제가 사라진다"는 단정은 부정확하다. 지연 변동과 재전송은 별도 관리 대상이다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 체크 질문
1. 무선 링크 품질 저하와 코어 혼잡을 어떤 지표로 구분할 수 있는가?
2. RTT 변동성이 큰 환경에서 RTO 설정이 왜 어려운가?
3. 지연 민감 애플리케이션에서 무선 구간의 핵심 운영 지표는 무엇인가?

### 한 줄 요약
- 무선 링크는 "가변성"이 본질이며, 상위 계층은 손실·지연 변동을 혼잡과 구분해 대응해야 성능을 유지할 수 있다.([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))

## 7.3 와이파이: 802.11 무선 랜
### 학습목표
- Wi-Fi 환경에서 IP 전달 관점의 핵심 구성(AP, 단말, 로컬 링크)을 설명할 수 있다.
- 802.11 기반 무선랜에서 IETF가 다루는 운영 요소(CAPWAP, IP over IEEE 802)를 설명할 수 있다.([RFC 5415](https://www.rfc-editor.org/rfc/rfc5415))([RFC 5416](https://www.rfc-editor.org/rfc/rfc5416))([RFC 1042](https://www.rfc-editor.org/rfc/rfc1042))
- Wi-Fi 로밍이 상위 계층 세션 품질에 미치는 영향을 설명할 수 있다.

### 핵심개념
- Wi-Fi는 단말이 AP를 통해 IP 네트워크에 접속하는 대표 무선 LAN 모델이며, 링크 상태 변화에 따라 로밍이 발생한다.
- IETF CAPWAP은 무선 단말이 아니라 AP와 컨트롤러 간 제어/데이터 터널링 구조를 정의해 대규모 WLAN 운영을 지원한다.([RFC 5415](https://www.rfc-editor.org/rfc/rfc5415))([RFC 5416](https://www.rfc-editor.org/rfc/rfc5416))
- IP over IEEE 802 캡슐화 모델은 무선 LAN 포함 802 계열에서 IP 데이터그램 전달의 기본 틀을 제공한다.([RFC 1042](https://www.rfc-editor.org/rfc/rfc1042))
- Wi-Fi 로밍 중 지연·패킷 손실이 발생하면 TCP/QUIC 성능이 일시 저하될 수 있다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))([RFC 9002](https://www.rfc-editor.org/rfc/rfc9002))

### 심화 포인트
- 엔터프라이즈 WLAN 품질은 AP 무선 품질뿐 아니라 컨트롤러 설계, 로밍 정책, 백홀 네트워크 품질에 함께 좌우된다.
- 로밍 최적화는 인증/연결 재설정 시간을 줄이는 것과 전송 계층 재수렴 비용을 줄이는 것을 함께 고려해야 한다.

### 오해하기 쉬운 포인트
- "Wi-Fi 문제는 무조건 RF 세기 문제"라는 오해가 있다. IP 주소 해석, DHCP, 컨트롤러 정책도 병목이 될 수 있다.
- "로밍은 사용자 체감에 거의 영향이 없다"는 단정은 부정확하다. 실시간 앱에서는 짧은 단절도 품질 저하로 이어진다.

### 체크 질문
1. Wi-Fi 로밍 시 링크 계층 이벤트가 전송 계층 타임아웃으로 이어지는 경로를 설명할 수 있는가?
2. CAPWAP 도입 환경에서 장애 지점(AP/컨트롤러/백홀)을 어떻게 분리 진단할 것인가?
3. WLAN 품질 평가 시 평균 RSSI 외에 어떤 지표를 함께 봐야 하는가?

### 한 줄 요약
- Wi-Fi 학습의 핵심은 무선 PHY 상세 암기보다, 로밍·제어 구조·IP 전달 품질이 함께 서비스 체감을 결정한다는 점이다.([RFC 5415](https://www.rfc-editor.org/rfc/rfc5415))

## 7.4 셀룰러 네트워크: 4G, 5G
### 학습목표
- 4G/5G 셀룰러에서 IP 연결성과 이동성 유지가 왜 핵심인지 설명할 수 있다.
- 3GPP 셀룰러 환경에서 IETF가 다루는 IPv6 운용 관점을 설명할 수 있다.([RFC 6459](https://www.rfc-editor.org/rfc/rfc6459))([RFC 7066](https://www.rfc-editor.org/rfc/rfc7066))
- 셀룰러 환경 특성이 지연·처리율·세션 연속성에 미치는 영향을 설명할 수 있다.([Cisco: What is low latency?](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))

### 핵심개념
- 4G/5G는 광역 이동성을 전제로 설계되어 단말 위치 변화 중에도 IP 서비스 연속성을 유지하는 것이 중요하다.
- IETF RFC 6459/7066은 3GPP 셀룰러 맥락에서 IPv6 운용 고려사항과 단말 측 요구사항을 정리한다.([RFC 6459](https://www.rfc-editor.org/rfc/rfc6459))([RFC 7066](https://www.rfc-editor.org/rfc/rfc7066))
- 셀 변경·무선 상태 변화 시 지연 변동과 일시적 손실이 발생할 수 있어 상위 계층의 복원력 설계가 필요하다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))
- 셀룰러 환경에서도 종단 간 IP·전송 제어 원리는 유지되며, 링크 특성 변화에 대한 적응이 성능 차이를 만든다.

### 심화 포인트
- 셀룰러는 평균 대역폭보다 커버리지 경계·혼잡 셀·이동 속도에 따른 품질 편차 관리가 실무 핵심이다.
- 이동성과 저지연 요구가 동시에 큰 서비스는 전송 프로토콜 선택(TCP/QUIC/MPTCP)과 세션 재개 전략이 중요하다.([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))([RFC 8684](https://www.rfc-editor.org/rfc/rfc8684))

### 오해하기 쉬운 포인트
- "5G면 지연 문제가 자동 해결된다"는 오해가 있다. 실제 지연은 무선 구간 외 전송 경로·서버 위치에 함께 좌우된다.
- "셀룰러 이동성은 액세스망 내부에서만 처리된다"는 단정은 부정확하다. 종단 전송/세션 계층 영향까지 고려해야 한다.

### 체크 질문
1. 셀룰러 환경에서 IPv6 운용 지침이 중요한 이유를 설명할 수 있는가?
2. 4G/5G 환경에서 성능 편차를 만드는 핵심 요인은 무엇인가?
3. 이동 중 서비스 연속성을 높이기 위한 전송 계층 전략은 무엇인가?

### 한 줄 요약
- 4G/5G 학습의 핵심은 세대 명칭보다, 이동 중 IP 연속성과 전송 복원력을 어떻게 확보하느냐에 있다.([RFC 6459](https://www.rfc-editor.org/rfc/rfc6459))([RFC 7066](https://www.rfc-editor.org/rfc/rfc7066))

## 7.5 이동성 관리: 원칙
### 학습목표
- 이동성 관리의 기본 요소(식별자/위치자 분리, 위치 등록, 터널링)를 설명할 수 있다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))
- Mobile IPv4/IPv6의 핵심 개념을 설명할 수 있다.([RFC 5944](https://www.rfc-editor.org/rfc/rfc5944))([RFC 6275](https://www.rfc-editor.org/rfc/rfc6275))
- 호스트 기반과 네트워크 기반 이동성 관리의 차이를 설명할 수 있다.([RFC 6275](https://www.rfc-editor.org/rfc/rfc6275))([RFC 5213](https://www.rfc-editor.org/rfc/rfc5213))

### 핵심개념
- 이동성 관리는 단말의 위치가 바뀌어도 상위 세션이 끊기지 않도록 식별자와 위치 정보의 매핑을 유지하는 문제다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))
- Mobile IP 계열은 홈 주소, 케어오브 주소(CoA), 바인딩 갱신을 통해 이동 중 도달성을 유지한다.([RFC 5944](https://www.rfc-editor.org/rfc/rfc5944))([RFC 6275](https://www.rfc-editor.org/rfc/rfc6275))
- 네트워크 기반 이동성(PMIPv6)은 단말 개입을 줄이고 네트워크가 이동성 신호를 처리한다.([RFC 5213](https://www.rfc-editor.org/rfc/rfc5213))
- 분산 이동성 관리 요구사항은 중앙 앵커 병목 완화와 확장성 확보를 중요 과제로 본다.([RFC 7333](https://www.rfc-editor.org/rfc/rfc7333))

### 심화 포인트
- 이동성 관리 설계는 "세션 연속성"과 "경로 효율성"의 균형 문제다. 완전 연속성은 터널/앵커 비용을 증가시킬 수 있다.
- 중앙 앵커 구조는 운영 단순성이 장점이지만, 지연 증가와 단일 병목 가능성이 단점이 될 수 있다.

### 오해하기 쉬운 포인트
- "IP 주소가 바뀌어도 세션은 자동 유지된다"는 오해가 있다. 전송 세션 연속성은 별도 메커니즘이 필요하다.
- "모든 이동성은 단말 소프트웨어로 해결할 수 있다"는 단정은 부정확하다. 네트워크 기반 방식이 더 적합한 환경도 많다.

### 체크 질문
1. 홈 주소와 CoA를 분리하는 이유를 실무 관점에서 설명할 수 있는가?
2. 호스트 기반 이동성과 네트워크 기반 이동성의 운영 trade-off는 무엇인가?
3. 분산 이동성 관리가 필요한 환경 조건은 무엇인가?

### 한 줄 요약
- 이동성 관리 원칙의 본질은 위치 변화와 세션 연속성 사이의 균형을 식별자/위치자 분리로 해결하는 것이다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))([RFC 5213](https://www.rfc-editor.org/rfc/rfc5213))

## 7.6 실전에서의 이동성 관리
### 학습목표
- 실제 네트워크에서 사용되는 이동성 관리 패턴(로컬 로밍, 코어 앵커링, 터널 기반 전달)을 설명할 수 있다.([RFC 5213](https://www.rfc-editor.org/rfc/rfc5213))([RFC 5415](https://www.rfc-editor.org/rfc/rfc5415))
- 이동성 이벤트 시 장애/성능 저하를 줄이기 위한 운영 포인트를 설명할 수 있다.
- 다중 경로/다중 액세스 환경에서 세션 연속성을 높이는 접근을 설명할 수 있다.([RFC 8684](https://www.rfc-editor.org/rfc/rfc8684))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))

### 핵심개념
- 실무에서는 단일 표준 하나보다 WLAN 로밍, 셀룰러 코어 이동성, 오버레이 터널링이 조합되어 운영된다.
- PMIPv6 같은 네트워크 기반 방식은 단말 변경 최소화에 유리하고, host-based 방식은 종단 제어 유연성이 높다.([RFC 5213](https://www.rfc-editor.org/rfc/rfc5213))([RFC 6275](https://www.rfc-editor.org/rfc/rfc6275))
- MPTCP는 경로 전환 시 서브플로우 기반으로 세션 지속성을 높일 수 있다.([RFC 8684](https://www.rfc-editor.org/rfc/rfc8684))
- QUIC은 Connection ID 기반 연결 마이그레이션으로 IP/포트 변화 환경에서 세션 유지를 지원한다.([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))

### 심화 포인트
- 이동성 운영 품질은 핸드오버 시간 자체뿐 아니라, DNS 재해석 지연, 세션 재인증, 경로 재수렴 시간까지 포함해 측정해야 한다.
- 실시간 서비스는 "패킷 손실 0%"보다 짧은 단절과 빠른 복구가 더 중요한 품질 지표가 될 수 있다.

### 오해하기 쉬운 포인트
- "핸드오버가 빠르면 애플리케이션 품질도 자동 보장된다"는 오해가 있다. 전송 계층 복구/재적응 비용이 별도로 존재한다.
- "모바일 환경에서는 TCP를 쓰면 안 된다"는 단정은 부정확하다. 앱 요구사항에 따라 TCP, QUIC, MPTCP를 선택·보완할 수 있다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))

### 체크 질문
1. 실무 이동성 운영에서 "로밍 성공"을 어떤 지표로 정의할 수 있는가?
2. MPTCP와 QUIC 연결 마이그레이션은 어떤 상황에서 각각 유리한가?
3. 네트워크 기반 이동성 도입 시 점검해야 할 병목 지점은 무엇인가?

### 한 줄 요약
- 실전 이동성 관리는 핸드오버 기술 단일 선택이 아니라, 로밍·터널·전송 계층 복원력의 조합 설계 문제다.([RFC 5213](https://www.rfc-editor.org/rfc/rfc5213))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))

## 7.7 무선과 이동성: 상위 계층 프로토콜에의 영향
### 학습목표
- 무선/이동 환경이 TCP 혼잡 제어·재전송 타이머에 미치는 영향을 설명할 수 있다.([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))
- QUIC/MPTCP가 이동성 환경에서 제공하는 상위 계층 복원력 요소를 설명할 수 있다.([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))([RFC 9002](https://www.rfc-editor.org/rfc/rfc9002))([RFC 8684](https://www.rfc-editor.org/rfc/rfc8684))
- 큐 관리(AQM/ECN)가 무선 지연 품질 개선에 기여하는 원리를 설명할 수 있다.([RFC 7567](https://www.rfc-editor.org/rfc/rfc7567))([RFC 3168](https://www.rfc-editor.org/rfc/rfc3168))

### 핵심개념
- 무선 손실/핸드오버 손실은 TCP에서 혼잡으로 오인될 수 있어 cwnd 과감소와 처리율 저하가 발생할 수 있다.([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))
- 이동성으로 RTT가 급변하면 RTO 기반 복구 동작이 빈번해져 지연이 추가로 증가할 수 있다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))
- QUIC은 연결 ID 기반 마이그레이션과 개선된 손실 복구로 경로 변화에 대한 내성을 높인다.([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))([RFC 9002](https://www.rfc-editor.org/rfc/rfc9002))
- AQM/ECN은 큐 과포화를 완화해 지연 스파이크를 줄이는 데 도움을 줄 수 있다.([RFC 7567](https://www.rfc-editor.org/rfc/rfc7567))([RFC 3168](https://www.rfc-editor.org/rfc/rfc3168))

### 심화 포인트
- 무선·이동 환경 최적화는 프로토콜 선택뿐 아니라 앱 재시도 정책, 타임아웃, 버퍼 설계까지 포함한 종단 설계가 필요하다.
- 실시간·대화형 서비스는 최대 처리율보다 지연 안정성(지터 제어)이 더 중요한 KPI가 될 수 있다.

### 오해하기 쉬운 포인트
- "QUIC이면 이동성 문제가 모두 해결된다"는 오해가 있다. 링크 단절·정책 차단·앵커 병목은 여전히 성능 한계가 된다.
- "TCP는 모바일에 부적합"이라는 단정은 틀리다. TCP도 적절한 튜닝/운영으로 충분히 사용 가능하다.

### 체크 질문
1. 무선 손실과 혼잡 손실을 구분하지 못하면 TCP에 어떤 부작용이 생기는가?
2. QUIC 연결 마이그레이션과 MPTCP 멀티패스의 설계 철학 차이는 무엇인가?
3. 무선 지연 스파이크 완화를 위해 AQM/ECN을 어떻게 활용할 수 있는가?

### 한 줄 요약
- 무선·이동성의 상위 계층 영향은 "프로토콜 선택"보다 "손실 해석·타이머·복구 정책"의 정합성에서 결정된다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))([RFC 9002](https://www.rfc-editor.org/rfc/rfc9002))

## 7.8 요약
### 학습목표
- 7.1~7.7의 내용을 무선/이동 환경의 종단 성능 관점으로 통합 설명할 수 있다.
- 링크 변동성, 이동성 관리, 전송 적응의 관계를 구조적으로 설명할 수 있다.

### 핵심개념
- 무선 네트워크는 링크 품질 변동과 단말 이동으로 인해 고정 네트워크와 다른 제어 전략을 요구한다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))
- 이동성 관리는 Mobile IP/PMIPv6 같은 메커니즘과 실무 로밍 운영을 결합해 세션 연속성을 확보한다.([RFC 6275](https://www.rfc-editor.org/rfc/rfc6275))([RFC 5213](https://www.rfc-editor.org/rfc/rfc5213))
- 상위 계층 성능은 TCP/QUIC/MPTCP 복원력, 타이머/혼잡 제어, 큐 관리 정책의 결합으로 결정된다.([RFC 5681](https://www.rfc-editor.org/rfc/rfc5681))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))([RFC 8684](https://www.rfc-editor.org/rfc/rfc8684))

### 심화 포인트
- Chapter 7의 핵심은 무선 기술 세대 비교가 아니라, 변동성 높은 환경에서 종단 서비스 품질을 안정화하는 설계 원리다.

### 오해하기 쉬운 포인트
- 요약 섹션은 새 주장 추가가 아니라, 앞선 검증 사실을 이동성·성능 관점으로 재구성하는 단계다.

### 체크 질문
1. 무선/이동 환경에서 "연결 유지"와 "품질 유지"가 왜 다른 문제인가?
2. 핸드오버 최적화와 전송 계층 최적화를 함께 설계해야 하는 이유는 무엇인가?
3. Chapter 8(보안) 이전에 고정해야 할 무선/이동 핵심 개념은 무엇인가?

### 한 줄 요약
- Chapter 7은 무선 링크 변동성과 이동성 이벤트가 종단 전송 품질에 미치는 영향을 제어하는 원리를 다룬다.

## Chapter 7 핵심 연결 요약
- 무선·이동 네트워크의 본질적 난제는 높은 변동성과 세션 연속성 유지이며, 고정망과 동일한 가정이 자주 깨진다.([RFC 3753](https://www.rfc-editor.org/rfc/rfc3753))
- Wi-Fi/셀룰러 자체 PHY·무선 규격은 별도 영역이지만, IP 운용 관점에서는 로밍·주소 연속성·터널링이 핵심이다.([RFC 5415](https://www.rfc-editor.org/rfc/rfc5415))([RFC 6459](https://www.rfc-editor.org/rfc/rfc6459))
- Mobile IPv6/PMIPv6는 각각 호스트 기반·네트워크 기반 이동성 관리의 대표 모델을 제공한다.([RFC 6275](https://www.rfc-editor.org/rfc/rfc6275))([RFC 5213](https://www.rfc-editor.org/rfc/rfc5213))
- 상위 계층에서는 TCP 타이머/혼잡 제어, QUIC 연결 마이그레이션, MPTCP 멀티패스가 이동성 내성의 핵심 수단이다.([RFC 6298](https://www.rfc-editor.org/rfc/rfc6298))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))([RFC 8684](https://www.rfc-editor.org/rfc/rfc8684))
- 성능 개선은 단일 계층 튜닝이 아니라 링크 품질, 이동성 정책, 전송 제어를 함께 조정하는 통합 접근이 필요하다.([RFC 7567](https://www.rfc-editor.org/rfc/rfc7567))

## 참고문헌
- IETF RFC 1122, *Requirements for Internet Hosts -- Communication Layers*. ([링크](https://www.rfc-editor.org/rfc/rfc1122))
- IETF RFC 3753, *Mobility Related Terminology*. ([링크](https://www.rfc-editor.org/rfc/rfc3753))
- IETF RFC 3819, *Advice for Internet Subnetwork Designers*. ([링크](https://www.rfc-editor.org/rfc/rfc3819))
- IETF RFC 5944, *IP Mobility Support for IPv4, Revised*. ([링크](https://www.rfc-editor.org/rfc/rfc5944))
- IETF RFC 6275, *Mobility Support in IPv6*. ([링크](https://www.rfc-editor.org/rfc/rfc6275))
- IETF RFC 5213, *Proxy Mobile IPv6*. ([링크](https://www.rfc-editor.org/rfc/rfc5213))
- IETF RFC 7333, *Requirements for Distributed Mobility Management*. ([링크](https://www.rfc-editor.org/rfc/rfc7333))
- IETF RFC 5555, *Mobile IPv6 Support for Dual Stack Hosts and Routers*. ([링크](https://www.rfc-editor.org/rfc/rfc5555))
- IETF RFC 5415, *CAPWAP Protocol Specification*. ([링크](https://www.rfc-editor.org/rfc/rfc5415))
- IETF RFC 5416, *CAPWAP Protocol Binding for IEEE 802.11*. ([링크](https://www.rfc-editor.org/rfc/rfc5416))
- IETF RFC 1042, *A Standard for the Transmission of IP Datagrams over IEEE 802 Networks*. ([링크](https://www.rfc-editor.org/rfc/rfc1042))
- IETF RFC 6459, *IPv6 in the 3GPP Evolved Packet System (EPS)*. ([링크](https://www.rfc-editor.org/rfc/rfc6459))
- IETF RFC 7066, *IPv6 for 3GPP Cellular Hosts*. ([링크](https://www.rfc-editor.org/rfc/rfc7066))
- IETF RFC 5681, *TCP Congestion Control*. ([링크](https://www.rfc-editor.org/rfc/rfc5681))
- IETF RFC 6298, *Computing TCP's Retransmission Timer*. ([링크](https://www.rfc-editor.org/rfc/rfc6298))
- IETF RFC 3168, *The Addition of Explicit Congestion Notification (ECN) to IP*. ([링크](https://www.rfc-editor.org/rfc/rfc3168))
- IETF RFC 7567, *IETF Recommendations Regarding Active Queue Management*. ([링크](https://www.rfc-editor.org/rfc/rfc7567))
- IETF RFC 8684, *TCP Extensions for Multipath Operation with Multiple Addresses*. ([링크](https://www.rfc-editor.org/rfc/rfc8684))
- IETF RFC 9000, *QUIC: A UDP-Based Multiplexed and Secure Transport*. ([링크](https://www.rfc-editor.org/rfc/rfc9000))
- IETF RFC 9002, *QUIC Loss Detection and Congestion Control*. ([링크](https://www.rfc-editor.org/rfc/rfc9002))
- IETF RFC 9293, *Transmission Control Protocol (TCP)*. ([링크](https://www.rfc-editor.org/rfc/rfc9293))
- IBM, *What is computer networking?* ([링크](https://www.ibm.com/think/topics/networking))
- IBM, *What is latency?* ([링크](https://www.ibm.com/think/topics/latency))
- Cisco, *What is low latency?* ([링크](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))
- Cisco, *What is routing?* ([링크](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
