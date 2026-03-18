---
inclusion: manual
---

# Step 2: 화면 목록 추출 및 화면명 정규화

> 참조: #[[file:project/extraction-checklist.md]] — 1-2. 화면 목록 및 정규화
> 📖 기술 용어: [용어 사전](../../glossary.md)

## 선행 산출물 참조

- **Step 1b(Dead Code)**: `01b-dead-code/` 산출물에서 Dead 판정(🔴)된 화면/Activity/Fragment/ViewController는 화면 목록에서 제외하라. 조건부 Dead(🟡)는 포함하되 `⚠️ Dead 의심` 표기.
- **Step 1c(공통 모듈)**: `01c-common-modules/` 산출물에서 공통 UI 컴포넌트(Base Activity, 공통 Fragment 등)를 식별하여, 화면 목록에서 "공통 UI 컴포넌트" 섹션으로 별도 분류하라.

## ⚡ 컨텍스트 복원 (새 세션 시작 시)

> `services/{서비스명}/docs/extraction/_context-notes.md`를 먼저 읽어 이전 Step 인사이트를 복원하세요.
> 이 Step 완료 후 `_context-notes.md`의 해당 섹션에 핵심 발견사항을 기록하세요.

## 목표

프론트엔드 소스에서 전체 화면 목록을 추출하고, 화면 ID를 부여하며, 화면명을 정규화한다.

## 작업 절차

### 1. 화면 목록 추출
- 프론트엔드 라우트/페이지 컴포넌트를 스캔하라
  - React: `react-router` 설정, 페이지 컴포넌트
  - Vue: `vue-router` 설정
  - Angular: `routing module`
  - Native: Activity/Fragment(Android), ViewController(iOS)
  - STB: 미들웨어 화면 정의 파일
- 팝업, 모달, 바텀시트, 탭 등 서브 화면도 누락 없이 식별하라

### 2. 화면 ID 부여
- 채번 규칙: `{사업구분}-{서비스약어}-{화면유형}-{순번}`
- 화면 유형 코드: SCR(일반화면), POP(팝업), MOD(모달), BTM(바텀시트), TAB(탭)

### 3. 화면명 정규화
- 코드에서 추출한 원본 명칭(클래스명, 라우트명, 주석, i18n 키)을 수집하라
- 동일 화면이 다른 명칭을 가지는 경우 클러스터링하라 (URL 경로, 기능, 호출 API 기준)
- 클러스터별 대표 명칭(Canonical Name) 후보를 제시하라
- 정규화 매핑 테이블을 작성하라

### 4. 크로스 플랫폼 화면 동기화 (iOS / Android / STB)
- iOS와 Android 모바일 앱이 모두 존재하는 서비스의 경우, 양 플랫폼 화면을 동시에 추출하라
- 동일 기능 화면 매핑:
  - iOS ViewController/SwiftUI View ↔ Android Activity/Fragment/Compose Screen 매핑
  - 화면 ID는 플랫폼 무관하게 하나로 통합 부여
  - 소스 위치는 `소스 위치(iOS)`, `소스 위치(Android)` 컬럼으로 병기
- 플랫폼 전용 화면 식별:
  - iOS에만 있는 화면 / Android에만 있는 화면 → ⚠️ 표기하고 사유 기록
  - STB(AOSP/Android TV) 앱과 모바일 앱 간 동일 기능 화면도 매핑
- 화면 정의서 산출물 포맷에 `플랫폼` 컬럼 추가: iOS / Android / STB / Web / 공통
- 정규화 시 플랫폼 간 용어 통일을 최우선으로 적용
  - iOS 네이밍 vs Android 네이밍 중 대표 명칭 하나로 확정
  - 용어 사전에 플랫폼별 원본 명칭 → 대표 명칭 매핑 등록

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

> 대용량 Repo에서 이 Step의 항목을 "해당 없음"으로 판정하기 전에, 아래 패턴을 반드시 검색하라.

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| 라우터/네비게이션 | `*router*`, `*route*`, `*navigation*`, `*Routes*` | `createBrowserRouter`, `vue-router`, `@angular/router`, `NavHost`, `UINavigationController` |
| 화면 컴포넌트 | `*Screen*`, `*Page*`, `*View*`, `*Activity*`, `*ViewController*`, `*Fragment*` | `@Composable`, `struct.*View.*Body`, `class.*Activity`, `class.*Fragment` |
| 팝업/모달 | `*Modal*`, `*Dialog*`, `*Popup*`, `*BottomSheet*`, `*Alert*` | `showDialog`, `present`, `showModalBottomSheet`, `AlertDialog` |
| STB 화면 | `*Launcher*`, `*Leanback*`, `*TvActivity*` | `BrowseSupportFragment`, `LeanbackActivity`, `D-pad`, `KeyEvent` |

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 📍 소스 로케이터 필수 규칙

