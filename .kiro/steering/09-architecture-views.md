---
inclusion: manual
---

# Step 9: 아키텍처 View 생성 (Phase 2)

> 참조: #[[file:project/extraction-checklist.md]] — Phase 2 전체 (2-1 ~ 2-9)

## 목표

Phase 1(Step 01~08)에서 추출한 결과를 기반으로, Stakeholder별 아키텍처 View 문서와 C4 다이어그램을 생성한다.

## 선행 조건

Step 01~08의 산출물이 `services/{서비스명}/docs/extraction/` 하위에 존재해야 한다.
Step 1b(Dead Code), Step 1c(공통 모듈 그룹핑) 산출물도 포함.

## 작업 절차

### 2-1. 시스템 컨텍스트 & 컨테이너 View (C4 Level 1-2)
- 전체 시스템 컨텍스트 다이어그램 작성 (외부 연동 시스템 인벤토리 기반)
- 서비스별 컨테이너 다이어그램 (앱, API, DB, 메시지큐 등)
- 외부 연동 인터페이스별 시퀀스 다이어그램
- 장애 시 영향 범위 식별
- → 대상: PO, PM

#### 코드 없는 구성 요소 표기 규칙
다이어그램에 아래 스테레오타입을 사용하여 코드 없는 구성 요소를 명시적으로 표기하라:

| 스테레오타입 | 대상 | 다이어그램 표기 예시 |
|---|---|---|
| `<<binary SDK>>` | 소스 없이 바이너리(.aar, .jar, .framework)로만 제공되는 SDK | `[결제 SDK <<binary SDK>>]` |
| `<<no-source>>` | 소스코드 접근 불가 서버/서비스 (타팀 운영, 외부 벤더 등) | `[인증 서버 <<no-source>>]` |
| `<<console>>` | 관리 콘솔/어드민 포탈에서 코딩(설정, 스크립트)으로 동작하는 구성 요소 | `[푸시 콘솔 <<console>>]` |
| `<<external-db>>` | 외부 연동 DBMS (직접 접근 또는 DB Link) | `[고객 DB <<external-db>>]` |
| `<<external>>` | 기존 외부 시스템 (3rd-party API 등) | `[PG사 API <<external>>]` |

- 스테레오타입이 붙은 노드는 다이어그램에서 점선 테두리(`style ... stroke-dasharray: 5 5`)로 구분하라
- 각 노드에 `[KIRO]` 또는 `[인터뷰]` 태그를 병기하여 분석 가능 여부를 표시하라
  - `[KIRO]`: 바이너리 API 시그니처, 설정 파일 등으로 부분 분석 가능
  - `[인터뷰]`: 코드 분석 불가, 담당자 인터뷰 필수

#### Dead Code 제외/구분 표기 규칙
- Step 1b(`01b-dead-code/`) 산출물에서 Dead 판정된 코드/모듈/API는 다이어그램에서 **제외**하라
- 단, Dead 판정이 `⚠️ 일부확인`(조건부 Dead)인 경우, 다이어그램에 포함하되 회색 배경 + 취소선 텍스트(`~~모듈명~~`)로 구분 표기하라
- 다이어그램 하단에 "Dead Code 제외 목록" 테이블을 추가하라:
  ```markdown
  | 제외 항목 | Dead 판정 근거 (Step 1b 참조) | 제외/구분 |
  |---|---|---|
  | LegacyPaymentModule | 호출처 0건, 12개월 미변경 | 제외 |
  | OldCacheManager | 1건 호출 (테스트 코드) | 구분 표기 (⚠️) |
  ```

### 2-2. 컴포넌트 & 코드 View (C4 Level 3-4)
- 주요 서비스의 컴포넌트 다이어그램
- 모듈 의존성 그래프 (내부 패키지 간)
  - **Step 1c 공통 모듈 결과 반영 필수**: `01c-common-modules/dependency-direction.md`의 의존 방향 다이어그램을 기반으로 작성
  - 공통 모듈(util, common, core 등)을 별도 컴포넌트로 표기하고, 참조 횟수·영향 범위 등급(🔴/🟡/🟢)을 주석으로 기재
  - 역의존(⚠️ 아키텍처 위반 후보)이 있는 경우 다이어그램에 경고 표기
  - 사내 공통 라이브러리(`01c-common-modules/external-common-libraries.md`)를 외부 의존성으로 표기
- 핵심 비즈니스 로직 시퀀스 다이어그램
- 상태 전이 다이어그램 (정상 + 예외/이상 전이 경로 구분)
- → 대상: PM, 개발자

