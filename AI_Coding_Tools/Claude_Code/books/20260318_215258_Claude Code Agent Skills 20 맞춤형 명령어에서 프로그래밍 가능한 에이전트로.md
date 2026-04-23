# 번역 기사

- **원본 URL:** https://medium.com/towards-artificial-intelligence/claude-code-agent-skills-2-0-from-custom-instructions-to-programmable-agents-ab6e4563c176
- **번역 일시:** 2026-03-18 21:52:58

---

# Claude Code Agent Skills 2.0: 맞춤형 명령어에서 프로그래밍 가능한 에이전트로

## 사이드바 메뉴

*   작성
*   알림
    *   21
*   홈
*   라이브러리
*   프로필
*   스토리
*   통계
*   팔로잉
*   AI Advances
*   Artificial Intelligence in Plain English
*   Joe Njenga
*   Alex Dunlop
*   Algo Insights
*   The Latency Gambler
*   Civil Learning
*   Alain Airom (Ayrom)
*   Will Lockett
*   Yashwanth Sai
*   더 보기
*   Towards AI

저희는 엔터프라이즈 AI(Enterprise AI)를 구축하고, 저희가 배운 것을 가르칩니다. Towards AI 아카데미에서 10만 명 이상의 AI 실무자들과 함께하세요. 무료: 6일 에이전트 기반 AI 엔지니어링(Agentic AI Engineering) 이메일 가이드: https://email-course.towardsai.net/

출판물 팔로우

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

## Claude Code Agent Skills 2.0

회원 전용 스토리

## Claude Code Agent Skills 2.0: 맞춤형 명령어에서 프로그래밍 가능한 에이전트로

스킬은 더 이상 명령어가 아닙니다. 스킬은 프로그램입니다.

Rick Hightower
팔로우
17분 읽기
·
2026년 3월 9일

214

6

Claude Code의 스킬 시스템은 단순한 마크다운(markdown) 명령어에서 서브 에이전트(subagent) 실행, 동적 컨텍스트(dynamic context) 주입, 라이프사이클 훅(lifecycle hook) 및 공식 평가를 갖춘 완전한 프로그래밍 가능한 에이전트(programmable agent) 플랫폼으로 발전했습니다.

이제 Claude Code 스킬은 프로그램이 되었습니다.

Claude Code가 처음 명령어를 도입했을 때, 방식은 간단했습니다. `.claude/commands/`에 마크다운 파일을 넣고, 몇 가지 지침을 제공한 다음, 슬래시 명령어(slash command)로 호출하는 식이었습니다. 유용했지만, 한계가 있었습니다. 스킬은 폴더가 있는 맞춤형 명령어에 불과했습니다.

그 시대는 끝났습니다.

Claude Code의 최신 버전은 스킬 작동 방식을 근본적으로 재설계했습니다. 이제 명령어와 스킬은 통합되었습니다. 스킬은 자체 컨텍스트 창(context window)을 가진 격리된 서브 에이전트를 생성할 수 있습니다. 셸(shell) 명령어는 Claude가 프롬프트(prompt)를 보기 전에 스킬 프롬프트에 실시간 데이터를 주입할 수 있습니다. 스킬은 Claude가 사용하는 도구를 제한하고, 모델을 재정의하며, 라이프사이클 이벤트에 훅을 걸고(hook into), 포크된 컨텍스트(forked contexts)에서 실행될 수 있습니다. 대규모 변경 사항을 코드베이스 전체에 분해하고 별도의 Git 워크트리(git worktrees)에서 병렬 에이전트를 생성하는 스킬을 포함하여, 네 가지 강력한 번들 스킬이 즉시 제공됩니다.

커뮤니티의 일부에서는 이를 “Agent Skills 2.0”이라고 부르며, 이 이름은 적절합니다. 스킬은 더 이상 명령어가 아닙니다. 스킬은 프로그램입니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### Skills 2.0 — 컨텍스트 및 병렬 실행을 지원하는 에이전트 기반 스킬

## 무엇이 달라졌는가

가장 눈에 띄는 변화는 간단합니다. 명령어와 스킬이 통합되었다는 점입니다. `.claude/commands/`의 파일은 여전히 작동합니다. 하지만 `.claude/skills/`에 있는 스킬은 디렉토리, 더 풍부한 프론트매터(frontmatter), 지원 파일, 그리고 이 글에서 설명하는 모든 새로운 기능을 지원하므로 이제 권장되는 경로입니다.

이러한 통합 아래에는 개별적으로 이해할 가치가 있는 더 심층적인 변화들이 있습니다.

**번들 슬래시 명령어(bundled slash commands)**는 모든 Claude Code 설치에 함께 제공됩니다.
*   `/simplify`는 세 개의 병렬 검토 에이전트를 생성합니다 — 이 에이전트들은 코드 재사용, 코드 품질, 효율성을 검토합니다.
*   `/batch`는 코드베이스 전체에 걸쳐 대규모 변경 사항을 조율합니다 — 작업을 5개에서 30개의 독립적인 단위로 분해하고, 승인을 위한 계획을 제시한 다음, 격리된 Git 워크트리에서 단위별로 하나의 백그라운드 에이전트를 생성합니다.
*   `/debug`는 Claude Code 세션의 문제 해결을 돕습니다 — 잘못 구성된 MCP(mcp), 도구 호출 실패 등.
*   `/claude-api`는 프로젝트 언어에 대한 Claude API 참조 자료를 로드합니다.
이것들은 번들 스킬과 유사하지만 내장되어 있습니다.

