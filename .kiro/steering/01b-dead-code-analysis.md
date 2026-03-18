---
inclusion: manual
---

# Step 1b: Dead Code 및 미사용 리소스 분석

> 참조: #[[file:project/extraction-checklist.md]] — 1-1b. Dead Code 및 미사용 리소스 분석
> 선행: Step 01 (코드 구조 분석) 완료 후 실행
> 📖 기술 용어: [용어 사전](../../glossary.md)

## ⚡ 컨텍스트 복원 (새 세션 시작 시)

> `services/{서비스명}/docs/extraction/_context-notes.md`를 먼저 읽어 이전 Step 인사이트를 복원하세요.
> 이 Step 완료 후 `_context-notes.md`의 해당 섹션에 핵심 발견사항을 기록하세요.

## 목표

소스코드에서 더 이상 사용되지 않는 코드, DB 테이블/컬럼, API, 화면, 설정을 식별하여
이후 Step(02~08)에서 "살아있는 코드"에만 집중할 수 있도록 한다.
Dead Code 리포트는 Phase 3 Gap 분석의 입력으로도 활용된다.

## 왜 별도 Step인가

- Dead Code 분석은 전체 코드베이스를 대상으로 참조 관계를 추적해야 하므로 컨텍스트 소모가 크다
- 기존 Step에 포함하면 메모리 부족으로 중단될 위험이 높다
- Step 01에서 파악한 코드 구조를 기반으로 분석하므로 Step 01 직후가 최적 시점이다

## 작업 절차

> ⚠️ 대용량 Repo에서는 아래 절차를 모듈/패키지 단위로 분할 실행하라.
> 한 번에 분석하는 범위를 모듈 1~2개로 제한하여 컨텍스트 윈도우를 보호하라.

### 1. 미사용 코드 식별 (Dead Code Detection)

#### 1-1. 미호출 클래스/메서드 탐지
- Step 01의 디렉토리 구조와 의존성 정보를 기반으로 분석 범위를 확정하라
- 아래 순서로 미호출 코드를 탐지하라:
  1. `grepSearch`로 클래스명/메서드명의 참조를 검색하라
  2. 참조가 0건인 public 클래스/메서드를 "미사용 후보"로 분류하라
  3. 리플렉션, 어노테이션 기반 호출(@Scheduled, @EventListener 등)을 재확인하라
  4. 프레임워크 자동 등록(Spring Bean, DI 컨테이너)을 재확인하라
  5. 재확인 후에도 참조 없으면 "Dead Code 확정"으로 판정하라

#### 1-2. 미사용 import/의존성 탐지
- 선언되었으나 사용되지 않는 import 문을 식별하라
- `pom.xml`/`build.gradle`/`package.json`에 선언되었으나 코드에서 사용되지 않는 의존성을 식별하라

#### 1-3. 도달 불가 코드 (Unreachable Code)
- 항상 false인 조건문 내 코드 블록을 식별하라
- return/throw 이후 실행되지 않는 코드를 식별하라
- 피처 플래그로 영구 비활성화된 코드 블록을 식별하라

### 2. 미사용 DB 리소스 식별

#### 2-1. 미참조 테이블/컬럼 탐지
- ORM Entity/Mapper에서 정의된 테이블/컬럼 목록을 추출하라
- 코드 전체에서 해당 테이블/컬럼명의 참조를 검색하라
- 참조가 0건인 테이블/컬럼을 "미사용 후보"로 분류하라
- MyBatis XML, Native Query, Stored Procedure 내 참조도 반드시 확인하라
- ⚠️ 외부 시스템이 직접 DB를 참조할 수 있으므로, 미사용 확정은 `[KIRO+확정]`으로 표기하라

#### 2-2. 레거시 마이그레이션 잔존물
- Flyway/Liquibase 마이그레이션에서 DROP 되었어야 할 테이블이 Entity에 남아있는 경우 식별하라
- 마이그레이션 스크립트에서 ADD COLUMN 후 사용되지 않는 컬럼을 식별하라

### 3. 미사용 API 식별

