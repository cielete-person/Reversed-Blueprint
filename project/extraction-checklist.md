# 설계도 파묘 — 추출 항목 체크리스트

> 이 문서는 소스코드 분석을 통해 설계도를 역추적할 때 확인해야 하는 모든 항목을 정의합니다.
> 프로젝트 로드맵은 [workplan-roadmap.md](workplan-roadmap.md) 참조
> CDR 설계 체크리스트 매핑은 [cdr-design-checklist.md](cdr-design-checklist.md) 참조 (CTO품질PMO팀 CDR 가이드 기반)

---

## 확인 상태 표기 원칙

모든 분석 항목에 아래 확인 상태를 표기한다.

| 상태 | 의미 |
|---|---|
| ✅ 확인완료 | 코드/설정에서 자동 또는 수동으로 확인됨 |
| ⚠️ 일부확인 | 부분적으로만 확인됨 (확인된 범위 명시) |
| ❌ 미확인 | 소스코드 없음, 접근 불가, 또는 코드에서 추출 불가 |
| 🔍 인터뷰필요 | 코드만으로 확인 불가, 담당자 확인 필요 |

## 분석 주체 태그

각 항목에 아래 태그를 표기하여, KIRO로 바로 실행 가능한 항목과 별도 조치가 필요한 항목을 구분한다.

| 태그 | 의미 | 설명 |
|---|---|---|
| `[KIRO]` | KIRO 단독 가능 | 소스코드/설정 파일만으로 KIRO가 직접 추출 가능 |
| `[KIRO+도구]` | KIRO + 외부 도구 | KIRO가 코드 패턴은 식별하나, 정밀 결과는 외부 도구 실행 필요 (JaCoCo, Snyk, SonarQube, git log 등) |
| `[KIRO+확정]` | KIRO 추출 + 사람 확정 | KIRO가 후보를 제시하고, PO/UX/담당자가 최종 확정 |
| `[인터뷰]` | 담당자 확인 필수 | 코드에서 추출 불가, 외부 시스템/운영 환경/정책 문서 확인 필요 |

---

## Phase 1: 소스코드 기초 분석 — 추출 항목

### 1-1. 코드 구조 분석

- [ ] Repository별 자동 스캔 `[KIRO]`
  - 언어/프레임워크 식별 (package.json, pom.xml, build.gradle 등)
  - 디렉토리 구조 패턴 분류 (MVC, Hexagonal, Monolith, MSA 등)
  - 의존성 목록 추출 (내부 라이브러리, 외부 패키지)
- [ ] 설정 프로파일별 차이 분석 (Configuration Profile Analysis) `[KIRO]`
  - 환경별 설정 파일 식별: application-{profile}.yml, .env.{환경}, config/{환경}/ 등
  - dev/staging/prod 간 주요 차이점 추출: DB 접속 정보, 외부 연동 URL, 기능 플래그, 로그 레벨
  - 환경별 활성화되는 Bean/모듈 차이 (@Profile, @ConditionalOnProperty 등)
  - 설정값 중 하드코딩 vs 환경변수 vs 외부 설정 서버(Config Server) 구분
  - prod 전용 설정 중 보안 민감 항목 식별 (암호화 키, 인증 정보 등 → 1-7 연계)

