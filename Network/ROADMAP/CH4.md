# Chapter 4 학습 문서

## 작성/검증 원칙
- 출처 우선순위: IETF 표준/BCP 및 RFC를 1차 기준으로 두고, IBM/Cisco 공식 문서로 개념 설명을 교차검증한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- 추정 배제: 데이터 평면 동작(포워딩, 주소 처리, 단편화, 미들박스 기능)은 RFC와 공식 문서로 확인된 사실만 기록한다.
- 용어 일관성: `포워딩(Forwarding)`, `라우팅(Routing)`, `FIB`, `데이터그램`, `MTU`, `Hop Limit/TTL`, `미들박스`를 구분해 사용한다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))([RFC 3234](https://www.rfc-editor.org/rfc/rfc3234))
- 업데이트 기준일: 2026-04-22 (KST).

## 4.1 네트워크 계층 개요
### 학습목표
- 네트워크 계층의 핵심 역할(호스트 간 전달, 라우터 포워딩)을 설명할 수 있다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))
- 데이터 평면과 제어 평면의 책임 경계를 구분할 수 있다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- IPv4/IPv6 데이터그램 전달의 공통점과 차이를 큰 틀에서 설명할 수 있다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))

### 핵심개념
- 네트워크 계층은 전송 계층 세그먼트를 IP 데이터그램으로 전달하고, 라우터는 목적지 방향으로 패킷을 다음 홉에 전달한다.([RFC 1122](https://www.rfc-editor.org/rfc/rfc1122))([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))
- 데이터 평면은 각 패킷의 실제 처리(헤더 조회, 출력 인터페이스 선택, 큐잉)를 담당하고, 제어 평면은 경로 계산/정책 배포를 담당한다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- IPv4와 IPv6 모두 비연결형 데이터그램 전달 모델을 따르며, 종단 신뢰성은 상위 계층이 보완한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))

### 심화 포인트
- 네트워크 계층의 성능은 단순 링크 속도보다 포워딩 지연, 큐잉, 혼잡 상태에 크게 영향을 받는다.([IBM: What is latency?](https://www.ibm.com/think/topics/latency))
- 데이터 평면은 "빠른 패킷 처리"가 목표이고, 제어 평면은 "일관된 경로/정책"이 목표이므로 운영 실패 양상도 다르다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))

### 오해하기 쉬운 포인트
- "네트워크 계층이 종단 간 신뢰 전달을 보장한다"는 오해가 있다. IP는 기본적으로 최선형 전달(best effort) 모델이다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))
- "포워딩과 라우팅은 같은 작업"이라는 표현은 부정확하다. 라우팅은 경로 결정, 포워딩은 패킷 처리 실행이다.([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))

### 체크 질문
1. 데이터 평면과 제어 평면을 장애 관점에서 어떻게 구분해 진단할 수 있는가?
2. IPv4/IPv6가 공통적으로 제공하는 네트워크 계층 서비스 모델은 무엇인가?
3. 네트워크 계층과 전송 계층의 책임 경계를 한 문장으로 설명할 수 있는가?

### 한 줄 요약
- 네트워크 계층의 데이터 평면은 패킷을 빠르게 전달하고, 제어 평면은 그 전달 규칙을 만든다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))

## 4.2 라우터 내부에는 무엇이 있을까?
### 학습목표
- 라우터의 기본 처리 흐름(수신, 조회, 스위칭, 송신)을 설명할 수 있다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))([Cisco: What is a router?](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))
- 라우터가 TTL/Hop Limit, 체크섬, 단편화 관련 필드를 어떻게 다루는지 설명할 수 있다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))
- 큐잉과 버퍼링이 지연/손실에 미치는 영향을 설명할 수 있다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))([Cisco: Troubleshooting Network Latency and Packet Drops](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html))

