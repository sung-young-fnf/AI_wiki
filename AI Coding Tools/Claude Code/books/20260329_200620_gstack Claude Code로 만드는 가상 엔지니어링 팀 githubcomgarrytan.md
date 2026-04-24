# 번역 기사

- **원본 URL:** https://share.google/BBhiePFqMP9vyvWGp
- **번역 일시:** 2026-03-29 20:06:20

---

# gstack: Claude Code로 만드는 가상 엔지니어링 팀 (github.com/garrytan)

78점 | 작성자 xguru | 6일 전 | ★ 즐겨찾기 | 댓글 4개

YC CEO 개리 탄(Garry Tan)이 직접 개발하고 사용하는, AI로 구성된 오픈소스 소프트웨어 팩토리(Software Factory)로, 개인 개발자가 20명 규모의 팀처럼 작업할 수 있도록 설계되었습니다.

구상(Think) → 계획(Plan) → 구현(Build) → 검토(Review) → 테스트(Test) → 배포(Ship) → 회고(Reflect)의 순서로 전체 스프린트(Sprint) 과정을 아우르는 슬래시 커맨드(Slash Command)로 구성되어 있으며, 각 스킬(Skill)은 다음 단계의 스킬에 자동으로 컨텍스트(Context)를 전달합니다.

`/office-hours`로 제품 가정을 검증하는 것을 시작으로, `/plan-ceo-review` 및 `/plan-eng-review`를 통해 아키텍처(Architecture)를 확정하며, `/review`, `/qa`, `/ship`을 활용해 버그(Bug) 수정부터 풀 리퀘스트(PR, Pull Request) 생성까지 전 과정을 자동화합니다.

각 명령(Command)은 마치 역할별 전문가처럼 동작합니다: CEO 리뷰어, 엔지니어링 매니저(Engineering Manager) 설계자, 디자이너 감리자, QA 리드(QA Lead), 릴리즈 엔지니어(Release Engineer) 등.

---

### 대상 사용자

*   **창업자·CEO(Founder·CEO)** — 여전히 직접 코드를 배포(Deploy)하고 싶은 기술 창업자
*   **Claude Code 입문자** — 빈 프롬프트(Prompt) 대신 구조화된 역할 기반 워크플로우(Workflow)가 필요한 사용자
*   **테크 리드·스태프 엔지니어(Tech Lead·Staff Engineer)** — 풀 리퀘스트(PR)마다 엄격한 리뷰(Review), QA, 릴리스(Release) 자동화가 필요한 시니어 엔지니어(Senior Engineer)

---

### 스프린트의 핵심 스킬

*   `/office-hours` — YC 오피스아워(Office Hours) 형식의 6가지 필수 질문으로 제품 가정을 검증하고, 디자인 문서를 생성하여 하위 스킬(Sub-skill)에 자동으로 전달합니다.
*   `/plan-ceo-review` — 문제를 재정의하고 '10성급 제품'을 탐색합니다. 확장(Expansion)·선택적 확장(Selective Expansion)·범위 유지(Hold Scope)·축소(Reduction)의 4가지 모드를 제공합니다.
*   `/plan-eng-review` — 아키텍처(Architecture), 데이터 흐름(Data Flow), ASCII 다이어그램(Diagram), 엣지 케이스(Edge Case), 테스트 매트릭스(Test Matrix), 보안 우려 사항을 정의합니다.
*   `/plan-design-review` — 디자인의 각 차원(Dimension)을 0~10점으로 평가하고 10점 기준을 설명합니다. AI 슬롭(Slop) 감지 기능이 포함되어 있으며, 디자인 결정마다 `AskUserQuestion`을 통해 한 번의 인터랙티브(Interactive) 질문을 진행합니다.
*   `/design-consultation` — 완전한 디자인 시스템(Design System)을 처음부터 구축하고, 현실적인 제품 목업(Mockup)을 생성합니다.
*   `/review` — CI(Continuous Integration)를 통과하지만 프로덕션(Production) 환경에서 발생하는 버그(Bug)를 탐지하고, 명백한 이슈(Issue)를 자동으로 수정하며, 완성도 격차를 표시합니다.
*   `/investigate` — 철칙: 조사 없이는 수정 없음(Investigate, then Fix); 데이터 흐름(Data Flow)을 추적하고 가설을 검증하며, 3회 실패 시 작업을 중단합니다.
*   `/design-review` — `/plan-design-review`와 동일한 감사(Audit)를 수행한 후 발견된 문제를 직접 수정하며, 수정 전/후 스크린샷(Screenshot)을 첨부합니다.
*   `/qa` — 실제 브라우저로 애플리케이션(App)을 테스트하고, 버그를 발견 및 수정하며, 수정 건마다 회귀 테스트(Regression Test)를 자동으로 생성합니다.
*   `/qa-only` — `/qa`와 동일한 방법론을 사용하나, 코드 변경 없이 버그 리포트(Bug Report)만 생성합니다.
*   `/cso` — OWASP Top 10 및 STRIDE 위협 모델(Threat Model) 감사를 수행합니다. 17개의 거짓 양성(False Positive) 제외 규칙과 10점 만점 중 8점 이상의 신뢰도를 가진 게이트(Gate)를 포함하며, 각 발견 사항에는 구체적인 익스플로잇(Exploit) 시나리오가 포함됩니다.
*   `/ship` — `main` 브랜치 동기화, 테스트 실행, 코드 커버리지(Code Coverage) 감사, 푸시(Push), 풀 리퀘스트(PR) 생성을 수행합니다. 테스트 프레임워크(Test Framework)가 없을 시 자동으로 부트스트랩(Bootstrap)합니다.
*   `/land-and-deploy` — 풀 리퀘스트(PR) 머지(Merge) → CI(Continuous Integration) 및 배포(Deploy) 대기 → 프로덕션(Production) 상태 검증을 단일 커맨드(Command)로 완료합니다.
*   `/canary` — 배포(Deployment) 후 콘솔 에러(Console Error), 성능 회귀(Performance Regression), 페이지 장애 모니터링 루프(Monitoring Loop)를 실행합니다.
*   `/benchmark` — 페이지 로드 시간(Page Load Time), 코어 웹 바이탈(Core Web Vitals), 리소스 크기 기준선(Baseline)을 측정하고 풀 리퀘스트(PR)별로 전후를 비교합니다.
*   `/document-release` — 배포된 내용에 맞춰 모든 프로젝트 문서를 업데이트하고, 오래된 `README` 파일을 자동으로 감지합니다.
*   `/retro` — 주간 회고(Weekly Retrospective); 개인별 분석, 배포 연속 기록, 테스트 건강도 추세(Trend)를 제공합니다. `/retro global` 명령으로 전체 프로젝트 및 AI 도구(Claude Code, Codex, Gemini) 통합 회고를 진행할 수 있습니다.
*   `/browse` — 실제 크로미움(Chromium) 브라우저를 사용하며, 실제 클릭과 스크린샷(Screenshot)을 찍습니다. 커맨드당 약 100ms가 소요됩니다.
*   `/setup-browser-cookies` — 크롬(Chrome)·아크(Arc)·브레이브(Brave)·엣지(Edge) 브라우저에서 쿠키(Cookie)를 헤드리스 세션(Headless Session)으로 가져와 인증된 페이지를 테스트합니다.
*   `/autoplan` — CEO → 디자인 → 엔지니어링 리뷰(Review)를 자동으로 순서대로 실행하며, 사용자의 취향에 따른 결정 사항만 노출합니다.

