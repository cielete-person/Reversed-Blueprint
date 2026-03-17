---
inclusion: manual
---

# Step 0: 워크스페이스 구조 및 서비스별 작업 가이드

> 참조: #[[file:project/extraction-checklist.md]]

## 목표

서비스별 소스코드를 Bitbucket DC에서 clone하고, KIRO로 설계도를 추출할 수 있는 폴더 구조를 구성한다.
설계도는 서비스별로 독립 관리하되, 최종적으로 전체 통합본을 만들어 문서화(Confluence 등)에 활용한다.

## 워크스페이스 폴더 구조

```
프로젝트 루트/
├── .kiro/steering/              ← steering 문서 (본 파일 포함)
├── services/                    ← 서비스별 소스코드 + 설계도
│   ├── {서비스명}/
│   │   ├── src/                 ← Bitbucket에서 clone한 소스코드
│   │   └── docs/                ← KIRO가 추출한 설계도
│   │       ├── extraction/      ← Phase 1: 소스코드 기초 분석 결과
│   │       │   ├── 01-code-structure/
│   │       │   │   └── _backup/ ← 이전 버전 백업 (자동 생성)
│   │       │   ├── 01b-dead-code/
│   │       │   ├── 02-screens/
│   │       │   ├── 03-api-data/
│   │       │   ├── 04-business-logic/
│   │       │   ├── 05-security/
│   │       │   ├── 06-quality/
│   │       │   ├── 07-ux/
│   │       │   ├── 08-infra/
│   │       │   └── runtime-data.md  ← 런타임 트래픽/성능 데이터 (수작업 수집)
│   │       ├── views/           ← Phase 2: 아키텍처 View 문서
│   │       └── gap-analysis/    ← Phase 3: Gap 분석
│   │
│   ├── media-iptv-vod/          ← SVC-001: IPTV VOD
│   │   ├── src/                 ← git clone {Repo URL} services/media-iptv-vod/src
│   │   └── docs/
│   ├── auth-pass/               ← SVC-005: PASS
│   │   ├── src/
│   │   └── docs/
│   ├── msg-sms/                 ← SVC-011: SMS
│   │   ├── src/
│   │   └── docs/
│   └── ...                      ← 전체 30개 서비스 (service-inventory.md 참조)
│
├── docs-integrated/             ← 전체 서비스 통합 설계도 (복붙 문서화용)
│   ├── extraction/              ← Phase 1 통합본 (서비스별 섹션 구분)
│   │   ├── 01-code-structure.md
│   │   ├── 02-screens.md
│   │   ├── 03-api-data.md
│   │   ├── 04-business-logic.md
│   │   ├── 05-security.md
│   │   ├── 06-quality.md
│   │   ├── 07-ux.md
│   │   └── 08-infra.md
│   ├── views/                   ← Phase 2 통합본
│   │   ├── system-context.md
│   │   ├── component-code.md
│   │   ├── business-rules.md
│   │   ├── event-flow.md
│   │   ├── data.md
│   │   ├── ui-ux.md
│   │   ├── cross-platform-screen-map.md
│   │   ├── security.md
│   │   ├── quality.md
│   │   ├── stb-resources.md
│   │   ├── resilience-dr.md
│   │   └── ai-governance.md
│   ├── gap-analysis/            ← Phase 3 통합본 (cross-platform-gap.md 포함)
│   └── glossary/                ← 용어 사전 통합본
│
├── project/                     ← 프로젝트 관리 문서
│   ├── workplan-roadmap.md
│   ├── extraction-checklist.md
│   ├── service-inventory.md
│   ├── platform-service-inventory.md
│   ├── security-layer-checklist.md
│   ├── cdr-design-checklist.md
│   ├── manual-tasks.md              ← 수작업 항목 목록 (런타임 데이터, 인터뷰, 교차 검증)
│   └── prompt-cookbook.md            ← 프롬프트 쿡북 (효과적인 추가 프롬프트 패턴 축적소)
├── glossary.md                  ← 용어 사전 초안
```

## 서비스 인벤토리 관련 문서

프로젝트에는 서비스 인벤토리 관련 문서가 2개 있으며, 역할이 다르다:

| 문서 | 역할 | 주요 컬럼 |
|---|---|---|
| `project/service-inventory.md` | **작업 관리용** (KIRO 작업 시 참조) | 서비스 ID, 폴더명, Repo URL, 소스 유무, 분석 진행 현황 |
| `project/platform-service-inventory.md` | **플랫폼 관점 참조용** | 플랫폼 그룹, 앱 목록, 관련 단말, 관련 서버 |

### 서비스 인벤토리 작성

작업 시작 전, `project/service-inventory.md`에서 대상 서비스의 Repo URL과 소스 유무를 확정하라.
30개 서비스의 서비스 ID, 폴더명은 이미 등록되어 있다.

- 폴더명 규칙: `{플랫폼약어}-{서비스영문명}` (예: `media-iptv-vod`, `auth-pass`)
- 플랫폼 약어: `media`, `auth`, `msg`, `pay`, `adm`, `iot`, `loc`, `voip`

## 서비스 폴더 생성 (_template 복사)

새 서비스 작업을 시작할 때, `services/_template/` 폴더를 복사하여 서비스 폴더를 생성한다:

```bash
# Windows (PowerShell)
Copy-Item -Recurse services/_template services/{폴더명}

# macOS / Linux
cp -r services/_template services/{폴더명}

# 예시: SVC-001 IPTV VOD
Copy-Item -Recurse services/_template services/media-iptv-vod
```

