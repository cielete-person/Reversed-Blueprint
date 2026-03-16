# LG유플러스 서비스 설계도 역추적 프로젝트 — Workplan Roadmap

## 프로젝트 목표

운영 중인 모바일/홈 사업 서비스의 소스코드 및 인프라 현황으로부터,
Stakeholder 관점별 설계 문서(Architecture Views)를 역추적·생성하여
요구사항 → 설계 → 구현 간의 추적성(Traceability)을 확보한다.

> 설계도 추출 시 필요한 상세 항목 체크리스트는 [extraction-checklist.md](extraction-checklist.md) 참조

---

## Stakeholder별 필요 View

| Stakeholder | 관심 관점 | 산출물 |
|---|---|---|
| 사업부 PO | 비즈니스 기능 단위, 서비스 흐름, 화면 식별, 유저 시나리오 참조 | Business Capability Map, 서비스 기능 목록, 화면 카탈로그(ID/명칭), 인터랙션 패턴 맵, 검증 규칙 카탈로그 |
| 개발부서 PM | 시스템 구조, 모듈 의존성, API 명세, 변경 영향도 | Component Diagram, API Spec, 모듈 의존성 맵, 영향도 매트릭스 |
| 외주/내부 개발자 | 코드 구조, 빌드/배포, DB 스키마, STB 리소스 제약 | Code Structure Doc, ERD, 빌드 가이드, STB 리소스 프로파일 |
| UX센터 | 화면 흐름, UI 컴포넌트 구조, 화면 ID 체계, 인터랙션/로딩/접근성 패턴 | Screen Flow Map, UI Component Tree, 화면 카탈로그, 인터랙션 패턴 맵, 로딩 UX 맵, 접근성 현황 |
| 품질센터 | 테스트 범위, 품질 지표, 코드 건강도, 성능/정합성 위험 | Test Coverage Map, 테스트-기능 추적 매트릭스, 변경 빈도/결함 밀집도 맵, 로깅/모니터링 현황, 의존성 건강도, 동시성 안전성, 데이터 정합성, 성능 병목 맵, 정적분석 리포트 |
| 보안센터 | 인증/인가, 개인정보 처리, 암호화, 크리덴셜 관리, API 보안, 감사 로그, 보안 하드닝 | Security Architecture, PII 처리 현황 맵, 암호화 인벤토리, 크리덴셜 관리 현황, API 보안 매트릭스, 감사 로그 현황, 보안 하드닝 체크리스트, 서드파티 스크립트 인벤토리 |

---

## Phase 0: 준비 (2주)

**목표:** 프로젝트 범위 확정 및 환경 구축

**주요 태스크:**
- 대상 서비스 인벤토리 작성 (서비스 ID 채번 규칙 포함)
- 화면 ID 체계 및 채번 규칙 정의
- 용어 사전(Glossary) 초안 작성 (화면명 정규화 규칙 포함)
- Bitbucket DC 접근 권한 확보 및 Repository 목록 수집
- 소스코드 없는 서버 식별 및 대체 분석 방법 결정
- 분석 도구 선정 및 환경 구축
- 변경 요청(CR) 프로세스 및 템플릿 정의
- **문서 인프라 결정** (아래 상세)
- Stakeholder 킥오프 — 관점별 우선순위 합의

### 문서 인프라 결정 사항

Phase 0에서 아래 항목을 확정하여, Phase 1부터 산출물이 즉시 올바른 위치에 저장되도록 한다.

**1) 기준문서 저장소 (Source of Truth)**

| 옵션 | 장점 | 단점 | 비고 |
|---|---|---|---|
| Bitbucket DC (기존 인프라) | 소스코드와 동일 플랫폼, PR 리뷰 가능 | 비개발자 접근 불편 | 설계도 전용 repo 생성 권장 |
| GitHub Enterprise | PR 기반 리뷰, Actions CI/CD, Pages 지원 | 별도 라이선스, Bitbucket과 이중 관리 | 외부 공개 불필요 시 비추천 |
| GitLab Self-Hosted | MR 리뷰 + CI/CD + Pages 통합 | 별도 인프라 구축 | 이미 운영 중이면 유력 |

→ 결정 기준: 기존 인프라 활용 우선. Bitbucket DC에 `blueprint-docs` 전용 repo를 생성하고, Markdown 기준문서를 Git으로 버전 관리하는 것을 기본안으로 권장.