### 핵심개념
- 라우터는 입력 인터페이스에서 패킷을 수신하고 헤더를 검사한 뒤 FIB 기반으로 출력 인터페이스를 결정한다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))
- IPv4 라우터는 TTL을 감소시키고 헤더 체크섬을 재계산한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))
- IPv6에서는 `Hop Limit`을 감소시키며, 중간 라우터는 패킷을 단편화하지 않는다.([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))
- 출력 큐 포화 시 지연 증가와 패킷 손실이 발생하며, 이는 상위 계층 성능 저하로 이어진다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))([IBM: What is latency?](https://www.ibm.com/think/topics/latency))

### 심화 포인트
- 데이터 평면 최적화는 ASIC/NPU 같은 하드웨어 가속과 테이블 구조 최적화(FIB/TCAM 설계)로 구현되는 경우가 많다.([Cisco: What is a router?](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))
- 운영 환경에서 라우터 병목은 링크 대역폭이 아니라 특정 큐/인터페이스 불균형에서 발생하기 쉽다.([Cisco: Troubleshooting Network Latency and Packet Drops](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html))

### 오해하기 쉬운 포인트
- "라우터는 단순히 패킷을 통과시킨다"는 오해가 있다. 실제로는 필드 갱신, 정책 적용, 큐잉 등 복합 처리를 수행한다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))
- "IPv6도 IPv4처럼 중간 라우터 단편화를 한다"는 오해가 있다. IPv6 단편화는 송신 노드가 수행한다.([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))

### 체크 질문
1. 라우터의 입력/출력 처리 중 지연을 가장 크게 유발할 수 있는 지점은 어디인가?
2. IPv4의 TTL과 IPv6의 Hop Limit 처리 방식은 어떤 공통 목적을 가지는가?
3. 단편화 정책 차이가 운영 측면(PMTU, 성능)에 어떤 영향을 주는가?

### 한 줄 요약
- 라우터 내부 데이터 평면은 헤더 처리와 FIB 조회, 큐잉 제어의 결합으로 성능과 안정성을 결정한다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))

