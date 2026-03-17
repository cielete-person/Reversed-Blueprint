---
inclusion: manual
---

# Step 3: API 엔드포인트 추출 및 DB 스키마 역추적

> 참조: #[[file:project/extraction-checklist.md]] — 1-3. API 및 데이터

## 선행 산출물 참조

- **Step 1b(Dead Code)**: `01b-dead-code/` 산출물에서 Dead 판정(🔴)된 API 엔드포인트, 미사용 DB 테이블/컬럼은 본 Step 산출물에서 제외하라. 조건부 Dead(🟡)는 포함하되 `⚠️ Dead 의심` 표기.
- **Step 1c(공통 모듈)**: `01c-common-modules/` 산출물에서 공통 데이터 접근 모듈(공통 Repository, 공통 DAO)과 공통 API 클라이언트를 식별하여, API/DB 분석 시 공통 모듈 경유 여부를 표기하라.

## ⚡ 컨텍스트 복원 (새 세션 시작 시)

> `services/{서비스명}/docs/extraction/_context-notes.md`를 먼저 읽어 이전 Step 인사이트를 복원하세요.
> 이 Step 완료 후 `_context-notes.md`의 해당 섹션에 핵심 발견사항을 기록하세요.

## 목표

REST API 엔드포인트를 자동 추출하고, DB 스키마를 역추적하여 ERD 초안을 생성한다.

## 작업 절차

### 1. API 엔드포인트 추출
- REST Controller/Router 파일을 스캔하라
  - Spring: `@RestController`, `@RequestMapping`, `@GetMapping` 등
  - Express/Koa: `router.get()`, `router.post()` 등
  - FastAPI: `@app.get()`, `@app.post()` 등
- 각 엔드포인트의 메타정보를 추출하라:
  - HTTP 메서드, URL 경로, 요청/응답 DTO, 인증 필요 여부
- 서비스 간 호출 관계를 식별하라 (Feign, RestTemplate, gRPC, HttpClient 등)

### 1-1. API 요청/응답 스키마 상세 추출 (Swagger 미정비 레거시 대응)
- Swagger/OpenAPI가 없거나 미정비된 경우, Controller 파라미터 + DTO/VO 클래스에서 직접 추출하라
- 요청 DTO: 필드명, 타입, 필수/선택(@NotNull 등), validation 어노테이션, 기본값
- 응답 DTO: 필드명, 타입, 중첩 구조(Wrapper/Page 등), 에러 응답 포맷
- 공통 응답 래퍼 패턴 식별 (ApiResponse, CommonResult 등)

### 1-2. API 버전 관리 전략 추출
- 버전 관리 방식 식별: URL 경로(/v1/, /v2/), 헤더(Accept-Version), 쿼리 파라미터
- 버전 미적용 API 식별 (레거시 무버전 API → ⚠️ 표기)
- Deprecated API 식별 (@Deprecated, 주석, 미사용 추정)

### 1-3. 서비스 간 내부 API 계약 추출
- Feign Client 인터페이스에서 호출 대상 서비스, URL, 요청/응답 타입 추출
- RestTemplate/WebClient 호출부에서 URL 패턴, 요청 빌더, 응답 파싱 로직 추출
- gRPC proto 파일 존재 시 서비스/메서드 정의 수집
- 내부 API 호출 체인 시각화 (A→B→C 순차 호출, 병렬 호출 구분)
- 호출 시 타임아웃/리트라이/서킷브레이커 설정 수집

### 1-4. REST API 멱등성 / 페이지네이션 패턴
- PUT/DELETE 멱등성 보장 여부, POST 멱등키(Idempotency-Key) 사용 여부
- 페이지네이션 방식: offset/limit, cursor-based, keyset
- 대용량 조회 API의 최대 페이지 크기 제한 설정

### 2. DB 스키마 역추적
- ORM Entity/Mapper를 분석하여 테이블 구조를 추출하라
  - JPA: `@Entity`, `@Table`, `@Column`
  - MyBatis: Mapper XML, `@Select` 등
  - Sequelize/TypeORM: 모델 정의
