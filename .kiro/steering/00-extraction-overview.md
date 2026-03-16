---
inclusion: manual
---

# 설계도 파묘 프로젝트 — Steering 개요

이 프로젝트는 소스코드를 분석하여 Stakeholder별 설계도를 역추적·추출하는 작업입니다.

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
│   └── security-layer-checklist.md
├── docs-integrated/              ← 전체 서비스 통합 설계도 (복붙 문서화용)
```

## 작업 순서

아래 steering 문서를 순서대로, 서비스 단위로 실행하세요.
채팅에서 대상 서비스를 지정: `#01-code-structure-scan 서비스: {서비스명}`

| 순서 | Steering 문서 | 작업 내용 | 산출물 위치 (서비스별) |
|---|---|---|---|
| 0 | `00-workspace-setup` | 폴더 구조 구성, 서비스 인벤토리, 소스 clone | `service-inventory.md` |
| 1 | `01-code-structure-scan` | Repository 스캔, 기술 스택 식별, 의존성 추출 | `services/{서비스명}/docs/extraction/01-code-structure/` |
| 2 | `02-screen-inventory` | 화면 목록 추출, 화면명 정규화, 화면 ID 부여 | `services/{서비스명}/docs/extraction/02-screens/` |
| 3 | `03-api-and-data` | API 엔드포인트 추출, DB 스키마 역추적, ERD | `services/{서비스명}/docs/extraction/03-api-data/` |
| 4 | `04-business-logic` | 비즈니스 규칙, 에러 처리, 상태 전이, 이벤트 흐름 | `services/{서비스명}/docs/extraction/04-business-logic/` |
| 5 | `05-security-extraction` | 크리덴셜, 개인정보, 세션/토큰, 입력 보안, API 보안, 감사 로그, 암호화, 하드닝 | `services/{서비스명}/docs/extraction/05-security/` |
| 6 | `06-quality-extraction` | 테스트 현황, 변경 빈도, 로깅, 의존성 건강도, 동시성, 정합성, 성능 | `services/{서비스명}/docs/extraction/06-quality/` |
| 7 | `07-ux-extraction` | 인터랙션 패턴, 유효성 검증, 로딩 UX, 다국어/접근성, 딥링크, 피처 플래그 | `services/{서비스명}/docs/extraction/07-ux/` |
| 8 | `08-infra-and-integration` | 외부 연동, 배포 토폴로지, STB 리소스, 비기능 현황 | `services/{서비스명}/docs/extraction/08-infra/` |
| 9 | `09-architecture-views` | Phase 1 결과 기반 C4 다이어그램, Stakeholder별 View 문서 생성 | `services/{서비스명}/docs/views/` |
| 10 | `10-gap-analysis` | 추적성 매핑, Gap 분석, 위험도 매트릭스, 용어 사전 확정 | `services/{서비스명}/docs/gap-analysis/` |
| — | 통합 | 모든 서비스 완료 후, 서비스별 결과를 `docs-integrated/`에 통합 | `docs-integrated/` |

## 공통 규칙

0. **서비스 ID 확인 필수 (작업 시작 전 반드시 수행)**
   - 사용자가 서비스명을 지정하면, 반드시 `project/service-inventory.md`를 열어 해당 서비스의 정보를 확인하라
   - 확인 항목: 서비스 ID, 폴더명, 소스 유무, Bitbucket Repo URL
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

2. **확인 상태 표기**: 모든 항목에 아래 상태를 반드시 표기
   - ✅ 확인완료 — 코드/설정에서 확인됨
   - ⚠️ 일부확인 — 부분적으로만 확인됨 (범위 명시)
   - ❌ 미확인 — 소스 없음, 접근 불가
   - 🔍 인터뷰필요 — 코드만으로 확인 불가

3. **화면명 정규화**: 화면 관련 산출물에서는 반드시 정규화된 대표 명칭 사용

4. **산출물 포맷**: 각 산출물은 Markdown 테이블 또는 Mermaid 다이어그램으로 작성

5. **참조 문서**: #[[file:project/extraction-checklist.md]] 의 상세 항목을 기준으로 작업

6. **통합 문서**: 모든 서비스 분석 완료 후, `docs-integrated/`에 서비스별 섹션으로 구분된 통합본 생성 (Confluence 복붙용)

7. **산출물 버전 백업**: 설계도 파일을 덮어쓰기 전에 반드시 이전 버전을 백업
   - 백업 위치: 해당 파일과 같은 폴더 내 `_backup/` 하위
   - 백업 파일명: `{원본파일명}_v{버전}_{yyyyMMdd_HHmm}.md`
     - 예: `api-endpoints.md` → `_backup/api-endpoints_v1_20260316_1430.md`
   - 버전 번호: 해당 파일의 `_backup/` 내 동일 원본명 파일 수 + 1
   - 최초 생성 시에는 백업 불필요 (파일이 존재하지 않으므로)
   - `_backup/` 폴더는 통합 문서(`docs-integrated/`) 생성 시 제외