**스킬은 서브 에이전트 실행(Subagent execution)**을 허용하며, 어떤 스킬이든 격리된 컨텍스트 창(context window)에서 실행될 수 있습니다. 프론트매터에 `context: fork`를 추가하면, 스킬 콘텐츠가 20만 토큰 컨텍스트를 가진 새로운 서브 에이전트의 태스크 프롬프트(task prompt)가 됩니다. 주요 대화는 깨끗하게 유지됩니다.

**스킬은 동적 컨텍스트 주입(Dynamic context injection)**을 허용하며, 이는 프롬프트가 Claude에 도달하기 전에 셸(shell) 명령어를 미리 처리합니다. `!` 백틱(backtick) 구문은 명령어를 실행하고 그 출력을 대체하여, Claude가 오래된 텍스트 대신 실시간 데이터를 받도록 합니다.
예시:
`! git branch`

**세분화된 권한(Granular permissions)**은 누가 스킬을 호출할 수 있는지(사용자, Claude, 또는 둘 다), 어떤 도구에 접근할 수 있는지, 그리고 어떤 모델에서 실행되는지 제어합니다.

agentskills.io의 **Agent Skills 오픈 표준(open standard)**은 스킬이 Claude Code에 종속되지 않음을 의미합니다. 이 형식은 여러 AI 도구에서 작동하며, Claude Code는 호출 제어 및 서브 에이전트 실행과 같은 기능을 추가하여 이를 확장합니다.

## 스킬의 구조

모든 스킬은 `SKILL.md` 파일을 진입점(entry point)으로 하는 자체 디렉토리에 있습니다.

```
my-skill/
├── SKILL.md           # 주요 명령어 (필수)
├── reference.md       # 상세 문서 (요청 시 로드)
├── examples/
│   └── sample.md      # 예시 출력
└── scripts/
    └── validate.sh    # 실행 가능한 스크립트
```

디렉토리 구조가 중요한 이유는 다음과 같습니다. 주요 명령어를 간결하게 유지하면서도 Claude가 필요할 때 풍부한 지원 자료에 접근할 수 있도록 하기 위함입니다. Claude는 호출 시 `SKILL.md`를 로드하고, 태스크가 필요할 때만 다른 파일을 참조합니다.

`SKILL.md` 파일은 두 부분으로 구성됩니다. 동작을 구성하는 YAML 프론트매터(YAML frontmatter)와 실제 명령어가 포함된 마크다운(markdown) 콘텐츠입니다.

```yaml
---
name: explain-code
description: 시각적 다이어그램과 비유를 사용하여 코드를 설명합니다. 코드 작동 방식 설명 시 또는
  사용자가 "이것은 어떻게 작동합니까?"라고 물을 때 사용하세요.
---
```

코드를 설명할 때 항상 다음을 포함하세요:

1.  **비유로 시작하기**: 코드를 일상생활의 무언가에 비유하세요.
2.  **다이어그램 그리기**: ASCII 아트(ASCII art)를 사용하여 흐름, 구조 또는 관계를 보여주세요.
3.  **코드를 단계별로 설명하기**: 어떤 일이 발생하는지 단계별로 설명하세요.
4.  **주의할 점 강조하기**: 흔한 실수나 오해는 무엇입니까?

`name` 필드는 슬래시 명령어(`/explain-code`)가 됩니다. `description`은 대화 내용과 일치할 때 Claude가 스킬을 자동으로 로드할지 여부를 결정하는 방식입니다. "코드에 도움을 줍니다"와 같은 모호한 설명은 거의 올바르게 트리거되지 않습니다. "다이어그램과 비유를 사용하여 코드를 설명합니다"와 같은 구체적인 설명은 Claude가 적절한 순간에 스킬을 로드할 충분한 신호를 제공합니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.
이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 스킬 전용 프론트매터

## 스킬 저장 위치

스킬은 위치에 따라 범위가 지정되며, 높은 우선순위 수준이 낮은 수준을 재정의합니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

*   **엔터프라이즈 수준(Enterprise Level)**: 조직 설정을 통해 관리되며, 조직의 모든 사용자에게 적용됩니다.
*   **개인 수준(Personal Level)**: `~/.claude/skills/<name>/SKILL.md`에 위치하며, 모든 프로젝트에 적용됩니다.
*   **프로젝트 수준(Project Level)**: `.claude/skills/<name>/SKILL.md`에 위치하며, 현재 프로젝트에만 적용됩니다.
*   **플러그인 수준(Plugin Level)**: `<plugin>/skills/<name>/SKILL.md`에 위치하며, 플러그인이 활성화된 모든 곳에 적용됩니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 스킬은 워크플로우 및 모노레포에 자동으로 범위가 지정됩니다.

Claude Code는 또한 중첩된 디렉토리에서도 스킬을 자동으로 찾아냅니다. 예를 들어, `packages/frontend/`에서 파일을 편집하는 경우, `packages/frontend/.claude/skills/`에서 스킬을 가져옵니다. 이를 통해 스킬은 추가 구성 없이 모노레포(monorepo) 설정에서 자연스럽게 작동합니다.

## 프론트매터 전체 참조

사용 가능한 모든 프론트매터 필드는 다음과 같습니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

