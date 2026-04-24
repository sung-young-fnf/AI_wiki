# 번역 기사

- **원본 URL:** https://share.google/oCobkTR0ki8hl18QQ
- **번역 일시:** 2026-03-29 20:04:22

---

# 클로드 코드(Claude Code) 치트시트 (cc.storyfox.cz)

긱뉴스 최신 글 | 댓글 | 이전 글 | Ask | Show | GN⁺ 위클리 | 글 등록
로그인

▲
**클로드 코드(Claude Code) 치트시트 (cc.storyfox.cz)**
53포인트 by GN⁺ | 5일 전 | ★ 즐겨찾기 | 댓글 2개

클로드 코드(Claude Code) 최신 버전의 주요 명령어, 단축키, 설정, 환경 변수, MCP 서버 및 에이전트(agent) 구성을 정리한 개발자용 요약 문서입니다.

새 버전에는 헤드리스 모드(headless mode, `--bare`), MCP를 통한 디스코드/텔레그램 메시지 전송(`--channels`), 스킬(skill)/슬래시(slash) 명령어를 위한 프론트매터(frontmatter, `effort`), 기존 'fork'가 `/branch`로 변경, SendMessage 자동 재개 기능이 추가되었습니다.

키보드 단축키, MCP 서버, 슬래시 명령어, 스킬(skill)·에이전트(agent) 관리, 헤드리스(headless) 실행 및 원격 제어 등 대부분의 명령어를 보기 좋게 정리했습니다.

Windows/Mac용 별도 보기 전환(switch)을 지원합니다.

### 키보드 단축키

**일반 제어**
*   `Ctrl+C`: 입력/생성 취소 (Cancel input/generation)
*   `Ctrl+D`: 세션 종료 (End session)
*   `Ctrl+L`: 화면 지우기 (Clear screen)
*   `Ctrl+O`: 상세 출력 전환 (Toggle verbose output)
*   `Ctrl+R`: 히스토리 검색 (Search history)
*   `Ctrl+G`: 프롬프트 편집기 열기 (Open prompt editor)

*   `Ctrl+B`: 백그라운드 실행 (Run in background)
*   `Ctrl+T`: 작업 목록 전환 (Switch task list)
*   `Ctrl+V`: 이미지 붙여넣기 (Paste image)
*   `Ctrl+F`: 백그라운드 에이전트 종료 (Terminate background agent - 2회 필요)
*   `Esc`: 되돌리기 (Undo)

**모드 전환**
*   `Shift+Tab`: 권한 모드 순환 (Cycle permission modes)
*   `Alt+P`: 모델 전환 (Switch model)
*   `Alt+T`: 사고 모드 전환 (Toggle thinking mode)

**입력 제어**
*   `Enter`: 빠른 줄바꿈 (Quick line break)
*   `Ctrl+J`: 제어 시퀀스 줄바꿈 (Control sequence line break)

**접두사 (Prefixes)**
*   `/`: 슬래시 명령 (Slash command)
*   `!`: 직접 bash 실행 (Direct bash execution)
*   `@`: 파일 언급 및 자동완성 (File mention and autocompletion)

### 세션 선택기 (Session Selector)
*   방향키로 탐색 및 확장/축소
*   `P`: 미리보기 (Preview)
*   `R`: 이름 변경 (Rename)
*   `/`: 검색 (Search)
*   `A`: 전체 프로젝트 (All projects)
*   `B`: 현재 브랜치 (Current branch)

### MCP 서버 관리 (MCP Server Management)

**서버 추가**
*   `--transport http`: 원격 HTTP (Remote HTTP, 권장)
*   `--transport stdio`: 로컬 프로세스 (Local process)
*   `--transport sse`: 원격 SSE (Remote SSE)

**스코프 (Scope)**
*   로컬 (Local): `~/.claude.json`
*   프로젝트 (Project): `project.mcp.json`
*   사용자 (User): `~/.claude.json`

**관리 명령 (Management Commands)**
*   `/mcp`: 인터랙티브 UI (Interactive UI)
*   `claude mcp list`: 전체 서버 목록
*   `claude mcp serve`: 클로드 코드(CC)를 MCP 서버로 실행

### Elicitation 서버 (Elicitation Servers)
*   작업 중 입력 요청 기능 (Input prompting during work - 신규)

### 슬래시 명령어 (Slash Commands)