### 2-3. 비즈니스 규칙 & 에러 처리 & Use Case View
- 비즈니스 규칙 카탈로그 완성 (규칙 ID, 규칙명, 설명, 적용 서비스, 소스 위치, 확인 상태)
- 규칙 간 의존 관계 매핑
- 에러/예외 처리 패턴 맵 완성 (에러 코드 체계, 예외 처리 흐름도, fallback 시나리오)
- Use Case 다이어그램 & 시나리오 문서 완성:
  - B2C 공통 Use Case 카탈로그 (가입/해지/변경/조회/결제/인증/고객센터/알림/계정)
  - 서비스 특화 Use Case 카탈로그
  - 주요 Use Case별 시퀀스 다이어그램 (Mermaid sequenceDiagram)
  - Use Case ↔ API ↔ 화면 ↔ 비즈니스 규칙 교차 매핑 테이블
  - Use Case별 정상/대안/예외 시나리오 문서
- → 대상: PM, 개발자, 품질센터, PO

### 2-4. 이벤트/메시지 흐름 View
- 서비스 간 비동기 메시지 발행-구독 관계 시각화
- 이벤트 토픽/큐별 스키마, 처리 순서, 재처리 정책
- 동기 API + 비동기 이벤트 통합 전체 흐름도
- 이벤트 스토밍 결과물 (도메인 이벤트 → 커맨드 → 애그리거트)
- **Dead Code 제외**: Step 1b에서 Dead 판정된 이벤트 핸들러/리스너는 흐름도에서 제외하라. 조건부 Dead(⚠️)는 점선으로 구분 표기
- **코드 없는 구성 요소**: `<<no-source>>`, `<<console>>` 노드가 이벤트를 발행/구독하는 경우, 해당 연결선에 `[인터뷰]` 태그를 표기하라
- → 대상: PM, 개발자

### 2-5. 데이터 View
- ERD (논리/물리)
- 데이터 흐름도 (DFD) — 개인정보 포함 데이터 식별
- **외부 DBMS 표기**: DB Link, 직접 접근 등으로 연동되는 외부 DBMS는 ERD/DFD에 `<<external-db>>` 스테레오타입으로 표기하라. 스키마 접근 불가 시 `[인터뷰]` 태그 병기
- → 대상: 개발자, 보안센터

### 2-6. UI/UX View
- 화면 카탈로그 완성 (화면 ID, 화면명, 캡처, 영역 구분)
- 크로스 플랫폼 화면 매핑표 (iOS↔Android↔STB 동일 화면 매핑, 플랫폼 전용 화면 ⚠️ 표기)
- 화면 흐름도 (Screen Flow) — 딥링크/푸시 외부 진입 경로 포함, 플랫폼별 차이 표기
- 유저 인터랙션 패턴 맵 (조작 → 반응 매핑표)
- 입력 유효성 검증 규칙 카탈로그 (프론트/백엔드 이중 구조)
- 로딩/대기 상태 UX 패턴 맵
- 딥링크/푸시 알림 진입 경로 맵 (iOS/Android 플랫폼별 설정 차이 포함)
- 다국어/접근성(A11y) 현황 맵
- A/B 테스트 및 피처 플래그 현황 맵
- 프론트엔드 컴포넌트 트리
- 화면 ↔ API ↔ 비즈니스 기능 매핑 테이블
- 화면별 권한/역할 접근 매트릭스
- → 대상: UX센터, PO

### 2-7. 보안 View
- 인증/인가 아키텍처 (OAuth, SSO, 토큰 흐름)
- 세션/토큰 관리 아키텍처 (생성/만료/갱신, JWT 라이프사이클, 동시 로그인 제어)
- 개인정보 처리 현황 맵 (라이프사이클, 법적 요구사항 대비, 암호화, 동의 관리, 접근 권한)
- 입력값 보안 검증 아키텍처 (XSS/SQLi/CSRF/Path Traversal 방어, 보안 필터 체인)
- API 보안 현황 매트릭스 (인증 방식, Rate Limit, IP 제한, 민감 데이터, Gateway/WAF)
- 감사 로그 아키텍처 (대상 행위, 흐름도, 법적 의무 대비, 위변조 방지)
- 데이터 암호화 아키텍처 (At Rest/In Transit, 알고리즘, 키 관리, TLS)
- 크리덴셜/시크릿 관리 현황 (위험도 등급: 🔴/🟡/🟢, 키 로테이션, 관리 도구)
- 보안 하드닝 체크리스트 (HTTP 헤더, CORS, 디버그 잔존, 에러 노출, 쿠키)
- 서드파티 스크립트 보안 현황 (인벤토리, SRI, 개인정보 유출 위험)
- 모바일 앱 쉴딩 현황 아키텍처 (난독화, 위변조 탐지, 루팅/탈옥, 디버깅 방지, 쉴딩 솔루션, 169~181번 대비)
- 정적분석 취약점 리포트
- → 대상: 보안센터

