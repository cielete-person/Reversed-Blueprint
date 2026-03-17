---
inclusion: manual
---

# Step 4: 비즈니스 로직 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-4. 비즈니스 로직

## 선행 산출물 참조

- **Step 1b(Dead Code)**: `01b-dead-code/` 산출물에서 Dead 판정(🔴)된 비즈니스 로직, 이벤트 핸들러, 배치 작업은 본 Step에서 제외하라. 조건부 Dead(🟡)는 포함하되 `⚠️ Dead 의심` 표기.
- **Step 1c(공통 모듈)**: `01c-common-modules/` 산출물에서 공통 예외 처리(GlobalExceptionHandler 등), 공통 이벤트 발행/구독 모듈을 식별하여, 비즈니스 로직 분석 시 공통 모듈 기반 처리와 서비스 고유 처리를 구분하라.

## ⚡ 컨텍스트 복원 (새 세션 시작 시)

> `services/{서비스명}/docs/extraction/_context-notes.md`를 먼저 읽어 이전 Step 인사이트를 복원하세요.
> 이 Step 완료 후 `_context-notes.md`의 해당 섹션에 핵심 발견사항을 기록하세요.

## 목표

코드에서 비즈니스 규칙, 에러/예외 처리 패턴, 상태 전이 조건, 이벤트/메시지 흐름을 추출한다.

## 작업 절차

### 1. 비즈니스 규칙 추출
- 코드 내 하드코딩된 비즈니스 규칙을 식별하라 (조건문, 상수, 설정값)
- 설정 파일(properties, yaml, DB 설정 테이블)에서 관리되는 규칙도 포함하라
- 각 규칙에 ID를 부여하고 메타정보를 정리하라:
  - 규칙 ID, 규칙명, 설명, 소스 위치, 관련 서비스, 확인 상태

### 2. 에러/예외 처리 패턴 추출
- 서비스별 에러 코드 체계를 수집하라 (HTTP 상태코드, 커스텀 에러코드)
- 예외 처리 방식을 분류하라 (GlobalExceptionHandler, ErrorBoundary, try-catch 등)
- 사용자 노출 에러 메시지 목록을 정리하라
- 외부 연동 실패 시 fallback/retry 정책을 식별하라

### 3. 상태값 및 전이 조건 추출
- 도메인 객체의 상태 enum/status 컬럼을 식별하라
- 상태 변경 로직에서 전이 조건을 역추적하라
- Mermaid stateDiagram으로 상태 전이도를 작성하라

### 4. 이벤트/메시지 흐름 추출
- Kafka, RabbitMQ, Redis Pub/Sub 등 비동기 메시지 Producer/Consumer를 식별하라
- 이벤트 토픽/큐 목록, 이벤트 스키마(페이로드 구조)를 정리하라
- 발행-구독 관계를 매핑하라

### 4-1. 배치/스케줄러 작업 인벤토리 추출
- @Scheduled, Quartz, Spring Batch, Cron 표현식을 전수 스캔하라
- 배치 작업별 메타정보를 정리하라: 작업명, 실행 주기, 대상 데이터, 처리 로직 요약, 소스 위치
- 배치 실패 시 재처리/알림 정책을 식별하라
- 배치 간 의존 관계를 매핑하라 (선행 배치 완료 후 후행 배치 실행 등)

### 4-2. 서비스 간 Call Flow 호출 체인 원시 데이터 추출
- 동기 호출 체인: API Gateway → Service A → Service B → DB 순서를 추적하라
- 비동기 호출 체인: Service A → MQ → Service B → DB 순서를 추적하라
- 호출 체인별 메타정보: 호출 순서, 프로토콜(REST/gRPC/MQ), 타임아웃, 에러 전파 방식
- 순환 호출(Circular Dependency) 패턴을 식별하라 → ⚠️ 표기
- **Use Case ID 연결 필수**: 각 호출 체인이 어떤 Use Case(UC-{서비스}-{순번})에 해당하는지 매핑하라. 하나의 UC가 여러 호출 체인을 포함할 수 있음
- **E2E Call Flow 원시 데이터 추출**: 주요 Use Case(가입, 결제, 인증 등)에 대해 사용자 조작부터 최종 응답까지의 전체 흐름을 추출하라:
  - 전체 구간: 앱(프론트엔드) → 공통모듈/SDK → API Gateway → 백엔드 서비스 → 외부 시스템 → DB → 응답 → 앱 UI 갱신
  - 각 구간의 프로토콜, 인증 방식, 에러 시 분기(fallback/retry/에러 화면)를 포함
  - 동기 호출과 비동기 이벤트가 혼합된 경우 양쪽 모두 표기 (예: API 응답 후 비동기 알림 발송)