**세션 관련 (Session-related)**
*   `/clear`, `/compact`, `/resume`, `/rename`, `/branch`, `/cost`, `/context`, `/diff`, `/copy`, `/export`

**설정 관련 (Configuration-related)**
*   `/config`, `/model`, `/fast`, `/vim`, `/theme`, `/permissions`, `/effort`, `/color`

**도구 관련 (Tool-related)**
*   `/init`, `/memory`, `/mcp`, `/hooks`, `/skills`, `/agents`, `/chrome`, `/reload-plugins`

**특수 명령 (Special Commands)**
*   `/btw`, `/plan`, `/loop`, `/voice`, `/doctor`, `/rc`, `/pr-comments`, `/stats`, `/insights`, `/desktop`, `/remote-control`, `/stickers`

### 메모리 및 파일 구조 (Memory & File Structure)

**CLAUDE.md 위치**
*   프로젝트 (Project): `./CLAUDE.md`
*   개인 (Personal): `~/.claude/CLAUDE.md`
*   조직 (Organization): `/etc/claude-code/Managed`

**규칙 및 임포트 (Rules & Imports)**
*   `.claude/rules/*.md`, `~/.claude/rules/*.md`, `@path/to/file` 임포트 가능

**자동 메모리 (Auto Memory)**
*   `~/.claude/projects/<project_id>/memory/` 내 `MEMORY.md` 및 주제별 파일 자동 로드

### 워크플로 및 팁 (Workflow & Tips)

**계획 모드 (Plan Mode)**
*   `Shift+Tab`으로 일반 → 자동 → 계획 모드 전환
*   `--permission-mode plan`으로 시작 가능

**사고 및 노력 (Thinking & Effort)**
*   `Alt+T`로 사고 모드 전환
*   `"ultrathink"`는 최대 노력 모드
*   `/effort`로 수준 설정 (`low`, `med`, `high`)

**Git 워크트리 (Git Worktrees)**
*   `--worktree`로 기능별 분리 브랜치 생성
*   `sparsePaths`로 필요한 디렉터리만 체크아웃

**음성 모드 (Voice Mode)**
*   `/voice`로 음성 입력 활성화
*   스페이스바(Spacebar)로 녹음 및 전송
*   20개 언어 지원

**컨텍스트 관리 (Context Management)**
*   `/context`, `/compact`로 컨텍스트(context) 최적화
*   최대 1M 컨텍스트 지원
*   `CLAUDE.md`는 압축 후에도 유지

**세션 단축 명령 (Session Shortcut Commands)**
*   `claude -c`: 마지막 대화 이어가기
*   `claude -r "name"`: 이름으로 재개
*   `/btw`: 별도 질문

### SDK 및 헤드리스 모드 (SDK & Headless Mode)

**비대화형 실행 (Non-interactive Execution)**
*   `claude -p "query"`
*   `--output-format json`
*   `--max-budget-usd`: 비용 제한 (cost limit)
*   파이프(pipe) 입력 지원

**스케줄링 및 원격 (Scheduling & Remote)**
*   `/loop`: 주기적 작업 (periodic tasks)
*   `/rc`: 원격 제어 (remote control)
*   `--remote`: 웹 세션 연결

### 설정 및 환경 (Settings & Environment)

**설정 파일 (Configuration Files)**
*   사용자 (User): `~/.claude/settings.json`
*   프로젝트 (Project): `.claude/settings.json`
*   로컬 (Local): `.claude/settings.local.json`
*   OAuth, MCP, 상태 (Status): `~/.claude.json`
*   프로젝트 MCP 서버 (Project MCP Server): `.mcp.json`

**핵심 설정 항목 (Key Configuration Items)**
*   `modelOverrides`, `autoMemoryDirectory`, `worktree.sparsePaths`

**환경 변수 (Environment Variables)**
*   `ANTHROPIC_API_KEY`, `ANTHROPIC_MODEL`, `CLAUDE_CODE_EFFORT_LEVEL`, `MAX_THINKING_TOKENS`, `ANTHROPIC_CUSTOM_MODEL_OPTION`, `CLAUDE_CODE_PLUGIN_SEED_DIR`

### 스킬 및 에이전트 (Skills & Agents)

**내장 스킬 (Built-in Skills)**
*   `/simplify`, `/batch`, `/debug`, `/loop`, `/claude-api`

