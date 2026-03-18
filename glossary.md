# 용어 사전 (Glossary)

> Phase 0 초안. Phase 1~3 진행 중 지속 갱신하며, Phase 3 Step 10에서 최종 확정한다.

## 화면명 정규화 매핑

| 대표 명칭 (Canonical) | 원본 명칭 (Raw) | 출처 서비스 | 비고 |
|---|---|---|---|
| (예시) 홈 | Home, MainScreen, home_view, 메인 | mobile-home, iptv-stb-app | |
| (예시) 로그인 | Login, SignIn, login_page, 로그인화면 | mobile-home | |

## 화면 유형 코드

| 코드 | 유형 | 설명 |
|---|---|---|
| SCR | 일반 화면 | 전체 화면 페이지 |
| POP | 팝업 | 오버레이 팝업 |
| MOD | 모달 | 모달 다이얼로그 |
| BTM | 바텀시트 | 하단 슬라이드업 |
| TAB | 탭 | 탭 내 콘텐츠 영역 |

## 사업 도메인 용어

| 용어 | 정의 | 영문 | 비고 |
|---|---|---|---|
| STB | 셋톱박스. IPTV 서비스를 수신하는 단말 장치 | Set-Top Box | 저사양/고사양 구분 |
| PO | 사업부 서비스 기획 담당자 | Product Owner | |
| PM | 개발부서 프로젝트 관리자 | Project Manager | |
| CR | 변경 요청 | Change Request | |
| NFR | 비기능 요구사항 | Non-Functional Requirement | |
| PII | 개인식별정보 | Personally Identifiable Information | |
| SLA | 서비스 수준 협약 | Service Level Agreement | |

## 기술 용어

