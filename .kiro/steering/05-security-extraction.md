---
inclusion: manual
---

# Step 5: 보안 관련 설계도 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-7. 보안 관련 추출
> 참조: #[[file:project/security-layer-checklist.md]] — 보안기술팀 Security Layer 체크리스트 (217항목)

## 목표

> ⚠️ **이 steering을 수정·고도화하려는 경우 필수 확인:**
> 현재 보안 체크리스트(217항목)는 보안기술팀 CTO 제공 기준입니다.
> 항목을 추가·수정하려면 CTO보안허브의 정책 문서(패스워드 관리, 개인정보 파기, 암호화 적용 등)를 반영하여
> 1~2 depth 더 상세한 수준으로 steering을 고도화해야 합니다.
> 상세 안내: `00-extraction-overview.md` 공통 규칙 8번 참조

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

### 10. 모바일 앱 쉴딩 현황 (Android/iOS)
- 코드 난독화 적용 여부를 확인하라:
  - Android: ProGuard/R8 설정 파일(proguard-rules.pro), 난독화 제외 항목
  - iOS: 난독화 도구(iXGuard, SwiftShield 등) 적용 여부
  - 크로스플랫폼(React Native/Flutter): Hermes 엔진, 코드 번들 난독화
- 앱 위변조 탐지 메커니즘을 식별하라 (서명 검증, 해시 비교, 대응 로직)
- 루팅/탈옥 탐지 현황을 확인하라 (RootBeer, SafetyNet/Play Integrity, DeviceCheck)
- 디버깅/리버스 엔지니어링 방지 현황을 확인하라 (디버거 탐지, 에뮬레이터 탐지, Frida/Xposed 탐지)
- 상용 앱 쉴딩 솔루션 적용 여부를 확인하라 (AppSealing, Arxan, Promon, 에스이웍스 등)
- 안전한 키 저장(Android Keystore/iOS Keychain) 활용 여부를 확인하라
- Security Layer 체크리스트 169~181번(모바일 앱 환경) 대비 현황을 매핑하라

산출물: `services/{서비스명}/docs/extraction/05-security/app-shielding.md`

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

> 대용량 Repo에서 이 Step의 항목을 "해당 없음"으로 판정하기 전에, 아래 패턴을 반드시 검색하라.

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| Security 설정 | `*SecurityConfig*`, `*WebSecurityConfig*`, `*AuthConfig*`, `*SecurityFilter*` | `@EnableWebSecurity`, `SecurityFilterChain`, `HttpSecurity`, `cors`, `csrf` |
| 암호화 | `*Crypto*`, `*Encrypt*`, `*Cipher*`, `*Hash*`, `*Aes*`, `*Rsa*` | `AES`, `RSA`, `SHA-256`, `BCrypt`, `PBKDF2`, `MessageDigest`, `Cipher.getInstance` |
| 인증/인가 | `*Auth*`, `*Login*`, `*Token*`, `*Jwt*`, `*OAuth*`, `*Session*` | `@PreAuthorize`, `@Secured`, `JWT`, `Bearer`, `AccessToken`, `RefreshToken` |
| 개인정보 필드 | — | `phone`, `email`, `name`, `address`, `ci`, `di`, `ssn`, `주민`, `전화`, `이메일`, `@PersonalInfo` |
| 감사 로그 | `*Audit*`, `*AuditLog*`, `*AccessLog*` | `@Audit`, `AuditTrail`, `audit_log`, `access_log`, `감사` |
| 앱 쉴딩 | `proguard-rules.pro`, `*Guard*`, `*Shield*`, `*Root*`, `*Jailbreak*` | `ProGuard`, `R8`, `RootBeer`, `SafetyNet`, `DeviceCheck`, `iXGuard`, `AppSealing` |

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
- `app-shielding.md` — 모바일 앱 쉴딩 현황 (난독화, 위변조 탐지, 루팅/탈옥, 디버깅 방지, 쉴딩 솔루션)

### 10. Security Layer 체크리스트 대비 현황 분석

> 참조: #[[file:project/security-layer-checklist.md]] — 보안기술팀 217항목 체크리스트

- 소스코드 분석 결과를 Security Layer 체크리스트 10개 구분(인증/인가, 입력검증, 중요정보, 에러처리, 파일보안, API보안, 오픈소스, 웹앱, 모바일, CI/CD)과 대비하라
- 각 체크리스트 항목(1~217)에 대해 해당 서비스의 준수 여부를 판정하라:
  - ✅ 준수: 코드에서 해당 보안 요구사항이 구현됨
  - ⚠️ 부분 준수: 일부만 구현되었거나 불완전
  - ❌ 미준수: 해당 요구사항이 구현되지 않음
  - ➖ 해당없음: 서비스 특성상 적용 대상 아님
- 미준수(❌) 항목은 위험도(🔴 Critical / 🟡 Warning)를 부여하라
- 서비스별 준수율 요약표를 작성하라 (구분별 준수/부분/미준수/해당없음 수)

산출물: `services/{서비스명}/docs/extraction/05-security/security-layer-compliance.md`

## 완료 기준
- 모든 보안 항목에 확인 상태가 표기됨
- 개인정보 항목별 라이프사이클(수집→저장→처리→전송→파기)이 추적됨
- 크리덴셜 위험도 등급이 부여됨
- 취약 암호화 알고리즘 사용 영역이 식별됨
