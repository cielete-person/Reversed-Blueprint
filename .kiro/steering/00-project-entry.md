---
inclusion: auto
---

# 설계도 파묘 프로젝트 — 자동 가이드

> 이 steering은 KIRO가 항상 자동으로 읽는 진입점입니다. 작업자가 별도로 호출할 필요 없습니다.

## 프로젝트 개요

이 워크스페이스는 **"설계도 파묘"** 프로젝트입니다.
운영 중인 서비스의 소스코드로부터 Stakeholder 관점별 설계 문서를 역추적·추출합니다.

## KIRO의 역할

작업자가 서비스명이나 소스코드 경로를 알려주면, KIRO가 아래를 자동으로 수행합니다:
1. 서비스 폴더 생성 및 소스코드 배치
2. 13단계 steering(01→1b→1c→02~10)에 따른 설계도 추출
3. 산출물 생성 및 버전 백업

## 작업자 프롬프트 감지 및 자동 안내

작업자가 아래와 같은 프롬프트를 입력하면, 해당 절차를 자동으로 시작하세요:

### 패턴 1: 소스코드 경로를 알려주는 경우
- "소스코드를 clone해 뒀어. 경로는 ..."
- "C:\Projects\xxx 에 소스가 있어"
- "이 폴더에서 설계도 추출해줘"

→ `#00-extraction-overview`의 "사용자가 이미 clone한 소스 경로를 알려준 경우" 절차를 따르세요.
→ 멀티 경로면 "사용자가 여러 경로를 알려준 경우 (멀티 Repo 자동 판별)" 절차를 따르세요.

### 패턴 2: 서비스명을 지정하는 경우
- "SVC-001 서비스 작업 시작하고 싶어"
- "IPTV VOD 설계도 추출해줘"
- "media-iptv-vod 분석 시작"

→ `project/service-inventory.md`에서 서비스 정보를 확인하고, 확인 메시지를 출력한 후 승인을 받으세요.

### 패턴 3: 처음 시작하는 경우
- "뭐부터 하면 돼?"
- "설계도 추출 시작하고 싶어"
- "이 프로젝트 어떻게 쓰는 거야?"

→ 아래와 같이 안내하세요:
```
👋 설계도 파묘 프로젝트에 오신 것을 환영합니다!

이 프로젝트는 소스코드에서 설계도를 역추적·추출하는 작업입니다.

📖 시작하기 전에:
1. project/operator-manual.md (작업자용 매뉴얼)을 먼저 읽어보세요
2. project/service-inventory.md 에서 대상 서비스를 확인하세요

🚀 바로 시작하려면:
- 이미 소스코드를 clone해 뒀다면:
  "소스코드를 clone해 뒀어. 경로는 {경로}야. 서비스명은 {서비스명}이야."
- 서비스명만 알고 있다면:
  "SVC-001 서비스 작업 시작하고 싶어"
- 서비스 목록을 보고 싶다면:
  "서비스 목록 보여줘"

💡 각 분석 단계는 채팅에서 # + steering 문서명으로 실행합니다:
  예) #01-code-structure-scan 서비스: media-iptv-vod
```

### 패턴 4: 특정 Step을 실행하는 경우
- "#01-code-structure-scan 서비스: xxx"
- "#03-api-and-data 서비스: xxx"

→ 해당 steering 문서의 지시를 따르세요. 반드시 서비스 ID 확인 → 승인 → 분석 순서를 지키세요.

## 필수 참조 문서

| 문서 | 용도 |
|---|---|
| `project/operator-manual.md` | 작업자용 실행 가이드 (신규 작업자 필독) |
| `project/service-inventory.md` | 서비스 목록, Repo URL, 진행 현황 |
| `project/prompt-cookbook.md` | 효과적인 추가 프롬프트 패턴 |
| `.kiro/steering/00-extraction-overview.md` | Steering 개요 및 공통 규칙 |

## 공통 규칙 요약

- 모든 항목에 확인 상태 표기: ✅/⚠️/❌/🔍
- 산출물 덮어쓰기 전 자동 백업 (`backup-before-overwrite` hook 동작)
- `services/*/docs/` 산출물 작업 완료 시 프롬프트 피드백 수집 (`collect-prompt-feedback` hook 동작)
- ❌ 판정 전 최소 6회 검색 검증 필수 (2-Pass 스캔 전략)
- Step 01 완료 후 반드시 Step 1b(Dead Code 분석)를 실행하여 미사용 코드/DB/API를 식별
- Step 1b 완료 후 Step 1c(공통 모듈 그룹핑)를 실행하여 공통 패키지/사내 라이브러리를 식별
- 보안 steering 수정 시 CTO보안허브 정책 안내 필수 출력
