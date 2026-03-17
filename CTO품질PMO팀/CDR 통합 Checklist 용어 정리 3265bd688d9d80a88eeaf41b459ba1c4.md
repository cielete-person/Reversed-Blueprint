# CDR 통합 Checklist 용어 정리

1장 서비스 기획 및 책임 구조 설계 CDR

1.1.1 서비스 개요 및 명칭 CDR

1. 용어: Problem Statement, 도메인 용어, Glossary, NFR(Non-Functional Requirements), NFR(Non-Functional Requirements) 목록/선언, 서비스 카탈로그, 서
    
    비스 카탈로그 매핑
    
2. 초급자 정의: Problem Statement는 “누가/왜/무엇을” 해결하는지 한 문단으로 요약한 문제정의이
    
    고, NFR은 성능/보안/가용성 같은 “품질 요구”입니다. 서비스 카탈로그 매핑은 문서의 서비스가 조
    
    직의 “공식 서비스ID/오너”와 연결됐음을 뜻합니다.
    
3. 실무 예시: “결제 승인 API” 문서 첫머리에 p95 200ms, 월 99.9% 같은 NFR을 선언하고, 서비
    
    스 카탈로그의 SVC-1023 (Owner: 결제플랫폼팀)와 매핑 표를 둡니다.
    
