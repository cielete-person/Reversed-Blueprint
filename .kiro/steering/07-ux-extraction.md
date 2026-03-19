---
inclusion: manual
---

# Step 7: UX 관련 설계도 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-10. UX 관련 추출
> 📖 기술 용어: [용어 사전](../../glossary.md) — APNs, FCM, SRI, CDN 등

## 선행 산출물 참조

- **Step 04(비즈니스 로직)**: `04-business-logic/use-case-scenarios.md`에서 Use Case 시나리오를 참조하라. 각 시나리오의 화면 전이 경로와 UX 인터랙션 패턴(1번)을 교차 매핑하여, 시나리오상 누락된 UX 처리(로딩, 에러 피드백 등)를 식별하는 데 활용.
- **Step 1b(Dead Code)**: `01b-dead-code/` 산출물에서 Dead 판정(🔴)된 화면/인터랙션 핸들러는 UX 분석에서 제외하라. 조건부 Dead(🟡)는 포함하되 `⚠️ Dead 의심` 표기.
- **Step 1c(공통 모듈)**: `01c-common-modules/` 산출물에서 공통 UI 컴포넌트(공통 로딩, 공통 에러 화면, 공통 다이얼로그 등)를 식별하여, UX 패턴 분석 시 "공통 UX 패턴" 섹션으로 별도 정리하라.

## ⚡ 컨텍스트 복원 (새 세션 시작 시)

> `services/{서비스명}/docs/extraction/_context-notes.md`를 먼저 읽어 이전 Step 인사이트를 복원하세요.
> 이 Step 완료 후 `_context-notes.md`의 해당 섹션에 핵심 발견사항을 기록하세요.

## 목표

PO/UX센터가 기획 시 참조할 수 있는 UX 설계도를 소스코드에서 역추적한다.
"어느 화면의 어느 부분에서, 어떤 조작을 하면, 어떻게 반응하는지"를 빠짐없이 추출한다.

## 작업 절차

### 1. 유저 인터랙션 패턴
- 화면별 이벤트 핸들러를 스캔하라 (onClick, onSwipe, onKeyDown 등)
- 조작 → 화면 반응 매핑을 작성하라 (애니메이션, 전이, 데이터 갱신, API 호출)
- STB: 리모컨 키 매핑, 음성 명령 핸들러를 식별하라
- iOS/Android 플랫폼별 인터랙션 차이를 식별하라:
  - iOS 전용 제스처 (3D Touch/Haptic Touch, 스와이프 백) vs Android 전용 (하드웨어 백 버튼)
  - 플랫폼별 네비게이션 패턴 차이 (iOS 탭바/네비게이션 스택 vs Android 바텀 네비게이션/드로어)
  - 동일 화면에서 플랫폼별 UX 흐름이 다른 경우 차이점 기록

### 2. 입력 유효성 검증 규칙
- 화면별 입력 필드의 검증 규칙을 추출하라 (필수/선택, 형식, 길이, 범위)
- 프론트엔드 vs 백엔드 이중 검증 여부를 식별하라
- 검증 실패 시 사용자 피드백 메시지를 수집하라

### 3. 로딩/대기 상태 UX 패턴
- 화면별 로딩 인디케이터 유형을 식별하라 (스피너, 스켈레톤, 프로그레스바)
- 타임아웃 시 사용자 피드백, 빈 상태(Empty State) 화면 패턴을 수집하라

### 4. 다국어/접근성
- i18n 적용 범위, 미번역 영역을 식별하라
- ARIA 속성, 키보드 네비게이션, 포커스 관리 현황을 확인하라

### 5. 딥링크/푸시 알림 진입 경로
- 딥링크 URL 스킴, 유니버설 링크 라우팅을 스캔하라
- 외부 진입 경로별 대상 화면 ID, 필요 파라미터, 인증 요구사항을 매핑하라
- iOS(Universal Links, URL Scheme) vs Android(App Links, Intent Filter) 설정 차이를 식별하라
- 푸시 알림 처리 차이를 식별하라 (APNs vs FCM, 알림 채널/카테고리 설정)

### 6. A/B 테스트 및 피처 플래그
- 피처 플래그 관리 도구, 활성 플래그 목록, 적용 화면/기능을 식별하라

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

