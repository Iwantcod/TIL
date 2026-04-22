# Chapter 5 학습 문서

## 작성/검증 원칙
- 출처 우선순위: IETF 표준/BCP 및 RFC를 1차 기준으로 두고, IBM/Cisco 공식 문서로 개념 설명을 교차검증한다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- 추정 배제: 제어 평면 프로토콜(OSPF, BGP, ICMP, SNMP, NETCONF/YANG)의 동작과 역할은 RFC 근거가 있는 내용만 기록한다.
- 용어 일관성: `제어 평면(Control Plane)`, `데이터 평면(Data Plane)`, `RIB`, `FIB`, `AS`, `IGP`, `EGP`, `정책 기반 라우팅`을 구분해 사용한다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- 업데이트 기준일: 2026-04-22 (KST).

## 5.1 개요
### 학습목표
- 네트워크 계층 제어 평면의 역할(경로 계산, 경로 배포, 정책 적용)을 설명할 수 있다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))
- AS 내부 라우팅(IGP)과 AS 간 라우팅(EGP)의 차이를 설명할 수 있다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- 제어 평면 변화가 데이터 평면(FIB) 동작에 미치는 영향을 설명할 수 있다.([RFC 1812](https://www.rfc-editor.org/rfc/rfc1812))

### 핵심개념
- 제어 평면은 네트워크 토폴로지와 정책 정보를 바탕으로 경로를 계산하고, 데이터 평면이 참조할 전달 상태를 만든다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- 인터넷 라우팅은 도메인 내부 최적화(예: OSPF)와 도메인 간 정책 조정(예: BGP)으로 분할되어 운영된다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- 제어 평면 수렴 속도와 안정성은 실제 서비스 가용성, 지연 변동, 우회 경로 품질에 직접 영향을 준다.([Cisco: What is routing?](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))

### 심화 포인트
- 제어 평면은 단순 최단경로 계산이 아니라, 규모 확장성과 정책 일관성 유지가 핵심 과제다.([RFC 1958](https://www.rfc-editor.org/rfc/rfc1958))
- 장애 상황에서는 데이터 평면 자체보다 제어 평면의 재수렴 품질이 복구 체감 시간을 결정하는 경우가 많다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))

### 오해하기 쉬운 포인트
- "제어 평면은 라우터 초기 설정 시에만 중요하다"는 오해가 있다. 실제로는 지속적인 경로 갱신과 정책 변경이 상시 발생한다.
- "AS 내부와 AS 간 라우팅은 같은 최적화 원리로 동작한다"는 오해가 있다. 내부는 기술적 최적화, 외부는 정책/비즈니스 제약이 크게 작용한다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))

### 체크 질문
1. 제어 평면과 데이터 평면을 장애 분석 관점에서 어떻게 분리해 진단할 수 있는가?
2. IGP와 BGP를 동시에 운영해야 하는 구조적 이유는 무엇인가?
3. 제어 평면 수렴 지연이 사용자 체감 품질에 어떤 형태로 나타나는가?

### 한 줄 요약
- Chapter 5의 핵심은 "누가 패킷을 보낼지(데이터 평면)"보다 "어떤 경로를 선택할지(제어 평면)"를 설계하는 원리를 이해하는 것이다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))

