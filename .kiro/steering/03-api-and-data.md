---
inclusion: manual
---

# Step 3: API 엔드포인트 추출 및 DB 스키마 역추적

> 참조: #[[file:project/extraction-checklist.md]] — 1-3. API 및 데이터

## 목표

REST API 엔드포인트를 자동 추출하고, DB 스키마를 역추적하여 ERD 초안을 생성한다.

## 작업 절차

### 1. API 엔드포인트 추출
- REST Controller/Router 파일을 스캔하라
  - Spring: `@RestController`, `@RequestMapping`, `@GetMapping` 등
  - Express/Koa: `router.get()`, `router.post()` 등
  - FastAPI: `@app.get()`, `@app.post()` 등
- 각 엔드포인트의 메타정보를 추출하라:
  - HTTP 메서드, URL 경로, 요청/응답 DTO, 인증 필요 여부
- 서비스 간 호출 관계를 식별하라 (Feign, RestTemplate, gRPC, HttpClient 등)

### 2. DB 스키마 역추적
- ORM Entity/Mapper를 분석하여 테이블 구조를 추출하라
  - JPA: `@Entity`, `@Table`, `@Column`
  - MyBatis: Mapper XML, `@Select` 등
  - Sequelize/TypeORM: 모델 정의
- 테이블 간 관계(FK, Join)를 식별하라
- DDL 파일이 있는 경우 수집하라

### 3. 서비스 간 통신 매트릭스
- 서비스 A → 서비스 B 호출 관계를 매트릭스로 정리하라
- 동기(API) / 비동기(MQ) 구분하라

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/03-api-data/` 에 아래 파일 생성:
- `api-endpoints.md` — API 엔드포인트 목록
- `erd.md` — ERD 초안 (Mermaid erDiagram 사용)
- `service-communication-matrix.md` — 서비스 간 통신 매트릭스

## 완료 기준
- 모든 Controller/Router의 엔드포인트가 목록화됨
- ERD 초안이 Mermaid 다이어그램으로 작성됨
- 서비스 간 호출 관계가 매트릭스로 정리됨
