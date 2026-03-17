---
inclusion: manual
---

# Step 4: 비즈니스 로직 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-4. 비즈니스 로직

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
- `use-case-tree.md` — Use Case Tree 계층 구조도 (EP→LC→FG→UC→Scenario, Mermaid mindmap)
- `use-cases.md` — Use Case 카탈로그 (공통 + 서비스 특화, ID/구현여부/API/화면 매핑)
- `use-case-scenarios.md` — 주요 Use Case 시나리오 상세 (정상/대안/예외, NFR 체크포인트 인라인 태그 포함)

## 완료 기준
- 주요 비즈니스 규칙이 ID와 함께 목록화됨
- 에러 코드 체계가 서비스별로 정리됨
- 핵심 도메인 객체의 상태 전이도가 작성됨
- 비동기 메시지 흐름이 매핑됨
- 배치/스케줄러 작업이 인벤토리로 정리됨 (작업명, 주기, 의존 관계)
- 서비스 간 Call Flow 호출 체인이 동기/비동기 구분하여 추출됨
- B2C 공통 Use Case 9개 카테고리에 대한 구현 여부가 확인됨
- 서비스 특화 Use Case가 식별되어 ID가 부여됨
- 주요 Use Case의 정상/예외 시나리오가 작성됨
- Use Case Tree가 EP→LC→FG→UC→Scenario 5단계로 계층화됨
- Use Case 시나리오에 보안/품질/성능 NFR 체크포인트가 단계별로 매핑됨
