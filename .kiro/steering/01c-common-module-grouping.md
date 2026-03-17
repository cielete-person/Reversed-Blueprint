---
inclusion: manual
---

# Step 1c: 공통 모듈 그룹핑 분석

> 참조: #[[file:project/extraction-checklist.md]] — 1-1c. 공통 모듈 그룹핑 분석
> 선행: Step 01(코드 구조 스캔) 완료 필수. Step 1b(Dead Code) 완료 권장.

## ⚡ 컨텍스트 복원 (새 세션 시작 시)

> `services/{서비스명}/docs/extraction/_context-notes.md`를 먼저 읽어 이전 Step 인사이트를 복원하세요.
> 이 Step 완료 후 `_context-notes.md`의 해당 섹션에 핵심 발견사항을 기록하세요.

## 목표

많은 코드에서 참조·호출하는 공통 성격의 코드 집합을 식별하고 그룹핑한다.
단일 Repo 내부의 공통 패키지(util, common, core, shared 등)와
여러 서비스가 공유하는 사내 공통 라이브러리(별도 Repo)를 모두 대상으로 한다.

## 작업 절차

### 1. Repo 내부 공통 패키지 탐지

소스코드 내에서 다수 모듈/클래스가 공통으로 참조하는 패키지·디렉토리를 식별하라.

#### 1-1. 공통 패키지 후보 자동 탐지
- 디렉토리명 패턴으로 후보를 1차 선별하라:
  - `common/`, `shared/`, `core/`, `util/`, `utils/`, `lib/`, `base/`, `foundation/`, `support/`, `helper/`, `framework/`, `infrastructure/`, `platform/`
- 패키지 선언 패턴으로 추가 후보를 식별하라:
  - Java/Kotlin: `*.common.*`, `*.core.*`, `*.util.*`, `*.shared.*`, `*.base.*`, `*.framework.*`
  - JS/TS: `@shared/`, `@common/`, `@core/`, `@lib/`, `@utils/`
  - Python: `common/`, `utils/`, `core/`, `lib/`, `shared/`

#### 1-2. 참조 횟수 기반 검증
- 후보 패키지 내 각 클래스/모듈의 **import 참조 횟수**를 집계하라:
  - `grepSearch`로 `import.*{패키지명}` 또는 `from.*{패키지명}` 또는 `require.*{패키지명}` 검색
  - 참조 횟수 ≥ 3인 클래스/모듈 = 공통 모듈로 확정
  - 참조 횟수 1~2인 클래스/모듈 = 공통 모듈 후보 (⚠️ 표기)
  - 참조 횟수 0인 클래스/모듈 = Step 1b Dead Code 결과와 교차 확인

#### 1-3. 공통 모듈 기능별 그룹핑
- 식별된 공통 모듈을 아래 카테고리로 분류하라:

| 그룹 | 설명 | 예시 |
|---|---|---|
| 유틸리티 | 범용 헬퍼 (문자열, 날짜, 컬렉션 등) | StringUtils, DateUtils, CollectionHelper |
| 공통 응답/DTO | API 공통 응답 래퍼, 페이지네이션, 에러 응답 | ApiResponse, PageResult, ErrorResponse |
| 공통 예외 | 공통 예외 클래스, 글로벌 예외 핸들러 | BusinessException, GlobalExceptionHandler |
| 공통 설정 | 공통 Bean, 설정 클래스, 프로파일 | WebConfig, SecurityConfig, SwaggerConfig |
| 공통 인증/보안 | 인증 필터, 토큰 처리, 권한 체크 | JwtFilter, AuthInterceptor, PermissionChecker |
| 공통 로깅/AOP | 로깅 인터셉터, AOP Aspect, 감사 로그 | LoggingAspect, AuditInterceptor, TraceFilter |
| 공통 데이터 접근 | 공통 Repository, 쿼리 빌더, 트랜잭션 | BaseRepository, QueryBuilder, TxManager |
| 공통 외부 연동 | HTTP 클라이언트 래퍼, 메시지 발행 공통 | RestClientWrapper, KafkaProducerBase |
| 공통 도메인 | 공통 도메인 모델, 값 객체, Enum | BaseEntity, Money, PhoneNumber, StatusCode |
| 공통 UI/프론트 | 공통 컴포넌트, 레이아웃, 테마 | BaseActivity, CommonDialog, AppTheme |
| 기타 | 위 카테고리에 해당하지 않는 공통 코드 | — |

