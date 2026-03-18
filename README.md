# Reversed Blueprint — 서비스 설계도 파묘 프로젝트

운영 중인 서비스의 소스코드로부터 Stakeholder 관점별 설계 문서(Architecture Views)를 역추적·생성하는 프로젝트입니다.

## Executive Summary

### 이 프로젝트가 해결하는 문제

운영 중인 30개 서비스의 설계 문서가 부재하거나 코드와 불일치하여,
변경 영향도 파악, 보안 점검, 품질 평가, 신규 인력 온보딩에 과도한 시간과 비용이 소요됩니다.

### AI가 자동으로 하는 일

| 구분 | 내용 |
|---|---|
| 소스코드 → 설계도 역추출 | 코드를 13단계로 분석하여 기술 구조, API, 화면, 보안, 품질 등 설계도를 자동 생성 |
| 미사용 코드·자원 식별 | 실제로 쓰이지 않는 코드, DB, API를 자동 탐지하여 기술 부채 현황 파악 |
| 공통 모듈 영향도 분석 | 공유 모듈 변경 시 영향받는 서비스 범위를 자동 산출 |
| SW 기술 계층 시각화 | 클라이언트~인프라까지 7개 계층을 한눈에 파악 가능한 다이어그램 생성 |
| 보안 Gap 자동 점검 | 보안센터 217항목 + 개인정보보호법 대비 미충족 영역 자동 식별 |
| 품질 위험도 매트릭스 | 변경 빈도 × 테스트 커버리지 × 복잡도 교차 분석으로 고위험 영역 식별 |

### AI만으로 완결되지 않는 영역

소스코드 분석만으로는 설계도의 완전성을 100% 확보할 수 없습니다.
아래 영역은 사람 또는 외부 도구를 통해 보완해야 하며, AI가 보완이 필요한 항목을 자동으로 식별·표기합니다.

| 보완 방식 | 내용 | 예시 |
|---|---|---|
| AI + 외부 도구 병행 | AI가 코드 패턴을 식별하고, 정밀 수치는 외부 도구로 확보 | 테스트 커버리지(JaCoCo), 취약점 스캔(Snyk), 코드 복잡도(SonarQube) |
| AI 추출 + 담당자 확정 | AI가 후보를 제시하고, PO/UX/개발 리드가 최종 확정 | 화면명 정규화, Use Case 시나리오 검증, Dead Code 최종 판정 |
| 담당자 인터뷰 필수 | 코드에서 추출 불가, 운영 환경·정책·외부 시스템 정보 필요 | 런타임 트래픽 데이터, 외부 연동 SLA, 개인정보 법적 적정성, 인프라 실 구성 |

> 📌 각 산출물 항목에 `[KIRO]` `[KIRO+도구]` `[KIRO+확정]` `[인터뷰]` 태그가 표기되어 있어,
> 어떤 항목이 추가 조치가 필요한지 한눈에 파악할 수 있습니다.
> 상세 항목은 [수작업 항목 목록](project/manual-tasks.md)을 참조하세요.

### Stakeholder별 산출물

| 대상 | 받는 산출물 |
|---|---|
| PO (상품기획) | 화면-API 영향도 매트릭스, Use Case 카탈로그, 변경 요청 시 영향 범위 |
| PM (프로젝트관리) | C4 아키텍처 다이어그램, 비즈니스 규칙 카탈로그, 기술 부채 목록 |
| 개발자 | 컴포넌트 의존성, 상태 전이, API 계약, 코드 품질 리포트 |
| UX센터 | 화면 카탈로그, 인터랙션 패턴, 접근성 현황 |
| 품질센터 | 테스트 Gap, 성능 병목, 데이터 정합성 위험 시나리오 |
| 보안센터 | 크리덴셜 관리, 개인정보 처리, API 보안, 앱 쉴딩 현황 |

### 기대 효과

- **변경 영향도 파악 시간 단축**: 화면 수정 → 영향 API/DB 즉시 역추적
- **보안·품질 점검 자동화**: 수작업 점검 대비 커버리지 확대, 누락 최소화
- **신규 인력 온보딩 가속**: 설계도 기반 서비스 이해 → 기존 대비 학습 시간 단축
- **기술 부채 가시화**: Dead Code, 미사용 자원, 취약 의존성을 정량적으로 파악
- **분석 노하우 자동 축적**: 작업자 간 효과적인 분석 패턴이 자동 공유·개선 (FeedbackOps)

## 프로젝트 목표

- 소스코드 → 설계도 역추적 (Reverse Engineering)
- 요구사항 → 설계 → 구현 간 추적성(Traceability) 확보
- Stakeholder별 맞춤 View 제공 (PO, PM, 개발자, UX센터, 품질센터, 보안센터)

## 대상 플랫폼

- 모바일 앱: iOS, Android (크로스 플랫폼 화면 동기화 — 화면 ID 통합, 용어 정규화)
- STB 앱: AOSP / Android TV OS 기반, LGU+ 자체 론처
- 서버: Spring Boot, Node.js 등 (Swagger 미정비 레거시 포함)
- 디바이스 환경별 분기 코드(플랫폼 전용 코드, 네이티브 모듈, 기종별 분기)를 설계도에 포함

## 폴더 구조