4. 추천 한글 URL:
- NFR 개념/예시: [[정보처리기사] 기능 요구사항 vs 비기능 요구사항: 정의와 예시로 이해하기](https://august-jhy.tistory.com/54)
- NFR 가이드(한글): [비기능 요구사항(Non-Functional Requirements, NFR): IT 시스템의 성능과 안정성을 보장하는 핵심 요소](https://chiba.tistory.com/78)

1.1.2 서비스 범위 및 비범위 정의 CDR

1. 용어: In-Scope, Out-of-Scope, 스코프 결정 기록(Decision Log)
2. 초급자 정의: In/Out-of-Scope는 “이번 릴리스에서 하는 것/안 하는 것”을 기능 단위로 경계선 긋
    
    는 것입니다. 결정 기록은 제외 사유·리스크·승인자를 남긴 증적입니다. CDR
    
3. 실무 예시: “정산 리팩토링은 Out-of-Scope(규제 검토 미완)”로 명시하고, 회의록에 “대신 멱등
    
    키로 중복결제 리스크 완화” 같은 보완책을 남깁니다.
    
4. 추천 한글 URL: (스코프/결정기록은 방법론 성격이 강해 실무 템플릿이 유용합니다)
- RACI/책임할당(결정권자 명확화에 연동): [RACI 차트 란 무엇인가요 ? 정의, 사용 방법 및 예 - ProcessOn](https://www.processon.io/ko/blog/raci-chart-meaning)
- SLA/SLO/SLI(스코프-품질목표 연결에 유용): [https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas](https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas)

1.1.3 서비스 유형 및 제공 방식 CDR

1. 용어: 실시간/배치/이벤트, SLO(Service Level Objective) (운영시간대), Severity(Incident Severity)
2. 초급자 정의: 처리방식은 요청을 즉시 처리하는지(실시간), 모아서 처리하는지(배치), 이벤트로 흘려보내는지(이벤트)를 뜻합니다. Severity는 장애의 심각도를 단계로 나눠 대응 속도/권한을 정하는 기준입니다. CDR
3. 실무 예시: “주문 생성=실시간, 정산=배치(매일 02시), 알림=이벤트”로 매핑표 작성. 장애는 Sev1(전체 결제 불가)~Sev4(일부 로그 누락)로 트리거 정의.
4. 추천 한글 URL:
- SLI/SLO/SLA 차이: [https://www.whatap.io/ko/blog/35/](https://www.whatap.io/ko/blog/35/)
- Atlassian SLI/SLO/SLA: [https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas](https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas)

1.2.1 사업 목적 및 도입 배경 CDR

1. 용어: KPI(Key Performance Indicator) (정량 목표), 기술부채(Tech Debt)
2. 초급자 정의: KPI는 성과를 숫자로 측정 가능한 목표로 만든 것이고, 기술부채는 “나중에 갚아야 할 설계/구현의 미뤄둔 비용”입니다.
3. 실무 예시: “장애 월 3건→1건, 배포 리드타임 2일→2시간” 같은 KPI를 두고, 도입 배경에 “레거시 결제모듈 결합도 높아 변경 리스크 증가”를 기술.
4. 추천 한글 URL:
- SLO/신뢰성 관점 KPI 연결: [https://bcho.tistory.com/1332](https://bcho.tistory.com/1332)

1.2.2 핵심 성과지표(KPI) 정의 CDR

1. 용어: KPI(Key Performance Indicator) 정의식, 측정 주기, 소스 데이터, KPI↔필드/이벤트 매핑, 자동 수집(표준 이벤트)
2. 초급자 정의: KPI는 “어떤 데이터의 어떤 계산식으로, 얼마나 자주” 측정할지까지 정해야 의미가 생깁니다. 이벤트/필드 매핑은 KPI가 어떤 로그/이벤트/DB 컬럼에서 나오는지 연결하는 것입니다. CDR
3. 실무 예시: KPI=“결제 성공률” 정의식 = success / total(1분 집계), 소스=결제승인 이벤트의 result_code, trace_id 포함.
4. 추천 한글 URL:
- 로깅/트레이싱 설계(이벤트/필드 표준화에 직결): [https://techblog.woowahan.com/2710/](https://techblog.woowahan.com/2710/)

1.2.3 서비스 수준목표(SLA/SLO) 정의 CDR

1. 용어: SLA(Service Level Agreement), SLO(Service Level Objective), SLI(Service Level Indicator), Error Budget, Error Budget Burn Rate, Freeze/승인강화
2. 초급자 정의: SLI는 측정값, SLO는 내부 목표, SLA는 고객과의 계약입니다. Error Budget은 SLO가 허용하는 “실패 예산”이고 Burn Rate는 그 예산이 얼마나 빨리 소진되는지입니다.
3. 실무 예시: SLO 99.9%인 API에서 1시간 Burn Rate가 급증하면 “배포 Freeze + 원인 제거 후 재개” 런북을 실행.
4. 추천 한글 URL:
- SLI/SLO/SLA(한글): [https://www.whatap.io/ko/blog/35/](https://www.whatap.io/ko/blog/35/)
- SLO/SLI/SLA(Atlassian 한글): [https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas](https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas)

1.2.4 서비스 중단 시 사업 영향 분석 CDR

1. 용어: Impact Matrix, BIA(Business Impact Analysis), RTO(Recovery Time Objective), RPO(Recovery Point Objective), Residual Risk, Risk Register
2. 초급자 정의: BIA는 중단이 비즈니스에 주는 영향(매출/규제 등)을 분석해 우선순위를 정합니다. RTO/RPO는 복구 시간/데이터 손실 한계 목표이고, Residual Risk는 대책 후에도 남는 리스크를 Risk Register에 기록·승인받는 것입니다.
3. 실무 예시: 결제 중단 30분이면 매출 손실이 커서 RTO 30분, RPO 5분. Active-Active가 비용상 불가하면 “리전 장애 시 2시간 내 복구”를 잔여 리스크로 승인.
4. 추천 한글 URL:
- BIA/RTO/RPO 설명: [비즈니스 연속성 계획의 구성 요소는 무엇입니까?](https://www.zmanda.com/ko/%EB%B8%94%EB%A1%9C%EA%B7%B8/BCP-%ED%95%B5%EC%8B%AC-%EA%B5%AC%EC%84%B1-%EC%9A%94%EC%86%8C/)
- MS BCDR(한글): [https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery](https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery)

1.3.1 서비스 책임자 및 운영 책임자 지정 CDR

1. 용어: Service Owner, Ops Owner, Incident Commander, On-call, 에스컬레이션
2. 초급자 정의: Owner는 “최종 책임자”, IC(Incident Commander)는 장애 시 의사결정/지휘 책임자, On-call은 호출 대응 체계입니다.
3. 실무 예시: 결제 장애 발생 시 IC가 배포 중지/우회 결정, On-call 엔지니어가 런북 수행, 에스컬레이션 기준에 따라 보안/인프라 담당 호출.
4. 추천 한글 URL:
- RACI로 책임구조 정리: [https://www.atlassian.com/ko/team-playbook/plays/raci-matrix](https://www.atlassian.com/ko/team-playbook/plays/raci-matrix)

1.3.2 역할과 책임(RACI) 정의 CDR

1. 용어: RACI(Responsible, Accountable, Consulted, Informed), Change, Emergency Change
2. 초급자 정의: RACI는 Responsible/Accountable/Consulted/Informed로 “누가 실행/최종책임/자문/공유”인지 표로 고정하는 것입니다. Change는 변경통제, Emergency는 긴급 변경 절차입니다.
3. 실무 예시: DB 스키마 변경: R=DBA, A=서비스오너, C=보안/운영, I=CS. 긴급 장애 패치 시 Emergency Change로 승인·사후 리뷰.
4. 추천 한글 URL:
- Atlassian RACI: [https://www.atlassian.com/ko/team-playbook/plays/raci-matrix](https://www.atlassian.com/ko/team-playbook/plays/raci-matrix)
- ProcessOn RACI: [RACI 차트 란 무엇인가요 ? 정의, 사용 방법 및 예 - ProcessOn](https://www.processon.io/ko/blog/raci-chart-meaning)

1.3.3 외부 협력사 및 공급자 책임 범위 CDR

1. 용어: SaaS(Software as a Service), 벤더(Vendor), SLA/SLO, Exit Plan
2. 초급자 정의: SaaS는 외부 서비스(구독형)이고, Exit Plan은 공급자 장애/계약 종료 시 대체 제품·자체 구현·기능 축소로 “탈출 전략”을 갖는 것입니다.
3. 실무 예시: 외부 SMS 벤더 장애 시 “캐시/큐잉 후 재시도, 장기 장애면 대체 벤더로 스위칭”을 문서화.
4. 추천 한글 URL:
- SLO/SLA 개념: [https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas](https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas)

2장 운영 환경 및 플랫폼 구조 설계 CDR

2.1.1 운영체제 및 런타임 구성 CDR

1. 용어: OS(Operating System), JDK(Java Development Kit), 런타임(Runtime), 미들웨어(Middleware), EOL(End of Life), 패치(정기/긴급), 유지보수 창
2. 초급자 정의: EOL은 벤더가 보안패치/지원 제공을 끝내는 시점이라 운영 리스크가 급상승합니다. 유지보수 창은 패치·재부팅으로 서비스 영향이 허용되는 시간대입니다.
3. 실무 예시: JDK 11 EOL 도래 전 업그레이드 계획 + 월 2회 새벽 유지보수 창에 커널 패치/재부팅.
4. 추천 한글 URL:
- (조직별 표준이 강해 “벤더 EOL 정책 + 내부 런북” 링크가 보통 핵심 증적입니다)

2.1.2 가상화·컨테이너 구조 설계 CDR

1. 용어: 컨테이너 이미지(Image), VM AMI(Amazon Machine Image), 서명(Signing), 취약점 스캔(Vulnerability Scan), CI/CD(Continuous Integration/Continuous Delivery), SBOM(Software Bill of Materials), Config/Secret 분리
2. 초급자 정의: SBOM은 소프트웨어 구성요소 “자재명세서”이고, 이미지 서명/스캔은 공급망 공격을 막기 위한 기본 방어입니다. Config/Secret 분리는 설정값과 비밀값(키/토큰)을 분리 관리하는 원칙입니다.
3. 실무 예시: 빌드 파이프라인에서 SBOM 생성→취약점 스캔→서명 후 레지스트리에 푸시. Secret은 Vault/KMS 기반으로 주기 회전.
4. 추천 한글 URL:
- SBOM(IBM): [Think Topics | IBM](https://www.ibm.com/kr-ko/think/topics/software-bill-of-materials)
- SBOM(Wiz Academy): [https://www.wiz.io/academy/software-bill-of-materials-sbom](https://www.wiz.io/academy/software-bill-of-materials-sbom)

2.1.3 네트워크 구성 및 보안 경계 CDR

1. 용어: Inbound, Outbound, 방화벽(Firewall), SG(Security Group)/ACL(Access Control List), WAF(Web Application Firewall), 프록시(Proxy)/게이트웨이(Gateway)
2. 초급자 정의: WAF는 SQLi/XSS 같은 웹 공격을 HTTP 레벨에서 필터링/차단하는 보호 계층입니다. SG/ACL은 네트워크 접근 제어 규칙을 의미하며(환경에 따라 범위/특성이 다름), 다이어그램+포트표로 “경계”를 명확히 해야 합니다.
3. 실무 예시: 인터넷 노출 API는 반드시 WAF/게이트웨이 경유 + 허용 포트(443)만 오픈, 내부 DB는 Outbound도 제한.
4. 추천 한글 URL:
- WAF(팔로알토): [WAF란 무엇인가요? | 웹 애플리케이션 방화벽 설명](https://www.paloaltonetworks.co.kr/cyberpedia/what-is-a-web-application-firewall)
- WAF(포티넷): [https://www.fortinet.com/kr/resources/cyberglossary/web-application-firewall](https://www.fortinet.com/kr/resources/cyberglossary/web-application-firewall)

2.2.1 확장 전략(수직/수평 확장) CDR

1. 용어: 수직/수평 확장(Vertical/Horizontal Scaling), Capacity 모델(용량 산정), Autoscaling(자동 확장) 트리거/상한/하한
2. 초급자 정의: 수직 확장은 “더 좋은 서버로 업그레이드”, 수평 확장은 “서버(파드)를 더 늘림”입니다. 오토스케일은 지표 기반으로 자동 증감시키되 상·하한을 두어 폭주를 막습니다.
3. 실무 예시: CPU 60% 초과 5분이면 파드 2→10까지 증가(HPA), 최소 2 유지.
4. 추천 한글 URL:
- Kubernetes HPA(한글): [https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/](https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/)

2.2.2 부하 분산 및 트래픽 처리 구조 CDR

1. 용어: LB(Load Balancer), Ingress, Healthcheck, Stateless, Sticky Session
2. 초급자 정의: LB/Ingress는 트래픽을 여러 인스턴스로 분산시키는 관문이고, Healthcheck는 “살아있는지” 판단하는 기준입니다. Stateless는 서버가 사용자 상태를 들고 있지 않는 원칙이며, Sticky는 특정 사용자 요청을 같은 서버로 붙이는 방식입니다.
3. 실무 예시: 세션을 서버 메모리에 두면 오토스케일/롤링배포에 취약 → Redis로 세션 외부화(Stateless).
4. 추천 한글 URL:
- Kubernetes Ingress(한글): [인그레스(Ingress)](https://kubernetes.io/ko/docs/concepts/services-networking/ingress/)

2.2.3 병목 구간 분석 및 대응 전략 CDR

1. 용어: 병목(Bottleneck), 캐시(Cache)/큐(Queue)/스케일(Scale), 부하 테스트(Load Test) 계획(Test Plan), Chaos Test(카오스 테스트)
2. 초급자 정의: 병목은 처리량을 제한하는 구간(CPU/DB/외부API 등)이고, 부하 테스트는 목표 트래픽에서 합격 기준을 검증하는 계획입니다. Chaos Test는 의도적으로 장애를 주입해 복원력을 시험합니다.
3. 실무 예시: 외부 결제 API 병목 가설 → 결과 캐시/비동기 큐잉/동시성 제한 적용. Chaos로 DB 연결 끊김 주입 후 자동복구 확인.
4. 추천 한글 URL:
- Chaos Mesh 소개(한글): [https://chaos-mesh.org/ko/docs/](https://chaos-mesh.org/ko/docs/)
- 부하테스트(k6 한글 문서): [Grafana k6 | Grafana k6 documentation](https://k6.io/docs/) (※ k6는 한글 UI/문서 일부 제공)

2.3.1 클라우드/IDC 의존성 분석 CDR

1. 용어: 리전/존(Region/Availability Zone), CSP(Cloud Service Provider) 종속(Managed Service), 대체 가능성
2. 초급자 정의: 리전/존 장애는 “물리적으로 떨어진 인프라 단위”가 통째로 영향받는 재해 시나리오입니다. 매니지드 서비스에 깊게 의존하면 락인(lock-in)이 생겨 대체가 어려워집니다.
3. 실무 예시: 단일 리전에만 있으면 리전 장애에 서비스 중단 → 멀티 AZ + 백업/복제 + DR 목표 설정.
4. 추천 한글 URL:
- MS BCDR(한글): [https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery](https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery)

2.3.2 플랫폼 버전 및 업그레이드 전략 CDR

1. 용어: 업그레이드(Upgrade)/마이그레이션(Migration), 롤백(Rollback) 런북(Runbook), 호환성(Compatibility)
2. 초급자 정의: 업그레이드는 버전 변경, 마이그레이션은 플랫폼/환경 이전까지 포함한 변경입니다. 롤백은 실패 시 “되돌리는 절차”를 실행 가능한 단계로 문서화한 것입니다.
3. 실무 예시: 쿠버네티스 버전 업 → API deprecation 영향 분석표 + 롤백 Runbook(컨트롤플레인/노드 순서) 준비.
4. 추천 한글 URL:
- Kubernetes 버전 업그레이드(한글): [kubeadm 클러스터 업그레이드](https://kubernetes.io/ko/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)

2.3.3 환경 변경 시 영향도 분석 CDR

1. 용어: Change(변경통제), IaC(Infrastructure as Code), Drift(환경 드리프트)
2. 초급자 정의: Drift는 “코드(IaC)에 정의된 상태”와 “실제 인프라 상태”가 어긋난 것입니다. Drift 탐지는 임의 수동 변경/긴급 조치가 쌓여 재현성·보안을 깨뜨리는 문제를 막습니다.
3. 실무 예시: 운영자가 콘솔에서 SG를 임시로 열었는데 IaC에 반영 안 됨 → 드리프트 탐지 도구가 경고, PR로 IaC 반영 또는 원복.
4. 추천 한글 URL:
- IaC Drift 개념(한글 기사): [https://www.hashicorp.com/blog/drift-detection-for-terraform](https://www.hashicorp.com/blog/drift-detection-for-terraform) (※ 한국어 페이지/번역 제공 여부는 접속 시 UI 언어 확인 필요)

3장 서비스 흐름 및 연계 구조 설계 CDR

3.1.1 End-to-End 처리 흐름 정의 CDR

1. 용어: End-to-End(E2E), Call Flow, Timeout/Retry(예외 플로우)
2. 초급자 정의: E2E Call Flow는 사용자→채널→서버→DB/외부연계까지 “요청 1건의 전체 흐름”을 도식화한 것입니다. 예외 플로우는 정상뿐 아니라 타임아웃/재시도/실패 시 경로까지 포함합니다.
3. 실무 예시: 결제 승인 요청이 외부 PG 타임아웃이면 1회 재시도 후 실패 응답 + DLQ/보상 처리로 이어지는 흐름을 같은 다이어그램에 표시.
4. 추천 한글 URL:
- 우아한형제들 분산추적(한글): [https://techblog.woowahan.com/2710/](https://techblog.woowahan.com/2710/)

3.1.2 외부 시스템 연계 구조 CDR

1. 용어: 프로토콜(Protocol), 스키마(Schema), Timeout/Retry, Fallback(우회/부분 기능)
2. 초급자 정의: 외부 연계는 계약(API 스펙) 자체가 설계의 핵심이며, 장애 시 “우리 서비스가 어떻게 버틸지(Fallback)”를 미리 정해야 합니다.
3. 실무 예시: 외부 배송조회 API 장애 시 “최근 조회결과 캐시 제공(부분 기능)”으로 UX를 유지.
4. 추천 한글 URL:
- SLO/SLI/SLA(한글): [https://www.whatap.io/ko/blog/35/](https://www.whatap.io/ko/blog/35/)

3.1.3 인증 및 인가 처리 흐름 CDR

1. 용어: 인증(Authentication), 인가(Authorization), 토큰 발급/검증/갱신/폐기, OAuth2(Open Authorization 2.0), OIDC(OpenID Connect)
2. 초급자 정의: 인증은 “누구인지 확인”, 인가는 “무엇을 할 권한이 있는지 확인”입니다. OAuth2는 인가 프레임워크, OIDC는 OAuth2 위에 “인증”을 표준화한 확장입니다.
3. 실무 예시: 모바일 앱 로그인=OIDC로 ID 토큰(JWT) 발급, API 호출=Access 토큰 검증, 만료 시 Refresh로 갱신.
4. 추천 한글 URL:
- OAuth2 vs OIDC(한글): [브런치](https://brunch.co.kr/@isacc/24)
- OAuth 2.0/OIDC(한글 정리): [https://velog.io/@ksh940925/OAuth-2.0-개념](https://velog.io/@ksh940925/OAuth-2.0-%EA%B0%9C%EB%85%90)

3.1.4 트랜잭션 경계 설정 CDR

1. 용어: 트랜잭션(Transaction) 경계, ACID(Atomicity, Consistency, Isolation, Durability), 비동기(Async) 분리, 분산 트랜잭션(Distributed Transaction), Saga(사가) 패턴(보상 트랜잭션)
2. 초급자 정의: 트랜잭션 경계는 “원자적으로 묶어야 하는 구간”을 정하는 것이고, 마이크로서비스에서는 분산 트랜잭션을 피하고 Saga로 보상 동작을 설계합니다.
3. 실무 예시: 주문 생성 후 결제 실패 시 “주문 취소(보상 트랜잭션)” 이벤트 발행.
4. 추천 한글 URL:
- Saga 패턴(AWS 한글): [https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/modernization-data-persistence/saga-pattern.html](https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/modernization-data-persistence/saga-pattern.html)
- Saga 패턴(한글 블로그): [https://velog.io/@hax0r/saga-pattern](https://velog.io/@hax0r/saga-pattern)

3.2.1 장애 발생 가능 지점 분석 CDR

1. 용어: SPOF(Single Point of Failure), Failure Mode(응답지연/오류/부분장애)
2. 초급자 정의: SPOF는 단일 장애점이고, Failure Mode는 컴포넌트가 “어떤 방식으로 망가질 수 있는지”를 유형화한 표입니다.
3. 실무 예시: 단일 Redis 세션 저장은 SPOF → 클러스터/대체 저장소/Stateless로 완화.
4. 추천 한글 URL:
- BCDR 개념(한글): [https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery](https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery)

3.2.2 장애 확산 경로 정의 CDR

1. 용어: 장애 전파(Propagation), 격리(Isolation), Circuit Breaker(서킷 브레이커), Bulkhead(벌크헤드), Rate Limit(레이트 리밋)
2. 초급자 정의: 서킷브레이커는 “상대가 죽었을 때 더 호출하지 않고 빠르게 실패”하게 하고, 벌크헤드는 자원 격리로 연쇄 장애를 막습니다. 레이트리밋은 요청 상한으로 폭주를 차단합니다.
3. 실무 예시: 외부 결제 API 지연이 길어지면 CB Open → 즉시 우회/차단, 내부 스레드풀 고갈 방지(벌크헤드).
4. 추천 한글 URL:
- 무중단 배포(롤링/블루그린/카나리): [https://kylo8.tistory.com/entry/무중단-배포-전략-이해하기-롤링-배포-블루그린-배포-카나리-배포](https://kylo8.tistory.com/entry/%EB%AC%B4%EC%A4%91%EB%8B%A8-%EB%B0%B0%ED%8F%AC-%EC%A0%84%EB%9E%B5-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EB%A1%A4%EB%A7%81-%EB%B0%B0%ED%8F%AC-%EB%B8%94%EB%A3%A8%EA%B7%B8%EB%A6%B0-%EB%B0%B0%ED%8F%AC-%EC%B9%B4%EB%82%98%EB%A6%AC-%EB%B0%B0%ED%8F%AC)

3.2.3 동기·비동기 처리 구분 CDR

1. 용어: 동기(Sync)/비동기(Async), 사후정합(Eventual Consistency)
2. 초급자 정의: 동기는 사용자가 기다리는 흐름, 비동기는 “일단 접수 후 뒤에서 처리”입니다. 비동기는 실패 시 재처리/정합성 회복 정책이 필수입니다.
3. 실무 예시: 결제는 동기(즉시 결과 필요), 영수증 발송은 비동기(MQ로 발행, 실패 시 재처리).
4. 추천 한글 URL:
- DLQ(Dead Letter Queue) 개념(한글): [https://aws.amazon.com/ko/what-is/dead-letter-queue/](https://aws.amazon.com/ko/what-is/dead-letter-queue/)

3.2.4 사용자 영향도 평가 CDR

1. 용어: UX(User Experience) 오류 메시지 정책, CS(Customer Support) 트리거, Degraded Mode(최소 기능 모드)
2. 초급자 정의: Degraded Mode는 장애 시 “완전 실패” 대신 기능을 줄여 서비스 핵심만 유지하는 전략입니다. 고객 공지/CS 트리거는 언제 공지할지 기준입니다.
3. 실무 예시: 추천 기능 장애 시 추천만 OFF(기본 목록은 제공), 결제 장애 시 “장애 안내+재시도 버튼+대체 결제수단” 노출.
4. 추천 한글 URL:
- SLO 기반 운영(한글): [https://bcho.tistory.com/1332](https://bcho.tistory.com/1332)

4장 응용 소프트웨어 구조 설계 CDR

4.1.1 아키텍처 유형 정의 CDR

1. 용어: 모놀리식(Monolithic)/모듈러(Modular)/마이크로서비스(Microservices), ADR(Architecture Decision Record), 도메인/데이터 경계(Bounded Context)
2. 초급자 정의: ADR은 “왜 이 구조를 선택했는지” 남기는 설계 결정 기록입니다. 도메인/데이터 경계는 서비스 분해 기준입니다.
3. 실무 예시: “마이크로서비스 채택(팀 독립 배포 필요)”을 ADR로 남기고, 주문/결제 데이터 경계로 분해.
4. 추천 한글 URL:
- MSA 개요(한글): [마이크로서비스(Microservice) 정의, 구축, 장단점, 사례](https://www.redhat.com/ko/topics/microservices/what-are-microservices)

4.1.2 모듈 구성 및 책임 분리 CDR

1. 용어: 모듈 책임(Responsibility), 순환 의존(Cyclic Dependency), 결합도(Coupling), 정적분석(Static Analysis)
2. 초급자 정의: 순환 의존은 A가 B를, B가 A를 참조해 변경이 어려워지는 구조이고 결합도는 서로 얽힌 정도입니다. 정적분석은 코드 실행 없이 의존성을 분석합니다.
3. 실무 예시: 공통 유틸이 도메인 모듈을 참조해 순환 발생 → 인터페이스/포트 분리로 끊음.
4. 추천 한글 URL:
- SonarQube 정적분석(한글): [Code Quality, Security & Static Analysis Tool with SonarQube](https://www.sonarsource.com/products/sonarqube/) (※ 한국어 UI 제공 여부는 접속 시 확인)

4.1.3 핵심 기능 처리 흐름도 CDR

1. 용어: 상태머신(State Machine), 정책(Policy), 경계조건(Edge Case), Flow chart
2. 초급자 정의: 상태머신은 상태 전이 규칙을 가진 로직이고, 경계조건은 입력이 비정상/극단일 때 깨지기 쉬운 경우입니다.
3. 실무 예시: 주문 상태(PAID→SHIPPED→DONE) 전이 + 중복 결제/동시 클릭 경쟁 조건 처리 흐름을 도식화.
4. 추천 한글 URL:
- 멱등성(Idempotency) 개념(한글): [https://eddykang.tistory.com/entry/API-멱등성-Idempotency](https://eddykang.tistory.com/entry/API-%EB%A9%B1%EB%93%B1%EC%84%B1-Idempotency)

4.1.4 설계 복잡도 및 변경 용이성 평가 CDR

1. 용어: Blast Radius(영향 범위), Config(Configuration)/Feature Flag, Toil(토일)
2. 초급자 정의: Blast Radius는 변경 1건의 영향 범위입니다. Feature Flag는 배포와 기능 공개를 분리하고, Toil은 사람이 반복 수행하는 운영 잡무입니다.
3. 실무 예시: 추천 알고리즘 변경은 일부 화면만 영향(Blast Radius 작음) vs 인증 모듈 변경은 전 서비스 영향(큼). 플래그로 점진 공개.
4. 추천 한글 URL:
- Feature Flag(한글): [https://tech.kakaopay.com/post/feature-flag/](https://tech.kakaopay.com/post/feature-flag/)
- 무중단 배포(한글): [https://kylo8.tistory.com/entry/무중단-배포-전략-이해하기-롤링-배포-블루그린-배포-카나리-배포](https://kylo8.tistory.com/entry/%EB%AC%B4%EC%A4%91%EB%8B%A8-%EB%B0%B0%ED%8F%AC-%EC%A0%84%EB%9E%B5-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EB%A1%A4%EB%A7%81-%EB%B0%B0%ED%8F%AC-%EB%B8%94%EB%A3%A8%EA%B7%B8%EB%A6%B0-%EB%B0%B0%ED%8F%AC-%EC%B9%B4%EB%82%98%EB%A6%AC-%EB%B0%B0%ED%8F%AC)

4.2.1 오류 처리 원칙 CDR

1. 용어: Error Handling(오류 처리), 로그/추적/알람 연계 기준
2. 초급자 정의: “어디서 예외를 잡고(Handling), 어디까지 전파하고, 사용자에게 무엇을 보여줄지”를 원칙으로 고정해야 운영이 흔들리지 않습니다.
3. 실무 예시: DB 타임아웃은 서비스 레이어에서 표준 오류코드로 변환, 사용자에겐 “잠시 후 재시도” 메시지, 내부는 알람+트레이스 링크.
4. 추천 한글 URL:
- 분산 추적/로그 설계(한글): [https://techblog.woowahan.com/2710/](https://techblog.woowahan.com/2710/)

4.2.2 오류 코드 체계 설계 CDR

1. 용어: Error Code Spec(오류 코드 규격), 네임스페이스(Namespace), HTTP 매핑, 런북(Runbook) 매핑
2. 초급자 정의: 오류 코드는 “분류 규칙+형식+HTTP 상태 매핑”이 있어야 검색/대응이 됩니다. 런북 매핑은 “이 코드면 이 절차”로 연결하는 것입니다.
3. 실무 예시: PAY-EXT-504(외부PG 타임아웃) → 런북에서 “재시도/서킷브레이커 상태 확인/벤더 연락” 절차로 바로 점프.
4. 추천 한글 URL:
- (사내 표준 Error Code + Runbook 링크가 가장 강력한 증적입니다)

4.2.3 재시도·타임아웃·차단기 전략 CDR

1. 용어: Timeout(타임아웃) 표, Retry(재시도) (Backoff/지수 백오프), Idempotency(멱등성), Circuit Breaker(CB), Rate Limit
2. 초급자 정의: 타임아웃은 “얼마나 기다릴지”, 재시도는 “몇 번/어떤 간격(백오프)”으로 할지이며 멱등성이 없으면 재시도가 중복결제 같은 사고를 만듭니다. CB는 연쇄 장애를 막기 위한 자동 차단 장치입니다.
3. 실무 예시: 결제 승인 호출은 2초 타임아웃+지수 백오프 1회 재시도, 멱등키 필수, CB 오픈 시 즉시 실패+대체 안내.
4. 추천 한글 URL:
- Saga 패턴(AWS 한글): [https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/modernization-data-persistence/saga-pattern.html](https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/modernization-data-persistence/saga-pattern.html)
- 멱등성(Idempotency) 개념(한글): [https://eddykang.tistory.com/entry/API-멱등성-Idempotency](https://eddykang.tistory.com/entry/API-%EB%A9%B1%EB%93%B1%EC%84%B1-Idempotency)

4.2.4 장애 격리 및 복구 구조 CDR

1. 용어: Bulkhead(벌크헤드), 큐 분리(Queue Isolation), 자동 복구(Auto Recovery) (재기동/헬스체크/재처리), 런북(Runbook)
2. 초급자 정의: 격리는 “한 곳이 죽어도 다른 곳이 같이 죽지 않게” 자원/큐/스레드풀을 분리하는 것입니다. 자동 복구는 헬스체크 기반 재기동/재처리까지 포함합니다.
3. 실무 예시: 이미지 처리 워커가 폭주해도 API 서버 스레드풀이 고갈되지 않도록 큐/워커풀 분리.
4. 추천 한글 URL:
- Kubernetes HPA(한글): [https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/](https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/)

4.3.1 메시지 기반 처리 도입 목적 CDR

1. 용어: MQ(Message Queue), Queue, 버퍼링(Buffering), 격리(Isolation), 지연 허용(Latency Tolerance), ADR(Architecture Decision Record)
2. 초급자 정의: MQ/Queue는 요청을 “줄 세워” 처리량 급증을 완충(버퍼링)하고 장애를 격리합니다. 도입 이유를 ADR로 남겨야 남용을 막습니다.
3. 실무 예시: 이메일 발송을 동기 처리하면 외부 SMTP 지연이 사용자 UX를 망침 → 큐로 비동기화.
4. 추천 한글 URL:
- DLQ(Dead Letter Queue) 개념(한글): [https://aws.amazon.com/ko/what-is/dead-letter-queue/](https://aws.amazon.com/ko/what-is/dead-letter-queue/)

4.3.2 전달 보장 수준 정의 CDR

1. 용어: At-least-once(최소 1회 전달), 중복 가능성(Duplicate), Producer/Consumer Contract
2. 초급자 정의: At-least-once는 “최소 1번은 전달되지만 중복될 수 있음”이라 소비자는 중복 처리 방지가 필요합니다. Contract는 생산자/소비자 간 스키마·규칙 약속입니다.
3. 실무 예시: 주문 이벤트가 중복 수신될 수 있으니, 소비자는 멱등키로 중복 업데이트를 무시.
4. 추천 한글 URL:
- 멱등성(Idempotency) 개념(한글): [https://eddykang.tistory.com/entry/API-멱등성-Idempotency](https://eddykang.tistory.com/entry/API-%EB%A9%B1%EB%93%B1%EC%84%B1-Idempotency)

4.3.3 메시지 순서 보장 정책 CDR

1. 용어: Ordering(순서 보장), 파티셔닝(Partitioning), 키(Key) 전략
2. 초급자 정의: 순서 보장은 “같은 키의 메시지는 순서대로 처리” 같은 제약이 필요할 때 사용합니다. 파티션 키를 잘못 잡으면 순서가 깨지거나 병목이 생깁니다.
3. 실무 예시: order_id를 키로 잡아 같은 주문의 상태 이벤트는 순서대로 처리되게 함.
4. 추천 한글 URL:
- Kafka 파티션/키(한글): [데브원영](https://blog.voidmainvoid.net/) (※ 특정 글 링크는 사내 표준에 맞춰 지정 권장)

4.3.4 중복 처리 방지 전략 CDR

1. 용어: Idempotency(멱등성) 키, TTL(Time To Live), DLQ(Dead Letter Queue), Poison 메시지
2. 초급자 정의: 멱등성은 같은 요청을 여러 번 받아도 결과가 1번 수행과 같게 만드는 성질입니다. DLQ는 계속 실패하는(독이 든) 메시지를 격리하는 큐입니다.
3. 실무 예시: 결제 요청에 idempotency_key를 넣고, 처리 완료 키는 24시간 보관. 3회 실패한 메시지는 DLQ로 이동.
4. 추천 한글 URL:
- DLQ(Dead Letter Queue) 개념(한글): [https://aws.amazon.com/ko/what-is/dead-letter-queue/](https://aws.amazon.com/ko/what-is/dead-letter-queue/)
- 멱등성(Idempotency) 개념(한글): [https://eddykang.tistory.com/entry/API-멱등성-Idempotency](https://eddykang.tistory.com/entry/API-%EB%A9%B1%EB%93%B1%EC%84%B1-Idempotency)

5장 데이터 구조 및 상태 관리 설계 CDR

5.1.1 논리 데이터 모델 설계 CDR

1. 용어: 엔터티(Entity)/관계(Relationship), 제약(Constraint), ERD(Entity Relationship Diagram) (논리), 데이터 소유권(Data Ownership)
2. 초급자 정의: 논리 ERD는 “업무 관점의 데이터 구조(무엇이 어떤 관계인지)”를 그린 것이고, 데이터 소유권은 도메인별 책임(누가 변경 권한을 갖는지)을 정합니다.
3. 실무 예시: 주문 도메인은 Order, OrderItem 엔터티를 소유하고 결제 도메인은 Payment를 소유(교차 변경 시 변경통제).
4. 추천 한글 URL:
- ERD 기초(한글): [ERDCloud](https://www.erdcloud.com/) (※ 학습자료/가이드 메뉴 참고)

5.1.2 물리 데이터 구조 설계 CDR

1. 용어: 테이블(Table)/인덱스(Index), 파티션(Partition), 샤딩(Sharding), 아카이빙(Archiving), 데이터 수명주기(Data Lifecycle)
2. 초급자 정의: 물리 설계는 실제 DB 객체(테이블/인덱스/파티션)를 어떻게 만들지이며, 샤딩/파티션은 대용량 성능/확장 목적의 분할입니다. 데이터 수명주기는 보관→아카이빙→파기 규칙입니다.
3. 실무 예시: 주문 테이블 월별 파티션 + 최근 6개월은 hot, 이후는 archive 스토리지로 이전.
4. 추천 한글 URL:
- PostgreSQL 파티셔닝(한글): [PostgreSQL 공식 한글 설명서들: 한국 포스트그레스큐엘 홈페이지](https://postgresql.kr/docs/) (※ 파티셔닝 항목)

5.1.3 데이터 무결성 및 정합성 규칙 CDR

1. 용어: 무결성(Integrity) (키/유니크/참조), 정합성(Consistency), 동시성(Concurrency)/경합(Contention), 락(Lock), 낙관적 락(Optimistic Lock)
2. 초급자 정의: 무결성은 DB 제약으로 “깨지지 않게” 보장하는 것이고, 동시성 전략은 동시에 수정할 때 충돌을 어떻게 다룰지입니다. 낙관적 락은 버전으로 충돌을 감지해 재시도합니다.
3. 실무 예시: 재고 차감은 낙관적 락 버전이 안 맞으면 재조회 후 재시도.
4. 추천 한글 URL:
- 낙관적 락(한글): [[데이터베이스/JPA] 낙관적 락, 비관적 락이란? 예시를 통해 쉽게 알아보자](https://hstory0208.tistory.com/entry/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4JPA-%EB%82%99%EA%B4%80%EC%A0%81-%EB%9D%BD-%EB%B9%84%EA%B4%80%EC%A0%81-%EB%9D%BD%EC%9D%B4%EB%9E%80-%EC%98%88%EC%8B%9C%EB%A5%BC-%ED%86%B5%ED%95%B4-%EC%89%BD%EA%B2%8C-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)

5.1.4 트랜잭션 설계 기준 CDR

1. 용어: 격리 수준(Isolation Level), 일관성 요구, 장시간 트랜잭션(Long Transaction) 회피, 보상(Saga)
2. 초급자 정의: 격리 수준은 동시에 접근할 때 “어디까지 보장할지”의 레벨이고, 장시간 트랜잭션은 락으로 병목을 만들기 쉬워 회피/보상이 필요합니다.
3. 실무 예시: 결제 승인과 배송 요청을 하나의 긴 트랜잭션으로 묶지 않고, 사가로 단계별 확정/보상.
4. 추천 한글 URL:
- Saga 패턴(AWS 한글): [https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/modernization-data-persistence/saga-pattern.html](https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/modernization-data-persistence/saga-pattern.html)

5.2.1 데이터 이관 전략 CDR

1. 용어: 온라인/오프라인 이관, 이중쓰기(Dual-write), 백필(Backfill), Migration Plan(이관 계획)
2. 초급자 정의: 이중쓰기는 구/신 DB에 동시에 쓰는 방식이고, 백필은 과거 데이터를 신시스템에 채워 넣는 작업입니다. Migration Plan은 단계/리스크/롤백까지 포함한 이관 계획서입니다.
3. 실무 예시: 1주간 이중쓰기 후 신DB 기준으로 조회 전환, 과거 2년 데이터는 배치 백필로 채움.
4. 추천 한글 URL:
- 데이터 마이그레이션 개요(한글): [마이그레이션 계획 - Cloud Adoption Framework](https://learn.microsoft.com/ko-kr/azure/cloud-adoption-framework/migrate/)

5.2.2 전환 검증 절차 CDR

1. 용어: 정합성 검증(샘플/전수), 합격 기준(Pass Criteria), 승인자(RACI)
2. 초급자 정의: 전환 검증은 전/후 데이터가 맞는지(정합성)를 확인하는 절차이며, 샘플/전수 범위와 합격 기준이 필수입니다. 승인자 지정은 책임소재를 고정합니다.
3. 실무 예시: “주문 건수/합계금액/상태별 분포”를 전수 비교, 불일치 0.01% 이하 합격.
4. 추천 한글 URL:
- RACI: [https://www.atlassian.com/ko/team-playbook/plays/raci-matrix](https://www.atlassian.com/ko/team-playbook/plays/raci-matrix)

5.2.3 롤백 가능성 분석 CDR

1. 용어: Rollback Decision Tree, 롤백 가능/불가 조건, 데이터 되돌림
2. 초급자 정의: 롤백 가능성은 “어느 시점까지 되돌릴 수 있는지”를 조건으로 정의하는 것이고, Decision Tree는 조건별 선택지를 나무 형태로 정리한 것입니다.
3. 실무 예시: “신DB에 쓰기 시작 전=즉시 롤백 가능 / 이후=이중쓰기 종료까지는 롤백 불가(데이터 충돌)”
4. 추천 한글 URL:
- 무중단 배포(롤백 맥락): [https://kylo8.tistory.com/entry/무중단-배포-전략-이해하기-롤링-배포-블루그린-배포-카나리-배포](https://kylo8.tistory.com/entry/%EB%AC%B4%EC%A4%91%EB%8B%A8-%EB%B0%B0%ED%8F%AC-%EC%A0%84%EB%9E%B5-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EB%A1%A4%EB%A7%81-%EB%B0%B0%ED%8F%AC-%EB%B8%94%EB%A3%A8%EA%B7%B8%EB%A6%B0-%EB%B0%B0%ED%8F%AC-%EC%B9%B4%EB%82%98%EB%A6%AC-%EB%B0%B0%ED%8F%AC)

5.2.4 데이터 손상 시 복구 방안 CDR

1. 용어: Backup/Restore Runbook, PITR(Point In Time Recovery), 손상 탐지(Detection)
2. 초급자 정의: PITR은 특정 시점으로 복구하는 방식이고, Runbook은 복구를 “명령/시간/담당” 수준으로 실행 가능하게 만든 절차서입니다. 손상 탐지는 무결성 체크/샘플 검증으로 조기 발견합니다.
3. 실무 예시: 야간 배치 후 “주문 합계 = 결제 합계” 검증 쿼리 알람, 손상 감지 시 PITR 수행.
4. 추천 한글 URL:
- MS BCDR(한글): [https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery](https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery)

6장 모니터링 및 성과 측정 설계 CDR

6.1.1 로그 구조 및 수집 체계 CDR

1. 용어: 구조화 로그(Structured Logging), trace_id, error_code, 마스킹(Masking)
2. 초급자 정의: 구조화 로그는 JSON처럼 필드를 고정해 검색/집계가 쉬운 로그입니다. 마스킹은 개인정보를 로그에 남기지 않도록 가리는 규칙입니다.
3. 실무 예시: 모든 요청 로그에 trace_id, user_id(해시), error_code를 포함하고, 주민번호/카드번호는 저장 전 마스킹.
4. 추천 한글 URL:
- 우아한형제들 로깅/추적(한글): [https://techblog.woowahan.com/2710/](https://techblog.woowahan.com/2710/)

6.1.2 성능 지표(Metric) 정의 CDR

1. 용어: Metric(메트릭), RED/USE, SLI(Service Level Indicator)
2. 초급자 정의: Metric은 수치 지표이고, RED/USE는 핵심 지표 묶음(요청/오류/지연 또는 자원 사용/포화/오류)입니다. SLI는 SLO를 계산하는 실제 측정 지표입니다.
3. 실무 예시: API는 RED(요청수, 오류율, p95 지연), 인프라는 USE(CPU 사용률/포화/에러)로 대시보드 표준화.
4. 추천 한글 URL:
- SLI/SLO/SLA(한글): [https://www.whatap.io/ko/blog/35/](https://www.whatap.io/ko/blog/35/)

6.1.3 추적(Trace) 체계 설계 CDR

1. 용어: 분산 트레이싱(Distributed Tracing), trace/span, 샘플링(Sampling)
2. 초급자 정의: 트레이스는 요청 1건의 전체 여정, 스팬은 그 안의 구간(서비스 호출 단위)입니다. 샘플링은 비용 때문에 일부만 수집하는 정책입니다.
3. 실무 예시: 결제 오류는 100% 트레이싱, 정상 트래픽은 1% 샘플링.
4. 추천 한글 URL:
- 우아한형제들 트레이싱(한글): [https://techblog.woowahan.com/2710/](https://techblog.woowahan.com/2710/)

6.1.4 경보(Alert) 기준 정의 CDR

1. 용어: Alert(알람) 룰, 임계치(Threshold), 에스컬레이션(Escalation), 알람 노이즈(Noise) 억제
2. 초급자 정의: 알람은 “언제 울려야 하는지(임계치) + 누가 받는지(온콜/에스컬레이션)”가 세트입니다. 노이즈 억제는 오탐을 줄여 “진짜 알람을 놓치지 않게” 하는 운영 기술입니다.
3. 실무 예시: “p95 300ms 초과 5분 지속”이면 Sev3 알람, “오류율 5% 초과”는 Sev2로 IC 호출.
4. 추천 한글 URL:
- SLO 기반 운영(한글): [https://bcho.tistory.com/1332](https://bcho.tistory.com/1332)

6.2.1 서비스 성과 지표 설계 CDR

1. 용어: KPI(Key Performance Indicator)↔Event 매핑
2. 초급자 정의: KPI가 어떤 이벤트/필드로 계산되는지 정하지 않으면, KPI는 “보고용 문구”로 끝납니다.
3. 실무 예시: “재구매율”은 purchase_completed 이벤트의 user_id로 Cohort 계산.
4. 추천 한글 URL:
- 이벤트/로그 설계(한글): [https://techblog.woowahan.com/2710/](https://techblog.woowahan.com/2710/)

6.2.2 이벤트 데이터 수집 기준 CDR

1. 용어: 이벤트 스키마(Event Schema), 버전/호환성(Compatibility), 유실(Loss) 처리
2. 초급자 정의: 이벤트 스키마는 이벤트의 “필드 규격”이고, 버전/호환성은 필드 추가·변경이 구독자(분석/서비스)에 깨지지 않게 하는 규칙입니다. 유실 처리 정책은 이벤트가 빠졌을 때 어떻게 복구/보정할지 정하는 것입니다.
3. 실무 예시: event_version=2로 필드 추가, 구버전 소비자는 무시 가능하게 설계. 유실 시 재전송(backfill) 배치 운영.
4. 추천 한글 URL:
- (조직의 데이터 파이프라인/스키마 레지스트리 운영 가이드가 핵심)

6.2.3 분석 데이터 항목 정의 CDR

1. 용어: 개인정보(PII, Personally Identifiable Information)/민감정보, 데이터 분류표
2. 초급자 정의: 분석 필드는 “수집 목적”이 있어야 하며 개인정보/민감정보와 충돌하지 않도록 분류·검토 기록이 필요합니다.
3. 실무 예시: 사용자 식별은 원문이 아닌 해시/대체키로 수집, 민감 필드는 원천 차단.
4. 추천 한글 URL:
- RBAC(한글): [https://www.redhat.com/ko/topics/security/what-is-role-based-access-control](https://www.redhat.com/ko/topics/security/what-is-role-based-access-control)

6.2.4 운영 대시보드 구성 CDR

1. 용어: SLO/KPI 통합 대시보드
2. 초급자 정의: SLO는 “신뢰성”, KPI는 “비즈니스 성과”라서 둘이 한 화면에서 같이 보여야 운영이 성과와 분리되지 않습니다.
3. 실무 예시: 결제 대시보드 상단=가용성/오류율(SLO), 하단=결제 성공액/KPI.
4. 추천 한글 URL:
- SLI/SLO/SLA(한글): [https://www.whatap.io/ko/blog/35/](https://www.whatap.io/ko/blog/35/)

7장 정보보호 및 준법 설계 CDR

7.1.1 인증 구조 설계 CDR

1. 용어: OAuth2(Open Authorization 2.0)/OIDC(OpenID Connect), mTLS(Mutual TLS), API Key, 토큰 수명/갱신/폐기, 탈취 대응
2. 초급자 정의: OAuth2/OIDC는 토큰 기반 인증/인가 표준이고, mTLS는 서버와 클라이언트가 서로 인증서로 상호 인증하는 방식입니다. 토큰 정책은 만료/회전/폐기까지 포함해야 합니다.
3. 실무 예시: 내부 마이크로서비스 간 호출은 mTLS, 외부 공개 API는 OIDC+Access Token, 토큰 탈취 의심 시 즉시 폐기/재발급.
4. 추천 한글 URL:
- OAuth2 vs OIDC(한글): [브런치](https://brunch.co.kr/@isacc/24)
- mTLS(한글): [mTLS란? | 양방향 TLS | Cloudflare](https://www.cloudflare.com/ko-kr/learning/access-management/what-is-mutual-tls/)

7.1.2 권한 관리 체계 CDR

1. 용어: RBAC(Role-Based Access Control), ABAC(Attribute-Based Access Control), PoLP(Principle of Least Privilege, 최소권한)
2. 초급자 정의: RBAC는 역할 기반, ABAC는 속성(부서/등급/시간/리소스 속성) 기반 권한입니다. 최소권한은 업무에 필요한 권한만 주는 원칙입니다.
3. 실무 예시: 운영자 역할은 “조회/재처리”만, DB 직접 접근은 Break-glass 절차로 제한. ABAC로 “야간에는 승인자만” 같은 조건 부여.
4. 추천 한글 URL:
- RBAC(한글): [https://www.redhat.com/ko/topics/security/what-is-role-based-access-control](https://www.redhat.com/ko/topics/security/what-is-role-based-access-control)
- 최소 권한(PoLP) 개념(한글): [https://www.cloudflare.com/ko-kr/learning/security/glossary/principle-of-least-privilege/](https://www.cloudflare.com/ko-kr/learning/security/glossary/principle-of-least-privilege/)

7.1.3 감사 로그 설계 CDR

1. 용어: Audit Log(감사로그), 무결성(변조 방지), 보관 정책(Retention)
2. 초급자 정의: 감사로그는 “누가/언제/무엇을 했는지”를 법적·보안적으로 추적 가능한 로그이며, 변조 방지와 보관기간이 필수입니다.
3. 실무 예시: 권한 변경/데이터 조회/설정 변경을 감사 이벤트로 남기고 WORM 스토리지에 보관.
4. 추천 한글 URL:
- PoLP(한글): [https://www.cloudflare.com/ko-kr/learning/security/glossary/principle-of-least-privilege/](https://www.cloudflare.com/ko-kr/learning/security/glossary/principle-of-least-privilege/)

7.2.1 개인정보 분류 및 처리 기준 CDR

1. 용어: 개인정보/민감정보/일반정보 분류, 수집 최소화, 목적 제한
2. 초급자 정의: 데이터 분류는 “어떤 규정/통제 수준”을 적용할지 결정하는 기준입니다. 수집 최소화/목적 제한은 필요한 데이터만, 정해진 목적 내에서만 쓰는 원칙입니다.
3. 실무 예시: 주민번호 수집 금지, 나이대만 필요하면 생년월일 대신 연령대(파생 데이터) 저장.
4. 추천 한글 URL:
- (조직 규정/법무 가이드가 최우선)

7.2.2 암호화 정책 CDR

1. 용어: 전송/저장 암호화, KMS(Key Management Service), 키 회전(Rotation), Secret 관리(회전/노출 방지)
2. 초급자 정의: KMS는 암호화 키를 중앙에서 안전하게 생성/보관/회전하는 서비스입니다. Secret 회전은 노출 가능성을 줄이기 위해 주기적으로 키/비밀번호를 바꾸는 운영입니다.
3. 실무 예시: DB는 저장 암호화 + KMS 키 연 1회 자동 회전, API 토큰은 90일 주기로 교체.
4. 추천 한글 URL:
- AWS KMS(한글): [AWS Key Management Service - AWS Key Management Service](https://docs.aws.amazon.com/ko_kr/kms/latest/developerguide/overview.html)
- Ncloud KMS(한글): [Key Management Service 개요](https://guide.ncloud-docs.com/docs/kms-kms-1-1)

7.2.3 데이터 보존 및 파기 정책 CDR

1. 용어: 보존 기간(Retention Period), 파기(Deletion/Disposal) 절차, 백업 동일 정책
2. 초급자 정의: 보존/파기는 “원본뿐 아니라 백업/로그/아카이브에도 동일하게” 적용되어야 합니다.
3. 실무 예시: 개인정보는 1년 보존 후 파기, 백업 스냅샷도 동일 기간 이후 자동 삭제.
4. 추천 한글 URL:
- (조직 규정/법무 가이드가 최우선)

8장 배포 및 변경 통제 설계 CDR

8.1.1 배포 유형 정의 CDR

1. 용어: 블루그린(Blue-Green)/카나리(Canary)/롤링(Rolling)/빅뱅(Big-bang) 배포, 자동 중단(Abort), 자동 롤백(Auto Rollback)
2. 초급자 정의: 롤링은 순차 교체, 블루그린은 구/신 두 환경 스위칭, 카나리는 일부 트래픽으로 신버전 검증입니다. 자동 중단/롤백은 지표 기반으로 배포를 자동으로 멈추거나 되돌리는 장치입니다.
3. 실무 예시: 카나리 5%에서 오류율 2% 초과 시 자동 중단→구버전 유지.
4. 추천 한글 URL:
- 무중단 배포(한글): [https://kylo8.tistory.com/entry/무중단-배포-전략-이해하기-롤링-배포-블루그린-배포-카나리-배포](https://kylo8.tistory.com/entry/%EB%AC%B4%EC%A4%91%EB%8B%A8-%EB%B0%B0%ED%8F%AC-%EC%A0%84%EB%9E%B5-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EB%A1%A4%EB%A7%81-%EB%B0%B0%ED%8F%AC-%EB%B8%94%EB%A3%A8%EA%B7%B8%EB%A6%B0-%EB%B0%B0%ED%8F%AC-%EC%B9%B4%EB%82%98%EB%A6%AC-%EB%B0%B0%ED%8F%AC)
- Blue/Green/Canary/Feature Flag(한글): [https://jojoldu.tistory.com/627](https://jojoldu.tistory.com/627)

8.1.2 전환(Cutover) 전략 CDR

1. 용어: Cutover(전환), Go/No-Go 기준
2. 초급자 정의: Cutover는 트래픽/기능 활성화가 실제로 “넘어가는 순간”이며, Go/No-Go는 전환 중 계속 진행할지 중단할지 판단하는 기준입니다.
3. 실무 예시: 전환 10분 동안 오류율/지연/결제 성공률이 임계치 내면 Go, 초과면 No-Go로 즉시 롤백.
4. 추천 한글 URL:
- 무중단 배포(전환/롤백 맥락): [https://kylo8.tistory.com/entry/무중단-배포-전략-이해하기-롤링-배포-블루그린-배포-카나리-배포](https://kylo8.tistory.com/entry/%EB%AC%B4%EC%A4%91%EB%8B%A8-%EB%B0%B0%ED%8F%AC-%EC%A0%84%EB%9E%B5-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EB%A1%A4%EB%A7%81-%EB%B0%B0%ED%8F%AC-%EB%B8%94%EB%A3%A8%EA%B7%B8%EB%A6%B0-%EB%B0%B0%ED%8F%AC-%EC%B9%B4%EB%82%98%EB%A6%AC-%EB%B0%B0%ED%8F%AC)

8.1.3 기능 제어(Feature Flag) 전략 CDR

1. 용어: Feature Flag, 소유자/권한, 플래그 로깅/관측
2. 초급자 정의: Feature Flag는 “배포는 했지만 기능은 꺼두는” 스위치입니다. 플래그가 언제/누가 바꿨는지와 상태가 로그/메트릭에 남아야 사고를 줄입니다.
3. 실무 예시: 새 추천 기능을 내부 사용자만 ON, 이상 징후 시 즉시 OFF(킬스위치).
4. 추천 한글 URL:
- Feature Flag(한글): [https://tech.kakaopay.com/post/feature-flag/](https://tech.kakaopay.com/post/feature-flag/)
- Feature Flag 포함 릴리즈 전략(한글): [https://jojoldu.tistory.com/627](https://jojoldu.tistory.com/627)

8.2.1 데이터 전환 절차 CDR

1. 용어: 사전 백업(Pre-backup), 이관(Migration), 검증(Validation), 승인(Approval) (전환 게이트)
2. 초급자 정의: 데이터 전환은 “백업→이관→검증→승인”이 게이트로 고정돼야 하고, 누락되면 되돌릴 수 없는 사고가 납니다.
3. 실무 예시: 승인 전까지는 트래픽 전환 금지, 승인 시점이 Cutover 신호.
4. 추천 한글 URL:
- MS BCDR(한글): [https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery](https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery)

8.2.2 운영 인수인계 계획 CDR

1. 용어: 런북(Runbook), On-call, 모의 리허설(Drill)
2. 초급자 정의: 런북은 장애/운영 작업을 “누가/어떻게/무엇을 확인”까지 실행 가능하게 만든 문서입니다. 인수인계는 모니터링/알람/권한/연락망까지 포함합니다.
3. 실무 예시: 운영팀이 새 서비스 On-call을 서기 전, 장애 모의훈련 1회 이상.
4. 추천 한글 URL:
- RACI: [https://www.atlassian.com/ko/team-playbook/plays/raci-matrix](https://www.atlassian.com/ko/team-playbook/plays/raci-matrix)

8.2.3 롤백 전략 CDR

1. 용어: Rollback Runbook, Fail-safe Plan(안전장치 계획)
2. 초급자 정의: 롤백은 “되돌리는 것”이 아니라 되돌리는 절차가 실행 가능한가가 핵심입니다. Fail-safe는 롤백이 안 될 때 최소 피해로 버티는 대안입니다.
3. 실무 예시: DB 마이그레이션이 irreversible이면 “기능 차단/읽기전용 모드/대체 경로”를 Fail-safe로 둠.
4. 추천 한글 URL:
- 무중단 배포(롤백 맥락): [https://kylo8.tistory.com/entry/무중단-배포-전략-이해하기-롤링-배포-블루그린-배포-카나리-배포](https://kylo8.tistory.com/entry/%EB%AC%B4%EC%A4%91%EB%8B%A8-%EB%B0%B0%ED%8F%AC-%EC%A0%84%EB%9E%B5-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EB%A1%A4%EB%A7%81-%EB%B0%B0%ED%8F%AC-%EB%B8%94%EB%A3%A8%EA%B7%B8%EB%A6%B0-%EB%B0%B0%ED%8F%AC-%EC%B9%B4%EB%82%98%EB%A6%AC-%EB%B0%B0%ED%8F%AC)

8.2.4 비상 복구 및 최소 기능 운영 전략 CDR

1. 용어: Degraded Mode, 기능 축소/차단/대체, 고객 공지 트리거
2. 초급자 정의: Degraded Mode는 장애 시 핵심 기능만 살리고 나머지를 끄는 운영 모드입니다. 공지 트리거는 언제 고객/CS 커뮤니케이션을 시작할지 기준입니다.
3. 실무 예시: “검색 장애 시 인기목록으로 대체”, “결제 장애 시 주문 생성 자체 차단” + 공지 자동 게시.
4. 추천 한글 URL:
- SLO 기반 운영(한글): [https://bcho.tistory.com/1332](https://bcho.tistory.com/1332)

9장 비용 및 용량 관리 설계 CDR

9.1.1 예상 비용 산정 CDR

1. 용어: 컴퓨트(Compute)/스토리지(Storage)/네트워크(Network) 비용, 피크 비용 시나리오
2. 초급자 정의: 비용 산정은 “평균”만 보면 사고가 나고, 피크 트래픽 기준(최악 시나리오) 비용을 따로 봐야 합니다.
3. 실무 예시: 이벤트 기간(피크 10배)에는 오토스케일로 비용이 급증 → 사전 예산/알람 필요.
4. 추천 한글 URL:
- FinOps 개요(한글): [FinOps Foundation - What is FinOps?](https://www.finops.org/introduction/what-is-finops/) (※ 한국어 페이지 제공 여부는 접속 시 확인)

9.1.2 트래픽 증가에 따른 비용 변화 CDR

1. 용어: 비용 곡선(선형/비선형), 캐시/MQ/로그 비용 영향
2. 초급자 정의: 트래픽이 늘어도 비용이 선형으로만 늘지 않습니다(로그/네트워크/외부API 과금 등 비선형). 캐시/MQ/로그는 “성능을 올리지만 비용도 바꿉니다.”
3. 실무 예시: 로그 레벨이 DEBUG로 올라가면 저장/검색 비용이 폭발 → Guardrail 필요.
4. 추천 한글 URL:
- SLO 기반 운영(비용 가드레일과 연계): [https://bcho.tistory.com/1332](https://bcho.tistory.com/1332)

9.1.3 재해복구 포함 비용 분석 CDR

1. 용어: DR(Disaster Recovery) (이중화/백업/복제) 비용
2. 초급자 정의: DR은 신뢰성을 올리지만 항상 비용이 듭니다(복제 스토리지, 이중 인프라, 테스트 비용 포함).
3. 실무 예시: 멀티리전 복제는 네트워크 egress 비용이 커서, RPO 목표에 맞춰 주기/대상 데이터를 선별.
4. 추천 한글 URL:
- MS BCDR(한글): [https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery](https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery)

9.2.1 비용 한계(Guardrail) 설정 CDR

1. 용어: Guardrail, 쿼터(Quota), Rate Limit, 샘플링(Sampling), FinOps 알람/프로세스
2. 초급자 정의: Guardrail은 “비용 폭주를 막는 안전난간”으로 쿼터/레이트리밋/샘플링 같은 상한 정책입니다. FinOps는 비용 책임/승인/알람을 운영 프로세스로 만드는 것입니다.
3. 실무 예시: 외부 API 호출은 일일 쿼터 초과 시 차단+대체 응답, 로그는 샘플링 비율 상향 조정으로 비용 통제.
4. 추천 한글 URL:
- FinOps(소개): [FinOps Foundation - What is FinOps?](https://www.finops.org/introduction/what-is-finops/) (※ 한국어 페이지 제공 여부는 접속 시 확인)

9.2.2 용량 확장 통제 정책 CDR

1. 용어: 오토스케일 상한(Cap)
2. 초급자 정의: 오토스케일은 비용/장애 확산을 막기 위해 상한(캡)이 필수입니다.
3. 실무 예시: DDoS/버그로 트래픽이 폭주해도 파드가 무한히 늘지 않도록 max=200으로 캡.
4. 추천 한글 URL:
- Kubernetes HPA(한글): [https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/](https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/)

9.2.3 비용 책임 체계 정의 CDR

1. 용어: FinOps Owner, 승인 체계(RACI)
2. 초급자 정의: 비용도 “누가 최종 책임(A)”인지 명확해야 통제가 됩니다.
3. 실무 예시: 신규 캐시 도입으로 월 2천만원 증가 → FinOps Owner 승인 없이는 반영 불가.
4. 추천 한글 URL:
- RACI: [https://www.atlassian.com/ko/team-playbook/plays/raci-matrix](https://www.atlassian.com/ko/team-playbook/plays/raci-matrix)

10장 외부 의존성 및 공급자 리스크 관리 CDR

10.1.1 외부 API 및 SaaS 목록 CDR

1. 용어: Dependency Register(의존성 등록부), 쿼터(Quota), 요금, Rate Limit 초과 동작
2. 초급자 정의: Dependency Register는 외부 의존성을 “도메인/용도/중요도/쿼터/요금”으로 관리하는 대장입니다. Rate Limit 초과 시 차단/큐잉/부분 기능을 미리 정해야 합니다.
3. 실무 예시: 지도 API 쿼터 초과 시 “간소화 지도/이미지 대체”로 기능 축소.
4. 추천 한글 URL:
- SLI/SLO/SLA(Atlassian 한글): [https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas](https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas)

10.1.2 서비스 수준 계약 검토 CDR

1. 용어: SLA/SLO 충돌, 에스컬레이션, 보상(서비스 크레딧 등)
2. 초급자 정의: 외부 벤더 SLA가 우리 내부 SLO보다 낮으면, 벤더 장애가 곧 우리 SLO 위반으로 이어질 수 있어 충돌 분석이 필요합니다.
3. 실무 예시: 외부 결제 SLA 99.5%인데 우리는 99.9% 목표 → 캐시/대체 PG/우회로 보완.
4. 추천 한글 URL:
- SLI/SLO/SLA(Atlassian 한글): [https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas](https://www.atlassian.com/ko/incident-management/kpis/slis-slos-and-slas)

10.1.3 계약 조건과 설계 일치 여부 CDR

1. 용어: 데이터 처리/보관/책임(약관), 계약-설계 매핑
2. 초급자 정의: 계약에서 금지/의무인 항목(데이터 보관 위치, 삭제 의무 등)이 설계와 불일치하면 준법 리스크가 됩니다.
3. 실무 예시: “해외 리전 저장 금지” 조항이 있는데 로그를 해외 SaaS로 보내면 즉시 위반.
4. 추천 한글 URL:
- (조직 규정/법무 가이드가 최우선)

10.2.1 대체 전략(Exit Plan) CDR

1. 용어: Exit Plan, 전환 비용/기간
2. 초급자 정의: Exit Plan은 벤더를 못 쓰게 될 때 “대체/자체 구현/기능 축소”로 갈아타는 계획이며, 비용·기간 추정이 있어야 현실적입니다.
3. 실무 예시: 외부 검색 SaaS 장애 장기화 시 “기능 축소 검색 + 오픈소스 전환 3개월” 계획.
4. 추천 한글 URL:
- (조직 표준 “벤더 대체/전환” 템플릿이 유용)

10.2.2 공급자 장애 대응 전략 CDR

1. 용어: 차단/캐시/큐잉/재시도(벤더 장애 시나리오)
2. 초급자 정의: 벤더 장애는 “우리 서비스가 어떤 모드로 바뀔지”를 미리 정해야 연쇄 장애를 막습니다.
3. 실무 예시: 벤더 지연 시 CB 오픈+캐시 응답, 메시지는 큐잉 후 재처리.
4. 추천 한글 URL:
- Circuit Breaker 개념(한글): [외부 API 장애 대응방법 – 타임아웃, 벌크헤드, 서킷 브레이커](https://velog.io/@sihun23/%EC%99%B8%EB%B6%80-API-%EC%9E%A5%EC%95%A0-%EB%8C%80%EC%9D%91%EB%B0%A9%EB%B2%95-%ED%83%80%EC%9E%84%EC%95%84%EC%9B%83-%EB%B2%8C%ED%81%AC%ED%97%A4%EB%93%9C-%EC%84%9C%ED%82%B7-%EB%B8%8C%EB%A0%88%EC%9D%B4%EC%BB%A4)

10.2.3 기술 종속성 분석 CDR

1. 용어: Lock-in(종속), 프로토콜/SDK/데이터 포맷 종속, Blast Radius
2. 초급자 정의: Lock-in은 특정 벤더 기술에 깊게 묶여 교체가 어려운 상태입니다. 제거 시 영향 범위(Blast Radius)를 분석해 단계적 제거가 가능하도록 합니다.
3. 실무 예시: 벤더 SDK가 결제 핵심 흐름에 박혀 있으면 전환 시 전 서비스 영향 → 어댑터 레이어로 격리 후 교체.
4. 추천 한글 URL:
- 무중단 배포(점진 전환 맥락): [https://kylo8.tistory.com/entry/무중단-배포-전략-이해하기-롤링-배포-블루그린-배포-카나리-배포](https://kylo8.tistory.com/entry/%EB%AC%B4%EC%A4%91%EB%8B%A8-%EB%B0%B0%ED%8F%AC-%EC%A0%84%EB%9E%B5-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EB%A1%A4%EB%A7%81-%EB%B0%B0%ED%8F%AC-%EB%B8%94%EB%A3%A8%EA%B7%B8%EB%A6%B0-%EB%B0%B0%ED%8F%AC-%EC%B9%B4%EB%82%98%EB%A6%AC-%EB%B0%B0%ED%8F%AC)

11장 장애 복원력 및 재해복구 설계 CDR

11.1.1 장애 유형 분류 CDR

1. 용어: Failure Mode Catalog, MTTD(Mean Time To Detect), 런북 매핑
2. 초급자 정의: Failure Mode Catalog는 장애 유형을 카탈로그로 정리한 것이고, MTTD는 탐지까지 평균 시간입니다. 유형별로 탐지 지표와 1차 조치를 런북으로 연결해야 합니다.
3. 실무 예시: DB 장애 모드=연결불가/슬로우쿼리/스토리지 포화. 각 모드별 알람/런북 링크.
4. 추천 한글 URL:
- SLO 기반 운영(한글): [https://bcho.tistory.com/1332](https://bcho.tistory.com/1332)

11.1.2 RTO/RPO 정의 CDR

1. 용어: RTO(Recovery Time Objective), RPO(Recovery Point Objective), 달성 근거
2. 초급자 정의: RTO/RPO는 목표가 아니라 “달성 가능한 근거”가 같이 있어야 합니다(인프라·운영 역량과 맞아야 함).
3. 실무 예시: RPO 1분을 원하면 동기복제/높은 비용이 필요 → 현실적 타협(RPO 5분).
4. 추천 한글 URL:
- BIA/RTO/RPO 설명: [비즈니스 연속성 계획의 구성 요소는 무엇입니까?](https://www.zmanda.com/ko/%EB%B8%94%EB%A1%9C%EA%B7%B8/BCP-%ED%95%B5%EC%8B%AC-%EA%B5%AC%EC%84%B1-%EC%9A%94%EC%86%8C/)

11.1.3 재해복구 아키텍처 설계 CDR

1. 용어: Active-Active, Standby, Backup, 복제 지연(Replication Lag), 자동 검증 스크립트
2. 초급자 정의: Active-Active는 둘 다 서비스, Standby는 대기, Backup은 백업 기반 복구입니다. DR 전환 후 자동 검증 스크립트는 “살아났다”를 기능/데이터로 확인하는 자동화입니다.
3. 실무 예시: DR 전환 후 “주요 API 10개 헬스체크 + 핵심 테이블 레코드 수/합계 검증” 자동 실행.
4. 추천 한글 URL:
- MS BCDR(한글): [https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery](https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery)

11.2.1 복구 시험 계획 CDR

1. 용어: DR Test Plan(주기/범위/합격 기준)
2. 초급자 정의: DR은 “구성이 있다”가 아니라 주기적으로 시험해서 합격해야 신뢰할 수 있습니다.
3. 실무 예시: 분기 1회 “리전 장애 가정” 모의훈련, RTO/RPO 달성 여부 체크.
4. 추천 한글 URL:
- MS BCDR(한글): [https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery](https://learn.microsoft.com/ko-kr/azure/reliability/concept-business-continuity-high-availability-disaster-recovery)

11.2.2 복구 절차 검증 CDR

1. 용어: Runbook(명령/시간/검증 포함), 복구 후 검증 체크리스트
2. 초급자 정의: 런북은 “실행 가능한 수준(명령/담당/시간)”이 핵심이고, 복구 후 검증은 기능/데이터가 정상인지 확인하는 마지막 게이트입니다.
3. 실무 예시: DR 전환 후 결제 승인 테스트 거래 1건 수행 + 대시보드에서 오류율 정상 확인.
4. 추천 한글 URL:
- 로깅/추적(한글): [https://techblog.woowahan.com/2710/](https://techblog.woowahan.com/2710/)

11.2.3 서비스 축소 운영 전략 CDR

1. 용어: Degraded Mode, KPI 재정의(임시 KPI)
2. 초급자 정의: 대규모 장애에서는 “정상 KPI”를 그대로 쓰기 어렵고, 축소 운영에 맞춘 임시 KPI/보고 기준이 필요합니다.
3. 실무 예시: 평시 KPI=전환율, 장애 시 임시 KPI=핵심 결제 성공률/복구 진행률로 전환.
4. 추천 한글 URL:
- SLI/SLO/SLA(한글): [https://www.whatap.io/ko/blog/35/](https://www.whatap.io/ko/blog/35/)

12장 인공지능 활용 및 모델 거버넌스 설계 CDR

12.1.1 AI 활용 여부 선언 CDR

1. 용어: AI 사용/미사용 선언, 로드맵(Roadmap) (예정/금지/검토)
2. 초급자 정의: AI를 쓰는지 여부와 “앞으로 도입 가능한지/금지인지”를 명시해야 보안·준법·품질 기준이 흔들리지 않습니다.
3. 실무 예시: “현재는 미사용, 단 FAQ 자동요약은 3Q 검토(개인정보 비포함 조건)”
4. 추천 한글 URL:
- (조직의 AI 정책/보안팀 가이드가 핵심)

12.1.2 AI 기능 및 역할 정의 CDR

1. 용어: Use Case, 자동결정/승인 필요, Human-in-the-loop / Human-on-the-loop / Human-out-of-the-loop
2. 초급자 정의: Human-in-the-loop는 사람이 승인/개입하는 방식, on-the-loop는 사람이 감독(모니터링/중지권), out-of-the-loop는 완전 자동입니다. 기능별로 어디까지 자동화할지 구분해야 합니다.
3. 실무 예시: “고객 답변 추천=Human-in-the-loop(상담원이 승인 후 발송)”, “스팸 분류=on-the-loop(자동 분류, 오탐 모니터링)”.
4. 추천 한글 URL:
- Human-in-the-loop(한글): [AI 시스템을 강화하는 휴먼인더루프 HUMAN IN THE LOOP | 인사이트리포트 | 삼성SDS](https://www.samsungsds.com/kr/insights/human_in_the_loop.html)

12.1.3 모델 출처 및 버전 관리 CDR

1. 용어: 모델 출처(내부/외부), 버전 고정, 모델 레지스트리(Model Registry), 프롬프트/시스템 지시문 변경관리, Dataset Register
2. 초급자 정의: 모델 레지스트리는 “모델 버전/승인/배포 상태”를 관리하는 저장소이고, 프롬프트/파라미터도 결과를 바꾸므로 변경관리 대상입니다. 데이터셋도 출처/버전/보관/폐기까지 거버넌스가 필요합니다.
3. 실무 예시: “모델 v1.3.2 승인→프로덕션 배포”, 프롬프트 수정은 PR+리뷰+롤백 가능해야 함.
4. 추천 한글 URL:
- Model Registry 개념(한글): [ML 모델 레지스트리란 무엇입니까?](https://wandb.ai/site/ko/articles/what-is-an-ml-model-registry/)

12.2.1 인간 개입(Human Override) 전략 CDR

1. 용어: Kill-switch, Feature Flag, 백업(수동) 프로세스
2. 초급자 정의: Kill-switch는 AI 기능을 즉시 꺼서 사고 확산을 막는 스위치이며, 보통 Feature Flag로 구현합니다. 백업 프로세스는 AI가 멈춰도 업무가 돌아가게 하는 수동 절차입니다.
3. 실무 예시: 추천이 이상하면 즉시 OFF, 이후 상담원 수동 처리로 전환.
4. 추천 한글 URL:
- Feature Flag(한글): [https://tech.kakaopay.com/post/feature-flag/](https://tech.kakaopay.com/post/feature-flag/)

12.2.2 AI 결과 검증 절차 CDR

1. 용어: Evaluation Plan(평가 계획) (평가셋/승인 기준), 환각(Hallucination)/편향(Bias) 대응, Prompt Injection, Data Exfiltration, Jailbreak
2. 초급자 정의: 평가 계획은 “어떤 데이터로/어떤 기준으로 통과시킬지”이고, 프롬프트 인젝션/데이터 유출/탈옥은 입력으로 모델을 속여 정책을 우회하거나 민감정보를 빼내려는 위협입니다.
3. 실무 예시: 운영 전 레드팀 프롬프트로 우회 시도 → 필터링/정책 강화, 오답률 임계치 초과 시 자동 차단.
4. 추천 한글 URL:
- Prompt Injection(IBM 한글): [프롬프트 인젝션 공격이란 무엇인가요? | IBM](https://www.ibm.com/kr-ko/think/topics/prompt-injection)

12.2.3 AI 변경에 대한 변경관리 기준 CDR

1. 용어: 모델/프롬프트 변경=Change, 드리프트(Drift) 감지(성능 저하), Fail-safe(가드레일 강화/보수적 전환)
2. 초급자 정의: AI 변경은 결과가 바로 바뀌므로 코드 변경급(Change)으로 승인/검증/롤백이 필요합니다. 드리프트는 시간이 지나 성능이 떨어지는 현상이며, Fail-safe는 성능 악화 시 자동 제한/롤백하는 정책입니다.
3. 실무 예시: 정확도 3%p 하락 감지→알람→자동으로 “보수적 출력 모드”로 전환 + 필요 시 이전 모델로 롤백.
4. 추천 한글 URL:
- IaC Drift 개념(한글 기사): [https://www.hashicorp.com/blog/drift-detection-for-terraform](https://www.hashicorp.com/blog/drift-detection-for-terraform) (※ 한국어 페이지/번역 제공 여부는 접속 시 UI 언어 확인 필요)