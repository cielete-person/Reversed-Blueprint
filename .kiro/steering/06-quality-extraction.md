---
inclusion: manual
---

# Step 6: 품질 관련 설계도 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-6. 품질 관련 추출
> 📖 기술 용어: [용어 사전](../../glossary.md) — EOL, CVE, OOM, TPS, APM, CI/CD 등

## 선행 산출물 참조

- **Step 04(비즈니스 로직)**: `04-business-logic/use-cases.md`에서 Use Case 목록을, `04-business-logic/state-machines.md`에서 상태 전이를 참조하라. 테스트-기능 추적(2번) 시 Use Case별 테스트 존재 여부를 교차 매핑하고, 상태 전이 경로별 테스트 커버리지를 확인하는 데 활용.
- **Step 1b(Dead Code)**: `01b-dead-code/` 산출물에서 Dead 판정(🔴)된 코드의 테스트는 품질 분석에서 제외하라. Dead Code를 테스트하는 테스트 코드 자체도 Dead로 분류하라.
- **Step 1c(공통 모듈)**: `01c-common-modules/` 산출물에서 공통 모듈의 테스트 커버리지를 별도 집계하라. 공통 모듈은 영향 범위가 넓으므로(🔴 High) 테스트 미비 시 고위험으로 분류하라.

## ⚡ 컨텍스트 복원 (새 세션 시작 시)

> `services/{서비스명}/docs/extraction/_context-notes.md`를 먼저 읽어 이전 Step 인사이트를 복원하세요.
> 이 Step 완료 후 `_context-notes.md`의 해당 섹션에 핵심 발견사항을 기록하세요.

## 목표

품질센터가 참조할 수 있는 품질 설계도를 소스코드에서 역추적한다.

## 작업 절차

### 1. 테스트 자동화 현황
- 서비스별 테스트 종류(단위/통합/E2E/API)를 식별하라
- 테스트 프레임워크/도구를 식별하라 (JUnit, Jest, Selenium 등)
- CI/CD 파이프라인 내 자동 실행 여부를 확인하라

### 2. 테스트-기능 추적 관계
- 테스트 파일/메서드와 소스 파일/클래스 간 매핑을 분석하라
- 기능은 있는데 테스트가 없는 영역(Test Gap)을 식별하라

### 3. 코드 변경 빈도
- Git 히스토리 기반 파일/모듈별 변경 빈도(Churn Rate)를 산출하라
- 변경 빈도 높은 + 테스트 커버리지 낮은 = 고위험 영역을 식별하라

### 4. 로깅/모니터링 현황
- 서비스별 로깅 프레임워크, 로그 레벨, 분산 추적 적용 여부를 확인하라
- 헬스체크 엔드포인트, 메트릭 수집 현황을 식별하라

### 5. 의존성 건강도
- 외부 라이브러리 버전 현황, EOL 라이브러리, CVE 취약점을 식별하라

### 6. 동시성/스레드 안전성
- 멀티스레드/비동기 처리 패턴, 공유 자원 접근 패턴을 식별하라
- 레이스 컨디션 위험 영역을 식별하라

### 7. 데이터 정합성
- 트랜잭션 경계(@Transactional 범위), 분산 트랜잭션 패턴을 식별하라
- 캐시-DB 간 정합성 전략을 확인하라

### 8. 성능 병목 후보
- N+1 쿼리, 루프 내 외부 호출, 메모리 집약 처리 영역을 식별하라

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

