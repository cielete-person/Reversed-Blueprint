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

### 2-4. 이벤트/메시지 흐름 & E2E Call Flow View
- 서비스 간 비동기 메시지 발행-구독 관계 시각화
- 이벤트 토픽/큐별 스키마, 처리 순서, 재처리 정책
- 동기 API + 비동기 이벤트 통합 전체 흐름도
- 이벤트 스토밍 결과물 (도메인 이벤트 → 커맨드 → 애그리거트)
- **Dead Code 제외**: Step 1b에서 Dead 판정된 이벤트 핸들러/리스너는 흐름도에서 제외하라. 조건부 Dead(⚠️)는 점선으로 구분 표기
- **코드 없는 구성 요소**: `<<no-source>>`, `<<console>>` 노드가 이벤트를 발행/구독하는 경우, 해당 연결선에 `[인터뷰]` 태그를 표기하라

#### 주요 Use Case별 E2E Call Flow View
> Step 04(4-2)에서 추출한 E2E Call Flow 원시 데이터를 기반으로, 주요 Use Case의 전체 구간 시퀀스 다이어그램을 생성한다.

- **대상 Use Case 선정**: 가입, 결제, 인증, 해지 등 핵심 B2C Use Case + 서비스 특화 주요 UC (최소 5개 이상)
- **E2E 시퀀스 다이어그램 작성** (Mermaid sequenceDiagram):
  - 전체 구간: 앱(프론트엔드) → API Gateway → 백엔드 서비스 → 외부 시스템 → DB → 응답 → 앱 UI 갱신
  - 정상 흐름(Happy Path) + 에러/fallback 분기 경로를 하나의 다이어그램에 `alt/else` 블록으로 표현
  - 동기 호출(실선 화살표)과 비동기 이벤트(점선 화살표)를 구분 표기
  - 각 구간에 프로토콜(REST/gRPC/MQ/DB), 인증 방식, 타임아웃 설정을 주석으로 기재
- **멀티 Repo 앱↔서버 교차 매핑**:
  - 앱 코드의 API 호출부(Retrofit/Alamofire/fetch 등)와 서버 코드의 Controller 엔드포인트를 교차 매핑
  - 앱 Repo와 서버 Repo가 분리된 경우, 양쪽 소스 위치를 병기하여 Call Flow 단절 없이 연결
  - 앱→서버 호출 시 요청/응답 DTO 일치 여부 확인 (불일치 시 ⚠️ 표기)
- **에러 전파 경로 시각화**:
  - 외부 시스템 장애 → 서비스 fallback → 앱 에러 화면까지의 에러 전파 경로
  - Circuit Breaker 발동 시 대체 흐름
  - 타임아웃 발생 시 각 구간별 처리 방식
- **Use Case ↔ Call Flow 교차 매핑 테이블**:
  | Use Case ID | Use Case명 | 관련 Call Flow | 동기/비동기 | 주요 외부 연동 |
  |---|---|---|---|---|
  | UC-{서비스}-001 | 상품가입 | CF-001, CF-002 | 동기+비동기 | 과금, CRM |
- 산출물: `services/{서비스명}/docs/views/event-flow/` 하위에 `e2e-call-flow-{UC-ID}.md` 파일로 생성
- → 대상: PM, 개발자, 품질센터

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

### 2-13. Function–External Stack Call Flow View (기능-외부 스택 호출 관계)

> 개발 PM이 주요 비즈니스 Function과 공통 모듈, SDK, Library, 서버 간 통신, OS API 등 외부 스택 간의 호출 관계를 여러 관점에서 파악할 수 있도록 다중 관점 Call Flow를 생성한다.

