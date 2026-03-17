# Reversed Blueprint — 서비스 설계도 파묘 프로젝트

운영 중인 서비스의 소스코드로부터 Stakeholder 관점별 설계 문서(Architecture Views)를 역추적·생성하는 프로젝트입니다.

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
├── .kiro/steering/        ← KIRO steering 문서 (10단계 순차 작업 가이드)
├── project/               ← 프로젝트 관리 문서
│   ├── workplan-roadmap.md            로드맵 (Phase 0~4, 18주)
│   ├── extraction-checklist.md        추출 항목 체크리스트
│   ├── service-inventory.md           서비스 인벤토리 (작업 관리용)
│   ├── platform-service-inventory.md  플랫폼 서비스 인벤토리 (참조용)
│   ├── security-layer-checklist.md    보안 체크리스트 (217항목)
│   ├── cdr-design-checklist.md        CDR 설계 체크리스트 (CTO품질PMO팀 가이드 매핑)
│   └── manual-tasks.md                수작업 항목 목록 (런타임 데이터, 인터뷰, 교차 검증)
├── services/              ← 서비스별 소스코드 + 설계도
│   ├── {서비스명}/src/          Bitbucket clone 소스코드
│   ├── {서비스명}/docs/         KIRO 추출 설계도
│   └── _template/               새 서비스 추가 시 복사용 템플릿
├── docs-integrated/       ← 전체 서비스 통합 설계도 (문서화용)
├── glossary.md            ← 용어 사전
```

## 작업 방법 (KIRO 기반)

1. `project/service-inventory.md`에 대상 서비스 등록
2. 소스코드를 `services/{서비스명}/src/`에 clone
3. KIRO 채팅에서 steering 문서를 순서대로 실행:
   - `#00-workspace-setup` → 환경 구성
   - `#01-code-structure-scan` ~ `#08-infra-and-integration` → Phase 1 추출
   - `#09-architecture-views` → Phase 2 View 생성
   - `#10-gap-analysis` → Phase 3 Gap 분석
4. 서비스별 완료 후 `docs-integrated/`에 통합

## Phase 구성

| Phase | 기간 | 내용 |
|---|---|---|
| Phase 0 | 2주 | 준비 (인벤토리, 용어 사전, 문서 인프라 결정) |
| Phase 1 | 4주 | 소스코드 기초 분석 (10개 카테고리) |
| Phase 2 | 6주 | 아키텍처 View 생성 (11개 View) |
| Phase 3 | 4주 | 추적성 매핑 및 Gap 분석 |
| Phase 4 | 2주 | 문서 체계화 및 파이프라인 구축 |

## 상세 문서

- [작업자용 매뉴얼](project/operator-manual.md) — KIRO 설계도 추출 작업 실행 가이드 (신규 작업자 필독)
- [Workplan Roadmap](project/workplan-roadmap.md)
- [Extraction Checklist](project/extraction-checklist.md)
- [Service Inventory](project/service-inventory.md) — 작업 관리용 (폴더명, Repo URL, 진행 현황)
- [Platform Service Inventory](project/platform-service-inventory.md) — 플랫폼 관점 참조용 (앱/단말/서버)
- [Security Layer Checklist](project/security-layer-checklist.md) — 보안기술팀 설계/구현 체크리스트 (217항목)
- [CDR Design Checklist](project/cdr-design-checklist.md) — CTO품질PMO팀 CDR 가이드 설계 반영 매핑
- [Manual Tasks](project/manual-tasks.md) — 작업자 수작업 항목 목록 (런타임 데이터, 인터뷰, 교차 검증 등)
- [Glossary](glossary.md)