### 2-8. 품질 View
- 테스트 자동화 현황 맵 (테스트 피라미드 시각화)
- 테스트-기능 추적 매트릭스 (Test Gap 시각화)
- 코드 변경 빈도 및 결함 밀집도 맵 (Churn Rate 히트맵)
- 코드 복잡도/중복도 리포트
- 로깅/모니터링 현황 아키텍처
- 의존성 건강도 리포트 (EOL, CVE 심각도별)
- 동시성/스레드 안전성 아키텍처
- 데이터 정합성 아키텍처 (트랜잭션 경계, 분산 트랜잭션, 캐시 정합성)
- 성능 병목 후보 영역 맵 (위험도 등급별)
- 기술 부채 식별 목록
- 테스트 코드 커버리지 현황
- → 대상: 품질센터

### 2-9. STB 하드웨어 리소스 관리 View (IPTV/홈 사업 전용)
- STB 기종별 하드웨어 스펙 프로파일
- 프로세스/메모리 관리 아키텍처 (부팅 시퀀스, 상주 프로세스, OOM 정책)
- 리소스 모니터링 포인트 (임계치, 메모리 릭 패턴)
- 앱/UI 리소스 제약 분석 (렌더링 엔진, 이미지 제한, 동시 로딩 제한)
- 펌웨어/소프트웨어 업데이트 프로세스 (OTA, 롤백)
- → 대상: PM, 개발자, 품질센터

### 2-10. 장애 복원력 및 DR View `[CDR 11장]`
- 장애 유형 분류 (App/DB/네트워크/외부/데이터손상) 및 Failure Mode Catalog
- SPOF 식별 결과 및 완화 방안 `[CDR 3.2.1]`
- 장애 격리 아키텍처 (벌크헤드/큐 분리/리소스 분리) `[CDR 4.2.4]`
- DR 아키텍처 현황 (Active-Active/Standby/Backup)
- 데이터 복제/정합성 리스크 분석
- → 대상: PM, 개발자, 품질센터

### 2-11. AI 거버넌스 View `[CDR 12장]`
- AI 활용 서비스 맵 (AI 사용/미사용 선언, 역할별 분류)
- AI 모델/엔진 인벤토리 (출처, 버전, 변경 관리 방식)
- AI 입출력 통제 현황 (개인정보, 프롬프트 보안)
- AI Fail-safe 아키텍처 (Kill-switch, 수동 전환, Drift 감지)
- AI 의사결정 흐름도 (자동 실행/승인 필요/참고용 분류)
- → 대상: PM, 개발자, 보안센터

### 2-12. Layer Stack View (기술 계층 구조)
- 서비스별 전체 기술 계층(Layer)을 단일 다이어그램으로 시각화
- Phase 1 전체 결과(Step 01~08)를 종합하여 아래 Layer를 추정·식별:

#### Client Layer (앱/프론트엔드)
- 앱 유형별 UI Framework 식별: Jetpack Compose, SwiftUI, XML Layout, React, Vue 등
- 상태 관리 패턴: MVVM, MVI, Redux, MobX 등
- 디자인 시스템: Material, Cupertino, 사내 공통 UI SDK
- 렌더링 방식: CSR, SSR, Hybrid, WebView
- → Step 01(기술 스택), Step 07(UX) 결과 참조

#### Middleware / SDK Layer
- 사내 공통 라이브러리 (Step 1c 결과 참조)
- 외부 SDK: DRM, 푸시, 결제, 분석, 광고, 앱 쉴딩 등
- 프레임워크 미들웨어: Spring Security Filter Chain, Express middleware 등
- → Step 01c(공통 모듈), Step 05(보안) 결과 참조

#### OS / Platform Layer
- 타겟 OS 및 최소 버전: Android 12+, iOS 15+, AOSP(Android TV) 등
- OS 레벨 API 의존: Keystore/Keychain, 생체인증, 카메라, 위치 등
- → Step 01(빌드 파일 minSdk/deploymentTarget) 결과 참조

#### Firmware / Hardware Layer (STB/IoT 전용)
- 보안칩, CAS 모듈, HDMI-CEC, 튜너 등 하드웨어 의존
- 기종별 분기 조건 (저사양/고사양)
- → Step 08(STB 리소스) 결과 참조. 해당 없는 서비스는 "N/A" 표기

#### Network Layer
- 통신 프로토콜: HTTPS, gRPC, WebSocket, MQTT 등
- 네트워크 환경 추정:
  - 사설 IP(10.x, 172.x, 192.168.x) / 내부 도메인(.internal, .corp) → OnPremise 폐쇄망 추정
  - AWS/GCP/Azure SDK, S3/CloudFront URL → Cloud 추정
  - 양쪽 모두 존재 → 하이브리드 추정
  - 환경변수 주입(`${API_HOST}`) → 🔍 인터뷰필요
