# 설계도 파묘 — 전체 산출물 생성 체크리스트

> 각 Step 실행 후 산출물이 빠짐없이 생성되었는지 확인하는 체크리스트입니다.
> 서비스별로 복사하여 사용하세요. 산출물 경로: `services/{서비스명}/docs/`

## 사용법

1. Step 실행 완료 후 해당 섹션의 체크박스를 확인
2. `[ ]` → `[x]`로 변경하여 생성 완료 표기
3. 누락된 파일이 있으면 해당 Step을 재실행하거나 추가 프롬프트로 보완
4. 모든 Step 완료 후 "전체 완전성 검증" 섹션에서 최종 확인

---

## Step 01: 코드 구조 스캔 (8개)

경로: `extraction/01-code-structure/`

| # | 산출물 파일 | 생성 여부 | 비고 |
|---|---|---|---|
| 1 | `tech-profile.md` | `[ ]` | 기술 프로파일 시트 |
| 2 | `directory-structure.md` | `[ ]` | 디렉토리 구조 및 아키텍처 패턴 |
| 3 | `dependencies.md` | `[ ]` | 의존성 목록 (내부/외부) |
| 4 | `config-profiles.md` | `[ ]` | 설정 프로파일 비교표 |
| 5 | `platform-branching.md` | `[ ]` | 플랫폼 분기 코드 인벤토리 |
| 6 | `tech-debt-markers.md` | `[ ]` | 기술 부채 마커 목록 |
| 7 | `iac-inventory.md` | `[ ]` | IaC 인벤토리 |
| 8 | `cicd-pipeline.md` | `[ ]` | CI/CD 파이프라인 분석서 |

## Step 1b: Dead Code 분석 (5개)

경로: `extraction/01b-dead-code/`

| # | 산출물 파일 | 생성 여부 | 비고 |
|---|---|---|---|
| 1 | `dead-code-report.md` | `[ ]` | 미사용 클래스/메서드 목록 |
| 2 | `unused-db-resources.md` | `[ ]` | 미참조 테이블/컬럼 |
| 3 | `unused-api-endpoints.md` | `[ ]` | 미호출 API 엔드포인트 |
| 4 | `unused-config-resources.md` | `[ ]` | 미참조 설정/리소스/Bean |
| 5 | `code-age-analysis.md` | `[ ]` | 코드 연대 분석 |

## Step 1c: 공통 모듈 그룹핑 (4개)

경로: `extraction/01c-common-modules/`

| # | 산출물 파일 | 생성 여부 | 비고 |
|---|---|---|---|
| 1 | `internal-common-modules.md` | `[ ]` | 내부 공통 패키지 인벤토리 |
| 2 | `external-common-libraries.md` | `[ ]` | 사내 공통 라이브러리 인벤토리 |
| 3 | `dependency-direction.md` | `[ ]` | 의존 방향 다이어그램 |
| 4 | `impact-analysis.md` | `[ ]` | 변경 영향도 분석 Top 10 |

## Step 02: 화면 인벤토리 (3개)

경로: `extraction/02-screens/`

| # | 산출물 파일 | 생성 여부 | 비고 |
|---|---|---|---|
| 1 | `screen-inventory.md` | `[ ]` | 화면 목록 (ID, 명칭, 플랫폼) |
| 2 | `screen-name-normalization.md` | `[ ]` | 화면명 정규화 매핑 |
| 3 | `cross-platform-screen-map.md` | `[ ]` | 크로스 플랫폼 화면 매핑표 |

## Step 03: API & 데이터 (8개)

경로: `extraction/03-api-data/`

