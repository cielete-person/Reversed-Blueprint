---
inclusion: manual
---

# Step 2: 화면 목록 추출 및 화면명 정규화

> 참조: #[[file:project/extraction-checklist.md]] — 1-2. 화면 목록 및 정규화

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

## 완료 기준
- 모든 라우트/페이지가 식별되고 화면 ID가 부여됨
- 서브 화면(팝업/모달 등)이 누락 없이 포함됨
- 정규화 매핑 테이블이 완성됨
- iOS/Android 양 플랫폼 화면이 동일 화면 ID로 매핑됨 (해당 서비스에 한함)
- 플랫폼 전용 화면이 ⚠️ 표기와 함께 식별됨