> 대용량 Repo에서 이 Step의 항목을 "해당 없음"으로 판정하기 전에, 아래 패턴을 반드시 검색하라.

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| 테스트 디렉토리 | `*test*/**`, `*Test*`, `*Spec*`, `*spec*`, `__tests__/**` | `@Test`, `@SpringBootTest`, `describe(`, `it(`, `test(`, `expect(`, `assert` |
| 로깅 설정 | `logback*.xml`, `log4j*.xml`, `log4j*.properties`, `*logging*` | `LoggerFactory`, `@Slf4j`, `log.info`, `log.error`, `winston`, `pino` |
| 의존성 파일 | `pom.xml`, `build.gradle`, `package.json`, `package-lock.json`, `yarn.lock` | `version`, `dependency`, `devDependencies` |
| 동시성 패턴 | — | `synchronized`, `ReentrantLock`, `@Async`, `CompletableFuture`, `ExecutorService`, `AtomicInteger`, `ConcurrentHashMap` |
| 트랜잭션 | — | `@Transactional`, `TransactionTemplate`, `propagation`, `isolation`, `REQUIRES_NEW` |
| 캐시 | `*Cache*`, `*Redis*`, `*Ehcache*` | `@Cacheable`, `@CacheEvict`, `RedisTemplate`, `CacheManager`, `ehcache` |
| 모니터링 | `*Health*`, `*Metric*`, `*Actuator*` | `@HealthIndicator`, `actuator`, `micrometer`, `prometheus`, `/health` |

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 📍 소스 로케이터 필수 규칙

> 모든 산출물 테이블에 `소스 위치` 컬럼을 필수로 포함하라.
> 포맷: [source-locator-standard.md](../project/source-locator-standard.md) 참조
> 각 Step 완료 시 주요 항목을 `reverse-lookup-index.md`에 등록하라.

## 산출물

`services/{서비스명}/docs/extraction/06-quality/` 에 아래 파일 생성:
- `test-automation-inventory.md` — 테스트 자동화 현황
- `test-feature-traceability.md` — 테스트-기능 추적 매핑
- `change-frequency.md` — 코드 변경 빈도 리포트
- `logging-observability.md` — 로깅/모니터링 현황
- `dependency-health.md` — 의존성 건강도 리포트
- `concurrency-patterns.md` — 동시성/스레드 안전성 패턴
- `data-integrity.md` — 데이터 정합성 검증 포인트
- `performance-hotspots.md` — 성능 병목 후보 영역

## 💡 효과적인 추가 프롬프트 예시

> 출처: [prompt-cookbook.md](../project/prompt-cookbook.md)

**테스트 파일 누락 시:**
```
test, spec, __tests__ 폴더와 *Test.java, *Spec.js, *.test.ts 파일을 모두 찾아줘.
통합 테스트(IT), E2E 테스트도 별도 구분해줘.
```

**동시성 패턴 누락 시:**
```
synchronized, ReentrantLock, @Async, CompletableFuture, ExecutorService,
AtomicInteger, ConcurrentHashMap 키워드를 모두 검색해줘.
Reactor/RxJava 비동기 패턴도 포함해줘.
```

## 🔧 작업자 수작업 보충 항목

> 이 Step에서 KIRO 자동 추출만으로는 완성할 수 없는 항목입니다.
> 산출물 생성 후 아래 항목을 작업자가 직접 보충해 주세요.
> 상세: [manual-tasks.md](../project/manual-tasks.md)

| 항목 ID | 항목명 | 유형 | 해야 할 일 |
|---|---|---|---|
| P1-2 | 슬로우 쿼리/DB 성능 수집 | `[런타임]` | 실제 슬로우 쿼리 목록을 DB 모니터링에서 수집하여 성능 병목 후보와 비교 |
| P1-1 | 런타임 트래픽 데이터 수집 | `[런타임]` | 에러율 상위 API 목록을 APM에서 수집하여 품질 위험 영역과 교차 검증 |
| P2-1 | Stakeholder 리뷰 (품질) | `[확정]` | 품질센터가 테스트 Gap, 성능 병목 산출물을 리뷰 |

## 🔀 분할 생성 가이드

> 산출물 8개를 한 번에 생성하면 컨텍스트 윈도우 한계로 후반부 품질이 저하된다.
> 테스트·로깅과 성능·정합성을 분리하여 실행하라.