- **★ 멀티 Repo E2E 연결 절차 (앱/서버 Repo 분리 시 필수)**:
  > 외주 개발사가 다른 경우 등 앱과 서버가 별도 Repo에 있는 서비스가 대부분이다.
  > 이 경우 앱↔서버 간 Call Flow가 단절되기 쉬우므로, 아래 절차를 반드시 수행하라.

  **Phase A: 앱 Repo에서 API 호출 인벤토리 추출 (앱 → 서버 방향)**
  1. 앱 소스(`src/app-android/`, `src/app-ios/`, `src/app-cross/`)에서 HTTP 클라이언트 호출부를 전수 스캔하라:
     - Android: `Retrofit` 인터페이스(@GET/@POST), `OkHttp` 직접 호출, `Volley`
     - iOS: `Alamofire`, `URLSession`, `Moya`
     - React Native: `fetch()`, `axios`, `apisauce`
     - Flutter: `http`, `dio`, `chopper`
  2. 각 API 호출부에서 추출할 정보:
     - 호출 URL/경로 (Base URL + endpoint path)
     - HTTP 메서드 (GET/POST/PUT/DELETE)
     - 요청 파라미터/Body 구조
     - 호출하는 화면/ViewModel/UseCase 클래스 (어떤 사용자 행위에서 호출되는지)
     - 응답 처리 로직 (성공 시 화면 전이, 실패 시 에러 처리)
     - 호출 전 경유하는 공통 모듈/SDK (인증 토큰 주입, 암호화, 로깅 등)
  3. 결과를 "앱 API 호출 인벤토리" 테이블로 정리:
     | 호출 위치 (앱 소스) | 화면 ID | 사용자 행위 | API 경로 | HTTP 메서드 | 경유 공통모듈/SDK | 응답 처리 |
     |---|---|---|---|---|---|---|

  **Phase B: 서버 Repo에서 엔드포인트 인벤토리 추출 (서버 → DB/외부 방향)**
  1. 서버 소스(`src/server/`)에서 Controller/Router 엔드포인트를 전수 스캔하라 (Step 03 결과 활용)
  2. 각 엔드포인트에서 내부 호출 체인을 추적하라:
     - Controller → Service → Repository → DB
     - Controller → Service → 외부 시스템 호출 (Feign/RestTemplate/gRPC)
     - Controller → Service → MQ 발행 → Consumer → 후속 처리
  3. 결과를 "서버 엔드포인트 호출 체인" 테이블로 정리:
     | API 경로 | Controller 클래스 | Service 메서드 | 내부 호출 체인 | 외부 연동 | DB 접근 | 비동기 후속 처리 |
     |---|---|---|---|---|---|---|

  **Phase C: 앱↔서버 교차 매핑 (E2E 연결)**
  1. Phase A의 "API 경로"와 Phase B의 "API 경로"를 매칭하라
  2. 매칭 결과를 3가지로 분류:
     - ✅ 매칭 성공: 앱 호출 URL = 서버 엔드포인트 → E2E 연결 완성
     - ⚠️ 부분 매칭: URL 패턴은 유사하나 버전/경로 차이 → 확인 필요
     - ❌ 매칭 실패: 앱에서 호출하는 API가 서버에 없음 (다른 서비스 호출 또는 외부 API) → 호출 대상 식별
  3. 매칭 성공한 항목에 대해 E2E Call Flow를 조합하라:
     ```
     [앱 화면] → [앱 공통모듈/SDK] → [API 호출] → [서버 Controller] → [서버 Service] → [외부/DB] → [응답] → [앱 UI 갱신]
     ```
  4. 매칭 실패 항목은 "미연결 API 목록"으로 별도 정리하고 🔍 표기

  **Phase D: 중간 계층(공통모듈/SDK/Library) 삽입**
  1. Step 1c(`01c-common-modules/`) 결과에서 E2E 경로상에 개입하는 공통 모듈을 식별하라:
     - 앱 측: 공통 네트워크 모듈(토큰 주입, 헤더 설정), 공통 에러 핸들러, 암호화 모듈
     - 서버 측: Security Filter Chain, 공통 인터셉터, AOP 로깅
  2. Binary SDK(`<<binary SDK>>`)가 E2E 경로에 개입하는 경우 식별하라:
     - 결제 SDK, DRM SDK, 인증 SDK, 앱 쉴딩 SDK 등
     - SDK 내부 로직은 추적 불가하므로 `<<binary SDK>>` 표기하고 입출력만 기록
  3. E2E Call Flow에 중간 계층을 삽입하여 완성:
     ```
     [앱 화면] → [앱 공통 네트워크 모듈(토큰 주입)] → [결제 SDK <<binary>>] → [API Gateway] → [서버 Security Filter] → [Controller] → [Service] → [공통 암호화 모듈] → [외부 PG] → [DB] → [응답] → [앱 공통 에러 핸들러] → [앱 UI 갱신]
     ```

  **E2E Call Flow 산출물 포맷**:
  주요 Use Case별로 아래 형식의 E2E Call Flow 문서를 작성하라:
  ```markdown
  ## UC-{서비스}-001: {Use Case명} E2E Call Flow

  ### 전체 흐름 요약
  앱 화면 → ... → 최종 응답 (1줄 요약)

  ### 상세 Call Flow
  | 순서 | 구간 | 소스 위치 (Repo/파일) | 호출 대상 | 프로토콜 | 비고 |
  |---|---|---|---|---|---|
  | 1 | 앱 화면 조작 | app-android/...Activity | ViewModel.doAction() | — | 사용자 버튼 클릭 |
  | 2 | 앱 공통 모듈 | app-android/.../NetworkModule | 토큰 주입, 헤더 설정 | — | 공통 인터셉터 |
  | 3 | SDK 호출 | app-android/.../PaymentSDK | <<binary SDK>> 결제 요청 | SDK API | 내부 추적 불가 |
  | 4 | API 호출 | app-android/.../ApiService | POST /api/v1/payment | HTTPS | Retrofit |
  | 5 | 서버 수신 | server/.../PaymentController | processPayment() | REST | Spring MVC |
  | 6 | 서버 로직 | server/.../PaymentService | validate → process | — | @Transactional |
  | 7 | 외부 연동 | server/.../PGClient | PG사 승인 API | HTTPS | Feign, timeout 5s |
  | 8 | DB 저장 | server/.../PaymentRepository | INSERT payment_history | JDBC | JPA |
  | 9 | 비동기 이벤트 | server/.../PaymentEventPublisher | Kafka: payment.completed | MQ | 알림 서비스로 전달 |
  | 10 | 응답 반환 | server/.../PaymentController | 200 OK + PaymentResult | REST | — |
  | 11 | 앱 응답 처리 | app-android/.../PaymentViewModel | 결제 완료 화면 전이 | — | LiveData 갱신 |

  ### 에러/Fallback 분기
  | 분기 지점 | 에러 조건 | 처리 | 사용자 피드백 |
  |---|---|---|---|
  | 순서 7 (PG 연동) | timeout 5s 초과 | 재시도 1회 → 실패 시 Circuit Breaker | "결제 처리 중 오류" 팝업 |
  | 순서 4 (API 호출) | 401 Unauthorized | 토큰 갱신 → 재시도 | 자동 재시도 (사용자 무인지) |

  ### 앱↔서버 매칭 근거
  - 앱 호출: `POST /api/v1/payment` (app-android/.../PaymentApiService.kt:42)
  - 서버 수신: `@PostMapping("/api/v1/payment")` (server/.../PaymentController.java:28)
  - 요청 DTO 일치 여부: ✅ (PaymentRequest ↔ PaymentRequestDto 필드 매칭)
  ```