> 대용량 Repo에서 이 Step의 항목을 "해당 없음"으로 판정하기 전에, 아래 패턴을 반드시 검색하라.

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| 이벤트 핸들러 | `*Handler*`, `*Listener*`, `*Event*` | `onClick`, `onPress`, `onSwipe`, `onKeyDown`, `addEventListener`, `@IBAction`, `setOnClickListener` |
| Validation | `*Validator*`, `*Validation*`, `*Form*` | `validate`, `@Valid`, `@Pattern`, `@Size`, `@Min`, `@Max`, `Yup.`, `Joi.`, `formik` |
| 로딩 컴포넌트 | `*Loading*`, `*Spinner*`, `*Skeleton*`, `*Progress*`, `*Empty*` | `isLoading`, `loading`, `skeleton`, `shimmer`, `CircularProgress`, `ActivityIndicator` |
| i18n/다국어 | `*i18n*`, `*locale*`, `*translation*`, `*strings.xml`, `Localizable.strings` | `useTranslation`, `t(`, `i18next`, `NSLocalizedString`, `getString(R.string` |
| 딥링크 설정 | `*DeepLink*`, `*AppLink*`, `*UniversalLink*`, `Info.plist`, `AndroidManifest.xml` | `deeplink`, `scheme`, `host`, `pathPrefix`, `universalLinks`, `intent-filter`, `CFBundleURLSchemes` |
| 피처 플래그 | `*FeatureFlag*`, `*Feature*`, `*Toggle*`, `*RemoteConfig*` | `featureFlag`, `isEnabled`, `RemoteConfig`, `LaunchDarkly`, `Unleash`, `toggle` |

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 📍 소스 로케이터 필수 규칙

> 모든 산출물 테이블에 `소스 위치` 컬럼을 필수로 포함하라.
> 포맷: [source-locator-standard.md](../project/source-locator-standard.md) 참조
> 각 Step 완료 시 주요 항목을 `reverse-lookup-index.md`에 등록하라.

## 산출물

`services/{서비스명}/docs/extraction/07-ux/` 에 아래 파일 생성:
- `interaction-patterns.md` — 유저 인터랙션 패턴 목록
- `validation-rules.md` — 입력 유효성 검증 규칙 목록
- `loading-ux-patterns.md` — 로딩/대기 상태 UX 패턴
- `i18n-accessibility.md` — 다국어/접근성 현황
- `entry-points.md` — 딥링크/푸시 알림 진입 경로
- `feature-flags.md` — A/B 테스트 및 피처 플래그 현황

## 💡 효과적인 추가 프롬프트 예시

> 출처: [prompt-cookbook.md](../project/prompt-cookbook.md)

**딥링크 설정 누락 시:**
```
AndroidManifest.xml에서 intent-filter, scheme, host, pathPrefix 를 찾아줘.
iOS는 Info.plist에서 CFBundleURLSchemes, Associated Domains 를 확인해줘.
```

**피처 플래그 누락 시:**
```
featureFlag, isEnabled, RemoteConfig, toggle, experiment 키워드를 검색해줘.
Firebase Remote Config, LaunchDarkly 등 외부 서비스 연동도 확인해줘.
```

## 🔧 작업자 수작업 보충 항목

> 이 Step에서 KIRO 자동 추출만으로는 완성할 수 없는 항목입니다.
> 산출물 생성 후 아래 항목을 작업자가 직접 보충해 주세요.
> 상세: [manual-tasks.md](../project/manual-tasks.md)

| 항목 ID | 항목명 | 유형 | 해야 할 일 |
|---|---|---|---|
| P1-5 | 화면명 정규화 최종 확정 | `[확정]` | UX 인터랙션 패턴에서 사용하는 화면명이 Step 02 정규화 결과와 일치하는지 PO/UX 확인 |
| P2-1 | Stakeholder 리뷰 (UX) | `[확정]` | PO, UX센터가 화면 카탈로그의 화면 누락, 화면명 정확성을 리뷰 |
| P1-1 | 런타임 트래픽 데이터 수집 | `[런타임]` | 화면별 체류 시간, 이탈률 데이터를 GA/Firebase에서 수집하여 UX 분석 보충 |

