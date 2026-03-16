# JIRA / Confluence 연동 가이드

## 1. JIRA 칸반보드 연동

### 방식 A: REST API로 직접 이슈 생성

로드맵의 각 체크리스트 항목을 JIRA 이슈로 자동 생성할 수 있습니다.

```python
# jira_sync.py
import requests
import json

JIRA_URL = "https://your-jira.atlassian.net"  # 또는 Jira DC URL
API_TOKEN = "your-api-token"
EMAIL = "[email]"
PROJECT_KEY = "REVENG"  # 프로젝트 키

headers = {
    "Content-Type": "application/json",
    "Authorization": f"Basic {API_TOKEN}"  # DC의 경우 Bearer 토큰
}

# 로드맵에서 파싱한 태스크 목록
phases = {
    "Phase 0: 준비": {
        "epic": "프로젝트 준비 및 환경 구축",
        "tasks": [
            "대상 서비스 인벤토리 작성",
            "Bitbucket DC 접근 권한 확보 및 Repository 목록 수집",
            "소스코드 없는 서버 식별 및 대체 분석 방법 결정",
            "분석 도구 선정 및 환경 구축",
            "Stakeholder 킥오프 — 관점별 우선순위 합의",
        ]
    },
    "Phase 1: 기초 분석": {
        "epic": "소스코드 탐색 및 기초 분석",
        "tasks": [
            "Repository별 자동 스캔 (언어/프레임워크 식별)",
            "API 엔드포인트 자동 추출",
            "DB 스키마 역추적",
            "소스 없는 서버 분석",
        ]
    },
    # Phase 2~4도 동일 패턴으로 추가
}

def create_epic(name, phase_name):
    payload = {
        "fields": {
            "project": {"key": PROJECT_KEY},
            "summary": f"[{phase_name}] {name}",
            "issuetype": {"name": "Epic"},
            "customfield_10011": name,  # Epic Name 필드 (서버마다 ID 다름)
        }
    }
    resp = requests.post(f"{JIRA_URL}/rest/api/2/issue", headers=headers, json=payload)
    return resp.json()["key"]

def create_task(summary, epic_key, labels=None):
    payload = {
        "fields": {
            "project": {"key": PROJECT_KEY},
            "summary": summary,
            "issuetype": {"name": "Task"},
            "customfield_10014": epic_key,  # Epic Link 필드
            "labels": labels or ["reverse-engineering"],
        }
    }
    resp = requests.post(f"{JIRA_URL}/rest/api/2/issue", headers=headers, json=payload)
    return resp.json()

# 실행
for phase_name, phase_data in phases.items():
    epic_key = create_epic(phase_data["epic"], phase_name)
    print(f"Created Epic: {epic_key} - {phase_name}")
    for task in phase_data["tasks"]:
        result = create_task(task, epic_key)
        print(f"  Created Task: {result.get('key')} - {task}")
```

### 방식 B: CSV Import (간단한 방법)

JIRA의 CSV Import 기능을 활용하면 코드 없이도 가능합니다.

```csv
Summary,Issue Type,Epic Name,Epic Link,Labels,Priority,Sprint
"[Phase 0] 대상 서비스 인벤토리 작성",Task,,Phase 0 - 준비,reverse-engineering,High,Sprint 1
"[Phase 0] Bitbucket DC 접근 권한 확보",Task,,Phase 0 - 준비,reverse-engineering,High,Sprint 1
"[Phase 1] Repository별 자동 스캔",Task,,Phase 1 - 기초분석,reverse-engineering,High,Sprint 2
"[Phase 1] API 엔드포인트 자동 추출",Task,,Phase 1 - 기초분석,reverse-engineering,Medium,Sprint 2
```

JIRA > Project Settings > Import Issues > CSV 로 업로드

### 칸반보드 설정 권장사항

```
칼럼 구성:
  Backlog → To Do → In Progress → Review → Done

필터 (JQL):
  project = REVENG AND labels = "reverse-engineering" ORDER BY priority DESC

스윔레인: Epic 기준 (Phase별 그룹핑)
```

---

## 2. Confluence 페이지 자동 포스팅

### 방식 A: REST API로 페이지 생성