### 5. 유저 시나리오(Use Case) 추출

> B2C 상품의 공통 Use Case와 서비스 특화 Use Case를 체계적으로 식별한다.
> Use Case는 사용자 여정(User Journey) 기반 Tree 구조로 계층화하여 관리한다.

#### 5-0. Use Case Tree 계층 구조 구축
- 사용자 여정 기반으로 Use Case를 5단계 Tree 구조로 계층화하라:
  1. **진입점(EP) 식별**: 앱 최초 실행(미로그인), 로그인 후 메인화면, 외부 진입(딥링크/푸시)
  2. **라이프사이클(LC) 매핑**: 온보딩(LC-1), 인증(LC-2), 메인 활동(LC-3), 이탈(LC-4)
  3. **기능 그룹(FG) 분류**: LC-3 하위에 상품/서비스(FG-1), 결제/과금(FG-2), 콘텐츠(FG-3), 고객센터(FG-4), 알림(FG-5), 계정관리(FG-6), 서비스 특화(FG-7)
  4. **개별 UC 배치**: 5-1(공통) + 5-2(특화) 결과를 해당 FG에 배치, Tree 경로 표기 (예: `LC-3 > FG-1 > UC-{서비스}-001`)
  5. **시나리오 연결**: 각 UC 하위에 정상(Happy)/대안(Alternative)/예외(Exception) 시나리오 배치 (5-3 결과)
