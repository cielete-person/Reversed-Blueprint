---
inclusion: manual
---

# 설계도 파묘 프로젝트 — Steering 개요

이 프로젝트는 소스코드를 분석하여 Stakeholder별 설계도를 역추적·추출하는 작업입니다.

> 신규 작업자는 먼저 [작업자용 매뉴얼](../project/operator-manual.md)을 읽고 시작하세요.

## 워크스페이스 구조

> 상세 폴더 구조 및 서비스별 작업 방법은 `00-workspace-setup` steering 참조

```
프로젝트 루트/
├── services/{서비스명}/src/      ← Bitbucket clone 소스코드
├── services/{서비스명}/docs/     ← KIRO 추출 설계도 (서비스별)
├── project/                      ← 프로젝트 관리 문서
│   ├── workplan-roadmap.md
│   ├── extraction-checklist.md
│   ├── service-inventory.md
│   ├── platform-service-inventory.md
│   ├── security-layer-checklist.md
│   ├── cdr-design-checklist.md
│   ├── manual-tasks.md
│   └── prompt-cookbook.md
├── docs-integrated/              ← 전체 서비스 통합 설계도 (복붙 문서화용)
```

## 작업 순서

아래 steering 문서를 순서대로, 서비스 단위로 실행하세요.
채팅에서 대상 서비스를 지정: `#01-code-structure-scan 서비스: {서비스명}`

| 순서 | Steering 문서 | 작업 내용 | 산출물 위치 (서비스별) |
|---|---|---|---|
| 0 | `00-workspace-setup` | 폴더 구조 구성, 서비스 인벤토리, 소스 clone | `service-inventory.md` |
| 1 | `01-code-structure-scan` | Repository 스캔, 기술 스택 식별, 의존성 추출 | `services/{서비스명}/docs/extraction/01-code-structure/` |
| 1b | `01b-dead-code-analysis` | Dead Code, 미사용 DB/API/설정 식별, 코드 연대 분석 | `services/{서비스명}/docs/extraction/01b-dead-code/` |
| 1c | `01c-common-module-grouping` | 공통 모듈 그룹핑, 사내 라이브러리 식별, 의존 방향·영향 범위 분석 | `services/{서비스명}/docs/extraction/01c-common-modules/` |
| 2 | `02-screen-inventory` | 화면 목록 추출, 화면명 정규화, 화면 ID 부여 | `services/{서비스명}/docs/extraction/02-screens/` |
| 3 | `03-api-and-data` | API 엔드포인트 추출, DB 스키마 역추적, ERD | `services/{서비스명}/docs/extraction/03-api-data/` |
| 4 | `04-business-logic` | 비즈니스 규칙, 에러 처리, 상태 전이, 이벤트 흐름, Use Case Tree, NFR 체크포인트 | `services/{서비스명}/docs/extraction/04-business-logic/` |
| 5 | `05-security-extraction` | 크리덴셜, 개인정보, 세션/토큰, 입력 보안, API 보안, 감사 로그, 암호화, 하드닝 | `services/{서비스명}/docs/extraction/05-security/` |
| 6 | `06-quality-extraction` | 테스트 현황, 변경 빈도, 로깅, 의존성 건강도, 동시성, 정합성, 성능 | `services/{서비스명}/docs/extraction/06-quality/` |
| 7 | `07-ux-extraction` | 인터랙션 패턴, 유효성 검증, 로딩 UX, 다국어/접근성, 딥링크, 피처 플래그 | `services/{서비스명}/docs/extraction/07-ux/` |
| 8 | `08-infra-and-integration` | 외부 연동, 배포 토폴로지, STB 리소스, 비기능 현황 | `services/{서비스명}/docs/extraction/08-infra/` |
| 9 | `09-architecture-views` | Phase 1 결과 기반 C4 다이어그램, Stakeholder별 View 문서, Layer Stack View 생성 | `services/{서비스명}/docs/views/` |
| 10 | `10-gap-analysis` | 추적성 매핑, Gap 분석, 위험도 매트릭스, 용어 사전 확정 | `services/{서비스명}/docs/gap-analysis/` |
| — | 통합 | 모든 서비스 완료 후, 서비스별 결과를 `docs-integrated/`에 통합 | `docs-integrated/` |

> 📌 각 Step 실행 중 `[인터뷰]` 태그 항목이 나오면, [수작업 항목 목록](../project/manual-tasks.md)을 참조하여 해당 수작업을 병행하라.

## 공통 규칙

### ★ 대용량 코드 분석 전략 (2-Pass 스캔)

