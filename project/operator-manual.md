# 설계도 파묘 — 작업자용 매뉴얼

> KIRO를 사용하여 서비스 소스코드에서 설계도를 추출하는 작업자를 위한 실행 가이드입니다.
> 프로젝트 개요: [README.md](../README.md) | 추출 항목: [extraction-checklist.md](extraction-checklist.md)

---

## 0. 사전 준비

### 0-1. 필수 설치

| 도구 | 용도 | 설치 확인 |
|---|---|---|
| KIRO IDE | AI 기반 설계도 추출 | KIRO 실행 가능 여부 확인 |
| Git | 소스코드 clone, 산출물 버전 관리 | `git --version` |
| Bitbucket DC 접근 권한 | 대상 서비스 소스코드 clone | VPN 연결 + Bitbucket 로그인 확인 |

### 0-2. 워크스페이스 열기

1. 프로젝트 Repository를 clone한다:
   ```bash
   git clone https://github.com/cielete-person/Reversed-Blueprint.git
   ```
2. KIRO에서 clone한 폴더를 연다: `File > Open Folder > Reversed-Blueprint`
3. 채팅에서 `#` 입력 시 steering 문서 목록이 보이는지 확인한다

### 0-3. KIRO 모드 설정

| 모드 | 설명 | 권장 상황 |
|---|---|---|
| Autopilot | 파일 생성/수정을 자동 수행 | 익숙해진 후, 반복 작업 시 |
| Supervised | 변경 사항 제안 → 작업자 승인 후 반영 | 처음 작업 시, 중요 서비스 |

처음에는 Supervised 모드로 시작하여 산출물 품질을 확인하면서 진행할 것을 권장한다.

---

## 1. 대상 서비스 확인 및 Repo URL 확정

### 1-1. 서비스 인벤토리 확인

`project/service-inventory.md`를 열어 대상 서비스를 확인한다.

- 30개 서비스가 등록되어 있으며, 서비스 ID(SVC-001~030), 폴더명, 담당팀이 기재되어 있다
- 플랫폼 관점 상세 정보(앱/단말/서버 관계)는 `project/platform-service-inventory.md` 참조

### 1-2. Repo URL 확정 (현재 TBD 상태)

현재 모든 서비스의 Bitbucket Repo URL이 `TBD`이다. 작업 시작 전 반드시 확정해야 한다.

1. 담당팀에 Bitbucket Repo URL을 확인한다
2. `project/service-inventory.md`의 해당 서비스 행에 Repo URL을 기입한다
3. 소스 유무 컬럼을 `🔍` → `✅` (소스 있음) 또는 `❌` (소스 없음)으로 변경한다

### 1-3. 멀티 Repo 서비스 처리

앱(iOS/Android)과 서버가 별도 Repository인 경우:

```
services/{서비스명}/src/
├── app-android/     ← Android 앱 Repo clone
├── app-ios/         ← iOS 앱 Repo clone
├── app-stb/         ← STB 앱 Repo clone (해당 시)
└── server/          ← 서버 Repo clone
```

clone 명령 예시:
```bash
# 멀티 Repo (예: SVC-001 IPTV VOD)
git clone {Android Repo URL} services/media-iptv-vod/src/app-android
git clone {iOS Repo URL} services/media-iptv-vod/src/app-ios
git clone {STB Repo URL} services/media-iptv-vod/src/app-stb
git clone {Server Repo URL} services/media-iptv-vod/src/server

# 단일 Repo (예: SVC-006 휴대폰 본인확인 — 서버만)
git clone {Repo URL} services/auth-phone-verify/src
```

`service-inventory.md`에 Repo URL이 여러 개인 경우, 비고 컬럼에 Repo 구분을 기재한다.

---

## 2. 서비스 폴더 구조 생성

### 2-1. _template 복사

새 서비스 작업을 시작할 때, `services/_template/` 폴더를 복사하여 서비스 폴더를 생성한다.

```bash
# Windows (PowerShell)
Copy-Item -Recurse services/_template services/{폴더명}

# macOS / Linux
cp -r services/_template services/{폴더명}

# 예시: SVC-001 IPTV VOD
Copy-Item -Recurse services/_template services/media-iptv-vod
```