*   `name` (아니오) — 표시 이름 및 슬래시 명령어입니다. 생략 시 디렉토리 이름을 사용합니다.
*   `description` (권장) — 스킬의 기능입니다. Claude는 이를 사용하여 스킬을 로드할 시점을 결정합니다.
*   `argument-hint` (아니오) — 자동 완성(autocomplete) 시 표시되는 힌트입니다(예: `[issue-number]`).
*   `disable-model-invocation` (아니오) — `true`는 Claude가 자동으로 로드하는 것을 방지합니다. 수동으로 `/name`만 가능합니다.
*   `user-invocable` (아니오) — `false`는 `/` 메뉴에서 숨깁니다. Claude 전용 백그라운드 지식입니다.
*   `allowed-tools` (아니오) — 스킬이 활성화되어 있을 때 Claude가 요청 없이 사용할 수 있는 도구입니다.
*   `model` (아니오) — 스킬이 활성화되어 있을 때 모델을 재정의합니다.
*   `context` (아니오) — `fork`는 격리된 서브 에이전트 컨텍스트에서 실행됩니다.
*   `agent` (아니오) — `context: fork`일 때의 서브 에이전트 유형입니다. 옵션: Explore, Plan, general-purpose 또는 사용자 정의.
*   `hooks` (아니오) — 이 스킬의 라이프사이클에 범위가 지정된 훅(hook)입니다.

## 4가지 주요 번들 스킬 유사 명령어

Claude Code는 이제 새로운 시스템의 모든 기능을 보여주는 네 가지 내장 스킬 유사 명령어를 제공합니다. 각 명령어는 이전에는 수동 조정이 필요했거나 작업을 완전히 포기해야 했던 문제를 해결합니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 번들 스킬은 즉시 제공됩니다.

### /simplify

기능을 구현하거나 버그를 수정한 후 `/simplify`를 실행하면 세 개의 검토 에이전트가 병렬로 생성됩니다.

*   **코드 재사용(Code reuse)**: 중복을 줄일 기회를 찾습니다.
*   **코드 품질(Code quality)**: 버그, 불분명한 논리, 유지 관리 문제를 확인합니다.
*   **효율성(Efficiency)**: 성능 개선 사항을 식별합니다.

에이전트들은 동시에 실행되어 발견 사항을 통합하고 수정 사항을 적용합니다. 검토의 초점을 맞출 수도 있습니다: `/simplify focus on memory efficiency`.

이것은 린트(lint) 검사가 아닙니다. 각 에이전트는 최근 변경된 파일을 읽고, 더 넓은 코드베이스 컨텍스트를 이해하며, 목표에 맞는 개선을 수행합니다. 병렬 실행은 세 번의 철저한 검토가 한 번의 순차적인 통과보다 빠르게 완료됨을 의미합니다. 이것이 `context: fork`의 구체적인 주요 가치입니다. 이전에는 대화를 방해했던 작업이 이제 백그라운드에서 실행됩니다.

### /batch

이것은 가장 야심 찬 번들 스킬입니다. `/batch`에 변경 사항에 대한 설명을 제공하면 다음을 수행합니다.

*   범위를 이해하기 위해 코드베이스를 조사합니다.
*   작업을 5개에서 30개의 독립적인 단위로 분해합니다.
*   승인을 위한 계획을 제시합니다.
*   단위별로 하나의 에이전트를 생성하며, 각 에이전트는 격리된 Git 워크트리(git worktree)에 있습니다.
*   각 에이전트는 구현, 테스트를 수행하고 풀 리퀘스트(Pull Request)를 엽니다.

예시: `/batch migrate src/ from Solid to React`. Claude는 어떤 컴포넌트가 마이그레이션(migration)이 필요한지 분석하고, 이를 독립적인 단위로 그룹화하며, 자체 워크트리에서 각 단위에 대한 병렬 에이전트를 생성하고, 검토 및 병합할 수 있는 개별 PR을 생성합니다.

이 스킬은 Git 리포지토리(repository)를 필요로 하며, 격리를 위해 Git 워크트리를 사용합니다. 각 에이전트는 별도의 브랜치(branch)에서 작업하므로 병렬 실행 중 병합 충돌(merge conflicts)이 발생하지 않습니다. 트레이드오프(trade-off)는 분명합니다. 분해 품질이 결과 품질을 결정합니다. Claude가 작업을 제대로 분할하지 못하면, 일부 에이전트는 다른 에이전트가 아직 해결하지 못한 종속성(dependencies)에 의해 막힐 수 있습니다. 승인 전에 계획을 검토하고, 최상의 결과를 위해 구체적이고 범위가 명확한 변경 설명을 사용하세요.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 격리된 환경에서 복잡한 리팩토링을 동시에 실행

### /debug

Claude Code 자체가 예상대로 작동하지 않을 때, `/debug`는 세션 디버그 로그(debug log)를 읽고 문제를 진단합니다. 분석에 초점을 맞추기 위해 선택적 설명을 전달할 수 있습니다: `/debug why is the Bash tool failing?`

### /claude-api

