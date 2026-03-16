---
inclusion: manual
---

# Step 8: 인프라, 외부 연동, STB, 비기능 설계도 추출

> 참조: #[[file:project/extraction-checklist.md]] — 1-5, 1-8, 1-9

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

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/08-infra/` 에 아래 파일 생성:
- `external-systems.md` — 외부 연동 시스템 인벤토리
- `deployment-topology.md` — 배포 토폴로지
- `stb-resources.md` — STB 리소스 관리 현황
- `nfr-baseline.md` — 비기능 현황 베이스라인

## 완료 기준
- 외부 연동 시스템이 연동 방식과 함께 목록화됨
- 배포 환경별 구성 차이가 식별됨
- STB 기종별 리소스 제약이 정리됨
