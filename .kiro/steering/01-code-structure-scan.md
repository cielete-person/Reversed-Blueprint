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

### 5. 디바이스/플랫폼 분기 코드 추출
- **모바일 앱 (iOS / Android)**:
  - 플랫폼별 네이티브 코드 분기를 식별하라 (Platform.OS, #if os(iOS), Build.VERSION 등)
  - 공유 코드 vs 플랫폼 전용 코드 비율을 분석하라 (React Native: .ios.js/.android.js, Flutter: Platform.isIOS)
  - 플랫폼별 네이티브 모듈/브릿지 목록을 추출하라 (카메라, 푸시, 생체인증, 결제 등)
  - 플랫폼별 권한(Permission) 요청 차이를 식별하라 (iOS Info.plist vs Android Manifest)
  - 플랫폼별 푸시 알림 처리 차이를 식별하라 (APNs vs FCM)
- **STB 앱 (AOSP / Android TV)**:
  - AOSP 기반 커스텀 론처(LGU+ 자체 론처) 관련 코드를 식별하라
  - Android TV Leanback 라이브러리 사용 여부 및 커스텀 UI 범위를 확인하라
  - STB 기종별 분기 코드를 식별하라 (저사양/고사양, 칩셋별, 미들웨어별)
  - TV 입력 프레임워크(TIF) / CAS / DRM 연동 코드를 식별하라
  - 리모컨 전용 네비게이션 로직을 추출하라 (D-pad 포커스, 키 이벤트)
- **크로스 플랫폼 공통**:
  - 디바이스 타입별 분기 로직을 식별하라 (phone/tablet/TV/STB)
  - 화면 크기/해상도별 레이아웃 분기를 식별하라 (Responsive/Adaptive)
  - OS 버전별 분기 코드를 식별하라 (minSdkVersion, iOS Deployment Target 등)

### 6. 기술 부채 마커 추출 (Tech Debt Marker Extraction)
- 소스코드 내 TODO, FIXME, HACK, WORKAROUND, XXX, DEPRECATED 주석을 전수 스캔하라
- 마커별 메타정보를 추출하라: 파일 위치, 라인 번호, 작성자(git blame), 작성일, 내용
- 마커 밀집도를 분석하라: 파일/모듈별 기술 부채 마커 수 집계
- ADR(Architecture Decision Record), CHANGELOG, README 내 설계 의도 기술을 수집하라
- 심각도 분류는 개발 리드가 수작업으로 확정 → `[KIRO+확정]` (manual-tasks.md P1-4)

### 7. IaC(Infrastructure as Code) 분석
- Terraform(.tf), Helm Chart(Chart.yaml, values.yaml), Ansible(playbook.yml), CloudFormation(.yaml/.json), Kustomize(kustomization.yaml) 파일을 식별하라
- IaC에서 인프라 아키텍처를 역추적하라: VPC/서브넷, 보안 그룹, 로드밸런서, 오토스케일링
- k8s NetworkPolicy, 서비스 메시(Istio/Linkerd) 설정을 식별하라
- IaC 파일이 소스 Repo에 없는 경우 → `🔍 인터뷰필요` (manual-tasks.md P1-7)

### 8. CI/CD 파이프라인 상세 분석
- Jenkinsfile, .gitlab-ci.yml, bitbucket-pipelines.yml, GitHub Actions(.github/workflows/) 등 파이프라인 파일을 스캔하라
- 빌드 스테이지 구성을 정리하라: lint → test → build → deploy 순서 및 게이트 조건
- 배포 전략을 식별하라: 블루그린/카나리/롤링 `[CDR 8.1.1]`
- 자동 롤백 트리거 조건을 식별하라
- 환경별 배포 파이프라인 차이를 정리하라 (dev/staging/prod)

## 필수 탐색 파일 패턴 (Pass 1 구조 스캔용)

> 대용량 Repo에서 이 Step의 항목을 "해당 없음"으로 판정하기 전에, 아래 패턴을 반드시 검색하라.

| 카테고리 | fileSearch 패턴 | grepSearch 키워드 |
|---|---|---|
| 빌드 파일 | `pom.xml`, `build.gradle`, `package.json`, `go.mod`, `requirements.txt`, `Cargo.toml`, `*.csproj` | `dependencies`, `plugins`, `devDependencies` |
| 설정 파일 | `application*.yml`, `application*.properties`, `*.env*`, `config/**`, `bootstrap*.yml` | `spring.profiles`, `server.port`, `datasource` |
| IaC 파일 | `*.tf`, `Chart.yaml`, `values.yaml`, `kustomization.yaml`, `*.cfn.yml`, `playbook*.yml` | `resource`, `provider`, `helm`, `ansible` |
| CI/CD | `Jenkinsfile`, `.gitlab-ci.yml`, `bitbucket-pipelines.yml`, `.github/workflows/*.yml` | `pipeline`, `stage`, `deploy`, `build` |
| 플랫폼 분기 | `*.ios.js`, `*.android.js`, `Info.plist`, `AndroidManifest.xml`, `proguard-rules.pro` | `Platform.OS`, `Build.VERSION`, `#if os(iOS)` |
| 기술 부채 | — | `TODO`, `FIXME`, `HACK`, `WORKAROUND`, `XXX`, `DEPRECATED` |
| 하드코딩 탐지 | — | `http://`, `https://` (*.java/*.kt/*.js/*.ts 내 문자열), `password`, `passwd`, `secret`, `api_key`, `apikey`, `token`, `jdbc:`, `port = ` |

## 소스코드 위치

`services/{서비스명}/src/` — 분석 대상 소스코드

## 산출물

`services/{서비스명}/docs/extraction/01-code-structure/` 에 아래 파일 생성:
- `tech-profile.md` — 기술 프로파일 시트
- `directory-structure.md` — 디렉토리 구조 및 아키텍처 패턴
- `dependencies.md` — 의존성 목록 (내부/외부 구분)
- `config-profiles.md` — 설정 프로파일 비교표 (dev/staging/prod)
- `platform-branching.md` — 디바이스/플랫폼 분기 코드 인벤토리 (iOS/Android/STB)
- `tech-debt-markers.md` — 기술 부채 마커 목록 (TODO/FIXME/HACK, 밀집도 분석)
- `iac-inventory.md` — IaC 인벤토리 (Terraform/Helm/Ansible/CloudFormation → 인프라 역추적)
- `cicd-pipeline.md` — CI/CD 파이프라인 분석서 (빌드 스테이지, 배포 전략, 롤백 조건)

## 💡 효과적인 추가 프롬프트 예시

> 출처: [prompt-cookbook.md](../project/prompt-cookbook.md)

**빌드 파일 누락 시:**
```
src/ 하위에서 pom.xml, build.gradle, package.json, go.mod 파일을 모두 찾아줘.
멀티 모듈이면 하위 모듈의 빌드 파일도 포함해줘.
```

**하드코딩 전수 스캔:**
```
소스코드(.java/.kt/.js/.ts)에서 http://, https://, jdbc:, password, secret, api_key 가
포함된 문자열 리터럴을 모두 찾아줘. 설정파일(.yml/.properties)은 제외하고.
```

**IaC 파일 누락 시:**
```
프로젝트 루트와 ops/, deploy/, infra/, k8s/, helm/ 폴더에서
Dockerfile, *.tf, Chart.yaml, values.yaml, kustomization.yaml 을 찾아줘.
```

## 🔧 작업자 수작업 보충 항목

> 이 Step에서 KIRO 자동 추출만으로는 완성할 수 없는 항목입니다.
> 산출물 생성 후 아래 항목을 작업자가 직접 보충해 주세요.
> 상세: [manual-tasks.md](../project/manual-tasks.md)

| 항목 ID | 항목명 | 유형 | 해야 할 일 |
|---|---|---|---|
| P1-4 | 기술 부채 마커 검토 및 분류 | `[확정]` | KIRO가 추출한 TODO/FIXME/HACK 목록의 심각도(🔴/🟡/⚪)를 개발 리드가 확정 |
| P1-7 | IaC 파일 수집 | `[문서수집]` | IaC 파일이 소스 Repo에 없는 경우 인프라팀에 별도 요청하여 수집 |
| P1-6 | DB 마이그레이션 이력 수집 | `[도구실행]` | Flyway/Liquibase 마이그레이션 스크립트 수집 및 주요 변경점 정리 |

## 완료 기준
- 대상 서비스의 기술 프로파일이 작성됨
- 각 항목에 확인 상태(✅/⚠️/❌/🔍)가 표기됨