## 5.2 라우팅 알고리즘
### 학습목표
- 링크 상태(link-state)와 거리 벡터(distance-vector) 알고리즘의 차이를 설명할 수 있다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([RFC 2453](https://www.rfc-editor.org/rfc/rfc2453))
- 수렴성, 루프 가능성, 업데이트 범위 관점에서 알고리즘 특성을 비교할 수 있다.([RFC 2453](https://www.rfc-editor.org/rfc/rfc2453))([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))
- 정책 기반 경로 선택이 순수 최단경로와 어떻게 다른지 설명할 수 있다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))

### 핵심개념
- 거리 벡터 계열은 이웃과 거리 정보를 교환하며 점진적으로 경로를 수렴시키고, 링크 상태 계열은 전체 토폴로지 정보를 바탕으로 각 노드가 경로를 계산한다.([RFC 2453](https://www.rfc-editor.org/rfc/rfc2453))([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))
- 거리 벡터는 단순성과 구현 용이성이 강점이지만, 특정 장애 조건에서 수렴 지연과 루프 위험 관리가 중요하다.([RFC 2453](https://www.rfc-editor.org/rfc/rfc2453))
- 링크 상태는 빠른 수렴과 경로 품질 예측 가능성이 장점이지만, 토폴로지 정보 동기화와 계산 비용 관리가 필요하다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))
- 인터넷 규모에서는 최단경로만으로 충분하지 않으며, 정책 기반 의사결정이 경로 선택에 핵심 역할을 한다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))

### 심화 포인트
- 알고리즘 선택은 "정확도" 문제가 아니라, 도메인 규모·변동성·운영 자동화 수준과의 적합성 문제다.
- 동일한 알고리즘이라도 타이머, 메트릭 설계, 장애 도메인 분할(영역화)에 따라 체감 수렴 성능이 크게 달라진다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))

### 오해하기 쉬운 포인트
- "링크 상태가 항상 거리 벡터보다 우월하다"는 단정은 부정확하다. 환경 규모와 요구사항에 따라 trade-off가 다르다.
- "라우팅 알고리즘은 순수 수학 문제"라는 오해가 있다. 실제 인터넷은 정책, 안정성, 운영 단순화 요구가 함께 작동한다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))

### 체크 질문
1. 링크 상태와 거리 벡터의 정보 교환 단위를 각각 무엇으로 볼 수 있는가?
2. 수렴 속도와 안정성의 균형을 위해 어떤 운영 파라미터를 조정할 수 있는가?
3. 정책 기반 선택이 최단경로를 의도적으로 포기하는 대표 이유는 무엇인가?

### 한 줄 요약
- 라우팅 알고리즘은 최단경로 계산 기법이 아니라, 수렴·안정성·정책 제약을 함께 만족시키는 제어 평면 설계 도구다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))

## 5.3 인터넷에서의 AS 내부 라우팅: OSPF
### 학습목표
- OSPF의 기본 동작(링크 상태 교환, SPF 계산, 영역 구조)을 설명할 수 있다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))
- OSPFv2와 OSPFv3의 공통점/차이를 설명할 수 있다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([RFC 5340](https://www.rfc-editor.org/rfc/rfc5340))
- ABR/ASBR, 백본 영역(area 0)의 운영 의미를 설명할 수 있다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))

### 핵심개념
- OSPF는 링크 상태 광고(LSA)와 인접 관계 형성을 통해 영역 내 토폴로지를 공유하고, 각 라우터가 SPF를 계산해 경로를 결정한다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))
- 영역 기반 설계는 LSA 범위를 제한해 제어 평면 확장성을 개선한다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))
- OSPFv3는 IPv6 지원을 포함해 확장되었지만, 링크 상태 기반 제어 원리는 OSPFv2와 동일하다.([RFC 5340](https://www.rfc-editor.org/rfc/rfc5340))
- OSPF는 AS 내부(IGP) 경로 최적화에 초점을 두며, 인터넷 도메인 간 정책 조정은 BGP가 담당한다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))

### 심화 포인트
- OSPF 성능은 프로토콜 자체보다 영역 분할, 요약, 링크 비용 정책 설계에 크게 좌우된다.
- 대규모 환경에서 불안정한 인접 관계(Flap)는 SPF 재계산 빈도를 높여 전체 제어 평면 부담을 키울 수 있다.

### 오해하기 쉬운 포인트
- "OSPF는 자동이므로 설계 없이도 확장된다"는 오해가 있다. 영역 설계와 메트릭 정책 없이는 제어 평면 불안정이 커질 수 있다.
- "OSPF 메트릭은 실제 지연을 완전히 반영한다"는 단정은 부정확하다. 메트릭은 운영자가 정의하는 비용 모델이다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))