복사 후 폴더 구조:
```
services/media-iptv-vod/
├── src/                         ← 여기에 소스코드 clone
│   └── README.md
└── docs/                        ← KIRO가 산출물을 저장할 위치
    ├── extraction/              ← Phase 1 산출물 (Step 01~08)
    ├── views/                   ← Phase 2 산출물 (Step 09)
    └── gap-analysis/            ← Phase 3 산출물 (Step 10)
```

### 2-2. 소스코드 clone

```bash
# service-inventory.md에서 확정된 Repo URL 사용
git clone {Repo URL} services/{폴더명}/src
```

> ⚠️ clone 시 `services/{폴더명}/src/` 경로에 직접 clone해야 한다.

### 2-3. 확인

- `services/{폴더명}/src/` 에 소스코드 파일이 존재하는지 확인
- `services/{폴더명}/docs/extraction/`, `docs/views/`, `docs/gap-analysis/` 폴더가 존재하는지 확인

---

## 3. KIRO로 설계도 추출 실행

### 3-1. 작업 흐름 개요

```
서비스 선택 → Step 01~08 (Phase 1) → Step 09 (Phase 2) → Step 10 (Phase 3) → 다음 서비스
```

모든 steering 문서는 KIRO 채팅에서 `#` + 문서명으로 호출한다.

### 3-2. Step별 실행 방법

KIRO 채팅창에 아래 형식으로 입력한다:

```
#{steering 문서명} 서비스: {폴더명}
```

**실행 예시** (SVC-001 IPTV VOD 기준):

| 순서 | 채팅 입력 | 작업 내용 | 산출물 위치 |
|---|---|---|---|
| Step 01 | `#01-code-structure-scan 서비스: media-iptv-vod` | 기술 스택, 의존성, 플랫폼 분기 | `extraction/01-code-structure/` |
| Step 02 | `#02-screen-inventory 서비스: media-iptv-vod` | 화면 목록, 크로스 플랫폼 동기화 | `extraction/02-screens/` |
| Step 03 | `#03-api-and-data 서비스: media-iptv-vod` | API, DB 스키마, 쿼리 패턴 | `extraction/03-api-data/` |
| Step 04 | `#04-business-logic 서비스: media-iptv-vod` | 비즈니스 규칙, 상태 전이, Use Case, NFR 체크포인트 | `extraction/04-business-logic/` |
| Step 05 | `#05-security-extraction 서비스: media-iptv-vod` | 크리덴셜, 개인정보, 보안 | `extraction/05-security/` |
| Step 06 | `#06-quality-extraction 서비스: media-iptv-vod` | 테스트, 로깅, 의존성 건강도 | `extraction/06-quality/` |
| Step 07 | `#07-ux-extraction 서비스: media-iptv-vod` | 인터랙션, 유효성 검증, 접근성 | `extraction/07-ux/` |
| Step 08 | `#08-infra-and-integration 서비스: media-iptv-vod` | 외부 연동, 배포, STB 리소스 | `extraction/08-infra/` |
| Step 09 | `#09-architecture-views 서비스: media-iptv-vod` | C4 다이어그램, Stakeholder View | `views/` |
| Step 10 | `#10-gap-analysis 서비스: media-iptv-vod` | 추적성 매핑, Gap 분석 | `gap-analysis/` |

> 산출물 위치는 모두 `services/media-iptv-vod/docs/` 하위이다.

### 3-3. Step 실행 시 KIRO 동작

각 Step을 실행하면 KIRO는 다음과 같이 동작한다:

1. **서비스 확인**: `service-inventory.md`에서 서비스 정보를 조회하고 확인 메시지를 출력
   ```
   📋 대상 서비스 확인:
   - 서비스 ID: SVC-001
   - 서비스명: IPTV VOD
   - 폴더명: media-iptv-vod
   - 소스 경로: services/media-iptv-vod/src/
   - 산출물 경로: services/media-iptv-vod/docs/
   
   이 서비스로 진행할까요?
   ```
2. **승인 후 분석 시작**: "네" 또는 "진행"으로 응답하면 소스코드 분석 시작
3. **산출물 생성**: 분석 결과를 `services/{서비스명}/docs/` 하위에 Markdown으로 저장
4. **확인 상태 표기**: 모든 항목에 ✅/⚠️/❌/🔍 상태를 표기

### 3-4. Step별 완료 확인

각 Step 완료 후 아래를 확인한다:

- [ ] 산출물 파일이 해당 폴더에 생성되었는가
- [ ] 모든 항목에 확인 상태(✅/⚠️/❌/🔍)가 표기되었는가
- [ ] 🔍(인터뷰필요) 항목이 있다면 별도 메모해 두었는가
- [ ] 산출물 내용이 합리적인가 (Supervised 모드에서 검토)

> 📌 `[인터뷰]` 또는 `🔍` 태그 항목이 나오면, `project/manual-tasks.md`의 해당 수작업 항목을 참조하여 병행 수집하라. 특히 런타임 데이터(P1-1~P1-3), IaC 파일 수집(P1-7), 외부 연동 SLA(P1-8) 등은 KIRO 추출과 동시에 진행해야 Phase 2/3에서 활용할 수 있다.

### 3-5. 소스 없는 서비스 처리

`service-inventory.md`에서 소스 유무가 `❌`인 서비스는:
- 배포 설정, 네트워크 구성도, 담당자 인터뷰 기반으로 추정 분석
- 대부분의 항목이 `🔍 인터뷰필요` 또는 `❌ 미확인`으로 표기됨
- Dockerfile, k8s manifest, CI/CD 파이프라인 등 가용 자료를 최대한 활용

---

## 4. 산출물 버전 백업

KIRO에 `backup-before-overwrite` hook이 설정되어 있어, 기존 산출물을 덮어쓸 때 자동으로 백업된다.

- **대상**: `services/*/docs/` 및 `docs-integrated/` 하위 `.md` 파일 (README.md 제외)
- **백업 위치**: 해당 파일과 같은 폴더 내 `_backup/`
- **백업 파일명**: `{원본파일명}_v{버전}_{yyyyMMdd_HHmm}.md`

```
services/media-iptv-vod/docs/extraction/03-api-data/
├── api-endpoints.md                              ← 현재 버전 (최신)
└── _backup/
    ├── api-endpoints_v1_20260310_0930.md          ← 1차 추출본
    └── api-endpoints_v2_20260315_1400.md          ← 2차 재추출본
```

---

## 5. 진행 현황 업데이트

### 5-1. service-inventory.md 업데이트

각 Step 완료 시 `project/service-inventory.md`의 "분석 진행 현황" 테이블을 업데이트한다.

| 상태 | 의미 |
|---|---|
| ⬜ | 미시작 |
| 🔄 | 진행중 |
| ✅ | 완료 |
| ⏭️ | 해당없음 |

### 5-2. 인터뷰 필요 항목 관리

`🔍 인터뷰필요`로 표기된 항목은 담당자 확인 후 산출물에 반영하고 ✅로 변경한다.

---

## 6. 전체 서비스 통합 (docs-integrated)

### 6-1. 통합 시점

모든 서비스(또는 의미 있는 단위)의 개별 분석이 완료된 후 통합 문서를 생성한다.

### 6-2. 통합 방법

KIRO 채팅에서:
```
모든 서비스의 Phase 1/2/3 산출물을 docs-integrated/에 통합해줘
```

### 6-3. 통합 문서 구조

```
docs-integrated/
├── extraction/              ← Phase 1 통합본 (서비스별 섹션 구분)
├── views/                   ← Phase 2 통합본 (11개 View + 크로스 플랫폼)
├── gap-analysis/            ← Phase 3 통합본
└── glossary/                ← 용어 사전 통합본
```

통합 문서 내 서비스 구분 형식:
```markdown
# 01. 코드 구조 분석 — 통합본

## SVC-001: IPTV VOD (media-iptv-vod)
(services/media-iptv-vod/docs/extraction/01-code-structure/ 내용)

---

## SVC-005: PASS (auth-pass)
(services/auth-pass/docs/extraction/01-code-structure/ 내용)
```

---

## 7. Git 커밋 및 푸시

```bash
# 서비스별 분석 완료 시
git add services/{폴더명}/docs/
git commit -m "feat(SVC-XXX): {서비스명} Step {번호} 완료"

# 진행 현황 업데이트 시
git add project/service-inventory.md
git commit -m "chore: SVC-XXX 진행 현황 업데이트"

# 통합 문서 생성 시
git add docs-integrated/
git commit -m "feat: Phase 1 통합 문서 생성"

# 푸시
git push origin main
```

---

## 8. 전체 작업 흐름 요약