### 2. 사내 공통 라이브러리 (외부 Repo) 식별

서비스 Repo 외부에 존재하는 사내 공통 라이브러리 의존성을 식별하라.

#### 2-1. 빌드 파일에서 사내 라이브러리 추출
- `pom.xml` / `build.gradle(.kts)`: groupId가 사내 도메인인 의존성
- `package.json`: scope가 사내 prefix인 패키지
- `requirements.txt` / `pyproject.toml`: 사내 PyPI 또는 Git URL 의존성
- 사내 Maven/npm/PyPI 레지스트리 URL이 설정에 있는지 확인

#### 2-2. 사내 라이브러리 메타정보 수집
- 각 사내 라이브러리에 대해:
  - 라이브러리명, 버전, groupId/scope
  - 용도 추정 (라이브러리명, import 사용처에서 추론)
  - 참조하는 서비스 내 모듈/클래스 수
  - 소스 Repo URL (확인 가능한 경우, 불가 시 🔍)

### 3. 공통 모듈 의존 관계 분석

#### 3-1. 의존 방향 매핑
- 공통 모듈 → 비즈니스 모듈 방향의 역의존이 있는지 확인
  - 공통 모듈이 비즈니스 로직을 import하면 → ⚠️ 아키텍처 위반 후보
- 공통 모듈 간 의존 관계를 식별하라 (core → util, security → core 등)
- Mermaid flowchart로 의존 방향 다이어그램을 작성하라

#### 3-2. 영향 범위 분석 (Impact Scope)
- 각 공통 모듈 변경 시 영향받는 클래스/모듈 수를 집계하라
- 영향 범위가 큰 순서로 정렬하여 "변경 영향도 Top 10"을 산출하라
- 영향 범위 등급:
  - 🔴 High: 참조 10개 이상 — 변경 시 광범위 영향
  - 🟡 Medium: 참조 5~9개 — 변경 시 중간 영향
  - 🟢 Low: 참조 1~4개 — 변경 시 제한적 영향

### 4. 멀티 서비스 공통 모듈 교차 매핑 (선택)

> 2개 이상 서비스의 Step 1c가 완료된 후 실행 가능. 단일 서비스 분석 시에는 건너뛰어도 됨.

- 서비스 A와 서비스 B가 동일한 사내 공통 라이브러리를 사용하는 경우 교차 매핑
- 서비스별 공통 라이브러리 버전 차이 식별 (동일 라이브러리의 다른 버전 → ⚠️)
- 서비스 간 공통 패턴(동일 유틸리티 클래스를 각 서비스가 복사하여 사용) 식별 → 라이브러리화 후보

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