### 분할 전략: 2분할 (테스트·로깅 / 성능·정합성)

| Part | 산출물 | 프롬프트 예시 |
|---|---|---|
| Part 1: 테스트·로깅 | `test-automation-inventory.md`, `test-feature-traceability.md`, `change-frequency.md`, `logging-observability.md` | `#06-quality-extraction 서비스: {서비스명} 범위: Part 1 (테스트 현황, 추적 매핑, 변경 빈도, 로깅)` |
| Part 2: 성능·정합성 | `dependency-health.md`, `concurrency-patterns.md`, `data-integrity.md`, `performance-hotspots.md` | `#06-quality-extraction 서비스: {서비스명} 범위: Part 2 (의존성 건강도, 동시성, 정합성, 성능 병목)` |

### 분할 실행 시 주의사항
- Part 1에서 식별한 Test Gap 영역을 Part 2 성능 병목 분석 시 교차 참조하라
- 모든 Part 완료 후 `project/artifact-checklist.md`의 Step 06 섹션에서 8개 산출물 생성 여부를 확인하라
- 소규모 서비스(소스 파일 1,000개 미만)에서는 분할 없이 한 번에 실행 가능

## 완료 기준
- 테스트 피라미드 현황(단위 > 통합 > E2E 비율)이 파악됨
- 고위험 영역(변경 빈도 높음 + 테스트 없음)이 식별됨
- 성능 병목 후보가 위험도 등급과 함께 목록화됨

## 🔄 역방향 피드백 체크 (이전 산출물 업데이트)

이 Step에서 발견한 내용이 이전 Step 산출물에 영향을 주는지 확인하세요:

- [ ] **Step 01b → dead-code 목록 업데이트 필요?**
  - 테스트 코드 분석에서 발견한 테스트 전용 코드가 dead-code로 오분류되어 있지 않은지 확인
  - 검증 방법: `grepSearch`로 `01b-dead-code/dead-code-report.md`에서 `Test|Spec|test|spec`을 검색하여, Dead 판정(🔴)된 항목 중 테스트 전용 코드(테스트 헬퍼, Mock, Fixture)가 포함되어 있는지 확인. 테스트 전용 코드가 Dead로 오분류된 경우 판정을 `⚠️ 테스트 전용`으로 변경
- [ ] **Step 04 → business-rules.md 업데이트 필요?**
  - 테스트 케이스에서 발견한 비즈니스 규칙 경계값이 비즈니스 로직 문서에 반영되어 있는지 확인
  - 검증 방법: `grepSearch`로 `06-quality/test-feature-traceability.md`에서 경계값 테스트 케이스(예: `boundary|edge|min|max|limit`)를 검색하고, 해당 경계값이 `04-business-logic/business-rules.md`에 규칙으로 명시되어 있는지 대조. 누락된 경계값 규칙이 있으면 `business-rules.md`에 추가
- [ ] **Step 03 → api-inventory.md 업데이트 필요?**
  - 성능 테스트/부하 테스트에서 식별된 병목 API가 API 인벤토리에 성능 속성으로 반영되어 있는지 확인
  - 검증 방법: `grepSearch`로 `06-quality/performance-hotspots.md`에서 `API|endpoint|/api`를 검색하여 병목 API 목록을 추출하고, `03-api-data/api-endpoints.md`에서 해당 API의 성능 속성(응답시간, TPS 등) 컬럼이 존재하는지 확인. 누락 시 성능 속성 컬럼을 추가하고 병목 표기(⚠️) 반영

> 업데이트가 필요한 경우, 해당 산출물 파일을 직접 수정하고 `_context-notes.md`에 변경 사유를 기록하세요.


## ➡️ 다음 Step

- 다음: [Step 07 — UX 관련 설계도 추출](07-ux-extraction.md)
- 이전: [Step 05 — 보안 관련 설계도 추출](05-security-extraction.md)