- 각 Level 간 전이 조건을 명시하라 (인증 상태, 권한, 가입 상태 등)
- 화면 흐름도(Screen Flow)와 교차 매핑: Tree의 각 UC가 어떤 화면 ID를 거치는지 연결
- Mermaid mindmap 또는 들여쓰기 목록으로 Tree 구조를 시각화하라

#### 5-1. B2C 공통 Use Case 매핑
- 아래 9개 공통 카테고리를 기준으로 코드 내 구현 여부를 확인하라:
  - 가입(Subscription), 해지(Cancellation), 변경(Modification), 조회(Inquiry)
  - 결제/과금(Billing), 인증/본인확인(Verification), 고객센터(Support)
  - 알림/마케팅(Notification), 계정 관리(Account)
- 각 Use Case에 ID를 부여하라: UC-{서비스약어}-{순번}
- Controller/Service 클래스에서 해당 기능의 엔드포인트, 소스 위치를 매핑하라
- 02-screens 결과의 화면 ID와 연결하라

#### 5-2. 서비스 특화 Use Case 발굴
- 공통 Use Case에 매핑되지 않는 API 엔드포인트를 추출하라
- 도메인 서비스 클래스에서 공통 카테고리 외 비즈니스 로직 메서드를 식별하라
- 상태 전이(3번 결과)에서 공통 Use Case로 설명되지 않는 전이 경로를 확인하라
- 이벤트/메시지(4번 결과)에서 공통 Use Case 외 비동기 처리를 확인하라

#### 5-3. Use Case 시나리오 상세화
- 주요 Use Case에 대해 정상/대안/예외 시나리오를 작성하라
- 사전 조건(Precondition)과 사후 조건(Postcondition)을 명시하라
- 코드에서 추출한 근거(메서드명, 조건문, 에러코드)를 기재하라

#### 5-4. Use Case 시나리오별 비기능(NFR) 체크포인트 연계
- 각 시나리오의 단계별로 아래 비기능 점검 항목을 교차 매핑하라:
  - `[보안]` 인증/인가 확인, 개인정보 처리, 입력값 검증, 감사 로그, 세션 만료 처리
  - `[품질]` 에러 처리/사용자 피드백, 로딩/타임아웃 UX, 데이터 정합성, 동시성 충돌, 테스트 존재 여부
  - `[성능]` 응답시간 민감 단계, 대용량 처리, 외부 연동 fallback
  - `[플랫폼]` iOS/Android/STB 동작 차이, 플랫폼별 제약사항
- 시나리오 문서 내 각 단계에 `[보안]` `[품질]` `[성능]` 태그로 인라인 표기
- 미충족 NFR 항목은 Phase 3 Gap 분석(10-gap-analysis)의 입력으로 연계

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

