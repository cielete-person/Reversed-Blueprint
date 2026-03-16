# 서비스 인벤토리

> Phase 0에서 작성. 대상 서비스 목록과 Bitbucket Repository 정보를 관리한다.

## 서비스 목록

| 서비스 ID | 서비스명 | 폴더명 | 사업구분 | Bitbucket Repo URL | 담당조직 | 소스 유무 | 비고 |
|---|---|---|---|---|---|---|---|
| SVC-001 | (예시) 모바일 홈 | mobile-home | 모바일 | https://bitbucket.../mobile-home.git | 모바일개발팀 | ✅ | |
| SVC-002 | (예시) IPTV STB 앱 | iptv-stb-app | 홈 | https://bitbucket.../iptv-stb.git | 홈개발팀 | ✅ | 저사양 STB |
| SVC-003 | (예시) 과금 API | billing-api | 공통 | — | 과금팀 | ❌ | 소스 없음 |

## 서비스 ID 채번 규칙

- 형식: `SVC-{3자리 순번}`
- 순번은 등록 순서대로 부여
- 사업구분: 모바일 / 홈 / 공통

## 소스코드 clone 명령

```bash
# 서비스별 소스코드를 services/{폴더명}/src/ 에 clone
git clone {Repo URL} services/{폴더명}/src
```

## 분석 진행 현황

| 서비스 ID | Step 01 | Step 02 | Step 03 | Step 04 | Step 05 | Step 06 | Step 07 | Step 08 | Step 09 | Step 10 |
|---|---|---|---|---|---|---|---|---|---|---|
| SVC-001 | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ |
| SVC-002 | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ |

> ⬜ 미시작 / 🔄 진행중 / ✅ 완료 / ⏭️ 해당없음