| 용어 | 정의 | ⚠️ 미적용 시 위험 | 비고 |
|---|---|---|---|
| C4 Model | 소프트웨어 아키텍처 시각화 모델 (Context, Container, Component, Code) | — (문서화 도구) | |
| ERD | 엔티티 관계 다이어그램 | ERD 없으면 DB 구조 파악 불가, 테이블 간 관계 오해로 데이터 정합성 문제 | Entity Relationship Diagram |
| DFD | 데이터 흐름도 | DFD 없으면 개인정보가 어디로 흐르는지 추적 불가 | Data Flow Diagram |
| OOM | 메모리 부족 | OOM 발생 시 앱/서비스가 강제 종료되어 사용자 서비스 중단 | Out Of Memory |
| SRI | 서브리소스 무결성. 외부 스크립트가 변조되지 않았는지 해시값으로 검증하는 보안 기법 | SRI 미적용 시 외부 CDN 해킹으로 악성 스크립트가 사용자에게 전달될 위험 | Subresource Integrity |
| SPOF | 단일 장애점. 이 지점이 고장나면 전체 시스템이 멈추는 구성 요소 | SPOF 존재 시 해당 구성 요소 장애 = 전체 서비스 장애 | Single Point of Failure |
| DLQ | 처리 실패한 메시지를 별도 보관하는 큐. 재처리·원인 분석에 사용 | DLQ 미설정 시 실패 메시지가 유실되어 데이터 누락·정합성 깨짐 | Dead Letter Queue |
| Circuit Breaker | 외부 시스템 장애 시 호출을 차단하여 장애 전파를 방지하는 패턴 | 미적용 시 외부 장애가 우리 서비스까지 연쇄 장애로 확산 | |
| Bulkhead | 서비스 간 리소스를 격리하여 한 서비스 장애가 다른 서비스에 영향을 주지 않도록 하는 패턴 | 미적용 시 한 기능의 과부하가 전체 서비스 리소스를 고갈시킴 | 선박 격벽에서 유래 |
| Fallback | 주 기능 실패 시 대체 동작을 수행하는 메커니즘 | 미적용 시 장애 발생 = 사용자에게 에러 화면만 노출, 대안 없음 | |
| Retry | 일시적 오류 발생 시 자동으로 재시도하는 패턴 | 미적용 시 일시적 네트워크 오류에도 즉시 실패 처리됨 | |
| Idempotency | 멱등성. 동일 요청을 여러 번 보내도 결과가 같은 성질 | 미적용 시 재시도·중복 요청으로 결제 이중 처리 등 심각한 데이터 오류 | |
| mTLS | 서버와 클라이언트가 서로의 인증서를 검증하는 양방향 TLS 인증 | 미적용 시 서버 간 통신에서 위장 서버가 끼어들 수 있음 | Mutual TLS |
| gRPC | Google이 개발한 고성능 원격 프로시저 호출 프레임워크. HTTP/2 + Protocol Buffers 기반 | — (기술 선택지) | |
| Feign | 선언적 HTTP 클라이언트. 인터페이스 정의만으로 REST API 호출 코드를 자동 생성 | — (기술 선택지) | Spring Cloud OpenFeign |
| ORM | 객체-관계 매핑. 코드의 객체와 DB 테이블을 자동 변환하는 기술 | — (기술 선택지) | Object-Relational Mapping |
| CVE | 공개된 보안 취약점에 부여되는 고유 식별 번호 | CVE가 있는 라이브러리 방치 시 알려진 공격 방법으로 해킹 가능 | Common Vulnerabilities and Exposures |
| EOL | 지원 종료. 더 이상 보안 패치·업데이트가 제공되지 않는 상태 | EOL 라이브러리 사용 시 새 취약점 발견돼도 패치 불가 | End of Life |
| CORS | 브라우저가 다른 도메인의 리소스 요청을 허용/차단하는 보안 정책 | CORS 설정 오류 시 악성 사이트에서 우리 API를 무단 호출 가능 | Cross-Origin Resource Sharing |
| CSRF | 사용자가 의도하지 않은 요청을 위조하여 서버에 보내는 공격 기법 | CSRF 방어 미적용 시 사용자가 모르게 결제·설정 변경 등이 실행됨 | Cross-Site Request Forgery |
| XSS | 웹 페이지에 악성 스크립트를 삽입하여 사용자 정보를 탈취하는 공격 기법 | XSS 방어 미적용 시 사용자 세션·개인정보가 공격자에게 유출 | Cross-Site Scripting |
| JWT | JSON 형식의 토큰으로 사용자 인증 정보를 전달하는 표준 | JWT 검증 미흡 시 토큰 위조로 타인 계정 접근 가능 | JSON Web Token |
| OAuth | 제3자 앱에 비밀번호 없이 제한된 접근 권한을 부여하는 인증 프로토콜 | OAuth 미적용 시 비밀번호를 직접 공유해야 하여 유출 위험 증가 | |
| SSO | 한 번 로그인으로 여러 시스템에 접근할 수 있는 통합 인증 방식 | — (편의 기능) | Single Sign-On |
| ProGuard | Android 앱의 코드를 난독화·최적화하여 리버스 엔지니어링을 어렵게 하는 도구 | 미적용 시 앱 코드가 그대로 노출되어 API 키·비즈니스 로직 유출 | |
| R8 | ProGuard의 후속 도구. Android 빌드 시 코드 축소·난독화를 수행 | ProGuard와 동일 위험 | Google 제공 |
| APNs | Apple 기기에 푸시 알림을 전달하는 Apple 서비스 | — (플랫폼 서비스) | Apple Push Notification service |
| FCM | Android/iOS/웹에 푸시 알림을 전달하는 Google 서비스 | — (플랫폼 서비스) | Firebase Cloud Messaging |
| IaC | 인프라를 코드로 정의·관리하는 방식. 수동 서버 설정 대신 코드로 자동화 | IaC 미적용 시 서버 설정이 문서화되지 않아 장애 복구·환경 재현 불가 | Infrastructure as Code |
| Helm | Kubernetes 애플리케이션의 패키지 관리 도구. 배포 설정을 템플릿화 | — (기술 선택지) | |
| Terraform | 클라우드 인프라를 코드로 선언·프로비저닝하는 IaC 도구 | — (기술 선택지) | HashiCorp |
| Kustomize | Kubernetes YAML 설정을 환경별로 커스터마이징하는 도구 | — (기술 선택지) | |
| MSA | 하나의 큰 애플리케이션을 독립적인 작은 서비스들로 분리하는 아키텍처 | — (아키텍처 패턴) | Microservice Architecture |
| API Gateway | 모든 API 요청의 단일 진입점. 인증, 라우팅, 속도 제한 등을 처리 | API Gateway 없으면 각 서비스가 개별로 인증·보안을 구현해야 하여 일관성 저하 | |
| CDN | 전 세계에 분산된 서버로 콘텐츠를 빠르게 전달하는 네트워크 | CDN 미사용 시 원본 서버에 트래픽 집중, 응답 지연·서버 과부하 | Content Delivery Network |
| CI/CD | 코드 변경을 자동으로 빌드·테스트·배포하는 파이프라인 | CI/CD 미적용 시 수동 배포로 인한 휴먼 에러, 배포 지연 | Continuous Integration / Continuous Delivery |
| TPS | 초당 처리 건수. 시스템 성능을 측정하는 지표 | — (측정 지표) | Transactions Per Second |
| APM | 애플리케이션 성능을 실시간 모니터링하는 도구/서비스 | APM 미적용 시 성능 저하·장애 원인을 실시간 파악 불가 | Application Performance Monitoring |
| DRM | 디지털 콘텐츠의 불법 복제를 방지하는 기술적 보호 조치 | DRM 미적용 시 유료 콘텐츠가 무단 복제·유포됨 | Digital Rights Management |
| CAS | 방송 콘텐츠의 수신 권한을 제어하는 시스템 (IPTV/케이블) | CAS 미적용 시 미가입자도 유료 채널 시청 가능 | Conditional Access System |
| WebSocket | 서버와 클라이언트 간 양방향 실시간 통신을 지원하는 프로토콜 | — (기술 선택지) | |
| MQTT | IoT 기기 간 경량 메시지 전달 프로토콜 | — (기술 선택지) | Message Queuing Telemetry Transport |
