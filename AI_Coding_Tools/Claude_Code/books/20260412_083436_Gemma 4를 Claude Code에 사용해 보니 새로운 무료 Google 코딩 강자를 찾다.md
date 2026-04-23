# 번역 기사

- **원본 URL:** https://medium.com/@joe.njenga/i-tried-gemma-4-on-claude-code-and-found-new-free-google-coding-beast-6d0995ba8645
- **번역 일시:** 2026-04-12 08:34:36

---

Gemma 4를 Claude Code에 사용해 보니 (새로운 무료 Google 코딩 강자를 찾다)

NVIDIA GPU에서 Google의 Gemma 4 코딩 모델과 함께 Claude Code를 실행하는 것을 상상해 보세요. 이보다 더 좋을 수 있을까요?

Google Gemma 4는 최근 큰 인기를 끌고 있는 최신 AI 코딩 모델이며, 저는 이를 Claude Code에서 사용해 보았습니다. 결과는 실망스럽지 않았습니다.

이전 Gemma 4 출시 관련 내용을 아시는 분이라면, 이 새로운 Google 오픈소스(Open-Source) 코딩 모델에 대해 제가 이미 기대가 컸다는 것을 아실 겁니다. 하지만 31B 모델을 로컬에서 실행하려면 상당한 하드웨어가 필요했기에, 완벽하게 테스트할 수 없었습니다. 이번 주에 상황이 달라졌습니다.

이제 Ollama는 NVIDIA와의 Blackwell GPU 파트너십을 통해 클라우드에서 Gemma 4를 실행합니다. 단 한 줄의 명령어로 LiveCodeBench에서 80%, AIME 2026에서 89.2%를 기록한 모델로 코딩할 수 있게 된 것입니다. Ollama 덕분에 새로운 AI 모델을 실행하기 위해 복잡한 하드웨어에 의존하는 시대는 점차 끝나가고 있습니다. Gemma 4의 경우, `ollama launch claude --model gemma4:31b-cloud` 명령만 입력하면 바로 실행할 수 있습니다.

저는 이 조합을 테스트하는 데 몇 시간을 할애했습니다.
*   256K 컨텍스트 윈도우(Context Window)는 대규모 코드베이스(Codebase)를 청킹(Chunking) 없이 처리합니다.
*   네이티브 함수 호출(Native Function Calling)은 Claude Code의 에이전틱(Agentic) 워크플로우(Workflow)와 연동됩니다.
*   Apache 2.0 라이선스(License)는 어떠한 제한도 없음을 의미합니다.

이 글에서는 설정 방법, 실제 코딩 테스트(Coding Test) 실행, 그리고 이 조합에 대한 솔직한 경험을 공유할 것입니다.

### Claude Code에 완벽하게 구축된 Gemma 4

Google은 Gemini 3와 동일한 연구를 바탕으로 Gemma 4를 구축했습니다. 차이점은 사용자가 직접 로컬(Local) 또는 Ollama 클라우드(Cloud)를 통해 실행할 수 있다는 점입니다.

31B 모델은 다음과 같은 강력한 벤치마크(Benchmark)를 보여줍니다.
*   **LiveCodeBench v6:** 80% — 이는 코딩 분야 오픈소스(Open-Source) 최고 성능(SOTA)입니다.
*   **AIME 2026:** 89.2% — 동일 테스트에서 Gemma 3의 20.8%에서 크게 상승했습니다.
*   **Codeforces ELO:** 2150 — 경쟁 프로그래밍(Competitive Programming) 수준입니다.
*   **MMLU Pro:** 85.2% — 강력한 일반 추론(General Reasoning) 능력을 갖추고 있습니다.

참고로, 몇 달 전만 해도 이 수치들은 독점 모델(Proprietary Model)의 최첨단 성능이었습니다.

### 컨텍스트 윈도우 (Context Window)

31B 및 26B 모델은 256K 토큰(Token)을 지원합니다. 더 작은 E2B 및 E4B 엣지(Edge) 모델은 최대 128K를 지원합니다.

실제로, 단일 프롬프트(Prompt)에 전체 코드베이스를 청킹 없이 전달할 수 있습니다. 모델이 모든 것을 한 번에 볼 수 있을 때 다중 파일 리팩토링(Multi-file Refactoring)이 훨씬 원활해집니다.

### 네이티브 함수 호출 (Native Function Calling)

Gemma 4는 즉시 사용 가능한 구조화된 도구 사용(Structured Tool Use)을 지원합니다.

이는 Claude Code의 에이전틱 워크플로우에 중요합니다. 모델은 외부 도구(Tool), API, 다단계 작업(Multi-step Operation)을 빠르고 쉽게 호출하고 실행할 수 있습니다.

### Apache 2.0 라이선스 (License)