이렇게 하면 `docs/extraction/`, `docs/views/`, `docs/gap-analysis/` 폴더가 자동으로 생성된다.

## 서비스별 소스코드 준비

각 서비스의 소스코드를 `services/{서비스명}/src/` 에 clone한다.

### Baseline Commit 확정

clone 후, 분석 대상 브랜치와 커밋을 확정한다 (manual-tasks.md P0-4 참조):
- 권장: `main` 또는 `master` 브랜치의 최신 태그 또는 특정 커밋
- 분석 중 코드 변경이 발생해도 Baseline 기준으로 분석 완료
- 분석 완료 후 Baseline 이후 변경분은 증분 분석으로 처리

```bash
# 단일 Repo 서비스
git clone {Repo URL} services/media-iptv-vod/src
git clone {Repo URL} services/auth-pass/src
git clone {Repo URL} services/msg-sms/src
```

### 멀티 Repo 서비스 (앱 + 서버가 별도 Repo인 경우)

```bash
# 앱/서버 별도 Repo인 경우 src/ 하위에 구분하여 clone
git clone {Android Repo URL} services/media-iptv-vod/src/app-android
git clone {iOS Repo URL} services/media-iptv-vod/src/app-ios
git clone {STB Repo URL} services/media-iptv-vod/src/app-stb
git clone {Server Repo URL} services/media-iptv-vod/src/server
```

`service-inventory.md`에 Repo URL이 여러 개인 경우, 비고 컬럼에 Repo 구분을 기재한다.

## KIRO 서비스별 작업 방법

### 작업 단위
- KIRO에서 steering 문서(01~10)를 실행할 때, 한 번에 하나의 서비스를 대상으로 작업한다
- 채팅에서 대상 서비스를 지정하라:
  ```
  #01-code-structure-scan 서비스: mobile-home
  ```
- KIRO는 `services/mobile-home/src/` 를 분석하고, 결과를 `services/mobile-home/docs/` 에 저장한다

### 서비스별 작업 순서
1. `service-inventory.md`에서 대상 서비스 확인
2. 소스코드가 `services/{서비스명}/src/`에 clone되어 있는지 확인
3. steering 01~08을 순서대로 실행 (Phase 1)
4. steering 09 실행 (Phase 2 — View 생성)
5. steering 10 실행 (Phase 3 — Gap 분석)
6. 다음 서비스로 이동하여 1~5 반복

### 소스 없는 서비스
- `service-inventory.md`에서 소스 유무가 ❌인 서비스는 Step 01에서 별도 처리
- 배포 설정, 네트워크 구성도, 담당자 인터뷰 기반으로 추정 분석

## 통합 문서 생성 (docs-integrated)

모든 서비스의 개별 분석이 완료된 후, 통합 문서를 생성한다.

### 통합 원칙
- 각 통합 문서는 서비스별 섹션으로 구분하되, 하나의 파일로 합친다
- Confluence/문서 포털에 복붙할 때 이 통합본을 사용한다
- 통합 문서 내 서비스 구분 형식:

```markdown
# 01. 코드 구조 분석 — 통합본

## SVC-001: IPTV VOD (media-iptv-vod)
(services/media-iptv-vod/docs/extraction/01-code-structure/ 내용)

---

## SVC-005: PASS (auth-pass)
(services/auth-pass/docs/extraction/01-code-structure/ 내용)

---
...
```

### 서비스 횡단 View (Cross-Service Views)
- Phase 2의 일부 View는 서비스를 횡단하는 통합 관점이 필요하다:
  - 시스템 컨텍스트 다이어그램 (C4 Level 1) — 전체 서비스 간 관계
  - 서비스 간 통신 매트릭스 — 전체 서비스 간 API/MQ 호출 관계
  - 외부 연동 시스템 통합 인벤토리 — 서비스별 외부 연동을 하나로 합침
  - 용어 사전 — 전체 서비스 통합
- 이 문서들은 `docs-integrated/views/`에 직접 작성한다

## 완료 기준
- `service-inventory.md`가 작성됨
- 각 서비스의 소스코드가 `services/{서비스명}/src/`에 준비됨
- 폴더 구조가 위 규칙대로 생성됨

## 산출물 버전 백업 정책

설계도 파일을 재추출(덮어쓰기)할 때, 이전 버전을 자동으로 백업한다.

### 백업 규칙
- **대상**: `services/*/docs/` 및 `docs-integrated/` 하위의 `.md` 파일 (README.md 제외)
- **백업 위치**: 해당 파일과 같은 폴더 내 `_backup/`
- **백업 파일명**: `{원본파일명}_v{버전}_{yyyyMMdd_HHmm}.md`
  - 버전 번호: `_backup/` 내 동일 원본명 파일 수 + 1
  - 날짜시간: 백업 시점 기준
- **최초 생성 시**: 기존 파일이 없으므로 백업 불필요
- **자동 실행**: `backup-before-overwrite` hook이 write 도구 사용 전에 자동 체크

### 백업 예시
```
services/mobile-home/docs/extraction/03-api-data/
├── api-endpoints.md                              ← 현재 버전 (최신)
├── erd.md
└── _backup/
    ├── api-endpoints_v1_20260310_0930.md          ← 1차 추출본
    ├── api-endpoints_v2_20260315_1400.md          ← 2차 재추출본
    └── erd_v1_20260310_0945.md
```

### 백업 제외 대상
- `README.md` 파일
- `_backup/` 폴더 내 파일 (백업의 백업은 하지 않음)
- `glossary.md` (루트 용어 사전은 Git 버전 관리로 대체)
