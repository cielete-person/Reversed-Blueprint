---
inclusion: manual
---

# Step 1: Repository 스캔 및 코드 구조 분석

> 참조: #[[file:project/extraction-checklist.md]] — 1-1. 코드 구조 분석

## 목표

대상 서비스의 Repository를 스캔하여 기술 스택, 디렉토리 구조, 의존성을 파악한다.

## 작업 절차

### 1. 프로젝트 파일 식별
- `package.json`, `pom.xml`, `build.gradle`, `requirements.txt`, `go.mod` 등 빌드 설정 파일을 찾아 언어/프레임워크를 식별하라
- 결과를 아래 포맷으로 정리:

```markdown
| 서비스명 | 언어 | 프레임워크 | 빌드 도구 | 확인 상태 |
|---|---|---|---|---|
```

### 2. 디렉토리 구조 패턴 분류
- 소스 디렉토리 구조를 분석하여 아키텍처 패턴을 식별하라 (MVC, Hexagonal, Layered, MSA 등)
- 주요 디렉토리와 역할을 매핑하라

### 3. 의존성 목록 추출
- 내부 라이브러리 (사내 공통 모듈) 와 외부 패키지를 구분하여 목록화하라
- 서비스 간 코드 의존성이 있는 경우 식별하라

### 4. 설정 프로파일별 차이 분석
- 환경별 설정 파일을 식별하라: application-{profile}.yml, .env.{환경}, config/{환경}/ 등
- dev/staging/prod 간 주요 차이점을 추출하라: DB 접속 정보, 외부 연동 URL, 기능 플래그, 로그 레벨
- 환경별 활성화되는 Bean/모듈 차이를 식별하라 (@Profile, @ConditionalOnProperty 등)
- 설정값 중 하드코딩 vs 환경변수 vs 외부 설정 서버(Config Server) 구분하라

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/01-code-structure/` 에 아래 파일 생성:
- `tech-profile.md` — 기술 프로파일 시트
- `directory-structure.md` — 디렉토리 구조 및 아키텍처 패턴
- `dependencies.md` — 의존성 목록 (내부/외부 구분)
- `config-profiles.md` — 설정 프로파일 비교표 (dev/staging/prod)

## 완료 기준
- 대상 서비스의 기술 프로파일이 작성됨
- 각 항목에 확인 상태(✅/⚠️/❌/🔍)가 표기됨
