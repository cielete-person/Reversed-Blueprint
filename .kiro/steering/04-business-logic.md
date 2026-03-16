---
inclusion: manual
---

# Step 4: 비즈니스 로직 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-4. 비즈니스 로직

## 목표

코드에서 비즈니스 규칙, 에러/예외 처리 패턴, 상태 전이 조건, 이벤트/메시지 흐름을 추출한다.

## 작업 절차

### 1. 비즈니스 규칙 추출
- 코드 내 하드코딩된 비즈니스 규칙을 식별하라 (조건문, 상수, 설정값)
- 설정 파일(properties, yaml, DB 설정 테이블)에서 관리되는 규칙도 포함하라
- 각 규칙에 ID를 부여하고 메타정보를 정리하라:
  - 규칙 ID, 규칙명, 설명, 소스 위치, 관련 서비스, 확인 상태

### 2. 에러/예외 처리 패턴 추출
- 서비스별 에러 코드 체계를 수집하라 (HTTP 상태코드, 커스텀 에러코드)
- 예외 처리 방식을 분류하라 (GlobalExceptionHandler, ErrorBoundary, try-catch 등)
- 사용자 노출 에러 메시지 목록을 정리하라
- 외부 연동 실패 시 fallback/retry 정책을 식별하라

### 3. 상태값 및 전이 조건 추출
- 도메인 객체의 상태 enum/status 컬럼을 식별하라
- 상태 변경 로직에서 전이 조건을 역추적하라
- Mermaid stateDiagram으로 상태 전이도를 작성하라

### 4. 이벤트/메시지 흐름 추출
- Kafka, RabbitMQ, Redis Pub/Sub 등 비동기 메시지 Producer/Consumer를 식별하라
- 이벤트 토픽/큐 목록, 이벤트 스키마(페이로드 구조)를 정리하라
- 발행-구독 관계를 매핑하라

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/04-business-logic/` 에 아래 파일 생성:
- `business-rules.md` — 비즈니스 규칙 목록
- `error-handling.md` — 에러 코드/예외 처리 패턴 목록
- `state-machines.md` — 상태 전이 다이어그램 (Mermaid stateDiagram)
- `event-flows.md` — 이벤트/메시지 토픽 목록 및 발행-구독 관계

## 완료 기준
- 주요 비즈니스 규칙이 ID와 함께 목록화됨
- 에러 코드 체계가 서비스별로 정리됨
- 핵심 도메인 객체의 상태 전이도가 작성됨
- 비동기 메시지 흐름이 매핑됨