```
├── .kiro/steering/        ← KIRO steering 문서 (13단계 순차 작업 가이드)
├── project/               ← 프로젝트 관리 문서
│   ├── workplan-roadmap.md            로드맵 (Phase 0~4, 18주)
│   ├── extraction-checklist.md        추출 항목 체크리스트
│   ├── service-inventory.md           서비스 인벤토리 (작업 관리용)
│   ├── platform-service-inventory.md  플랫폼 서비스 인벤토리 (참조용)
│   ├── security-layer-checklist.md    보안 체크리스트 (217항목)
│   ├── cdr-design-checklist.md        CDR 설계 체크리스트 (CTO품질PMO팀 가이드 매핑)
│   ├── manual-tasks.md                수작업 항목 목록 (런타임 데이터, 인터뷰, 교차 검증)
│   ├── prompt-cookbook.md             프롬프트 쿡북 (효과적인 추가 프롬프트 패턴 축적소)
│   └── ownership/                     코드 소유권 및 조직 매핑
│       ├── code-ownership-map.md          코드 영역 → 담당 조직 매핑 (215개 시스템)
│       ├── org-contact-directory.md       조직 → 현재 담당자/외주사 (갱신용)
│       └── glossary-business-tech.md      기획 용어 → 코드 구현체 매핑 사전
├── services/              ← 서비스별 소스코드 + 설계도
│   ├── {서비스명}/src/          Bitbucket clone 소스코드
│   ├── {서비스명}/docs/         KIRO 추출 설계도
│   └── _template/               새 서비스 추가 시 복사용 템플릿
├── docs-integrated/       ← 전체 서비스 통합 설계도 (문서화용)
├── glossary.md            ← 용어 사전
```

## 작업 방법 (KIRO 기반)

> KIRO에서 이 폴더를 열면, 자동 가이드 steering(`00-project-entry`)이 항상 로드됩니다.
> 작업자가 소스코드 경로나 서비스명을 알려주면 KIRO가 자동으로 절차를 안내합니다.
> 상세 실행 가이드: [작업자용 매뉴얼](project/operator-manual.md)

1. `project/service-inventory.md`에 대상 서비스 등록
2. 소스코드를 `services/{서비스명}/src/`에 clone
3. KIRO 채팅에서 steering 문서를 순서대로 실행:
   - `#00-workspace-setup` → 환경 구성
   - `#01-code-structure-scan` ~ `#08-infra-and-integration` → Phase 1 추출
     - Step 01 완료 후 `#01b-dead-code-analysis` → Dead Code/미사용 리소스 식별
     - Step 1b 완료 후 `#01c-common-module-grouping` → 공통 모듈 그룹핑/사내 라이브러리 식별
   - `#09-architecture-views` → Phase 2 View 생성
   - `#10-gap-analysis` → Phase 3 Gap 분석
4. 서비스별 완료 후 `docs-integrated/`에 통합

## Phase 구성

| Phase | 기간 | 내용 |
|---|---|---|
| Phase 0 | 2주 | 준비 (인벤토리, 용어 사전, 문서 인프라 결정) |
| Phase 1 | 4주 | 소스코드 기초 분석 (10개 카테고리) |
| Phase 2 | 6주 | 아키텍처 View 생성 (15개 View: 2-1~2-14 + 2-4b E2E 완전성 검증) |
| Phase 3 | 4주 | 추적성 매핑 및 Gap 분석 |
| Phase 4 | 2주 | 문서 체계화 및 파이프라인 구축 |

## 상세 문서

- [작업자용 매뉴얼](project/operator-manual.md) — KIRO 설계도 추출 작업 실행 가이드 (신규 작업자 필독)
- [자동 가이드 Steering](.kiro/steering/00-project-entry.md) — KIRO가 항상 자동 로드하는 진입점 (작업자 프롬프트 감지 → 자동 안내)
- [Workplan Roadmap](project/workplan-roadmap.md)
- [Extraction Checklist](project/extraction-checklist.md)
- [Service Inventory](project/service-inventory.md) — 작업 관리용 (폴더명, Repo URL, 진행 현황)
- [Platform Service Inventory](project/platform-service-inventory.md) — 플랫폼 관점 참조용 (앱/단말/서버)
- [Security Layer Checklist](project/security-layer-checklist.md) — 보안기술팀 설계/구현 체크리스트 (217항목)
- [CDR Design Checklist](project/cdr-design-checklist.md) — CTO품질PMO팀 CDR 가이드 설계 반영 매핑
- [Manual Tasks](project/manual-tasks.md) — 작업자 수작업 항목 목록 (런타임 데이터, 인터뷰, 교차 검증 등)
- [Prompt Cookbook](project/prompt-cookbook.md) — 효과적인 추가 프롬프트 패턴 축적소
- [Code Ownership Map](project/ownership/code-ownership-map.md) — 코드 영역 → 담당 조직 매핑 (215개 시스템, 8개 팀)
- [Org Contact Directory](project/ownership/org-contact-directory.md) — 조직 → 현재 담당자/외주사 연락처 (조직재편 시 갱신)
- [Business–Tech Glossary](project/ownership/glossary-business-tech.md) — 기획 용어 → 코드 구현체 매핑 사전
- [Glossary](glossary.md)