- VPN/프록시, mTLS, 인증서 pinning 설정
- CDN 사용 여부 및 제공자
- → Step 03(API), Step 05(보안), Step 08(인프라) 결과 참조

#### Server Layer
- SW 아키텍처 패턴: Layered, Hexagonal, Event-Driven, MSA 등
- 서버 프레임워크: Spring Boot, Express, NestJS, Django 등
- API Gateway 유무 및 종류
- 캐시 레이어: Redis, Memcached, Ehcache 등
- 메시지 브로커: Kafka, RabbitMQ 등 (내부 브로커 vs 관리형 서비스 구분)
- → Step 01(코드 구조), Step 03(API/데이터), Step 04(비즈니스 로직) 결과 참조

#### Infra / Deploy Layer
- 배포 환경 추정: k8s manifest → 컨테이너, Dockerfile → Docker, EC2/VM → 전통 배포
- CI/CD 파이프라인: Jenkins(OnPrem 추정) vs GitHub Actions/CodePipeline(Cloud 추정)
- IaC 도구: Terraform, Helm, Ansible 등
- OnPremise vs Cloud vs 하이브리드 최종 판정 (복수 단서 종합)
- → Step 01(CI/CD, IaC), Step 08(배포 토폴로지) 결과 참조

#### Layer Stack 산출물 규칙
- 각 Layer 항목에 추정 근거(소스 파일/설정 파일 경로)와 확인 상태(✅/⚠️/🔍) 표기
- 코드에서 확인 불가한 항목은 🔍 인터뷰필요로 표기하고 추정 근거를 명시
- 하이브리드(OnPrem+Cloud) 구성이 추정되면 ⚠️로 표기하고 양쪽 단서를 모두 기재
- Mermaid block-beta 또는 flowchart로 Layer 다이어그램 작성
- → 대상: PM, 개발자, 인프라팀

## 산출물

`services/{서비스명}/docs/views/` 에 아래 구조로 생성:
- `system-context/` — C4 Level 1-2 다이어그램 및 설명
- `component-code/` — C4 Level 3-4, 시퀀스, 상태 전이 다이어그램
- `business-rules/` — 비즈니스 규칙 카탈로그, 에러 처리 패턴 맵, Use Case 카탈로그/시나리오
- `event-flow/` — 이벤트 흐름도, 이벤트 스토밍 결과
- `data/` — ERD, DFD
- `ui-ux/` — 화면 카탈로그, 화면 흐름도, 인터랙션 맵, 검증 규칙, 접근성
- `security/` — 보안 View 전체 (인증, PII, 암호화, API 보안, 감사 로그, 하드닝 등)
- `quality/` — 품질 View 전체 (테스트, 변경 빈도, 의존성, 동시성, 정합성, 성능 등)
- `stb-resources/` — STB 리소스 관리 View
- `resilience-dr/` — 장애 복원력 및 DR View (장애 유형, SPOF, 격리, DR 아키텍처) `[CDR 11장]`
- `ai-governance/` — AI 거버넌스 View (활용 맵, 모델 인벤토리, Fail-safe, 의사결정 흐름) `[CDR 12장]`
- `layer-stack/` — Layer Stack View (Client/Middleware/OS/Firmware/Network/Server/Infra 계층 다이어그램)
- `stakeholder-summary/` — Stakeholder별 맞춤 요약 문서

## 다이어그램 작성 규칙
- C4 다이어그램: Structurizr DSL 또는 Mermaid C4 확장 사용
- 시퀀스 다이어그램: Mermaid `sequenceDiagram` 사용
- 상태 전이: Mermaid `stateDiagram-v2` 사용
- ERD: Mermaid `erDiagram` 사용
- 기타 흐름도: Mermaid `flowchart` 사용

## 완료 기준
- 12개 View 섹션(2-1 ~ 2-12) 모두 문서화됨
- 각 View에 대상 Stakeholder가 명시됨
- 모든 다이어그램이 Mermaid 또는 Structurizr로 렌더링 가능
- Phase 1 추출 결과의 확인 상태(✅/⚠️/❌/🔍)가 View에 반영됨
- 코드 없는 구성 요소가 스테레오타입(`<<binary SDK>>`, `<<no-source>>`, `<<console>>`, `<<external-db>>`, `<<external>>`)으로 표기됨
- Dead Code(Step 1b 판정)가 다이어그램에서 제외 또는 구분 표기됨
- 공통 모듈(Step 1c 결과)이 2-2 컴포넌트 View에 반영됨
- Layer Stack View(2-12)에 7개 Layer가 모두 식별되고, 각 항목에 추정 근거와 확인 상태가 표기됨
