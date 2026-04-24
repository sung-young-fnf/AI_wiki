# 번역 기사

- **원본 URL:** https://medium.com/google-cloud/integrate-notebooklm-with-gemini-cli-google-antigravity-or-other-agents-with-mcp-cd83b575dc39
- **번역 일시:** 2026-03-18 21:53:00

---

# NotebookLM을 Gemini CLI, Google Antigravity 또는 기타 에이전트와 MCP로 통합하기

_Dazbo (Darren Lester) 작성_
_읽는 데 8분 소요 · 2026년 3월 2일 게시_

---

_이 글은 Google Cloud 개발자 옹호자들이 발행하거나 선별한 기술 문서 및 블로그 모음인 Google Cloud - Community에 게시되었습니다. 표현된 견해는 저자의 것이며 Google의 견해와 반드시 일치하지는 않습니다._

NotebookLM은 정말 대단합니다. 통합에 대해 자세히 알아보기 전에, NotebookLM이 익숙하지 않은 분들을 위해 간략한 개요를 제공해 드리겠습니다. 하지만 이 개요를 시작하기 전에 한 가지 중요한 사항을 알려드립니다.

다음 요약은 Antigravity 내의 Gemini를 사용하여 전적으로 생성되었으며, NotebookLM MCP 서버를 통해 저의 기존 NotebookLM 노트북을 쿼리(query)하고 있습니다!

제가 무슨 말을 하는지 전혀 모르셔도 걱정하지 마세요. 곧 모든 것이 명확해질 것입니다.

다음은 아래 요약을 생성하는 데 사용된 프롬프트(prompt)입니다.

_"제 노트북 "The Power of NotebookLM"에서 알고 있는 내용을 바탕으로 NotebookLM이 무엇인지, 주요 강점(USP) 및 핵심 기능 중 일부를 Dazbo 스타일로 간략하게 요약해 주세요."_

### Gemini/MCP/Notebook으로 생성된 개요

다음은 Gemini의 답변입니다.

### NotebookLM이란 무엇인가요?

NotebookLM은 Google의 다중 모드(multimodal) Gemini 모델을 기반으로 구축된 개인화된 AI 연구 조수입니다. 다양한 형식으로 흩어져 있는 복잡한 정보를 이해하고, 종합하고, 발표하는 데 도움을 주도록 설계되었습니다.

### 왜 그렇게 특별한가요? (주요 강점, USPs)

*   **엄격한 출처 기반:** 이것이 핵심입니다. 범용 모델은 환각 현상(hallucination)을 일으킬 수 있으며, 특히 전문적인 주제를 다룰 때 자주 발생합니다. 그러나 NotebookLM의 답변은 사용자가 제공한 문서에만 기반합니다. NotebookLM이 제시하는 모든 주장은 출처의 원문으로 직접 연결되는 인라인 인용(inline citation)을 포함합니다. AI 환각 현상에 대한 허용치가 낮은 경우에 정확히 필요한 기능입니다.
*   **1백만 토큰(Token)에 달하는 거대한 컨텍스트 창(Context Window):** 한 번에 약 70만 단어를 분석할 수 있습니다. 가장 좋은 점은 무엇일까요? 이 완전한 1백만 토큰 컨텍스트 창은 매우 관대한 무료 계층에서도 모든 사용자에게 제공됩니다.
*   **광범위한 다중 모드(Multimodal) 지원:** 다양한 입력을 처리합니다. PDF, Google Docs, Sheets, Word 문서, 이미지, 웹 URL, 심지어 YouTube 동영상까지 모두 기본으로 지원됩니다.

### 핵심 기능 (진정한 강점)

