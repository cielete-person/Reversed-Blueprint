# CDR-상세 설계

CDR의 목적: 기술 설계 검토가 아니라 '운영 가능한 서비스 설계 검증 - Production Readiness Review

| **구분** | **항목** |  | **주관 Lab** | **주관 팀** | **Link** |
| --- | --- | --- | --- | --- | --- |
| Business / Product Intent Layer
”이 기능/제품은 왜 존재하며, 실패 시 사업 영향은 무엇인가?” | 공통

Server

Native App
Firmware | 서비스 정의 (Servcie Scope)
SLA / SLO
장애,변경시 책임 (RACI)
운영팀 지정 (Service Owner / Ops Owner)
외부 연계 (SaaS, Vendor) 책임 범위
앱 기능 목적, 스토어 정책 영향
디바이스 기능 목적, 리콜/안전 영향 | Consumer서비스개발Lab(이진혁님) |  | [https://lgucorp.atlassian.net/wiki/x/opr8Jw](https://lgucorp.atlassian.net/wiki/x/opr8Jw) |
| End-to-End Interaction Layer
”사용자 행동부터 시스템 반응까지 흐름이 명확한가?” | 공통
Server

Native App
Firmware | End-to-End Call Flow
외부 시스템 연계 흐름
인증/인가 흐름
장애 전파 경로
동기/비동기 경계
UI → OS → App → Server Flow
Sensor → MCU → OS → Network
(Firmware는 물리 이벤트 포함) | ixi Agent개발Lab
(신정호님) |  | [https://lgucorp.atlassian.net/wiki/spaces/ixiOTask/pages/631496637](https://lgucorp.atlassian.net/wiki/spaces/ixiOTask/pages/631496637) |
| Core Architecture Layer
”핵심 로직은 변경,확장,오류에 견딜 수 있는가?” | Server

Native App
Firmware | Application 구조 (Monolith, MSA, Modular)
주요 컴포넌트 역할
복잡한 Flow Chart (비즈니스 로직)
Error Handling 전략
Error Code 체계
Retry, Timeout, Circuit Breaker
MQ(Message Queue)
-Async 처리 필요성 검토
-MQ 사용 사유
-메시지 유실 허용 범위
-Ordering 요구
-Idempotency 전략
App Architecture, State 관리
RTOS 구조, Interrupt 처리
(Firmware는 실시간 제약(Hard/Soft RT) | 디지털플랫폼개발Lab
(방욱재님) |  | [https://lgucorp.atlassian.net/wiki/spaces/Lab2/pages/673125319](https://lgucorp.atlassian.net/wiki/spaces/Lab2/pages/673125319) |
| Data / State Management Layer
“상태와 데이터는 안전하고, 일관되게 유지되어, 변경 가능하며, 되돌릴 수 있는가?” | Server

Native App
Firmware | DB Logical, Physical 설계
데이터 정합성 규칙
트랜잭션 경계
데이터 마이그레이션 전략
Rollback 가능성
대용량 처리 전략
Local Storage, Cache, Sync
Flash.EEPROM, Power-loss 대응
(Firmware는 전원 차단시 데이터 보존) | BSS/OSS개발Lab
(김상욱님) |  | [https://lgucorp.atlassian.net/wiki/spaces/BD/pages/666207098](https://lgucorp.atlassian.net/wiki/spaces/BD/pages/666207098) 
 [https://lgucorp.atlassian.net/wiki/spaces/BD/pages/666796943](https://lgucorp.atlassian.net/wiki/spaces/BD/pages/666796943) 
[https://lgucorp.atlassian.net/wiki/spaces/BD/pages/672727585](https://lgucorp.atlassian.net/wiki/spaces/BD/pages/672727585) |
| Platform / Runtime Dependency Layer
”이 설계는 실제 인프라에서 현실적으로 동작하는가?”
”기반 플랫폼 변화에 얼마나 취약한가?” | Server

Native App
Firmware | 서버 아키텍처
네트워크 구성
보안 경계(FW, SG, ACL)
배포 구조
확장성 (Scale Out/In)
iOS/Android 버전, OEM 차이
Chipset, BSP, Bootloader
(Firmware는 벤더 종속성 극대화) | 아키텍처Lab
(곽효신님) |  | [https://lgucorp.atlassian.net/wiki/x/QqIfK](https://lgucorp.atlassian.net/wiki/x/QqIfK) |
| Observability & Measurability Layer
”문제가 생기거나 성과를 물었을 때 우리는 알 수 있는가?”
“문제가 생기면, 우리는 ‘원인’을 알 수 있는가? | 공통
Server

Native App
Firmware | Business KPI 정의
로그/이벤트에 포함될 Business Field
Log 설계 (구조화 로그), Metric 설계, Trace 설계
Alert 기준, 운영 대시보드 구성
Crash Log, User Event
Debug Port, Dump, LED/Signal
(Firmware는 원격 관측 한계) | 아키텍처Lab
(곽효신님) |  | [https://lgucorp.atlassian.net/wiki/x/rhkeK](https://lgucorp.atlassian.net/wiki/x/rhkeK) |
| Security & Compliance Layer
”사고가 나거나 감사가 와도 설명하고 책임질 수 있는가?”
“침해,변조,오용을 막을 수 있는가?” | 공통

Server
Native App
Firmware | 데이터 암호화
감사 로그(Audit Log) 요건
개인정보, 민감정보 분류
법/규제 요구사항
인증/인가 모델(RBAC, ABAC)
Secure Storage, Jailbreak
Secure Boot, Code Signing
(Firmware는 물리 공격 포함) | 클라우드기술Lab
(배은옥님) | CTO보안기술팀(양재훈님) | [https://lgucorp.atlassian.net/wiki/spaces/fhiU4gdJS1vr/pages/666700588](https://lgucorp.atlassian.net/wiki/spaces/fhiU4gdJS1vr/pages/666700588) 
[https://lgucorp.atlassian.net/wiki/spaces/fhiU4gdJS1vr/pages/666766844](https://lgucorp.atlassian.net/wiki/spaces/fhiU4gdJS1vr/pages/666766844) |
| Transition & Change Readiness Layer
”이 설계는 실제 배포,이관, 운영 전환이 가능한가?”
“이 변경은 안전하게 배포,확산,중단 가능한가?” | 공통
서버

Native App
Firmware | 배포 전략
데이터 전환 시나리오
운영 인수인계 항목
장애 대응 Runbook 초안
Rollback 시나리오
Feature Flag 검토
상용 환경 대비 검증이 안된 기능 검토
Rollback 불가시 최후 대응 전략(Fail-safe Plan)
배포에 포함된 기능/컴포넌트 범위 - 배포대상이 아닌 영역
App Store 배포, 심사
OTA, 단계별 Rollout
(App/Firmware는 즉시 배포 불가) | AICC개발Lab
(이진희님) | AICC플랫폼개발팀(윤민아님) | [https://lgucorp.atlassian.net/wiki/spaces/Lab/pages/673124357](https://lgucorp.atlassian.net/wiki/spaces/Lab/pages/673124357) |
| Cost / FinOps & Capacity Layer
”시간이 지날수록 비용과 부담은 어떻게 변하는가?” | Server

Native App
Firmware | 예상 트래픽 기준 비용 모델 - 확장시 동일 아키텍처 유지?
Scale-out시 비용 증가 곡선
비용 책임 주체(FinOps Owner) 
유지보수 인력, OS 대응
AS, 리콜, 현장 대응
(Firmware는 OPEX보다 리스크 비용) | 클라우드기술Lab
(배은옥님) |  |  |
| Dependency & Vendor Risk Layer
”우리가 통제하지 못하는 것까지 고려되었는가?” | 공통

Server
Native App
Firmware | SLA / SLO 존재 여부
장애시 책임 주체
대체 가능성 (Exit Plan)
계약서/약관 설계 검토 여부
외부 API / SaaS / 솔루션 목록
App Store 정책
Chipset Vendor, OEM, 타사 앱
(App/Firmware는 단일 벤더 lock in) | AICC개발Lab
(이진희님) | AICC플랫폼개발팀(윤민아님) | [https://lgucorp.atlassian.net/wiki/spaces/Lab/pages/673092594](https://lgucorp.atlassian.net/wiki/spaces/Lab/pages/673092594) |
| Resilience & Recovery Layer
”이 서비스는 망가져도 살아남는가?” | 공통

Server

Native App
Firmware | 장애 유형 분류 (App / DB / Region)
RTO / RPO 정의
DR 아키텍처
데이터 손상시 복구 전략
DR 테스트 계획 (모의훈련)
Offline Mode, Retry(최대, 최소)
Safe Mode, Watchdog
(Firmware는 Fail-safe가 필수) | 아키텍처Lab
(곽효신님) |  | [https://lgucorp.atlassian.net/wiki/x/HxYiK](https://lgucorp.atlassian.net/wiki/x/HxYiK) |
| AI Usage & Model Governace Layer
”이 시스템에서 AI가 의사결정/추천/자동화를 수행할 때, 우리는 그 결과를 설명하고, 통제하고, 중단할 수 있는가?” |  | AI 사용 여부 선언:현재 사용, 향후 도입, AI미사용선언
Ai적용 범위와 역할:
  -로그 분석 자동화
  -고객 응대 자동 답변
  -이상 탐지
  -점수/등급 산정
모델/엔진 출처
  -내부 모델 /  외부 SaaS
  -버전 고정 여부
  -모델 변경 통제 방식
데이터 입력/출력 통제
  -학습/추론 데이터에 개인정보 포함 여부
  -민감정보 필터링
  -프롬프트/입력값 검증
결과 신뢰성 & 설명 가능성
  -AI판단 근거 설명 가능 여부
  -오류 발생 시 책임 주체
Fail-safe & Human Override
  -AI 결과 무시/차단 가능 여부
  -Feature Flag로 AI 비활성화 가능 여부
  -수동 처리로 즉시 전환 가능 여부
운영/모니터링
  -AI성능 저하 감지 방법
  -Drift 탐지 여부
  -로그/감사 기록
비용/확장 영향
  -호출량 증가 시 비용 곡선
  -AI호출 장애 시 영향 범위 | AI응용기술Lab
(조현철님) |  |  |

[**CDR 통합 checklist v0.5**](CDR%20%ED%86%B5%ED%95%A9%20checklist%20v0%205%203265bd688d9d8022b7c8ee6ab56af9ba.md)

[**CDR 통합 Checklist 용어 정리**](CDR%20%ED%86%B5%ED%95%A9%20Checklist%20%EC%9A%A9%EC%96%B4%20%EC%A0%95%EB%A6%AC%203265bd688d9d80a88eeaf41b459ba1c4.md)