> 대용량 Repo(10,000파일 이상) 또는 멀티 모듈(3개 이상) 서비스에서는 컨텍스트 윈도우 한계로 인해
> 실제 존재하는 항목을 "해당 없음(❌)"으로 오판하는 현상이 발생할 수 있다.
> 이를 방지하기 위해 아래 2-Pass 스캔 전략을 반드시 적용하라.

#### Pass 1: 구조 스캔 (탐색 범위 확정)
1. `listDirectory`로 전체 디렉토리 트리를 depth 3까지 스캔하라
2. `fileSearch`로 각 Step의 "필수 탐색 파일 패턴"(아래 각 steering 참조)을 검색하라
3. `grepSearch`로 핵심 키워드(어노테이션, import, 설정 키)를 검색하라
4. 탐색 결과를 "분석 대상 파일 목록"으로 정리하라 (파일 경로 + 예상 카테고리)

#### Pass 2: 정밀 분석 (파일 단위 추출)
1. Pass 1에서 식별된 파일 목록을 대상으로 `readCode`/`readFile`로 상세 분석하라
2. 한 번에 분석하는 파일 수를 10개 이내로 제한하라 (컨텍스트 윈도우 보호)
3. 분석 결과를 산출물에 즉시 기록하라 (메모리 의존 금지)

#### "해당 없음(❌)" 판정 최소 검증 절차
- 어떤 항목을 "해당 없음(❌)"으로 판정하기 전에, 반드시 아래 검증을 수행하라:
  1. `grepSearch` 최소 3회: 직접 키워드 + 유사 키워드 + 프레임워크별 변형 키워드
  2. `fileSearch` 최소 2회: 파일명 패턴 + 확장자 패턴
  3. 설정 파일 검색 1회: `application*.yml`, `*.properties`, `*.env`, `config/` 하위
- 위 6회 검색 모두 결과 없음일 때만 "❌ 해당 없음"으로 판정하라
- 검색 결과가 1건이라도 있으면 Pass 2 정밀 분석으로 진행하라
- 판정 근거를 산출물 하단 "누락 검증 로그" 테이블에 기록하라

#### 분할 실행 원칙
- 10,000파일 이상 Repo: 모듈/패키지 단위로 분할하여 Step을 반복 실행하라
- 멀티 모듈 3개 이상: 모듈별로 Pass 1→Pass 2를 개별 수행 후 결과를 병합하라
- 멀티 Repo(앱+서버): Repo별로 독립 분석 후 통합 산출물에서 교차 매핑하라

#### 누락 검증 로그 (산출물 하단 필수 포함)
모든 산출물 파일 하단에 아래 형식의 "누락 검증 로그" 테이블을 포함하라:

```markdown
---
## 누락 검증 로그

| 항목 | 검색 키워드 | grepSearch 결과 | fileSearch 결과 | 설정파일 결과 | 최종 판정 | 판정 근거 |
|---|---|---|---|---|---|---|
| DB 마이그레이션 | flyway, liquibase, migration | 0건 | 0건 | 0건 | ❌ 해당 없음 | 6회 검색 모두 결과 없음 |
| 캐시 설정 | @Cacheable, redis, ehcache | 3건 | 1건 | 2건 | ✅ 확인완료 | RedisConfig.java에서 확인 |
```