| # | 산출물 파일 | 생성 여부 | 비고 |
|---|---|---|---|
| 1 | `api-endpoints.md` | `[ ]` | API 엔드포인트 목록 |
| 2 | `api-schema-detail.md` | `[ ]` | 요청/응답 스키마 상세 |
| 3 | `api-versioning.md` | `[ ]` | API 버전 관리 현황 |
| 4 | `internal-api-contracts.md` | `[ ]` | 서비스 간 내부 API 계약 |
| 5 | `erd.md` | `[ ]` | ERD 초안 |
| 6 | `db-query-patterns.md` | `[ ]` | DB 쿼리 패턴 상세 |
| 7 | `db-migration-history.md` | `[ ]` | DB 마이그레이션 이력 |
| 8 | `service-communication-matrix.md` | `[ ]` | 서비스 간 통신 매트릭스 |

## Step 04: 비즈니스 로직 (10개)

경로: `extraction/04-business-logic/`

| # | 산출물 파일 | 생성 여부 | Part | 비고 |
|---|---|---|---|---|
| 1 | `business-rules.md` | `[ ]` | Part 1 | 비즈니스 규칙 목록 |
| 2 | `error-handling.md` | `[ ]` | Part 1 | 에러 코드/예외 처리 |
| 3 | `state-machines.md` | `[ ]` | Part 1 | 상태 전이 다이어그램 |
| 4 | `event-flows.md` | `[ ]` | Part 2 | 이벤트/메시지 흐름 |
| 5 | `batch-scheduler-inventory.md` | `[ ]` | Part 2 | 배치/스케줄러 인벤토리 |
| 6 | `call-flow-chains.md` | `[ ]` | Part 2 | 서비스 간 Call Flow |
| 7 | `e2e-call-flows.md` | `[ ]` | Part 2 | E2E Call Flow 원시 데이터 |
| 8 | `use-case-tree.md` | `[ ]` | Part 3 | Use Case Tree 계층 구조 |
| 9 | `use-cases.md` | `[ ]` | Part 3 | Use Case 카탈로그 |
| 10 | `use-case-scenarios.md` | `[ ]` | Part 3 | Use Case 시나리오 상세 |

## Step 05: 보안 (11개)

경로: `extraction/05-security/`

| # | 산출물 파일 | 생성 여부 | Part | 비고 |
|---|---|---|---|---|
| 1 | `credential-management.md` | `[ ]` | Part 1 | 크리덴셜 관리 현황 |
| 2 | `pii-privacy-map.md` | `[ ]` | Part 1 | 개인정보 처리 현황 |
| 3 | `session-token-management.md` | `[ ]` | Part 1 | 세션/토큰 관리 |
| 4 | `input-security-validation.md` | `[ ]` | Part 2 | 입력값 보안 검증 |
| 5 | `api-security-posture.md` | `[ ]` | Part 2 | API 보안 현황 |
| 6 | `audit-log-status.md` | `[ ]` | Part 2 | 감사 로그 현황 |
| 7 | `encryption-inventory.md` | `[ ]` | Part 2 | 데이터 암호화 인벤토리 |
| 8 | `security-hardening.md` | `[ ]` | Part 3 | 보안 하드닝 체크리스트 |
| 9 | `third-party-scripts.md` | `[ ]` | Part 3 | 서드파티 스크립트 |
| 10 | `app-shielding.md` | `[ ]` | Part 3 | 모바일 앱 쉴딩 현황 |
| 11 | `security-layer-compliance.md` | `[ ]` | Part 3 | Security Layer 217항목 대비 |

## Step 06: 품질 (8개)

경로: `extraction/06-quality/`

| # | 산출물 파일 | 생성 여부 | 비고 |
|---|---|---|---|
| 1 | `test-automation-inventory.md` | `[ ]` | 테스트 자동화 현황 |
| 2 | `test-feature-traceability.md` | `[ ]` | 테스트-기능 추적 매핑 |
| 3 | `change-frequency.md` | `[ ]` | 코드 변경 빈도 리포트 |
| 4 | `logging-observability.md` | `[ ]` | 로깅/모니터링 현황 |
| 5 | `dependency-health.md` | `[ ]` | 의존성 건강도 리포트 |
| 6 | `concurrency-patterns.md` | `[ ]` | 동시성/스레드 안전성 |
| 7 | `data-integrity.md` | `[ ]` | 데이터 정합성 검증 |
| 8 | `performance-hotspots.md` | `[ ]` | 성능 병목 후보 영역 |

