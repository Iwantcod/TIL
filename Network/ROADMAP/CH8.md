# Chapter 8 학습 문서

## 작성/검증 원칙
- 출처 우선순위: IETF 표준/BCP 및 RFC를 1차 기준으로 두고, IBM/Cisco 공식 문서로 개념 설명을 교차검증한다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))([IBM: What is network security?](https://www.ibm.com/think/topics/network-security))([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))
- 추정 배제: 보안 위협, 암호 기술, 인증·암호화 프로토콜, 운영 보안 통제는 RFC와 공식 문서로 확인 가능한 사실만 기록한다.
- 용어 일관성: `기밀성`, `무결성`, `가용성`, `인증(Authentication)`, `인가(Authorization)`, `암호화(Encryption)`, `전자서명`, `종단점 인증`을 구분해 사용한다.([RFC 4949](https://www.rfc-editor.org/rfc/rfc4949))([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))
- 범위 명시: 무선 랜/셀룰러의 PHY·무선 접속 세부 보안 규격은 IEEE/3GPP 비중이 크며, 본 문서는 IETF의 IP·전송·인증·운영 보안 관점 중심으로 정리한다.([RFC 3748](https://www.rfc-editor.org/rfc/rfc3748))([RFC 6459](https://www.rfc-editor.org/rfc/rfc6459))
- 업데이트 기준일: 2026-04-22 (KST).

## 8.1 네트워크 보안이란 무엇인가?
### 학습목표
- 네트워크 보안의 핵심 목표(CIA: 기밀성·무결성·가용성)를 설명할 수 있다.([RFC 4949](https://www.rfc-editor.org/rfc/rfc4949))([IBM: What is network security?](https://www.ibm.com/think/topics/network-security))
- 보안 위협 모델링이 프로토콜 설계에서 왜 필수인지 설명할 수 있다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))
- 대규모 감시(pervasive monitoring)를 공격으로 간주하는 배경을 설명할 수 있다.([RFC 7258](https://www.rfc-editor.org/rfc/rfc7258))

### 핵심개념
- 네트워크 보안은 단일 장비가 아니라 위협 모델 기반의 다계층 통제(암호화, 인증, 접근통제, 모니터링)로 구성된다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))([Cisco: What is network security?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))
- IETF 보안 고려사항(RFC 3552)은 도청·변조·재전송·삽입·삭제·MITM·DoS 같은 위협을 체계적으로 검토하도록 요구한다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))
- 네트워크 보안은 성능·운영 편의와 충돌할 수 있으므로 위험 기반 우선순위 설정이 필요하다.([IBM: What is network security?](https://www.ibm.com/think/topics/network-security))

### 심화 포인트
- 보안 설계의 핵심은 "기술 도입"보다 자산·공격면·위협 행위자 가정을 명확히 하는 것이다.
- 동일한 통제도 배치 위치(종단, 전송 경로, 경계 장비)에 따라 효과와 부작용이 다르게 나타난다.

### 오해하기 쉬운 포인트
- "보안은 방화벽만 잘 두면 된다"는 오해가 있다. 실제로는 암호화·인증·운영 모니터링이 함께 필요하다.
- "내부망은 기본적으로 신뢰 가능"이라는 가정은 현대 분산 환경에서 성립하기 어렵다.([IBM: What is zero trust?](https://www.ibm.com/think/topics/zero-trust))

### 체크 질문
1. CIA 목표를 네트워크 운영 지표로 각각 어떻게 표현할 수 있는가?
2. RFC 3552의 위협 항목을 실제 서비스 설계에 어떻게 매핑할 수 있는가?
3. 프라이버시 위협을 가용성 위협과 구분해 대응해야 하는 이유는 무엇인가?

### 한 줄 요약
- 네트워크 보안의 출발점은 위협 모델이며, 기술 선택은 그 모델을 만족시키는 수단이어야 한다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))

## 8.2 암호의 원리
### 학습목표
- 대칭키·비대칭키 암호의 역할과 차이를 설명할 수 있다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))([RFC 8017](https://www.rfc-editor.org/rfc/rfc8017))
- 키 교환, 세션키, 전방향 기밀성(Forward Secrecy)의 의미를 설명할 수 있다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))
- 현대 프로토콜에서 AEAD가 왜 기본이 되었는지 설명할 수 있다.([RFC 5116](https://www.rfc-editor.org/rfc/rfc5116))

### 핵심개념
- 대칭키 암호는 고속 데이터 보호에 적합하고, 비대칭 암호는 키 교환·서명·신원 검증에 주로 사용된다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))
- TLS 1.3은 (EC)DHE 기반 키 교환으로 세션키를 만들고, AEAD 암호군으로 트래픽을 보호한다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))([RFC 5116](https://www.rfc-editor.org/rfc/rfc5116))
- 전방향 기밀성은 장기 키가 노출되어도 과거 세션 평문이 즉시 복호화되지 않도록 위험을 줄인다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))

### 심화 포인트
- 암호 알고리즘 선택은 강도만이 아니라 키 관리, 구현 안전성, 하드웨어 가속 여부까지 고려해야 한다.
- 보안 사고의 많은 원인은 암호학 이론 자체가 아니라 키 유출·인증서 오남용·잘못된 설정에서 발생한다.

### 오해하기 쉬운 포인트
- "강한 알고리즘 하나면 보안이 완성된다"는 오해가 있다. 키 수명·교체·폐기 정책이 함께 필요하다.
- "암호화는 기밀성만 제공한다"는 단정은 부정확하다. AEAD는 무결성 보호까지 함께 제공한다.([RFC 5116](https://www.rfc-editor.org/rfc/rfc5116))

### 체크 질문
1. 대칭키와 비대칭키를 실제 핸드셰이크 흐름에서 어떻게 분담해 쓰는가?
2. 전방향 기밀성이 없는 설계의 장기 위험은 무엇인가?
3. 암호 강도 외에 운영자가 반드시 점검해야 할 항목은 무엇인가?

### 한 줄 요약
- 암호의 핵심은 알고리즘 이름이 아니라 키 교환·키 관리·무결성 보호를 포함한 전체 설계다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))

## 8.3 메시지 무결성과 전자서명
### 학습목표
- 해시, MAC, 전자서명의 목적과 차이를 설명할 수 있다.([RFC 2104](https://www.rfc-editor.org/rfc/rfc2104))([RFC 8017](https://www.rfc-editor.org/rfc/rfc8017))([RFC 8032](https://www.rfc-editor.org/rfc/rfc8032))
- 메시지 무결성 검증이 기밀성과 다른 이유를 설명할 수 있다.
- 전자서명이 인증·부인방지에 기여하는 방식의 한계를 설명할 수 있다.([RFC 4949](https://www.rfc-editor.org/rfc/rfc4949))

### 핵심개념
- MAC(HMAC)은 공유 키 기반으로 무결성·송신자 인증을 제공하지만, 키를 공유하므로 부인방지 성격은 제한적이다.([RFC 2104](https://www.rfc-editor.org/rfc/rfc2104))
- 전자서명은 개인키로 생성하고 공개키로 검증해 발신자 증명과 변조 탐지를 지원한다.([RFC 8017](https://www.rfc-editor.org/rfc/rfc8017))([RFC 8032](https://www.rfc-editor.org/rfc/rfc8032))
- TLS는 핸드셰이크 전자서명과 레코드 무결성 보호를 결합해 세션 신뢰 기반을 만든다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))

### 심화 포인트
- 무결성 보호는 암호화와 별개 요구사항이며, 평문 데이터라도 무결성 보호 부재 시 공격 표면이 커진다.
- 서명 알고리즘 선택은 보안성뿐 아니라 인증서 생태계 호환성과 검증 비용에 영향을 준다.

### 오해하기 쉬운 포인트
- "암호화되어 있으면 변조도 자동 방지된다"는 오해가 있다. 무결성 검증 메커니즘이 별도로 필요하다.
- "전자서명은 절대적인 법적 부인방지를 보장한다"는 단정은 부정확하다. 키 관리·운영 통제·정책이 함께 필요하다.

### 체크 질문
1. HMAC과 전자서명을 동일 용도로 쓸 수 없는 이유는 무엇인가?
2. 무결성만 필요한 시나리오와 기밀성까지 필요한 시나리오를 구분할 수 있는가?
3. 서명 검증 실패를 운영에서 어떤 경보로 연결해야 하는가?

### 한 줄 요약
- 무결성과 전자서명은 "누가 보냈고 바뀌지 않았는가"를 검증하는 축이며, 암호화와 별개로 설계해야 한다.([RFC 2104](https://www.rfc-editor.org/rfc/rfc2104))([RFC 8032](https://www.rfc-editor.org/rfc/rfc8032))

## 8.4 종단점 인증
### 학습목표
- 종단점 인증에서 PKI, 인증서, 신뢰 체인의 역할을 설명할 수 있다.([RFC 5280](https://www.rfc-editor.org/rfc/rfc5280))
- 서비스 식별자 검증(이름 검증)이 왜 중요한지 설명할 수 있다.([RFC 6125](https://www.rfc-editor.org/rfc/rfc6125))
- 네트워크 접근 인증(EAP)과 애플리케이션 종단 인증(TLS)을 구분해 설명할 수 있다.([RFC 3748](https://www.rfc-editor.org/rfc/rfc3748))([RFC 5216](https://www.rfc-editor.org/rfc/rfc5216))

### 핵심개념
- PKI 기반 인증은 인증서 체인 검증과 만료/폐기 상태 확인을 통해 상대 신원을 검증한다.([RFC 5280](https://www.rfc-editor.org/rfc/rfc5280))
- TLS에서 종단점 인증은 인증서와 핸드셰이크 검증 절차를 통해 MITM 위험을 줄인다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))
- RFC 6125는 인증서 유효성뿐 아니라 서비스 이름 일치 검증을 요구해 잘못된 엔드포인트 수용을 방지한다.([RFC 6125](https://www.rfc-editor.org/rfc/rfc6125))
- EAP 프레임워크는 네트워크 접근 단계의 사용자/장치 인증을 지원하며, EAP-TLS는 대표적인 인증 방식이다.([RFC 3748](https://www.rfc-editor.org/rfc/rfc3748))([RFC 5216](https://www.rfc-editor.org/rfc/rfc5216))

### 심화 포인트
- 인증 실패의 다수는 암호 알고리즘 문제가 아니라 인증서 배포, 신뢰 저장소, 이름 검증 정책 설정 오류에서 발생한다.
- 대규모 환경에서는 단일 인증 수단보다 인증 수명주기(발급·회수·자동 갱신) 자동화가 운영 안정성을 좌우한다.

### 오해하기 쉬운 포인트
- "인증서가 유효하면 신뢰해도 된다"는 오해가 있다. 대상 서비스 이름 검증이 빠지면 MITM 위험이 남는다.([RFC 6125](https://www.rfc-editor.org/rfc/rfc6125))
- "인증은 로그인 단계에서 끝난다"는 단정은 부정확하다. 세션 중 키 갱신·재인증·토큰 만료 정책이 함께 필요하다.

### 체크 질문
1. 인증서 체인 검증과 이름 검증을 왜 분리해서 봐야 하는가?
2. 접근 인증(EAP)과 종단 인증(TLS)의 경계는 어디인가?
3. 인증서 운영 자동화가 보안과 가용성에 동시에 중요한 이유는 무엇인가?

### 한 줄 요약
- 종단점 인증의 핵심은 "인증서 존재"가 아니라 체인 검증과 이름 검증을 포함한 신원 검증 절차의 완결성이다.([RFC 5280](https://www.rfc-editor.org/rfc/rfc5280))([RFC 6125](https://www.rfc-editor.org/rfc/rfc6125))

## 8.5 전자메일의 보안
### 학습목표
- 이메일 보안의 주요 위협(스푸핑, 피싱, 변조, 도청)을 설명할 수 있다.([IBM: What is email security?](https://www.ibm.com/think/topics/email-security))
- SMTP 전송 보안(STARTTLS)과 도메인 인증(SPF/DKIM/DMARC)의 역할을 설명할 수 있다.([RFC 3207](https://www.rfc-editor.org/rfc/rfc3207))([RFC 7208](https://www.rfc-editor.org/rfc/rfc7208))([RFC 6376](https://www.rfc-editor.org/rfc/rfc6376))([RFC 7489](https://www.rfc-editor.org/rfc/rfc7489))
- 콘텐츠 보안(S/MIME)과 전송 보안의 차이를 설명할 수 있다.([RFC 8551](https://www.rfc-editor.org/rfc/rfc8551))

### 핵심개념
- SMTP STARTTLS는 전송 경로 암호화를 제공하지만, 메시지 자체의 종단 암호화/서명과는 목적이 다르다.([RFC 3207](https://www.rfc-editor.org/rfc/rfc3207))
- SPF는 발신 IP 정당성, DKIM은 도메인 서명 무결성, DMARC는 정책·정합성 검증을 제공한다.([RFC 7208](https://www.rfc-editor.org/rfc/rfc7208))([RFC 6376](https://www.rfc-editor.org/rfc/rfc6376))([RFC 7489](https://www.rfc-editor.org/rfc/rfc7489))
- S/MIME는 메시지 레벨 암호화/서명으로 수신 경로를 거쳐도 콘텐츠 보호를 강화한다.([RFC 8551](https://www.rfc-editor.org/rfc/rfc8551))
- MTA-STS는 SMTP TLS 적용 정책을 전달해 다운그레이드 위험 완화에 기여한다.([RFC 8461](https://www.rfc-editor.org/rfc/rfc8461))

### 심화 포인트
- 이메일 보안은 단일 기술 도입보다 발신 도메인 정책 정합성(SPF/DKIM/DMARC alignment)과 운영 모니터링이 핵심이다.
- 전송 보안이 있어도 피싱·사회공학 위협은 남기 때문에 콘텐츠 검사·사용자 교육·정책 집행이 병행되어야 한다.

### 오해하기 쉬운 포인트
- "TLS를 적용했으니 이메일이 완전히 안전하다"는 오해가 있다. 도메인 위·변조와 피싱 대응은 별도 체계가 필요하다.
- "SPF만 적용하면 스푸핑이 막힌다"는 단정은 부정확하다. DKIM/DMARC와 결합 운영이 필요하다.([RFC 7489](https://www.rfc-editor.org/rfc/rfc7489))

### 체크 질문
1. STARTTLS와 S/MIME의 보호 범위 차이를 설명할 수 있는가?
2. SPF/DKIM/DMARC를 함께 운영해야 하는 이유는 무엇인가?
3. 이메일 보안 운영에서 가장 먼저 계측해야 할 실패 지표는 무엇인가?

### 한 줄 요약
- 이메일 보안은 전송 암호화, 도메인 인증, 메시지 서명을 결합해 위조·도청·피싱 위험을 낮추는 다층 구조다.([RFC 3207](https://www.rfc-editor.org/rfc/rfc3207))([RFC 7489](https://www.rfc-editor.org/rfc/rfc7489))

## 8.6 TCP 연결의 보안: TLS
### 학습목표
- TLS 1.3의 핵심 보안 속성(기밀성, 무결성, 인증, PFS)을 설명할 수 있다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))
- TLS 핸드셰이크와 세션 키 파생 흐름을 개괄할 수 있다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))
- TLS 운영 모범사례(암호군, 버전, 인증서 정책)를 설명할 수 있다.([RFC 9325](https://www.rfc-editor.org/rfc/rfc9325))

### 핵심개념
- TLS는 TCP 위에서 동작하는 보안 프로토콜로, 핸드셰이크를 통해 암호 스위트 협상·키 교환·인증을 수행한다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))
- TLS 1.3은 구식 알고리즘/절차를 제거해 공격면을 줄이고 기본 보안성을 강화했다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))
- 0-RTT 데이터는 지연을 줄일 수 있지만 재전송(replay) 위험 특성을 이해하고 제한적으로 사용해야 한다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))
- BCP 195bis(RFC 9325)는 안전한 TLS/DTLS 구성 가이드를 제공한다.([RFC 9325](https://www.rfc-editor.org/rfc/rfc9325))

### 심화 포인트
- TLS 성능 최적화는 단순 CPU 문제가 아니라 핸드셰이크 왕복 횟수, 인증서 체인 크기, 세션 재개 정책과 직결된다.
- 운영 환경에서 TLS 취약점 노출은 프로토콜 결함보다 구버전 허용, 약한 암호군 유지, 인증서 관리 실패에서 자주 발생한다.

### 오해하기 쉬운 포인트
- "HTTPS를 켰으니 보안이 끝났다"는 오해가 있다. 인증서 검증·HSTS·키 관리·취약 설정 점검이 함께 필요하다.
- "TLS는 기밀성만 제공한다"는 단정은 부정확하다. 무결성과 인증까지 포함한 세션 보안을 제공한다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))

### 체크 질문
1. TLS 1.3이 이전 버전 대비 줄인 공격면은 무엇인가?
2. 0-RTT를 안전하게 적용하려면 어떤 조건을 만족해야 하는가?
3. TLS 운영 상태를 점검할 때 우선 봐야 할 설정 항목은 무엇인가?

### 한 줄 요약
- TLS는 TCP 연결의 보안 기본선이며, 강한 프로토콜 버전 선택과 올바른 운영 설정이 실제 보안 수준을 결정한다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))([RFC 9325](https://www.rfc-editor.org/rfc/rfc9325))

## 8.7 네트워크 계층 보안: IPsec과 가상 사설 네트워크
### 학습목표
- IPsec의 핵심 구성요소(AH/ESP/IKEv2/SA)를 설명할 수 있다.([RFC 4301](https://www.rfc-editor.org/rfc/rfc4301))([RFC 4302](https://www.rfc-editor.org/rfc/rfc4302))([RFC 4303](https://www.rfc-editor.org/rfc/rfc4303))([RFC 7296](https://www.rfc-editor.org/rfc/rfc7296))
- 터널 모드와 전송 모드의 차이를 설명할 수 있다.([RFC 4301](https://www.rfc-editor.org/rfc/rfc4301))
- VPN에서 IPsec이 제공하는 보안 가치와 운영 이슈를 설명할 수 있다.([IBM: What is a VPN?](https://www.ibm.com/think/topics/vpn))

### 핵심개념
- IPsec은 IP 계층에서 패킷 보호를 제공하며, ESP는 기밀성·무결성 보호, AH는 헤더 무결성 중심 보호를 제공한다.([RFC 4302](https://www.rfc-editor.org/rfc/rfc4302))([RFC 4303](https://www.rfc-editor.org/rfc/rfc4303))
- IKEv2는 보안 연관(SA) 협상과 키 관리 절차를 담당한다.([RFC 7296](https://www.rfc-editor.org/rfc/rfc7296))
- VPN은 공용 네트워크 위에서 사설 통신 보안을 제공하는 운영 패턴이며, 원격 접속·사이트 간 연결에 활용된다.([RFC 4301](https://www.rfc-editor.org/rfc/rfc4301))([IBM: What is a VPN?](https://www.ibm.com/think/topics/vpn))
- NAT 환경에서는 IPsec UDP 캡슐화(NAT-T)가 필요할 수 있다.([RFC 3948](https://www.rfc-editor.org/rfc/rfc3948))

### 심화 포인트
- IPsec 설계는 암호 강도뿐 아니라 터널 종단 배치, 라우팅 정책, MTU/분절 영향까지 함께 고려해야 한다.
- 대규모 VPN 운영에서 장애의 상당수는 키 재협상/정책 불일치/터널 경로 비대칭에서 발생한다.

### 오해하기 쉬운 포인트
- "IPsec이면 모든 트래픽이 자동 보호된다"는 오해가 있다. 정책 매칭 범위와 라우팅 경로가 정확히 설계되어야 한다.
- "VPN은 성능에 큰 영향이 없다"는 단정은 부정확하다. 암호화 오버헤드와 경로 우회로 지연이 증가할 수 있다.

### 체크 질문
1. AH와 ESP를 실제 운영에서 어떻게 구분해 선택할 수 있는가?
2. 터널 모드와 전송 모드 중 사이트 간 VPN에 적합한 이유를 설명할 수 있는가?
3. IPsec VPN 성능 저하를 진단할 때 우선 확인할 항목은 무엇인가?

### 한 줄 요약
- IPsec/VPN의 본질은 공용망 위에서 IP 계층 보안을 일관되게 적용하는 것이며, 키·정책·경로 설계가 성공을 좌우한다.([RFC 4301](https://www.rfc-editor.org/rfc/rfc4301))([RFC 7296](https://www.rfc-editor.org/rfc/rfc7296))

## 8.8 무선 랜과 4G/5G 셀룰러 네트워크 보안
### 학습목표
- 무선 액세스 보안에서 인증·키 관리·접근제어의 공통 원리를 설명할 수 있다.([RFC 3748](https://www.rfc-editor.org/rfc/rfc3748))([RFC 5216](https://www.rfc-editor.org/rfc/rfc5216))
- WLAN/셀룰러 환경에서 AAA 인프라(RADIUS/Diameter)의 역할을 설명할 수 있다.([RFC 2865](https://www.rfc-editor.org/rfc/rfc2865))([RFC 6733](https://www.rfc-editor.org/rfc/rfc6733))
- 셀룰러에서 IETF 관점의 IPv6 보안 운용 포인트를 설명할 수 있다.([RFC 6459](https://www.rfc-editor.org/rfc/rfc6459))([RFC 7066](https://www.rfc-editor.org/rfc/rfc7066))

### 핵심개념
- 무선 액세스 보안의 기본 축은 사용자/장치 인증, 세션 키 생성, 접근 정책 집행이다.([RFC 3748](https://www.rfc-editor.org/rfc/rfc3748))
- EAP 프레임워크와 EAP-TLS는 엔터프라이즈 무선 접근 제어에서 널리 쓰이는 인증 체계를 제공한다.([RFC 3748](https://www.rfc-editor.org/rfc/rfc3748))([RFC 5216](https://www.rfc-editor.org/rfc/rfc5216))
- RADIUS/Diameter는 AAA 신호 전달과 정책 적용의 핵심 인프라로 무선·이동 환경에서 중요한 역할을 한다.([RFC 2865](https://www.rfc-editor.org/rfc/rfc2865))([RFC 6733](https://www.rfc-editor.org/rfc/rfc6733))
- 4G/5G 맥락에서도 종단 IP 보안(TLS/IPsec)과 주소 운용 보안 정책이 서비스 보호에 중요하다.([RFC 6459](https://www.rfc-editor.org/rfc/rfc6459))([RFC 7066](https://www.rfc-editor.org/rfc/rfc7066))

### 심화 포인트
- 무선 보안은 접속 구간 보안만으로 충분하지 않으며, 코어·클라우드 구간의 종단 암호화가 함께 필요하다.
- 대규모 무선 환경에서는 인증서/자격증명 수명주기 관리가 보안성과 운영비용을 동시에 좌우한다.

### 오해하기 쉬운 포인트
- "WLAN 암호화가 있으니 상위 계층 암호화는 불필요하다"는 오해가 있다. 종단 보호는 별도로 필요하다.
- "셀룰러망은 통신사 구간이라 기본적으로 안전하다"는 단정은 부정확하다. 애플리케이션 종단 보안은 여전히 필수다.

### 체크 질문
1. 무선 액세스 인증과 애플리케이션 종단 인증을 왜 분리해 설계해야 하는가?
2. AAA 인프라 장애가 사용자 체감 품질에 어떤 형태로 나타나는가?
3. 4G/5G 환경에서 TLS/IPsec을 추가 적용해야 하는 이유는 무엇인가?

### 한 줄 요약
- 무선·셀룰러 보안은 액세스 인증과 종단 암호화를 함께 적용하는 다층 접근이 기본이다.([RFC 3748](https://www.rfc-editor.org/rfc/rfc3748))([RFC 7066](https://www.rfc-editor.org/rfc/rfc7066))

## 8.9 운영 보안: 방화벽과 침입 탐지 시스템
### 학습목표
- 방화벽의 역할(정책 기반 트래픽 제어)과 한계를 설명할 수 있다.([RFC 2979](https://www.rfc-editor.org/rfc/rfc2979))([Cisco: What is a firewall?](https://www.cisco.com/site/us/en/learn/topics/security/what-is-a-firewall.html))
- IDS/IPS의 목적(탐지·차단)과 운영상 핵심 지표를 설명할 수 있다.([RFC 4765](https://www.rfc-editor.org/rfc/rfc4765))
- 보안 운영에서 로그/플로우 관측이 왜 중요한지 설명할 수 있다.([RFC 5424](https://www.rfc-editor.org/rfc/rfc5424))([RFC 7011](https://www.rfc-editor.org/rfc/rfc7011))

### 핵심개념
- 방화벽은 허용/차단 정책을 통해 공격면을 줄이지만, 허용된 트래픽 내부 위협까지 자동 해결하지는 못한다.([RFC 2979](https://www.rfc-editor.org/rfc/rfc2979))
- IDS/IPS는 시그니처·행위 기반 탐지로 이상 징후를 식별하며, 오탐/미탐 균형 조정이 운영 품질의 핵심이다.([RFC 4765](https://www.rfc-editor.org/rfc/rfc4765))
- 보안 운영은 로그(Syslog), 플로우(IPFIX), 알람 상관분석을 결합해 탐지 정확도와 대응 속도를 높인다.([RFC 5424](https://www.rfc-editor.org/rfc/rfc5424))([RFC 7011](https://www.rfc-editor.org/rfc/rfc7011))
- 경계 보안 정책은 애플리케이션 변경 주기와 함께 지속적으로 갱신되어야 실제 보호 효과를 유지한다.

### 심화 포인트
- 운영 보안의 핵심은 도구 개수가 아니라 탐지-분석-대응(MTTD/MTTR) 루프의 성숙도다.
- 암호화 트래픽 비중이 커질수록 메타데이터·행위 기반 탐지와 종단 텔레메트리의 중요성이 증가한다.

### 오해하기 쉬운 포인트
- "차단 정책을 강하게 하면 무조건 안전하다"는 오해가 있다. 과도한 차단은 가용성과 운영 복잡도를 악화시킬 수 있다.
- "IDS 경보가 많을수록 보안 수준이 높다"는 단정은 부정확하다. 오탐 비율이 높으면 대응 역량이 오히려 저하된다.

### 체크 질문
1. 방화벽 정책 품질을 측정할 수 있는 운영 지표는 무엇인가?
2. IDS/IPS에서 오탐과 미탐의 균형을 어떻게 관리할 수 있는가?
3. 암호화 트래픽 증가 시대에 필요한 추가 관측 체계는 무엇인가?

### 한 줄 요약
- 운영 보안의 성패는 경계 차단 장비 자체보다 탐지·관측·대응 프로세스의 일관성과 품질에서 결정된다.([RFC 4765](https://www.rfc-editor.org/rfc/rfc4765))([RFC 7011](https://www.rfc-editor.org/rfc/rfc7011))

## 8.10 요약
### 학습목표
- 8.1~8.9의 내용을 네트워크 보안 아키텍처 관점으로 통합 설명할 수 있다.
- 암호·인증·전송/네트워크 보안·운영 통제를 계층별로 연결해 설명할 수 있다.

### 핵심개념
- 네트워크 보안은 위협 모델 기반으로 암호화, 무결성, 인증, 접근 통제, 모니터링을 계층별로 결합한 체계다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))
- TLS(전송 계층)와 IPsec(네트워크 계층), 이메일 보안, 무선 접근 인증, 운영 보안 통제는 상호 대체가 아니라 상호 보완 관계다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))([RFC 4301](https://www.rfc-editor.org/rfc/rfc4301))
- 실무 보안 품질은 기술 스택보다 키·인증서·정책·관측의 운영 수명주기 관리에서 결정된다.

### 심화 포인트
- Chapter 8의 핵심은 개별 보안 기술 암기가 아니라 "공격면 축소 + 침해 탐지 + 복구"를 하나의 운영 체계로 연결하는 것이다.

### 오해하기 쉬운 포인트
- 요약 섹션은 새 주장 추가가 아니라, 앞선 검증 사실을 계층 통합 관점으로 재구성하는 단계다.

### 체크 질문
1. TLS와 IPsec을 각각 언제 선택해야 하는지 계층 관점에서 설명할 수 있는가?
2. 인증·암호화·운영 모니터링 중 하나라도 누락되면 어떤 위험이 생기는가?
3. Chapter 1~8 전체를 마친 뒤 보안 학습을 실무 운영으로 연결할 첫 단계는 무엇인가?

### 한 줄 요약
- Chapter 8은 네트워크 보안을 단일 기술이 아닌 계층별 통제와 운영 프로세스의 결합 시스템으로 이해하도록 만든다.

## Chapter 8 핵심 연결 요약
- 보안 설계의 기준은 위협 모델이며, CIA 목표를 만족하도록 암호·인증·운영 통제를 결합해야 한다.([RFC 3552](https://www.rfc-editor.org/rfc/rfc3552))
- 암호 원리(키 교환, AEAD, 서명)와 종단점 인증(PKI, 이름 검증)은 전송 보안(TLS)의 기반이다.([RFC 8446](https://www.rfc-editor.org/rfc/rfc8446))([RFC 5280](https://www.rfc-editor.org/rfc/rfc5280))([RFC 6125](https://www.rfc-editor.org/rfc/rfc6125))
- 이메일 보안은 STARTTLS, SPF/DKIM/DMARC, S/MIME처럼 상이한 보호 층을 함께 운영해야 실효성이 높다.([RFC 3207](https://www.rfc-editor.org/rfc/rfc3207))([RFC 7489](https://www.rfc-editor.org/rfc/rfc7489))([RFC 8551](https://www.rfc-editor.org/rfc/rfc8551))
- IPsec/VPN은 네트워크 계층 보호를 제공하고, 무선·셀룰러 환경에서는 액세스 인증과 종단 암호화를 동시 적용해야 한다.([RFC 4301](https://www.rfc-editor.org/rfc/rfc4301))([RFC 3748](https://www.rfc-editor.org/rfc/rfc3748))
- 운영 보안의 핵심은 방화벽 단일 통제가 아니라 IDS/IPS·로그·플로우를 통한 지속 관측과 대응 루프다.([RFC 4765](https://www.rfc-editor.org/rfc/rfc4765))([RFC 7011](https://www.rfc-editor.org/rfc/rfc7011))

## 참고문헌
- IETF RFC 4949, *Internet Security Glossary, Version 2*. ([링크](https://www.rfc-editor.org/rfc/rfc4949))
- IETF RFC 3552, *Guidelines for Writing RFC Text on Security Considerations*. ([링크](https://www.rfc-editor.org/rfc/rfc3552))
- IETF RFC 7258, *Pervasive Monitoring Is an Attack*. ([링크](https://www.rfc-editor.org/rfc/rfc7258))
- IETF RFC 5116, *An Interface and Algorithms for Authenticated Encryption*. ([링크](https://www.rfc-editor.org/rfc/rfc5116))
- IETF RFC 8017, *PKCS #1: RSA Cryptography Specifications Version 2.2*. ([링크](https://www.rfc-editor.org/rfc/rfc8017))
- IETF RFC 8032, *Edwards-Curve Digital Signature Algorithm (EdDSA)*. ([링크](https://www.rfc-editor.org/rfc/rfc8032))
- IETF RFC 2104, *HMAC: Keyed-Hashing for Message Authentication*. ([링크](https://www.rfc-editor.org/rfc/rfc2104))
- IETF RFC 5280, *Internet X.509 Public Key Infrastructure Certificate and CRL Profile*. ([링크](https://www.rfc-editor.org/rfc/rfc5280))
- IETF RFC 6125, *Service Identity in TLS*. ([링크](https://www.rfc-editor.org/rfc/rfc6125))
- IETF RFC 8446, *The Transport Layer Security (TLS) Protocol Version 1.3*. ([링크](https://www.rfc-editor.org/rfc/rfc8446))
- IETF RFC 9325, *Recommendations for Secure Use of TLS and DTLS*. ([링크](https://www.rfc-editor.org/rfc/rfc9325))
- IETF RFC 3207, *SMTP Service Extension for Secure SMTP over TLS*. ([링크](https://www.rfc-editor.org/rfc/rfc3207))
- IETF RFC 7208, *Sender Policy Framework (SPF)*. ([링크](https://www.rfc-editor.org/rfc/rfc7208))
- IETF RFC 6376, *DomainKeys Identified Mail (DKIM) Signatures*. ([링크](https://www.rfc-editor.org/rfc/rfc6376))
- IETF RFC 7489, *Domain-based Message Authentication, Reporting, and Conformance (DMARC)*. ([링크](https://www.rfc-editor.org/rfc/rfc7489))
- IETF RFC 8551, *S/MIME 4.0 Message Specification*. ([링크](https://www.rfc-editor.org/rfc/rfc8551))
- IETF RFC 8461, *SMTP MTA Strict Transport Security (MTA-STS)*. ([링크](https://www.rfc-editor.org/rfc/rfc8461))
- IETF RFC 4301, *Security Architecture for the Internet Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc4301))
- IETF RFC 4302, *IP Authentication Header*. ([링크](https://www.rfc-editor.org/rfc/rfc4302))
- IETF RFC 4303, *IP Encapsulating Security Payload (ESP)*. ([링크](https://www.rfc-editor.org/rfc/rfc4303))
- IETF RFC 7296, *Internet Key Exchange Protocol Version 2 (IKEv2)*. ([링크](https://www.rfc-editor.org/rfc/rfc7296))
- IETF RFC 3948, *UDP Encapsulation of IPsec ESP Packets*. ([링크](https://www.rfc-editor.org/rfc/rfc3948))
- IETF RFC 3748, *Extensible Authentication Protocol (EAP)*. ([링크](https://www.rfc-editor.org/rfc/rfc3748))
- IETF RFC 5216, *The EAP-TLS Authentication Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc5216))
- IETF RFC 2865, *Remote Authentication Dial In User Service (RADIUS)*. ([링크](https://www.rfc-editor.org/rfc/rfc2865))
- IETF RFC 6733, *Diameter Base Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc6733))
- IETF RFC 6459, *IPv6 in the 3GPP Evolved Packet System (EPS)*. ([링크](https://www.rfc-editor.org/rfc/rfc6459))
- IETF RFC 7066, *IPv6 for 3GPP Cellular Hosts*. ([링크](https://www.rfc-editor.org/rfc/rfc7066))
- IETF RFC 2979, *Behavior of and Requirements for Internet Firewalls*. ([링크](https://www.rfc-editor.org/rfc/rfc2979))
- IETF RFC 4765, *The Intrusion Detection Message Exchange Format (IDMEF) Data Model*. ([링크](https://www.rfc-editor.org/rfc/rfc4765))
- IETF RFC 5424, *The Syslog Protocol*. ([링크](https://www.rfc-editor.org/rfc/rfc5424))
- IETF RFC 7011, *Specification of the IP Flow Information Export (IPFIX) Protocol for the Exchange of Flow Information*. ([링크](https://www.rfc-editor.org/rfc/rfc7011))
- IBM, *What is network security?* ([링크](https://www.ibm.com/think/topics/network-security))
- IBM, *What is email security?* ([링크](https://www.ibm.com/think/topics/email-security))
- IBM, *What is a VPN?* ([링크](https://www.ibm.com/think/topics/vpn))
- IBM, *What is zero trust?* ([링크](https://www.ibm.com/think/topics/zero-trust))
- Cisco, *What is network security?* ([링크](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-security.html))
- Cisco, *What is a firewall?* ([링크](https://www.cisco.com/site/us/en/learn/topics/security/what-is-a-firewall.html))
