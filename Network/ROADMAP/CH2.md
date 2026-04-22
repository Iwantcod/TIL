# Chapter 2 학습 문서

## 작성/검증 원칙
- 출처 우선순위: IETF 표준/BCP 및 RFC를 1차 기준으로 두고, IBM/Cisco 공식 문서로 개념 설명을 교차검증한다.([RFC 1123](https://www.rfc-editor.org/rfc/rfc1123))([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))([Cisco: Understand DNS](https://www.cisco.com/c/en/us/support/docs/security/umbrella/225283-understand-dns.html))
- 추정 배제: 프로토콜 동작, 상태 코드, 메시지 구조, 전송 특성은 확인된 근거만 기록한다.
- 용어 일관성: `요청/응답`, `메시지 포맷`, `이름 해석`, `오버레이 분배`, `적응형 스트리밍`, `소켓 API`를 구분해 사용한다.
- 업데이트 기준일: 2026-04-22 (KST).

## 2.1 네트워크 애플리케이션의 원리
### 학습목표
- 인터넷 애플리케이션이 전송 계층과 소켓 인터페이스 위에서 동작함을 설명할 수 있다.([RFC 1123](https://www.rfc-editor.org/rfc/rfc1123))([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))
- 애플리케이션 설계에서 TCP/UDP 선택 기준(신뢰성, 지연, 혼잡제어 책임)을 설명할 수 있다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))
- 클라이언트-서버와 P2P 모델의 구조적 차이를 설명할 수 있다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))([IBM: What is computer networking?](https://www.ibm.com/think/topics/networking))

### 핵심개념
- RFC 1123은 인터넷 호스트의 애플리케이션 및 지원 프로토콜 요구사항을 정의한다.([RFC 1123](https://www.rfc-editor.org/rfc/rfc1123))
- 애플리케이션은 일반적으로 소켓 API를 통해 전송 계층(TCP/UDP)에 접근한다.([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))
- UDP 기반 애플리케이션은 혼잡 붕괴 방지를 위한 자체 제어 메커니즘을 반드시 고려해야 한다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 심화 포인트
- 애플리케이션 성능은 프로토콜 선택뿐 아니라 메시지 크기, 재전송 전략, 이름 해석 지연(DNS)에도 크게 좌우된다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))([IBM: What is DNS lookup?](https://www.ibm.com/think/topics/dns-lookup))
- 같은 기능이라도 중앙집중형(클라이언트-서버)과 분산형(P2P)은 확장성 병목 위치가 다르다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))

### 오해하기 쉬운 포인트
- "UDP를 쓰면 무조건 빠르고 유리하다"는 단정은 틀리다. 신뢰성·혼잡제어를 애플리케이션이 직접 감당해야 한다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))
- "애플리케이션 설계는 전송 계층과 독립적이다"는 오해가 있다. 실제 동작 특성은 전송 선택과 강하게 결합된다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))

### 체크 질문
1. 애플리케이션 설계 시 TCP 대신 UDP를 선택할 때 추가로 구현해야 할 책임은 무엇인가?
2. 클라이언트-서버와 P2P에서 확장성 병목이 각각 어디에서 발생하는가?
3. 소켓 API 관점에서 앱 계층과 전송 계층의 경계를 어떻게 설명할 수 있는가?

