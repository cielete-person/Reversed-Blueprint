---
inclusion: manual
---

# Step 5: 보안 관련 설계도 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-7. 보안 관련 추출

## 목표

보안센터가 참조할 수 있는 보안 설계도를 소스코드에서 역추적한다.
개인정보보호법/정보통신망법 규제 대응 현황도 포함한다.

## 작업 절차

### 1. 크리덴셜/시크릿 관리 현황
- 소스코드 내 하드코딩된 ID/PW, API Key, 토큰을 탐지하라
- 설정 파일 내 크리덴셜 저장 방식을 식별하라 (평문/암호화/환경변수/Vault)
- 위험도 등급을 부여하라: 🔴 Critical / 🟡 Warning / 🟢 Safe

### 2. 개인정보 처리 현황
- 개인정보 항목(이름, 전화번호, 주소, CI/DI 등)의 코드 내 사용 위치를 스캔하라
- 암호화 적용 여부(저장 시/전송 시/표시 시 마스킹)를 확인하라
- 수집 동의 로직, 보관 기간, 파기 로직을 식별하라
- 제3자 제공/위탁 처리 흐름(외부 연동 시 개인정보 전달)을 추적하라

### 3. 세션/토큰 관리
- 세션 생성/만료/갱신 정책, JWT 설정(유효기간, Refresh Token, 서명 알고리즘)을 식별하라
- 동시 로그인 제어 정책, 세션 고정 방어 메커니즘을 확인하라

### 4. 입력값 보안 검증
- XSS, SQL Injection, CSRF, Path Traversal 방어 현황을 확인하라
- 보안 필터 체인(Spring Security Filter, Express middleware 등)을 식별하라

### 5. API 보안
- API별 인증 방식(없음/API Key/OAuth/JWT/mTLS)을 매핑하라
- Rate Limiting, IP 화이트리스트 설정을 확인하라
- 민감 데이터 반환 API, 내부 전용 API 외부 노출 여부를 점검하라

### 6. 감사 로그
- 개인정보 접근/변경 이력 로깅 현황을 확인하라
- 감사 로그 저장소, 보관 기간, 위변조 방지 메커니즘을 식별하라

### 7. 데이터 암호화 상세
- 저장 시(At Rest) / 전송 시(In Transit) 암호화 현황을 정리하라
- 사용 중인 암호화 알고리즘을 식별하고, 취약 알고리즘(MD5, SHA1, DES) 사용 시 🔴 표기하라

### 8. 보안 하드닝
- HTTP 보안 헤더(HSTS, CSP, X-Frame-Options 등) 적용 현황을 확인하라
- CORS 설정, 디버그 모드 잔존, 에러 응답 내부 정보 노출을 점검하라

### 9. 서드파티 스크립트
- 프론트엔드 외부 스크립트(광고 SDK, 분석 도구 등) 목록을 작성하라
- SRI 적용 여부, 개인정보 유출 위험을 평가하라

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/05-security/` 에 아래 파일 생성:
- `credential-management.md` — 크리덴셜 관리 현황 (위험도 등급 포함)
- `pii-privacy-map.md` — 개인정보 처리 현황 맵
- `session-token-management.md` — 세션/토큰 관리 현황
- `input-security-validation.md` — 입력값 보안 검증 현황
- `api-security-posture.md` — API 보안 현황 매트릭스
- `audit-log-status.md` — 감사 로그 현황
- `encryption-inventory.md` — 데이터 암호화 인벤토리
- `security-hardening.md` — 보안 하드닝 체크리스트
- `third-party-scripts.md` — 서드파티 스크립트 인벤토리

## 완료 기준
- 모든 보안 항목에 확인 상태가 표기됨
- 개인정보 항목별 라이프사이클(수집→저장→처리→전송→파기)이 추적됨
- 크리덴셜 위험도 등급이 부여됨
- 취약 암호화 알고리즘 사용 영역이 식별됨