**2) View 플랫폼 (Stakeholder 열람용)**

| 옵션 | 대상 | 장점 | 단점 |
|---|---|---|---|
| Confluence | PO, UX센터, 품질센터, 보안센터 | 이미 사용 중일 가능성 높음, 비개발자 친화적 | Markdown 네이티브 미지원, 수동 동기화 필요 |
| Backstage (Spotify) | PM, 개발자 | 서비스 카탈로그 통합, TechDocs(Markdown→사이트) | 초기 구축 비용, 비개발자 진입장벽 |
| GitBook | 전체 | Markdown 네이티브, Git 동기화, 검색 우수 | SaaS 비용, 사내 보안 정책 확인 필요 |
| MkDocs + GitHub/GitLab Pages | PM, 개발자 | 무료, Markdown 네이티브, CI/CD 자동 배포 | 비개발자 편집 불편 |
| Notion | PO, UX센터 | 비개발자 편집 용이, 실시간 협업 | Git 연동 약함, 버전 관리 미흡 |

→ 권장 조합: **Git 저장소(기준문서) + Confluence(비개발자 View) + MkDocs/Pages(개발자 View)**
  - Git repo가 Single Source of Truth
  - CI/CD 파이프라인이 Git → Confluence 자동 동기화 + MkDocs 사이트 자동 빌드

**3) Stakeholder별 접근 권한 매트릭스**

| Stakeholder | Git 저장소 | Confluence | MkDocs/Pages | 편집 권한 |
|---|---|---|---|---|
| 사업부 PO | — | ✅ 열람 | — | Confluence 코멘트 |
| 개발부서 PM | ✅ PR 리뷰 | ✅ 열람 | ✅ 열람 | Git PR 승인 |
| 외주/내부 개발자 | ✅ PR 제출 | — | ✅ 열람 | Git PR 제출 |
| UX센터 | — | ✅ 열람/편집 | — | Confluence 직접 편집 (UI/UX View) |
| 품질센터 | ✅ PR 리뷰 | ✅ 열람 | ✅ 열람 | Git PR 승인 (품질 View) |
| 보안센터 | ✅ PR 리뷰 | ✅ 열람 | ✅ 열람 | Git PR 승인 (보안 View) |

**산출물:**
- 서비스 인벤토리 (서비스 ID, 서비스명, repo URL, 담당조직, 소스 유무)
- 화면 ID 채번 규칙서
- 용어 사전 (Glossary) 초안
- 변경 요청(CR) 프로세스 정의서 및 템플릿
- 프로젝트 헌장 및 R&R 정의
- **문서 인프라 결정서** (저장소, View 플랫폼, 접근 권한, 파이프라인 방식)

---

## Phase 1: 소스코드 탐색 및 기초 분석 (4주)

**목표:** 각 서비스의 기술 스택과 코드 구조를 파악

