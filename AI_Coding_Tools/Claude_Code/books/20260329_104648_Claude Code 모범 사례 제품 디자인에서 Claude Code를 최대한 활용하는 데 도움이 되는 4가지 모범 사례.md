# 번역 기사

- **원본 URL:** https://uxplanet.org/claude-code-best-practices-28609653abcf
- **번역 일시:** 2026-03-29 10:46:48

---

# Claude Code 모범 사례: 제품 디자인에서 Claude Code를 최대한 활용하는 데 도움이 되는 4가지 모범 사례

*닉 바비치(Nick Babich) 작성*
*5분 소요*
*2026년 3월 8일*

---

## 1. 실행 전에 계획 모드(Plan mode)로 시작하기

계획 없이 바로 실행에 들어가는 것은 Claude를 사용할 때 저지르는 가장 큰 실수입니다. 오류를 수정하고 Claude를 올바른 방향으로 안내하는 데 아마도 N배 더 많은 시간을 소요하게 될 것입니다.

"계획 모드(Plan mode)" 옵션이 VSCode용 Claude Code에서 선택됨.

새 프로젝트를 구축할 때는 계획 모드(Plan mode)를 가장 먼저 시작해야 합니다. 계획이 수립된 후에야 다음 단계인 실행으로 넘어갈 수 있습니다.

### Claude Code에서 계획 모드(Plan mode)를 사용하는 방법

예를 들어, 다음은 제가 이전 기사에서 디자인했던 랜딩 페이지(landing page) 재설계를 계획하기 위해 Claude Code에 사용하는 프롬프트(prompt)입니다.

```
Plan a redesign for this web page before making any edits.

Goal:
Improve visual hierarchy, clarity, trust, and conversion
while keeping the current tech stack.

Your process:
1. Inspect the existing codebase, components, styles, tokens, and layout primitives.
2. Identify UX/UI issues in the current implementation.
3. Ask clarifying questions if brand/style/conversion intent is unclear.
4. Produce a design-first implementation plan in markdown.

Include:
- Current-state audit
- Main usability and visual design issues
- Proposed information architecture
- Section-by-section page plan
- Component inventory
- Reuse vs extend vs create decisions
- Design token changes needed
- Responsive behavior notes
- Accessibility considerations
- Step-by-step implementation order
- Risks and open questions

Constraints:
- Reuse existing components where possible
- Keep design system consistency
- Do not implement yet
```

**목표:**
현재의 기술 스택(tech stack)을 유지하면서 시각적 계층(visual hierarchy), 명확성, 신뢰도, 전환율을 개선하세요.

**작업 프로세스:**
1.  기존 코드베이스(codebase), 컴포넌트(components), 스타일(styles), 토큰(tokens), 레이아웃 프리미티브(layout primitives)를 검사합니다.
2.  현재 구현에서 UX/UI 문제를 식별합니다.
3.  브랜드/스타일/전환 의도가 불분명할 경우 명확화 질문을 합니다.
4.  디자인 우선 구현 계획을 마크다운(Markdown) 형식으로 작성합니다.

**포함할 내용:**
*   현재 상태 감사
*   주요 사용성(usability) 및 시각 디자인 문제
*   제안된 정보 아키텍처(information architecture)
*   섹션별 페이지 계획
*   컴포넌트(component) 목록
*   재사용 vs 확장 vs 생성 결정
*   필요한 디자인 토큰(design token) 변경 사항
*   반응형(responsive) 동작 참고 사항
*   접근성(accessibility) 고려 사항
*   단계별 구현 순서
*   위험 및 미해결 질문

**제약 사항:**
*   가능한 경우 기존 컴포넌트(components) 재사용
*   디자인 시스템(design system) 일관성 유지
*   아직 구현하지 말 것

계획을 세울 때는 Claude가 이를 사용하여 관련 구현 계획을 작성할 수 있도록 목표를 가능한 한 명확하게 작성하는 것이 좋습니다.

다음은 위 목표가 Claude가 우리를 위해 생성한 계획과 어떻게 일치하는지 보여줍니다.