```python
# confluence_sync.py
import requests
import markdown  # pip install markdown

CONFLUENCE_URL = "https://your-confluence.atlassian.net"  # 또는 DC URL
API_TOKEN = "your-api-token"
SPACE_KEY = "REVENG"

headers = {
    "Content-Type": "application/json",
    "Authorization": f"Basic {API_TOKEN}"
}

def md_to_confluence_html(md_file_path):
    """마크다운 파일을 Confluence Storage Format(HTML)으로 변환"""
    with open(md_file_path, "r", encoding="utf-8") as f:
        md_content = f.read()
    
    html = markdown.markdown(md_content, extensions=["tables", "fenced_code"])
    return html

def create_page(title, body_html, parent_id=None):
    payload = {
        "type": "page",
        "title": title,
        "space": {"key": SPACE_KEY},
        "body": {
            "storage": {
                "value": body_html,
                "representation": "storage"
            }
        }
    }
    if parent_id:
        payload["ancestors"] = [{"id": parent_id}]
    
    resp = requests.post(
        f"{CONFLUENCE_URL}/rest/api/content",
        headers=headers,
        json=payload
    )
    return resp.json()

def update_page(page_id, title, body_html, version_number):
    """기존 페이지 업데이트 (버전 증가 필요)"""
    payload = {
        "type": "page",
        "title": title,
        "version": {"number": version_number + 1},
        "body": {
            "storage": {
                "value": body_html,
                "representation": "storage"
            }
        }
    }
    resp = requests.put(
        f"{CONFLUENCE_URL}/rest/api/content/{page_id}",
        headers=headers,
        json=payload
    )
    return resp.json()

# 로드맵 페이지 생성
html_content = md_to_confluence_html("workplan-roadmap.md")
result = create_page(
    title="[설계역추적] Workplan Roadmap",
    body_html=html_content
)
parent_page_id = result["id"]
print(f"Created parent page: {result['id']}")

# Phase별 하위 페이지 생성
phase_pages = [
    ("Phase 0 - 준비", "phase0-detail.md"),
    ("Phase 1 - 기초 분석", "phase1-detail.md"),
    ("Phase 2 - View 생성", "phase2-detail.md"),
    ("Phase 3 - Gap 분석", "phase3-detail.md"),
    ("Phase 4 - 체계화", "phase4-detail.md"),
]

for title, md_file in phase_pages:
    # 각 Phase 상세 문서가 있을 경우
    try:
        html = md_to_confluence_html(md_file)
        create_page(f"[설계역추적] {title}", html, parent_id=parent_page_id)
    except FileNotFoundError:
        print(f"  Skipped: {md_file} not found yet")
```

### 방식 B: Confluence CLI (Atlassian 공식 도구 없을 때)

```bash
# markdown을 직접 Confluence에 올리는 오픈소스 도구
pip install md2cf

# 단일 페이지 업로드
md2cf --host https://your-confluence.atlassian.net \
      --username [email] \
      --password [api-token] \
      --space REVENG \
      --title "[설계역추적] Workplan Roadmap" \
      workplan-roadmap.md

# 폴더 내 모든 md 파일을 계층 구조로 업로드
md2cf --host https://your-confluence.atlassian.net \
      --username [email] \
      --password [api-token] \
      --space REVENG \
      --parent "[설계역추적] 프로젝트 홈" \
      docs/
```

---

## 3. CI/CD 파이프라인 연동 (자동 동기화)

분석 결과가 갱신될 때마다 자동으로 JIRA/Confluence를 업데이트하는 파이프라인:

```yaml
# .github/actions 또는 Bitbucket Pipeline 예시
# bitbucket-pipelines.yml

pipelines:
  custom:
    sync-docs:
      - step:
          name: Sync to Confluence & JIRA
          image: python:3.11
          script:
            - pip install requests markdown md2cf
            - python scripts/confluence_sync.py
            - python scripts/jira_sync.py
          artifacts:
            - reports/**

  branches:
    main:
      - step:
          name: Auto-sync on merge
          trigger: automatic
          script:
            - pip install requests markdown md2cf
            - python scripts/confluence_sync.py
```

---

## 4. 권장 페이지 구조 (Confluence)

```
📁 [설계역추적] 프로젝트 홈
├── 📄 Workplan Roadmap
├── 📄 서비스 인벤토리
├── 📁 Phase 0 - 준비
│   ├── 📄 Bitbucket Repository 목록
│   └── 📄 도구 선정 결과
├── 📁 Phase 1 - 기초 분석
│   ├── 📄 기술 프로파일 시트
│   ├── 📄 API 엔드포인트 목록
│   └── 📄 서비스 간 통신 매트릭스
├── 📁 Phase 2 - 아키텍처 View
│   ├── 📁 PO View (비즈니스)
│   ├── 📁 PM View (시스템)
│   ├── 📁 개발자 View (코드)
│   ├── 📁 UX View (화면흐름)
│   ├── 📁 보안 View
│   └── 📁 품질 View
├── 📁 Phase 3 - Gap 분석
│   └── 📄 추적성 매트릭스
└── 📁 Phase 4 - 유지보수 가이드
```
