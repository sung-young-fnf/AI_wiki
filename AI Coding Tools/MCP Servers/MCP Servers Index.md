# MCP Servers Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-01-03 |
| 키워드 | `MCP 서버`, `MCP server`, `GitHub MCP`, `Filesystem MCP`, `Figma MCP`, `NotebookLM MCP`, `DevOps MCP`, `Vercel MCP`, `Docker MCP`, `Playwright MCP`, `Exa`, `Sequential Thinking`, `MCP 큐레이션` |
| 관련문서 | [[AI Coding Tools Index]], [[ArticleScrap Platform Index]] |

## 개요

MCP(Model Context Protocol) 서버 큐레이션·리뷰·DevOps용·주식 데이터용·Filesystem·GitHub·Figma·NotebookLM 등 MCP 서버 모음 (MCP 설계 원리는 AI Engineering).

## 파일 목록 (12개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260301_185802_MCP 서버 12가지 사용기 내 프로젝트 개발 방식을 혁신한 5가지 핵심 서버]] | Gaurav Shrivastav이 3주간 12개 MCP 서버를 테스트해 5개로 추린 실무 리뷰. GitHub MCP(버전 제어·이슈·PR·브랜치)를 첫 번째로 꼽으며 npx @modelcontextprotocol/server-github + GITHUB_PERSONAL_ACCESS_TOKEN JSON 설정 예시를 제시한다. | GitHub MCP, @modelcontextprotocol/server-github, GITHUB_PERSONAL_ACCESS_TOKEN, Gaurav Shrivastav, MCP 서버 리뷰, 12개 서버 테스트, IDE JSON 설정 |
| [[20260301_185815_제가 12개의 MCP 서버를 사용해 본 결과 이 5가지가 프로젝트 구축 방식을 변화시켰습니다]] | Gaurav Shrivastav의 12개 MCP 서버 중 5선 리뷰(중복 번역본). GitHub MCP를 비롯한 5개 서버를 Cursor·Claude 등 MCP 호환 IDE에 복사-붙여넣기 JSON으로 설정하는 방법을 보여준다. | MCP 서버, 모델 컨텍스트 프로토콜, GitHub MCP, Gaurav Shrivastav, Cursor 설정, JSON 설정, 5가지 필수 서버 |
| [[20260318_213645_Claude Code로 구축한 결과물을 Figma로 직접 전송할 수 있습니다]] | Anthropic 공식 발표: Figma의 최신 MCP 서버로 Claude Code로 만든 작동 프로토타입을 Figma 캔버스에 직접 전송 가능. `/plugin install figma@claude-plugin-directory`로 시작하며 코드→디자인 역방향 흐름과 병렬 디자인 탐색을 지원한다. | Figma MCP 서버, claude-plugin-directory, Claude Code Figma 전송, /plugin install figma, 코드에서 디자인으로 역방향, 원격 MCP 서버 Claude Code |
| [[20260318_215300_NotebookLM을 Gemini CLI Google Antigravity 또는 기타 에이전트와 MCP로 통합하기]] | Dazbo(Darren Lester)가 NotebookLM MCP 서버로 Gemini CLI·Google Antigravity에서 기존 NotebookLM 노트북을 쿼리·생성·결합하는 방법을 시연. 1M 토큰 컨텍스트·인라인 인용·Deep Research Agents·오디오/비디오 개요·Nano Banana 슬라이드 덱 같은 NotebookLM 주요 기능도 정리한다. | NotebookLM MCP 서버, Gemini CLI, Google Antigravity, Deep Research Agents, 인라인 인용, Nano Banana 슬라이드 덱, Dazbo Darren Lester, 1M 토큰 컨텍스트 |
| [[20260318_215352_Claude Code를 운영 체제로 전환하다 그 청사진]] | Claude Code를 6계층 OS로 구성한 청사진: CLAUDE.md 커널 + memory/ 영구 메모리 + ~/.claude/rules path-scoped 규칙 + 32개 스킬(10카테고리, AAPEV 5단계) + 10개 전문 에이전트(브레인스토밍 병렬 실행) + 78개 제로트러스트 권한(허용 40/거부 38) + 17개 라이프사이클 훅 + 6개 MCP 서버(context7, fetch, git, huggingface, jupyter, ide). bash-guard가 rm -rf, sudo, eval 패턴 차단. 환각 방지 프로토콜이 Context7 우선 질의 강제. claude-code-blueprint 저장소 공개. | Claude Code 청사진, CLAUDE.md 커널, 제로 트러스트 권한, PreToolUse 훅, bash-guard, AAPEV 5단계, 환각 방지 프로토콜, Context7, MCP 서버 통합, claude-code-blueprint |
| [[20260322_122341_이 8가지 MCP 서버 없이는 당신의 AI는 무용지물입니다 대부분의 개발자는 이 서버들을 들어본 적이 없을 겁니다]] | Usman Writes가 꼽은 MCP 서버 8선: #8 Vercel(배포 로그 직결, Pro $20/월), #7 Docker(CI vs 로컬 컨테이너 검사, Docker Desktop 4.48+ MCP 툴킷), #6 Apify(2000+ 유지보수 액터 스크레이퍼, $49 시작), #5 Playwright(브라우저 자동화 무료), #4 Ref(문서 정밀 질의, 토큰 절약), #3 File System MCP(프로젝트 전역 인식 무료), #2 Exa(의미 기반 AI 웹 검색, 1000 무료), #1 Sequential Thinking(계획→답변, 무료). "채팅에서 구축으로, 추측에서 엔지니어링으로." | Vercel MCP, Docker MCP 툴킷, Apify 2000 액터, Playwright MCP, Ref 문서 MCP, File System MCP, Exa 의미 검색, Sequential Thinking 계획, MCP 서버 8선 추천, modelcontextprotocol/servers |
| [[20260329_181809_2026년 당신이 진정으로 필요로 할 클로드Claude 플러그인 10가지 및 그 기능]] | Anthropic이 2026년 2월 기업용 프라이빗 플러그인 마켓플레이스를 발표하고 커뮤니티에서 1,000개 이상 MCP 서버를 공개한 시점을 배경으로 10가지 핵심 Claude 플러그인을 소개한다. GitHub PR 생성, PostgreSQL 쿼리, Playwright 브라우저 제어, Gmail/Drive/Slack 접근, 실시간 웹 검색 등 에이전트형 자동화를 가능하게 하는 MCP 기반 플러그인들이다. Rohan Mistry가 2026년 필수 설치 목록으로 정리. | Claude MCP 플러그인, MCP 서버, Model Context Protocol, Playwright, PostgreSQL 연동, Gmail Drive 접근, Anthropic 플러그인 마켓플레이스, Rohan Mistry |
| [[20260329_200422_클로드 코드Claude Code 치트시트 ccstoryfoxcz]] | Claude Code 최신 명령과 단축키를 A4 한 장에 정리한 치트시트로, 기억에 의존하던 사용 패턴을 빠르게 참조 가능한 형식으로 바꿔준다. `--bare`, `--channels`, `/branch`, MCP 설정, 스킬 frontmatter의 `effort`, SendMessage 자동 재개 같은 최근 기능까지 한 번에 확인할 수 있다. | Claude Code cheatsheet, --bare, --channels, /branch, SendMessage auto-resume, MCP servers, frontmatter effort, cc.storyfox.cz |
| [[20260402_072729_Python 개발자인 내가 실제로 사용하는 3가지 MCP 서버]] | Filesystem, Git, HTTP API MCP 서버 세 개만 남겨 인지 부하를 줄이고 레거시 코드 조사와 변경 이력 분석, 라이브 API 검증을 단일 인터페이스로 통합한 경험을 설명한다. 500개 이상 파일의 코드베이스에서도 구조화된 파일 접근과 Git 맥락 추론이 생산성을 높이는 핵심으로 제시된다. | Filesystem MCP, Git MCP, HTTP API MCP, structured file access, codebase archaeology, API validation |
| [[20260409_074710_DevOps 속도를 높이는 상위 10가지 MCP 서버]] | DevOps 워크플로를 빠르게 만드는 MCP 서버 10종을 소개하는 도구 리뷰 글이다. 운영 자동화와 인프라 작업을 Claude류 에이전트에 연결해 반복 작업을 줄이는 데 초점을 둔다. | MCP server, DevOps automation, infrastructure tooling, workflow acceleration, agent tools, model context protocol |
| [[20260413_211809_2026년 모든 파이썬 개발자가 자신만의 MCP 서버를 구축해야 하는 이유]] | 파이썬 개발자가 자신만의 MCP 서버를 만들면 에이전트와 로컬 시스템을 더 직접적으로 연결할 수 있다는 주장을 편다. 단순 사용기를 넘어 도구 표면을 표준 프로토콜로 노출하는 개발자 습관을 강조한다. | MCP server, Python developer, Model Context Protocol, custom server, tool surface, local integration, agent tools, protocol standard |
| [[20260421_225156_주식 시장 데이터용 최고의 MCP 서버 7가지 2026]] | 주식 시장 데이터에 연결할 MCP 서버 7종을 데이터 깊이와 속도, 문서 품질 기준으로 비교한 글이다. EODHD, Financial Modeling Prep, Alpaca, Polygon, Finnhub, QuantConnect 같은 공급자를 실제 에이전트 워크플로 관점에서 평가한다. | MCP 서버, EODHD, Financial Modeling Prep, Alpaca, Polygon.io, Finnhub, QuantConnect, 주식 데이터 |

## 관련 카테고리

* > 상위: [[AI Coding Tools Index]]
* > 최상위: [[ArticleScrap Platform Index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