이전 Gemma 릴리스(Release)의 맞춤형 라이선스와 달리, Gemma 4는 Apache 2.0 라이선스로 제공됩니다. 상업적 사용, 파인튜닝(Fine-tuning), 배포(Deployment)에 제한이 없습니다. 이는 Qwen 및 대부분의 오픈-웨이트(Open-weight) 생태계와 유사한 조건입니다.

### Ollama 클라우드 Gemma 4 Claude Code

이는 고성능 GPU가 없는 개발자들에게 꼭 필요한 기능입니다.

31B 모델을 로컬에서 실행하려면 20GB 이상의 VRAM이 필요합니다. 26B MoE는 18GB를 필요로 합니다. 모든 사람이 이러한 하드웨어를 가지고 있는 것은 아닙니다.

Ollama는 NVIDIA와 파트너십을 맺고 클라우드의 Blackwell GPU에서 Gemma 4를 실행합니다. 하드웨어 요구 사항 없이 31B 모델 전체를 사용할 수 있습니다.

설정은 `ollama launch claude --model gemma4:31b-cloud` 단 한 줄의 명령어로 가능합니다.

클라우드 모델은 전체 컨텍스트 길이로 자동 실행됩니다. 로컬 모델처럼 컨텍스트 설정을 구성할 필요가 없습니다.

### Claude Code와 Gemma 4 설정하기

Gemma 4는 Ollama의 클라우드 통합을 통해 사용할 수 있습니다. 한 줄의 명령어로 시작할 수 있습니다.

**전제 조건**
시작하기 전에 다음을 확인하세요:
*   Ollama v0.15+ 설치
*   Claude Code v2.1+ 설치
*   Ollama 클라우드 계정(Ollama Cloud Account)

**1단계: Ollama 설치**
Ollama가 설치되어 있지 않다면:
*   **Mac:** `brew install ollama`
*   **Linux:** `curl -fsSL https://ollama.com/install.sh | sh`
*   **Windows:** ollama.com에서 설치 프로그램(Installer)을 다운로드하여 실행하세요.

설치 확인: `ollama --version`

**2단계: Claude Code 설치**
Claude Code가 이미 설치되어 있다면, 3단계로 건너뛰세요.
*   **Mac/Linux:** `curl -fsSL https://claude.ai/install.sh | bash`
*   **Windows PowerShell:** `irm https://claude.ai/install.ps1 | iex`

설치 확인: `claude --version`
버전 2.1.92 이상을 확인해야 합니다.

**3단계: Gemma 4 클라우드 모델 풀(Pull)**
`ollama pull gemma4:31b-cloud`

클라우드 모델은 NVIDIA의 Blackwell GPU에서 원격으로 추론(Inference)이 발생하므로 빠르게 등록됩니다.

**4단계: Gemma 4로 Claude Code 실행**
명령어는 다음과 같습니다:
`ollama launch claude --model gemma4:31b-cloud`

Ollama는 백그라운드에서 API 구성(Configuration)을 처리합니다. 환경 변수(Environment Variable)를 내보내거나 기본 URL을 수동으로 설정할 필요가 없습니다.

**5단계: 설정 확인**
Claude Code가 실행되면 `/status`를 통해 상태를 확인하세요.
다음과 같은 내용이 표시되어야 합니다:
*   Model: `gemma4:31b-cloud`
*   Anthropic base URL: `http://127.0.0.1:11434`
*   Auth token: `ANTHROPIC_AUTH_TOKEN`

### 테스트 프롬프트 (Test Prompt)

Gemma 4 모델을 사용하고 있음을 확인했다면, 이제 테스트 프롬프트를 실행할 수 있습니다.

### 클라우드 모델 이해하기

Ollama 클라우드의 Gemma 4는 로컬이 아닌 원격으로 실행됩니다.

이는 다음을 의미합니다:
*   **gemma4:31b-cloud:** Ollama 클라우드를 통해 NVIDIA Blackwell GPU에서 실행
*   **256K 컨텍스트 윈도우:** 전체 컨텍스트 길이를 제공하며 구성 불필요
*   **로컬 GPU 불필요:** 주요 작업은 NVIDIA 서버에서 수행

사용자의 코드와 프롬프트는 처리를 위해 클라우드로 전송됩니다. 독점 코드베이스(Proprietary Codebase)로 작업하는 경우 이 점을 유의해야 합니다.

### 로컬 모델 옵션

하드웨어를 갖추고 로컬 추론을 선호한다면:

**# 노트북용 (10GB+ VRAM)**
`ollama pull gemma4:e4b`
`ollama launch claude --model gemma4:e4b`

**# 워크스테이션용 (18GB+ VRAM)**
`ollama pull gemma4:26b`
`ollama launch claude --model gemma4:26b`

**# 최고 품질용 (20GB+ VRAM)**
`ollama pull gemma4:31b`
`ollama launch claude --model gemma4:31b`

