# 번역 기사

- **원본 URL:** https://medium.com/ai-software-engineer/9-agent-skills-repos-i-tried-and-now-i-cant-stop-using-them-e359f956137b
- **번역 일시:** 2026-04-09 07:34:22

---

9 Agent Skills Repos I Tried (And Now I Can’t Stop Using Them)

에이전트 스킬(Agent skill)은 제대로 작동하는 AI 에이전트를 구축하기 위한 새로운 표준이 되고 있지만, 누구도 이 생태계가 얼마나 혼란스러운지 말해주지 않습니다.

안전하고 효과적인 스킬을 찾는 것은 복불복입니다. 대부분의 저장소는 인상적으로 보이지만… 직접 사용해 보기 전까지는 알 수 없습니다.

제가 수십 개의 저장소를 직접 경험해 보았기 때문에 압니다. 저는 여러 저장소를 하나하나 테스트하는 과정에 깊이 파고들었고, 일부 에이전트 스킬 저장소는 심지어 여러 AI 도구를 결합한 것보다 훨씬 더 좋은 성능을 보인다는 것을 발견했습니다.

이러한 에이전트 스킬들을 최대한 활용하려면 스킬의 기본을 이해해야 합니다.

### 에이전트 스킬(Agent Skill)이란 무엇인가요?

스킬은 단순히 내부에 `SKILL.md` 파일이 있는 폴더입니다.

이 파일에는 AI 에이전트가 특정 작업을 수행하는 방법을 가르치는 지침, 코드 예시 및 맥락(context)이 포함되어 있습니다. 에이전트가 작업을 시작하기 전에 읽는 플레이북(playbook)인 셈입니다.

기본 스킬 구조는 다음과 같습니다:

```
my-skill/
├── SKILL.md          # Required: instructions + metadata
├── scripts/          # Optional: helper scripts
└── references/       # Optional: docs and examples
```

`SKILL.md` 파일은 두 부분으로 구성됩니다.

*   메타데이터(이름, 설명, 트리거)가 포함된 YAML 프런트매터(frontmatter)
*   마크다운(Markdown) 형식의 실제 지침

에이전트에게 특정 작업을 요청하면, 에이전트는 해당 작업에 맞는 스킬을 확인하고 관련 맥락을 로드합니다.

### 스킬 라이브러리(Skill Library)가 필요한 이유는 무엇인가요?

스킬을 처음부터 작성하는 것은 시간이 많이 걸립니다.

도구를 이해하고, 명확한 지침을 작성하고, 엣지 케이스(edge case)를 테스트하며, 프롬프트(prompt)를 개선해야 합니다. 또한, 여러분의 스택에 있는 모든 도구마다 며칠의 작업이 필요하다는 점도 고려해야 합니다.

스킬 라이브러리가 이 문제를 해결해 줍니다.

누군가는 이미 Claude를 Obsidian과 연동하거나, UI 목업(mockup)을 생성하거나, 과학 데이터베이스를 쿼리하는 가장 좋은 방법을 찾아냈습니다. 여러분은 그들의 스킬을 설치하여 몇 초 만에 프로덕션(production) 환경에서 바로 사용할 수 있는 기능을 얻을 수 있습니다.

문제는 제대로 작동하는 스킬을 찾는 것입니다.

GitHub에는 유망해 보이는 저장소들이 넘쳐나지만, 오래된 종속성(dependencies)으로 인해 작동하지 않거나 지침이 모호한 경우가 많습니다.

때로는 6개월 전에는 작동했던 스킬이 현재 모델에서는 작동하지 않을 수도 있습니다.

여러분의 시간을 아낄 수 있는 저장소를 찾기 위해 40개 이상의 저장소를 테스트했습니다.

이 9가지 저장소는 활발하게 유지 관리되거나, 강력한 커뮤니티 지원을 받거나, 특정 도메인(domain) 문제를 해결하기 때문에 눈에 띄었습니다.

### 엄선된 컬렉션 저장소

이들은 생태계 전반의 스킬을 집대성한 대규모 컬렉션을 가진 '어썸 리스트(awesome list)' 스타일의 저장소입니다. 다양성과 폭넓은 적용 범위를 원한다면 좋은 시작점이 될 것입니다.

#### 1. Awesome Claude Skills