0. **서비스 ID 확인 필수 (작업 시작 전 반드시 수행)**
   - 사용자가 서비스명을 지정하면, 반드시 `project/service-inventory.md`를 열어 해당 서비스의 정보를 확인하라
   - 확인 항목: 서비스 ID, 폴더명, 소스 유무, Bitbucket Repo URL
   - **사용자가 이미 clone한 소스 경로를 알려준 경우**:
     1. `service-inventory.md`에서 서비스 정보를 확인하라
     2. `services/_template/`을 복사하여 `services/{폴더명}/` 폴더를 생성하라
     3. 사용자가 알려준 경로의 소스코드를 `services/{폴더명}/src/`로 복사하라 (멀티 Repo면 `src/app-android/`, `src/app-ios/`, `src/server/` 등으로 구분)
     4. `service-inventory.md`의 소스 유무를 `✅`로, Repo URL을 기입하라
     5. 아래 확인 메시지를 출력하고 승인을 받은 후 Step 01부터 시작하라
   - **사용자가 여러 경로를 알려준 경우 (멀티 Repo 자동 판별)**:
     1. 각 경로의 루트 디렉토리를 `listDirectory`로 스캔하라
     2. 아래 기준으로 플랫폼 유형을 자동 판별하라:
        | 판별 파일/패턴 | 플랫폼 유형 | 배치 폴더 |
        |---|---|---|
        | `AndroidManifest.xml` + `build.gradle(.kts)` (android 플러그인) | Android 앱 | `src/app-android/` |
        | `Info.plist` + `*.xcodeproj` 또는 `*.xcworkspace` | iOS 앱 | `src/app-ios/` |
        | `AndroidManifest.xml` + Leanback/TV 의존성 | STB 앱 | `src/app-stb/` |
        | `pom.xml` 또는 `build.gradle(.kts)` (spring 의존성) | 서버 (Java/Kotlin) | `src/server/` |
        | `package.json` + express/nest/koa 의존성 | 서버 (Node.js) | `src/server/` |
        | `pubspec.yaml` (Flutter) 또는 `react-native` 의존성 | 크로스플랫폼 앱 | `src/app-cross/` |
     3. 판별 결과를 아래 형식으로 출력하고 승인을 받은 후 배치하라:
        ```
        📋 멀티 Repo 플랫폼 자동 판별 결과:
        | 경로 | 판별 근거 | 플랫폼 유형 | 배치 위치 |
        |---|---|---|---|
        | C:\Projects\my-app-android | AndroidManifest.xml, com.android.application | Android 앱 | src/app-android/ |
        | C:\Projects\my-app-ios | Info.plist, MyApp.xcworkspace | iOS 앱 | src/app-ios/ |
        | C:\Projects\my-server | pom.xml, spring-boot-starter | 서버 (Java) | src/server/ |
        
        이대로 배치할까요? (수정이 필요하면 알려주세요)
        ```
     4. 승인 후 각 경로를 해당 배치 위치로 복사하고 Step 01부터 시작하라
   - 확인 후 사용자에게 아래 형식으로 응답하라:
     ```
     📋 대상 서비스 확인:
     - 서비스 ID: SVC-001
     - 서비스명: IPTV VOD
     - 폴더명: media-iptv-vod
     - 플랫폼: 미디어플랫폼
     - 소스 유무: 🔍 (확인 필요)
     - 소스 경로: services/media-iptv-vod/src/
     - 산출물 경로: services/media-iptv-vod/docs/
     
     이 서비스로 진행할까요?
     ```
   - 사용자가 확인(승인)한 후에만 분석 작업을 시작하라
   - 서비스명이 불명확하거나 `service-inventory.md`에 없는 경우, 30개 서비스 목록을 보여주고 선택을 요청하라
   - 앱/단말/서버 등 플랫폼 관점 상세 정보가 필요하면 `project/platform-service-inventory.md`도 함께 참조하라

1. **서비스 단위 작업**: 한 번에 하나의 서비스를 대상으로 작업. 소스는 `services/{서비스명}/src/`, 산출물은 `services/{서비스명}/docs/`

1-1. **Baseline Commit 확정**: 분석 시작 전 대상 서비스의 분석 기준 브랜치/커밋을 확정하라. 권장: `main` 또는 `master` 브랜치의 최신 태그 또는 특정 커밋. 분석 중 코드 변경이 발생해도 Baseline 기준으로 분석 완료. 분석 완료 후 Baseline 이후 변경분은 증분 분석으로 처리. (manual-tasks.md P0-4 참조)

2. **확인 상태 표기**: 모든 항목에 아래 상태를 반드시 표기
   - ✅ 확인완료 — 코드/설정에서 확인됨
   - ⚠️ 일부확인 — 부분적으로만 확인됨 (범위 명시)
   - ❌ 미확인 — 소스 없음, 접근 불가
   - 🔍 인터뷰필요 — 코드만으로 확인 불가

3. **화면명 정규화**: 화면 관련 산출물에서는 반드시 정규화된 대표 명칭 사용

3-1. **크로스 플랫폼 화면 동기화**: iOS/Android 모바일 앱이 모두 존재하는 서비스는 양 플랫폼 화면을 동시에 추출하고, 화면 ID를 플랫폼 무관하게 통합 부여하라. 플랫폼 전용 화면은 ⚠️ 표기. STB(AOSP/Android TV) 앱도 동일 기능 화면 매핑 포함.

3-2. **디바이스/플랫폼 분기 코드**: 모바일(iOS/Android), STB(AOSP/Android TV/자체 론처) 등 디바이스 환경에 따른 분기 코드를 반드시 식별하여 설계도에 포함하라. 공유 코드 vs 플랫폼 전용 코드 비율, 네이티브 모듈/브릿지, 권한 차이, 기종별 분기 등.

4. **산출물 포맷**: 각 산출물은 Markdown 테이블 또는 Mermaid 다이어그램으로 작성

5. **참조 문서**: #[[file:project/extraction-checklist.md]] 의 상세 항목을 기준으로 작업

