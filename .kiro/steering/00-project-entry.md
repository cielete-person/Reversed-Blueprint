---
inclusion: auto
---

# 설계도 파묘 프로젝트 — 자동 가이드

> 이 steering은 KIRO가 항상 자동으로 읽는 진입점입니다.

## 프로젝트 개요

이 워크스페이스는 **"설계도 파묘"** 프로젝트입니다.
운영 중인 서비스의 소스코드로부터 Stakeholder 관점별 설계 문서를 역추적·추출합니다.

## KIRO의 역할

작업자가 서비스명이나 소스코드 경로를 알려주면, KIRO가 13단계 steering(01→1b→1c→02~10)에 따라 설계도를 추출합니다.

## 작업자 프롬프트 감지

| 패턴 | 동작 |
|---|---|
| 소스코드 경로 제공 | `#00-extraction-overview`의 소스 배치 절차 수행 |
| 서비스명 지정 | `project/service-inventory.md`에서 확인 후 승인 대기 |
| "뭐부터 하면 돼?" | `project/operator-manual.md` 안내 |
| `#XX-steering명 서비스: xxx` | 해당 steering 실행 |

## 필수 참조 문서

| 문서 | 용도 |
|---|---|
| `project/operator-manual.md` | 작업자용 실행 가이드 |
| `project/service-inventory.md` | 서비스 목록 (36개), 진행 현황 |
| `project/artifact-checklist.md` | 전체 산출물 생성 체크리스트 |
| `.kiro/steering/00-extraction-overview.md` | 공통 규칙 및 상세 가이드 |

## 핵심 규칙 (상세: `00-extraction-overview.md` 참조)

- 확인 상태 표기: ✅/⚠️/❌/🔍
- ❌ 판정 전 최소 6회 검색 검증 (2-Pass 스캔)
- Step 순서: 01→1b→1c→02~08→09(4그룹 분할)→10
- 산출물 덮어쓰기 전 자동 백업 (hook)
- 보안 steering 수정 시 CTO보안허브 정책 안내 필수
- 산출물 분할 생성 시 `project/artifact-checklist.md`로 완전성 확인