## Step 07: UX (6개)

경로: `extraction/07-ux/`

| # | 산출물 파일 | 생성 여부 | 비고 |
|---|---|---|---|
| 1 | `interaction-patterns.md` | `[ ]` | 유저 인터랙션 패턴 |
| 2 | `validation-rules.md` | `[ ]` | 입력 유효성 검증 규칙 |
| 3 | `loading-ux-patterns.md` | `[ ]` | 로딩/대기 상태 UX |
| 4 | `i18n-accessibility.md` | `[ ]` | 다국어/접근성 현황 |
| 5 | `entry-points.md` | `[ ]` | 딥링크/푸시 진입 경로 |
| 6 | `feature-flags.md` | `[ ]` | 피처 플래그 현황 |

## Step 08: 인프라 & 외부 연동 (5개)

경로: `extraction/08-infra/`

| # | 산출물 파일 | 생성 여부 | 비고 |
|---|---|---|---|
| 1 | `external-systems.md` | `[ ]` | 외부 연동 시스템 인벤토리 |
| 2 | `deployment-topology.md` | `[ ]` | 배포 토폴로지 |
| 3 | `stb-resources.md` | `[ ]` | STB 리소스 관리 현황 |
| 4 | `nfr-baseline.md` | `[ ]` | 비기능 현황 베이스라인 |
| 5 | `runtime-data.md` | `[ ]` | 런타임 데이터 (수작업) |

## Step 09: 아키텍처 View (14개 폴더)

경로: `views/`

| # | 산출물 폴더 | 생성 여부 | 그룹 | 비고 |
|---|---|---|---|---|
| 1 | `system-context/` | `[ ]` | A | 2-1 C4 Level 1-2 |
| 2 | `component-code/` | `[ ]` | A | 2-2 C4 Level 3-4 |
| 3 | `business-rules/` | `[ ]` | A | 2-3 비즈니스 규칙/UC View |
| 4 | `event-flow/` | `[ ]` | A | 2-4, 2-4b 이벤트/E2E Call Flow |
| 5 | `data/` | `[ ]` | B | 2-5 ERD, DFD |
| 6 | `ui-ux/` | `[ ]` | B | 2-6 화면 카탈로그/흐름도 |
| 7 | `security/` | `[ ]` | B | 2-7 보안 View |
| 8 | `quality/` | `[ ]` | B | 2-8 품질 View |
| 9 | `stb-resources/` | `[ ]` | C | 2-9 STB 리소스 (해당 시) |
| 10 | `resilience-dr/` | `[ ]` | C | 2-10 장애 복원력/DR |
| 11 | `ai-governance/` | `[ ]` | C | 2-11 AI 거버넌스 (해당 시) |
| 12 | `layer-stack/` | `[ ]` | D | 2-12 Layer Stack |
| 13 | `function-stack-callflow/` | `[ ]` | D | 2-13 Function-External Stack |
| 14 | `stakeholder-summary/` | `[ ]` | D | 2-14 PM 의사결정 지원 |

## Step 10: Gap 분석 (21개)

경로: `gap-analysis/`

