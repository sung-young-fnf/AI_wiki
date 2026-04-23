# 번역 기사

- **원본 URL:** https://medium.com/data-science-collective/the-complete-guide-to-ai-agent-memory-files-claude-md-agents-md-and-beyond-49ea0df5c5a9
- **번역 일시:** 2026-03-02 19:26:35

---

# AI 에이전트 메모리 파일 완벽 가이드 (CLAUDE.md, AGENTS.md 및 그 외)

단 하나의 파일. 모든 대화 전에 로드됩니다. 이것만으로도 스테이트리스(Stateless) AI 코딩 어시스턴트가 프로젝트 작동 방식을 실제로 기억하는 도구로 변모할 수 있습니다.

저는 이 사실을 뼈저리게 깨달았습니다. 프로덕션 코드베이스에 Claude Code를 사용한 지 석 달이 지나도, 매 세션마다 동일한 실수를 계속 수정해야 했습니다. "아니요, 저희는 `npm`이 아니라 `pnpm`을 사용합니다." "아니요, 테스트 명령어는 `pytest`가 아니라 `make test-integration`입니다." "아니요, 여기서는 기본 익스포트(default exports)를 사용하지 않습니다." 세션이 끝나면 모든 수정 사항이 사라졌습니다.

그러다 제가 `CLAUDE.md` 파일을 만들었습니다. 40줄짜리 프로젝트 컨텍스트였습니다. 하룻밤 사이에 수정할 일이 없어졌습니다.

하지만 `CLAUDE.md`는 이제 하나의 생태계가 된 것의 일부일 뿐입니다. `AGENTS.md`, `.cursorrules`, `copilot-instructions.md`, `CLAUDE.local.md`, 그리고 이제 Claude의 자동 메모리 시스템도 있습니다. 여러분의 저장소는 혼란스러운 봇을 위한 마크다운 박물관처럼 보일 수도 있습니다.

이 가이드는 이 모든 것을 다룹니다. 각 파일이 하는 일, 파일의 위치, 그리고 (가장 중요하게) 실제로 필요한 파일은 무엇인지 알려드립니다.

## 문제점: 모든 도구가 자신만의 파일을 원합니다

두 개 이상의 AI 코딩 어시스턴트를 사용한다면 (그리고 제가 쓴 AI 에이전트 프레임워크 솔직한 계층형 목록을 읽었다면 그 이유를 아실 겁니다), 이 복잡한 상황을 이미 눈치채셨을 겁니다. Claude는 `CLAUDE.md`를 원합니다. Cursor는 `.cursorrules` (또는 `.cursor/rules/`)를 원합니다. GitHub Copilot은 `.github/copilot-instructions.md`를 원합니다. Windsurf는 `.windsurf/rules`를 원합니다. Google의 Jules는 `JULES.md`를 원합니다.

모든 파일의 내용은 거의 동일합니다. 코딩 표준, 빌드 명령어, 테스트 설정, 아키텍처 패턴 등입니다. 하지만 동일한 지침을 다섯 개의 다른 파일에 복사하여 붙여넣고 있습니다.

이것이 바로 `AGENTS.md`가 해결하기 위해 탄생한 파편화 문제입니다. 잠시 후에 더 자세히 설명하겠습니다.

먼저, 각 파일이 실제로 어떤 역할을 하는지 이해해 봅시다.

## CLAUDE.md: 모든 것의 시작

`CLAUDE.md`는 Claude Code의 메모리 파일입니다. 프로젝트 루트에 넣어두면 Claude는 매 세션 시작 시 이 파일을 읽습니다. 마치 기억 상실증에 걸린 새로운 팀원을 위한 브리핑 문서라고 생각하면 됩니다.

**파일 위치 (계층 구조가 중요합니다):**

계층 구조는 상향식(bottom-up)으로 로드됩니다. 엔터프라이즈 정책이 먼저 로드되고, 그 다음 개인 설정, 프로젝트 수준, 마지막으로 하위 디렉토리 수준 순입니다. 더 구체적인 지침이 더 광범위한 지침을 재정의합니다.