## 🔀 분할 생성 가이드

> 산출물 6개를 한 번에 생성하면 컨텍스트 윈도우 한계로 후반부 품질이 저하될 수 있다.
> 인터랙션·검증·로딩과 다국어·딥링크·피처 플래그를 분리하여 실행하라.

### 분할 전략: 2분할 (인터랙션·검증·로딩 / 다국어·딥링크·피처)

| Part | 산출물 | 프롬프트 예시 |
|---|---|---|
| Part 1: 인터랙션·검증·로딩 | `interaction-patterns.md`, `validation-rules.md`, `loading-ux-patterns.md` | `#07-ux-extraction 서비스: {서비스명} 범위: Part 1 (인터랙션 패턴, 유효성 검증, 로딩 UX)` |
| Part 2: 다국어·딥링크·피처 | `i18n-accessibility.md`, `entry-points.md`, `feature-flags.md` | `#07-ux-extraction 서비스: {서비스명} 범위: Part 2 (다국어/접근성, 딥링크, 피처 플래그)` |

### 분할 실행 시 주의사항
- Part 1에서 식별한 화면 ID를 Part 2 딥링크 매핑 시 참조하라
- 모든 Part 완료 후 `project/artifact-checklist.md`의 Step 07 섹션에서 6개 산출물 생성 여부를 확인하라
- 화면 수가 30개 미만인 소규모 서비스에서는 분할 없이 한 번에 실행 가능

## 완료 기준
- 화면별 조작→반응 매핑이 완성됨
- 입력 필드별 검증 규칙이 프론트/백엔드 구분되어 정리됨
- 외부 진입 경로가 화면 ID와 매핑됨

## 🔄 역방향 피드백 체크 (이전 산출물 업데이트)

이 Step에서 발견한 내용이 이전 Step 산출물에 영향을 주는지 확인하세요:

- [ ] **Step 02 → screen-inventory.md 업데이트 필요?**
  - UX 분석에서 발견한 숨겨진 화면 상태(로딩/에러/빈 상태)가 화면 인벤토리에 반영되어 있는지 확인
  - 검증 방법: `grepSearch`로 `07-ux/loading-ux-patterns.md`에서 화면 ID(`SCR-|POP-|MOD-`)를 검색하여 로딩/에러/빈 상태가 있는 화면 목록을 추출하고, `02-screens/screen-inventory.md`에서 해당 화면 ID를 검색하여 상태 화면이 등재되어 있는지 확인. 누락된 상태 화면은 `screen-inventory.md`에 추가
- [ ] **Step 04 → business-rules.md 업데이트 필요?**
  - 프론트엔드 검증 규칙이 비즈니스 규칙과 일치하는지, 불일치 항목이 있는지 확인
  - 검증 방법: `grepSearch`로 `07-ux/validation-rules.md`에서 검증 규칙(예: `필수|최소|최대|형식|패턴`)을 검색하고, `04-business-logic/business-rules.md`에서 동일 필드의 규칙을 검색하여 대조. 프론트엔드와 백엔드 규칙이 불일치하면 양쪽 문서에 ⚠️ 불일치 표기 추가
- [ ] **Step 03 → api-inventory.md 업데이트 필요?**
  - 화면 조작→API 호출 매핑에서 발견한 누락 API가 있는지 확인
  - 검증 방법: `grepSearch`로 `07-ux/interaction-patterns.md`에서 API 호출 패턴(`/api|fetch|request|call`)을 검색하여 화면에서 호출하는 API 목록을 추출하고, `03-api-data/api-endpoints.md`에서 해당 API가 등재되어 있는지 확인. 누락된 API는 `api-endpoints.md`에 추가

> 업데이트가 필요한 경우, 해당 산출물 파일을 직접 수정하고 `_context-notes.md`에 변경 사유를 기록하세요.


## ➡️ 다음 Step

- 다음: [Step 08 — 인프라, 외부 연동, STB, 비기능 설계도 추출](08-infra-and-integration.md)
- 이전: [Step 06 — 품질 관련 설계도 추출](06-quality-extraction.md)