로컬 모델은 프라이버시(Privacy)와 제로 레이턴시(Zero Latency)를 제공하지만, 상당한 VRAM을 필요로 합니다.

### 실제 코딩 테스트

저는 Gemma 4가 실제 코딩 시나리오에서 어떻게 작동하는지 알아보기 위해 몇 가지 테스트를 실행했습니다.

**테스트 1: 복잡한 앱 구축 (원샷)**
다음은 제 프롬프트(Prompt)입니다:
"제목, 우선순위, 마감일, 태그(Tag)를 포함한 작업을 추가하고, 우선순위별(막대 차트) 및 완료율별(진행 링) 작업을 보여주는 대시보드(Dashboard)를 제공하며, 우선순위, 태그, 날짜 범위로 작업을 필터링하고, 애니메이션(Animation)과 함께 완료 표시, 다크/라이트 모드(Dark/Light Mode) 토글, Tailwind를 사용한 깔끔하고 현대적인 UI, 로컬 저장소(Local Storage)에 저장 기능을 갖춘 실시간 작업 추적기(Task Tracker)를 만들어 주세요."

Gemma 4는 자동 조종 모드(Autopilot Mode)를 활성화하고, 코드를 작성하기 전에 플래너 에이전트(Planner Agent)를 사용하여 작업을 분해했습니다.

Vite + React + Tailwind + Recharts를 스택(Stack)으로 선택했습니다. 그런 다음 "실시간"이 무엇을 의미하는지 — UI 상태만, 교차 탭 동기화, 또는 클라우드 동기화 — 에 대한 명확한 질문을 던졌습니다.

이는 제가 요청한 것이 아니었습니다. 모델은 복잡한 요청을 단계별로 나누고, 진행하기 전에 명확화를 요청했습니다.

'UI 상태만'을 선택한 후, Gemma 4는 전체 애플리케이션(Application)을 구축했습니다.
*   컴포넌트(Component)는 잘 정리되어 있었고
*   차트(Chart)는 올바르게 렌더링(Render)되었으며
*   다크 모드는 첫 시도에 작동했습니다.

차트, 필터링, 애니메이션, 로컬 저장소 영속성을 갖춘 작동하는 앱이 한 번에 완성되었습니다. 코드 구조는 깔끔했고, Tailwind 클래스(Class)는 일관된 패턴(Pattern)을 따랐습니다.

**테스트 2: 다중 파일 작업**
256K 컨텍스트 윈도우는 다중 파일 리팩토링에 도움이 될 것입니다.

저는 Gemma 4에게 기존 JavaScript 프로젝트를 TypeScript로 리팩토링하도록 요청했습니다:
"이 프로젝트를 JavaScript 대신 TypeScript를 사용하도록 리팩토링하세요. 모든 파일을 업데이트하고, 적절한 타입(Type)을 추가하며, 모든 것이 여전히 작동하는지 확인하세요."

Gemma 4는 모든 파일에서 컨텍스트를 유지했습니다. 한 파일의 타입 정의(Type Definition)를 업데이트할 때, 다른 파일의 임포트(Import)를 업데이트할 때 해당 타입을 기억했습니다.

**테스트 3: 터미널(Terminal) 작업**
터미널 기반 코딩 기능(Terminal-based Coding Capability)을 테스트했습니다:
"TypeScript, ESLint, Prettier, Jest를 사용하여 새로운 Node.js 프로젝트를 설정하세요. 모든 구성 파일(Config File)을 올바르게 구성하고, 개발(Dev), 빌드(Build), 테스트(Test), 린트(Lint)를 위한 npm 스크립트(Script)를 추가하세요."

`tsconfig.json`, `.eslintrc`, `.prettierrc`, `jest.config.js`는 모두 충돌 없이 함께 작동했습니다.

### 관찰 결과

테스트 중에 몇 가지 특징이 두드러졌습니다.
*   **자동 조종 계획(Autopilot Planning):** Gemma 4는 복잡한 작업을 단계별로 나눕니다. 코드를 작성하기 전에 계획 에이전트를 사용하여 요구 사항을 분해합니다.
*   **명확화 질문:** 요구 사항이 모호할 때 모델은 현명한 질문을 합니다.
*   **코드 품질(Code Quality):** 생성된 코드는 깔끔합니다. 컴포넌트는 잘 정리되어 있습니다.
*   **컨텍스트 유지:** 다중 파일 작업이 원활하게 작동합니다.
*   **속도:** 클라우드 추론은 빠릅니다.

### 결론

Claude Code에서 Gemma 4는 빠른 속도와 우수한 성능을 제공합니다. Google은 강력한 오픈소스 코딩 모델을 출시했습니다.

벤치마크는 실제 성능으로 이어집니다. Ollama 클라우드 통합은 하드웨어 장벽을 제거합니다.

Claude Code와 잘 작동하는 Google 모델을 기다려왔다면, 바로 이것입니다.