**커스텀 스킬 위치 (Custom Skill Locations)**
*   프로젝트 (Project): `.claude/skills/<skill_name>/`
*   개인 (Personal): `~/.claude/skills/<skill_name>/`

**스킬 프론트매터 (Skill Frontmatter)**
*   `description`, `allowed-tools`, `model`, `effort`, `context`, `$ARGUMENTS`, `${CLAUDE_SKILL_DIR}`, `!cmd`

**내장 에이전트 (Built-in Agents)**
*   `Explore`, `Plan`, `General`, `Bash`

**에이전트 프론트매터 (Agent Frontmatter)**
*   `permissionMode`, `isolation`, `memory`, `background`, `maxTurns`, `SendMessage` (신규 자동 재개 기능)

### CLI 및 플래그 (CLI & Flags)

**핵심 명령 (Core Commands)**
*   `claude`, `claude "q"`, `claude -p "q"`, `claude -c`, `claude -r`, `claude update`

**주요 플래그 (Key Flags)**
*   `--model`, `-w`, `-n`, `--add-dir`, `--agent`, `--allowedTools`, `--output-format`, `--json-schema`, `--max-turns`, `--max-budget-usd`, `--console`, `--verbose`, `--bare`, `--channels`, `--remote`, `--chrome`

**권한 모드 (Permission Modes)**
*   `default`, `acceptEdits`, `plan`, `dontAsk`, `bypassPermissions`

**핵심 환경 변수 (Key Environment Variables)**
*   `ANTHROPIC_API_KEY`, `ANTHROPIC_MODEL`, `CLAUDE_CODE_EFFORT_LEVEL`, `MAX_THINKING_TOKENS`, `CLAUDE_CODE_MAX_OUTPUT_TOKENS`, `CLAUDE_CODE_DISABLE_CRON`

---

▲
click | 2일 전 | [-]

클로드 코드(Claude Code)와 코덱스(Codex) 둘 다 사용하고 있는데, 클로드 코드에서 '$' 기호가 없는 점이 다소 불편합니다. 하나의 프롬프트(prompt)에 여러 스킬(skill)을 명시하려 할 때 코덱스는 자연스럽게 동작하지만, 클로드 코드는 그렇지 못하다는 점이 아쉬운 부분입니다.

답변 달기

---

▲
GN⁺ | 5일 전 | [-]
**해커 뉴스(Hacker News) 의견**

저는 매일 클로드 코드(Claude Code)를 사용하지만 명령어를 자주 잊어버립니다. 그래서 클로드에게 공식 문서와 GitHub에서 모든 기능을 조사하게 하고, 단축키, 슬래시 명령어, 워크플로(workflow), 스킬 시스템(skill system), 메모리/`CLAUDE.md`, MCP 설정, CLI 플래그, 설정 파일을 한눈에 볼 수 있는 A4 가로 HTML 치트시트를 만들게 했습니다.
이 치트시트는 Mac/Windows 단축키를 자동으로 감지하며, 최신 버전과 변경 로그(changelog)를 표시합니다. 매일 크론 잡(cron job)이 변경 사항을 확인하여 자동 업데이트하며, 새로운 기능에는 'NEW' 배지(badge)가 붙습니다.
가볍고 무료이며 회원가입이 필요 없습니다. cc.storyfox.cz에서 Ctrl+P로 인쇄 가능하며 모바일에서도 작동합니다.

*   `'Ctrl+P로 인쇄 가능, 모바일에서도 작동'`이라는 문구가 흥미롭네요. 제 스마트폰에는 Ctrl 키가 없고, Mac에서는 아마 Cmd+P일 것 같습니다.
*   이 치트시트가 어떤 클로드 코드(Claude Code) 버전 기준인지 궁금합니다. 제 버전에는 `/cost` 명령어가 없네요.
*   `'^'` 기호는 컨트롤(Control) 키를 의미하며, `⌘`는 아닙니다.
*   혹시 소스 코드 공개 계획이 있으신가요?
*   훌륭한 작업입니다. 감사합니다.

*   저는 최근 CC 터미널에서 VS Code 확장으로 전환했는데, 훨씬 더 마음에 듭니다.
*   저도 마찬가지입니다. UI에서 작업하고, 리포지토리(repository) 파일을 탐색, 검토, 편집하기가 훨씬 더 쉬워졌습니다.