Awesome Claude Skills는 Composio가 Claude.ai, Claude Code, Claude API 전반에서 생산성을 향상시키기 위해 엄선한 실용적인 Claude 스킬 모음입니다.

문서 처리부터 SaaS 자동화까지 모든 것을 다룹니다. 특히 Composio 통합 기능이 돋보입니다. Claude를 Gmail, Slack, GitHub, Notion 등 500개 이상의 앱과 연결할 수 있습니다.

이 저장소는 스킬을 문서 처리(docx, pdf, pptx, xlsx), 개발 도구, 비즈니스 자동화, 창의적 워크플로(workflow)와 같은 명확한 범주로 분류합니다. 각 스킬 폴더에는 작동 예시와 통합 가이드가 포함되어 있습니다.

**주요 기능**
*   Composio SDK를 통한 78개 이상의 앱 자동화 스킬
*   Anthropic 공식 저장소의 문서 조작 스킬
*   CRM, 프로젝트 관리, 마케팅 도구를 위한 사전 구축된 워크플로
*   Claude.ai, Claude Code, API 전반에서 작동

설정은 간단합니다. 플러그인(plugin)을 추가하고 `connect-apps` 설정을 실행하면 몇 분 내에 Claude에서 이메일을 보낼 수 있습니다.

링크: Awesome Claude Skills

#### 2. Antigravity Awesome Skills

Antigravity Awesome Skills는 제가 찾은 가장 큰 컬렉션으로, 1,239개 이상의 스킬을 보유하고 있으며 계속 증가하고 있습니다. Claude Code, Cursor, Gemini CLI, Codex 및 기타 여러 AI 코딩 도구에서 작동합니다.

이 저장소에는 Anthropic, Vercel, OpenAI, Supabase, Microsoft의 공식 스킬이 포함되어 있습니다. 커뮤니티 기여를 통해 보안 감사부터 마케팅 자동화에 이르는 모든 격차를 채웁니다.

특히 스킬을 웹 개발자, 보안 엔지니어, DevOps, QA 테스트와 같은 역할별 번들로 그룹화하는 방식이 마음에 듭니다.

**주요 기능**
*   9개 카테고리에 걸친 1,239개 이상의 스킬
*   역할별(웹 마법사, 보안 엔지니어 등)로 구성된 스타터 번들
*   크로스 플랫폼(Claude Code, Cursor, Gemini CLI, Codex, Antigravity) 지원
*   스킬 검색 및 필터링을 위한 내장 웹 앱

이들은 탐색을 위한 웹 앱도 구축했습니다. 카테고리별로 검색, 필터링하고 스킬을 직접 복사할 수 있습니다. GitHub 폴더를 스크롤하는 것보다 빠릅니다.

링크: Antigravity Awesome Skills

#### 3. Agent Skills for Context Engineering

Agent Skills for Context Engineering은 효과적인 에이전트 시스템을 구축하는 방법을 이해하기 위한 프레임워크(framework)입니다.

이 저장소는 정적 스킬 아키텍처(static skill architecture)에 대한 기초 연구로 베이징 대학의 학술 연구에 인용되었습니다. 컨텍스트(context) 기본 사항(컨텍스트 창에 무엇이 들어가고 왜 들어가는지), 메모리 시스템(단기, 장기, 그래프 기반), 다중 에이전트 패턴(오케스트레이터, P2P, 계층적), 평가 프레임워크를 다룹니다.

각 스킬에는 파이썬(Python)으로 작성된 스크립트와 예시가 포함되어 있습니다. 프로덕션 에이전트 시스템을 구축하고 있다면, 이곳에서 아키텍처를 배울 수 있습니다.

**주요 기능**
*   컨텍스트 엔지니어링을 위한 15개 이상의 기본 스킬
*   메모리 시스템 설계(추가 전용, 시맨틱, 그래프 기반)
*   다중 에이전트 오케스트레이션 패턴
*   에이전트 시스템을 위한 평가 및 디버깅 프레임워크

이 스킬들은 Claude Code 플러그인으로 작동합니다. 마켓플레이스(marketplace)를 통해 설치하면 Claude가 작업에 따라 관련 스킬을 활성화합니다.

링크: Agent Skills for Context Engineering

### 전문화된 스킬