6. **통합 문서**: 모든 서비스 분석 완료 후, `docs-integrated/`에 서비스별 섹션으로 구분된 통합본 생성 (Confluence 복붙용)

7. **산출물 버전 백업**: 설계도 파일을 덮어쓰기 전에 반드시 이전 버전을 백업

8. **보안 관련 steering/checklist 수정 시 필수 안내**
   - `05-security-extraction.md`, `security-layer-checklist.md`, `extraction-checklist.md`의 1-7 보안 섹션을 수정·고도화하려는 경우, 반드시 아래 안내를 먼저 출력하라:
     ```
     ⚠️ 보안 관련 steering/checklist 수정 전 필수 확인:
     
     현재 보안 체크리스트(217항목)는 보안기술팀 CTO 제공 기준입니다.
     이 항목을 추가·수정·고도화하려면, 아래 CTO보안허브 정책 문서를 반영하여
     1~2 depth 더 상세한 수준으로 설계도를 추출할 수 있도록 steering을 고도화해야 합니다.
     
     📌 반영 필수 정책 (CTO보안허브):
     - 패스워드 관리 정책 (복잡도, 주기, 이력, 잠금, 초기화 절차)
     - 개인정보 파기 정책 (파기 주기, 파기 방법, 파기 대상, 파기 증적)
     - 암호화 적용 정책 (대상 데이터, 알고리즘, 키 관리, 적용 구간)
     - 기타 CTO보안허브 내 설계/구현 관련 정책
     
     👉 CTO보안허브에서 해당 정책 문서를 확보한 후,
        security-layer-checklist.md 항목을 세분화하고
        05-security-extraction.md steering의 작업 절차를 고도화하세요.
     
     이 안내를 확인했으면 작업을 계속하세요.
     ```
   - 이 안내는 사용자 본인 포함 누구든 보안 steering을 수정하려 할 때 반드시 출력한다
   - 백업 위치: 해당 파일과 같은 폴더 내 `_backup/` 하위
   - 백업 파일명: `{원본파일명}_v{버전}_{yyyyMMdd_HHmm}.md`
     - 예: `api-endpoints.md` → `_backup/api-endpoints_v1_20260316_1430.md`
   - 버전 번호: 해당 파일의 `_backup/` 내 동일 원본명 파일 수 + 1
   - 최초 생성 시에는 백업 불필요 (파일이 존재하지 않으므로)
   - `_backup/` 폴더는 통합 문서(`docs-integrated/`) 생성 시 제외

### ★ Phase 간 역동기화 규칙

> Phase 2(Step 09) 또는 Phase 3(Step 10) 작업 중 이전 Phase 산출물의 누락·오류를 발견한 경우,
> 해당 산출물에 보정 내용을 역반영하여 전체 산출물 간 정합성을 유지하라.

#### 역동기화 트리거
- Phase 2(Step 09)에서 발견: Phase 1(Step 01~08) 산출물에 누락/오류가 있는 경우
- Phase 3(Step 10)에서 발견: Phase 1(Step 01~08) 또는 Phase 2(Step 09) 산출물에 누락/오류가 있는 경우

#### 역동기화 절차
1. 발견 즉시, 해당 산출물 파일을 열어 보정 내용을 추가하라
2. 보정 항목에는 `[P2 보정]` 또는 `[P3 보정]` 태그를 붙여 원래 추출 내용과 구분하라
3. 보정 내용은 기존 항목을 수정하지 말고, 항목 옆에 보정 태그와 함께 추가 기재하라
   - 예: `| PaymentService.refund() | ✅ 확인완료 | [P3 보정] 실제로는 ⚠️ 일부확인 — fallback 미구현 |`
4. 산출물 파일 하단에 "역동기화 로그" 테이블을 추가/갱신하라:

```markdown
---
## 역동기화 로그

| 발견 Phase | 발견 Step | 원본 산출물 | 보정 내용 | 보정일 |
|---|---|---|---|---|
| Phase 3 | Step 10-4 보안 Gap | extraction/05-security/credential-scan.md | API Key 평문 저장 1건 추가 발견 | 2026-03-17 |
| Phase 2 | Step 09 2-2 | extraction/01-code-structure/tech-stack.md | 내부 공통 라이브러리 2건 누락 보완 | 2026-03-17 |
```

#### 역동기화 범위 제한
- 보정은 **사실 관계 오류**(누락, 오판정, 상태 변경)에 한정하라
- 표현 방식, 포맷, 구조 변경은 역동기화 대상이 아니다
- 대량 보정(5건 이상)이 필요한 경우, 해당 Step을 재실행하는 것을 권장하라