*   `'MCP'` 섹션에서 `'Local'` 앞의 `'~'` 표기는 잘못된 것 같습니다. 프로젝트별 설정은 단순히 `.claude.json`이어야 합니다.
*   `'CMD + V로 이미지 붙여넣기'`는 잘못된 정보입니다. Mac에서도 Windows와 마찬가지로 `Ctrl+V`를 사용합니다. `Cmd+V`는 텍스트 붙여넣기용입니다.
*   Warp Terminal에서는 Mac에서도 `Cmd+V`로 이미지를 붙여넣을 수 있습니다.
*   다른 명령어들도 비슷합니다. 예를 들어 외부 편집기를 여는 것도 Mac에서 `Cmd+G`가 아니라 `Ctrl+G`입니다.
*   Linux에서는 `Ctrl+Shift+V`를 사용하는 것으로 알고 있습니다. `Ctrl+V`는 다른 키 조합으로 인식됩니다.

*   실제로는 환경 변수(environment variable)가 훨씬 더 많습니다. 제가 좋아하는 것은 `IS_DEMO=1`인데, 이는 불필요한 환영 배너를 제거해줍니다.
*   관련 문서는 code.claude.com/docs/en/env-vars에서 확인할 수 있습니다.

*   `'프로젝트 규칙(project rules)'`이라는 개념이 실제로 존재하는지 궁금합니다.
*   `.claude/rules/`와 `~/.claude/rules/` 디렉터리가 있는데, 이것이 단순히 다른 프롬프트(prompt)에서 불러올 파일을 정리하는 용도인지 알고 싶습니다.

*   이러한 기능 요약 시트를 만들어주셔서 감사합니다. 새로운 기능이 자주 추가되는데, 한눈에 볼 수 있어 문서를 찾아볼 필요가 줄어듭니다.

*   클로드 코드(Claude Code)가 CLI(Command Line Interface) 측면에서 코덱스(Codex)보다 훨씬 앞서 있다는 점이 놀랍습니다.

*   저는 클로드 코드(Claude Code)로 자기 복제형 에이전트(agent)를 만들어봤습니다. 메인 브랜치(main branch)에서 5개의 Git 워크트리(worktree)를 분기하여 각각 독립적으로 작업하게 하고, 60초마다 성능을 분석하여 더 나은 방향으로 자신을 개선하도록 했습니다.
    43회 반복 후에는 어떤 웹사이트든 다양한 프로토콜(protocol) (WebSocket, GraphQL, gRPC-Web 등)을 타입드 JSON API(typed JSON API)로 변환하는 데 10~30분밖에 걸리지 않습니다.
    다음에는 4년치 주식·옵션 거래 데이터 263GB를 학습시켜 거래 전략을 찾게 할 예정입니다. 클로드 코드(Claude Code)가 AGI(Artificial General Intelligence)에 가장 먼저 도달할 것 같습니다.
    하지만 너무 느립니다. 입력이 자주 씹히고, TUI(Text-based User Interface)라 빠를 줄 알았는데 그렇지 않습니다.
    그런데 OpenAI가 인수한 사람들은 여전히 코덱스(Codex)가 '미래'라고 말합니다.
    실제로 성능 면에서는 코덱스(Codex)가 클로드 코드(Claude Code)보다 낫다고 느껴집니다.

*   페이지의 변경 로그(changelog) 링크를 보고, 변경 이력(change history)을 시각화(visualize)해봤습니다. ChatGPT에게 `CHANGELOG.md`의 일별 추가 항목 수를 그래프로 그리게 했는데, 대략적으로 맞는 것 같습니다.
    (imgur.com/a/tky9Pkz)

*   `'실행 취소(Undo) (입력 취소)'`는 `Ctrl + _` (Ctrl + 언더스코어)로 작동합니다. 클로드 코드(CC) 외부의 라인 에디터(line editor)에서도 동일하게 적용됩니다.

답변 달기

---

처음 오셨나요? | 사이트 이용법 | FAQ | About | 이용약관 | 개인정보 처리방침 | 블로그(Blog) | 목록(Lists) | RSS | 북마클릿(Bookmarklet)
X (트위터) | 페이스북(Facebook) | 긱뉴스봇: 슬랙(Slack), 잔디(Jandi), 디스코드(Discord), 팀즈(Teams), 두레이(Dooray!), 구글 챗(Google Chat), 스윗(Swit)
검색