- 테이블 간 관계(FK, Join)를 식별하라
- DDL 파일이 있는 경우 수집하라

### 2-1. DB 쿼리 패턴 상세 추출
- MyBatis XML Mapper: SQL 문 전수 수집, 동적 SQL(if/choose/foreach) 패턴 식별
- JPA Native Query / @Query 어노테이션 수집
- Stored Procedure / Function 호출 목록
- N+1 쿼리 위험 패턴 식별 (Lazy Loading + 루프 내 조회)
- 복잡 조인/서브쿼리 목록 (성능 병목 후보)

### 2-2. DB 마이그레이션 이력 추출
- Flyway(V*.sql), Liquibase(changelog), Django migrations 등 마이그레이션 스크립트를 식별하라
- 스키마 변경 이력을 정리하라: 변경일(파일명/타임스탬프), 변경 내용(ADD/ALTER/DROP), 대상 테이블
- 주요 스키마 변경점을 요약하라 (컬럼 추가/삭제, 인덱스 변경, 테이블 분리/통합)
- 마이그레이션 스크립트가 소스 Repo에 없는 경우 → `🔍 인터뷰필요` (manual-tasks.md P1-6)

### 3. 서비스 간 통신 매트릭스
- 서비스 A → 서비스 B 호출 관계를 매트릭스로 정리하라
- 동기(API) / 비동기(MQ) 구분하라

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