### 한 줄 요약
- 네트워크 애플리케이션은 소켓 API와 전송 프로토콜 선택을 통해 성능·신뢰성·확장성의 트레이드오프를 결정한다.([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

## 2.2 웹과 HTTP
### 학습목표
- HTTP의 핵심 의미(상태 비저장, 요청/응답, 리소스 표현)를 설명할 수 있다.([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))
- HTTP/1.1, HTTP/2, HTTP/3의 주요 구조 차이를 설명할 수 있다.([RFC 9112](https://www.rfc-editor.org/rfc/rfc9112))([RFC 9113](https://www.rfc-editor.org/rfc/rfc9113))([RFC 9114](https://www.rfc-editor.org/rfc/rfc9114))
- 프록시/게이트웨이 같은 중간자 구성요소가 웹 전달에 미치는 영향을 설명할 수 있다.([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))([Cisco: What is a Proxy Server?](https://umbrella.cisco.com/blog/what-is-a-proxy-server))

### 핵심개념
- RFC 9110은 HTTP를 상태 비저장 애플리케이션 계층 프로토콜로 정의하고 공통 의미론을 규정한다.([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))
- RFC 9112는 HTTP/1.1의 메시지 문법과 연결 관리를 정의한다.([RFC 9112](https://www.rfc-editor.org/rfc/rfc9112))
- RFC 9113은 하나의 연결에서 다중 스트림 교환(멀티플렉싱)을 통해 효율을 높이는 HTTP/2를 정의한다.([RFC 9113](https://www.rfc-editor.org/rfc/rfc9113))
- RFC 9114는 QUIC 위에서 HTTP 의미론을 전달하는 HTTP/3 매핑을 정의한다.([RFC 9114](https://www.rfc-editor.org/rfc/rfc9114))([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))

### 심화 포인트
- HTTP 의미론(메서드, 상태코드, 헤더)은 버전이 바뀌어도 최대한 유지되며, 전송/프레이밍 계층이 진화한다.([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))([RFC 9113](https://www.rfc-editor.org/rfc/rfc9113))([RFC 9114](https://www.rfc-editor.org/rfc/rfc9114))
- 웹 경로의 프록시는 캐싱, 정책 집행, 보안 검사, 성능 최적화에 활용될 수 있다.([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))([Cisco: What is a Proxy Server?](https://umbrella.cisco.com/blog/what-is-a-proxy-server))

### 오해하기 쉬운 포인트
- "HTTP/3는 HTTP 의미 자체를 바꾼다"는 오해가 있다. 의미론은 유지되고 전송 계층(QUIC) 기반이 바뀐 것이다.([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))([RFC 9114](https://www.rfc-editor.org/rfc/rfc9114))
- "HTTP는 연결 상태를 항상 유지한다"는 표현은 부정확하다. 프로토콜 의미론은 상태 비저장이다.([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))

### 체크 질문
1. HTTP/1.1과 HTTP/2/3의 가장 큰 차이는 의미론인가, 전송/프레이밍인가?
2. QUIC 기반 HTTP/3가 지연 개선에 유리한 이유는 무엇인가?
3. 프록시가 웹 아키텍처에서 수행하는 역할을 2가지 이상 설명할 수 있는가?

### 한 줄 요약
- HTTP는 공통 의미론을 유지한 채 전송 계층을 진화시켜(1.1 -> 2 -> 3) 효율과 지연 특성을 개선해 왔다.([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))([RFC 9114](https://www.rfc-editor.org/rfc/rfc9114))

## 2.3 인터넷 전자메일
### 학습목표
- 전자메일 시스템에서 전송(SMTP)과 메시지 형식(IMF/MIME), 접근(IMAP/POP)의 역할을 구분할 수 있다.([RFC 5321](https://www.rfc-editor.org/rfc/rfc5321))([RFC 5322](https://www.rfc-editor.org/rfc/rfc5322))([RFC 2045](https://www.rfc-editor.org/info/rfc2045))([RFC 9051](https://www.rfc-editor.org/rfc/rfc9051))([RFC 1939](https://www.rfc-editor.org/rfc/rfc1939))
- SMTP의 핵심 동작(서버 간 전달, 릴레이, 요청/응답 모델)을 설명할 수 있다.([RFC 5321](https://www.rfc-editor.org/rfc/rfc5321))
- 현대 이메일 운영에서 보안 위협과 보호 필요성을 설명할 수 있다.([IBM: What is email security?](https://www.ibm.com/think/topics/email-security))

### 핵심개념
- RFC 5321은 인터넷 전자메일 전송의 기본 프로토콜(SMTP)을 정의한다.([RFC 5321](https://www.rfc-editor.org/rfc/rfc5321))
- RFC 5322는 이메일 헤더/본문의 인터넷 메시지 형식을 정의한다.([RFC 5322](https://www.rfc-editor.org/rfc/rfc5322))
- RFC 2045는 MIME 기반으로 본문 구조/콘텐츠 타입 확장을 정의한다.([RFC 2045](https://www.rfc-editor.org/info/rfc2045))
- IMAP4rev2(RFC 9051)는 서버 측 메일박스 동기화/조작을, POP3(RFC 1939)는 메일드롭 접근 모델을 제공한다.([RFC 9051](https://www.rfc-editor.org/rfc/rfc9051))([RFC 1939](https://www.rfc-editor.org/rfc/rfc1939))

### 심화 포인트
- 전송 계층의 성공(메일 전달)과 사용자 경험(동기화/폴더/검색)은 다른 프로토콜 계층의 책임이다.([RFC 5321](https://www.rfc-editor.org/rfc/rfc5321))([RFC 9051](https://www.rfc-editor.org/rfc/rfc9051))
- 실무에서는 전송 신뢰성 외에도 스푸핑/피싱/악성 첨부 대응까지 포함한 운영 보안이 중요하다.([IBM: What is email security?](https://www.ibm.com/think/topics/email-security))

### 오해하기 쉬운 포인트
- "SMTP 하나면 이메일 기능이 완성된다"는 오해가 있다. 형식(IMF/MIME)과 접근(IMAP/POP) 계층이 별도로 필요하다.([RFC 5321](https://www.rfc-editor.org/rfc/rfc5321))([RFC 5322](https://www.rfc-editor.org/rfc/rfc5322))([RFC 9051](https://www.rfc-editor.org/rfc/rfc9051))
- "IMAP과 POP는 단순 대체 관계"가 아니다. 동기화 모델과 사용 시나리오가 다르다.([RFC 9051](https://www.rfc-editor.org/rfc/rfc9051))([RFC 1939](https://www.rfc-editor.org/rfc/rfc1939))

### 체크 질문
1. SMTP와 IMAP/POP의 책임 경계를 사용자 관점에서 설명할 수 있는가?
2. MIME이 왜 필요한지, RFC 5322만으로 부족한 이유를 말할 수 있는가?
3. 이메일 보안에서 프로토콜 표준 준수 외에 운영 정책이 필요한 이유는 무엇인가?

### 한 줄 요약
- 전자메일은 SMTP(전송), IMF/MIME(형식), IMAP/POP(접근)의 조합으로 동작하는 다계층 서비스다.([RFC 5321](https://www.rfc-editor.org/rfc/rfc5321))([RFC 5322](https://www.rfc-editor.org/rfc/rfc5322))([RFC 9051](https://www.rfc-editor.org/rfc/rfc9051))

## 2.4 DNS: 인터넷의 디렉터리 서비스
### 학습목표
- DNS를 계층형/분산형 이름 해석 시스템으로 설명할 수 있다.([RFC 1034](https://www.rfc-editor.org/rfc/rfc1034))([RFC 1035](https://www.rfc-editor.org/rfc/rfc1035))
- 리소스 레코드와 질의/응답 흐름의 핵심을 설명할 수 있다.([RFC 1035](https://www.rfc-editor.org/rfc/rfc1035))
- 현대 DNS 전송 방식(예: DoH)과 운영 보안 관점을 설명할 수 있다.([RFC 8484](https://www.rfc-editor.org/rfc/rfc8484))([Cisco: DNS Best Practices, Network Protections, and Attack Identification](https://sec.cloudapps.cisco.com/security/center/resources/dns_best_practices))

### 핵심개념
- RFC 1034/1035는 DNS의 개념, 이름공간, 리소스 레코드, 메시지 형식의 기준을 제공한다.([RFC 1034](https://www.rfc-editor.org/rfc/rfc1034))([RFC 1035](https://www.rfc-editor.org/rfc/rfc1035))
- DNS는 사람이 읽는 도메인 이름을 IP 주소 등 네트워크 식별자로 해석해 인터넷 서비스 접근을 가능하게 한다.([IBM: What is DNS lookup?](https://www.ibm.com/think/topics/dns-lookup))([Cisco: Understand DNS](https://www.cisco.com/c/en/us/support/docs/security/umbrella/225283-understand-dns.html))
- RFC 8484는 DNS 질의를 HTTPS로 전달하는 DoH를 정의한다.([RFC 8484](https://www.rfc-editor.org/rfc/rfc8484))

### 심화 포인트
- DNS는 웹뿐 아니라 이메일(MX) 등 여러 인터넷 서비스의 기반이므로 장애/오염 시 영향 반경이 매우 크다.([RFC 1035](https://www.rfc-editor.org/rfc/rfc1035))([IBM: What is DNS lookup?](https://www.ibm.com/think/topics/dns-lookup))
- 운영 관점에서 DNS는 성능과 보안을 함께 다뤄야 하며, 캐시/재귀/권한 서버 구조 이해가 필수다.([Cisco: DNS Best Practices, Network Protections, and Attack Identification](https://sec.cloudapps.cisco.com/security/center/resources/dns_best_practices))([Cisco: Understand DNS](https://www.cisco.com/c/en/us/support/docs/security/umbrella/225283-understand-dns.html))

### 오해하기 쉬운 포인트
- "DNS는 단순 전화번호부라서 보안 영향이 작다"는 오해가 있다. 실제로 트래픽 유도의 핵심 제어면이다.([Cisco: DNS Best Practices, Network Protections, and Attack Identification](https://sec.cloudapps.cisco.com/security/center/resources/dns_best_practices))
- "DNS는 UDP만 사용한다"는 표현은 불완전하다. DNS는 TCP도 사용하며, DoH 같은 HTTP 기반 전송도 표준화되어 있다.([RFC 1035](https://www.rfc-editor.org/rfc/rfc1035))([RFC 8484](https://www.rfc-editor.org/rfc/rfc8484))

### 체크 질문
1. DNS가 계층형 분산 구조를 쓰는 이유를 확장성 관점에서 설명할 수 있는가?
2. DNS 레코드와 애플리케이션 동작(웹/메일)이 어떻게 연결되는가?
3. DoH 도입이 전통 DNS 운영/관측 방식에 어떤 영향을 줄 수 있는가?

### 한 줄 요약
- DNS는 인터넷 전반의 이름 해석 기반 인프라이며, 성능·신뢰성·보안을 동시에 요구하는 핵심 디렉터리 서비스다.([RFC 1034](https://www.rfc-editor.org/rfc/rfc1034))([RFC 8484](https://www.rfc-editor.org/rfc/rfc8484))

## 2.5 P2P 파일 분배
### 학습목표
- P2P 분배 모델에서 피어가 동시에 소비자이자 공급자라는 구조를 설명할 수 있다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))
- 청크 기반 분배, 무결성 검증, 피어 발견 방식의 핵심 개념을 설명할 수 있다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))
- P2P 분배에서 혼잡 제어/공정성 고려가 필요한 이유를 설명할 수 있다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 핵심개념
- IETF PPSPP(RFC 7574)는 동일 콘텐츠를 다수 피어가 스트리밍/분배하는 P2P 프로토콜 구조를 정의한다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))
- PPSPP는 청크 교환, 무결성 보호(예: Merkle tree 계열), 피어 간 상호 업로드를 핵심 메커니즘으로 둔다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))
- 중앙 트래커 또는 DHT 기반 발견 같은 다양한 피어 탐색 모델을 수용한다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))

### 심화 포인트
- P2P는 서버 중심 병목을 완화하지만, 피어 이탈(churn), 불균형 업로드, 악성 피어 대응 같은 운영 문제가 추가된다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))
- UDP 기반 전달을 택하는 경우 혼잡 제어와 손실 대응을 프로토콜/애플리케이션이 반드시 보완해야 한다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 오해하기 쉬운 포인트
- "P2P는 서버가 전혀 필요 없다"는 단정은 틀리다. 초기 시드/트래커/신뢰 앵커 등 보조 인프라가 필요한 경우가 많다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))
- "P2P는 항상 더 빠르다"는 오해가 있다. 피어 품질·망 상태·정책 제한에 따라 성능 편차가 크다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))

### 체크 질문
1. P2P에서 확장성 이점이 발생하는 정확한 메커니즘은 무엇인가?
2. 청크 무결성 검증이 없는 분배 시스템은 어떤 위험을 가지는가?
3. P2P 트래픽이 공용 네트워크와 공존하려면 어떤 혼잡 제어 원칙이 필요한가?

### 한 줄 요약
- P2P 파일 분배는 피어의 업로드 자원을 집합적으로 활용해 확장성을 얻지만, 무결성·혼잡 제어·운영 안정성 설계가 필수다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

## 2.6 비디오 스트리밍과 콘텐츠 분배 네트워크
### 학습목표
- 스트리밍 서비스가 세그먼트/플레이리스트 기반으로 동작하는 원리를 설명할 수 있다.([RFC 8216](https://www.rfc-editor.org/rfc/rfc8216))
- CDN이 지연 완화와 확장성 확보에 기여하는 메커니즘을 설명할 수 있다.([IBM: What is a content delivery network (CDN)?](https://www.ibm.com/think/topics/content-delivery-networks))([IBM: What is live streaming?](https://www.ibm.com/think/topics/live-streaming))
- 스트리밍 품질이 지연/지터/손실/경로 구조의 영향을 받는 이유를 설명할 수 있다.([Cisco: Video Quality of Service (QOS) Tutorial](https://www.cisco.com/c/en/us/support/docs/quality-of-service-qos/qos-video/212134-Video-Quality-of-Service-QOS-Tutorial.html))([Cisco: What is low latency?](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))

### 핵심개념
- RFC 8216(HLS)은 멀티미디어 스트림 전달을 위한 플레이리스트와 세그먼트 구조를 정의한다.([RFC 8216](https://www.rfc-editor.org/rfc/rfc8216))
- CDN은 엣지 서버 캐싱/근접 전달로 원본 서버 부담을 줄이고 전송 지연을 완화한다.([IBM: What is a content delivery network (CDN)?](https://www.ibm.com/think/topics/content-delivery-networks))
- 라이브 스트리밍은 실시간 전달 특성상 네트워크 품질 변동에 민감하다.([IBM: What is live streaming?](https://www.ibm.com/think/topics/live-streaming))([Cisco: Video Quality of Service (QOS) Tutorial](https://www.cisco.com/c/en/us/support/docs/quality-of-service-qos/qos-video/212134-Video-Quality-of-Service-QOS-Tutorial.html))

### 심화 포인트
- 적응형 비트레이트(ABR) 계열 스트리밍은 네트워크 상태에 따라 품질 계층을 전환해 재생 연속성을 우선 확보한다.([RFC 8216](https://www.rfc-editor.org/rfc/rfc8216))
- 멀티-CDN/분산 PoP 전략은 트래픽 급증 시 가용성과 품질을 동시에 확보하는 데 유리하다.([IBM: What is a content delivery network (CDN)?](https://www.ibm.com/think/topics/content-delivery-networks))([IBM: IBM Video Streaming](https://www.ibm.com/products/video-streaming))

### 오해하기 쉬운 포인트
- "고해상도 코덱만 좋으면 품질 문제가 해결된다"는 오해가 있다. 실제 품질은 네트워크 지연·손실·버퍼 정책과 함께 결정된다.([Cisco: Video Quality of Service (QOS) Tutorial](https://www.cisco.com/c/en/us/support/docs/quality-of-service-qos/qos-video/212134-Video-Quality-of-Service-QOS-Tutorial.html))([Cisco: What is low latency?](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))
- "CDN은 정적 콘텐츠에만 유효하다"는 단정은 틀리다. 라이브/동적 콘텐츠 전달에도 핵심 역할을 한다.([IBM: What is a content delivery network (CDN)?](https://www.ibm.com/think/topics/content-delivery-networks))

### 체크 질문
1. 스트리밍에서 세그먼트 크기와 버퍼 정책은 어떤 트레이드오프를 가지는가?
2. CDN이 원본 서버 병목을 줄이는 구조적 이유는 무엇인가?
3. 라이브 스트리밍 품질 문제를 진단할 때 어떤 네트워크 지표를 우선 보아야 하는가?

### 한 줄 요약
- 비디오 스트리밍은 세그먼트 기반 전송과 CDN 분산 전달을 결합해 대규모 시청자에게 품질·확장성을 동시에 제공한다.([RFC 8216](https://www.rfc-editor.org/rfc/rfc8216))([IBM: What is a content delivery network (CDN)?](https://www.ibm.com/think/topics/content-delivery-networks))

## 2.7 소켓 프로그래밍: 네트워크 애플리케이션 생성
### 학습목표
- 소켓 API를 이용해 애플리케이션이 전송 계층에 접근하는 기본 구조를 설명할 수 있다.([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))
- 이름 해석(`getaddrinfo`)과 주소 변환(`inet_pton`/`inet_ntop`)의 역할을 설명할 수 있다.([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))
- TCP/UDP 기반 소켓 설계 시 신뢰성·혼잡제어·메시지 경계 처리 책임을 구분할 수 있다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 핵심개념
- RFC 3493은 IPv6 확장을 포함한 기본 소켓 인터페이스 확장을 정의한다.([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))
- `getaddrinfo`는 프로토콜 독립적 주소 해석을 제공해 IPv4/IPv6 공존 환경 대응을 돕는다.([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))
- UDP 애플리케이션은 손실/중복/순서 문제를 직접 처리해야 하며, 혼잡 제어도 고려해야 한다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 심화 포인트
- 실무 소켓 프로그래밍에서는 API 호출 성공 여부뿐 아니라 타임아웃, 재시도, 백오프, 버퍼 전략이 성능·안정성을 좌우한다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))
- TCP 스트림은 메시지 경계가 보존되지 않으므로 애플리케이션 프레이밍 설계가 별도로 필요하다.([RFC 9293](https://www.rfc-editor.org/rfc/rfc9293))

### 오해하기 쉬운 포인트
- "소켓 = TCP"는 잘못된 등식이다. 소켓은 추상 인터페이스이고 전송 프로토콜 선택은 별개다.([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))([RFC 768](https://www.rfc-editor.org/rfc/rfc768))
- "UDP는 확인응답이 없으니 구현이 단순하다"는 오해가 있다. 신뢰성 요구가 생기면 애플리케이션 복잡도가 오히려 증가한다.([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 체크 질문
1. IPv4/IPv6 이중 스택 환경에서 `getaddrinfo`를 우선 사용하는 이유는 무엇인가?
2. TCP 소켓에서 애플리케이션 레벨 메시지 경계를 어떻게 설계할 것인가?
3. UDP 기반 실시간 앱에서 최소한 필요한 혼잡 제어 정책은 무엇인가?

### 한 줄 요약
- 소켓 프로그래밍의 본질은 전송 특성을 이해하고, 애플리케이션 책임(프레이밍·재전송·혼잡 대응)을 명확히 설계하는 데 있다.([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

## 2.8 요약
### 학습목표
- 2.1~2.7의 내용을 애플리케이션 설계 관점에서 통합해 설명할 수 있다.
- 프로토콜 선택과 서비스 품질의 연계를 구조적으로 설명할 수 있다.

### 핵심개념
- 애플리케이션 계층은 HTTP, 이메일, DNS, 스트리밍, P2P 등 서로 다른 프로토콜 생태계를 전송 계층 위에서 구성한다.([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))([RFC 5321](https://www.rfc-editor.org/rfc/rfc5321))([RFC 1034](https://www.rfc-editor.org/rfc/rfc1034))([RFC 8216](https://www.rfc-editor.org/rfc/rfc8216))
- 성능·보안·확장성 요구에 따라 아키텍처(중앙집중형/분산형)와 전송 전략(TCP/UDP/QUIC)이 달라진다.([RFC 9000](https://www.rfc-editor.org/rfc/rfc9000))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

### 심화 포인트
- Chapter 2의 핵심은 "서비스별 프로토콜 암기"보다 "요구사항 -> 프로토콜/아키텍처 선택"의 의사결정 틀을 만드는 것이다.

### 오해하기 쉬운 포인트
- 요약은 새로운 주장을 추가하는 단계가 아니라, 섹션별로 검증된 사실을 연결해 재구성하는 단계다.

### 체크 질문
1. 웹/이메일/DNS/스트리밍을 각각 다른 프로토콜로 설계한 이유를 공통 원리로 설명할 수 있는가?
2. 같은 서비스라도 전송 계층 선택이 달라질 때 사용자 체감 품질은 어떻게 변하는가?
3. Chapter 3(트랜스포트 계층)로 넘어가기 전에 고정해야 할 선행 개념은 무엇인가?

### 한 줄 요약
- Chapter 2는 인터넷 서비스가 애플리케이션 요구사항에 맞춰 서로 다른 프로토콜 조합으로 구현되는 원리를 정리하는 장이다.

## Chapter 2 핵심 연결 요약
- 애플리케이션 계층은 전송 계층 위에서 서비스 목적별 프로토콜(HTTP, SMTP/IMAP/POP, DNS 등)을 조합해 동작한다.([RFC 1123](https://www.rfc-editor.org/rfc/rfc1123))([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110))([RFC 5321](https://www.rfc-editor.org/rfc/rfc5321))([RFC 1034](https://www.rfc-editor.org/rfc/rfc1034))
- 웹은 HTTP 의미론을 유지하면서 1.1->2->3으로 진화해 효율과 지연 특성을 개선했다.([RFC 9112](https://www.rfc-editor.org/rfc/rfc9112))([RFC 9113](https://www.rfc-editor.org/rfc/rfc9113))([RFC 9114](https://www.rfc-editor.org/rfc/rfc9114))
- 이메일은 전송, 형식, 접근이 분리된 다계층 구조이며, DNS와도 강하게 결합된다.([RFC 5321](https://www.rfc-editor.org/rfc/rfc5321))([RFC 5322](https://www.rfc-editor.org/rfc/rfc5322))([RFC 1035](https://www.rfc-editor.org/rfc/rfc1035))
- DNS는 이름 해석 인프라로서 웹·메일·앱 전체를 떠받치며, 전송 방식도 확장되고 있다(DoH).([RFC 8484](https://www.rfc-editor.org/rfc/rfc8484))
- P2P와 스트리밍은 분산 업로드·세그먼트 전달·CDN 엣지 전략을 통해 대규모 분배와 품질 유지를 동시에 추구한다.([RFC 7574](https://www.rfc-editor.org/rfc/rfc7574))([RFC 8216](https://www.rfc-editor.org/rfc/rfc8216))([IBM: What is a content delivery network (CDN)?](https://www.ibm.com/think/topics/content-delivery-networks))
- 소켓 프로그래밍은 전송 특성을 코드 수준의 책임으로 변환하는 단계이며, UDP 사용 시 혼잡 제어 고려는 필수다.([RFC 3493](https://www.rfc-editor.org/rfc/rfc3493))([RFC 8085](https://www.rfc-editor.org/rfc/rfc8085))

## 참고문헌

- IETF RFC 1123, *Requirements for Internet Hosts -- Application and Support*. ([링크](https://www.rfc-editor.org/rfc/rfc1123))
- IETF RFC 9110, *HTTP Semantics*. ([링크](https://www.rfc-editor.org/rfc/rfc9110))
- IETF RFC 9112, *HTTP/1.1*. ([링크](https://www.rfc-editor.org/rfc/rfc9112))
- IETF RFC 9113, *HTTP/2*. ([링크](https://www.rfc-editor.org/rfc/rfc9113))
- IETF RFC 9114, *HTTP/3*. ([링크](https://www.rfc-editor.org/rfc/rfc9114))
- IETF RFC 9000, *QUIC: A UDP-Based Multiplexed and Secure Transport*. ([링크](https://www.rfc-editor.org/rfc/rfc9000))
- IETF RFC 5321, *Simple Mail Transfer Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc5321))
- IETF RFC 5322, *Internet Message Format*. ([링크](https://www.rfc-editor.org/rfc/rfc5322))
- IETF RFC 2045, *MIME Part One: Format of Internet Message Bodies*. ([링크](https://www.rfc-editor.org/info/rfc2045))
- IETF RFC 9051, *Internet Message Access Protocol (IMAP) - Version 4rev2*. ([링크](https://www.rfc-editor.org/rfc/rfc9051))
- IETF RFC 1939, *Post Office Protocol - Version 3*. ([링크](https://www.rfc-editor.org/rfc/rfc1939))
- IETF RFC 1034, *Domain Names - Concepts and Facilities*. ([링크](https://www.rfc-editor.org/rfc/rfc1034))
- IETF RFC 1035, *Domain Names - Implementation and Specification*. ([링크](https://www.rfc-editor.org/rfc/rfc1035))
- IETF RFC 8484, *DNS Queries over HTTPS (DoH)*. ([링크](https://www.rfc-editor.org/rfc/rfc8484))
- IETF RFC 7574, *Peer-to-Peer Streaming Peer Protocol (PPSPP)*. ([링크](https://www.rfc-editor.org/rfc/rfc7574))
- RFC 8216, *HTTP Live Streaming*. ([링크](https://www.rfc-editor.org/rfc/rfc8216))
- IETF RFC 3493, *Basic Socket Interface Extensions for IPv6*. ([링크](https://www.rfc-editor.org/rfc/rfc3493))
- IETF RFC 9293, *Transmission Control Protocol (TCP)*. ([링크](https://www.rfc-editor.org/rfc/rfc9293))
- IETF RFC 768, *User Datagram Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc768))
- IETF RFC 8085, *UDP Usage Guidelines*. ([링크](https://www.rfc-editor.org/rfc/rfc8085))
- IBM, *What is computer networking?* ([링크](https://www.ibm.com/think/topics/networking))
- IBM, *What is DNS lookup?* ([링크](https://www.ibm.com/think/topics/dns-lookup))
- IBM, *What is a content delivery network (CDN)?* ([링크](https://www.ibm.com/think/topics/content-delivery-networks))
- IBM, *What is live streaming?* ([링크](https://www.ibm.com/think/topics/live-streaming))
- IBM, *IBM Video Streaming*. ([링크](https://www.ibm.com/products/video-streaming))
- IBM, *What is email security?* ([링크](https://www.ibm.com/think/topics/email-security))
- Cisco, *Understand DNS*. ([링크](https://www.cisco.com/c/en/us/support/docs/security/umbrella/225283-understand-dns.html))
- Cisco, *DNS Best Practices, Network Protections, and Attack Identification*. ([링크](https://sec.cloudapps.cisco.com/security/center/resources/dns_best_practices))
- Cisco Umbrella, *What is a Proxy Server?* ([링크](https://umbrella.cisco.com/blog/what-is-a-proxy-server))
- Cisco, *Video Quality of Service (QOS) Tutorial*. ([링크](https://www.cisco.com/c/en/us/support/docs/quality-of-service-qos/qos-video/212134-Video-Quality-of-Service-QOS-Tutorial.html))
- Cisco, *What is low latency?* ([링크](https://www.cisco.com/c/en/us/solutions/data-center/data-center-networking/what-is-low-latency.html))
