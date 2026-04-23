# 번역 기사

- **원본 URL:** https://medium.com/@joe.njenga/6-claude-code-new-commands-variables-you-likely-missed-rushed-updates-0b6d1995c2aa
- **번역 일시:** 2026-04-08 16:41:01

---

6 Claude Code New Commands & Variables (You Likely Missed In Rushed Updates)

Claude Code는 우리가 본 어떤 AI 코딩 도구보다 빠르게 변화하고 있으며, 이러한 변화를 따라잡기란 쉽지 않습니다.

최근에는 일상적인 작업 흐름에 중요한 여러 가지 변경 사항이 있었습니다.

저는 이러한 모든 변화를 따라잡고 튜토리얼을 여러분과 공유하기 위해 최선을 다하고 있지만, 일부 변경 사항들은 작지만 주목할 가치가 있어도 단독 기사로 다룰 만큼은 아니기에, 한데 모아 설명하는 것이 더 효율적입니다.

이러한 변경 사항 중 일부는 최근 몇 차례 업데이트에서 발생했습니다.

이 글에서는 각 변경 사항을 소개하고, 왜 필요한지, 그리고 즉시 어떻게 사용을 시작할 수 있는지 시연해 보이겠습니다.

### 1) /powerup — 터미널에서 Claude Code 학습하기

`/powerup`을 입력하면 Claude Code 내에서 바로 대화형 강의(interactive lessons)를 이용할 수 있습니다.

각 강의는 애니메이션 데모(animated demos)를 통해 작동 방식을 보여주며 하나의 기능을 가르칩니다.

화살표 키와 Enter를 사용하여 탐색합니다. 강의를 선택하고 데모를 시청한 다음 다음으로 이동합니다.

강의는 다음 기능을 다룹니다:

*   컨텍스트 관리(Context management) 및 Claude가 코드베이스를 읽는 방식
*   권한 모드(Permission modes): ask, auto-edit, full auto
*   `/compact` 명령어(command) 및 압축(compaction)이 중요한 이유
*   훅(Hooks) 및 자동화(automation)
*   서브 에이전트(Sub-agents) 및 병렬 워크플로우(parallel workflows)
*   사용자 지정 슬래시 명령어(Custom slash commands)
*   프로젝트 메모리(project memory)를 위한 `CLAUDE.md` 파일
*   `MCP` 서버 통합(server integration)

각 강의는 개념 설명, 실습(hands-on exercise), 그리고 다음 주제로 연결되는 간단한 구조를 따릅니다.

저는 모든 강의를 살펴보았고, 제 경험에도 불구하고 몇 가지 새로운 것을 배웠습니다.

Claude Code를 처음 사용한다면, 이곳이 학습을 시작하기에 좋은 곳입니다.

### 2) /cost — 이제 모델별 및 캐시 분석 표시

구독 플랜(subscription plan)을 사용 중이라면, `/cost`가 이제 유용해졌습니다.

이 업데이트 전에는 `/cost`가 토큰 사용량(token usage)을 보여주었지만, 세분화(breakdown)하지 않았습니다. 어떤 모델이 할당량(quota)을 소모했는지, 캐싱(caching)으로 얼마나 절약했는지 알 수 없는 총량만 볼 수 있었습니다.

이제 다음을 보여줍니다:

*   모델별 분석(Per-model breakdown) (Opus vs Sonnet vs Haiku)
*   캐시 적중률(Cache-hit percentages)
*   입력 및 출력 토큰 분할(Input vs output token split)

이는 세션 중에 모델을 전환할 때 중요합니다.

Opus는 Sonnet보다 할당량(quota)을 더 빨리 소모합니다. 각 모델이 얼마나 소비했는지 알면 언제 `/model`을 사용하여 전환할지에 대해 더 현명한 결정을 내릴 수 있습니다.

캐시 적중률 분석(cache-hit breakdown)도 마찬가지로 유용합니다. Claude Code는 프롬프트 캐싱(prompt caching)을 많이 사용합니다. 캐시 적중률(cache-hit rate)이 낮으면 필요 이상으로 비용을 지불하고 있는 것입니다.

*   높은 캐시 적중률은 Claude가 컨텍스트(context)를 효율적으로 재사용하고 있음을 의미합니다.
*   낮은 캐시 적중률은 무언가가 캐시를 무효화(invalidate)하고 있음을 의미합니다. 파일을 너무 자주 전환하거나, 턴(turn) 사이에 컨텍스트가 너무 많이 변경될 수 있습니다.

API 사용자(API users)의 경우 이 데이터는 Anthropic 대시보드(dashboard)에서 항상 볼 수 있었습니다. 구독 사용자(subscription users)의 경우 지금까지는 미지의 영역이었습니다.

### 3) /release-notes — 대화형 버전 선택기

이전에는 `/release-notes`가 방대한 텍스트 덩어리(wall of text)를 출력했습니다. 특정 버전에서 무엇이 변경되었는지 찾기 위해 모든 내용을 스크롤해야 했습니다.

이제 대화형 선택기(interactive picker)입니다.