> 상세 추출 항목은 [extraction-checklist.md — Phase 1 추출 항목](extraction-checklist.md#phase-1-소스코드-기초-분석--추출-항목) 참조

**주요 태스크 카테고리:**
- 코드 구조 분석 (Repository 스캔, 의존성 추출)
- 화면 목록 추출 및 화면명 정규화
- API 엔드포인트 자동 추출
- DB 스키마 역추적
- 비즈니스 규칙 / 에러 처리 / 상태값 / 이벤트 흐름 추출
- 비기능 현황 수집 (NFR Baseline)
- 품질 관련 추출 (테스트 현황, 변경 빈도, 로깅, 의존성, 동시성, 정합성, 성능)
- 보안 관련 추출 (크리덴셜, 개인정보, 세션/토큰, 입력 보안, API 보안, 감사 로그, 암호화, 하드닝, 서드파티)
- 외부 연동 시스템 인벤토리
- 배포 토폴로지 추출
- STB 하드웨어 리소스 기초 분석
- UX 관련 추출 (인터랙션, 유효성 검증, 로딩 상태, 다국어/접근성, 딥링크, 피처 플래그)

**산출물:** → [extraction-checklist.md — Phase 1 산출물](extraction-checklist.md#phase-1-산출물) 참조

---

## Phase 2: 아키텍처 View 생성 (6주)

**목표:** Stakeholder 관점별 설계 문서 작성

> 상세 View 항목은 [extraction-checklist.md — Phase 2 View 항목](extraction-checklist.md#phase-2-아키텍처-view-생성-항목) 참조

**View 구성:**
- 2-1. 시스템 컨텍스트 & 컨테이너 View (C4 Level 1-2) → PO, PM
- 2-2. 컴포넌트 & 코드 View (C4 Level 3-4) → PM, 개발자
- 2-3. 비즈니스 규칙 & 에러 처리 View → PM, 개발자, 품질센터
- 2-4. 이벤트/메시지 흐름 View → PM, 개발자
- 2-5. 데이터 View → 개발자, 보안센터
- 2-6. UI/UX View → UX센터, PO
- 2-7. 보안 View → 보안센터
- 2-8. 품질 View → 품질센터
- 2-9. STB 하드웨어 리소스 관리 View → PM, 개발자, 품질센터

**산출물:** → [extraction-checklist.md — Phase 2 산출물](extraction-checklist.md#phase-2-산출물) 참조

---

## Phase 3: 추적성 매핑 및 Gap 분석 (4주)

**목표:** 요구사항 ↔ 설계 ↔ 코드 간 추적성 확보

> 상세 Gap 분석 항목은 [extraction-checklist.md — Phase 3 Gap 분석 항목](extraction-checklist.md#phase-3-추적성-매핑-및-gap-분석-항목) 참조

**주요 태스크 카테고리:**
- 요구사항-코드 기능 매핑
- 화면-API 영향도 매트릭스
- 유저 시나리오 완전성 Gap 분석
- 품질 인프라 Gap 분석
- 보안 Gap 분석
- 코드 품질 위험도 종합 매트릭스
- 데이터 정합성 위험 시나리오 검증
- STB 리소스 Gap 분석
- 용어 사전 최종 검증

**산출물:** → [extraction-checklist.md — Phase 3 산출물](extraction-checklist.md#phase-3-산출물) 참조

---

## Phase 4: 문서 체계화 및 유지보수 파이프라인 구축 (2주)

**목표:** 산출물을 지속 가능한 형태로 정리하고, Stakeholder가 업데이트할 수 있는 파이프라인을 구축

### 4-1. 기준문서 저장소 구축

**태스크:**
- Phase 0에서 결정한 저장소에 `blueprint-docs` repo 생성
- 폴더 구조 셋업:
  ```
  blueprint-docs/
  ├── services/{서비스명}/
  │   ├── extraction/     ← Phase 1 결과
  │   ├── views/          ← Phase 2 View
  │   └── gap-analysis/   ← Phase 3 Gap
  ├── integrated/          ← 서비스 횡단 통합 문서
  ├── glossary/            ← 용어 사전
  └── mkdocs.yml           ← MkDocs 설정 (개발자 View용)
  ```
- 브랜치 전략 정의:
  - `main` — 승인된 최신 문서 (Protected)
  - `draft/{서비스명}` — 서비스별 작업 브랜치
  - PR 리뷰 필수: 해당 Stakeholder 최소 1명 승인

### 4-2. View 플랫폼 배포

**태스크:**

**(A) 개발자 View — MkDocs + Pages**
- `mkdocs.yml` 설정 (Material 테마, Mermaid 플러그인, 검색)
- CI/CD 파이프라인: `main` 브랜치 push → MkDocs 빌드 → Pages 자동 배포
- 서비스별/View별 네비게이션 구성

**(B) 비개발자 View — Confluence 자동 동기화**
- Confluence Space 구조 설계:
  ```
  설계도 역추적 프로젝트/
  ├── 서비스별 설계도/
  │   ├── {서비스명}/
  │   │   ├── 코드 구조
  │   │   ├── 화면 카탈로그
  │   │   ├── API 명세
  │   │   └── ...
  ├── 통합 View/
  │   ├── PO View (비즈니스/화면/인터랙션)
  │   ├── UX View (화면 흐름/접근성)
  │   ├── 보안 View
  │   ├── 품질 View
  │   └── 시스템 아키텍처
  ├── Gap 분석/
  └── 용어 사전
  ```
- Markdown → Confluence 변환 파이프라인 구축 (mark-cli 또는 Confluence REST API)
- 동기화 트리거: Git `main` 브랜치 merge 시 자동 실행

### 4-3. Stakeholder 업데이트 파이프라인

**목표:** 각 Stakeholder가 자신의 역할에 맞는 방식으로 문서를 업데이트하고, 변경이 전체 시스템에 반영되는 흐름을 구축

**파이프라인 흐름:**

```
┌─────────────────────────────────────────────────────────────────┐
│                    문서 업데이트 파이프라인                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [개발자/PM/품질/보안]          [PO/UX센터]                       │
│       │                            │                            │
│  Git에서 Markdown 직접 편집    Confluence에서 직접 편집            │
│       │                            │                            │
│  PR 제출                      Confluence → Git 역동기화          │
│       │                       (Webhook + 변환 스크립트)           │
│       ▼                            │                            │
│  PR 리뷰 (해당 Stakeholder)        ▼                            │
│       │                       자동 PR 생성                       │
│       ▼                            │                            │
│  main 브랜치 merge ◄───────────────┘                            │
│       │                                                         │
│       ▼                                                         │
│  CI/CD 파이프라인 자동 실행                                       │
│       │                                                         │
│       ├──► MkDocs 빌드 → Pages 배포 (개발자 View)                │
│       ├──► Confluence 동기화 (비개발자 View)                      │
│       ├──► Mermaid 다이어그램 렌더링 검증                         │
│       └──► 변경 알림 (Slack/Teams → 관련 Stakeholder)            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**Stakeholder별 업데이트 방식:**

| Stakeholder | 편집 방식 | 리뷰어 | 반영 경로 |
|---|---|---|---|
| 개발자 | Git PR (Markdown 직접 편집) | PM | PR merge → 자동 배포 |
| PM | Git PR | PO 또는 개발 리드 | PR merge → 자동 배포 |
| PO | Confluence 직접 편집 | PM (역동기화 PR 리뷰) | Confluence → Git PR → merge |
| UX센터 | Confluence 직접 편집 (UI/UX View) | PO | Confluence → Git PR → merge |
| 품질센터 | Git PR (품질 View) | PM | PR merge → 자동 배포 |
| 보안센터 | Git PR (보안 View) | PM + 보안 리드 | PR merge → 자동 배포 |

### 4-4. 자동 갱신 파이프라인 (코드 변경 연동)

**목표:** 소스코드 변경 시 설계도의 일부 항목을 자동으로 재추출·갱신

**자동 갱신 대상 (코드 변경 트리거):**

| 트리거 | 자동 갱신 항목 | 도구 |
|---|---|---|
| API Controller 변경 | API 엔드포인트 목록 재추출 | Swagger/OpenAPI Generator |
| ORM Entity 변경 | ERD 재생성 | SchemaSpy, KIRO 재실행 |
| package.json / pom.xml 변경 | 의존성 목록 갱신, CVE 재스캔 | Snyk, OWASP DC |
| 라우트 설정 변경 | 화면 목록 재추출 | KIRO 재실행 |
| 테스트 파일 변경 | 테스트-기능 추적 매트릭스 갱신 | KIRO 재실행 |

**파이프라인 구성:**
```
소스코드 repo (Bitbucket DC)
    │
    ▼ (Webhook: 특정 파일 패턴 변경 감지)
    │
CI/CD Job 실행
    │
    ├──► 변경된 항목만 재추출 (KIRO 또는 도구)
    ├──► blueprint-docs repo에 자동 PR 생성
    └──► Slack 알림: "API 명세가 변경되었습니다. 리뷰 필요"
```

### 4-5. 기타 태스크

- CR(변경 요청) 워크플로우 시스템 정착
- Stakeholder별 대시보드 구성 (Confluence 대시보드 또는 Backstage)
- 문서 유지보수 가이드라인 및 R&R 정의
- 최종 리뷰 및 Stakeholder 핸드오프

**Phase 4 산출물:**
- `blueprint-docs` 저장소 (구조 완성, 브랜치 전략 적용)
- MkDocs 사이트 (개발자 View) — 자동 배포 파이프라인 포함
- Confluence Space (비개발자 View) — 자동 동기화 파이프라인 포함
- Stakeholder 업데이트 파이프라인 (Git ↔ Confluence 양방향)
- 코드 변경 연동 자동 갱신 파이프라인
- 문서 유지보수 가이드
- 변경 알림 설정 (Slack/Teams)

---

## 전체 타임라인 (약 18주)

```
Phase 0  ████░░░░░░░░░░░░░░  준비           (2주)  Week 1-2
Phase 1  ░░░░████████░░░░░░  기초 분석       (4주)  Week 3-6
Phase 2  ░░░░░░░░████████████  View 생성     (6주)  Week 7-12
Phase 3  ░░░░░░░░░░░░░░████████  Gap 분석    (4주)  Week 13-16
Phase 4  ░░░░░░░░░░░░░░░░░░████  체계화      (2주)  Week 17-18
```

---

## 리스크 및 대응

| 리스크 | 영향 | 대응 |
|---|---|---|
| 소스코드 없는 서버가 많음 | 분석 범위 축소 | 인프라 설정/네트워크 기반 추정 + 담당자 인터뷰 |
| 레거시 코드 (문서 전무) | 분석 시간 증가 | 자동화 도구 우선 적용, 수동 분석은 핵심 서비스에 집중 |
| Stakeholder 협조 부족 | 검증 지연 | 킥오프에서 R&R 명확화, 정기 리뷰 사이클 운영 |
| 외주 개발 코드 품질 편차 | 분석 난이도 상승 | 서비스별 난이도 등급 분류 후 우선순위 조정 |
| Bitbucket DC 접근/성능 이슈 | 스캔 지연 | 미러링 또는 로컬 클론 배치 처리 |
| PO/PM 간 용어 불일치 | 커뮤니케이션 오류 | 용어 사전 조기 수립 및 정기 갱신 |
| 화면 ID 채번 기존 체계와 충돌 | 이중 관리 | 기존 체계 조사 후 통합 또는 매핑 테이블 운영 |
| 외부 연동 시스템 문서 부재 | 분석 불가 영역 발생 | 담당 조직 인터뷰 + 네트워크 트래픽 캡처로 보완 |
| 저사양 STB 기종 다양성 | 기종별 분석 공수 증가 | 주력 기종 우선 분석, 기종별 분기 조건 코드 기반으로 차이점 집중 식별 |
| STB 소스코드 접근 제한 (펌웨어 등) | 리소스 관리 분석 불가 | 런타임 로그/모니터링 데이터 + 담당 개발자 인터뷰로 보완 |
| 개인정보 처리 현황 파악 불완전 | 법적 리스크 | 개인정보보호 담당자 협업 + 코드 스캔 병행 |
| 보안 설정 운영 환경 차이 | 분석 결과 신뢰도 저하 | Dev/Staging/Prod 환경별 보안 설정 비교 검증 |
| Confluence ↔ Git 양방향 동기화 충돌 | 문서 버전 불일치 | Git을 Single Source of Truth로 확정, Confluence는 View 전용 원칙 (역동기화는 PR 리뷰 필수) |
| 비개발자의 Git 접근 거부감 | 업데이트 참여 저조 | Confluence 편집 → 자동 PR 생성 파이프라인으로 Git 직접 접근 불필요하게 설계 |
| 자동 갱신 파이프라인 오탐 | 불필요한 PR 과다 생성 | 변경 감지 파일 패턴을 정밀하게 설정, 자동 PR에 `auto-generated` 라벨 부여 |

---

## 권장 도구 스택

| 용도 | 도구 |
|---|---|
| 아키텍처 다이어그램 | Structurizr (C4), PlantUML, Mermaid |
| 정적 분석 | SonarQube, PMD, ESLint |
| 의존성 분석 | Dependency-Track, jdeps, madge(JS) |
| 테스트 커버리지 | JaCoCo(Java), Istanbul/nyc(JS), Coverage.py |
| 코드 변경 분석 | git-churn, Code Climate, CodeScene |
| 로깅/모니터링 분석 | OpenTelemetry, Zipkin, Jaeger |
| 성능 분석 | JProfiler, async-profiler, Lighthouse |
| API 문서화 | Swagger/OpenAPI, Spring REST Docs |
| ERD 생성 | SchemaSpy, DBeaver |
| 보안 분석 | OWASP Dependency-Check, Snyk, gitleaks, truffleHog |
| 보안 헤더/하드닝 | Mozilla Observatory, SecurityHeaders.com |
| 개인정보 스캔 | 자체 정규식 스캔, DLP 도구 |
| 문서 포털 | Confluence, Backstage, GitBook |
| 문서 사이트 빌드 | MkDocs (Material 테마), Docusaurus |
| Markdown→Confluence 동기화 | mark-cli, Confluence REST API |
| 다이어그램 렌더링 | Mermaid CLI (mmdc), Kroki |
| 변경 알림 | Slack Webhook, Microsoft Teams Connector |
| CI/CD 연동 | Jenkins, GitLab CI (Bitbucket Pipeline) |