*   **심층 연구 에이전트(Deep Research Agents):** NotebookLM은 웹을 적극적으로 검색하여 새로운 출처를 찾아줍니다. 연구 계획을 수립하고, 수백 개의 사이트를 스캔하며, 수분 내에 광범위하고 풍부한 인용이 포함된 5페이지 보고서를 생성합니다. 이는 초기 문헌 검토의 수동적인 힘든 작업을 효과적으로 없애줍니다.
*   **오디오 및 비디오 개요:** 밀도 높고 지루한 자료를 두 사람이 진행하는 팟캐스트(podcast) 스타일의 매력적인 대화로 즉시 변환할 수 있습니다! 이러한 오디오 개요(Audio Overviews)를 특정 주제에 집중하도록 사용자 지정하거나 자신의 작업에 대해 토론할 수도 있습니다. 또한, 비디오 개요(Video Overviews)도 제공하는데, 이는 AI가 시각 자료를 생성하고 문서에서 다이어그램과 인용문을 가져와 복잡한 개념을 시각적으로 설명하는 내레이션 슬라이드입니다.
*   **AI 슬라이드 덱(Slide Decks) 및 인포그래픽(Infographics):** Nano Banana의 지원을 받아 NotebookLM은 연구 내용을 전문적이고 발표 준비가 된 슬라이드 덱과 인포그래픽으로 자동 변환할 수 있습니다. 이는 본질적으로 플랫폼을 완벽한 "연구-발표" 파이프라인(pipeline)으로 만듭니다.
*   **업그레이드된 스튜디오 출력(Studio Output):** NotebookLM은 단순히 질문에 답변하는 것을 넘어, 브리핑 문서(Briefing Documents), 학습 가이드(Study Guides), FAQ, 마인드 맵(Mind Maps)과 같이 실행 가능한 형식으로 콘텐츠를 능동적으로 구성합니다. 스튜디오 패널(Studio panel)을 통해 여러 형식을 생성할 수 있습니다.

_**[이미지: 내 노트북 "The Power of NotebookLM"에서 NotebookLM이 생성함]**_

### AI 도구에서 노트북과 소통하기

저는 Google Antigravity 및 Gemini CLI와 같은 도구에서 주로 작업합니다. 이러한 도구에서 다음과 같은 작업을 할 수 있다면 좋지 않을까요?

*   기존 NotebookLM 노트북 쿼리(query)
*   새로운 노트북 생성 (예: Gemini CLI에서 진행 중인 연구 기반)
*   내 노트북들을 결합하여 연구 수행

네, 가능합니다. 잠시 후에 그 방법을 보여드리겠습니다.

### 요약: MCP (Model Context Protocol)

모델 컨텍스트 프로토콜(Model Context Protocol, MCP)은 AI 모델과 에이전트(agent)가 외부 도구, API, 데이터와 안전하고 쉽게 상호작용할 수 있도록 하는 개방형 표준입니다. 모델과 에이전트가 접근할 수 있는 도구를 찾아 실행할 수 있게 해주는 범용 어댑터(universal adapter)라고 생각할 수 있습니다.

**요약하자면:**

거대 언어 모델(LLM)을 통합하는 것은 과거에는 각 데이터 소스(data source)마다 맞춤형의 불안정한 API 연결을 작성해야 했으며, 이는 전형적인 M\*N 유지 보수 악몽이었습니다. MCP는 이 문제를 해결합니다. 하드 코딩(hard-coding)된 연결 대신, LLM은 사용자의 자연어 요청을 해석하고 그때그때 어떤 도구를 사용할지 파악합니다. 사용자가 어떤 작업을 요청하면, LLM은 MCP 클라이언트(client)에게 필요한 도구를 알려주고, 클라이언트는 해당 도구가 있는 올바른 서버로 요청을 전달하여 데이터를 가져오거나 작업을 실행합니다.

_**[이미지: 내 노트북에서 NotebookLM이 생성함: MCP를 사용하여 에이전트 통합하기]**_

MCP는 종종 "AI 애플리케이션을 위한 USB-C"로 묘사되는 범용 표준을 생성하기 때문에, 엄청난 수준의 즉시 사용 가능한 재사용성(reusability)을 제공합니다. 개발자는 MCP 서버를 한 번 구축하면, 어떤 MCP 호환 AI 클라이언트(예: Gemini CLI, Antigravity, 또는 자체 에이전트)라도 즉시 연결할 수 있습니다.

### NotebookLM MCP 서버

저는 "사용할 수 있는 기존 NotebookLM MCP 서버가 있을까?"라고 생각했습니다.