> 대용량 Repo에서 이 Step의 항목을 "해당 없음"으로 판정하기 전에, 아래 패턴을 반드시 검색하라.

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| Service 클래스 | `*Service*`, `*ServiceImpl*`, `*UseCase*`, `*Interactor*` | `@Service`, `@Transactional`, `@Component` |
| Enum/Status | `*Enum*`, `*Status*`, `*State*`, `*Type*`, `*Code*` | `enum`, `status`, `state`, `valueOf` |
| 이벤트/메시지 | `*Producer*`, `*Consumer*`, `*Listener*`, `*Publisher*`, `*Handler*` | `@KafkaListener`, `@RabbitListener`, `KafkaTemplate`, `@EventListener`, `@StreamListener` |
| 배치/스케줄러 | `*Job*`, `*Scheduler*`, `*Batch*`, `*Task*` | `@Scheduled`, `@EnableScheduling`, `Quartz`, `cron`, `JobDetail`, `Step` |
| Feign/내부호출 | `*Client*`, `*FeignClient*`, `*Proxy*` | `@FeignClient`, `RestTemplate`, `WebClient`, `circuitBreaker`, `@Retry` |
| 에러 처리 | `*Exception*`, `*ErrorHandler*`, `*ExceptionHandler*`, `*ErrorCode*` | `@ExceptionHandler`, `@ControllerAdvice`, `ErrorBoundary`, `GlobalException` |

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/04-business-logic/` 에 아래 파일 생성:
- `business-rules.md` — 비즈니스 규칙 목록
- `error-handling.md` — 에러 코드/예외 처리 패턴 목록
- `state-machines.md` — 상태 전이 다이어그램 (Mermaid stateDiagram)
- `event-flows.md` — 이벤트/메시지 토픽 목록 및 발행-구독 관계
- `batch-scheduler-inventory.md` — 배치/스케줄러 작업 인벤토리
- `call-flow-chains.md` — 서비스 간 Call Flow 호출 체인 원시 데이터
- `e2e-call-flows.md` — 주요 Use Case별 E2E Call Flow 원시 데이터 (앱→Gateway→서비스→외부→DB→응답→앱, 멀티 Repo 교차 매핑 포함)
- `use-case-tree.md` — Use Case Tree 계층 구조도 (EP→LC→FG→UC→Scenario, Mermaid mindmap)
- `use-cases.md` — Use Case 카탈로그 (공통 + 서비스 특화, ID/구현여부/API/화면 매핑)
- `use-case-scenarios.md` — 주요 Use Case 시나리오 상세 (정상/대안/예외, NFR 체크포인트 인라인 태그 포함)

## 💡 효과적인 추가 프롬프트 예시

> 출처: [prompt-cookbook.md](../project/prompt-cookbook.md)

**숨겨진 상태 전이 발견:**
```
DB 테이블에서 status, state, flag, type 컬럼을 가진 Entity를 모두 찾고,
해당 컬럼 값을 변경하는 UPDATE 쿼리나 setter 호출부를 추적해줘.
enum이 아닌 String/int 타입 상태값도 포함해줘.
```

**배치/스케줄러 누락 시:**
```
@Scheduled, cron, Quartz, JobDetail, Spring Batch Step 키워드를 모두 검색해줘.
XML 설정 기반 스케줄러도 포함해줘.
```

## 🔧 작업자 수작업 보충 항목

> 이 Step에서 KIRO 자동 추출만으로는 완성할 수 없는 항목입니다.
> 산출물 생성 후 아래 항목을 작업자가 직접 보충해 주세요.
> 상세: [manual-tasks.md](../project/manual-tasks.md)

| 항목 ID | 항목명 | 유형 | 해야 할 일 |
|---|---|---|---|
| P1-3 | 분산 추적 데이터 수집 | `[런타임]` | Zipkin/Jaeger에서 실제 서비스 간 호출 그래프를 수집하여 KIRO 추출 Call Flow와 비교 |
| P1-8 | 외부 연동 SLA 확인 | `[인터뷰]` | 외부 연동 시스템의 SLA/Rate Limit을 확인하여 Call Flow에 제약사항 보충 |
| P1-4 | 기술 부채 마커 검토 | `[확정]` | 비즈니스 규칙 중 하드코딩된 매직넘버/상수의 의도를 개발 리드에게 확인 |

## 완료 기준
- 주요 비즈니스 규칙이 ID와 함께 목록화됨
- 에러 코드 체계가 서비스별로 정리됨
- 핵심 도메인 객체의 상태 전이도가 작성됨
- 비동기 메시지 흐름이 매핑됨
- 배치/스케줄러 작업이 인벤토리로 정리됨 (작업명, 주기, 의존 관계)
- 서비스 간 Call Flow 호출 체인이 동기/비동기 구분하여 추출됨
- 주요 Use Case(가입, 결제, 인증 등)의 E2E Call Flow가 앱→서버→외부 전체 구간으로 추출됨
- 멀티 Repo 서비스에서 앱↔서버 간 API 호출부-Controller 교차 매핑이 완료됨
- B2C 공통 Use Case 9개 카테고리에 대한 구현 여부가 확인됨
- 서비스 특화 Use Case가 식별되어 ID가 부여됨
- 주요 Use Case의 정상/예외 시나리오가 작성됨
- Use Case Tree가 EP→LC→FG→UC→Scenario 5단계로 계층화됨
- Use Case 시나리오에 보안/품질/성능 NFR 체크포인트가 단계별로 매핑됨

## 🔄 역방향 피드백 체크 (이전 산출물 업데이트)

이 Step에서 발견한 내용이 이전 Step 산출물에 영향을 주는지 확인하세요:

- [ ] **Step 01 → code-structure.md 업데이트 필요?**
  - 비즈니스 로직에서 발견한 숨겨진 모듈 의존성이 코드 구조에 반영되어 있는지 확인
- [ ] **Step 02 → screen-inventory.md 업데이트 필요?**
  - 비즈니스 규칙에 의한 조건부 화면 분기가 화면 인벤토리에 반영되어 있는지 확인
- [ ] **Step 03 → api-inventory.md 업데이트 필요?**
  - 비즈니스 로직 내부에서 호출하는 내부/외부 API가 API 인벤토리에 누락되어 있는지 확인
  - 비즈니스 규칙에 따른 API 호출 조건/분기가 API 문서에 반영되어 있는지 확인

> 업데이트가 필요한 경우, 해당 산출물 파일을 직접 수정하고 `_context-notes.md`에 변경 사유를 기록하세요.
