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

| 용어 | 정의 | 비고 |
|---|---|---|
| C4 Model | 소프트웨어 아키텍처 시각화 모델 (Context, Container, Component, Code) | |
| ERD | 엔티티 관계 다이어그램 | Entity Relationship Diagram |
| DFD | 데이터 흐름도 | Data Flow Diagram |
| OOM | 메모리 부족 | Out Of Memory |
| SRI | 서브리소스 무결성. 외부 스크립트가 변조되지 않았는지 해시값으로 검증하는 보안 기법 | Subresource Integrity |
| SPOF | 단일 장애점. 이 지점이 고장나면 전체 시스템이 멈추는 구성 요소 | Single Point of Failure |
| DLQ | 처리 실패한 메시지를 별도 보관하는 큐. 재처리·원인 분석에 사용 | Dead Letter Queue |
| Circuit Breaker | 외부 시스템 장애 시 호출을 차단하여 장애 전파를 방지하는 패턴 | |
| Bulkhead | 서비스 간 리소스를 격리하여 한 서비스 장애가 다른 서비스에 영향을 주지 않도록 하는 패턴 | 선박 격벽에서 유래 |
| Fallback | 주 기능 실패 시 대체 동작을 수행하는 메커니즘 | |
| Retry | 일시적 오류 발생 시 자동으로 재시도하는 패턴 | |
| Idempotency | 멱등성. 동일 요청을 여러 번 보내도 결과가 같은 성질 | |
| mTLS | 서버와 클라이언트가 서로의 인증서를 검증하는 양방향 TLS 인증 | Mutual TLS |
| gRPC | Google이 개발한 고성능 원격 프로시저 호출 프레임워크. HTTP/2 + Protocol Buffers 기반 | |
| Feign | 선언적 HTTP 클라이언트. 인터페이스 정의만으로 REST API 호출 코드를 자동 생성 | Spring Cloud OpenFeign |
| ORM | 객체-관계 매핑. 코드의 객체와 DB 테이블을 자동 변환하는 기술 | Object-Relational Mapping |
| CVE | 공개된 보안 취약점에 부여되는 고유 식별 번호 | Common Vulnerabilities and Exposures |
| EOL | 지원 종료. 더 이상 보안 패치·업데이트가 제공되지 않는 상태 | End of Life |
| CORS | 브라우저가 다른 도메인의 리소스 요청을 허용/차단하는 보안 정책 | Cross-Origin Resource Sharing |
| CSRF | 사용자가 의도하지 않은 요청을 위조하여 서버에 보내는 공격 기법 | Cross-Site Request Forgery |
| XSS | 웹 페이지에 악성 스크립트를 삽입하여 사용자 정보를 탈취하는 공격 기법 | Cross-Site Scripting |
| JWT | JSON 형식의 토큰으로 사용자 인증 정보를 전달하는 표준 | JSON Web Token |
| OAuth | 제3자 앱에 비밀번호 없이 제한된 접근 권한을 부여하는 인증 프로토콜 | |
| SSO | 한 번 로그인으로 여러 시스템에 접근할 수 있는 통합 인증 방식 | Single Sign-On |
| ProGuard | Android 앱의 코드를 난독화·최적화하여 리버스 엔지니어링을 어렵게 하는 도구 | |
| R8 | ProGuard의 후속 도구. Android 빌드 시 코드 축소·난독화를 수행 | Google 제공 |
| APNs | Apple 기기에 푸시 알림을 전달하는 Apple 서비스 | Apple Push Notification service |
| FCM | Android/iOS/웹에 푸시 알림을 전달하는 Google 서비스 | Firebase Cloud Messaging |
| IaC | 인프라를 코드로 정의·관리하는 방식. 수동 서버 설정 대신 코드로 자동화 | Infrastructure as Code |
| Helm | Kubernetes 애플리케이션의 패키지 관리 도구. 배포 설정을 템플릿화 | |
| Terraform | 클라우드 인프라를 코드로 선언·프로비저닝하는 IaC 도구 | HashiCorp |
| Kustomize | Kubernetes YAML 설정을 환경별로 커스터마이징하는 도구 | |
| MSA | 하나의 큰 애플리케이션을 독립적인 작은 서비스들로 분리하는 아키텍처 | Microservice Architecture |
| API Gateway | 모든 API 요청의 단일 진입점. 인증, 라우팅, 속도 제한 등을 처리 | |
| CDN | 전 세계에 분산된 서버로 콘텐츠를 빠르게 전달하는 네트워크 | Content Delivery Network |
| CI/CD | 코드 변경을 자동으로 빌드·테스트·배포하는 파이프라인 | Continuous Integration / Continuous Delivery |
| TPS | 초당 처리 건수. 시스템 성능을 측정하는 지표 | Transactions Per Second |
| APM | 애플리케이션 성능을 실시간 모니터링하는 도구/서비스 | Application Performance Monitoring |
| DRM | 디지털 콘텐츠의 불법 복제를 방지하는 기술적 보호 조치 | Digital Rights Management |
| CAS | 방송 콘텐츠의 수신 권한을 제어하는 시스템 (IPTV/케이블) | Conditional Access System |
| WebSocket | 서버와 클라이언트 간 양방향 실시간 통신을 지원하는 프로토콜 | |
| MQTT | IoT 기기 간 경량 메시지 전달 프로토콜 | Message Queuing Telemetry Transport |