### 체크 질문
1. OSPF에서 area 0가 필요한 이유를 설명할 수 있는가?
2. ABR와 ASBR의 역할 차이를 실제 경로 관점에서 구분할 수 있는가?
3. OSPF 수렴 품질을 개선하기 위해 우선 점검할 설계 요소는 무엇인가?

### 한 줄 요약
- OSPF는 링크 상태 공유와 영역 구조를 통해 AS 내부 라우팅의 수렴성과 확장성을 동시에 확보하는 IGP다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([RFC 5340](https://www.rfc-editor.org/rfc/rfc5340))

## 5.4 인터넷 서비스 제공업자(ISP) 간의 라우팅: BGP
### 학습목표
- BGP-4의 핵심 역할(AS 간 경로 교환, 정책 기반 선택)을 설명할 수 있다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- eBGP와 iBGP의 역할 차이를 설명할 수 있다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- 경로 속성(AS_PATH, NEXT_HOP, LOCAL_PREF 등)이 의사결정에 미치는 영향을 설명할 수 있다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))

### 핵심개념
- BGP는 경로 벡터(path vector) 접근으로 AS 경계 간 경로 정보를 교환하고, 정책에 따라 최적 경로를 선택한다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- 인터넷 규모 확장을 위해 BGP는 다중 프로토콜 확장(MP-BGP), 4바이트 ASN 지원 등으로 진화했다.([RFC 4760](https://www.rfc-editor.org/rfc/rfc4760))([RFC 6793](https://www.rfc-editor.org/rfc/rfc6793))
- BGP는 단순 "최단 경로"보다 정책, 경제 관계, 안정성을 우선하는 의사결정 체계를 가진다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- 업데이트 오류 처리 개선은 대규모 인터넷 안정성 확보에 중요하다.([RFC 7606](https://www.rfc-editor.org/rfc/rfc7606))

### 심화 포인트
- ISP 간 연결에서는 사업자 관계(고객/피어/상위)가 라우팅 정책을 결정하며, 이는 데이터 경로와 비용 구조를 동시에 바꾼다.
- BGP 운영에서 핵심은 빠른 수렴만이 아니라, 비정상 광고 차단과 정책 일관성 검증이다.

### 오해하기 쉬운 포인트
- "BGP는 AS_PATH가 가장 짧은 경로를 항상 선택한다"는 오해가 있다. 실제 선택은 다단계 정책 규칙에 따른다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- "BGP는 ISP만 필요하다"는 단정은 부정확하다. 멀티홈 기업/대규모 데이터센터에서도 정책 제어를 위해 사용된다.

### 체크 질문
1. eBGP와 iBGP의 정보 전파 목적 차이는 무엇인가?
2. BGP 정책이 성능 최적화와 충돌하는 대표 상황은 무엇인가?
3. BGP 안정성 개선을 위해 운영자가 우선 점검해야 할 항목은 무엇인가?

### 한 줄 요약
- BGP는 인터넷 도메인 간 라우팅의 표준 제어 프로토콜이며, 기술적 최단경로보다 정책 기반 선택이 핵심이다.([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))

## 5.5 소프트웨어 정의 네트워크(SDN) 제어 평면
### 학습목표
- SDN 제어 평면의 핵심 개념(제어/데이터 평면 분리, 논리적 중앙화)을 설명할 수 있다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- 전통 분산 제어 평면과 SDN 제어 평면의 운영 차이를 설명할 수 있다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- SDN 제어 평면 도입 시 장점과 리스크를 균형 있게 설명할 수 있다.([IBM: What is software-defined networking (SDN)?](https://www.ibm.com/think/topics/software-defined-networking))([Cisco: What is software-defined networking?](https://www.cisco.com/site/us/en/learn/topics/software-defined-networking/what-is-sdn.html))

### 핵심개념
- SDN은 제어 로직을 소프트웨어화해 네트워크 정책 변경과 자동화를 빠르게 수행할 수 있게 한다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))([IBM: What is software-defined networking (SDN)?](https://www.ibm.com/think/topics/software-defined-networking))
- 데이터 평면 장치는 제어 평면이 배포한 규칙을 실행하며, 운영자는 전역 관점의 정책 일관성을 확보하기 쉬워진다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))
- SDN은 기존 라우팅 프로토콜을 완전히 대체하기보다, 환경에 따라 공존·보완되는 형태로 도입되는 경우가 많다.([Cisco: What is software-defined networking?](https://www.cisco.com/site/us/en/learn/topics/software-defined-networking/what-is-sdn.html))

### 심화 포인트
- SDN 제어 평면의 핵심 가치는 "프로그램 가능성"이며, 이는 정책 배포 속도와 변경 이력 관리 품질을 높인다.
- 반면 제어 시스템 장애가 광범위 서비스 영향으로 확산될 수 있어 고가용성, 롤백, 검증 파이프라인이 필수다.

### 오해하기 쉬운 포인트
- "SDN = 중앙 장비 1대"라는 오해가 있다. 실제 구현은 분산 제어기 클러스터와 계층적 제어 모델을 사용한다.
- "SDN 도입 즉시 운영 복잡도가 감소한다"는 단정은 틀리다. 초기에는 모델링·자동화 체계 정비로 복잡도가 증가할 수 있다.

### 체크 질문
1. SDN 제어 평면이 기존 분산 라우팅 대비 제공하는 운영 이점은 무엇인가?
2. 제어기 장애 시 데이터 평면 영향 반경을 줄이는 설계 요소는 무엇인가?
3. SDN 도입 효과를 검증할 때 어떤 지표(변경 리드타임, 장애율 등)를 봐야 하는가?

### 한 줄 요약
- SDN 제어 평면은 네트워크를 코드와 정책 중심으로 운영하게 만들지만, 안정적 자동화를 위한 검증 체계가 전제되어야 한다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))

## 5.6 인터넷 제어 메시지 프로토콜(ICMP)
### 학습목표
- ICMPv4/ICMPv6의 역할(오류 보고, 진단 지원)을 설명할 수 있다.([RFC 792](https://www.rfc-editor.org/rfc/rfc792))([RFC 4443](https://www.rfc-editor.org/rfc/rfc4443))
- ICMP와 PMTU 발견의 관계를 설명할 수 있다.([RFC 1191](https://www.rfc-editor.org/rfc/rfc1191))([RFC 8201](https://www.rfc-editor.org/rfc/rfc8201))
- ICMP 필터링 정책이 운영 안정성에 미치는 영향을 설명할 수 있다.([RFC 4890](https://www.rfc-editor.org/rfc/rfc4890))

### 핵심개념
- ICMP는 데이터 전달 프로토콜이 아니라, IP 동작 중 발생한 오류와 제어 정보를 전달하는 보조 프로토콜이다.([RFC 792](https://www.rfc-editor.org/rfc/rfc792))([RFC 4443](https://www.rfc-editor.org/rfc/rfc4443))
- `Echo Request/Reply`, `Destination Unreachable`, `Time Exceeded` 같은 메시지는 장애 분석과 경로 진단에 핵심적이다.([RFC 792](https://www.rfc-editor.org/rfc/rfc792))([RFC 4443](https://www.rfc-editor.org/rfc/rfc4443))
- PMTU 발견은 ICMP 오류 메시지를 활용해 패킷 크기를 조정하므로, 무분별한 ICMP 차단은 연결 품질 저하를 유발할 수 있다.([RFC 1191](https://www.rfc-editor.org/rfc/rfc1191))([RFC 8201](https://www.rfc-editor.org/rfc/rfc8201))
- IPv6 환경에서는 Neighbor Discovery가 ICMPv6 기반으로 동작하므로 ICMPv6 제어 트래픽 관리가 특히 중요하다.([RFC 4861](https://www.rfc-editor.org/rfc/rfc4861))

### 심화 포인트
- ICMP 정책은 "차단/허용" 이분법보다, 필요한 타입을 선별 허용하는 방식으로 설계해야 운영성과 보안을 동시에 확보할 수 있다.([RFC 4890](https://www.rfc-editor.org/rfc/rfc4890))
- 진단 도구(`ping`, `traceroute`) 해석 시 ICMP 응답 자체의 지연과 실제 데이터 경로 품질을 구분해 봐야 한다.

### 오해하기 쉬운 포인트
- "ICMP는 보안상 위험하니 전부 차단해야 한다"는 오해가 있다. 전면 차단은 PMTU/진단 기능을 손상시킬 수 있다.([RFC 4890](https://www.rfc-editor.org/rfc/rfc4890))
- "ICMP가 신뢰 전송을 보장한다"는 오해가 있다. 신뢰성은 전송 계층(TCP 등)의 책임이다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))

### 체크 질문
1. ICMP 차단 정책이 PMTU 문제를 어떻게 유발하는지 설명할 수 있는가?
2. ICMPv4와 ICMPv6에서 운영상 중요도가 크게 달라지는 지점은 무엇인가?
3. 장애 분석 시 ICMP 결과를 과신하면 어떤 오판이 생길 수 있는가?

### 한 줄 요약
- ICMP는 네트워크 제어·진단의 필수 신호 체계이며, 보안과 운영 가시성의 균형 있는 정책이 중요하다.([RFC 792](https://www.rfc-editor.org/rfc/rfc792))([RFC 4890](https://www.rfc-editor.org/rfc/rfc4890))

## 5.7 네트워크 관리와 SNMP, NETCONF/YANG
### 학습목표
- SNMP 관리 프레임워크의 핵심 구성(매니저, 에이전트, MIB)을 설명할 수 있다.([RFC 3411](https://www.rfc-editor.org/rfc/rfc3411))([RFC 3416](https://www.rfc-editor.org/rfc/rfc3416))
- SNMPv3 보안 모델의 목적을 설명할 수 있다.([RFC 3414](https://www.rfc-editor.org/rfc/rfc3414))
- NETCONF와 YANG이 구성 관리 자동화에서 어떤 역할을 하는지 설명할 수 있다.([RFC 6241](https://www.rfc-editor.org/rfc/rfc6241))([RFC 7950](https://www.rfc-editor.org/rfc/rfc7950))

### 핵심개념
- SNMP는 장비 상태 모니터링과 이벤트 통지에 널리 사용되는 관리 프로토콜 체계다.([RFC 3411](https://www.rfc-editor.org/rfc/rfc3411))([RFC 3416](https://www.rfc-editor.org/rfc/rfc3416))
- SNMPv3는 인증/무결성/기밀성 강화를 통해 기존 버전의 보안 한계를 보완한다.([RFC 3414](https://www.rfc-editor.org/rfc/rfc3414))
- NETCONF는 구조화된 구성 변경과 트랜잭션 모델을 제공하며, YANG은 데이터 모델 표준으로 관리 대상의 스키마를 정의한다.([RFC 6241](https://www.rfc-editor.org/rfc/rfc6241))([RFC 6242](https://www.rfc-editor.org/rfc/rfc6242))([RFC 7950](https://www.rfc-editor.org/rfc/rfc7950))
- 운영 자동화에서는 "관측(SNMP)"과 "구성( NETCONF/YANG )"을 분리해 설계하는 것이 변경 안정성에 유리하다.

### 심화 포인트
- 대규모 네트워크 운영에서는 폴링 주기, 알람 기준, 구성 검증 파이프라인을 함께 설계해야 실제 가용성 향상으로 이어진다.
- YANG 기반 모델링은 멀티벤더 환경에서 구성 일관성 확보에 유리하지만, 모델 버전 관리와 호환성 전략이 필수다.([RFC 7950](https://www.rfc-editor.org/rfc/rfc7950))

### 오해하기 쉬운 포인트
- "NETCONF/YANG이 있으면 SNMP는 불필요하다"는 단정은 부정확하다. 모니터링/이벤트 수집에서는 SNMP가 여전히 유용하다.
- "관리 프로토콜은 운영 편의 기능"이라는 오해가 있다. 실제로는 장애 탐지 속도와 변경 실패율을 결정하는 핵심 제어면이다.

### 체크 질문
1. SNMP와 NETCONF/YANG을 운영 파이프라인에서 어떻게 역할 분리할 것인가?
2. SNMPv3를 우선 적용해야 하는 이유를 보안 관점에서 설명할 수 있는가?
3. YANG 모델 도입 시 버전 호환성 문제를 줄이는 실무 원칙은 무엇인가?

### 한 줄 요약
- 네트워크 관리는 관측과 구성 제어를 분리해 자동화해야 하며, SNMP와 NETCONF/YANG은 상호 대체가 아닌 보완 관계다.([RFC 3411](https://www.rfc-editor.org/rfc/rfc3411))([RFC 6241](https://www.rfc-editor.org/rfc/rfc6241))([RFC 7950](https://www.rfc-editor.org/rfc/rfc7950))

## 5.8 요약
### 학습목표
- 5.1~5.7의 제어 평면 개념을 통합해 인터넷 라우팅 운영 관점으로 설명할 수 있다.
- 경로 계산, 정책, 제어 메시지, 관리 자동화가 서로 어떻게 연결되는지 설명할 수 있다.

### 핵심개념
- 제어 평면은 알고리즘(OSPF), 정책(BGP), 운영 신호(ICMP), 관리 프로토콜(SNMP/NETCONF/YANG)이 결합된 체계다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))([RFC 792](https://www.rfc-editor.org/rfc/rfc792))([RFC 6241](https://www.rfc-editor.org/rfc/rfc6241))
- 안정적인 데이터 평면 품질은 제어 평면 수렴, 정책 일관성, 관리 자동화 품질에 의해 좌우된다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))

### 심화 포인트
- Chapter 5의 핵심은 개별 프로토콜 암기가 아니라, "경로 결정-정책 적용-운영 관측-구성 변경"의 폐루프를 이해하는 것이다.

### 오해하기 쉬운 포인트
- 요약 섹션은 새 주장 추가가 아니라, 앞선 검증 사실을 제어 평면 관점으로 재구성하는 단계다.

### 체크 질문
1. OSPF와 BGP를 각각 어디에 배치해야 전체 제어 평면이 안정적인가?
2. ICMP 정책과 PMTU/진단 품질을 동시에 만족시키는 기준은 무엇인가?
3. Chapter 6(링크 계층) 학습 전에 고정해야 할 제어 평면 핵심 개념은 무엇인가?

### 한 줄 요약
- Chapter 5는 인터넷 제어 평면이 경로 알고리즘, 정책 라우팅, 관리 자동화를 통해 데이터 평면 품질을 만드는 방식을 다룬다.

## Chapter 5 핵심 연결 요약
- 제어 평면은 OSPF 같은 IGP와 BGP 같은 EGP를 분리 운용해 규모와 정책 요구를 동시에 충족한다.([RFC 2328](https://www.rfc-editor.org/rfc/rfc2328))([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- 라우팅 알고리즘은 최단경로 계산 그 자체보다 수렴 안정성과 운영 정책 반영 능력이 중요하다.([RFC 2453](https://www.rfc-editor.org/rfc/rfc2453))([RFC 4271](https://www.rfc-editor.org/rfc/rfc4271))
- ICMP는 오류 보고와 경로 진단, PMTU 동작에 필수이며 무분별 차단은 운영 품질을 저하시킬 수 있다.([RFC 792](https://www.rfc-editor.org/rfc/rfc792))([RFC 8201](https://www.rfc-editor.org/rfc/rfc8201))([RFC 4890](https://www.rfc-editor.org/rfc/rfc4890))
- SNMP와 NETCONF/YANG은 각각 관측과 구성 자동화 축에서 제어 평면 운영 완성도를 높인다.([RFC 3411](https://www.rfc-editor.org/rfc/rfc3411))([RFC 6241](https://www.rfc-editor.org/rfc/rfc6241))([RFC 7950](https://www.rfc-editor.org/rfc/rfc7950))
- SDN 제어 평면은 정책 자동화와 가시성을 확장하지만, 고가용성·검증 체계가 함께 설계되어야 한다.([RFC 7426](https://www.rfc-editor.org/rfc/rfc7426))

## 참고문헌
- IETF RFC 1958, *Architectural Principles of the Internet*. ([링크](https://www.rfc-editor.org/rfc/rfc1958))
- IETF RFC 1812, *Requirements for IP Version 4 Routers*. ([링크](https://www.rfc-editor.org/rfc/rfc1812))
- IETF RFC 7426, *Software-Defined Networking (SDN): Layers and Architecture Terminology*. ([링크](https://www.rfc-editor.org/rfc/rfc7426))
- IETF RFC 2328, *OSPF Version 2*. ([링크](https://www.rfc-editor.org/rfc/rfc2328))
- IETF RFC 5340, *OSPF for IPv6*. ([링크](https://www.rfc-editor.org/rfc/rfc5340))
- IETF RFC 2453, *RIP Version 2*. ([링크](https://www.rfc-editor.org/rfc/rfc2453))
- IETF RFC 4271, *A Border Gateway Protocol 4 (BGP-4)*. ([링크](https://www.rfc-editor.org/rfc/rfc4271))
- IETF RFC 4760, *Multiprotocol Extensions for BGP-4*. ([링크](https://www.rfc-editor.org/rfc/rfc4760))
- IETF RFC 6793, *BGP Support for Four-Octet AS Number Space*. ([링크](https://www.rfc-editor.org/rfc/rfc6793))
- IETF RFC 7606, *Revised Error Handling for BGP UPDATE Messages*. ([링크](https://www.rfc-editor.org/rfc/rfc7606))
- IETF RFC 792, *Internet Control Message Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc792))
- IETF RFC 4443, *ICMPv6 (ICMP for the Internet Protocol Version 6 (IPv6) Specification)*. ([링크](https://www.rfc-editor.org/rfc/rfc4443))
- IETF RFC 1191, *Path MTU Discovery*. ([링크](https://www.rfc-editor.org/rfc/rfc1191))
- IETF RFC 8201, *Path MTU Discovery for IP version 6*. ([링크](https://www.rfc-editor.org/rfc/rfc8201))
- IETF RFC 4861, *Neighbor Discovery for IP version 6 (IPv6)*. ([링크](https://www.rfc-editor.org/rfc/rfc4861))
- IETF RFC 4890, *Recommendations for Filtering ICMPv6 Messages in Firewalls*. ([링크](https://www.rfc-editor.org/rfc/rfc4890))
- IETF RFC 3411, *An Architecture for Describing SNMP Management Frameworks*. ([링크](https://www.rfc-editor.org/rfc/rfc3411))
- IETF RFC 3414, *User-based Security Model (USM) for SNMPv3*. ([링크](https://www.rfc-editor.org/rfc/rfc3414))
- IETF RFC 3416, *Version 2 of the Protocol Operations for the Simple Network Management Protocol (SNMP)*. ([링크](https://www.rfc-editor.org/rfc/rfc3416))
- IETF RFC 6241, *Network Configuration Protocol (NETCONF)*. ([링크](https://www.rfc-editor.org/rfc/rfc6241))
- IETF RFC 6242, *Using the NETCONF Protocol over Secure Shell (SSH)*. ([링크](https://www.rfc-editor.org/rfc/rfc6242))
- IETF RFC 7950, *The YANG 1.1 Data Modeling Language*. ([링크](https://www.rfc-editor.org/rfc/rfc7950))
- IETF RFC 9293, *Transmission Control Protocol (TCP)*. ([링크](https://www.rfc-editor.org/rfc/rfc9293))
- IBM, *What is computer networking?* ([링크](https://www.ibm.com/think/topics/networking))
- IBM, *What is software-defined networking (SDN)?* ([링크](https://www.ibm.com/think/topics/software-defined-networking))
- Cisco, *What is routing?* ([링크](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-routing.html))
- Cisco, *What is software-defined networking?* ([링크](https://www.cisco.com/site/us/en/learn/topics/software-defined-networking/what-is-sdn.html))