> 이 Step의 항목을 "해당 없음"으로 판정하기 전에, 아래 패턴을 반드시 검색하라.

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| 공통 디렉토리 | `common/`, `shared/`, `core/`, `util/`, `utils/`, `lib/`, `base/`, `helper/`, `framework/`, `support/` | — |
| 공통 클래스 (Java/Kotlin) | `*Utils*`, `*Util*`, `*Helper*`, `*Base*`, `*Common*`, `*Shared*`, `*Abstract*` | `abstract class`, `public static`, `@Component`, `@Configuration` |
| 공통 모듈 (JS/TS) | `*utils*`, `*helpers*`, `*common*`, `*shared*`, `*lib*` | `export default`, `export function`, `module.exports` |
| 사내 라이브러리 | `pom.xml`, `build.gradle*`, `package.json`, `requirements.txt` | 사내 groupId/scope |
| 공통 설정 | `*Config*`, `*Configuration*`, `*Properties*` | `@Configuration`, `@Bean`, `@ConfigurationProperties` |
| 공통 예외 | `*Exception*`, `*Error*`, `*ExceptionHandler*` | `extends RuntimeException`, `@ControllerAdvice`, `ErrorBoundary` |
| 공통 인터셉터/필터 | `*Interceptor*`, `*Filter*`, `*Aspect*`, `*Middleware*` | `@Aspect`, `implements Filter`, `implements HandlerInterceptor`, `middleware` |

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/01c-common-modules/` 에 아래 파일 생성:
- `internal-common-modules.md` — Repo 내부 공통 패키지 인벤토리 (그룹별 분류, 참조 횟수, 영향 범위 등급)
- `external-common-libraries.md` — 사내 공통 라이브러리 인벤토리 (라이브러리명, 버전, 용도, 참조 수)
- `dependency-direction.md` — 공통 모듈 의존 방향 다이어그램 (Mermaid flowchart, 역의존 ⚠️ 표기)
- `impact-analysis.md` — 변경 영향도 분석 (Top 10, 영향 범위 등급 🔴/🟡/🟢)

> 멀티 서비스 교차 매핑(섹션 4) 결과는 `docs-integrated/extraction/01c-common-modules-cross.md`에 별도 생성

## 💡 효과적인 추가 프롬프트 예시

> 출처: [prompt-cookbook.md](../project/prompt-cookbook.md)

**공통 패키지 탐지 강화:**
```
src/ 하위에서 common, shared, core, util, utils, lib, base, helper, framework, support
이름이 포함된 디렉토리를 모두 찾아줘. 깊이 제한 없이 전체 탐색해줘.
```

**참조 횟수 집계:**
```
{패키지명} 패키지 내 모든 클래스를 import하는 파일 수를 클래스별로 집계해줘.
참조 횟수가 많은 순서로 정렬해줘.
```

**사내 라이브러리 식별:**
```
pom.xml과 build.gradle에서 groupId가 사내 도메인으로 시작하는
의존성을 모두 추출해줘. 버전과 scope도 포함해줘.
```

**역의존 탐지:**
```
common/, core/, util/ 패키지의 소스 파일에서 비즈니스 패키지(service/, domain/, controller/)를
import하는 코드가 있는지 확인해줘. 있으면 아키텍처 위반 후보야.
```

**복사된 공통 코드 탐지 (멀티 서비스):**
```
이 서비스의 util/ 패키지 내 클래스명과 동일한 클래스가
다른 서비스(services/*/src/)에도 존재하는지 확인해줘.
복사해서 쓰고 있는 공통 코드 후보를 찾아줘.
```

## 🔧 작업자 수작업 보충 항목

> 이 Step에서 KIRO 자동 추출만으로는 완성할 수 없는 항목입니다.
> 산출물 생성 후 아래 항목을 작업자가 직접 보충해 주세요.
> 상세: [manual-tasks.md](../project/manual-tasks.md)

| 항목 ID | 항목명 | 유형 | 해야 할 일 |
|---|---|---|---|
| P1-7 | IaC 파일 수집 | `[문서수집]` | 사내 공통 라이브러리의 소스 Repo URL이 확인 불가한 경우 담당팀에 문의 |
| (Step 내) | 사내 라이브러리 용도 확인 | `[인터뷰]` | KIRO가 추정한 사내 라이브러리 용도가 정확한지 담당 개발자에게 확인 |

## 완료 기준
- Repo 내부 공통 패키지가 기능별 그룹으로 분류됨
- 각 공통 모듈의 참조 횟수와 영향 범위 등급이 산출됨
- 사내 공통 라이브러리 의존성이 목록화됨
- 공통 모듈 간 의존 방향 다이어그램이 작성됨
- 역의존(아키텍처 위반 후보)이 식별되어 ⚠️ 표기됨
- 변경 영향도 Top 10이 산출됨