이 저장소들은 한 가지 일을 매우 잘 수행합니다. 제가 테스트한 어떤 것보다 특정 문제를 더 잘 해결하는 집중된 도구입니다.

#### 4. UI UX Pro Max Skill

UI UX Pro Max는 주문형으로 완전한 디자인 시스템을 생성하는 AI 스킬입니다. 제품 설명을 제공하면 업계에 맞춰진 패턴, 색상, 타이포그래피(typography), 효과 및 안티패턴(anti-pattern)을 반환합니다.

이 추론 엔진은 SaaS부터 뷰티 스파, 핀테크(fintech)에 이르는 161가지 산업별 규칙을 포함합니다. "내 웰니스 앱(wellness app)을 위한 랜딩 페이지를 만들어줘"라고 말하면, 적절한 스타일(Soft UI Evolution), 팔레트(palming aqua, sage green) 및 타이포그래피(Cormorant Garamond / Montserrat)를 가져옵니다.

이 스킬은 React, Next.js, SwiftUI, Flutter, Tailwind 등 15가지 기술 스택(tech stack)에서 작동합니다. CLI(명령줄 인터페이스) 또는 Claude 마켓플레이스를 통해 설치할 수 있습니다.

**주요 기능**
*   161가지 산업별 디자인 추론 규칙
*   67가지 UI 스타일(Glassmorphism, Neubrutalism, Bento Grid 등)
*   Google Fonts 링크와 함께 큐레이션된 57가지 글꼴 조합
*   접근성 및 반응형(responsiveness)을 위한 사전 전달 체크리스트

결과는 여러분이 프로젝트에 복사하여 빌드를 시작할 수 있는 완전한 디자인 시스템입니다.

링크: UI UX Pro Max Skill

#### 5. Humanizer

Humanizer는 AI 생성 텍스트의 특징적인 징후를 제거합니다. Wikipedia의 "AI 작성 징후" 가이드에 기반하며, 수천 개의 AI 생성 텍스트를 검토한 후 WikiProject AI Cleanup에서 유지 관리합니다.

이 스킬은 29가지 패턴을 감지합니다. 중요성 과장("marking a pivotal moment in the evolution of…"), 계사(copula) 회피("serves as" 대신 "is"), 엠 대시(em dash) 과용, 아첨하는 어조, 셋의 규칙(rule of three) 등이 있습니다. 각 패턴에는 수정 전/후 예시가 있어 어떤 부분이 수정되는지 이해할 수 있습니다.

자신의 글 샘플을 붙여넣으면, 일반적인 "깨끗한" 출력을 생성하는 대신 사용자의 리듬, 단어 선택, 특이점을 반영합니다.

최종 단계에서는 "명백한 AI 생성" 감사(audit)를 실행하여 여전히 인공적으로 들리는 모든 것을 다시 작성합니다.

**주요 기능**
*   Wikipedia 연구 기반의 29가지 감지 패턴
*   개인 글쓰기 스타일에 맞춘 목소리 보정
*   최종 감사 기능이 포함된 2단계 재작성
*   Claude Code 및 OpenCode와 연동

스킬 디렉토리에 복제(clone)한 후 텍스트를 붙여넣고 `/humanizer`를 실행하여 설치합니다.

링크: Humanizer

#### 6. Last30Days Skill

Last30Days는 지난 30일간의 Reddit 및 X(구 트위터)의 모든 주제를 조사한 다음, 복사하여 붙여넣을 수 있는 프롬프트를 작성합니다.

이는 프롬프트 연구를 자동화한 것입니다. 이 스킬은 발견 사항을 실행 가능한 결과물로 종합합니다.

예를 들어, 법률 관련 프롬프트 쿼리에서는 환각 방지(hallucination prevention)가 지배적인 주제임을 식별하고, 다섯 가지 주요 프롬프트 전략을 추출했으며, 역할 할당, 구조화된 출력 요구 사항, 겸손 강화를 포함하는 즉시 사용 가능한 프롬프트를 생성했습니다.

**주요 기능**
*   지난 30일간의 Reddit 및 X 토론 스캔
*   참여 지표(업보트, 좋아요, 리포스트) 반환
*   대상 도구를 위한 복사-붙여넣기 가능한 프롬프트 생성
*   트렌드 연구, 제품 연구, 뉴스 분석에 활용 가능