#### 관점 A: Function → 공통 모듈/사내 라이브러리 호출 맵
- 주요 비즈니스 Function(Service 클래스의 핵심 메서드)이 호출하는 공통 모듈(util, common, core 등)을 매핑하라
- Step 1c(`01c-common-modules/`) 결과를 기반으로, Function별 공통 모듈 의존 관계를 시각화
- Mermaid flowchart로 작성: `비즈니스 Function` → `공통 모듈` → `사내 라이브러리`
- 각 연결선에 호출 빈도(High/Medium/Low)와 영향 범위 등급(🔴/🟡/🟢) 표기
- 공통 모듈 변경 시 영향받는 비즈니스 Function 역추적 테이블:
  | 공통 모듈 | 영향 등급 | 영향받는 Function | 영향받는 Use Case |
  |---|---|---|---|
  | CommonAuthUtil | 🔴 High | login(), verifyToken(), refreshSession() | UC-001, UC-004 |
- → PM 활용: 공통 모듈 수정 시 영향 범위 즉시 파악, 변경 리스크 사전 판단

#### 관점 B: Function → 외부 SDK/Library 호출 맵
- 주요 비즈니스 Function이 직접 호출하는 외부 SDK 및 3rd-party Library를 매핑하라
- 대상: 결제 SDK, DRM SDK, 푸시 SDK, 분석 SDK, 인증 SDK, 앱 쉴딩 SDK, 광고 SDK 등
- Mermaid flowchart로 작성: `비즈니스 Function` → `SDK/Library` → `외부 서비스(API)`
- 각 SDK/Library에 아래 메타정보를 주석으로 기재:
  - 소스 접근 가능 여부: `<<binary SDK>>` / 소스 포함
  - 버전, EOL 여부, 알려진 CVE
  - 대체 가능 여부 (벤더 락인 수준)
- SDK 장애 시 영향받는 Function과 사용자 시나리오를 역추적:
  | SDK/Library | 장애 시 영향 Function | 영향 Use Case | Fallback 유무 |
  |---|---|---|---|
  | 결제 SDK v3.2 | processPayment(), refund() | UC-020 요금납부 | ⚠️ 부분 (재시도만) |
  | DRM SDK v2.1 `<<binary SDK>>` | playContent(), validateLicense() | UC-030 VOD 재생 | ❌ 없음 |
- → PM 활용: SDK 업데이트/교체 시 영향 범위 파악, 벤더 리스크 관리

#### 관점 C: Function → 서버 간(Inter-Service) 호출 맵
- 주요 비즈니스 Function이 다른 서비스를 호출하는 관계를 매핑하라 (Feign, RestTemplate, gRPC, MQ 등)
- Mermaid flowchart로 작성: `Service A.function()` → `[프로토콜]` → `Service B.endpoint()`
- 동기 호출(실선)과 비동기 호출(점선)을 구분하라
- 각 연결선에 프로토콜, 타임아웃, Circuit Breaker 설정, 인증 방식을 주석으로 기재
- 서비스 간 호출 깊이(Depth) 표기: 1-hop, 2-hop, 3-hop 이상은 ⚠️ 경고
- 순환 호출(Circular Dependency) 패턴은 🔴 표기
- 서비스 간 호출 매트릭스 (히트맵 형태):
  | 호출자 \ 피호출자 | Auth Service | Payment Service | User Service | Notification |
  |---|---|---|---|---|
  | API Gateway | ●●● | ●● | ●●● | ● |
  | Order Service | ●● | ●●● | ● | ●● |
  | Batch Service | ● | ●● | — | ●●● |
  (●●● = 고빈도, ●● = 중빈도, ● = 저빈도, — = 호출 없음)
- → PM 활용: 서비스 간 결합도 파악, 장애 전파 경로 예측, MSA 분리/통합 판단 근거

#### 관점 D: Function → OS/Platform API 호출 맵
- 주요 비즈니스 Function이 OS 레벨 API를 직접 호출하는 관계를 매핑하라
- 대상 OS API: Keystore/Keychain(보안 저장), 생체인증(BiometricPrompt/LAContext), 카메라, 위치(GPS), 파일 시스템, 네트워크 상태, 푸시 토큰(FCM/APNs), 앱 권한(Permission) 등
- 플랫폼별 분기 표기: 동일 Function이 iOS/Android에서 다른 OS API를 호출하는 경우 양쪽 모두 표기
  ```
  authenticateUser()
  ├── [Android] BiometricPrompt → KeyStore
  └── [iOS] LAContext → Keychain
  ```