```
1. 사전 준비: KIRO 설치 → 워크스페이스 clone → KIRO에서 폴더 열기
       ↓
2. 서비스 선택: service-inventory.md → Repo URL 확정 → 소스 유무 갱신
       ↓
3. 폴더 생성 + 소스 clone: _template 복사 → git clone → 소스 확인
       ↓
4. Phase 1 추출 (Step 01~08): KIRO 채팅에서 순서대로 실행
   산출물 → services/{폴더명}/docs/extraction/
       ↓
5. Phase 2 View 생성 (Step 09)
   산출물 → services/{폴더명}/docs/views/
       ↓
6. Phase 3 Gap 분석 (Step 10)
   산출물 → services/{폴더명}/docs/gap-analysis/
       ↓
7. 진행 현황 업데이트 + Git 커밋/푸시
       ↓
8. 다음 서비스 → 2번으로 / 전체 완료 → docs-integrated/ 통합
```

---

## 9. 트러블슈팅 / FAQ

**Q1. KIRO 채팅에서 `#` 입력 시 steering 문서가 안 보여요**
- KIRO에서 프로젝트 루트 폴더를 열었는지 확인 (`.kiro/steering/`이 루트에 있어야 함)
- KIRO를 재시작해 본다

**Q2. steering 실행 시 "서비스를 찾을 수 없다"고 나와요**
- `service-inventory.md`에 해당 서비스의 폴더명이 정확히 등록되어 있는지 확인
- 채팅에서 입력한 서비스명이 폴더명과 일치하는지 확인 (예: `media-iptv-vod`)

**Q3. 소스코드가 너무 커서 KIRO가 분석을 못 해요**
- 모듈/패키지 단위로 나누어 분석을 요청한다
- 예: `#01-code-structure-scan 서비스: media-iptv-vod 범위: server/api-gateway 모듈만`

**Q4. 이전 산출물을 덮어쓰고 싶어요**
- 같은 Step을 다시 실행하면 된다. KIRO가 자동으로 `_backup/`에 이전 버전을 백업한다

**Q5. 멀티 Repo 서비스에서 앱과 서버를 따로 분석해야 하나요?**
- 하나의 서비스 폴더 안에 모든 Repo를 clone한 후, Step을 한 번에 실행한다
- KIRO가 `src/` 하위의 모든 소스를 통합 분석한다

**Q6. 🔍(인터뷰필요) 항목은 어떻게 처리하나요?**
- 담당팀에 확인 후, 산출물 파일을 직접 수정하거나 KIRO에게 보완을 요청한다

**Q7. 서비스 분석 순서가 정해져 있나요?**
- 정해진 순서는 없다. 권장: 소스 확보된 서비스부터, 규모 작은 서비스로 연습, 같은 플랫폼 그룹 연속 처리

**Q8. Step 순서를 건너뛸 수 있나요?**
- Step 01~08은 독립적이므로 순서 변경 가능 (순서대로 권장)
- Step 09는 반드시 Phase 1 완료 후, Step 10은 Step 09 완료 후 실행

---

## 10. 참조 문서 목록

| 문서 | 위치 | 용도 |
|---|---|---|
| 프로젝트 개요 | `README.md` | 프로젝트 목표, 폴더 구조, Phase 구성 |
| 추출 항목 체크리스트 | `project/extraction-checklist.md` | 전체 추출 항목 상세 (Phase 1/2/3) |
| 서비스 인벤토리 | `project/service-inventory.md` | 서비스 목록, Repo URL, 진행 현황 |
| 플랫폼 서비스 인벤토리 | `project/platform-service-inventory.md` | 앱/단말/서버 관계 참조 |
| 보안 체크리스트 | `project/security-layer-checklist.md` | 보안센터 217항목 |
| CDR 설계 체크리스트 | `project/cdr-design-checklist.md` | CTO품질PMO팀 CDR 가이드 매핑 |
| 워크플랜 로드맵 | `project/workplan-roadmap.md` | Phase 0~4 일정 계획 |
| 수작업 항목 목록 | `project/manual-tasks.md` | KIRO 자동 추출 불가 항목 (런타임 데이터, 인터뷰, 교차 검증 등) |
| 용어 사전 | `glossary.md` | 프로젝트 공통 용어 정의 |
| Steering 문서 (12개) | `.kiro/steering/` | KIRO 실행 가이드 (Step 00~10) |