**실제로 포함되어야 할 내용:**

`/init` 명령어는 프로젝트 구조를 기반으로 시작 파일을 생성합니다. 여기서 직관적이지 않은 부분이 있습니다: 생성된 대부분의 내용을 삭제하세요. 기본 파일에는 명백한 내용(네, Claude, 이것은 TypeScript 프로젝트이며 `package.json`에서 확인할 수 있습니다)이 포함되어 있습니다. `CLAUDE.md`의 모든 줄은 실제 작업과 주의를 놓고 경쟁합니다. (대부분의 AI 에이전트가 프로덕션 환경에서 실패하는 이유가 궁금했다면, 잘못된 컨텍스트 관리가 그 절반의 답입니다.)

**목표:** 300줄 미만으로 유지하세요. Claude가 파일 없이는 틀릴 만한 내용에 집중하세요.

좋은 `CLAUDE.md`는 네 가지 섹션으로 구성됩니다:

*   **프로젝트 컨텍스트.** 한 줄로 요약: "Stripe 연동 및 Postgres를 사용하는 Next.js 전자상거래 앱."
*   **코드 스타일 선호도.** "코드를 제대로 포맷하라"가 아니라 "ES 모듈을 사용하고, 네임드 익스포트(named exports)를 선호하며, 2칸 들여쓰기를 사용한다."
*   **명령어.** 정확한 문자열: `pnpm test:integration`, `make build-docker`, `npm run lint:fix`. Claude는 이들을 그대로 사용합니다.
*   **아키텍처 결정.** "API 경로는 `/src/api/[resource]/route.ts`에 위치합니다. 데이터베이스 접근에는 리포지토리 패턴(repository pattern)을 사용합니다."

(다른 모든 것들은요? 별도의 파일로 옮기고 `@imports`를 사용하세요.)

**@imports 시스템:**

`CLAUDE.md`는 `@path/to/file` 구문을 사용하여 다른 파일을 임포트(import)하는 것을 지원합니다:

```
See @README.md for project overview
See @docs/api-patterns.md for API conventions
See @package.json for available npm scripts
```

임포트(import)는 재귀적(recursive)일 수 있습니다 (참조된 파일이 다른 파일을 참조할 수 있으며, 최대 5단계 깊이까지 가능). 이것은 "하나의 거대한 파일" 문제를 해결합니다. `CLAUDE.md`를 간결하게 유지하고, 자세한 지침은 별도의 파일로 옮기세요.

팀의 경우, 이것은 강력한 기능입니다. 프론트엔드 팀은 `docs/frontend-rules.md`를 소유하고, 보안 팀은 `docs/security.md`를 소유합니다. `CLAUDE.md`는 이 모든 것을 임포트(import)하기만 하면 됩니다.

**솔직한 한계점:** `CLAUDE.md`는 Claude 전용입니다. 만약 팀이 Claude Code와 함께 Cursor나 Copilot을 사용한다면, 이 파일은 읽히지 않을 것입니다. 이것이 바로 `AGENTS.md`가 등장하는 이유입니다.

## AGENTS.md: 범용 표준

`AGENTS.md`는 2025년 중반 Sourcegraph, OpenAI, Google, Cursor 등과의 협력을 통해 등장했습니다. 현재는 리눅스 재단(Linux Foundation) 산하 Agentic AI Foundation에서 유지 관리되고 있습니다. 핵심 아이디어는 간단합니다: 하나의 파일, 어떤 에이전트든 지원합니다.

Claude Code, Cursor, GitHub Copilot, Gemini CLI, Windsurf, Aider, Zed, Warp, RooCode 등 점점 더 많은 도구들이 이를 지원하고 있습니다.

**작동 방식:**