물론 있습니다! 아주 짧은 검색 끝에 [https://github.com/jacob-bd/notebooklm-mcp-cli](https://github.com/jacob-bd/notebooklm-mcp-cli)를 찾았습니다. 그리고 이 도구는 분명히 매우 인기가 많습니다.

_**[이미지: NotebookLM CLI MCP]**_

### 설정

제가 어떻게 작동시켰는지 보여드리겠습니다. 단 몇 분밖에 걸리지 않았습니다. 저는 WSL Ubuntu를 사용하고 있으며, 이로 인해 추가 단계가 필요하다는 점을 참고하세요.

터미널에서:

```bash
# Linux 또는 WSL에서 실행하는 경우, Chrome 또는 Chromium을 실행할 수 있는지 확인합니다. 예:
sudo apt install chromium-browser

# 이제 NotebookLM MCP CLI를 설치합니다.
uv tool install notebooklm-mcp-cli
```

`nlm` 명령을 실행하여 NotebookLM CLI(Command Line Interface)가 작동하는지 확인해 봅시다.

_**[이미지: nlm 명령어]**_

지금까지는 순조롭습니다!

다음으로, CLI/MCP가 내 노트북에 접근할 수 있도록 `nlm`으로 인증하겠습니다.

```bash
nlm login
```

좋습니다, 인증되었습니다.

마지막으로, MCP 설정을 업데이트하여 우리가 선택한 도구에 MCP 구성을 추가해 봅시다. 예를 들어:

*   Antigravity에서는 이것을 `.gemini/antigravity/mcp_config.json`에 추가합니다.
*   Gemini CLI에서는 `.gemini/settings.json`에 추가합니다.

추가해야 할 구성은 다음과 같습니다.

```json
  "mcpServers": {
    // other MCP servers
    "notebooklm-mcp": {
      "command": "uvx",
      "args": ["--from", "notebooklm-mcp-cli", "notebooklm-mcp"]
    }
  }
```

### 시도해보기

이제 Gemini CLI에서 `/mcp`를 실행하면 출력에 다음 내용이 포함된 것을 볼 수 있습니다.

_**[이미지: Gemini CLI의 NotebookLM MCP 도구]**_

이제 자연어 명령을 사용하여 다음과 같은 작업을 수행할 수 있습니다.

*   노트북 목록 보기
*   노트북 생성 및 삭제
*   노트북 쿼리
*   이메일로 공동 작업자 초대
*   출처 추가
*   특정 주제에 대한 심층 연구 수행
*   팟캐스트, 비디오, 슬라이드 덱, 인포그래픽, 마인드 맵 등 스튜디오 결과물 생성
*   NotebookLM 결과물 다운로드 (예: 오디오 팟캐스트는 MP3, 비디오는 MP4, 보고서는 마크다운(markdown), 슬라이드 덱은 PDF 또는 PPTX, 인포그래픽은 PNG 등)

Google Antigravity에서 자연어 쿼리를 몇 가지 시도해 봅시다.

_**"제가 가지고 있는 NotebookLM 노트북은 무엇인가요?"**_

_**[이미지: NotebookLM MCP가 내 노트북 목록을 보여줌]**_

_**"NotebookLM과 함께 작동하는 어떤 도구들이 있나요?"**_

_**[이미지: NotebookLM MCP가 도구에 대해 알려줌]**_

### "The Power of NotebookLM" 노트북으로 작업하기

저는 이 블로그를 가져와 NotebookLM에 대한 기존 노트북에 추가하고 싶었습니다. 네, 좀 메타적(meta)이라는 걸 압니다.

먼저, 이 노트북을 구성하는 출처들을 살펴보겠습니다.

_**[이미지: 내 노트북의 출처들]**_

그래서 저는 이 블로그의 PDF를 생성하고(아직 작성 중일 때), Antigravity 작업 공간(workspace)의 루트(root)에 추가한 다음, Gemini 에이전트에게 다음 프롬프트를 제공했습니다.

_"제 "Integrate NotebookLM" PDF를 제 "Power of NotebookLM" 노트북에 추가해 주세요."_

실제 PDF 파일명은 "Integrate NotebookLM with Gemini CLI, Google Antigravity or Other Agents with MCP.pdf"였지만, 물론 Gemini는 제가 어떤 파일을 말하는지 쉽게 파악할 수 있습니다.

_**[이미지: Antigravity 내에서 내 노트북에 추가하기]**_

정말 빨랐습니다! 제대로 작동했는지 볼까요...

_**[이미지: 내 노트북의 출처들 — 하나 더 추가되었습니다!]**_

자, 여기 있습니다! 새 출처가 추가되었습니다. 정말 쉬웠습니다.

이제 Antigravity에서 이 주제에 대한 인포그래픽을 만들어 봅시다. 다음은 제가 사용한 프롬프트입니다.

_"해당 노트북에 인포그래픽을 만들어주세요. NotebookLM MCP를 사용하여 에이전트 클라이언트(Gemini CLI, Antigravity 또는 기타 에이전트 포함)를 NotebookLM 노트북에 통합하는 방법을 보여주세요. 흰색 배경에 현대적인 디자인이어야 합니다."_

Antigravity Gemini 에이전트는 즉시 `studio_create` 도구를 사용해야 한다고 판단합니다. 다음은 Antigravity의 출력입니다.

_**[이미지: "인포그래픽 생성..."에 대한 Antigravity의 응답]**_

물론 인포그래픽 생성은 시간이 좀 걸리고 NotebookLM에 의해 비동기적(asynchronously)으로 생성됩니다. 몇 분 후, 인포그래픽이 생성되었고, 제 Antigravity 작업 공간에도 직접 저장되었습니다!

_**[이미지: 인포그래픽이 생성되었습니다.]**_

그리고 여기 인포그래픽이 있습니다.

_**[이미지: NotebookLM과 MCP 통합에 대한 인포그래픽]**_

마음에 듭니다. 이 블로그의 헤더 이미지로 사용하겠습니다.

### 요약

Antigravity 내 에이전트를 통해 NotebookLM이 이 블로그의 요약을 작성하도록 해봅시다. 다음은 제가 사용한 프롬프트입니다.

_"제 노트북의 "Integrate NotebookLM" 출처를 기반으로 Dazbo 스타일의 요약을 작성해 주세요."_

답변은 꽤 좋았습니다. 다음은 제가 약간 수정하고 단축한 내용입니다.

결론은 이렇습니다. 과거에는 NotebookLM과 같은 매우 강력한 연구 엔진을 자체 맞춤형 AI 에이전트에 통합하는 데 많은 노력이 필요했습니다.

하지만 모델 컨텍스트 프로토콜(Model Context Protocol, MCP)은 본질적으로 "AI 애플리케이션을 위한 궁극의 USB-C"이며, 이를 통해 모든 어려움이 사라집니다. 좋아하는 AI 환경(Gemini CLI, Google Antigravity 또는 자체 맞춤형 에이전트 등)에 NotebookLM MCP 서버를 추가하기만 하면, 자연어만으로 NotebookLM을 제어할 수 있는 기능이 즉시 활성화됩니다.

생각해 보세요. 에이전트에게 새 노트북을 시작하고, 수백 개의 출처에서 데이터를 상호 참조하고, 웹에서 새로운 참조 자료를 동적으로 검색하거나, 팟캐스트나 슬라이드 덱 생성을 시작하도록 요청할 수 있습니다. 이 모든 것을 명령줄(command line)이나 통합 개발 환경(IDE)을 벗어나지 않고도 할 수 있습니다.

이는 NotebookLM을 훌륭한 독립형 웹 앱에서 자율 에이전트에 동력을 공급하는 강력하고 풍부한 인용 기반 백엔드 연구 서비스로 완전히 변화시킵니다.

### 마치기 전에

*   관심 있을 만한 분들과 이 글을 공유해 주세요. 그분들께 도움이 될 수 있고, 저에게도 큰 도움이 됩니다!
*   50회 박수를 쳐주세요! (박수 버튼을 길게 누르세요.)
*   자유롭게 댓글을 남겨주세요 💬.
*   제 콘텐츠를 놓치지 않도록 팔로우하고 구독해 주세요.

### 유용한 링크 및 참고 자료

*   [NotebookLM](https://notebooklm.google)
*   [NotebookLM은 Google의 엄청나게 멋진 개인 AI 연구 조수입니다.](https://medium.com/google-cloud/notebooklm-is-googles-insanely-cool-personal-ai-research-assistant-8a4866657c9c)
*   [Google Antigravity](https://antigravity.dev/)
*   [Gemini CLI](https://github.com/google/gemini-cli)
*   [NotebookLM MCP CLI](https://github.com/jacob-bd/notebooklm-mcp-cli)
*   [내 포트폴리오 / 블로그 / 애플리케이션](https://dazbo.dev)

---

_Google Cloud - Community에 게시됨_
_7.3만 팔로워 · 마지막 게시: 5시간 전_

_Google Cloud 개발자 옹호자들이 발행하거나 선별한 기술 문서 및 블로그 모음입니다. 표현된 견해는 저자의 것이며 Google의 견해와 반드시 일치하지는 않습니다._

_작성자: Dazbo (Darren Lester)_
_1.7천 팔로워 · 58명 팔로잉_

_클라우드 아키텍트(Cloud Architect)이자 적당한 괴짜입니다. Google Cloud 전도사입니다. 새로운 것을 배우는 것을 좋아하지만, 제 뇌는 작습니다. 그래서 무언가 들어가면, 무언가가 빠져나갑니다!_

---

### 답변 (1)

**Cloudcore.Pk** (5일 전)
NotebookLM + Gemini + MCP = 잠들지 않고, 환각 현상을 일으키지 않으며, 실제로 출처를 인용하는 연구 조수. 정말 놀랍습니다.
_5_
_답글_

---

### Dazbo (Darren Lester) 및 Google Cloud - Community의 추가 글

*   **Google Cloud - Community / Dazbo (Darren Lester)**
    *   **WSL에서 Google Antigravity 사용하기**
    *   WSL에서 Antigravity를 설정하는 것은 아주 간단합니다. 하지만 알아야 할 몇 가지 사항이 있습니다. 여기서 제가 그 과정을 안내해 드릴 것입니다…
    *   _2025년 11월 24일_
*   **Google Cloud - Community / Romin Irani**
    *   **튜토리얼: Google Antigravity 시작하기**
    *   Google의 무료 실험적 에이전트 우선 개발 플랫폼인 Antigravity 튜토리얼에 오신 것을 환영합니다.
    *   _2025년 11월 20일_
*   **Google Cloud - Community / Romin Irani**
    *   **튜토리얼: Antigravity 스킬 시작하기**
    *   이 튜토리얼은 IDE를 에이전트 우선 패러다임으로 발전시키는 에이전트 기반 플랫폼인 Google Antigravity에 익숙하다고 가정합니다…
    *   _1월 14일_
*   **Dazbo (Darren Lester)**
    *   **Chrome에서 길고 전체 페이지 스크린샷 생성 (전체 화면 복사-붙여넣기용)**
    *   제가 이 기능을 어떻게 몰랐을까요? Chrome에서 길고 전체 페이지 스크린샷을 캡처합니다. 전체 페이지를 이미지로 클립보드에 복사합니다.
    *   _2024년 2월 28일_

[Dazbo (Darren Lester)의 모든 글 보기](https://medium.com/@dazbo)
[Google Cloud - Community의 모든 글 보기](https://medium.com/google-cloud)

---

### 미디엄(Medium) 추천 글

*   **Google Cloud - Community / Romin Irani**
    *   **튜토리얼: Google Antigravity 시작하기**
    *   Google의 무료 실험적 에이전트 우선 개발 플랫폼인 Antigravity 튜토리얼에 오신 것을 환영합니다.
    *   _2025년 11월 20일_
*   **에이전트 네이티브**
    *   **Claude/Codex를 대체할 7가지 로컬 LLM 패밀리 (일상 업무용)**
    *   상업용 모델에 놀랍도록 가까운 실제 성능을 제공하는 로컬에서 실행할 수 있는 오픈소스 모델 패밀리
    *   _3월 4일_
*   **Obsidian Observer / Theo Stowell**
    *   **내 Claude 코드가 이제 Obsidian에 자체 '두 번째 뇌'를 갖게 되었습니다**
    *   Obsidian 볼트(vault)에 살면서 내 워크플로우를 배우는 개인 비서로 어떻게 만들었는가…
    *   _2월 20일_

[더 많은 추천 보기](https://medium.com/feed/related-stories?id=8dd4923f66c0)

---

[도움말](https://help.medium.com/) · [상태](https://medium.statuspage.io/) · [소개](https://medium.com/about) · [채용 정보](https://medium.com/careers) · [보도 자료](https://medium.com/press) · [블로그](https://blog.medium.com/) · [개인 정보 보호](https://policy.medium.com/medium-privacy-policy-f03bf9205748) · [규칙](https://policy.medium.com/medium-rules-47f6110a145e) · [약관](https://policy.medium.com/medium-terms-of-service-91fe16df525b) · [텍스트 음성 변환](https://medium.com/m/text-to-speech)