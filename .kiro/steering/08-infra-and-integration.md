---
inclusion: manual
---

# Step 8: 인프라, 외부 연동, STB, 비기능 설계도 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-5, 1-8, 1-9

## 선행 산출물 참조

- **Step 1b(Dead Code)**: `01b-dead-code/` 산출물에서 Dead 판정(🔴)된 외부 연동 코드(미사용 API 클라이언트, 미사용 MQ Producer/Consumer)는 인프라 분석에서 제외하라. 조건부 Dead(🟡)는 포함하되 `⚠️ Dead 의심` 표기.
- **Step 1c(공통 모듈)**: `01c-common-modules/` 산출물에서 공통 외부 연동 모듈(공통 HTTP Client, 공통 MQ 설정 등)을 식별하여, 외부 연동 인벤토리에 공통 모듈 경유 여부를 표기하라.

## 목표

외부 연동 시스템, 배포 토폴로지, STB 하드웨어 리소스, 비기능 현황을 추출한다.

## 작업 절차

### 1. 외부 연동 시스템 인벤토리
- BSS/OSS, 과금, CRM, 본인인증, 결제(PG) 등 외부 시스템을 식별하라
- 연동 방식(API, MQ, FTP, DB Link), 프로토콜, 담당 조직을 정리하라
- 연동별 SLA, Rate Limit, 점검 시간대, 데이터 포맷 제약사항을 확인하라 (미확인 시 🔍)

### 2. 배포 토폴로지
- Dockerfile, k8s manifest, docker-compose, CI/CD 파이프라인에서 환경 구성을 역추적하라
- 서비스별 인스턴스 수, 리소스 할당, 로드밸런서, CDN 구성을 정리하라
- Dev/Staging/Production 환경별 차이점을 식별하라

### 3. STB 하드웨어 리소스 (IPTV/홈 사업)
- STB 소스코드에서 프로세스 관리 로직을 식별하라 (watchdog, OOM handler)
- 메모리 할당/해제 패턴, 기종별 분기 조건을 추출하라
- 리소스 임계치 상수/설정값을 수집하라

### 4. 비기능 현황 (NFR Baseline)
- 코드에서 확인 가능한 항목: Timeout, 커넥션 풀, 캐시 TTL, Rate Limit 설정
- 코드에서 확인 불가(🔍): 실제 TPS, 응답시간, SLA/SLO

### 5. 런타임 데이터 수집 안내 (수작업)
- 이 항목은 KIRO 자동 추출 불가, 작업자가 수작업으로 수집해야 한다
- APM/모니터링에서 API별 TPS, 응답시간, 에러율 수집 → manual-tasks.md P1-1
- DB 슬로우 쿼리 / 실행 계획 수집 → manual-tasks.md P1-2
- 분산 추적 데이터(Zipkin/Jaeger) 수집 → manual-tasks.md P1-3
- 수집 결과를 `services/{서비스명}/docs/extraction/runtime-data.md`에 기록
- KIRO가 코드에서 추출한 Call Flow / 성능 병목 후보와 런타임 데이터를 비교 검증하는 데 활용

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

> 대용량 Repo에서 이 Step의 항목을 "해당 없음"으로 판정하기 전에, 아래 패턴을 반드시 검색하라.

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| 컨테이너/배포 | `Dockerfile`, `docker-compose*`, `*.yaml`, `*.yml` (k8s) | `FROM`, `EXPOSE`, `apiVersion`, `kind: Deployment`, `kind: Service`, `kind: Ingress` |
| k8s manifest | `*deployment*`, `*service*`, `*ingress*`, `*configmap*`, `*secret*` | `replicas`, `containers`, `resources`, `limits`, `requests`, `livenessProbe` |
| 외부 연동 설정 | `*Client*`, `*Gateway*`, `*Adapter*`, `*Connector*`, `*Integration*` | `@FeignClient`, `RestTemplate`, `WebClient`, `HttpClient`, `SoapClient`, `FTP`, `SFTP` |
| 캐시 설정 | `*Cache*`, `*Redis*`, `*Memcached*` | `@Cacheable`, `RedisTemplate`, `CacheManager`, `spring.cache`, `spring.redis` |
| 메시지 큐 | `*Kafka*`, `*Rabbit*`, `*MQ*`, `*Messaging*` | `spring.kafka`, `spring.rabbitmq`, `@KafkaListener`, `@RabbitListener`, `JmsTemplate` |
| STB 리소스 | `*Watchdog*`, `*OOM*`, `*Memory*`, `*Resource*` | `watchdog`, `oom`, `lowMemory`, `onTrimMemory`, `getMemoryInfo`, `Runtime.getRuntime` |
| NFR 설정 | `application*.yml`, `*timeout*`, `*pool*` | `timeout`, `connection-pool`, `max-connections`, `ttl`, `rate-limit`, `circuit-breaker` |

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/08-infra/` 에 아래 파일 생성:
- `external-systems.md` — 외부 연동 시스템 인벤토리
- `deployment-topology.md` — 배포 토폴로지
- `stb-resources.md` — STB 리소스 관리 현황
- `nfr-baseline.md` — 비기능 현황 베이스라인
- `runtime-data.md` — 런타임 트래픽/성능 데이터 (수작업 수집, manual-tasks.md P1-1~P1-3)

## 💡 효과적인 추가 프롬프트 예시

> 출처: [prompt-cookbook.md](../project/prompt-cookbook.md)

**외부 연동 누락 시:**
```
@FeignClient, RestTemplate, WebClient, HttpClient, SoapClient, FTP 키워드를 검색해줘.
application.yml에서 외부 URL 설정도 확인해줘.
```

**k8s 리소스 누락 시:**
```
Dockerfile, docker-compose, k8s manifest(deployment, service, ingress, configmap)를 찾아줘.
ops/, deploy/, k8s/, helm/ 폴더도 확인해줘.
```

## 완료 기준
- 외부 연동 시스템이 연동 방식과 함께 목록화됨
- 배포 환경별 구성 차이가 식별됨
- STB 기종별 리소스 제약이 정리됨