- STB 전용: 튜너 API, HDMI-CEC, CAS 모듈, 리모컨 키 이벤트 등 하드웨어 API 호출 매핑
- OS API 변경(minSdk 상향, Deprecated API) 시 영향받는 Function 역추적 테이블:
  | OS API | 플랫폼 | Deprecated 여부 | 영향 Function | 대체 API |
  |---|---|---|---|---|
  | FingerprintManager | Android | ⚠️ Deprecated (API 28+) | authenticateUser() | BiometricPrompt |
  | UIWebView | iOS | 🔴 Removed (iOS 12+) | loadWebContent() | WKWebView |
- → PM 활용: OS 버전 업그레이드 시 영향 범위 파악, minSdk 상향 판단 근거

#### 관점 E: 통합 의존성 매트릭스 (Function × External Stack)
- 위 A~D 관점을 종합하여, 주요 비즈니스 Function별 외부 의존성을 단일 매트릭스로 정리하라
- 매트릭스 형태:
  | Function | 공통 모듈 | SDK/Library | 서버 간 호출 | OS API | 총 의존 수 | 위험도 |
  |---|---|---|---|---|---|---|
  | processPayment() | CommonCrypto, CommonAuth | 결제SDK, 분석SDK | PaymentSvc, AuthSvc | Keystore | 6 | 🔴 |
  | playContent() | CommonDRM | DRM SDK `<<binary>>` | ContentSvc, LicenseSvc | MediaCodec | 5 | 🔴 |
  | login() | CommonAuth | — | AuthSvc | BiometricPrompt | 3 | 🟡 |
- 위험도 산정 기준:
  - 🔴 High: 외부 의존 5개 이상 또는 `<<binary SDK>>` 포함 또는 순환 호출 존재
  - 🟡 Medium: 외부 의존 3~4개
  - 🟢 Low: 외부 의존 1~2개
- 의존성 변경 시 영향 시뮬레이션: 특정 외부 스택(SDK, 서비스, OS API)이 변경/장애 시 영향받는 Function 목록을 역추적할 수 있는 참조 문서
- → PM 활용: 기능별 외부 의존도 한눈에 파악, 변경/장애 영향 범위 즉시 판단, 리팩토링 우선순위 결정

#### Function–External Stack View 산출물 규칙
- 각 관점(A~E)별 독립 문서로 작성하되, 관점 E(통합 매트릭스)에서 A~D를 교차 참조
- 주요 Function 선정 기준: Use Case Tree의 핵심 UC에 매핑된 Service 클래스의 public 메서드 (최소 10개 이상)
- 모든 외부 스택 노드에 확인 상태(✅/⚠️/🔍) 표기
- `<<binary SDK>>`, `<<no-source>>` 등 코드 없는 구성 요소는 점선 테두리로 구분
- Mermaid flowchart + Markdown 테이블 병행 사용
- → 대상: PM, 개발자

## 산출물

`services/{서비스명}/docs/views/` 에 아래 구조로 생성:
- `system-context/` — C4 Level 1-2 다이어그램 및 설명
- `component-code/` — C4 Level 3-4, 시퀀스, 상태 전이 다이어그램
- `business-rules/` — 비즈니스 규칙 카탈로그, 에러 처리 패턴 맵, Use Case 카탈로그/시나리오
- `event-flow/` — 이벤트 흐름도, 이벤트 스토밍 결과, E2E Call Flow 시퀀스 다이어그램 (Use Case별)
- `data/` — ERD, DFD
- `ui-ux/` — 화면 카탈로그, 화면 흐름도, 인터랙션 맵, 검증 규칙, 접근성
- `security/` — 보안 View 전체 (인증, PII, 암호화, API 보안, 감사 로그, 하드닝 등)
- `quality/` — 품질 View 전체 (테스트, 변경 빈도, 의존성, 동시성, 정합성, 성능 등)
- `stb-resources/` — STB 리소스 관리 View
- `resilience-dr/` — 장애 복원력 및 DR View (장애 유형, SPOF, 격리, DR 아키텍처) `[CDR 11장]`
- `ai-governance/` — AI 거버넌스 View (활용 맵, 모델 인벤토리, Fail-safe, 의사결정 흐름) `[CDR 12장]`
- `layer-stack/` — Layer Stack View (Client/Middleware/OS/Firmware/Network/Server/Infra 계층 다이어그램)
- `function-stack-callflow/` — Function–External Stack Call Flow View (관점 A~E: 공통 모듈, SDK/Library, 서버 간, OS API 호출 맵 및 통합 매트릭스)
- `stakeholder-summary/` — Stakeholder별 맞춤 요약 문서

