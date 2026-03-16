---
inclusion: manual
---

# Step 2: 화면 목록 추출 및 화면명 정규화

> 참조: #[[file:project/extraction-checklist.md]] — 1-2. 화면 목록 및 정규화

## 목표

프론트엔드 소스에서 전체 화면 목록을 추출하고, 화면 ID를 부여하며, 화면명을 정규화한다.

## 작업 절차

### 1. 화면 목록 추출
- 프론트엔드 라우트/페이지 컴포넌트를 스캔하라
  - React: `react-router` 설정, 페이지 컴포넌트
  - Vue: `vue-router` 설정
  - Angular: `routing module`
  - Native: Activity/Fragment(Android), ViewController(iOS)
  - STB: 미들웨어 화면 정의 파일
- 팝업, 모달, 바텀시트, 탭 등 서브 화면도 누락 없이 식별하라

### 2. 화면 ID 부여
- 채번 규칙: `{사업구분}-{서비스약어}-{화면유형}-{순번}`
- 화면 유형 코드: SCR(일반화면), POP(팝업), MOD(모달), BTM(바텀시트), TAB(탭)

### 3. 화면명 정규화
- 코드에서 추출한 원본 명칭(클래스명, 라우트명, 주석, i18n 키)을 수집하라
- 동일 화면이 다른 명칭을 가지는 경우 클러스터링하라 (URL 경로, 기능, 호출 API 기준)
- 클러스터별 대표 명칭(Canonical Name) 후보를 제시하라
- 정규화 매핑 테이블을 작성하라

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/02-screens/` 에 아래 파일 생성:
- `screen-inventory.md` — 화면 목록 (화면 ID, 화면명, 서비스, URL, 소스 위치)
- `screen-name-normalization.md` — 화면명 정규화 매핑 테이블 (원본 → 대표 명칭)

## 산출물 포맷

```markdown
| 화면 ID | 화면명(정규화) | 원본 명칭 | 서비스 | URL 경로 | 화면 유형 | 소스 파일 | 확인 상태 |
|---|---|---|---|---|---|---|---|
```

## 완료 기준
- 모든 라우트/페이지가 식별되고 화면 ID가 부여됨
- 서브 화면(팝업/모달 등)이 누락 없이 포함됨
- 정규화 매핑 테이블이 완성됨
