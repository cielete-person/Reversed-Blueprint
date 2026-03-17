---
inclusion: manual
---

# Step 6: 품질 관련 설계도 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-6. 품질 관련 추출

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

## 완료 기준
- 테스트 피라미드 현황(단위 > 통합 > E2E 비율)이 파악됨
- 고위험 영역(변경 빈도 높음 + 테스트 없음)이 식별됨
- 성능 병목 후보가 위험도 등급과 함께 목록화됨