프로젝트 루트에 `AGENTS.md`를 두세요. 표준 마크다운(Markdown)이며, 특별한 스키마나 YAML 프론트매터(YAML frontmatter)가 필요하지 않습니다. 편집 중인 파일에 가장 가까운 `AGENTS.md`가 우선순위를 가지며, 명시적인 사용자 프롬프트는 모든 것을 재정의합니다.

```markdown
# AGENTS.md

## Project Overview
Next.js 14, Postgres, Stripe로 구축된 전자상거래 플랫폼.

## Build & Test
- Install: `pnpm install`
- Dev: `pnpm dev`
- Test: `pnpm test`
- Lint: `pnpm lint:fix`

## Code Standards
- TypeScript strict mode 사용
- 기본 익스포트(default exports)보다 네임드 익스포트(named exports) 선호
- API 경로는 /src/api/에서 REST 컨벤션 따름

## Testing Requirements
- 모든 PR에는 테스트 포함되어야 함
- 유닛 테스트는 vitest, E2E 테스트는 playwright 사용
```

**AGENTS.md vs CLAUDE.md vs README:**

이들은 서로 보완적이지 경쟁적이지 않습니다. `README`는 사람을 위한 것이고, `AGENTS.md`는 범용 에이전트 브리핑이며, `CLAUDE.md`는 그 위에 Claude 고유의 지침을 추가합니다.

**저의 권장 사항:** 여러 AI 도구를 사용한다면, 공유 지침은 `AGENTS.md`에 넣고 `CLAUDE.md`는 `@imports` 및 `/init` 워크플로우와 같은 Claude 고유 기능에 사용하세요. Claude Code만 사용한다면, `CLAUDE.md`만으로도 충분합니다.

## 기타 메모리 파일 (빠른 참조)

아직 모든 도구가 `AGENTS.md`를 채택한 것은 아니며, 일부 도구는 `AGENTS.md`가 다루는 범위를 넘어서는 기능을 가지고 있습니다.