> 대용량 Repo에서 이 Step의 항목을 "해당 없음"으로 판정하기 전에, 아래 패턴을 반드시 검색하라.

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| Controller/Router | `*Controller*`, `*Resource*`, `*Router*`, `*Endpoint*`, `*Handler*` | `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, `router.get`, `@app.get` |
| DTO/VO | `*Dto*`, `*DTO*`, `*Vo*`, `*VO*`, `*Request*`, `*Response*` | `@RequestBody`, `@ResponseBody`, `@Valid`, `@NotNull` |
| Entity/Model | `*Entity*`, `*Model*`, `*Domain*` | `@Entity`, `@Table`, `@Column`, `@Id`, `@ManyToOne` |
| Repository/DAO | `*Repository*`, `*Dao*`, `*DAO*`, `*Mapper*` | `@Repository`, `JpaRepository`, `CrudRepository`, `@Mapper` |
| Mapper XML | `*Mapper.xml`, `*-mapper.xml`, `sqlmap/**` | `<mapper`, `<select`, `<insert`, `<update`, `<delete`, `resultMap` |
| DB 마이그레이션 | `V*.sql`, `*changelog*`, `*migration*`, `flyway/**`, `db/migrate/**` | `CREATE TABLE`, `ALTER TABLE`, `flyway`, `liquibase` |
| Feign/gRPC | `*Client*`, `*FeignClient*`, `*.proto` | `@FeignClient`, `RestTemplate`, `WebClient`, `gRPC`, `ManagedChannel` |

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/03-api-data/` 에 아래 파일 생성:
- `api-endpoints.md` — API 엔드포인트 목록
- `api-schema-detail.md` — API 요청/응답 스키마 상세 (DTO/VO 필드 레벨)
- `api-versioning.md` — API 버전 관리 현황 (버전 방식, Deprecated, 무버전)
- `internal-api-contracts.md` — 서비스 간 내부 API 계약 (Feign/RestTemplate/gRPC)
- `db-query-patterns.md` — DB 쿼리 패턴 상세 (MyBatis XML, Native Query, SP)
- `db-migration-history.md` — DB 마이그레이션 이력 (Flyway/Liquibase 스키마 변경 이력)
- `erd.md` — ERD 초안 (Mermaid erDiagram 사용)
- `service-communication-matrix.md` — 서비스 간 통신 매트릭스

## 💡 효과적인 추가 프롬프트 예시

> 출처: [prompt-cookbook.md](../project/prompt-cookbook.md)

**MyBatis XML 누락 시:**
```
src/ 하위에서 *.xml 파일 중 <mapper 또는 <select 태그가 포함된 파일을 모두 찾아줘.
sqlmap/, mapper/, mybatis/ 폴더도 확인해줘.
```

**DB 마이그레이션 누락 시:**
```
flyway, liquibase, migration, V1__, V2__ 키워드로 파일을 검색해줘.
db/, sql/, resources/db/ 폴더도 확인해줘.
```

**내부 API 호출 누락 시:**
```
@FeignClient, RestTemplate, WebClient, HttpClient 사용부를 모두 찾아줘.
URL이 하드코딩된 경우와 설정에서 주입되는 경우를 구분해줘.
```

## 🔧 작업자 수작업 보충 항목

> 이 Step에서 KIRO 자동 추출만으로는 완성할 수 없는 항목입니다.
> 산출물 생성 후 아래 항목을 작업자가 직접 보충해 주세요.
> 상세: [manual-tasks.md](../project/manual-tasks.md)

| 항목 ID | 항목명 | 유형 | 해야 할 일 |
|---|---|---|---|
| P1-1 | 런타임 트래픽 데이터 수집 | `[런타임]` | API별 호출 빈도(TPS), 평균/P95/P99 응답시간, 에러율 상위 API를 APM에서 수집 |
| P1-2 | 슬로우 쿼리/DB 성능 수집 | `[런타임]` `[도구실행]` | DB 모니터링에서 슬로우 쿼리 목록 수집, KIRO가 추정한 N+1 병목과 실제 DB 부하 비교 |
| P1-6 | DB 마이그레이션 이력 수집 | `[도구실행]` | Flyway/Liquibase 마이그레이션 스크립트가 Repo에 없는 경우 별도 수집 |
| P1-8 | 외부 연동 SLA 확인 | `[인터뷰]` | 외부 연동 시스템별 SLA, Rate Limit, 점검 시간대, 데이터 포맷 제약사항 확인 |

## 완료 기준
- 모든 Controller/Router의 엔드포인트가 목록화됨
- API 요청/응답 스키마가 DTO/VO 필드 레벨까지 추출됨 (Swagger 미정비 레거시 포함)
- API 버전 관리 전략이 식별되고 무버전/Deprecated API가 표기됨
- 서비스 간 내부 API 계약(Feign/RestTemplate/gRPC)이 호출 체인으로 정리됨
- DB 쿼리 패턴(MyBatis XML, Native Query, SP)이 수집됨
- DB 마이그레이션 이력(Flyway/Liquibase)이 수집됨 (스크립트 없는 경우 🔍 표기)
- ERD 초안이 Mermaid 다이어그램으로 작성됨
- 서비스 간 호출 관계가 매트릭스로 정리됨

## 🔄 역방향 피드백 체크 (이전 산출물 업데이트)

이 Step에서 발견한 내용이 이전 Step 산출물에 영향을 주는지 확인하세요:

- [ ] **Step 01 → code-structure.md 업데이트 필요?**
  - API 추출에서 발견한 내부 모듈 간 호출 관계가 코드 구조에 반영되어 있는지 확인
  - 새로 발견된 유틸리티/헬퍼 모듈이 코드 구조에 누락되어 있는지 확인
- [ ] **Step 02 → screen-inventory.md 업데이트 필요?**
  - API 엔드포인트에서 역추적한 화면이 화면 인벤토리에 누락되어 있는지 확인
  - API 응답 데이터가 화면에 어떻게 바인딩되는지 화면 흐름에 반영되어 있는지 확인
- [ ] **Step 01c → common-module 그룹핑 업데이트 필요?**
  - 여러 API에서 공통으로 사용하는 DTO/모델이 공통 모듈로 분류되어 있는지 확인

> 업데이트가 필요한 경우, 해당 산출물 파일을 직접 수정하고 `_context-notes.md`에 변경 사유를 기록하세요.