코드에서 `anthropic`, `@anthropic-ai/sdk`, 또는 `claude_agent_sdk`를 임포트(import)할 때 이 스킬은 자동으로 활성화됩니다. 이 스킬은 도구 사용, 스트리밍(streaming), 배치(batches), 구조화된 출력 및 일반적인 문제점을 다루는 프로젝트 언어(Python, TypeScript, Java, Go, Ruby, C#, PHP 또는 cURL)에 대한 API 참조 자료를 로드합니다.

이것은 수동으로 호출할 필요 없는 스킬의 좋은 예입니다. 이 스킬은 Anthropic SDK를 사용하는 프로젝트에서 작업할 때마다 Claude가 해당 SDK에 대해 더 스마트해지도록 만듭니다.

## 단순한 명령어에서 완전한 에이전트 프로그래밍 플랫폼으로

### 서브 에이전트 실행: `context: fork`

`context: fork` 프론트매터 필드는 Skills 2.0에서 가장 중요한 단일 추가 기능입니다. 이것은 스킬을 인라인(inline) 명령어에서 격리된 에이전트로 변환합니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 단순한 명령어에서 완전한 에이전트 프로그래밍 플랫폼으로

스킬이 `context: fork`와 함께 실행될 때:

*   새로운 컨텍스트 창(conversation history 없이 깨끗한 상태)이 생성됩니다.
*   스킬의 마크다운 콘텐츠가 서브 에이전트의 태스크 프롬프트(task prompt)가 됩니다.
*   `agent` 필드가 실행 환경을 결정합니다.
*   결과는 요약되어 주요 대화로 반환됩니다.

주요 대화 컨텍스트는 완전히 깨끗하게 유지됩니다. 서브 에이전트가 자체 공간에서 모든 어려운 작업을 처리합니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 포크된 서브 에이전트 실행으로 무거운 작업 부하 격리

이것이 왜 중요할까요? 오래 실행되는 연구 또는 분석 작업은 중간 생각과 도구 호출만으로 수천 개의 토큰을 소비할 수 있습니다. 컨텍스트 격리(context isolation)가 없으면, 그 비용은 주요 대화 창에 쌓여 미래 컨텍스트를 압박합니다. `context: fork`를 사용하면 서브 에이전트가 그 모든 오버헤드(overhead)를 흡수합니다. 사용자의 대화는 요약만 보게 됩니다.

```yaml
---
name: deep-research
description: 코드베이스에서 주제를 철저히 연구합니다.
context: fork
agent: Explore
---
$ARGUMENTS를 철저히 연구합니다:
1. Glob 및 Grep을 사용하여 관련 파일을 찾습니다.
2. 코드를 읽고 분석합니다.
3. 특정 파일 참조와 함께 발견 사항을 요약합니다.
```

`agent` 필드는 내장 유형(Explore, Plan, general-purpose) 또는 `.claude/agents/`에 정의된 모든 사용자 정의 에이전트를 허용합니다. 생략하면 `general-purpose`가 기본값으로 사용됩니다.

한 가지 중요한 제약 사항: `context: fork`는 명시적인 태스크(task) 명령어가 있는 스킬에만 의미가 있습니다. 스킬에 구체적인 태스크 없이 "이러한 API 규칙을 사용하세요"와 같은 지침만 포함되어 있다면, 서브 에이전트는 실행할 내용이 없는 지침을 받아 의미 있는 출력 없이 반환됩니다. 포크(fork) 스킬은 태스크용이며; 지침 스킬은 인라인으로(inline) 실행되어야 합니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### Skills 2.0 아키텍처의 네 가지 기둥

### 동적 컨텍스트 주입

`!` 백틱(backtick) 구문은 스킬 콘텐츠가 Claude에 도달하기 전에 셸(shell) 명령어를 실행합니다. 명령어 출력은 플레이스홀더(placeholder)를 대체하여, Claude가 플레이스홀더 텍스트 대신 실제 데이터를 받도록 합니다.

```yaml
---
name: pr-summary
description: 풀 리퀘스트의 변경 사항을 요약합니다.
context: fork
agent: Explore
allowed-tools: Bash(gh *)
---

## 풀 리퀘스트 컨텍스트
- PR diff: !`gh pr diff`
- PR comments: !`gh pr view --comments`
- Changed files: !`gh pr diff --name-only`

## 당신의 작업
다음 사항에 초점을 맞춰 이 풀 리퀘스트를 요약하세요:
1. 무엇이 변경되었고 그 이유는 무엇인지
2. 잠재적인 위험 또는 우려 사항
3. 제안된 검토 초점 영역
```

이 스킬이 실행될 때, 각 `!` 백틱 명령어는 즉시 실행됩니다. `gh pr diff`가 실행되고, 그 출력이 플레이스홀더를 대체하며, Claude는 실시간 PR 데이터로 완전히 렌더링(rendered)된 프롬프트를 보게 됩니다. 이것은 전처리(preprocessing)입니다. Claude는 이 명령어를 직접 실행하지 않습니다. 데이터는 이미 계산된 상태로 도착합니다.

이러한 분리가 중요한 두 가지 이유가 있습니다. 첫째, 더 빠릅니다. Claude는 이미 가지고 있는 컨텍스트를 수집하기 위해 도구를 호출할 필요가 없습니다. 둘째, 더 깔끔합니다. 스킬 프롬프트는 도구 호출의 연쇄가 아닌 문서처럼 읽히며, 이는 Claude가 데이터 수집보다 분석에 즉시 집중할 수 있음을 의미합니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### Claude가 보기 전에 프롬프트에 실시간 데이터 직접 주입

### 문자열 대체

스킬은 동적 값을 위한 변수 대체(variable substitution)를 지원합니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

*   `$ARGUMENTS` - 호출 시 전달된 모든 인자
*   `$ARGUMENTS[N]` 또는 `$N` - 인덱스별 특정 인자
*   `${CLAUDE_SESSION_ID}` - 현재 세션 ID
*   `${CLAUDE_SKILL_DIR}` - `SKILL.md`가 포함된 디렉토리

위치 인자(positional arguments) 예시:

```yaml
---
name: migrate-component
description: 프레임워크 간에 컴포넌트를 마이그레이션합니다.
---

$0 컴포넌트를 $1에서 $2로 마이그레이션합니다.
모든 기존 동작 및 테스트를 보존합니다.
```

`/migrate-component SearchBar React Vue`를 실행하면 각 위치 인자가 대체됩니다. 스킬은 매번 다시 작성해야 하는 정적 프롬프트가 아니라, 특정 값으로 호출하는 템플릿(template)이 됩니다.

## 권한 제어 및 호출

Skills 2.0은 누가 스킬을 호출할 수 있는지, 그리고 스킬이 무엇을 할 수 있는지에 대해 세분화된 제어(fine-grained control)를 제공합니다. 이러한 제어를 올바르게 설정하는 것이 안전한 자동화와 예상치 못한 자동화를 구분하는 요소입니다.

### 호출 제어

두 개의 프론트매터 필드가 누가 스킬을 트리거하는지 결정합니다.

*   `disable-model-invocation: true`: 사용자만 호출할 수 있음을 의미합니다. 부작용이 있는 워크플로우에 사용하세요: `/deploy`, `/commit`, `/send-slack-message`. 코드가 준비되었다고 Claude가 배포를 결정하는 것을 원치 않을 것입니다.
*   `user-invocable: false`: Claude만 호출할 수 있음을 의미합니다. 명령어로 실행할 수 없는 백그라운드 지식에 사용하세요. `legacy-system-context` 스킬은 오래된 시스템이 어떻게 작동하는지 설명합니다. Claude는 관련될 때 이를 참조하지만, `/legacy-system-context`는 사용자에게 의미 있는 동작이 아닙니다.

다음은 완전한 호출 매트릭스(matrix)입니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

*   **기본값(제한 없음)**: 사용자 및 Claude 모두 스킬을 호출할 수 있으며, 항상 컨텍스트에 로드됩니다.
*   `disable-model-invocation: true`: 사용자만 스킬을 호출할 수 있으며(Claude는 불가능), 사용자가 호출하지 않으면 컨텍스트에 로드되지 않습니다.
*   `user-invocable: false`: Claude만 스킬을 호출할 수 있으며(사용자는 불가능), Claude가 참조할 수 있도록 항상 컨텍스트에 로드됩니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### Claude가 시스템과 상호 작용하는 방식과 시점을 정확하게 제어

### 도구 제한

`allowed-tools` 필드는 스킬이 활성화되어 있을 때 Claude가 할 수 있는 작업을 제한합니다.

```yaml
---
name: safe-reader
description: 변경 없이 파일을 읽습니다.
allowed-tools: Read, Grep, Glob
---
```

이것은 읽기 전용 모드를 생성합니다. Claude는 파일을 탐색할 수 있지만 수정할 수는 없습니다. 이는 정보를 수집하거나 코드를 감사하는 스킬에 유용합니다. 의도치 않은 편집의 위험 없이 Claude의 분석 이점을 얻을 수 있습니다.

### 권한 규칙

특정 스킬을 전역적으로 허용하거나 거부할 수 있습니다.

```
# 특정 스킬만 허용
Skill(commit)
Skill(review-pr *)
# 특정 스킬 거부
Skill(deploy *)
```

구문 `Skill(name)`은 정확히 일치하고; `Skill(name *)`은 접두사와 모든 인자에 일치합니다.

## 지원 파일 및 시각적 출력

스킬은 디렉토리에 여러 파일을 포함할 수 있습니다. 이는 `SKILL.md`의 초점을 유지하면서 Claude가 필요할 때 상세 자료에 접근할 수 있도록 합니다.

```
codebase-visualizer/
├── SKILL.md                    # 명령어 (500라인 미만)
├── scripts/
│   └── visualize.py            # 대화형 HTML 생성
└── reference.md                # 상세 문서
```

`SKILL.md`에서 지원 파일을 참조하여 Claude가 어떤 내용을 포함하고 언제 로드해야 하는지 알 수 있도록 합니다.

```markdown
## 추가 자료
- 전체 API 세부 정보는 [reference.md](reference.md)를 참조하세요.
- 사용 예시는 [examples.md](examples.md)를 참조하세요.
```

시각적 출력 패턴은 특히 강력합니다. 스킬은 대화형 HTML 파일을 생성하는 모든 언어의 스크립트를 번들로 묶을 수 있습니다. 번들로 제공되는 `codebase-visualizer` 예시는 파일 크기, 유형 색상 및 집계 통계를 포함하는 접을 수 있는 트리 뷰(tree view)를 생성합니다. Claude는 Python 스크립트를 실행하고, HTML을 생성하며, 브라우저에서 엽니다. 이 패턴은 종속성 그래프, 테스트 커버리지 보고서, API 문서 또는 스키마 시각화에도 똑같이 잘 작동합니다. 스킬은 인텔리전스를 제공하고; 스크립트는 출력 형식을 제공합니다.

실제로 보려면 Anthropic의 스킬이 포함된 Playground Plugin을 사용해 보세요.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 풍부한 시각적 출력을 생성하기 위한 번들 스크립트

## 스킬 테스트 및 평가

스킬을 구축하는 것은 작업의 절반에 불과합니다. 스킬이 실제로 Claude의 출력을 개선하는지 테스트하는 것이 나머지 절반이며, 대부분의 사람이 건너뛰는 부분입니다.

### 세 가지 테스트 차원

**트리거링 테스트(Triggering tests)**는 스킬이 필요한 시점에 로드되고 불필요할 때는 조용히 유지되는지 확인합니다. 10~20개의 샘플 쿼리로 테스트하세요: 명확히 일치하는 쿼리("이 코드를 설명해 줘"), 바꿔 말한 쿼리("이것이 어떻게 작동하는지 단계별로 알려줘"), 그리고 관련 없는 주제("이 버그를 고쳐줘"). 관련 쿼리에서 90%의 적중률과 0%의 오탐(false triggers)을 목표로 합니다.

**기능 테스트(Functional tests)**는 스킬이 올바른 출력을 생성하는지 확인합니다. 정상 경로(happy path), 엣지 케이스(edge cases), 오류 시나리오(error scenarios)를 테스트하세요. 배포 스킬의 경우: 먼저 테스트를 실행하는가? 빌드 실패를 처리하는가? 배포 성공을 확인하는가?

**위험 및 품질 테스트(Risk and quality tests)**는 A/B 비교를 사용하여 스킬이 실제로 도움이 되는지 측정합니다. 스킬이 활성화된 경우와 그렇지 않은 경우를 비교하는 5~8개의 병렬 테스트를 실행하세요. 출력 품질, 토큰 사용량, 작업 완료 속도를 비교합니다. 더 나은 출력을 생성하지만 세 배 더 많은 토큰을 소모하는 스킬은 모든 세션에서 유지할 가치가 없을 수도 있습니다.

### 반복 루프

스킬 개발을 위한 권장 워크플로우는 다음과 같습니다.

1.  **하나의 어려운 작업으로 시작합니다.** Claude가 도움 없이는 어려워하는 작업을 찾으세요.
2.  **Claude가 성공할 때까지 반복합니다.** 명령어를 조정하고, 예시를 추가하며, 개선합니다.
3.  **성공적인 접근 방식을 스킬로 추출합니다.** 효과가 있었던 것을 캡처합니다.
4.  **테스트 커버리지(test coverage)를 확장합니다.** 변형, 엣지 케이스 및 관련 시나리오를 테스트합니다.
5.  **코드처럼 버전 관리합니다.** 변경 사항을 커밋(commit)하고, 변경된 내용과 이유를 문서화하며, 성능이 저하되면 롤백(roll back)합니다.

이 루프는 일반화를 시도하기 전에 실제 실패에 스킬의 기반을 두기 때문에 효과적입니다. "이것이 유용할 것 같아요"라고 생각하여 구축된 스킬은 "Claude가 이 특정 작업에서 실패했고, 이렇게 고쳤습니다"라고 구축된 스킬보다 종종 성능이 떨어집니다. 저는 이런 작업을 많이 합니다.

워크플로우를 숙달하면, Claude Code가 원하는 작업을 수행하도록 하는 데 필요했던 비밀 프롬프트와 마법 주문을 기억할 필요가 없도록 에이전트 기반 스킬로 정규화합니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 스킬 테스트 및 평가

### 스킬 생성기(Skill Creator) 사용

스킬 생성기 도구(그 자체로 스킬로 사용 가능)는 이 과정의 대부분을 자동화합니다. 이것은 설명으로부터 스킬을 생성하고, 스킬 활성화 성능과 기준 성능을 비교하는 A/B 벤치마크(benchmark)를 실행하며, 기존 스킬의 문제를 식별하고, 더 나은 트리거링을 위해 모호한 설명을 다시 작성하며, 평가 보고서를 생성합니다. 이 작업에 대한 대규모 업데이트를 방금 진행했습니다.

실패하는 스킬에 “이 채팅의 문제를 사용하여 이 스킬을 개선하세요”라고 제공하면, 실패 패턴을 분석하고 목표에 맞는 수정을 제안할 것입니다.

## 첫 스킬 구축: 빠른 시작

작동하는 스킬을 만드는 가장 빠른 경로는 다음과 같습니다.

```bash
# 1. 디렉토리 생성
mkdir -p ~/.claude/skills/review-pr

# 2. SKILL.md 작성
cat > ~/.claude/skills/review-pr/SKILL.md << 'EOF'
---
name: review-pr
description: 코드 품질, 보안 및 정확성을 위해 풀 리퀘스트를 검토합니다.
disable-model-invocation: true
context: fork
agent: general-purpose
allowed-tools: Bash(gh *), Read, Grep, Glob
---

현재 풀 리퀘스트를 검토합니다:

## 풀 리퀘스트 데이터
- Diff: !`gh pr diff`
- Description: !`gh pr view`
- Changed files: !`gh pr diff --name-only`

## 검토 체크리스트
1. **정확성**: 코드가 PR 설명대로 작동합니까?
2. **보안**: 주입, 인증 또는 데이터 노출 위험이 있습니까?
3. **성능**: N+1 쿼리, 불필요한 할당 또는 느린 경로가 있습니까?
4. **테스트**: 변경 사항이 테스트로 커버됩니까?
5. **스타일**: 프로젝트 규칙을 따릅니까?

모든 발견 사항에 대해 특정 파일 및 라인 참조를 제공하세요.
EOF

# 3. 테스트 (오픈된 PR이 있는 프로젝트에서)
# /review-pr
```

이 스킬은 여러 Skills 2.0 기능이 함께 작동하는 것을 보여줍니다. `context: fork`는 PR 분석을 주요 대화에서 분리합니다. `disable-model-invocation`은 사용자가 항상 의도적으로 검토를 트리거하도록 보장합니다. `allowed-tools`는 Claude가 `gh` 명령어를 읽고 실행하는 것만을 허용하도록 범위를 지정합니다. `!` 백틱 명령어는 Claude가 프롬프트를 읽기 전에 실시간 PR 데이터를 주입합니다.

이러한 각 선택은 의도적입니다. 부작용이 있는 PR 검토는 위험할 것입니다. 대규모 변경 사항에 대해 주요 컨텍스트를 소비하는 PR 검토는 답답할 것입니다. 임시로 도구를 호출하는 PR 검토는 느릴 것입니다. 이 조합은 세 가지 문제를 모두 해결합니다.

## 모범 사례

*   **스킬을 500라인 미만으로 유지합니다.** 상세한 참조 자료는 별도의 파일로 옮기세요.
*   **구조화된 형식을 사용합니다.** 글머리 기호(bullet points)와 번호 목록은 산문보다 적은 토큰을 소비합니다.
*   **구체적인 예시를 포함합니다.** 모호함을 줄이기 위해 올바른 동작과 잘못된 동작을 보여주세요.
*   **구체적인 설명을 작성합니다.** "보안 문제에 대한 풀 리퀘스트 검토"가 "코드에 도움을 줍니다"보다 더 잘 트리거됩니다.
*   **조건부로 로드합니다.** 모든 세션에 모든 스킬을 추가하지 말고; 태스크별 로드(task-specific loading)를 사용하세요.
*   **코드처럼 스킬을 버전 관리합니다.** 변경 사항을 커밋하고, 이유를 문서화하며, 회귀(regressions) 시 롤백하세요.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 반복해야 하는 어려운 작업이 있다면, 그것을 스킬로 만드세요.

저는 종종 배포 단계가 포함된 릴리스(release)를 위한 스킬을 만듭니다. 예를 들어, `main` 브랜치에서 분기(branch)하고, 새 버전으로 태그(tag)를 지정하며, 버전을 업데이트하고, PyPi에 게시하고, 변경 로그(change log)를 생성하고, Github에 릴리스 페이지를 만들고 PyPi 릴리스 링크를 추가하는 것과 같습니다. 복잡한 일련의 단계를 기억해야 한다면, 이는 에이전트 기반 스킬의 좋은 후보일 것입니다.

## 더 큰 그림

Skills 2.0은 AI 코딩 어시스턴트(coding assistant)에 대한 우리의 사고방식에 변화를 가져왔습니다. Claude Code는 더 이상 대화하는 도구가 아닙니다. 이것은 사용자가 프로그래밍하는 플랫폼입니다.

진행 과정은 명확합니다. `CLAUDE.md`는 프로젝트 수준 명령어를 제공했고, 명령어는 슬래시로 호출 가능한 워크플로우를 제공했습니다. Skills 1.0은 디렉토리와 지원 파일을 추가했습니다. Skills 2.0은 서브 에이전트 실행, 동적 컨텍스트 주입, 라이프사이클 훅, 권한 제어 및 공식 평가를 추가합니다. 각 단계는 "맞춤형 프롬프트(custom prompts)"에서 "에이전트 프로그램(agent programs)"으로 더 나아갑니다.

agentskills.io의 Agent Skills 오픈 표준은 이것이 독점적인 락인(lock-in)이 아님을 의미합니다. Claude Code용으로 작성한 스킬은 여러 AI 도구에서 작동할 수 있습니다. 이 형식은 설계상 이식성(portable)을 가집니다.

개인적인 `review-pr` 스킬을 구축하든, 수십 개의 전문 에이전트가 포함된 플러그인(plugin)을 배포하든, 인프라(infrastructure)는 동일합니다. 즉, `SKILL.md` 파일, 일부 선택적 지원 파일, 그리고 스킬이 어떻게 작동하는지 정확히 제어하는 프론트매터 필드 세트입니다.

이미지를 전체 크기로 보려면 Enter 키를 누르거나 클릭하세요.

### 이식 가능한 에이전트 기반 스킬 구축

도구는 여기 있습니다. 문제는 당신이 그 도구로 무엇을 구축할 것인가입니다.

Claude Code 스킬 문서: code.claude.com/docs/en/skills. Agent Skills 오픈 표준: agentskills.io.

## 저자 소개

릭 하이타워(Rick Hightower)는 포춘 100대 금융 서비스 회사에서 ML/AI 개발을 이끌었던 기술 임원이자 데이터 엔지니어입니다. 그는 Claude Code, Gemini, Copilot, Cursor를 포함한 30개 이상의 코딩 에이전트를 지원하는 범용 에이전트 스킬 설치 프로그램인 `skilz`를 만들었고, 세계 최대의 에이전트 기반 스킬 마켓플레이스(marketplace)를 공동 설립했습니다. LinkedIn 또는 Medium에서 Rick Hightower와 연결하세요. 릭은 오랫동안 활발하게 에이전트 개발, 생성형 AI(GenAI), 에이전트 및 에이전트 기반 워크플로우를 수행해 왔습니다. 그는 많은 에이전트 기반 프레임워크(framework)와 도구의 저자입니다. 그는 AI를 채택하려는 팀에 핵심적인 깊은 지식을 제공합니다.

## 제가 작성한 에이전트 기반 스킬 관련 글.

### Agent Skills 관련 글
**2026년 2월**
*   2월 23일: 컨텍스트 엔지니어링: 에이전트 — 적절한 순간에 올바른 규칙을 주입하기 (Context Engineering: Agents — Injecting the Right Rules at the Right Moment)
*   2월 22일: 수동 에이전트 스킬 호출의 종말: 이벤트 기반 AI 에이전트 (The End of Manual Agent Skill Invocation: Event-Driven AI Agents)
*   2월 19일: Agent RuleZ로 적절한 시점에 에이전트 스킬 활성화하기 (Activating Agent Skills at the Right Time with Agent RuleZ)
*   2월 11일: Agent RuleZ: AI 코딩 에이전트를 위한 결정론적 정책 엔진 (Agent RuleZ: A Deterministic Policy Engine for AI Coding Agents)
*   2월 5일: Agent Brain: AI 코딩 어시스턴트를 위한 코드 우선 RAG 시스템 (Agent Brain: A Code-First RAG System for AI Coding Assistants)
*   2월 4일: 승인 지옥에서 바로 실행으로: Claude Code 2.1에서 에이전트 스킬이 서브 에이전트를 어떻게 관리하는가 (From Approval Hell to Just Do It: How Agent Skills Fork Governed Sub-Agents in Claude Code 2.1)
*   2월 3일: Agent Brain: AI 코딩 에이전트에게 전체 엔터프라이즈에 대한 완전한 이해 제공하기 (Agent Brain: Giving AI Coding Agents a Full Understanding of Your Entire Enterprise)
*   2월 2일: Claude Code 2.1 릴리스로 에이전트 스킬 더 빠르게 구축하기 (Build Agent Skills Faster with Claude Code 2.1 Release)

**1월**
*   1월 29일: Context7 위저드를 사용하여 10분 만에 첫 에이전트 스킬 구축하고, 시간을 절약하세요 (Build Your First Agent Skill in 10 Minutes Using the Context7 Wizard, and Save Hours)
*   1월 28일: Vercel의 Claude Code, Codex를 위한 모범 사례 에이전트 스킬로 React 성능을 강화하세요 (Supercharge Your React Performance with Vercel’s Best Practices Agent Skill for Claude Code, Codex…)
*   1월 27일: Agent Skills: AI 에이전트 작동 방식을 혁신하는 범용 표준 (Agent Skills: The Universal Standard Transforming How AI Agents Work)
*   1월 20일: Agent-Browser: 컨텍스트 창의 93%를 절약하는 AI 우선 브라우저 자동화 (Agent-Browser: AI-First Browser Automation That Saves 93% of Your Context Window)
*   1월 17일: 개인 지식으로 AI 코딩 에이전트 역량 강화: Doc-Serve Agent Skill (Empowering AI Coding Agents with Private Knowledge: The Doc-Serve Agent Skill)
*   1월 15일: Skilz: AI 스킬을 위한 범용 패키지 매니저 (Skilz: The Universal Package Manager for AI Skills)
*   1월 12일: 에이전트 개발 마스터하기: 강력한 AI 에이전트 스킬 생성을 위한 Architect Agent 워크플로우 (Mastering Agent Development: The Architect Agent Workflow for Creating Robust AI Agent Skills)
*   1월 5일: 첫 Claude Code 에이전트 스킬 구축: 시간을 절약하는 간단한 프로젝트 메모리 시스템 (Build Your First Claude Code Agent Skill: A Simple Project Memory System That Saves Hours)

**2025년 12월**
*   12월 9일: 에이전트 기반 스킬 마스터하기: 효과적인 에이전트 스킬 구축을 위한 완벽 가이드 (Mastering Agentic Skills: The Complete Guide to Building Effective Agent Skills)
*   12월 9일: Claude Code Skills 심층 분석 2부 (Claude Code Skills Deep Dive Part 2)

**11월**
*   11월 12일: Claude Code Skills 심층 분석 1부 (Claude Code Skills Deep Dive Part 1)

**10월**
*   10월 12일: Claude Skills 개념 심층 분석 (Claude Skills Conceptual Deep Dive)

*   Agent Skills
*   Agentic Programming
*   Claude Code
*   Agentic Applications
*   AI Agent

214

6

Towards AI에 게재됨
팔로워 11.6만 명
·
최근 게시됨

저희는 엔터프라이즈 AI를 구축하고, 저희가 배운 것을 가르칩니다. Towards AI 아카데미에서 10만 명 이상의 AI 실무자들과 함께하세요. 무료: 6일 에이전트 기반 AI 엔지니어링 이메일 가이드: https://email-course.towardsai.net/

팔로우
작성자: Rick Hightower
팔로워 1.8천 명
·
팔로잉 52명

Apple에서 시스템을 구축하는 것부터 Capital One과 NFL에서 AI를 개척하기까지: 저는 20년 동안 엔터프라이즈 소프트웨어를 진정으로 지능적으로 만드는 데 전념했습니다. 저는 AI를 설계합니다.

## 응답 (6)
BongBongOogle

당신의 생각은 무엇입니까?
취소
답변
모든 응답 보기

도움말
상태
소개
채용
보도 자료
블로그
개인 정보
규칙
약관
텍스트 음성 변환