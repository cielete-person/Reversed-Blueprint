---
inclusion: manual
---

# Step 3: API 엔드포인트 추출 및 DB 스키마 역추적

> 참조: #[[file:project/extraction-checklist.md]] — 1-3. API 및 데이터

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

## 완료 기준
- 모든 Controller/Router의 엔드포인트가 목록화됨
- API 요청/응답 스키마가 DTO/VO 필드 레벨까지 추출됨 (Swagger 미정비 레거시 포함)
- API 버전 관리 전략이 식별되고 무버전/Deprecated API가 표기됨
- 서비스 간 내부 API 계약(Feign/RestTemplate/gRPC)이 호출 체인으로 정리됨
- DB 쿼리 패턴(MyBatis XML, Native Query, SP)이 수집됨
- DB 마이그레이션 이력(Flyway/Liquibase)이 수집됨 (스크립트 없는 경우 🔍 표기)
- ERD 초안이 Mermaid 다이어그램으로 작성됨
- 서비스 간 호출 관계가 매트릭스로 정리됨