- [ ] 디바이스/플랫폼 분기 코드 추출 (Device & Platform Branching Extraction) `[KIRO]`
  - **모바일 앱 (iOS / Android)**
    - 플랫폼별 네이티브 코드 분기 식별 (Platform.OS, #if os(iOS), Build.VERSION 등)
    - 공유 코드 vs 플랫폼 전용 코드 비율 분석 (React Native: .ios.js/.android.js, Flutter: Platform.isIOS)
    - 플랫폼별 네이티브 모듈/브릿지 목록 (카메라, 푸시, 생체인증, 결제 등)
    - 플랫폼별 권한(Permission) 요청 차이 (iOS Info.plist vs Android Manifest)
    - 플랫폼별 딥링크/유니버설 링크 설정 차이
    - 플랫폼별 푸시 알림 처리 차이 (APNs vs FCM)
  - **STB 앱 (AOSP / Android TV)**
    - AOSP 기반 커스텀 론처(LGU+ 자체 론처) 관련 코드 식별
    - Android TV Leanback 라이브러리 사용 여부 및 커스텀 UI 범위
    - STB 기종별 분기 코드 (저사양/고사양, 칩셋별, 미들웨어별)
    - TV 입력 프레임워크(TIF) / CAS / DRM 연동 코드
    - 리모컨 전용 네비게이션 로직 (D-pad 포커스 관리, 키 이벤트 핸들링)
    - STB 전용 시스템 API 호출 (하드웨어 제어, 튜너, HDMI-CEC 등)
  - **크로스 플랫폼 공통**
    - 디바이스 타입별 분기 로직 (phone/tablet/TV/STB)
    - 화면 크기/해상도별 레이아웃 분기 (Responsive/Adaptive)
    - OS 버전별 분기 코드 (minSdkVersion, iOS Deployment Target 등)
    - 네트워크 환경별 분기 (Wi-Fi/LTE/5G, 오프라인 모드)
  - 산출물: 플랫폼 분기 코드 인벤토리 (분기 위치, 분기 조건, 영향 범위)

- [ ] 기술 부채 마커 추출 (Tech Debt Marker Extraction) `[KIRO]`
  - 소스코드 내 TODO, FIXME, HACK, WORKAROUND, XXX, DEPRECATED 주석 전수 스캔
  - 마커별 메타정보: 파일 위치, 라인 번호, 작성자(git blame), 작성일, 내용
  - 마커 밀집도 분석: 파일/모듈별 기술 부채 마커 수 집계
  - ADR(Architecture Decision Record), CHANGELOG, README 내 설계 의도 기술 수집
  - 심각도 분류는 개발 리드가 수작업으로 확정 → `[KIRO+확정]` (manual-tasks.md P1-4)

- [ ] IaC(Infrastructure as Code) 분석 `[KIRO]`
  - Terraform, Helm Chart, Ansible, CloudFormation, Kustomize 파일 식별
  - IaC에서 인프라 아키텍처 역추적: VPC/서브넷, 보안 그룹, 로드밸런서, 오토스케일링
  - k8s NetworkPolicy, 서비스 메시(Istio/Linkerd) 설정 식별
  - IaC 파일이 소스 Repo에 없는 경우 → `🔍 인터뷰필요` (manual-tasks.md P1-7)

- [ ] CI/CD 파이프라인 상세 분석 `[KIRO]`
  - Jenkinsfile, .gitlab-ci.yml, bitbucket-pipelines.yml, GitHub Actions 등 파이프라인 파일 스캔
  - 빌드 스테이지 구성: lint → test → build → deploy 순서 및 게이트 조건
  - 배포 전략 식별: 블루그린/카나리/롤링 `[CDR 8.1.1]`
  - 자동 롤백 트리거 조건 식별
  - 환경별 배포 파이프라인 차이 (dev/staging/prod)

### 1-1b. Dead Code 및 미사용 리소스 분석

> Step 01 완료 후, Step 02 시작 전에 실행. 별도 Step으로 분리하여 컨텍스트 윈도우 보호.

- [ ] 미사용 코드 식별 (Dead Code Detection) `[KIRO]`
  - 미호출 클래스/메서드 탐지: 참조 0건 + 리플렉션/프레임워크 자동 등록 재확인
  - 미사용 import/의존성 탐지: 선언만 되고 사용되지 않는 import, 빌드 의존성
  - 도달 불가 코드: 항상 false 조건문, return/throw 이후 코드, 영구 비활성 피처 플래그
- [ ] 미사용 DB 리소스 식별 `[KIRO+확정]`
  - 미참조 테이블/컬럼: Entity/Mapper 정의 vs 코드 내 참조 교차 검증
  - 레거시 마이그레이션 잔존물: DROP 누락 테이블, ADD 후 미사용 컬럼
  - ⚠️ 외부 시스템 직접 DB 참조 가능성 → 개발 리드 확정 필요
- [ ] 미사용 API 식별 `[KIRO+확정]`
  - 미호출 API 엔드포인트: Controller 정의 vs 프론트엔드/Feign Client 호출 교차 검증
  - Deprecated API 현황: @Deprecated 표기 API 중 여전히 호출되는 것 vs 완전 미사용
  - ⚠️ 외부 시스템/파트너사 호출 가능성 → 개발 리드 확정 필요
- [ ] 미사용 설정/리소스 식별 `[KIRO]`
  - 미참조 설정값: application.yml/properties 정의 vs @Value/@ConfigurationProperties 참조
  - 미사용 리소스 파일: resources/assets 하위 이미지/템플릿/정적 파일
  - 미사용 Spring Bean: @Component 등록 vs @Autowired/생성자 주입 참조
- [ ] 코드 연대 분석 (Code Age Analysis) `[KIRO+도구]`
  - git log 기반 파일별 마지막 수정일 추출
  - 12개월+ 미변경 파일 = "장기 미변경 코드" 분류
  - 장기 미변경 + 미호출 = Dead Code 가능성 높음
  - ⚠️ 안정적 유틸리티/공통 모듈은 장기 미변경이 정상 → 참조 횟수와 교차 판단

### 1-1c. 공통 모듈 그룹핑 분석

> Step 01, Step 1b 완료 후 실행. 별도 Step으로 분리하여 컨텍스트 윈도우 보호.

- [ ] Repo 내부 공통 패키지 탐지 `[KIRO]`
  - 디렉토리명 패턴(common, shared, core, util, lib, base, helper, framework, support 등)으로 후보 선별
  - 패키지 선언 패턴(*.common.*, *.core.*, @shared/ 등)으로 추가 후보 식별
  - 후보 패키지 내 클래스/모듈의 import 참조 횟수 집계 (≥3: 확정, 1~2: ⚠️, 0: Dead Code 교차 확인)
  - 기능별 그룹 분류: 유틸리티, 공통 응답/DTO, 공통 예외, 공통 설정, 공통 인증/보안, 공통 로깅/AOP, 공통 데이터 접근, 공통 외부 연동, 공통 도메인, 공통 UI/프론트
- [ ] 사내 공통 라이브러리 (외부 Repo) 식별 `[KIRO]`
  - 빌드 파일(pom.xml, build.gradle, package.json 등)에서 사내 groupId/scope 의존성 추출
  - 사내 Maven/npm/PyPI 레지스트리 URL 확인
  - 라이브러리별 메타정보: 라이브러리명, 버전, 용도 추정, 참조 수
  - 소스 Repo URL (확인 불가 시 🔍)
- [ ] 공통 모듈 의존 방향 매핑 `[KIRO]`
  - 공통 모듈 → 비즈니스 모듈 역의존 탐지 (⚠️ 아키텍처 위반 후보)
  - 공통 모듈 간 의존 관계 식별 (core → util, security → core 등)
  - Mermaid flowchart로 의존 방향 다이어그램 작성
- [ ] 변경 영향 범위 분석 `[KIRO]`
  - 각 공통 모듈 변경 시 영향받는 클래스/모듈 수 집계
  - 영향 범위 등급: 🔴 High(10+), 🟡 Medium(5~9), 🟢 Low(1~4)
  - 변경 영향도 Top 10 산출
- [ ] 멀티 서비스 공통 모듈 교차 매핑 (선택) `[KIRO]`
  - 서비스 간 동일 사내 라이브러리 사용 현황 교차 매핑
  - 서비스별 라이브러리 버전 차이 식별 (⚠️)
  - 동일 유틸리티 클래스 복사 사용 패턴 식별 → 라이브러리화 후보

### 1-2. 화면 목록 및 정규화

- [ ] 화면 목록(Screen Inventory) 추출 `[KIRO]`
  - 프론트엔드 소스에서 라우트/페이지 컴포넌트 자동 스캔
  - 화면 ID 채번 규칙에 따라 ID 부여
  - 화면별 메타정보: 화면 ID, 화면명, 서비스, URL 경로, 담당조직, 소스 파일 위치
  - 팝업/모달/바텀시트 등 서브 화면 포함 누락 없이 식별

- [ ] 화면명 정규화 (Screen Name Normalization) `[KIRO+확정]`
  - AI 추출 시 동일 화면이 앱/서비스별로 다른 명칭을 가지는 경우 식별
    - 예: "홈" = "Home" = "메인" = "MainScreen" = "home_view"
  - 정규화 프로세스:
    1. AI가 코드에서 추출한 원본 명칭(Raw Name) 수집
    2. 유사도 분석으로 동일 화면 후보군 자동 클러스터링 (URL 경로, 기능, 호출 API 기준)
    3. 클러스터별 대표 명칭(Canonical Name) 후보 제시
    4. PO/UX 담당자 검토 → 대표 명칭 확정
    5. 용어 사전(Glossary)에 원본 명칭 → 대표 명칭 매핑 등록
  - 정규화 대상: 화면명, 화면 내 영역명, 버튼/메뉴 라벨, 기능명
  - STB 앱 특수 케이스: 동일 기능이 기종별/미들웨어별로 다른 명칭 사용 시 통합
  - 정규화 결과를 화면 카탈로그 및 모든 산출물에 일괄 반영

- [ ] 크로스 플랫폼 화면 동기화 (Cross-Platform Screen Sync) `[KIRO+확정]`
  - iOS / Android 모바일 앱 간 화면 정의서 용어·구분 동기화
    - 동일 기능 화면이 플랫폼별로 다른 클래스명/파일명을 가지는 경우 매핑
    - 예: iOS `HomeViewController` = Android `HomeActivity`/`HomeFragment`
    - 화면 ID는 플랫폼 무관하게 하나로 통합, 소스 위치만 플랫폼별 기재
  - 플랫폼별 화면 차이 식별
    - iOS에만 있는 화면 / Android에만 있는 화면 → ⚠️ 표기
    - 동일 화면이지만 레이아웃/UX 흐름이 다른 경우 차이점 기록
    - STB(AOSP/Android TV) 앱과 모바일 앱 간 동일 기능 화면 매핑
  - 화면 정의서 산출물 포맷에 플랫폼 컬럼 추가
    - `플랫폼`: iOS / Android / STB / Web / 공통
    - `소스 위치(iOS)`, `소스 위치(Android)` 등 플랫폼별 소스 경로 병기
  - 정규화 시 플랫폼 간 용어 통일을 최우선으로 적용
    - iOS 네이밍 vs Android 네이밍 중 대표 명칭 하나로 확정
    - 용어 사전에 플랫폼별 원본 명칭 → 대표 명칭 매핑 등록

### 1-3. API 및 데이터

- [ ] API 엔드포인트 자동 추출 `[KIRO]`
  - REST Controller/Router 스캔 → OpenAPI Spec 초안 생성
  - 서비스 간 호출 관계 식별 (Feign, RestTemplate, gRPC 등)
- [ ] API 요청/응답 스키마 상세 추출 (DTO/VO 필드 레벨) `[KIRO]`
  - Swagger/OpenAPI 미정비 레거시 대응: Controller 파라미터 + DTO/VO 클래스에서 직접 추출
  - 요청 DTO: 필드명, 타입, 필수/선택, validation 어노테이션, 기본값
  - 응답 DTO: 필드명, 타입, 중첩 구조(Wrapper/Page 등), 에러 응답 포맷
  - 공통 응답 래퍼 패턴 식별 (ApiResponse, CommonResult 등)
  - Swagger 어노테이션 존재 시 설명 텍스트도 수집
- [ ] API 버전 관리 전략 추출 `[KIRO]`
  - 버전 관리 방식 식별: URL 경로(/v1/, /v2/), 헤더(Accept-Version), 쿼리 파라미터
  - 버전 미적용 API 식별 (레거시 무버전 API → ⚠️ 표기)
  - 동일 기능의 복수 버전 공존 여부 (v1과 v2가 동시 운영되는 경우)
  - Deprecated API 식별 (@Deprecated, 주석, 미사용 추정)
- [ ] 서비스 간 내부 API 계약 추출 (Internal API Contract) `[KIRO]`
  - Feign Client 인터페이스에서 호출 대상 서비스, URL, 요청/응답 타입 추출
  - RestTemplate/WebClient 호출부에서 URL 패턴, 요청 빌더, 응답 파싱 로직 추출
  - gRPC proto 파일 존재 시 서비스/메서드 정의 수집
  - 내부 API 호출 체인 시각화 (A→B→C 순차 호출, 병렬 호출 구분)
  - 호출 시 타임아웃/리트라이/서킷브레이커 설정 수집
- [ ] REST API 멱등성 설계 추출 `[KIRO]`
  - PUT/DELETE 멱등성 보장 여부 확인 (동일 요청 반복 시 동일 결과)
  - POST 요청의 멱등키(Idempotency-Key) 사용 여부
  - 중복 요청 방지 로직 (DB 유니크 제약, Redis 중복 체크 등)
- [ ] API 페이지네이션/필터링 패턴 추출 `[KIRO]`
  - 페이지네이션 방식: offset/limit, cursor-based, keyset
  - 정렬/필터링 파라미터 패턴 (sort, filter, search 등)
  - 대용량 조회 API의 최대 페이지 크기 제한 설정
- [ ] DB 스키마 역추적 `[KIRO]`
  - ORM Entity/Mapper 분석 → ERD 초안 생성
  - 직접 DDL이 있는 경우 수집
  - 도메인 경계별 데이터 소유권 식별 (어떤 서비스가 어떤 엔터티를 소유/변경하는지) `[CDR 5.1.1]`
- [ ] DB 쿼리 패턴 상세 추출 `[KIRO]`
  - MyBatis XML Mapper: SQL 문 전수 수집, 동적 SQL(if/choose/foreach) 패턴 식별
  - JPA Native Query / @Query 어노테이션 수집
  - Stored Procedure / Function 호출 목록
  - N+1 쿼리 위험 패턴 식별 (Lazy Loading + 루프 내 조회)
  - 복잡 조인/서브쿼리 목록 (성능 병목 후보)
- [ ] DB 마이그레이션 이력 추출 `[KIRO+도구]`
  - Flyway(V*.sql), Liquibase(changelog), Django migrations 등 마이그레이션 스크립트 식별
  - 스키마 변경 이력 정리: 변경일(파일명/타임스탬프), 변경 내용(ADD/ALTER/DROP), 대상 테이블
  - 주요 스키마 변경점 요약 (컬럼 추가/삭제, 인덱스 변경, 테이블 분리/통합)
  - 마이그레이션 스크립트가 소스 Repo에 없는 경우 → `🔍 인터뷰필요` (manual-tasks.md P1-6)

### 1-4. 비즈니스 로직

- [ ] 비즈니스 규칙 추출 (Business Rule Extraction) `[KIRO]`
  - 코드 내 하드코딩된 비즈니스 규칙 식별 (조건문, 상수, 설정값)
  - 예: 요금 계산 로직, 할인 조건, 가입 자격, 약정 위약금, 한도 제한 등
  - 규칙별 메타정보: 규칙 ID, 규칙명, 위치(소스 파일/라인), 관련 서비스, 확인 상태
  - 설정 파일(properties, yaml, DB 설정 테이블)에서 관리되는 규칙도 포함
- [ ] 에러/예외 처리 패턴 추출 (Error Handling Extraction) `[KIRO]`
  - 서비스별 에러 코드 체계 수집 (HTTP 상태코드, 커스텀 에러코드)
  - 예외 처리 방식 분류: try-catch 패턴, GlobalExceptionHandler, ErrorBoundary 등
  - 사용자 노출 에러 메시지 목록
  - 외부 연동 실패 시 fallback/retry 정책 식별 (Circuit Breaker, Retry, Timeout 설정)
- [ ] 상태값 및 전이 조건 추출 (State Extraction) `[KIRO]`
  - 도메인 객체의 상태 enum/status 컬럼 식별
  - 상태 변경 로직에서 전이 조건 역추적
  - 예: 가입(신청→심사→개통→해지), 주문(주문→결제→배송→완료→취소)
- [ ] 이벤트/메시지 흐름 추출 (Event Flow Extraction) `[KIRO]`
  - Kafka, RabbitMQ, Redis Pub/Sub 등 비동기 메시지 Producer/Consumer 식별
  - 이벤트 토픽/큐 목록, 이벤트 스키마(페이로드 구조)
  - 발행-구독 관계 매핑
  - 전달 보장 수준 식별: At-least-once / At-most-once / Exactly-once 설정 확인 `[CDR 4.3.2]`
  - 메시지 순서 보장 정책: 파티셔닝 키 전략, Ordering 설정 확인 `[CDR 4.3.3]`
  - 멱등성(Idempotency) 설계: 멱등키 사용 여부, 중복 처리 방지 로직 `[CDR 4.3.4]`
  - DLQ(Dead Letter Queue) / Poison 메시지 처리 정책 `[CDR 4.3.4]`
- [ ] SPOF(단일 장애점) 및 Failure Mode 식별 `[KIRO]` `[CDR 3.2.1]`
  - 서비스별 SPOF 후보 식별 (단일 DB, 단일 캐시, 단일 외부 연동 등)
  - 의존 컴포넌트별 Failure Mode 분류 (응답지연/오류/부분장애)
  - Degraded Mode(최소 기능 모드) 코드 패턴 식별 (fallback, 기능 축소 분기) `[CDR 3.2.4]`
- [ ] 배치/스케줄러 작업 인벤토리 추출 (Batch & Scheduler Inventory) `[KIRO]`
  - @Scheduled, Quartz, Spring Batch, Cron 표현식 전수 스캔
  - 배치 작업별 메타정보: 작업명, 실행 주기, 대상 데이터, 처리 로직 요약, 소스 위치
  - 배치 실패 시 재처리/알림 정책 식별
  - 배치 간 의존 관계 (선행 배치 완료 후 후행 배치 실행 등)
  - 배치 실행 환경: 별도 배치 서버 / 동일 서버 / k8s CronJob 등
- [ ] 서비스 간 Call Flow 호출 체인 원시 데이터 추출 `[KIRO]`
  - 동기 호출 체인: API Gateway → Service A → Service B → DB 순서 추적
  - 비동기 호출 체인: Service A → MQ → Service B → DB 순서 추적
  - 호출 체인별 메타정보: 호출 순서, 프로토콜(REST/gRPC/MQ), 타임아웃, 에러 전파 방식
  - 순환 호출(Circular Dependency) 패턴 식별 → ⚠️ 표기

### 1-4b. 유저 시나리오(Use Case) 추출

- [ ] Use Case Tree 계층 구조 구축 (Use Case Hierarchy Tree) `[KIRO]`
  - 사용자 여정(User Journey) 기반으로 Use Case를 Tree 구조로 계층화하라
  - **Level 0 — 진입점 (Entry Points)**:
    - `EP-1` 앱 최초 실행 (미로그인 상태)
    - `EP-2` 로그인 완료 후 메인화면
    - `EP-3` 외부 진입 (딥링크, 푸시 알림, 위젯)
  - **Level 1 — 라이프사이클 단계 (Lifecycle Phases)**:
    - `LC-1` 온보딩 (회원가입, 본인인증, 약관동의, 초기설정)
    - `LC-2` 인증 (로그인, 자동로그인, 생체인증, SSO)
    - `LC-3` 메인 활동 (로그인 후 메인화면에서 접근 가능한 모든 기능)
    - `LC-4` 이탈 (로그아웃, 회원탈퇴, 서비스해지)
  - **Level 2 — 기능 그룹 (Feature Groups)** (LC-3 하위):
    - `FG-1` 상품/서비스 (가입, 변경, 해지, 조회)
    - `FG-2` 결제/과금 (납부, 자동이체, 환불, 포인트)
    - `FG-3` 콘텐츠 소비 (VOD, 채널, 음악 등 — 서비스별)
    - `FG-4` 고객센터 (문의, 장애신고, FAQ)
    - `FG-5` 알림/마케팅 (푸시설정, 이벤트, 수신동의)
    - `FG-6` 계정 관리 (프로필, 비밀번호, 기기관리)
    - `FG-7` 서비스 특화 기능 (플랫폼별 고유 기능)
  - **Level 3 — 개별 Use Case** (각 FG 하위):
    - 기존 B2C 공통 Use Case + 서비스 특화 Use Case를 해당 FG에 배치
    - 각 Use Case에 Tree 경로 표기: `LC-3 > FG-1 > UC-{서비스}-001 상품가입`
  - **Level 4 — 시나리오 (Scenarios)** (각 UC 하위):
    - 정상 시나리오 (Happy Path)
    - 대안 시나리오 (Alternative Path)
    - 예외 시나리오 (Exception Path)
  - Tree 구조 산출물 포맷 (Mermaid mindmap 또는 들여쓰기 목록):
    ```
    앱 실행
    ├── [EP-1] 미로그인
    │   ├── [LC-1] 온보딩
    │   │   ├── UC-001 회원가입
    │   │   ├── UC-002 본인인증
    │   │   └── UC-003 약관동의
    │   └── [LC-2] 인증
    │       ├── UC-004 로그인
    │       ├── UC-005 자동로그인
    │       └── UC-006 생체인증
    ├── [EP-2] 로그인 후 메인화면
    │   ├── [FG-1] 상품/서비스
    │   │   ├── UC-010 상품가입
    │   │   ├── UC-011 요금제변경
    │   │   └── UC-012 상품해지
    │   ├── [FG-2] 결제/과금
    │   │   ├── UC-020 요금납부
    │   │   └── UC-021 자동이체등록
    │   ├── [FG-3] 콘텐츠 소비 (서비스별)
    │   │   └── ...
    │   ├── [FG-4] 고객센터
    │   ├── [FG-5] 알림/마케팅
    │   ├── [FG-6] 계정 관리
    │   └── [FG-7] 서비스 특화
    ├── [EP-3] 외부 진입
    │   ├── 딥링크 → 대상 화면 직행
    │   └── 푸시 알림 → 대상 화면 직행
    └── [LC-4] 이탈
        ├── UC-090 로그아웃
        ├── UC-091 회원탈퇴
        └── UC-092 서비스해지
    ```
  - 각 Level 간 전이 조건을 명시하라 (인증 상태, 권한, 가입 상태 등)
  - 화면 흐름도(Screen Flow)와 교차 매핑: Tree의 각 UC가 어떤 화면 ID를 거치는지 연결

- [ ] B2C 공통 Use Case 매핑 (Common Use Case Mapping) `[KIRO]`
  - 통신사 B2C 상품에 공통적으로 존재하는 표준 Use Case를 코드에서 식별하라
  - 공통 Use Case 카탈로그 (아래 항목 기준으로 코드 내 구현 여부 확인):
    - **가입(Subscription)**: 상품 가입, 부가서비스 가입, 약정 가입, 번들 가입, 무료체험 가입
    - **해지(Cancellation)**: 상품 해지, 부가서비스 해지, 약정 해지(위약금), 즉시해지/예약해지
    - **변경(Modification)**: 요금제 변경, 부가서비스 변경, 약정 변경, 명의 변경, 번호 변경
    - **조회(Inquiry)**: 가입 상품 조회, 이용 내역 조회, 요금 조회, 잔여 데이터/혜택 조회, 계약 정보 조회
    - **결제/과금(Billing)**: 요금 납부, 자동이체 등록/변경, 미납 처리, 환불, 포인트/쿠폰 적용
    - **인증/본인확인(Verification)**: 본인인증(PASS/SMS/공동인증서), 미성년자 법정대리인 동의, 실명확인
    - **고객센터(Support)**: 문의 접수, 장애 신고, 상담 이력 조회, FAQ/챗봇
    - **알림/마케팅(Notification)**: 푸시 알림 수신/설정, 마케팅 수신 동의/철회, 이벤트 참여
    - **계정 관리(Account)**: 회원가입, 로그인/로그아웃, 비밀번호 변경/재설정, 회원 탈퇴, 프로필 관리
  - 각 Use Case에 대해:
    - Use Case ID 부여 (UC-{서비스약어}-{순번})
    - 구현 여부: ✅ 구현됨 / ❌ 미구현 / ⚠️ 부분 구현
    - 관련 소스 위치 (Controller, Service, Repository 등)
    - 관련 API 엔드포인트
    - 관련 화면 ID (02-screens 결과 참조)

- [ ] 서비스 특화 Use Case 발굴 (Service-Specific Use Case Discovery) `[KIRO]`
  - 공통 Use Case에 해당하지 않는 서비스 고유 비즈니스 로직을 식별하라
  - 발굴 방법:
    1. **Controller/Router 엔드포인트 전수 스캔**: 공통 Use Case에 매핑되지 않는 API를 추출
    2. **도메인 서비스 클래스 분석**: 비즈니스 로직 메서드 중 공통 카테고리에 속하지 않는 것
    3. **상태 전이 분석 연계**: 1-4 상태값 추출 결과에서 공통 Use Case로 설명되지 않는 전이 경로
    4. **이벤트/메시지 분석 연계**: 1-4 이벤트 흐름에서 공통 Use Case 외 비동기 처리
    5. **설정/상수 분석**: 서비스 고유 비즈니스 규칙이 반영된 설정값
  - 서비스 특화 Use Case 예시 (플랫폼별):
    - 미디어: VOD 구매/렌탈, 실시간 채널 전환, 녹화 예약, 시청 이력 기반 추천
    - 인증: PASS 인증 요청/결과 수신, 인증서 발급/갱신/폐기, 간편인증 등록
    - 메시징: 대량 발송, 발송 결과 콜백, 수신 거부 처리, 발송 이력 통계
    - 결제: 소액결제 한도 설정, 결제 승인/취소/부분취소, 정기결제 등록/해지
    - IoT: 디바이스 등록/해제, 펌웨어 OTA, 센서 데이터 수집, 원격 제어
    - 위치: 위치 조회 동의, 실시간 위치 추적, 지오펜싱 알림
  - 각 특화 Use Case에 동일한 메타정보 부여 (ID, 구현 여부, 소스 위치, API, 화면 ID)

- [ ] Use Case 시나리오 상세화 (Use Case Scenario Detail) `[KIRO+확정]`
  - 주요 Use Case(공통 + 특화)에 대해 시나리오를 상세화하라:
    - **정상 시나리오 (Happy Path)**: 사용자 행위 → 시스템 반응 단계별 기술
    - **대안 시나리오 (Alternative Path)**: 분기 조건에 따른 다른 경로
    - **예외 시나리오 (Exception Path)**: 에러/실패 시 처리 흐름
    - **사전 조건 (Precondition)**: 해당 Use Case 실행 전 필요 상태
    - **사후 조건 (Postcondition)**: 성공/실패 시 시스템 상태 변화
  - 시나리오 작성 시 코드에서 추출한 근거를 명시하라 (메서드명, 조건문, 에러코드 등)
  - PO/PM이 검토하여 누락된 시나리오를 보완할 수 있도록 `[KIRO+확정]` 태그

- [ ] Use Case 시나리오별 비기능(NFR) 체크포인트 연계 `[KIRO]`
  - 각 Use Case 시나리오에 아래 비기능 점검 항목을 교차 매핑하라:
  - **보안 체크포인트** (시나리오 단계별):
    - 인증/인가: 해당 UC 진입 시 인증 상태 확인, 권한 검증 로직 존재 여부
    - 개인정보: 시나리오에서 수집·조회·변경·전송되는 개인정보 항목 식별, 암호화/마스킹 적용 여부
    - 입력값 검증: 사용자 입력 단계에서 프론트/백엔드 이중 검증 여부 (XSS, Injection 방어)
    - 감사 로그: 민감 행위(개인정보 조회, 결제, 해지 등) 시 감사 로그 기록 여부
    - 세션/토큰: 시나리오 수행 중 세션 만료 시 처리 흐름 (재인증 유도, 데이터 유실 방지)
  - **품질 체크포인트** (시나리오 단계별):
    - 에러 처리: 각 단계에서 발생 가능한 에러 유형과 사용자 피드백 (에러 메시지, 재시도 안내)
    - 로딩/타임아웃: API 호출 단계의 로딩 UX, 타임아웃 시 사용자 안내, 재시도 로직
    - 데이터 정합성: 상태 변경 단계에서 트랜잭션 경계, 부분 실패 시 롤백/보상 처리
    - 동시성: 동일 UC를 복수 사용자/기기가 동시 수행 시 충돌 처리 (낙관적 락, 중복 방지)
    - 테스트 커버리지: 해당 시나리오(정상/대안/예외)에 대응하는 테스트 존재 여부
  - **성능 체크포인트**:
    - 응답시간 민감 단계 식별 (결제 승인, 실시간 조회 등)
    - 대용량 데이터 처리 단계 (목록 조회, 파일 업로드/다운로드)
    - 외부 연동 의존 단계 (외부 시스템 장애 시 fallback 시나리오)
  - **플랫폼별 차이 체크포인트**:
    - 동일 UC가 iOS/Android/STB에서 다르게 동작하는 단계 식별
    - 플랫폼별 제약사항 (STB 리모컨 조작, 모바일 생체인증 등)
  - 산출물 포맷: 시나리오 문서 내 각 단계에 `[보안]` `[품질]` `[성능]` 태그로 NFR 체크포인트 인라인 표기
  - 미충족 NFR 항목은 Phase 3 Gap 분석의 입력으로 연계

### 1-5. 비기능 현황

- [ ] 런타임 데이터 수집 안내 `[인터뷰]`
  - 이 항목은 KIRO 자동 추출 불가, 작업자가 수작업으로 수집해야 한다
  - APM/모니터링에서 API별 TPS, 응답시간, 에러율 수집 → manual-tasks.md P1-1
  - DB 슬로우 쿼리 / 실행 계획 수집 → manual-tasks.md P1-2
  - 분산 추적 데이터(Zipkin/Jaeger) 수집 → manual-tasks.md P1-3
  - 수집 결과를 `services/{서비스명}/docs/extraction/runtime-data.md`에 기록
- [ ] 비기능 현황 수집 (NFR Baseline Collection)
  - 코드에서 확인 가능: Timeout 설정, 커넥션 풀 크기, 캐시 TTL, Rate Limit 설정 `[KIRO]`
  - 코드에서 확인 불가(🔍): 실제 TPS, 응답시간, SLA/SLO → 모니터링 시스템 또는 담당자 확인 필요 `[인터뷰]`
- [ ] 미들웨어/캐시 레이어 인벤토리 추출 (Middleware & Cache Inventory) `[KIRO]`
  - Redis/Memcached/Ehcache 등 캐시 솔루션 식별
  - 캐시 키 네이밍 규칙 추출 (prefix, 구분자, 파라미터 조합 패턴)
  - 캐시 TTL 설정 수집 (키/용도별 만료 시간)
  - 캐시 전략 식별: Cache Aside, Write Through, Write Behind, Read Through
  - 캐시 무효화(Eviction) 정책: 수동 삭제, 이벤트 기반, TTL 만료
  - 세션 스토어로 사용되는 경우 세션 관련 키 패턴 별도 식별
  - 메시지 브로커(Kafka, RabbitMQ) 외 미들웨어: Elasticsearch, MongoDB 등 보조 저장소 식별

### 1-6. 품질 관련 추출

- [ ] 테스트 자동화 현황 추출 (Test Automation Inventory Extraction) `[KIRO]`
  - 서비스별 테스트 종류 식별: 단위 테스트, 통합 테스트, E2E 테스트, API 테스트 존재 여부
  - 테스트 프레임워크/도구 식별 (JUnit, Jest, Mocha, Selenium, Appium, Cypress 등)
  - 테스트 실행 환경: CI/CD 파이프라인 내 자동 실행 여부, 수동 실행 영역
  - test 디렉토리 구조, 테스트 설정 파일, CI 파이프라인 내 테스트 스텝 스캔
- [ ] 테스트-기능 추적 관계 추출 (Test-to-Feature Traceability Extraction) `[KIRO]`
  - 테스트 파일/메서드와 소스 파일/클래스 간 매핑 분석
  - mock/stub 대상 분석으로 테스트가 커버하는 의존성 범위 식별
  - 기능은 있는데 테스트가 없는 영역(Test Gap) 초안 식별
- [ ] 코드 변경 빈도 분석 (Change Frequency Analysis) `[KIRO+도구]`
  - Git 히스토리 기반 파일/모듈별 변경 빈도(Churn Rate) 산출
  - 변경 빈도 높은 + 테스트 커버리지 낮은 영역 = 고위험 영역 자동 식별
  - 커밋 빈도, 변경 라인 수 추이, 기여자 수 분석
- [ ] 로깅/모니터링 현황 추출 (Logging & Observability Extraction) `[KIRO]`
  - 서비스별 로깅 프레임워크, 로그 레벨 설정, 로그 포맷 현황
  - 구조화 로그 필수 필드 확인: trace_id, user/session, error_code, timestamp 포함 여부 `[CDR 6.1.1]`
  - 개인정보/민감정보 마스킹 규칙 적용 여부 `[CDR 6.1.1]`
  - 분산 추적(Distributed Tracing) 적용 여부 (Zipkin, Jaeger, OpenTelemetry 등)
  - 트레이싱 샘플링 정책 확인 (전수/비율/조건부) `[CDR 6.1.3]`
  - 헬스체크 엔드포인트 존재 여부 (/health, /readiness, /liveness)
  - 메트릭 수집 현황 (Prometheus, Micrometer, Grafana 연동 등)
  - RED/USE 메트릭 세트 정의 여부 확인 `[CDR 6.1.2]`
  - 알람/Alert 설정 파일 및 임계치 수집 `[CDR 6.1.4]`
- [ ] 의존성 버전 및 취약점 스캔 (Dependency Health Scan) `[KIRO+도구]`
  - 외부 라이브러리 버전 현황 및 최신 버전 대비 Gap
  - EOL(End of Life) 도달 라이브러리 식별
  - 알려진 CVE 취약점 보유 의존성 목록 (OWASP Dependency-Check, Snyk 등 활용)
- [ ] 동시성/스레드 안전성 패턴 추출 (Concurrency Pattern Extraction) `[KIRO]`
  - 멀티스레드/비동기 처리 패턴 식별 (synchronized, Lock, CompletableFuture, async/await 등)
  - 공유 자원 접근 패턴: 캐시, 세션, 전역 변수, DB 커넥션 풀
  - 레이스 컨디션 위험 영역 식별
  - 스레드 풀 설정, 트랜잭션 격리 수준 수집
- [ ] 데이터 정합성 검증 포인트 추출 (Data Integrity Extraction) `[KIRO]`
  - DB 트랜잭션 경계 식별: @Transactional 범위, 분산 트랜잭션 패턴 (Saga, 2PC 등)
  - 데이터 일관성 보장 메커니즘: 낙관적/비관적 락, 유니크 제약, FK 제약
  - 캐시-DB 간 정합성 전략 (Cache Aside, Write Through 등)
- [ ] 성능 병목 후보 영역 추출 (Performance Hotspot Extraction) `[KIRO]`
  - N+1 쿼리 패턴, 불필요한 전체 조회, 인덱스 미사용 쿼리 후보
  - 대용량 루프 내 외부 호출, 동기 블로킹 호출 패턴
  - 메모리 집약적 처리 (대용량 컬렉션, 스트림 미사용 전체 로딩)

### 1-7. 보안 관련 추출

> 📋 보안센터 Security Layer 체크리스트(217항목)와 대비 분석 필수 → [security-layer-checklist.md](security-layer-checklist.md)

- [ ] 크리덴셜/시크릿 관리 현황 스캔 (Credential Scan) `[KIRO]`
  - 소스코드 내 하드코딩된 ID/PW, API Key, 토큰 탐지 (git-secrets, truffleHog, gitleaks 등)
  - 설정 파일 내 크리덴셜 저장 방식 식별 (평문, 암호화, 환경변수, Vault 참조 등)
  - 외부 연동별 인증 방식 및 키 관리 현황 (API Key, OAuth Client, 인증서 등)
  - 시크릿 관리 도구 사용 여부 (HashiCorp Vault, AWS Secrets Manager, K8s Secret 등)
- [ ] 개인정보 처리 현황 추출 (PII/Privacy Data Extraction) `[KIRO]`
  - 개인정보 항목 식별: 이름, 전화번호, 주소, 이메일, 주민번호, CI/DI, 카드번호, 계좌번호 등
  - 코드 내 개인정보 필드 사용 위치 스캔 (Entity, DTO, API 파라미터, 로그 출력)
  - 개인정보 암호화 적용 여부: 저장 시(DB 컬럼 암호화), 전송 시(TLS), 표시 시(마스킹)
  - 개인정보 수집 동의 로직 식별 (약관 동의 화면, 동의 항목 관리)
  - 개인정보 보관 기간 및 파기 로직 식별 (배치 삭제, 익명화 처리)
  - 개인정보 제3자 제공/위탁 처리 흐름 (외부 연동 시 개인정보 전달 여부)
  - ※ 법적 적정성 판단은 `[인터뷰]` — 법무/개인정보보호 담당자 확인 필요
- [ ] 세션/토큰 관리 현황 추출 (Session & Token Management Extraction) `[KIRO]`
  - 세션 생성/만료/갱신 정책 식별 (세션 타임아웃, 슬라이딩 세션 등)
  - JWT 설정: 토큰 유효기간, Refresh Token 흐름, 서명 알고리즘
  - 세션 저장소 식별 (In-Memory, Redis, DB 등)
  - 동시 로그인 제어 정책 (중복 로그인 허용/차단, 기기 수 제한)
  - 세션 고정(Session Fixation) 방어 메커니즘
- [ ] 입력값 보안 검증 현황 추출 (Input Security Validation Extraction) `[KIRO]`
  - XSS 방어: 입력값 이스케이핑, 출력 인코딩, CSP 헤더 설정
  - SQL Injection 방어: PreparedStatement/Parameterized Query 사용 여부, ORM 사용 현황
  - CSRF 방어: CSRF 토큰 적용 여부, SameSite 쿠키 설정
  - Path Traversal 방어: 파일 업로드/다운로드 경로 검증
  - 보안 필터 체인 식별 (Spring Security Filter, Express middleware, WAF 룰 등)
- [ ] API 보안 현황 추출 (API Security Posture Extraction) `[KIRO]`
  - API별 인증 방식 매핑: 인증 없음 / API Key / OAuth / JWT / mTLS
  - Rate Limiting 설정 현황 (API Gateway, 코드 내 설정)
  - IP 화이트리스트/블랙리스트 설정
  - 민감 데이터 반환 API 식별 (개인정보, 내부 시스템 정보 노출 여부)
  - 내부 전용 API의 외부 노출 여부 (관리자 API, 디버그 API 등)
  - API Gateway/WAF 적용 현황
- [ ] 감사 로그(Audit Log) 현황 추출 (Audit Trail Extraction) `[KIRO]`
  - 개인정보 접근/변경 이력 로깅 현황 (개인정보보호법 의무사항)
  - 관리자 행위 로깅 (설정 변경, 사용자 관리, 권한 변경 등)
  - 로그인/로그아웃 이력 기록 현황
  - 감사 로그 저장소, 보관 기간, 위변조 방지 메커니즘 `[인터뷰]`
  - 코드에서 추출: AuditLog 관련 클래스/테이블, 로깅 인터셉터, 이벤트 리스너
- [ ] 데이터 암호화 현황 상세 추출 (Encryption Inventory Extraction) `[KIRO]`
  - 저장 시 암호화(At Rest): DB 컬럼 암호화, 파일 암호화, 디스크 암호화
  - 전송 시 암호화(In Transit): TLS 버전, 인증서 현황, 내부 서비스 간 통신 암호화
  - 암호화 알고리즘 현황: 사용 중인 알고리즘 목록 (AES-256, RSA-2048, SHA-256 등)
  - 취약 알고리즘 사용 여부: MD5, SHA1, DES, RC4 등 → 🔴 위험 표기
  - 암호화 키 관리: 키 저장 위치, 키 로테이션 주기, 키 접근 권한 `[인터뷰]`
- [ ] 보안 설정 하드닝 현황 추출 (Security Hardening Extraction) `[KIRO]`
  - HTTP 보안 헤더: HSTS, X-Frame-Options, X-Content-Type-Options, X-XSS-Protection, CSP, Referrer-Policy
  - CORS 설정: 허용 도메인, 메서드, 헤더 (와일드카드(*) 사용 시 ⚠️ 표기)
  - 디버그 모드/개발 설정 운영 잔존 여부 (debug=true, devtools, swagger-ui 노출 등)
  - 에러 응답 내부 정보 노출: 스택 트레이스, DB 스키마, 내부 IP 등
  - 쿠키 보안 설정: Secure, HttpOnly, SameSite 속성
  - 서버 정보 노출: Server 헤더, X-Powered-By 헤더 제거 여부
- [ ] 서드파티/외부 스크립트 보안 현황 추출 (Third-Party Script Extraction) `[KIRO]`
  - 프론트엔드 외부 스크립트 목록: 광고 SDK, 분석 도구(GA, Firebase Analytics), 채팅 위젯, 결제 SDK 등
  - 외부 CDN 의존성 및 SRI(Subresource Integrity) 적용 여부
  - 외부 스크립트의 데이터 수집 범위 (개인정보 전달 여부)
  - 코드에서 추출: HTML/JSP 내 외부 script 태그, CDN URL, SDK 초기화 코드
- [ ] 모바일 앱 쉴딩 현황 추출 (App Shielding Extraction) `[KIRO]`
  - **코드 난독화 (Obfuscation)**:
    - Android: ProGuard/R8 적용 여부, 난독화 규칙 파일(proguard-rules.pro) 분석, 난독화 제외 항목 확인
    - iOS: 난독화 도구 적용 여부 (iXGuard, SwiftShield 등), 빌드 설정 확인
    - React Native/Flutter: Hermes 엔진, 코드 번들 난독화 적용 여부
  - **앱 위변조 탐지 (Integrity Verification)**:
    - 앱 서명 검증 로직 (Android: PackageManager 서명 체크, iOS: 코드 서명 검증)
    - 실행 시 위변조 탐지 메커니즘 (해시 비교, 체크섬 검증)
    - 위변조 탐지 시 대응 로직 (앱 종료, 서버 통보, 기능 제한)
  - **루팅/탈옥 탐지 (Root/Jailbreak Detection)**:
    - 탐지 라이브러리 사용 여부 (RootBeer, SafetyNet/Play Integrity, DeviceCheck)
    - 다중 탐지 기법 적용 여부 (파일 존재 확인, su 바이너리, 시스템 속성 등)
    - 탐지 시 대응 정책 (차단/경고/로깅)
  - **디버깅/리버스 엔지니어링 방지 (Anti-Debug/Anti-RE)**:
    - 디버거 연결 탐지 (Android: Debug.isDebuggerConnected, iOS: sysctl/ptrace)
    - 에뮬레이터/시뮬레이터 탐지
    - 동적 분석 도구 탐지 (Frida, Xposed, Cydia Substrate)
  - **앱 쉴딩 솔루션 적용 현황**:
    - 상용 앱 쉴딩 솔루션 사용 여부 (AppSealing, Arxan, Promon, 에스이웍스 등)
    - 솔루션 적용 범위 (전체 앱/특정 모듈)
    - 솔루션 버전 및 업데이트 현황
  - **안전한 키 저장 (Secure Key Storage)**:
    - Android Keystore / iOS Keychain 활용 여부
    - 하드코딩된 키/시크릿 존재 여부 (크리덴셜 스캔 결과 연계)
  - Security Layer 체크리스트 169~181번(모바일 앱 환경) 대비 현황 매핑

### 1-8. 외부 연동 및 인프라

- [ ] 외부 연동 시스템 인벤토리 작성 `[KIRO]`
  - BSS/OSS, 과금(빌링), CRM, 본인인증, 결제(PG) 등 외부 시스템 식별
  - 연동 방식(API, MQ, FTP, DB Link 등), 프로토콜, 담당 조직 정리
  - 서비스별 외부 연동 의존성 매핑
  - 연동별 SLA, Rate Limit, 점검 시간대, 데이터 포맷 제약사항 (확인 가능한 범위 내, 미확인 시 🔍 표기) `[인터뷰]`
- [ ] 배포 토폴로지 추출 (Deployment Topology Extraction) `[KIRO]`
  - Dockerfile, k8s manifest, docker-compose, CI/CD 파이프라인에서 환경 구성 역추적
  - 서비스별 인스턴스 수, 리소스 할당, 로드밸런서, CDN 구성 `[인터뷰]`
  - Dev/Staging/Production 환경별 차이점 식별 `[인터뷰]`

### 1-9. STB 하드웨어 (IPTV/홈 사업)

- [ ] STB 하드웨어 리소스 기초 분석
  - STB 소스코드에서 프로세스 관리 로직 식별 (프로세스 기동/종료, watchdog, OOM handler) `[KIRO]`
  - 메모리 할당/해제 패턴, 리소스 풀 관리 코드 추출 `[KIRO]`
  - 기종별 분기 처리 로직 식별 (저사양/고사양 STB 분기 조건) `[KIRO]`
  - 리소스 임계치 상수/설정값 수집 `[KIRO]`
  - 기종별 실제 하드웨어 스펙 (CPU, RAM, 스토리지) `[인터뷰]`

### 1-10. UX 관련 추출

- [ ] 유저 인터랙션 패턴 추출 (User Interaction Pattern Extraction) `[KIRO]`
  - 화면별 이벤트 핸들러 스캔: onClick, onSwipe, onLongPress, onScroll, onKeyDown(STB 리모컨) 등
  - 제스처 라이브러리 사용 현황 (Hammer.js, React Native Gesture Handler 등)
  - 조작 → 화면 반응 매핑: 애니메이션 트리거, 화면 전이, 데이터 갱신, 상태 변경
  - STB 전용: 리모컨 키 매핑(방향키, 확인, 뒤로가기, 색상 버튼 등), 음성 명령 핸들러
- [ ] 입력 유효성 검증 규칙 추출 (Validation Rule Extraction) `[KIRO]`
  - 프론트엔드 검증: form validation 로직, 정규식, 필수/선택 필드, 길이/범위 제한
  - 백엔드 검증: DTO validation annotation (@NotNull, @Size, @Pattern 등), 커스텀 Validator
  - 프론트-백 이중 검증 여부 식별 (프론트만 있는 경우 ⚠️ 표기)
  - 검증 실패 시 사용자 피드백 메시지 목록 (인라인 에러, 토스트, 팝업 등)
- [ ] 로딩/대기 상태 UX 패턴 추출 (Loading State Extraction) `[KIRO]`
  - 화면별 로딩 인디케이터 유형 식별: 스피너, 스켈레톤 UI, 프로그레스바, 풀스크린 로딩
  - API 호출 중 사용자 조작 차단/허용 정책 (버튼 비활성화, 중복 클릭 방지 등)
  - 타임아웃 시 사용자 피드백: 재시도 버튼, 에러 메시지, 자동 재시도 로직
  - 빈 상태(Empty State) 화면 패턴: 데이터 없음, 검색 결과 없음, 네트워크 오류
- [ ] 다국어/접근성(A11y) 현황 추출 `[KIRO]`
  - i18n 적용 범위: 다국어 라이브러리 사용 여부, 언어 리소스 파일 구조, 지원 언어 목록
  - 미번역 영역 식별 (하드코딩된 한국어 문자열)
  - 접근성 현황: ARIA 속성(aria-label, aria-describedby, role), 키보드 네비게이션, 포커스 관리
  - 색상 대비, 폰트 크기 가변 지원, 스크린리더 대응 수준
- [ ] 딥링크/푸시 알림 진입 경로 추출 (Entry Point Extraction) `[KIRO]`
  - 딥링크 URL 스킴 및 유니버설 링크 라우팅 설정 스캔
  - 푸시 알림 클릭 시 화면 이동 핸들러 식별
  - 외부 진입 경로별 대상 화면 ID 매핑
  - 진입 시 필요한 파라미터 및 인증 상태 요구사항
  - STB: 채널 번호 직접 입력, EPG 바로가기, 앱 간 전환 경로
- [ ] A/B 테스트 및 피처 플래그 현황 추출 (Feature Flag Extraction) `[KIRO]`
  - 피처 플래그 관리 도구 사용 여부 (LaunchDarkly, Firebase Remote Config, 자체 구현 등)
  - 현재 활성화된 피처 플래그 목록 및 적용 화면/기능
  - A/B 테스트 분기 로직 식별 (코드 내 조건 분기)
  - 플래그별 대상 사용자 그룹 조건 (확인 가능한 범위 내) `[인터뷰]`

### 1-11. 소스 없는 서버

- [ ] 소스 없는 서버 분석 `[인터뷰]`
  - 배포 설정(Dockerfile, k8s manifest, CI/CD 파이프라인) 기반 추정
  - 네트워크 구성도/방화벽 정책에서 통신 관계 파악

### 1-12. AI/ML 활용 현황 추출 `[CDR 12장]`

- [ ] AI 사용 여부 식별 (AI Usage Declaration) `[KIRO]`
  - 서비스별 AI/ML 모듈 존재 여부 스캔 (TensorFlow, PyTorch, scikit-learn, OpenAI SDK, LangChain 등)
  - AI 적용 범위와 역할 식별: 추천, 분류, 요약, 자동결정, 이상탐지, 자동응답 등
  - AI 출력이 서비스 의사결정에 미치는 영향 분류: 자동 실행 / 승인 필요 / 참고용
  - Human-in-the-loop / on-the-loop / out-of-the-loop 수준 식별
- [ ] AI 모델/엔진 출처 및 버전 관리 현황 `[KIRO]`
  - 내부 모델 / 외부 SaaS(OpenAI, Claude 등) 구분
  - 모델 버전 고정 여부, 모델 레지스트리 사용 여부
  - 프롬프트/시스템 지시문 변경 관리 여부 (코드 내 하드코딩 vs 설정 관리)
- [ ] AI 입출력 통제 현황 `[KIRO]`
  - 학습/추론 데이터에 개인정보 포함 여부
  - 프롬프트 인젝션/데이터 유출 방어 로직 존재 여부 `[CDR 12.2.2]`
  - 입력값 검증/필터링 로직
- [ ] AI Fail-safe 현황 `[KIRO]`
  - AI 기능 비활성화 Feature Flag(Kill-switch) 존재 여부 `[CDR 12.2.1]`
  - AI 실패 시 수동 처리 전환 로직 존재 여부
  - AI 성능 저하 감지(Drift) 모니터링 설정 여부 `[CDR 12.2.3]`

### Phase 1 산출물

- 서비스별 기술 프로파일 시트 (각 항목에 확인 상태 표기)
- 설정 프로파일 비교표 (dev/staging/prod 환경별 차이)
- 디바이스/플랫폼 분기 코드 인벤토리 (iOS/Android/STB 분기 위치, 조건, 영향 범위)
- 화면 목록 (Screen Inventory): 화면 ID, 화면명(정규화 완료), 서비스, URL, 소스 위치
- 화면명 정규화 매핑 테이블 (원본 명칭 → 대표 명칭, 출처별)
- 크로스 플랫폼 화면 매핑표 (iOS↔Android↔STB 동일 화면 매핑, 플랫폼 전용 화면 식별)
- API 엔드포인트 목록 (초안)
- API 요청/응답 스키마 상세 (DTO/VO 필드 레벨, Swagger 미정비 레거시 포함)
- API 버전 관리 현황표 (버전 방식, Deprecated API, 무버전 API)
- 서비스 간 내부 API 계약서 (Feign/RestTemplate/gRPC 호출 체인)
- 비즈니스 규칙 목록 (초안, 확인 상태 포함)
- 에러 코드/예외 처리 패턴 목록
- 상태값 목록 및 전이 조건 (초안)
- 이벤트/메시지 토픽 목록 및 발행-구독 관계
- 배치/스케줄러 작업 인벤토리 (작업명, 주기, 의존 관계)
- 서비스 간 Call Flow 호출 체인 원시 데이터
- B2C 공통 Use Case 카탈로그 (가입/해지/변경/조회/결제/인증/고객센터/알림/계정)
- 서비스 특화 Use Case 목록 (플랫폼별 고유 비즈니스 로직)
- Use Case 시나리오 상세 (정상/대안/예외 경로, 사전·사후 조건)
- Use Case 시나리오별 NFR 체크포인트 매핑표 (보안/품질/성능 교차 점검 결과)
- Use Case Tree 계층 구조도 (EP→LC→FG→UC→Scenario 5단계 Tree)
- 비기능 현황 베이스라인 (확인 가능 항목 + 미확인 항목 구분)
- 미들웨어/캐시 레이어 인벤토리 (Redis 키 패턴, TTL, 캐시 전략)
- DB 쿼리 패턴 상세 (MyBatis XML, Native Query, Stored Procedure)
- 테스트 자동화 현황 인벤토리
- 테스트-기능 추적 매핑 (초안, Test Gap 포함)
- 코드 변경 빈도 리포트 (고위험 영역 식별 포함)
- 로깅/모니터링 현황 리포트
- 의존성 버전/취약점 리포트 (EOL, CVE 포함)
- 동시성/스레드 안전성 패턴 목록
- 데이터 정합성 검증 포인트 목록
- 성능 병목 후보 영역 목록
- 크리덴셜 관리 현황 리포트 (위험도 등급 포함)
- 개인정보 처리 현황 리포트 (항목별 수집·저장·처리·전송·파기 흐름)
- 세션/토큰 관리 현황 리포트
- 입력값 보안 검증 현황 리포트
- API 보안 현황 리포트 (인증 방식, Rate Limit, 민감 데이터 노출)
- 감사 로그 현황 리포트
- 데이터 암호화 인벤토리 (At Rest / In Transit / 알고리즘별)
- 보안 하드닝 현황 체크리스트
- 서드파티 스크립트 인벤토리
- 모바일 앱 쉴딩 현황 리포트 (난독화, 위변조 탐지, 루팅/탈옥, 디버깅 방지, 쉴딩 솔루션)
- 외부 연동 시스템 인벤토리 (SLA/제약사항 포함)
- 배포 토폴로지 (초안)
- STB 리소스 관리 현황
- 유저 인터랙션 패턴 목록
- 입력 유효성 검증 규칙 목록
- 로딩/대기 상태 UX 패턴 목록
- 다국어/접근성 현황 리포트
- 딥링크/푸시 알림 진입 경로 목록
- A/B 테스트 및 피처 플래그 목록
- 서비스 간 통신 관계 매트릭스
- SPOF 후보 목록 및 Failure Mode 분류표 `[CDR 3.2.1]`
- 메시지 전달 보장/순서/멱등성 현황표 `[CDR 4.3]`
- AI/ML 활용 현황 리포트 (사용 여부, 역할, 모델 출처, Fail-safe) `[CDR 12장]`
- 기술 부채 마커 목록 (TODO/FIXME/HACK 전수 스캔, 파일별 밀집도)
- Dead Code 리포트 (미사용 클래스/메서드/import, 판정 등급 🔴/🟡/🟠/🟢)
- 미사용 DB 리소스 리포트 (미참조 테이블/컬럼, 레거시 마이그레이션 잔존물)
- 미사용 API 엔드포인트 리포트 (미호출 API, Deprecated API 현황)
- 미사용 설정/리소스 리포트 (미참조 설정값, 리소스 파일, Bean)
- 코드 연대 분석 리포트 (파일별 마지막 수정일, 장기 미변경 코드)
- Repo 내부 공통 패키지 인벤토리 (기능별 그룹 분류, 참조 횟수, 영향 범위 등급)
- 사내 공통 라이브러리 인벤토리 (라이브러리명, 버전, 용도, 참조 수)
- 공통 모듈 의존 방향 다이어그램 (Mermaid flowchart, 역의존 ⚠️ 표기)
- 변경 영향도 분석 (Top 10, 영향 범위 등급 🔴/🟡/🟢)
- IaC 인벤토리 (Terraform/Helm/Ansible/CloudFormation 파일 기반 인프라 역추적)
- CI/CD 파이프라인 분석서 (빌드 스테이지, 배포 전략, 롤백 조건)
- DB 마이그레이션 이력 (Flyway/Liquibase 스키마 변경 이력)
- 런타임 트래픽 데이터 (수작업 수집 — manual-tasks.md P1-1~P1-3 참조)

---

## Phase 2: 아키텍처 View 생성 항목

### 2-1. 시스템 컨텍스트 & 컨테이너 View (C4 Level 1-2)
- [ ] 전체 시스템 컨텍스트 다이어그램 (외부 연동 시스템 인벤토리 기반, 누락 없이) `[KIRO]`
- [ ] 서비스별 컨테이너 다이어그램 (앱, API, DB, 메시지큐 등) `[KIRO]`
- [ ] 외부 연동 시스템 아키텍처 상세 `[KIRO]`
  - 연동 인터페이스별 시퀀스 다이어그램
  - 장애 시 영향 범위 식별
- → 대상: PO, PM

### 2-2. 컴포넌트 & 코드 View (C4 Level 3-4)
- [ ] 주요 서비스의 컴포넌트 다이어그램 `[KIRO]`
- [ ] 모듈 의존성 그래프 (내부 패키지 간) `[KIRO]`
- [ ] 핵심 비즈니스 로직 시퀀스 다이어그램 `[KIRO]`
- [ ] 상태 전이 다이어그램 (State Machine Diagram) `[KIRO]`
  - 핵심 도메인 객체별 상태 흐름도 (Phase 1 추출 결과 기반)
  - 정상 전이 경로 + 예외/이상 전이 경로 구분 표기
  - AI가 정상/예외 시나리오 도출 시 참조하는 핵심 산출물
- → 대상: PM, 개발자

### 2-3. 비즈니스 규칙 & 에러 처리 & Use Case View
- [ ] 비즈니스 규칙 카탈로그 완성 `[KIRO]`
  - 규칙 ID, 규칙명, 설명, 적용 서비스, 소스 위치, 확인 상태
  - 규칙 간 의존 관계 (규칙 A가 변경되면 규칙 B에 영향)
- [ ] 에러/예외 처리 패턴 맵 완성 `[KIRO]`
  - 서비스별 에러 코드 체계 (코드, 메시지, HTTP 상태, 사용자 노출 여부)
  - 예외 처리 흐름도 (정상 → 예외 발생 → 처리 → 사용자 응답)
  - 외부 연동 장애 시 fallback 시나리오 다이어그램
- [ ] Use Case 다이어그램 & 시나리오 문서 완성 `[KIRO+확정]`
  - Use Case Tree 계층 구조도 완성 (Mermaid mindmap — EP→LC→FG→UC→Scenario 5단계)
  - B2C 공통 Use Case 카탈로그 완성 (구현 여부, API/화면 매핑 포함)
  - 서비스 특화 Use Case 카탈로그 완성
  - 주요 Use Case별 시퀀스 다이어그램 (Mermaid sequenceDiagram)
  - Use Case ↔ API ↔ 화면 ↔ 비즈니스 규칙 교차 매핑 테이블
  - Use Case별 정상/대안/예외 시나리오 문서 (PO/PM 검토용)
  - Use Case 시나리오별 NFR 체크포인트 매핑 (보안/품질/성능 인라인 태그)
- → 대상: PM, 개발자, 품질센터, PO, 보안센터

### 2-4. 이벤트/메시지 흐름 View
- [ ] 이벤트 흐름도 (Event Flow Diagram) `[KIRO]`
  - 서비스 간 비동기 메시지 발행-구독 관계 시각화
  - 이벤트 토픽/큐별 스키마, 처리 순서, 재처리 정책
  - 동기 API 호출과 비동기 이벤트 흐름을 통합한 전체 흐름도
- [ ] 이벤트 스토밍 결과물 (Event Storming Output) `[KIRO]`
  - 도메인 이벤트 → 커맨드 → 애그리거트 매핑
- → 대상: PM, 개발자

### 2-5. 데이터 View
- [ ] ERD (논리/물리) `[KIRO]`
- [ ] 데이터 흐름도 (DFD) — 개인정보 포함 데이터 식별 `[KIRO]`
- → 대상: 개발자, 보안센터

### 2-6. UI/UX View
- [ ] 화면 카탈로그 완성 (화면 ID, 화면명, 캡처, 영역 구분) `[KIRO+확정]`
- [ ] 화면 흐름도 (Screen Flow) — 딥링크/푸시 외부 진입 경로 포함 `[KIRO]`
- [ ] 유저 인터랙션 패턴 맵 완성 (조작 → 반응 매핑표) `[KIRO]`
- [ ] 입력 유효성 검증 규칙 카탈로그 완성 (프론트/백엔드 이중 구조) `[KIRO]`
- [ ] 로딩/대기 상태 UX 패턴 맵 완성 (로딩 유형, 타임아웃, 빈 상태) `[KIRO]`
- [ ] 딥링크/푸시 알림 진입 경로 맵 완성 `[KIRO]`
- [ ] 다국어/접근성(A11y) 현황 맵 완성 `[KIRO]`
- [ ] A/B 테스트 및 피처 플래그 현황 맵 `[KIRO]`
- [ ] 프론트엔드 컴포넌트 트리 `[KIRO]`
- [ ] 화면 ↔ API ↔ 비즈니스 기능 매핑 테이블 `[KIRO]`
- [ ] 화면별 권한/역할 접근 매트릭스 `[KIRO]`
- → 대상: UX센터, PO

### 2-7. 보안 View
- [ ] 인증/인가 아키텍처 완성 `[KIRO]`
  - OAuth, SSO, 토큰 흐름 다이어그램
  - 서비스별 인증 방식 매트릭스
- [ ] 세션/토큰 관리 아키텍처 완성 `[KIRO]`
  - 세션 생성/만료/갱신 흐름도
  - JWT 토큰 라이프사이클 (발급 → 검증 → 갱신 → 만료)
  - 동시 로그인 제어 정책 다이어그램
  - 세션 하이재킹/고정 방어 메커니즘 현황
- [ ] 개인정보 처리 현황 맵 (PII/Privacy Data Map) 완성 `[KIRO]`
  - 개인정보 항목별 라이프사이클: 수집 → 저장 → 처리 → 전송 → 파기
  - 개인정보보호법/정보통신망법 요구사항 대비 현황 매트릭스 `[인터뷰]`
  - 개인정보 암호화 적용 현황 (항목별 At Rest / In Transit / 마스킹)
  - 동의 관리 현황: 수집 동의, 제3자 제공 동의, 마케팅 동의
  - 개인정보 접근 권한 매트릭스 (역할별 접근 가능 개인정보 항목)
- [ ] 입력값 보안 검증 아키텍처 완성 `[KIRO]`
  - XSS/SQL Injection/CSRF/Path Traversal 방어 현황 매트릭스
  - 보안 필터 체인 다이어그램 (요청 → 필터1 → 필터2 → ... → 컨트롤러)
  - 서비스별 보안 검증 적용 현황 (적용/미적용/부분적용)
- [ ] API 보안 현황 매트릭스 완성 `[KIRO]`
  - API별 인증 방식, Rate Limit, IP 제한 현황표
  - 민감 데이터 반환 API 목록 및 보호 조치 현황
  - 내부 전용 API 외부 노출 여부 점검 결과
  - API Gateway/WAF 적용 범위 다이어그램
- [ ] 감사 로그(Audit Log) 아키텍처 완성 `[KIRO]`
  - 감사 로그 대상 행위 목록 (개인정보 접근, 관리자 행위, 인증 이벤트)
  - 감사 로그 흐름도 (이벤트 발생 → 로그 기록 → 저장소 → 보관/파기)
  - 개인정보보호법 의무 로깅 항목 대비 현황 매트릭스
  - 위변조 방지 메커니즘 현황
- [ ] 데이터 암호화 아키텍처 완성 `[KIRO]`
  - 저장 시(At Rest) / 전송 시(In Transit) 암호화 현황 매트릭스
  - 암호화 알고리즘 현황표 (안전/취약 구분)
  - 암호화 키 관리 아키텍처 (키 저장, 로테이션, 접근 권한)
  - TLS 버전 및 인증서 현황
- [ ] 크리덴셜/시크릿 관리 현황 아키텍처 완성 `[KIRO]`
  - 서비스별 ID/PW, API Key, 토큰, 인증서 보관 방식 현황표
  - 위험도 등급: 🔴 Critical(평문 하드코딩) / 🟡 Warning(설정파일 평문) / 🟢 Safe(Vault/암호화)
  - 키 로테이션 정책 현황 (자동/수동/미설정)
  - 시크릿 관리 도구 사용 현황 및 권장 아키텍처
- [ ] 보안 하드닝 현황 체크리스트 완성 `[KIRO]`
  - HTTP 보안 헤더 적용 현황 매트릭스 (서비스별)
  - CORS 설정 현황 (와일드카드 사용 시 ⚠️)
  - 디버그/개발 설정 운영 잔존 점검 결과
  - 에러 응답 내부 정보 노출 점검 결과
  - 쿠키 보안 설정 현황
- [ ] 서드파티 스크립트 보안 현황 완성 `[KIRO]`
  - 외부 스크립트 인벤토리 (출처, 용도, 데이터 수집 범위)
  - SRI(Subresource Integrity) 적용 현황
  - 외부 스크립트 통한 개인정보 유출 위험 평가
- [ ] 모바일 앱 쉴딩 현황 아키텍처 완성 `[KIRO]`
  - 앱별 난독화 적용 현황 매트릭스 (Android/iOS/크로스플랫폼)
  - 위변조 탐지 메커니즘 현황 (서명 검증, 해시 비교, 대응 로직)
  - 루팅/탈옥 탐지 현황 (탐지 기법, 대응 정책)
  - 디버깅/리버스 엔지니어링 방지 현황
  - 앱 쉴딩 솔루션 적용 현황 (솔루션명, 버전, 적용 범위)
  - Security Layer 체크리스트 169~181번 대비 준수 현황
- [ ] 정적분석 취약점 리포트 `[KIRO+도구]`
- → 대상: 보안센터

### 2-8. 품질 View
- [ ] 테스트 코드 커버리지 현황 `[KIRO+도구]`
- [ ] 테스트 자동화 현황 맵 완성 (테스트 피라미드 시각화) `[KIRO]`
- [ ] 테스트-기능 추적 매트릭스 완성 (Test Gap 시각화) `[KIRO]`
- [ ] 코드 변경 빈도 및 결함 밀집도 맵 완성 (Churn Rate 히트맵) `[KIRO+도구]`
- [ ] 코드 복잡도/중복도 리포트 `[KIRO+도구]`
- [ ] 로깅/모니터링 현황 아키텍처 완성 `[KIRO]`
- [ ] 의존성 건강도 리포트 완성 (EOL, CVE 심각도별) `[KIRO+도구]`
- [ ] 동시성/스레드 안전성 아키텍처 완성 `[KIRO]`
- [ ] 데이터 정합성 아키텍처 완성 (트랜잭션 경계, 분산 트랜잭션, 캐시 정합성) `[KIRO]`
- [ ] 성능 병목 후보 영역 맵 완성 (위험도 등급별) `[KIRO]`
- [ ] 기술 부채 식별 목록 `[KIRO]`
- → 대상: 품질센터

### 2-9. STB 하드웨어 리소스 관리 View (IPTV/홈 사업 전용)
- [ ] STB 기종별 하드웨어 스펙 프로파일 `[인터뷰]`
- [ ] STB 프로세스/메모리 관리 아키텍처 (부팅 시퀀스, 상주 프로세스, OOM 정책) `[KIRO]`
- [ ] STB 리소스 모니터링 포인트 (임계치, 메모리 릭 패턴) `[KIRO]`
- [ ] STB 앱/UI 리소스 제약 분석 (렌더링 엔진, 이미지 제한, 동시 로딩 제한) `[KIRO]`
- [ ] STB 펌웨어/소프트웨어 업데이트 프로세스 (OTA, 롤백) `[인터뷰]`
- → 대상: PM, 개발자, 품질센터

### 2-10. 장애 복원력 및 DR View `[CDR 11장]`
- [ ] 장애 유형 분류 (App/DB/네트워크/외부/데이터손상) 및 Failure Mode Catalog `[KIRO]`
- [ ] SPOF 식별 결과 및 완화 방안 `[KIRO]`
- [ ] 장애 격리 아키텍처 (벌크헤드/큐 분리/리소스 분리) `[KIRO]` `[CDR 4.2.4]`
- [ ] DR 아키텍처 현황 (Active-Active/Standby/Backup) `[KIRO]`
- [ ] 데이터 복제/정합성 리스크 분석 `[KIRO]`
- [ ] RTO/RPO 현황 (코드/설정에서 추출 가능한 범위) `[KIRO]`
- → 대상: PM, 개발자, 품질센터

### 2-11. AI 거버넌스 View `[CDR 12장]`
- [ ] AI 활용 서비스 맵 (AI 사용/미사용 선언, 역할별 분류) `[KIRO]`
- [ ] AI 모델/엔진 인벤토리 (출처, 버전, 변경 관리 방식) `[KIRO]`
- [ ] AI 입출력 통제 현황 (개인정보, 프롬프트 보안) `[KIRO]`
- [ ] AI Fail-safe 아키텍처 (Kill-switch, 수동 전환, Drift 감지) `[KIRO]`
- [ ] AI 의사결정 흐름도 (자동 실행/승인 필요/참고용 분류) `[KIRO+확정]`
- → 대상: PM, 개발자, 보안센터

### 2-12. Layer Stack View (기술 계층 구조)
- [ ] Client Layer 식별: UI Framework, 상태 관리, 디자인 시스템, 렌더링 방식 `[KIRO]`
- [ ] Middleware/SDK Layer 식별: 사내 공통 라이브러리, 외부 SDK, 프레임워크 미들웨어 `[KIRO]`
- [ ] OS/Platform Layer 식별: 타겟 OS, 최소 버전, OS 레벨 API 의존 `[KIRO]`
- [ ] Firmware/Hardware Layer 식별 (STB/IoT 전용): 보안칩, CAS, 기종별 분기 `[KIRO]`
- [ ] Network Layer 추정: 프로토콜, OnPremise/Cloud/하이브리드 판정, VPN/mTLS/CDN `[KIRO]`
- [ ] Server Layer 식별: SW 아키텍처 패턴, 프레임워크, API Gateway, 캐시, MQ `[KIRO]`
- [ ] Infra/Deploy Layer 추정: 배포 환경, CI/CD, IaC, OnPrem vs Cloud 최종 판정 `[KIRO]`
- [ ] Layer Stack 다이어그램 작성 (Mermaid block-beta 또는 flowchart) `[KIRO]`
- → 대상: PM, 개발자, 인프라팀

### Phase 2 산출물

- C4 Model 기반 아키텍처 문서 세트
- 비즈니스 규칙 카탈로그
- 에러/예외 처리 패턴 맵
- Use Case 카탈로그 (공통 + 서비스 특화, 시퀀스 다이어그램 포함)
- Use Case Tree 계층 구조도 (Mermaid mindmap — EP→LC→FG→UC→Scenario)
- Use Case 시나리오별 NFR 체크포인트 매핑표 (보안/품질/성능 교차 점검)
- Use Case ↔ API ↔ 화면 ↔ 비즈니스 규칙 교차 매핑 테이블
- 상태 전이 다이어그램 세트
- 이벤트/메시지 흐름도
- 화면 카탈로그 (화면 ID/명칭/영역 구분 포함)
- 크로스 플랫폼 화면 매핑표 (iOS↔Android↔STB 동일 화면 매핑, 플랫폼 전용 화면 식별)
- 화면-API-기능 매핑 테이블
- 유저 인터랙션 패턴 맵
- 입력 유효성 검증 규칙 카탈로그
- 로딩/대기 상태 UX 패턴 맵
- 딥링크/푸시 알림 진입 경로 맵
- 다국어/접근성 현황 맵
- A/B 테스트 및 피처 플래그 현황 맵
- 화면별 권한/역할 접근 매트릭스
- 인증/인가 아키텍처 (세션/토큰 관리 포함)
- 개인정보 처리 현황 맵 (라이프사이클, 법적 요구사항 대비)
- 입력값 보안 검증 아키텍처
- API 보안 현황 매트릭스
- 감사 로그 아키텍처
- 데이터 암호화 아키텍처 (At Rest / In Transit / 알고리즘 / 키 관리)
- 크리덴셜/시크릿 관리 현황 아키텍처 (위험도 등급 포함)
- 보안 하드닝 현황 체크리스트
- 서드파티 스크립트 보안 현황
- 모바일 앱 쉴딩 현황 아키텍처 (난독화, 위변조 탐지, 루팅/탈옥, 쉴딩 솔루션)
- 정적분석 취약점 리포트
- 테스트 코드 커버리지 현황
- 테스트 자동화 현황 맵 및 테스트-기능 추적 매트릭스
- 코드 변경 빈도/결함 밀집도 맵
- 코드 복잡도/중복도 리포트
- 로깅/모니터링 현황 아키텍처
- 의존성 건강도 리포트
- 동시성/스레드 안전성 아키텍처
- 데이터 정합성 아키텍처
- 성능 병목 후보 영역 맵
- 기술 부채 식별 목록
- 프론트엔드 컴포넌트 트리
- STB 리소스 관리 아키텍처
- 배포 토폴로지 (인프라 View)
- 비기능 요구사항 베이스라인 문서
- 외부 연동 시스템 아키텍처
- Stakeholder별 맞춤 View 문서
- 장애 유형 분류 및 Failure Mode Catalog `[CDR 11장]`
- SPOF 식별 및 완화 방안 리포트 `[CDR 3.2.1]`
- 장애 격리 아키텍처 (벌크헤드/큐 분리) `[CDR 4.2.4]`
- DR 아키텍처 현황 리포트 `[CDR 11장]`
- AI 활용 서비스 맵 및 모델 인벤토리 `[CDR 12장]`
- AI Fail-safe 아키텍처 리포트 `[CDR 12장]`
- AI 의사결정 흐름도 `[CDR 12장]`
- Layer Stack View (Client/Middleware/OS/Firmware/Network/Server/Infra 계층 다이어그램)

---

## Phase 3: 추적성 매핑 및 Gap 분석 항목

- [ ] 기존 요구사항(PO 요구사항, PM 명세서)과 코드 기능 매핑 `[KIRO+확정]`
- [ ] 화면-API 영향도 분석 매트릭스 작성 `[KIRO]`
  - 특정 화면 수정 시 영향받는 API, 서비스, DB 테이블 역추적
  - PO 변경 요청(CR) 시 영향 범위를 즉시 파악할 수 있는 참조 문서
- [ ] 문서화되지 않은 기능(Undocumented Feature) 식별 `[KIRO]`
- [ ] 설계 문서 없이 구현된 영역 Gap 리포트 `[KIRO]`
- [ ] Dead Code / 미사용 API 식별 `[KIRO]`
  - Step 1b(`01b-dead-code-analysis`) 결과를 기반으로 최종 정리
  - Phase 1 이후 추가 발견된 미사용 코드를 보완
  - 🔴 Dead 판정 항목의 개발 리드 확정 결과 반영
- [ ] 미사용 화면 / 도달 불가 화면(Orphan Screen) 식별 `[KIRO]`
- [ ] 유저 시나리오 완전성 Gap 분석 `[KIRO]`
  - B2C 공통 Use Case 중 미구현/부분구현 항목 식별 (가입/해지/변경/조회/결제/인증/고객센터/알림/계정)
  - 서비스 특화 Use Case 중 시나리오 상세화가 누락된 항목 식별
  - Use Case별 예외 시나리오 누락 Gap (Happy Path만 구현, Exception Path 미구현)
  - Use Case ↔ 테스트 커버리지 교차 분석 (Use Case는 있으나 테스트가 없는 영역)
  - 화면별 인터랙션 패턴 중 예외 처리가 누락된 조작 식별
  - 입력 유효성 검증이 프론트엔드에만 있고 백엔드에 없는 필드 식별
  - 로딩/타임아웃 처리가 누락된 API 호출 지점 식별
  - 딥링크 진입 시 인증 체크 누락 경로 식별
  - 다국어 미적용 / 접근성 미준수 영역 Gap 리포트
  - 피처 플래그 잔존 코드 (실험 종료 후 미정리) 식별
  - 크로스 플랫폼 화면 불일치 Gap: iOS에만/Android에만 있는 화면, 동일 화면의 UX 흐름 차이
  - 플랫폼 분기 코드 중 한쪽만 업데이트되고 다른 쪽은 미반영된 영역 식별
  - **시나리오별 NFR 체크포인트 미충족 Gap** (Phase 1 NFR 매핑 결과 기반):
    - 보안 미충족: 인증 없이 접근 가능한 UC 단계, 개인정보 암호화/마스킹 미적용 단계, 감사 로그 미기록 민감 행위
    - 품질 미충족: 예외 시나리오에 에러 처리 미구현, 로딩/타임아웃 UX 미적용 API 호출, 테스트 미존재 시나리오
    - 성능 미충족: 응답시간 민감 단계에 캐시/최적화 미적용, 외부 연동 fallback 미구현
    - 정합성 미충족: 상태 변경 단계에 트랜잭션 미적용, 동시성 충돌 미처리
- [ ] 품질 인프라 Gap 분석 `[KIRO]`
  - 테스트 자동화 미적용 서비스/모듈 Gap 리포트
  - 로깅/모니터링 미적용 영역 → 장애 시 원인 추적 불가 영역 식별
  - 분산 추적 미적용 서비스 간 호출 경로 식별
  - 헬스체크 미구현 서비스 목록
- [ ] 보안 Gap 분석 `[KIRO]`
  - 개인정보보호법 의무사항 대비 미충족 영역 식별 `[인터뷰]`
    - 개인정보 암호화 미적용 항목
    - 접근 이력 로깅 미적용 영역
    - 동의 관리 누락 영역
    - 보관 기간 초과 데이터 미파기 영역
  - 입력값 보안 검증 미적용 API/화면 식별 (XSS, SQL Injection 방어 누락)
  - API 인증 미적용 엔드포인트 식별 (인증 없이 접근 가능한 API)
  - 취약 암호화 알고리즘 사용 영역 (MD5, SHA1, DES 등)
  - 보안 헤더 미적용 서비스 식별
  - 감사 로그 미적용 영역 (개인정보 접근 로깅 누락)
  - 크리덴셜 평문 저장 영역 (🔴 Critical 등급 항목)
  - 서드파티 스크립트 SRI 미적용 / 개인정보 유출 위험 영역
  - 모바일 앱 쉴딩 미적용/미흡 영역 (난독화 미적용, 루팅/탈옥 탐지 없음, 위변조 탐지 없음, 쉴딩 솔루션 미적용)
- [ ] 코드 품질 위험도 종합 매트릭스 `[KIRO]`
  - 변경 빈도 × 테스트 커버리지 × 코드 복잡도 × 동시성 위험 교차 분석
  - 서비스/모듈별 종합 품질 위험도 등급 산출
  - 기능 변경 시 회귀 테스트 범위 자동 산정 근거
- [ ] 데이터 정합성 위험 시나리오 검증 `[KIRO]`
  - 트랜잭션 경계 밖에서 데이터 변경이 일어나는 영역 식별
  - 캐시 무효화 누락 시나리오 식별
  - 분산 환경에서 정합성 깨질 수 있는 경로 식별
- [ ] STB 리소스 관련 Gap 분석 `[KIRO]`
  - 기종별 리소스 한도 대비 실제 사용량 Gap `[인터뷰]`
  - 저사양 STB에서 성능 병목 예상 지점 식별
  - 기능 추가 시 STB 리소스 영향도 판단 기준 수립
- [ ] 서비스 간 숨겨진 의존성(Hidden Dependency) 발굴 `[KIRO]`
- [ ] SPOF 미해소 Gap 분석 `[KIRO]` `[CDR 3.2.1]`
  - Phase 1에서 식별된 SPOF 중 완화 방안이 없는 항목
  - Failure Mode별 대응 미설계 영역
- [ ] 메시지 안정성 Gap 분석 `[KIRO]` `[CDR 4.3]`
  - 멱등성 미적용 비동기 처리 영역
  - DLQ 미설정 메시지 큐
  - 순서 보장 필요하나 미적용 영역
- [ ] AI 거버넌스 Gap 분석 `[KIRO]` `[CDR 12장]`
  - AI Kill-switch(Feature Flag) 미구현 서비스
  - AI 모델 변경 관리 미적용 영역
  - 프롬프트 인젝션 방어 미적용 영역
  - AI Drift 모니터링 미설정 영역
- [ ] 서비스 간 교차 검증 `[KIRO+확정]`
  - 서비스 A가 호출하는 API와 서비스 B가 제공하는 API 계약 일치 여부 검증
  - 이벤트 Producer 스키마와 Consumer 스키마 일치 여부 검증
  - 서비스 간 공유 용어(도메인 객체명, 상태값, 에러코드) 일관성 검증
  - 불일치 항목은 `cross-service-inconsistency.md`에 기록
  - 최종 판단은 PM/개발 리드가 확정 → manual-tasks.md P3-1
- [ ] 서비스 합리화 판단 `[KIRO+확정]`
  - 기능 중복 서비스 식별: 동일/유사 기능을 2개 이상 서비스가 구현하는 경우
  - 저사용 서비스 식별: 런타임 트래픽 데이터(P1-1) 기반 호출 빈도 극저 서비스
  - 고비용 서비스 식별: 코드 복잡도 높고 변경 빈도 높으나 비즈니스 가치 낮은 서비스
  - 통합/폐기/재작성 후보 판단 → PM+PO+개발 리드 확정 (manual-tasks.md P3-5)
- [ ] 용어 사전 최종 검증 및 확정 `[KIRO+확정]`
  - Phase 0 초안을 실제 코드/화면 분석 결과와 대조하여 보완
  - 화면명 정규화 결과 최종 검증: 모든 산출물에서 대표 명칭이 일관되게 사용되는지 크로스체크
  - 신규 발견된 동의어/이형어 추가 등록

### Phase 3 산출물

- 요구사항-코드 추적 매트릭스
- 화면-API 영향도 매트릭스 (화면 ID 기준)
- Dead Code / 미사용 API 리포트
- 미사용 화면 / Orphan Screen 리포트
- 유저 시나리오 완전성 Gap 리포트 (NFR 체크포인트 미충족 포함)
- 크로스 플랫폼 화면/코드 불일치 Gap 리포트 (iOS↔Android↔STB)
- 품질 인프라 Gap 리포트
- 보안 Gap 리포트 (개인정보보호법 대비, 입력 보안, API 보안, 암호화, 감사 로그, 하드닝, 앱 쉴딩)
- 코드 품질 위험도 종합 매트릭스 (서비스/모듈별 등급)
- 데이터 정합성 위험 시나리오 리포트
- STB 리소스 Gap 분석 리포트
- 서비스 간 숨겨진 의존성 리포트
- SPOF 미해소 Gap 리포트 `[CDR 3.2.1]`
- 메시지 안정성 Gap 리포트 (멱등성/DLQ/순서 보장) `[CDR 4.3]`
- AI 거버넌스 Gap 리포트 `[CDR 12장]`
- 서비스 간 교차 검증 리포트 (API 계약/이벤트 스키마/용어 불일치)
- 서비스 합리화 판단 리포트 (기능 중복/저사용/고비용 서비스 → 통합/폐기/재작성 후보)
- Gap 분석 리포트
- 용어 사전 (Glossary) 확정본
- 개선 권고사항
