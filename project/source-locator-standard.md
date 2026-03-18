# 소스 로케이터 표준 (Source Locator Standard)

> 모든 산출물에서 코드 위치를 참조할 때 이 표준 포맷을 사용합니다.
> PM이 설계도/문제점 리포트에서 해당 코드를 즉시 찾을 수 있도록 일관된 형식을 유지합니다.

## 소스 로케이터 포맷

### 기본 형식

```
{Repo명}/{상대경로}:{라인번호}
```

### 예시

| 유형 | 소스 로케이터 | 설명 |
|---|---|---|
| 클래스 | `svc-payment/src/main/java/com/example/PaymentService.java:42` | 42번 라인 |
| 메서드 | `svc-payment/src/main/java/com/example/PaymentService.java:42#processPayment()` | 메서드 지정 |
| 설정 파일 | `svc-payment/src/main/resources/application-prod.yml:15` | 설정값 위치 |
| 프론트엔드 | `app-android/app/src/main/kotlin/HomeActivity.kt:88` | 앱 소스 |
| 테스트 | `svc-payment/src/test/java/PaymentServiceTest.java:25` | 테스트 코드 |
| IaC | `infra/terraform/modules/payment/main.tf:12` | 인프라 코드 |

### 라인번호 생략 허용 케이스

- 파일 전체가 대상인 경우: `svc-payment/src/main/resources/application.yml`
- 디렉토리 전체가 대상인 경우: `svc-payment/src/main/java/com/example/config/`

### 범위 지정

```
{Repo명}/{상대경로}:{시작라인}-{끝라인}
```

예: `svc-payment/src/main/java/PaymentService.java:42-68`

### 메서드/함수 지정 (라인번호 대신)

```
{Repo명}/{상대경로}#{클래스명}.{메서드명}()
```

예: `svc-payment/src/main/java/PaymentService.java#PaymentService.processPayment()`

## 소스 없는 구간 표기

| 상황 | 표기 | 예시 |
|---|---|---|
| 바이너리 SDK | `<<binary SDK: {SDK명} v{버전}>>` | `<<binary SDK: PaymentSDK v3.2>>` |
| 소스 미확보 서버 | `<<no-source: {서비스명}>>` | `<<no-source: SVC-007 DAS>>` |
| 외부 시스템 | `<<external: {시스템명}>>` | `<<external: PG사 승인 API>>` |
| 콘솔/어드민 | `<<console: {콘솔명}>>` | `<<console: 푸시 발송 콘솔>>` |

## 산출물 테이블에서의 사용

모든 산출물의 항목 테이블에 `소스 위치` 컬럼을 포함합니다:

```markdown
| 항목 | 설명 | 소스 위치 | 확인 상태 |
|---|---|---|---|
| 요금 계산 규칙 | 월정액 + 부가서비스 합산 | `svc-billing/src/.../BillingCalc.java:55#calculate()` | ✅ |
| DRM 인증 | 콘텐츠 재생 전 라이선스 검증 | `<<binary SDK: DRM SDK v2.1>>` | ⚠️ |
```

## 복수 위치 표기

하나의 항목이 여러 파일에 걸쳐 있는 경우:

```markdown
| 소스 위치 |
|---|
| `svc-payment/src/.../PaymentController.java:28` (진입점) |
| `svc-payment/src/.../PaymentService.java:42` (비즈니스 로직) |
| `svc-payment/src/.../PaymentRepository.java:15` (DB 접근) |
```

## 적용 범위

이 표준은 아래 모든 산출물에 적용됩니다:

- Phase 1 extraction 산출물 (Step 01~08)
- Phase 2 architecture view 산출물 (Step 09)
- Phase 3 gap-analysis 산출물 (Step 10)
- 문제 리포트 및 개선 권고사항