보려는 버전을 선택할 수 있습니다. 화살표 키로 탐색하고 Enter로 선택합니다.

몇 차례 업데이트를 건너뛰고 특히 2.1.89 버전에서 무엇이 변경되었는지 확인하고 싶다면, 바로 그 버전으로 이동할 수 있습니다.

### 4) /feedback — 이제 사용 불가 이유를 알려줌

이는 새로운 수정 사항입니다. 만약 `/feedback`이 귀하의 계정 유형에서 사용할 수 없었다면, 슬래시 메뉴(slash menu)에서 단순히 사라졌을 것입니다.

`/feedback`을 입력해도 아무 일도 일어나지 않았죠. 이제 작동합니다!

구독이 아닌 API 청구(API billing)를 사용하는 경우, 피드백(feedback)은 구독 사용자에게만 제공된다는 설명이 표시됩니다.

### 5) CLAUDE_CODE_NO_FLICKER=1 — 터미널 깜빡임(Flickering) 해결

Claude Code 응답 중에 터미널(terminal)이 깜빡인다면, 이 기능이 여러분을 위한 것입니다.

깜빡임은 Claude Code가 출력을 렌더링(render)하는 방식 때문에 발생합니다. 화면을 업데이트할 때마다 모든 것을 지우고 처음부터 다시 그립니다(redraw).

빠른 업데이트와 전체 다시 그리기(full redraw)가 합쳐지면 깜빡임이 발생합니다.

코드 생성(code generation) 중에는 더욱 심해집니다. Claude가 함수를 한 줄씩 작성할 때마다 새로운 줄이 전체 화면 다시 그리기(repaint)를 유발합니다.

일부 사용자들은 몇 달 동안 이 문제로 어려움을 겪었습니다. 기능적으로는 작동하지만, 8시간 동안 그것을 응시하는 것은 좋은 경험이 아닙니다. 이는 제가 이전 테스트에서 확인했던 Gemini CLI의 일반적인 문제 중 하나로, 저를 Gemini CLI를 싫어하게 만들었습니다.

Anthropic이 마침내 이 문제를 해결했습니다.

`CLAUDE_CODE_NO_FLICKER=1 claude`

또는 Powershell에서:

`$env:CLAUDE_CODE_NO_FLICKER=1; claude`

이는 원시 터미널 다시 그리기(raw terminal redraws) 대신 가상 뷰포트(virtual viewport)를 사용하는 새로운 렌더러(renderer)를 활성화합니다.

**활성화 방법**

두 가지 옵션이 있습니다.

**옵션 1: 일회성 실행**

Claude Code를 시작하기 전에 다음을 실행합니다:

`CLAUDE_CODE_NO_FLICKER=1 claude`

**옵션 2: 영구 설정**

모든 세션이 새로운 렌더러를 사용하도록 셸 프로필(shell profile)에 추가합니다.

zsh(macOS 기본값)의 경우:

```bash
echo 'export CLAUDE_CODE_NO_FLICKER=1' >> ~/.zshrc
source ~/.zshrc
```

bash의 경우:

```bash
echo 'export CLAUDE_CODE_NO_FLICKER=1' >> ~/.bashrc
source ~/.bashrc
```

영구 설정을 추천합니다. 한번 사용해보면 다시 이전으로 돌아가고 싶지 않을 것입니다.

### 6) /buddy — 코딩을 지켜보는 터미널 펫

이것은 4월 1일에 출시되었습니다. 대부분의 사람들은 농담이라고 생각했습니다.

`/buddy`를 입력하면 입력 프롬프트(input prompt) 옆에 작은 ASCII 캐릭터(creature)가 부화합니다. 이름과 성격을 가지고 있으며, 여러분의 코딩 세션(coding session)을 지켜봅니다.

이 캐릭터는 프롬프트 옆에 앉아 여러분의 행동에 반응합니다. 까다로운 버그(bug)를 디버깅(debug)하면, 격려나 비꼬는 말을 할 수도 있습니다.

### 마무리 생각

Anthropic은 누구도 따라잡을 수 없을 정도로 빠르게 Claude Code 업데이트를 출시하고 있습니다.

이 6가지 변경 사항은 며칠 간격으로 여러 버전에 걸쳐 퍼져 있습니다.

저는 대부분 `/powerup`과 `NO_FLICKER` 변수를 사용합니다. 터미널을 떠나지 않고 기능을 배우면 시간을 절약할 수 있습니다. 부드러운 렌더링(rendering)은 눈을 보호해 줍니다.
`/cost` 분석(breakdown)은 구독 사용자(subscription users)에게 무엇이 할당량(quota)을 소모하는지 가시성을 제공합니다.
`/buddy`는 예상치 못하게 좋았습니다. 긴 세션 동안 작은 동반자가 있다는 것은 터미널을 더 상호작용적(interactive)으로 느끼게 합니다.

만약 2.1.90 버전 이상이 아니라면, 앞으로 몇 주 안에 찾아올 다음 업데이트 롤러코스터를 기다리면서 업데이트하시기 바랍니다.