랜딩 페이지(landing page) 재설계.

Claude가 작성한 계획을 검증한 후, Claude가 더 빠르게 진행할 수 있도록 자동 수락 모드(Edit automatically)로 전환할 수 있습니다.

자동 편집 모드(Edit automatically mode).

## 2. Claude.md를 프로젝트 메모리(Project Memory)로 활용하기

모든 프로젝트에 `CLAUDE.md` 파일을 추가하세요. Claude가 처음부터 더 나은 컨텍스트(context)를 가질수록, 양질의 결과물을 제공할 가능성이 높아집니다.

`Claude.md`를 Claude가 작업을 수행할 때 기반을 형성하는 장기 기억 장치라고 생각하세요.

`CLAUDE.md`에 Claude가 프로젝트에 대해 알아야 할 모든 필수 정보를 포함하세요. 웹 페이지 코딩과 같은 프로젝트 코딩의 경우 다음이 포함됩니다.

*   프로젝트 개요
*   기술 스택(Tech stack) 및 제약 사항
*   코딩 컨벤션(coding conventions) 및 스타일 선호도
*   특정 명령
*   비즈니스 제약 사항

`CLAUDE.md` 파일에 대한 모범 사례는 다음 기사에서 다루었습니다.

[**CLAUDE.md 모범 사례: CLAUDE.md에 포함해야 할 10가지 섹션**](https://uxplanet.org/claudemd-best-practices-10-sections-to-include-in-your-claudemd-f5e6101c5147)
*(uxplanet.org)*

이러한 구조는 세션(session) 전반에 걸쳐 더 일관된 결과물을 얻는 데 도움이 될 것입니다.

다음은 React로 구축된 랜딩 페이지(landing page) 예시에 대한 `CLAUDE.md` 파일 미리보기입니다.

내 프로젝트(랜딩 페이지 디자인)용 `CLAUDE.md` 파일.

## 3. 반복적인 작업에 스킬(Skills) 활용하기

사용자 조사, 프런트엔드(front-end) 디자인 또는 사용성 테스트(usability testing)와 같이 반복되는 작업을 하는 경우, 해당 작업에 대한 스킬(skills)을 구축하세요.

스킬(skill)을 Claude가 우리를 위해 무언가를 할 필요가 있을 때 제공하는 레시피(recipe)라고 생각하세요.

반복적인 작업을 하는 경우 스킬(skills)은 매우 강력합니다. 그리고 최근, Anthropic 팀은 새로운 스킬(skills) 생성 과정을 개선했습니다. 그들은 스킬 초안 작성 중에 발생하는 내부 평가 프로세스를 실행하여 스킬 생성 과정을 간소화하는 스킬 생성기(Skill Creator)라는 메타 스킬(meta skill)을 출시했습니다.

[**제품 디자이너를 위한 Claude 스킬(Skills) 2.0**](https://uxplanet.org/claude-skills-2-0-for-product-designers-dd0596395561)
*Anthropic은 최근 새로운 Claude 스킬(Skills) 생성 과정을 개선했으며, 이 개선은 매우 중요하여…*
*(uxplanet.org)*

다음은 제품 디자이너로서 시도해야 할 상위 10가지 스킬(skills)입니다.

[**제품 디자인에서 시도해야 할 상위 10가지 Claude 스킬(Skills)**](https://uxplanet.org/top-10-claude-skills-you-should-try-in-product-design-6b83f3281c7e)
*Anthropic의 AI 비서인 Claude는 제품 디자이너의 도구 키트에서 가장 다재다능한 도구 중 하나가 되었으며, 다음과 같은 기능을 수행할 수 있습니다…*
*(uxplanet.org)*

## 4. MCP (모델 컨텍스트 프로토콜)

MCP(Model Context Protocol)는 AI를 위한 USB입니다. 이를 통해 Claude는 GitHub, Notion, Slack, Figma와 같은 서드파티(3rd party) 도구에 연결하여 해당 서비스의 데이터로 컨텍스트(context)를 풍부하게 할 수 있습니다. 서드파티(3rd party) 도구에서 데이터를 수동으로 복사할 필요 없이, Claude 터미널(terminal)에서 직접 상호 작용할 수 있습니다.

MCP 작동 방식. Figma 제공 이미지.

다음은 제가 프로젝트에 사용하는 MCP 목록입니다.

[**제품 디자이너를 위한 상위 7가지 MCP**](https://uxplanet.org/top-7-mcp-for-product-designers-c58a5e828d15)
*모델 컨텍스트 프로토콜(Model Context Protocol, MCP)은 AI 모델이 외부 도구 및 데이터 소스에 연결할 수 있도록 하는 기술입니다…*
*(uxplanet.org)*

다음은 Claude Code와 MCP를 사용하는 두 가지 훌륭한 시나리오입니다.

### 시나리오 #1: NotebookLM을 Claude Code에 연결하기

모든 제품 사양(product specs)과 관련 세부 정보를 NotebookLM의 노트북(notebook)에 저장하면, 픽셀 푸싱(pixel-pushing) 단계를 건너뛰고… 사양을 직접 코드로 전환할 수 있습니다! 이는 UI 디자인 도구에 시간을 들이지 않고도 신속하게 프로토타입(prototyping)을 제작하는 매우 멋진 방법입니다.

[**MCP를 통해 NotebookLM으로 코딩을 안내하는 방법**](https://uxplanet.org/how-to-use-notebooklm-to-guide-coding-via-mcp-63e2632599a1)
*Figma에서 UI 디자인을 건너뛰고 디자인 품질을 희생하지 않으면서 바로 코딩으로 넘어갈 수 있다는 사실을 알고 계셨나요?*
*(uxplanet.org)*

### 시나리오 #2: Claude Code와 Figma 간의 양방향 연결

최근 Claude Code와 Figma는 양방향 연결을 구축했습니다. 이는 코드(Claude Code 내)와 디자인(Figma 내)에 있는 내용이 양방향(Figma에서 Claude Code로, Claude Code에서 Figma로)으로 전송될 수 있음을 의미합니다.

[**Claude Code + Figma = 💛**](https://uxplanet.org/claude-code-figma-a47781a96738)
*Claude Code와 Figma가 이제 양방향 통신을 지원한다는 것을 알고 계셨나요? 이는 Claude에서 디자인을 게시할 수 있다는 의미입니다…*
*(uxplanet.org)*

---
AI · Claude · 디자인 · 웹 개발 · 웹 디자인

345 1

**UX Planet**에 게시됨
팔로워 35.6만 명
최근 게시일: 1일 전

UX Planet은 사용자 경험과 관련된 모든 것을 위한 원스톱 리소스입니다.

**팔로우**

---
**닉 바비치(Nick Babich) 작성**
팔로워 14.1만 명
팔로잉 60명

제품 디자이너 및 UX Planet 편집장. 트위터: https://twitter.com/101babich

**팔로우**

---
**댓글 (1)**

**BongBongOogle**
어떻게 생각하시나요?
취소 답글 달기

**Valerian Kleinschnitz**
3월 21일
매우 흥미로운 기사입니다. Claude가 ChatGPT보다 낫다고 보시나요?
**답글**

---
**닉 바비치 및 UX Planet의 다른 글**

**UX Planet**에서 **닉 바비치**
[CLAUDE.md 모범 사례: CLAUDE.md에 포함해야 할 10가지 섹션](https://uxplanet.org/claudemd-best-practices-10-sections-to-include-in-your-claudemd-f5e6101c5147)
3월 7일

**UX Planet**에서 **닉 바비치**
[제품 디자인에서 시도해야 할 상위 10가지 Claude 스킬(Skills)](https://uxplanet.org/top-10-claude-skills-you-should-try-in-product-design-6b83f3281c7e)
*Anthropic의 AI 비서인 Claude는 제품 디자이너의 도구 키트에서 가장 다재다능한 도구 중 하나가 되었으며, 다음과 같은 기능을 수행할 수 있습니다…*
2월 19일

**UX Planet**에서 **닉 바비치**
[디자인 시스템 작업을 위한 Figma + Claude](https://uxplanet.org/figma-claude-for-design-system-tasks-59755b7662c1)
*디자이너가 무관심해서가 아니라, 대규모로 일관성을 유지하는 것이 정말 어렵기 때문에 디자인 시스템이 무너집니다. 토큰이 드리프트되고, 이름 지정이…*
1월 25일

**UX Planet**에서 **닉 바비치**
[Claude Code + Figma = 💛](https://uxplanet.org/claude-code-figma-a47781a96738)
*Claude Code와 Figma가 이제 양방향 통신을 지원한다는 것을 알고 계셨나요? 이는 Claude에서 디자인을 Figma로 게시할 수 있다는 의미입니다…*
3월 4일
[닉 바비치의 모든 글 보기](https://medium.com/@101babich/latest)
[UX Planet의 모든 글 보기](https://uxplanet.org/latest)

---
**미디엄(Medium) 추천 글**

**UX Planet**에서 **닉 바비치**
[제품 디자이너를 위한 Claude 스킬(Skills) 2.0](https://uxplanet.org/claude-skills-2-0-for-product-designers-dd0596395561)
*Anthropic은 최근 새로운 Claude 스킬(Skills) 생성 과정을 개선했으며, 이 개선은 너무나 중요하여 거의…*
3월 8일

**Michal Malewicz**
[2026년을 위한 나의 웹 디자인 및 구축 워크플로 완전 공개](https://uxplanet.org/my-complete-web-design-build-workflow-for-2026-6218e7e1747)
*25년의 경험이 최고의 프로세스로 응축되었습니다.*
3월 13일

**Amy Rogers**
[색상 팔레트 만들기를 멈추세요](https://uxplanet.org/stop-making-colour-palettes-c7f7690a2a1b)
*OKLCH로 동적이고 접근성 높은 시스템을 구축하는 방법*
1월 17일

**Reza Rezvani**
[보리스 처니(Boris Cherny)의 Claude Code 팁이 이제 스킬(Skill)이 되었습니다. 전체 컬렉션이 밝히는 것은 다음과 같습니다.](https://medium.com/m/global-identity?redirectUrl=https%3A%2F%2Fbootcamp.uxdesign.cc%2Fboris-chernys-claude-code-tips-are-now-a-skill-here-is-what-the-complete-collection-reveals-538dd3c9a63c)
6일 전

**Nurkhon**
[나의 AI 디자인 워크플로 — 2026년에 실제로 작동하는 것](https://bootcamp.uxdesign.cc/my-ai-design-workflow-what-actually-works-in-2026-5b487b9b7806)
*40개 이상의 AI 디자인 도구를 테스트했습니다. 대부분 실패했습니다. 다음은 제가 실제로 사용하는 간단한 워크플로입니다.*
2월 7일

**Marco Kotrotsos**
[Anthropic은 AI 업계에서 아무도 보고 싶어 하지 않는 솔직한 데이터를 방금 공개했습니다.](https://uxplanet.org/anthropic-just-published-honest-data-nobody-in-ai-wants-to-see-6014389b706c)
*"할 수 있다"와 "한다"의 격차는 서서히 줄어들고 있습니다.*
3월 10일
[더 많은 추천 글 보기](https://medium.com/m/recommended)

---
[도움말](https://help.medium.com/hc/en-us) · [상태](https://medium.com/m/status) · [소개](https://medium.com/about) · [채용](https://medium.com/careers) · [언론](https://medium.com/press) · [블로그](https://blog.medium.com/) · [개인정보 보호](https://policy.medium.com/medium-privacy-policy-f03bf9205748) · [규칙](https://policy.medium.com/medium-rules-53094ea5114b) · [약관](https://policy.medium.com/medium-terms-of-service-91fe16410887) · [텍스트 음성 변환](https://help.medium.com/hc/en-us/articles/360007873614-Text-to-speech)