*   **.cursorrules / .cursor/rules/*.mdc**: Cursor의 기본 형식입니다. 새로운 `.mdc` 형식은 활성화 모드(항상, 자동 첨부, 에이전트 요청, 수동)를 가진 YAML 프론트매터(YAML frontmatter)를 지원합니다. `AGENTS.md`보다 더 세분화되어 있지만, Cursor 전용입니다. Cursor는 `AGENTS.md`도 읽으므로 둘 다 사용할 수 있습니다. 공유 규칙은 `AGENTS.md`에, Cursor 고유 동작은 `.cursor/rules/`에 넣으세요.
*   **.github/copilot-instructions.md**: GitHub Copilot의 지침 파일입니다. `.github` 폴더에 위치합니다. Copilot은 이제 `AGENTS.md`도 읽으므로 둘 다 필요하지 않을 수 있습니다.
*   **.windsurfrules**: Windsurf의 형식입니다. 워크스페이스 전반의 지침을 위한 `global_rules.md`와 함께 이중 구조를 가집니다. Windsurf도 `AGENTS.md`를 지원합니다.
*   **CLAUDE.local.md**: Git에 커밋되지 않는 개인적이고 프로젝트별 선호 사항입니다. `.gitignore`에 자동으로 추가됩니다. 샌드박스 URL, 선호하는 테스트 데이터 또는 개인적인 워크플로우 특성과 같은 내용을 여기에 사용하세요. (팀원들이 여러분이 항상 "butts123"이라는 사용자 이름으로 테스트한다는 사실을 알 필요는 없습니다.)

## Claude의 자동 메모리: 스스로 기록하는 AI

이것은 가장 최근에 추가된 기능이자 가장 흥미로운 기능입니다. Claude Code는 이제 Claude가 세션 중에 스스로 메모를 작성하는 자동 메모리 시스템을 갖추고 있습니다.

이것은 `~/.claude/projects/<project>/memory/`에 있습니다:

```
memory/
├── MEMORY.md          # 인덱스 파일, 모든 세션에서 로드됨
├── debugging.md       # 디버깅 패턴에 대한 메모
├── api-conventions.md # API 설계 결정
└── ...
```

`CLAUDE.md`와의 주요 차이점: `CLAUDE.md`는 여러분이 작성하고, `MEMORY.md`는 Claude가 작성합니다. 여러분은 지침을 제공하고, Claude는 학습 내용을 포착합니다.

`MEMORY.md`의 처음 200줄만 자동으로 로드됩니다. 주제 파일(`debugging.md` 등)은 Claude가 필요할 때 온디맨드(on-demand)로 로드됩니다.

**실용적인 워크플로우:**

Claude가 프로젝트에 대해 어떤 것을 발견하면 ("아, 이 코드베이스는 커스텀 ORM 래퍼를 사용하는구나"), 이를 자동 메모리에 저장합니다. 다음 세션에서는 이미 알고 있습니다. 더 이상 반복할 필요가 없습니다.

수동으로도 이 기능을 활성화할 수 있습니다. 생산적인 세션이 끝날 때 Claude에게 이렇게 요청하세요: "오늘 코드베이스에 대해 학습한 내용으로 메모리 파일을 업데이트해 줘." 학습 내용은 계속 유지됩니다.

Claude가 저장한 내용을 검토하거나 편집하려면, 세션 중에 `/memory`를 실행하세요.

**솔직한 한계점:** 자동 메모리는 아직 점진적 출시(rolling out) 중입니다. 만약 보이지 않는다면, 환경 변수에 `CLAUDE_CODE_DISABLE_AUTO_MEMORY=0`를 설정하세요. 그리고 Claude가 이 메모를 작성하기 때문에 품질은 다를 수 있습니다. 다른 문서와 마찬가지로 주기적으로 검토하세요.

## `/init` 후 삭제 워크플로우

이것은 모든 새 프로젝트에 대한 메모리 파일을 부트스트랩(bootstrap)하는 가장 빠른 방법입니다.

1.  프로젝트 디렉토리에서 `/init` 실행
2.  Claude가 프로젝트 구조와 감지된 기술 스택을 기반으로 시작 `CLAUDE.md` 생성
3.  필요 없는 부분을 삭제

3단계에서 대부분의 사람들이 잘못합니다. 생성된 파일은 시작점이지 완성된 제품이 아닙니다. 종종 가치를 더하지 않는 불필요한 내용("이 프로젝트는 JavaScript를 사용합니다." 고마워, Claude. `package.json`에서 알 수 있어.)이 포함됩니다.

선삭제(delete-first) 방식은 처음부터 작성하는 것보다 빠릅니다. 여러분은 합리적인 초안을 편집하여 줄이는 것이지 빈 파일을 멍하니 바라보는 것이 아닙니다.

초기 설정 후, 메모리 파일을 점진적으로(organically) 구축하세요. Claude가 잘못된 가정을 할 때 (저의 경우, 더 이상 사용되지 않는 내부 패키지에서 계속 임포트(import)했습니다), 한 번만 수정하지 마세요. Claude에게 이렇게 말하세요: "내 `CLAUDE.md`에 추가해 줘: 항상 `@company/utils-v2`에서 임포트(import)하고, `@company/utils`에서는 하지 마." 이 지침은 다음 세션에도 유지됩니다.

몇 주마다 Claude에게 `CLAUDE.md`를 검토하고 최적화하도록 요청하세요. 지침은 축적되고, 일부는 중복되며, 다른 일부는 충돌합니다. 빠른 정리로 항상 날카롭게 유지하세요.

## 제가 실제로 사용하는 것 (저의 설정)

문서 Q&A 시스템부터 회사 정책을 환각적으로 생성하는 내부 에이전트에 이르는 다양한 프로젝트에서 이 모든 것을 실험한 후, 제가 정착한 것은 다음과 같습니다:

*   프로젝트 루트에 공유 지침(빌드 명령어, 코드 표준, 테스트 요구 사항)이 포함된 `AGENTS.md`. 이것은 우리 팀이 사용하는 모든 AI 도구를 포괄합니다.
*   Claude 고유 동작을 위한 `@imports`가 포함된 `CLAUDE.md`. 100줄 미만으로 간결하게 유지되며, 대부분 `docs/` 파일들을 가리킵니다.
*   저의 개인적인 특성(선호하는 테스트 데이터, 샌드박스 URL, 제가 자주 사용하는 단축 명령어)을 위한 `CLAUDE.local.md`.
*   자동 메모리 활성화. Claude가 스스로 메모를 작성하게 하고 매월 검토합니다.
*   나머지(`.cursorrules`, `copilot-instructions.md`)는 `AGENTS.md`에 대한 심볼릭 링크(symlinks)로 교체했습니다. 단일 정보원(single source of truth)이죠.

```bash
# 다중 도구 일관성을 위한 심볼릭 링크(Symlink) 설정
ln -sfn AGENTS.md .github/copilot-instructions.md
mkdir -p .cursor/rules && ln -sfn ../../AGENTS.md .cursor/rules/main.mdc
```

(이것이 우아한가요? 아닙니다. 하지만 도구 간의 지침 불일치(instruction drift)를 방지하나요? 네.)

## 빠른 결정 가이드

| 상황                                                | 추천 파일                                  |
| :-------------------------------------------------- | :----------------------------------------- |
| Claude Code만 사용한다면                           | `CLAUDE.md` (+ `@imports` 및 `/init`)     |
| 여러 AI 도구를 사용한다면                           | `AGENTS.md` (공유 지침) + `CLAUDE.md` (Claude 고유) |
| 개인적인 프로젝트별 설정이 필요하다면            | `CLAUDE.local.md`                          |
| Claude가 스스로 학습하고 기록하게 하고 싶다면      | Claude의 자동 메모리 (기본적으로 활성화)       |
| 특정 도구에 고유한 고급 기능이 필요하다면          | 해당 도구의 고유 파일 (예: `.cursorrules`) |
| 여러 도구에서 `AGENTS.md`를 사용하지 않는 경우   | `AGENTS.md`로 심볼릭 링크(symlink) |

## 수렴 현상 발생 중

6개월 전만 해도 다섯 가지 도구에 다섯 가지 다른 파일이 필요했습니다. 오늘날 대부분의 도구는 `AGENTS.md`를 읽습니다. 파편화 문제는 해결되지 않았지만, 개선되고 있습니다.

저의 예측: `AGENTS.md`는 `README.md`처럼 표준이 될 것입니다. 기술적으로 더 우월해서가 아니라, 리눅스 재단(Linux Foundation)의 지원과 광범위한 도구 지원이 생태계를 하나로 모으는 충분한 인력(gravity)을 생성하기 때문입니다. `CLAUDE.md`, `.cursorrules` 등은 도구별 기능을 위해 남아있겠지만, 공유 컨텍스트는 한 곳에 존재할 것입니다.

그때까지는 심볼릭 링크(symlink) 편법도 잘 작동합니다. 그리고 솔직히, 메모리 파일이 있다는 것만으로도 대부분의 개발자들보다 앞서가는 것입니다. 저는 랭그래프(LangGraph)를 사용한 AI 에이전트 구축에 대한 전체 가이드를 작성했는데, 사람들이 겪는 가장 큰 문제는 프레임워크가 아니었습니다. 바로 Claude가 세션 사이에 프로젝트 설정을 잊어버리는 것이었습니다. 매 세션마다 "Claude, 저희는 `pnpm`을 사용합니다"라고 말하는 것과 "Claude가 이미 알고 있습니다" 사이의 간극이 바로 도구와 팀원의 차이입니다.

이 글이 도움이 되었다면, 박수 50번을 주세요. Medium에게 더 많은 사람들에게 보여주라는 신호가 됩니다.