## 다이어그램 작성 규칙
- C4 다이어그램: Structurizr DSL 또는 Mermaid C4 확장 사용
- 시퀀스 다이어그램: Mermaid `sequenceDiagram` 사용
- 상태 전이: Mermaid `stateDiagram-v2` 사용
- ERD: Mermaid `erDiagram` 사용
- 기타 흐름도: Mermaid `flowchart` 사용

## 🔧 작업자 수작업 보충 항목

> 이 Step에서 KIRO 자동 추출만으로는 완성할 수 없는 항목입니다.
> 산출물 생성 후 아래 항목을 작업자가 직접 보충해 주세요.
> 상세: [manual-tasks.md](../project/manual-tasks.md)

| 항목 ID | 항목명 | 유형 | 해야 할 일 |
|---|---|---|---|
| P2-1 | Stakeholder 리뷰 체크포인트 | `[확정]` | 각 View별 Stakeholder(PO/PM/개발자/UX/보안/품질)가 산출물을 리뷰하고 누락/오류 확인 |
| P2-2 | STB 하드웨어 스펙 수집 | `[인터뷰]` | STB 리소스 관리 View(2-9) 작성 시 기종별 실제 하드웨어 스펙 보충 필요 |
| P2-3 | 운영 환경 정보 수집 | `[인터뷰]` | 배포 토폴로지 View 작성 시 실제 인스턴스 수, LB/CDN 구성 보충 필요 |
| P1-3 | 분산 추적 데이터 수집 | `[런타임]` | E2E Call Flow View 작성 시 실제 런타임 호출 그래프와 비교 검증 필요 |

## 완료 기준
- 13개 View 섹션(2-1 ~ 2-13) 모두 문서화됨
- 각 View에 대상 Stakeholder가 명시됨
- 모든 다이어그램이 Mermaid 또는 Structurizr로 렌더링 가능
- Phase 1 추출 결과의 확인 상태(✅/⚠️/❌/🔍)가 View에 반영됨
- 코드 없는 구성 요소가 스테레오타입(`<<binary SDK>>`, `<<no-source>>`, `<<console>>`, `<<external-db>>`, `<<external>>`)으로 표기됨
- Dead Code(Step 1b 판정)가 다이어그램에서 제외 또는 구분 표기됨
- 공통 모듈(Step 1c 결과)이 2-2 컴포넌트 View에 반영됨
- 주요 Use Case(최소 5개)의 E2E Call Flow 시퀀스 다이어그램이 앱→서버→외부 전체 구간으로 작성됨
- E2E Call Flow에 정상 흐름 + 에러/fallback 분기 경로가 포함됨
- 멀티 Repo 서비스에서 앱↔서버 간 Call Flow 교차 매핑이 완료됨
- Use Case ↔ Call Flow 교차 매핑 테이블이 작성됨
- Layer Stack View(2-12)에 7개 Layer가 모두 식별되고, 각 항목에 추정 근거와 확인 상태가 표기됨
- Function–External Stack View(2-13)에 5개 관점(A~E) 모두 작성됨
- 주요 Function(최소 10개)별 외부 의존성 통합 매트릭스가 위험도 등급과 함께 작성됨