---

### 파워 툴

*   `/codex` — OpenAI 코덱스(Codex) CLI의 독립적인 코드 리뷰(Code Review)를 수행합니다. 리뷰(통과/실패 게이트)·적대적 도전(Adversarial Challenge)·오픈 컨설테이션(Open Consultation)의 3가지 모드가 있으며, `/review`와 `/codex`를 모두 실행 시 교차 모델(Cross-Model) 분석을 제공합니다.
*   `/careful` — `rm -rf`, `DROP TABLE`, 강제 푸시(Force Push) 등 파괴적인 커맨드(Command) 실행 전에 경고를 표시하며, `"be careful"` 입력으로 활성화됩니다.
*   `/freeze` — 파일 편집을 특정 디렉토리(Directory)로 제한하여 디버깅(Debugging) 중 의도치 않은 범위 외 변경을 방지합니다.
*   `/guard` — `/careful`과 `/freeze`가 통합된 기능으로, 프로덕션(Production) 작업에 최적화된 최고 안전 설정을 제공합니다.
*   `/unfreeze` — `/freeze`로 설정된 제한을 해제합니다.
*   `/setup-deploy` — `/land-and-deploy`를 위한 일회성 설정(One-time Setup)을 제공하며, 플랫폼(Platform)·프로덕션 URL·배포 커맨드(Deploy Command)를 자동으로 감지합니다.
*   `/gstack-upgrade` — gstack을 최신 버전으로 업그레이드(Upgrade)합니다. 글로벌(Global) 및 벤더드(Vendored) 설치 모두 감지하여 동기화합니다.

---

### 병렬 스프린트(Parallel Sprints)

컨덕터(Conductor)를 통해 여러 Claude Code 세션(Session)을 격리된 워크스페이스(Workspace)에서 동시에 실행할 수 있습니다.
예: 한 세션에서는 `/office-hours`를, 다른 세션에서는 `/review`를, 세 번째 세션에서는 기능 구현을, 네 번째 세션에서는 `/qa`를 동시에 진행할 수 있습니다.

*   창업자·리드 엔지니어(Lead Engineer)·PM(Project Manager)이 AI 개발 팩토리(Factory)를 포크(Fork)하여 직접 실험하고 확장할 수 있습니다.
*   Claude Code 외에도 코덱스(Codex), 제미니 CLI(Gemini CLI), 커서(Cursor) 등 `SKILL.md` 표준을 지원하는 모든 에이전트(Agent)에서 동작합니다.

**MIT 라이선스**

---

### 댓글

**laeyoung** | 4일 전

오피스아워(Office Hour)를 자세히 살펴보니, 이 스킬(Skill) 하나만으로도 마크다운(Markdown) 길이가 상당하네요.

**kgcrom** | 6일 전

와우! 개리 탄(Garry Tan)이 긱뉴스를 리트윗(Retweet)했네요.
`https://x.com/garrytan/status/2035898375934300353`

**angrybird0** | 6일 전

점점 1인 기업(One-person Company)을 운영하는 사람들이 잘 활용할 수 있도록 진화하고 있네요.

**ragingwind** | 6일 전

`/office-hours` 기능이 흥미롭네요. 저는 핸즈온(Hands-on) 경험이 있습니다.

---

### 기타 정보

처음 오셨나요? | 사이트 이용 방법 | FAQ (자주 묻는 질문) | About (소개) | 이용 약관 | 개인정보 처리 방침

블로그(Blog) | 목록 | RSS | 북마클릿(Bookmarklet)

X (트위터) | 페이스북(Facebook)

긱뉴스봇: 슬랙(Slack), 잔디, 디스코드(Discord), 팀즈(Teams), 두레이(Dooray!), 구글 챗(Google Chat), 스윗(Swit)

검색