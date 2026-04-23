# 번역 기사

- **원본 URL:** https://sonnyhuynhb.medium.com/i-built-an-ai-powered-second-brain-with-obsidian-claude-code-heres-how-b70e28100099
- **번역 일시:** 2026-04-10 10:48:36

---

옵시디언(Obsidian)과 클로드 코드(Claude Code)로 구축한 AI 기반의 '제2의 뇌' 시스템, 그 방법을 공개합니다.

쌓이기만 하는 노트 작성은 이제 그만. 당신과 함께 사고하는 시스템을 구축해보세요.

솔직히 말해, 저는 시중에 있는 모든 생산성 시스템을 다 시도해봤습니다. 디지털 공동묘지가 되어버린 노션(Notion) 데이터베이스, 겉만 번지르르하고 실제 업무에는 도움이 안 되던 미로(Miro) 보드, 애플 노트(Apple Notes)의 혼돈까지, 안 해본 것이 없습니다.

그러던 중, 한 가지 깨달음을 얻었습니다. 저는 단순히 더 나은 노트 필기 시스템을 구축하는 대신, 저와 실제로 '함께 작동하는' 시스템, 즉 '제2의 뇌'를 만들기 시작했습니다.

그 비결은 옵시디언(Obsidian)의 로컬 우선(local-first) 일반 텍스트 유연성과, 클로드 코드(Claude Code)가 파일을 단순히 검색하거나 요약하는 것을 넘어 실제 작업하고, 프로그래밍 방식으로 조작, 생성, 개선할 수 있는 능력을 결합하는 것입니다.

제가 구축한 이 시스템과, 여러분도 여러분만의 시스템을 구축하는 방법을 소개합니다.

### 아키텍처 (생각보다 간단합니다)

이 시스템의 핵심은 옵시디언(Obsidian)이 모든 데이터를 사용자의 기기에 일반 마크다운(Markdown) 파일로 저장한다는 점입니다. 독점적인 파일 형식이나 클라우드 종속성(cloud lock-in) 없이, 클로드 코드(Claude Code)가 직접 읽고 쓰고 조작할 수 있는 `.md` 파일들로만 이루어져 있습니다.

시스템은 세 가지 간단한 계층으로 구성됩니다: 사용자가 소유한 일반 마크다운 파일들로 이루어진 옵시디언 볼트(Obsidian Vault), 이 파일들을 읽고 생성하며 조작하는 클로드 코드(Claude Code), 그리고 자동 연결된 노트, 생성된 요약, 지능적인 작업 추출 기능을 제공하는 '제2의 뇌'입니다.

### 기반 설정

먼저, 필수 사항을 준비합시다. 옵시디언(Obsidian)이 이미 설치되어 있다고 가정합니다. 그렇지 않다면 obsidian.md에서 다운로드하세요 (개인 사용은 무료입니다).

#### 1단계: 핵심 플러그인 설치

옵시디언에서 설정(Settings) → 커뮤니티 플러그인(Community Plugins)으로 이동하여 다음을 설치합니다:

*   **태스크(Tasks)**: 이 플러그인은 시스템의 중추(backbone) 역할을 합니다. 볼트(Vault) 내 어디에서든 작업을 작성하고, 다른 어떤 위치에서든 쿼리할 수 있게 해줍니다.
*   **템플릿터(Templater)**: 내장 템플릿보다 훨씬 강력합니다. 자바스크립트(JavaScript)를 실행하고, 날짜를 동적으로 가져오며, 심지어 외부 API를 호출할 수도 있습니다.
*   **데이터뷰(Dataview)**: 노트용 SQL이라고 생각하시면 됩니다. 볼트를 데이터베이스처럼 쿼리할 수 있습니다.

이게 전부입니다. 단 세 개의 플러그인으로 충분합니다. 불필요하게 많은 플러그인에 매달리지 마세요. 이 세 가지만으로도 필요한 기능의 90%를 충족할 수 있습니다.

#### 2단계: 클로드 코드(Claude Code) 설정

아직 설치하지 않았다면, 클로드 코드(Claude Code)를 설치하세요:

```bash
npm install -g @anthropic-ai/claude-code
```

그다음 터미널에서 옵시디언 볼트(Obsidian Vault)로 이동합니다:

```bash
cd ~/Documents/ObsidianVault
```

그리고 `claude`를 실행합니다:

```bash
claude
```

이게 다입니다. 이제 클로드 코드(Claude Code)는 당신의 전체 지식 베이스에 접근할 수 있습니다.

### 모든 것을 바꾼 워크플로우

여기서부터 흥미로워집니다. 제가 매일 사용하는 실제 워크플로우입니다:

#### 워크플로우 1: 아침 브레인 덤프(Brain Dump)

매일 아침, 저는 터미널을 열고 다음을 입력합니다:

```bash
claude “Create today’s daily note using my template. Pull in any tasks from yesterday that are still open, and link to any projects I worked on this week.”
```

클로드 코드(Claude Code)는 제 템플릿터(Templater) 템플릿을 읽고, 일일 노트를 생성하며, 어제 파일에서 미완료 작업을 스캔하고, 저를 위한 시작점을 구축합니다. 15분 걸리던 복사-붙여넣기 작업이 이제 30초면 끝납니다.

#### 워크플로우 2: 회의 노트 처리

회의를 마친 후, 저는 정리되지 않은 회의록을 파일에 저장합니다. 그리고:

```bash
claude “Read my meeting note from today’s standup and extract all action items. Format them as Obsidian Tasks with due dates based on what was discussed. Put them in my tasks.md file.”
```

이것은 “John이 금요일까지 API를 완료해야 한다고 언급했다”와 같은 내용을 파싱하여 다음을 생성합니다:

```
- [ ] Finish API implementation 📅 2026–01–10 #work/api
```

그러면 태스크(Tasks) 플러그인이 이를 인식하여 일일 노트 쿼리에 표시합니다. 수동 서식 지정은 전혀 필요 없습니다.

#### 워크플로우 3: 주간 검토

금요일 오후:

```bash
claude “Generate my weekly review. Summarize what I worked on based on this week’s daily notes, list completed tasks, identify any blocked projects, and suggest priorities for next week.”
```

이는 실제로 제가 한 주를 생각하는 데 도움이 되는 형식화된 마크다운(Markdown) 문서를 생성합니다. 귀찮은 일이 아니라 진정한 성찰 도구입니다.

### 템플릿 시스템

제 일일 노트 템플릿을 살짝 보여드립니다. 템플릿터(Templater)가 이를 처리하고, 클로드 코드(Claude Code)는 동일한 구조를 따르는 노트를 생성할 수 있습니다:

```markdown
# <% tp.date.now(“YYYY-MM-DD dddd”) %>

## 🎯 Today’s Focus

-

## 📋 Tasks

```tasks
not done
due before <% tp.date.now(“YYYY-MM-DD”) %>
```
```

전문가 팁: 템플릿을 간단하게 유지하세요. 마법은 복잡한 템플릿에 있는 것이 아니라, 클로드 코드(Claude Code)가 맥락을 이해하고 기존 구조에 자연스럽게 맞는 콘텐츠를 생성하는 능력에 있습니다.

### 이 시스템이 다른 모든 시스템보다 뛰어난 이유

저는 Make.com에서 자동화를 구축했고, 파이썬(Python)으로 스크립트를 작성했습니다. 옵시디언(Obsidian) + 클로드 코드(Claude Code) 조합이 승리하는 이유는 다음과 같습니다:

1.  **자연어가 인터페이스입니다.** 구문을 기억할 필요도, API 문서를 찾아볼 필요도 없습니다. 원하는 것을 그냥 말하기만 하면 됩니다.
2.  **자동화된 맥락 이해.** 클로드 코드(Claude Code)는 사용자의 기존 파일을 읽습니다. 볼트(Vault) 안에 모든 정보가 있기 때문에 명명 규칙, 프로젝트 구조, 선호 사항을 자동으로 파악합니다.
3.  **모든 데이터가 로컬에 저장됩니다.** 노트는 사용자의 기기를 벗어나지 않습니다. 동기화 문제나 구독 종속성(subscription lock-in)도 없으며, '서비스가 종료되니 데이터를 내보내세요' 같은 상황도 발생하지 않습니다.
4.  **지식의 복리 효과.** 사용하면 할수록 더 많은 맥락을 이해하게 됩니다. 지식 기반이 성장하면서 '제2의 뇌'는 실제로 더욱 똑똑해집니다.

### 오늘 바로 시작하기

여러분께 드리는 제 도전 과제입니다: 하나의 워크플로우로 시작하세요.

일일 노트 작성일 수도 있고, 작업 추출일 수도 있습니다. 현재 10분 이상 걸리는 한 가지를 선택하여 클로드 코드(Claude Code)로 자동화하세요.

그리고 다음 주에는 다른 것을 추가하세요.

한 달 이내에 여러분은 단순히 정보를 저장하는 것을 넘어, 중요한 것을 파악하고, 해야 할 일을 추적하며, 스스로는 연결하지 못했을 아이디어를 연결해 주는 시스템을 갖게 될 것입니다.

이는 단순한 노트 앱이 아닙니다. 바로 '제2의 뇌'인 것입니다.