> 모든 산출물 테이블에 `소스 위치` 컬럼을 필수로 포함하라.
> 포맷: [source-locator-standard.md](../project/source-locator-standard.md) 참조
> 각 Step 완료 시 주요 항목을 `reverse-lookup-index.md`에 등록하라.

## 산출물

`services/{서비스명}/docs/extraction/02-screens/` 에 아래 파일 생성:
- `screen-inventory.md` — 화면 목록 (화면 ID, 화면명, 서비스, URL, 소스 위치, 플랫폼)
- `screen-name-normalization.md` — 화면명 정규화 매핑 테이블 (원본 → 대표 명칭)
- `cross-platform-screen-map.md` — 크로스 플랫폼 화면 매핑표 (iOS↔Android↔STB)

## 산출물 포맷

```markdown
| 화면 ID | 화면명(정규화) | 원본 명칭 | 서비스 | URL 경로 | 화면 유형 | 플랫폼 | 소스 파일(iOS) | 소스 파일(Android) | 확인 상태 |
|---|---|---|---|---|---|---|---|---|---|
```

## 💡 효과적인 추가 프롬프트 예시

> 출처: [prompt-cookbook.md](../project/prompt-cookbook.md)

**라우터 누락 시:**
```
src/ 하위에서 router, route, navigation, Routes 가 포함된 파일을 모두 찾아줘.
설정 파일뿐 아니라 동적 라우팅(lazy import)도 포함해줘.
```

**서브 화면(팝업/모달) 누락 시:**
```
Modal, Dialog, Popup, BottomSheet, Alert, Toast, Snackbar 컴포넌트를 모두 찾아줘.
호출부(showDialog, present, showModal)도 추적해줘.
```

## 🔧 작업자 수작업 보충 항목

> 이 Step에서 KIRO 자동 추출만으로는 완성할 수 없는 항목입니다.
> 산출물 생성 후 아래 항목을 작업자가 직접 보충해 주세요.
> 상세: [manual-tasks.md](../project/manual-tasks.md)

| 항목 ID | 항목명 | 유형 | 해야 할 일 |
|---|---|---|---|
| P1-5 | 화면명 정규화 최종 확정 | `[확정]` | KIRO가 제시한 화면명 정규화 후보를 PO/UX 담당자가 검토하고 대표 명칭 확정 |
| P1-1 | 런타임 트래픽 데이터 수집 | `[런타임]` | 화면별 진입 빈도(GA/Firebase Analytics)를 수집하여 DAU 기준 PV, 이탈률, 체류 시간 보충 |

## 완료 기준
- 모든 라우트/페이지가 식별되고 화면 ID가 부여됨
- 서브 화면(팝업/모달 등)이 누락 없이 포함됨
- 정규화 매핑 테이블이 완성됨
- iOS/Android 양 플랫폼 화면이 동일 화면 ID로 매핑됨 (해당 서비스에 한함)
- 플랫폼 전용 화면이 ⚠️ 표기와 함께 식별됨

## 🔄 역방향 피드백 체크 (이전 산출물 업데이트)

이 Step에서 발견한 내용이 이전 Step 산출물에 영향을 주는지 확인하세요:

- [ ] **Step 01 → code-structure.md 업데이트 필요?**
  - 화면 인벤토리에서 발견한 새로운 모듈/컴포넌트가 코드 구조에 누락되어 있는지 확인
  - 화면 간 네비게이션에서 발견한 모듈 간 의존성이 코드 구조에 반영되어 있는지 확인
  - 검증 방법: `grepSearch`로 새로 발견한 모듈명을 `01-code-structure/directory-structure.md`에서 검색. 없으면 추가 필요
- [ ] **Step 01b → dead-code 목록 업데이트 필요?**
  - 화면에서 실제 사용되는 것으로 확인된 코드가 dead-code로 분류되어 있지 않은지 확인
  - 검증 방법: 이번 Step에서 식별한 화면 클래스명을 `01b-dead-code/dead-code-report.md`에서 검색. 🔴 Dead로 분류된 항목이 있으면 판정 재검토
- [ ] **Step 01c → common-module 그룹핑 업데이트 필요?**
  - 여러 화면에서 공통으로 사용하는 컴포넌트가 공통 모듈로 분류되어 있는지 확인
  - 검증 방법: 3개 이상 화면에서 import하는 컴포넌트를 `01c-common-modules/internal-common-modules.md`에서 검색. 없으면 공통 UI 그룹에 추가

> 업데이트가 필요한 경우, 해당 산출물 파일을 직접 수정하고 `_context-notes.md`에 변경 사유를 기록하세요.


## ➡️ 다음 Step

- 다음: [Step 03 — API 엔드포인트 추출 및 DB 스키마 역추적](03-api-and-data.md)
- 이전: [Step 1c — 공통 모듈 그룹핑 분석](01c-common-module-grouping.md)