| # | 산출물 파일 | 생성 여부 | Part | 비고 |
|---|---|---|---|---|
| 1 | `traceability-matrix.md` | `[ ]` | Part 1 | 요구사항-코드 추적 매트릭스 |
| 2 | `screen-api-impact.md` | `[ ]` | Part 1 | 화면-API 영향도 매트릭스 |
| 3 | `dead-code-unused.md` | `[ ]` | Part 1 | Dead Code/미사용 API 리포트 |
| 4 | `orphan-screens.md` | `[ ]` | Part 1 | 미사용 화면/Orphan Screen |
| 5 | `user-scenario-gap.md` | `[ ]` | Part 2 | 유저 시나리오 완전성 Gap |
| 6 | `cross-platform-gap.md` | `[ ]` | Part 2 | 크로스 플랫폼 불일치 Gap |
| 7 | `quality-infra-gap.md` | `[ ]` | Part 2 | 품질 인프라 Gap |
| 8 | `security-gap.md` | `[ ]` | Part 2 | 보안 Gap |
| 9 | `code-quality-risk-matrix.md` | `[ ]` | Part 3 | 코드 품질 위험도 매트릭스 |
| 10 | `data-integrity-risk.md` | `[ ]` | Part 3 | 데이터 정합성 위험 |
| 11 | `stb-resource-gap.md` | `[ ]` | Part 3 | STB 리소스 Gap |
| 12 | `hidden-dependencies.md` | `[ ]` | Part 3 | 숨겨진 의존성 |
| 13 | `spof-gap.md` | `[ ]` | Part 4 | SPOF 미해소 Gap |
| 14 | `message-stability-gap.md` | `[ ]` | Part 4 | 메시지 안정성 Gap |
| 15 | `ai-governance-gap.md` | `[ ]` | Part 4 | AI 거버넌스 Gap |
| 16 | `cross-service-inconsistency.md` | `[ ]` | Part 4 | 서비스 간 교차 검증 |
| 17 | `service-rationalization.md` | `[ ]` | Part 4 | 서비스 합리화 판단 |
| 18 | `e2e-callflow-gap.md` | `[ ]` | Part 5 | E2E Call Flow 완전성 Gap |
| 19 | `pm-decision-gap.md` | `[ ]` | Part 5 | PM 의사결정 지원 Gap |
| 20 | `glossary-final.md` | `[ ]` | Part 5 | 용어 사전 확정본 |
| 21 | `improvement-recommendations.md` | `[ ]` | Part 5 | 개선 권고사항 종합 |

---

## 공통 산출물 (Step별 자동 생성)

| # | 산출물 파일 | 위치 | 생성 여부 | 비고 |
|---|---|---|---|---|
| 1 | `_context-notes.md` | `extraction/` | `[ ]` | 컨텍스트 전달 노트 |
| 2 | `reverse-lookup-index.md` | `extraction/` | `[ ]` | 역방향 조회 인덱스 |

---

## 전체 완전성 검증

### 산출물 수량 요약

| Phase | Step | 산출물 수 | 생성 완료 | 누락 |
|---|---|---|---|---|
| Phase 1 | Step 01 | 8 | /8 | |
| Phase 1 | Step 1b | 5 | /5 | |
| Phase 1 | Step 1c | 4 | /4 | |
| Phase 1 | Step 02 | 3 | /3 | |
| Phase 1 | Step 03 | 8 | /8 | |
| Phase 1 | Step 04 | 10 | /10 | |
| Phase 1 | Step 05 | 11 | /11 | |
| Phase 1 | Step 06 | 8 | /8 | |
| Phase 1 | Step 07 | 6 | /6 | |
| Phase 1 | Step 08 | 5 | /5 | |
| Phase 2 | Step 09 | 14 폴더 | /14 | |
| Phase 3 | Step 10 | 21 | /21 | |
| 공통 | — | 2 | /2 | |
| **합계** | | **105** | | |

### 교차 검증 체크

- [ ] Step 간 교차 검증 로그가 각 산출물 하단에 포함됨
- [ ] 누락 검증 로그가 ❌ 판정 항목에 포함됨
- [ ] 역동기화 로그가 Phase 2/3 보정 항목에 포함됨
- [ ] 소스 로케이터가 모든 테이블에 포함됨
- [ ] 확인 상태(✅/⚠️/❌/🔍)가 모든 항목에 표기됨