## 4.3 인터넷 프로토콜(IP): IPv4, 주소체계, IPv6 emd
### 학습목표
- IPv4/IPv6 헤더와 주소체계의 핵심 차이를 설명할 수 있다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))([RFC 4291](https://www.rfc-editor.org/rfc/rfc4291))
- CIDR 기반 주소 표현과 프리픽스 집계를 설명할 수 있다.([RFC 4632](https://www.rfc-editor.org/rfc/rfc4632))
- IPv4 단편화와 IPv6 PMTU 기반 전달의 차이를 설명할 수 있다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8201](https://www.rfc-editor.org/rfc/rfc8201))

### 핵심개념
- IPv4는 32비트 주소, IPv6는 128비트 주소를 사용하며, IPv6는 확장 헤더 기반 구조를 채택한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))([RFC 4291](https://www.rfc-editor.org/rfc/rfc4291))
- CIDR은 클래스 기반 주소 체계의 비효율을 줄이고 라우팅 테이블 집계를 가능하게 한다.([RFC 4632](https://www.rfc-editor.org/rfc/rfc4632))
- IPv4에서는 경로 중간 단편화가 가능하지만, IPv6는 송신 호스트가 PMTU 정보를 활용해 전송 크기를 조정한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))([RFC 8201](https://www.rfc-editor.org/rfc/rfc8201))
- 사설 IPv4 주소(RFC 1918)는 주소 부족 완화와 내부망 설계에 널리 사용된다.([RFC 1918](https://www.rfc-editor.org/rfc/rfc1918))

### 심화 포인트
- IPv6 전환은 "주소 길이 확장"만이 아니라 헤더 처리 단순화, 자동 구성, 확장성 확보를 함께 목표로 한다.([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))([RFC 4291](https://www.rfc-editor.org/rfc/rfc4291))
- 주소 설계 품질은 보안 정책(세분화), 운영 복잡도(요약 가능성), 장애 영향 반경에 직접 영향을 준다.([RFC 4632](https://www.rfc-editor.org/rfc/rfc4632))([Cisco: What is IP address management?](https://www.cisco.com/c/en/us/products/security/what-is-ip-address-management-ipam.html))

### 오해하기 쉬운 포인트
- "IPv6는 IPv4보다 무조건 빠르다"는 일반화는 부정확하다. 성능은 경로, 장비 구현, 정책 구성에 좌우된다.
- "CIDR은 단순 표기법 변화"라는 오해가 있다. 실제로는 라우팅 집계와 주소 배분 효율의 핵심 원리다.([RFC 4632](https://www.rfc-editor.org/rfc/rfc4632))
- "NAT이 있으니 IPv6는 필요 없다"는 단정은 장기 확장성과 종단 간 모델 관점에서 한계가 있다.([RFC 2993](https://www.rfc-editor.org/rfc/rfc2993))

### 체크 질문
1. IPv4와 IPv6 헤더 설계 차이가 라우터 처리에 어떤 영향을 주는가?
2. CIDR 집계가 BGP/인터도메인 라우팅 규모 문제를 완화하는 이유는 무엇인가?
3. IPv6에서 PMTU 발견 실패 시 어떤 운영 문제가 발생하는가?

### 한 줄 요약
- IP 주소체계 학습의 핵심은 IPv4/IPv6 차이 암기가 아니라, CIDR·단편화·PMTU가 전달 품질에 미치는 영향을 이해하는 것이다.([RFC 4632](https://www.rfc-editor.org/rfc/rfc4632))([RFC 8201](https://www.rfc-editor.org/rfc/rfc8201))

## 4.4 일반화된 포워딩 및 소프트웨어 기반 네트워크(SDN)
### 학습목표
- 전통적 목적지 기반 포워딩과 일반화된 매치-액션 포워딩의 차이를 설명할 수 있다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- SDN의 핵심 아이디어(제어/데이터 평면 분리, 중앙 제어 논리)를 설명할 수 있다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))([IBM: What is software-defined networking (SDN)?](https://www.ibm.com/think/topics/software-defined-networking))
- SDN 도입 시 운영 장점과 리스크(복잡도, 장애 도메인 변화)를 설명할 수 있다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))([Cisco: What is software-defined networking?](https://www.cisco.com/site/us/en/learn/topics/software-defined-networking/what-is-sdn.html))

### 핵심개념
- 전통 라우터는 주로 목적지 프리픽스 기반 조회로 포워딩 결정을 수행하지만, 일반화된 포워딩은 다양한 헤더 필드/메타데이터를 조건으로 정책 적용이 가능하다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- SDN은 네트워크 제어 로직을 논리적으로 집중시켜 정책 자동화와 운영 민첩성을 높이는 접근이다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))([IBM: What is software-defined networking (SDN)?](https://www.ibm.com/think/topics/software-defined-networking))
- 데이터 평면 장비는 제어 평면이 배포한 규칙을 빠르게 적용하는 실행 계층으로 동작한다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))

### 심화 포인트
- SDN의 본질은 "중앙 장비 1대"가 아니라, 추상화된 제어 인터페이스를 통해 네트워크 동작을 프로그래밍 가능하게 만드는 것이다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- 운영 자동화 이점이 크지만, 제어 시스템 장애/정책 오류가 광범위 영향으로 확산될 수 있어 검증 체계가 필수다.([Cisco: What is software-defined networking?](https://www.cisco.com/site/us/en/learn/topics/software-defined-networking/what-is-sdn.html))

### 오해하기 쉬운 포인트
- "SDN이면 라우팅 프로토콜이 불필요하다"는 오해가 있다. 실제 운영은 기존 프로토콜과 병행/통합되는 경우가 많다.
- "일반화된 포워딩은 성능보다 유연성만 중시한다"는 오해가 있다. 하드웨어 오프로딩과 결합해 성능을 유지하도록 설계된다.([Cisco: What is software-defined networking?](https://www.cisco.com/site/us/en/learn/topics/software-defined-networking/what-is-sdn.html))

### 체크 질문
1. 목적지 기반 포워딩과 매치-액션 포워딩의 정책 표현력 차이는 무엇인가?
2. SDN에서 제어 평면 집중이 운영 효율과 장애 위험에 미치는 영향은 무엇인가?
3. 기존 네트워크에 SDN을 점진 도입할 때 필요한 최소 검증 항목은 무엇인가?

### 한 줄 요약
- 일반화된 포워딩과 SDN은 데이터 평면의 정책 표현력과 운영 자동화를 확대하지만, 검증과 장애 격리 설계가 전제되어야 한다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))

## 4.5 미들박스
### 학습목표
- 미들박스의 정의와 대표 유형(NAT, 방화벽, 프록시, 로드밸런서)을 설명할 수 있다.([RFC 3234](https://www.rfc-editor.org/rfc/rfc3234))([IBM: What is network security?](https://www.ibm.com/think/topics/network-security))
- NAT가 주소 변환과 종단 간 연결성에 미치는 영향을 설명할 수 있다.([RFC 3022](https://www.rfc-editor.org/rfc/rfc3022))([RFC 2993](https://www.rfc-editor.org/rfc/rfc2993))
- 미들박스 도입의 장점(보안/운영)과 한계(상호운용성/복잡도)를 균형 있게 설명할 수 있다.([RFC 3234](https://www.rfc-editor.org/rfc/rfc3234))([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))

### 핵심개념
- 미들박스는 순수 IP 라우팅 외에 트래픽 검사, 변환, 정책 집행 같은 추가 기능을 수행하는 중간 장치다.([RFC 3234](https://www.rfc-editor.org/rfc/rfc3234))
- NAT는 주소/포트 매핑을 통해 사설 주소 공간을 공인 주소와 연결하지만, 종단 간 투명성을 약화시킬 수 있다.([RFC 3022](https://www.rfc-editor.org/rfc/rfc3022))([RFC 2993](https://www.rfc-editor.org/rfc/rfc2993))
- 보안 미들박스는 위협 완화에 기여하지만, 암호화 확대 환경에서는 가시성/검사 전략 재설계가 필요하다.([RFC 7258](https://www.rfc-editor.org/rfc/rfc7258))([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))

### 심화 포인트
- 미들박스는 운영상 필수인 경우가 많지만, 프로토콜 혁신과 상호운용성 측면에서는 제약 요인이 될 수 있다.([RFC 3234](https://www.rfc-editor.org/rfc/rfc3234))
- NAT/방화벽 정책은 애플리케이션 연결성, 장애 분석 난이도, 추적 가능성에 직접 영향을 준다.([RFC 3022](https://www.rfc-editor.org/rfc/rfc3022))

### 오해하기 쉬운 포인트
- "미들박스는 보안에만 쓰인다"는 오해가 있다. 성능 최적화, 가용성, 트래픽 엔지니어링에도 사용된다.
- "NAT이 곧 보안"이라는 단정은 부정확하다. NAT은 주소 변환 기능이지 보안 정책의 대체물이 아니다.([RFC 2993](https://www.rfc-editor.org/rfc/rfc2993))

### 체크 질문
1. 미들박스가 네트워크 운영에 주는 이점과 기술 부채를 각각 한 가지씩 설명할 수 있는가?
2. NAT 도입 시 애플리케이션 레벨에서 추가 고려해야 할 항목은 무엇인가?
3. 암호화 트래픽 증가 환경에서 미들박스 정책을 어떻게 재정의해야 하는가?

### 한 줄 요약
- 미들박스는 현대 네트워크 운영의 핵심 도구지만, 종단 간 모델·상호운용성·운영 복잡도와의 균형 설계가 필요하다.([RFC 3234](https://www.rfc-editor.org/rfc/rfc3234))([RFC 3022](https://www.rfc-editor.org/rfc/rfc3022))

## 4.6 요약
### 학습목표
- 4.1~4.5의 내용을 데이터 평면 관점에서 통합 설명할 수 있다.
- 주소체계, 포워딩 구조, 미들박스 정책이 성능·확장성·운영 복잡도에 미치는 영향을 연결해 설명할 수 있다.

### 핵심개념
- 데이터 평면의 본질은 패킷 단위 실행(포워딩, 큐잉, 헤더 처리)이며, 제어 평면은 그 실행 규칙을 제공한다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))
- IPv4/IPv6 주소체계와 단편화/PMTU 처리 차이는 실제 전달 품질과 운영 방식에 큰 영향을 준다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8201](https://www.rfc-editor.org/rfc/rfc8201))
- 미들박스는 운영 필수 요소로 자리잡았지만, 종단 간 연결성과 상호운용성 문제를 함께 관리해야 한다.([RFC 3234](https://www.rfc-editor.org/rfc/rfc3234))([RFC 2993](https://www.rfc-editor.org/rfc/rfc2993))

### 심화 포인트
- Chapter 4의 핵심은 "라우터가 패킷을 어떻게 보낸다"를 넘어서, 정책/주소/장비 구조가 함께 데이터 평면 품질을 만든다는 점을 이해하는 것이다.

### 오해하기 쉬운 포인트
- 요약 섹션은 새 주장 추가가 아니라, 앞선 검증 사실을 운영·설계 관점으로 재구성하는 단계다.

### 체크 질문
1. 데이터 평면 최적화에서 장비 성능, 주소 설계, 정책 설계 중 무엇을 먼저 볼 것인가?
2. IPv6 전환과 미들박스 정책을 동시에 고려해야 하는 이유는 무엇인가?
3. Chapter 5(제어 평면)로 넘어가기 전에 반드시 고정해야 할 개념은 무엇인가?

### 한 줄 요약
- Chapter 4는 네트워크 계층 데이터 평면의 실행 원리와 주소/정책 구조가 실제 전달 품질을 어떻게 결정하는지 다룬다.

## Chapter 4 핵심 연결 요약
- 네트워크 계층은 패킷 전달의 실행 계층(데이터 평면)과 경로/정책 설계 계층(제어 평면)으로 구분해 이해해야 한다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- 라우터 내부 동작은 헤더 처리, FIB 조회, 큐잉으로 구성되며 지연/손실의 직접 원인이 된다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))
- IPv4/IPv6 차이는 주소 길이뿐 아니라 헤더 구조, 단편화 정책, PMTU 처리 방식까지 포함한다.([RFC 791](https://www.rfc-editor.org/rfc/rfc791))([RFC 8200](https://www.rfc-editor.org/rfc/rfc8200))([RFC 8201](https://www.rfc-editor.org/rfc/rfc8201))
- 일반화된 포워딩과 SDN은 정책 표현력과 자동화를 확장하지만, 운영 검증/장애 격리 설계가 필수다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- 미들박스는 필수 운영 구성요소이지만, 종단 간 모델 훼손과 상호운용성 리스크를 함께 관리해야 한다.([RFC 3234](https://www.rfc-editor.org/rfc/rfc3234))([RFC 3022](https://www.rfc-editor.org/rfc/rfc3022))

## 참고문헌
- IETF RFC 1122, *Requirements for Internet Hosts -- Communication Layers*. ([링크](https://www.rfc-editor.org/rfc/rfc1122))
- IETF RFC 1812, *Requirements for IP Version 4 Routers*. ([링크](https://www.rfc-editor.org/rfc/rfc1812))
- IETF RFC 791, *Internet Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc791))
- IETF RFC 8200, *Internet Protocol, Version 6 (IPv6) Specification*. ([링크](https://www.rfc-editor.org/rfc/rfc8200))
- IETF RFC 4291, *IP Version 6 Addressing Architecture*. ([링크](https://www.rfc-editor.org/rfc/rfc4291))
- IETF RFC 4632, *Classless Inter-domain Routing (CIDR): The Internet Address Assignment and Aggregation Plan*. ([링크](https://www.rfc-editor.org/rfc/rfc4632))
- IETF RFC 1918, *Address Allocation for Private Internets*. ([링크](https://www.rfc-editor.org/rfc/rfc1918))
- IETF RFC 8201, *Path MTU Discovery for IP version 6*. ([링크](https://www.rfc-editor.org/rfc/rfc8201))
- IETF RFC 7426, *Software-Defined Networking (SDN): Layers and Architecture Terminology*. ([링크](https://www.rfc-editor.org/rfc/rfc7426))
- IETF RFC 3234, *Middleboxes: Taxonomy and Issues*. ([링크](https://www.rfc-editor.org/rfc/rfc3234))
- IETF RFC 3022, *Traditional IP Network Address Translator (Traditional NAT)*. ([링크](https://www.rfc-editor.org/rfc/rfc3022))
- IETF RFC 2993, *Architectural Implications of NAT*. ([링크](https://www.rfc-editor.org/rfc/rfc2993))
- IETF RFC 7258, *Pervasive Monitoring Is an Attack*. ([링크](https://www.rfc-editor.org/rfc/rfc7258))
- IBM, *What is computer networking?* ([링크](https://www.ibm.com/think/topics/networking))
- IBM, *What is latency?* ([링크](https://www.ibm.com/think/topics/latency))
- IBM, *What is network security?* ([링크](https://www.ibm.com/think/topics/network-security))
- IBM, *What is software-defined networking (SDN)?* ([링크](https://www.ibm.com/think/topics/software-defined-networking))
- Cisco, *What is routing?* ([링크](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- Cisco, *What is a router?* ([링크](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/what-is-a-router.html))
- Cisco, *Troubleshooting Network Latency and Packet Drops*. ([링크](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html))
- Cisco, *What is network security?* ([링크](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))
- Cisco, *What is software-defined networking?* ([링크](https://www.cisco.com/site/us/en/learn/topics/software-defined-networking/what-is-sdn.html))
- Cisco, *What is IP address management?* ([링크](https://www.cisco.com/c/en/us/products/security/what-is-ip-address-management-ipam.html))