#### 3-1. 미호출 API 엔드포인트 탐지
- Controller/Router에 정의된 API 엔드포인트 전체 목록을 추출하라
- 프론트엔드 코드에서 해당 API URL 패턴의 호출을 검색하라
- 다른 서비스의 Feign Client/RestTemplate에서 호출을 검색하라
- 호출처가 0건인 API를 "미사용 후보"로 분류하라
- ⚠️ 외부 시스템/파트너사가 호출할 수 있으므로, 미사용 확정은 `[KIRO+확정]`으로 표기하라

#### 3-2. Deprecated API 현황
- `@Deprecated` 어노테이션이 붙은 API를 수집하라
- Swagger/OpenAPI에서 deprecated 표기된 API를 수집하라
- Deprecated API 중 여전히 호출되고 있는 것과 완전히 미사용인 것을 구분하라

### 4. 미사용 설정/리소스 식별

#### 4-1. 미참조 설정값
- `application*.yml`/`*.properties`에 정의되었으나 코드에서 `@Value`/`@ConfigurationProperties`로 참조되지 않는 설정키를 식별하라
- 환경변수로 정의되었으나 코드에서 사용되지 않는 항목을 식별하라

#### 4-2. 미사용 리소스 파일
- `resources/`(또는 `assets/`) 하위의 이미지, 템플릿, 정적 파일 중 코드에서 참조되지 않는 것을 식별하라
- i18n 메시지 키 중 코드에서 사용되지 않는 것을 식별하라

#### 4-3. 미사용 Spring Bean / DI 등록
- `@Component`/`@Service`/`@Repository`로 등록되었으나 `@Autowired`/생성자 주입으로 사용되지 않는 Bean을 식별하라

### 5. 코드 연대 분석 (Code Age Analysis)

- `git log`로 파일별 마지막 수정일을 추출하라
- 12개월 이상 수정되지 않은 파일을 "장기 미변경 코드"로 분류하라
- 장기 미변경 + 미호출 = Dead Code 가능성 높음으로 표기하라
- ⚠️ 안정적인 유틸리티/공통 모듈은 장기 미변경이 정상이므로, 참조 횟수와 교차 판단하라

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| 미사용 코드 | — | `@Deprecated`, `DEPRECATED`, `unused`, `obsolete`, `legacy` |
| DB Entity | `*Entity*`, `*Model*`, `*Mapper*`, `*Repository*` | `@Entity`, `@Table`, `@Column`, `resultMap`, `<select`, `<insert` |
| API 정의 | `*Controller*`, `*Router*`, `*Resource*` | `@GetMapping`, `@PostMapping`, `@RequestMapping`, `router.get`, `app.get` |
| 설정 참조 | `application*.yml`, `*.properties`, `*.env*` | `@Value`, `@ConfigurationProperties`, `process.env`, `os.environ` |
| 리소스 파일 | `resources/**`, `assets/**`, `static/**`, `public/**` | — |
| 피처 플래그 | `*FeatureFlag*`, `*Toggle*`, `*RemoteConfig*` | `featureFlag`, `isEnabled`, `toggle`, `experiment` |

## 판정 기준

| 판정 | 조건 | 표기 |
|---|---|---|
| Dead Code 확정 | 참조 0건 + 리플렉션/프레임워크 자동 등록 아님 | 🔴 Dead |
| Dead Code 의심 | 참조 0건이나 리플렉션/외부 호출 가능성 있음 | 🟡 Suspect |
| 장기 미변경 | 12개월+ 미수정, 참조는 있으나 활성 사용 불확실 | 🟠 Stale |
| 활성 사용 | 참조 있음, 최근 수정 이력 있음 | 🟢 Active |

> ⚠️ 🔴 Dead 판정이라도 외부 시스템 연동, 배치 전용, 관리자 전용 등 코드 내 참조가 없는 정당한 사유가 있을 수 있다.
> 최종 삭제/정리 판단은 반드시 개발 리드가 확정하라 → `[KIRO+확정]`

## 분할 실행 가이드

대용량 Repo에서는 아래와 같이 분할 실행하라:

```
# 1회차: 서버 모듈 Dead Code 분석
#01b-dead-code-analysis 서비스: media-iptv-vod 범위: server/ 하위만

# 2회차: Android 앱 Dead Code 분석
#01b-dead-code-analysis 서비스: media-iptv-vod 범위: app-android/ 하위만

# 3회차: iOS 앱 Dead Code 분석
#01b-dead-code-analysis 서비스: media-iptv-vod 범위: app-ios/ 하위만

# 4회차: 결과 통합
이전 분할 분석 결과를 하나의 산출물로 통합해줘.
```

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 📍 소스 로케이터 필수 규칙

> 모든 산출물 테이블에 `소스 위치` 컬럼을 필수로 포함하라.
> 포맷: [source-locator-standard.md](../project/source-locator-standard.md) 참조
> 각 Step 완료 시 주요 항목을 `reverse-lookup-index.md`에 등록하라.

## 산출물

`services/{서비스명}/docs/extraction/01b-dead-code/` 에 아래 파일 생성:
- `dead-code-report.md` — 미사용 클래스/메서드/import 목록 (판정 등급 포함)
- `unused-db-resources.md` — 미참조 테이블/컬럼 목록
- `unused-api-endpoints.md` — 미호출 API 엔드포인트 목록
- `unused-config-resources.md` — 미참조 설정값/리소스 파일/Bean 목록
- `code-age-analysis.md` — 파일별 마지막 수정일, 장기 미변경 코드 목록

## 💡 효과적인 추가 프롬프트 예시

**미사용 클래스 탐지:**
```
src/ 하위의 모든 public 클래스 중에서, 다른 파일에서 import 되지 않는 클래스를 찾아줘.
@Component, @Service, @Repository, @Controller 어노테이션이 붙은 것은 제외하고.
```

**미사용 DB 테이블 탐지:**
```
Entity 클래스에 @Table로 정의된 테이블명 목록을 뽑고,
각 테이블명이 MyBatis XML, Native Query, Repository 메서드에서 참조되는지 확인해줘.
참조가 없는 테이블을 알려줘.
```

**미사용 API 탐지:**
```
Controller에 정의된 모든 API 엔드포인트 URL을 뽑고,
프론트엔드 코드와 Feign Client에서 해당 URL이 호출되는지 확인해줘.
호출처가 없는 API를 알려줘.
```

**장기 미변경 코드 탐지:**
```
git log로 각 파일의 마지막 커밋 날짜를 확인하고,
1년 이상 수정되지 않은 파일 목록을 뽑아줘.
그 중 다른 파일에서 참조되지 않는 것을 Dead Code 후보로 표시해줘.
```

## 🔧 작업자 수작업 보충 항목

> 이 Step에서 KIRO 자동 추출만으로는 완성할 수 없는 항목입니다.
> 산출물 생성 후 아래 항목을 작업자가 직접 보충해 주세요.
> 상세: [manual-tasks.md](../project/manual-tasks.md)

| 항목 ID | 항목명 | 유형 | 해야 할 일 |
|---|---|---|---|
| P1-4 | 기술 부채 마커 검토 및 분류 | `[확정]` | 🔴 Dead 판정 항목의 최종 삭제/정리 판단을 개발 리드가 확정 (`[KIRO+확정]` 태그 항목) |
| P1-1 | 런타임 트래픽 데이터 수집 | `[런타임]` | 코드에서 미사용으로 보이나 런타임에서 호출되는 API가 있을 수 있음. APM 데이터로 교차 검증 필요 |

## 완료 기준
- 미사용 코드/DB/API/설정 목록이 판정 등급(🔴/🟡/🟠/🟢)과 함께 작성됨
- 각 항목에 판정 근거(참조 검색 결과, git log 날짜)가 기재됨
- 🔴 Dead 판정 항목에 `[KIRO+확정]` 태그가 표기됨 (개발 리드 확인 필요)
- 이후 Step(02~08)에서 Dead Code를 제외하고 분석할 수 있는 참조 목록이 제공됨


## ➡️ 다음 Step

- 다음: [Step 1c — 공통 모듈 그룹핑 분석](01c-common-module-grouping.md)
- 이전: [Step 01 — Repository 스캔 및 코드 구조 분석](01-code-structure-scan.md)