Reddit 조사를 위해서는 OpenAI API 키가, X 조사를 위해서는 xAI API 키가 필요합니다.

링크: Last30Days Skill

### 도메인별 저장소

이 저장소들은 특정 생태계를 목표로 합니다. 과학 분야에서 일하거나, Obsidian을 사용하거나, OpenAI 스택 위에서 개발한다면, 이 저장소들이 여러분을 위해 만들어졌습니다.

#### 7. Claude Scientific Skills

Claude Scientific Skills는 과학 컴퓨팅, 연구 및 분석을 위한 140가지 스킬 모음입니다.

생물정보학, 유전체학, 화학정보학, 신약 개발, 단백체학, 임상 연구, 의료 영상 등을 다룹니다. 이 저장소는 PubMed, ChEMBL, UniProt, ClinVar, COSMIC, DrugBank 등 28개 이상의 과학 데이터베이스에 연결됩니다.

**주요 기능**
*   12개 이상의 분야에 걸친 140가지 과학 스킬
*   28개 이상의 데이터베이스 통합(PubMed, UniProt, ChEMBL 등)
*   자동 설정이 가능한 55개 이상의 파이썬 패키지
*   유전체학, 신약 개발, 의료 영상을 위한 전문 워크플로

각 스킬에는 분자 분석을 위한 RDKit, 단일 세포 유전체학을 위한 Scanpy, 신약 개발 머신러닝(ML)을 위한 DeepChem과 같은 필수 파이썬 패키지가 포함되어 있습니다.

링크: Claude Scientific Skills

#### 8. OpenAI Skills (Codex)

OpenAI Skills는 OpenAI의 Codex CLI를 위한 공식 스킬 카탈로그입니다. 스킬이 어떻게 구조화되어야 하는지에 대한 참조 구현(reference implementation)입니다.

이 저장소는 스킬을 세 가지 계층으로 분류합니다. 시스템 스킬(`.system`)은 셸(shell) 명령, 파일 작업, 기본 유틸리티 등 자동으로 설치됩니다. 큐레이션된 스킬(`.curated`)은 OpenAI에 의해 검증됩니다. 실험적 스킬(`.experimental`)은 아직 테스트 중인 커뮤니티 기여물입니다.

스킬 설치 프로그램(`$skill-installer`)은 검색 및 설정을 처리합니다. 이를 실행하고 필요한 것을 검색한 다음 한 번의 명령으로 설치할 수 있습니다.

**주요 기능**
*   세 가지 계층 조직(system, curated, experimental)
*   검색 및 설정을 위한 내장 스킬 설치 프로그램
*   다국어 지원(Python, JavaScript, Shell, Swift, PowerShell)
*   agentskills.io 오픈 표준(open standard) 준수

Codex를 사용하거나 OpenAI가 스킬을 어떻게 구조화하는지 알고 싶다면, 이곳이 최고의 자료입니다.

링크: OpenAI Skills

#### 9. Obsidian Skills

Obsidian Skills는 Obsidian의 CEO인 Kepano가 유지 관리합니다. 이는 이 스킬들이 앱 작동 방식에 대한 깊은 지식을 바탕으로 설계되었음을 의미합니다.

이 저장소에는 5가지 스킬이 포함되어 있습니다. `obsidian-markdown`은 Obsidian의 확장된 마크다운 구문(콜아웃, 임베드, 주석)을 처리합니다. `obsidian-bases`는 구조화된 데이터를 위한 새로운 Bases 기능을 다룹니다. `json-canvas`는 Obsidian Canvas 형식으로 작업합니다. `obsidian-cli`는 명령줄과 통합됩니다. `defuddle`은 웹 페이지에서 깨끗한 콘텐츠를 추출하여 가져옵니다.

각 스킬은 agentskills.io의 에이전트 스킬 사양(specification)을 따릅니다. 이들은 Claude Code, Codex CLI 및 OpenCode와 바로 작동합니다.

**주요 기능**
*   Obsidian CEO가 제공하는 5가지 공식 스킬
*   Obsidian 고유 구문, Bases, Canvas 및 CLI 다룸
*   agentskills.io 사양 준수
*   Claude Code, Codex CLI 및 OpenCode 전반에서 작동

스킬 디렉토리로 복제하거나 지원되는 플랫폼을 통해 설치할 수 있습니다.

링크: Obsidian Skills