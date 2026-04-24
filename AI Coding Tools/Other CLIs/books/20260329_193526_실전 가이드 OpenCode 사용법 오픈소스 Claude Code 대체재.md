# 번역 기사

- **원본 URL:** https://generativeai.pub/hands-on-guide-how-to-use-opencode-the-open-source-claude-code-alternative-30f7be4b45a8
- **번역 일시:** 2026-03-29 19:35:26

---

# 실전 가이드: OpenCode 사용법 (오픈소스 Claude Code 대체재)

오늘 코딩 에이전트(Coding Agents)의 마법을 경험해보세요.

아마 Claude Code에 대해 들어보셨을 겁니다.

제품을 만들고, 워크플로우를 구축하며, 데이터를 분석하는 등 다양한 작업을 수행합니다. 심지어 Google의 수석 엔지니어조차 X에서 극찬했습니다.

하지만 현재 제가 생각하기에 Claude Code보다 훨씬 더 나은 제품이 있습니다.

바로 OpenCode이며, 무엇보다 오픈소스(open source)입니다.

OpenCode와 최고 등급(god-tier) 플러그인인 oh-my-opencode를 함께 사용하면, 지금 당장 프로그래밍 에이전트(programming Agent)를 시작하려는 모든 사람에게 최고의 설정이라고 확신합니다.

저처럼 ChatGPT Pro와 Gemini Ultra를 모두 구독하며(매달 수백 달러 지출)...

이 설정을 통해 기존 구독 서비스에서 훨씬 더 많은 가치를 얻을 수 있을 겁니다.

그래서 저는 이 조합을 모든 분께 추천하며, 단계별 시작 방법을 안내해 드리려고 합니다.

이 조합은 제게 중요한 깨달음을 주었습니다.

모든 사람이 프로그래밍 에이전트의 마법을 경험할 수 있는 시대가 공식적으로 시작되었습니다.

그럼 시작해 볼까요?

## 1. OpenCode 설치

첫째, OpenCode를 설치해 봅시다.

Claude Code와 같이 명령줄(command line)을 자주 사용해야 하는 도구와 달리...

OpenCode의 가장 큰 장점은 전용 클라이언트(client)가 있다는 것입니다. Antigravity와 같은 복잡한 IDE(통합 개발 환경) 인터페이스를 다룰 필요도 없고, CLI(명령줄 인터페이스)가 무엇인지 알거나 특정 명령어를 외울 필요도 없습니다.

깔끔하고 직관적인 인터페이스만 있으면 바로 대화를 시작할 수 있습니다.

OpenCode는 여기서 다운로드할 수 있습니다:
[Download OpenCode](https://opencode.ai/)
macOS, Windows, Linux용 OpenCode 다운로드
opencode.ai

Windows, Mac 등 모든 운영체제를 지원합니다.

전문 개발자라면 Linux에서 실행하거나 VS Code, Cursor, Antigravity에 통합하여 사용할 수도 있습니다. 전혀 문제없습니다.

저는 집에서 Windows를 사용하기 때문에 Windows 버전을 다운로드하여 설치했습니다.

설치 후 실행하면 금방 홈페이지가 나타날 것입니다.

지금은 다소 비어 보여 다음에 무엇을 해야 할지 궁금할 수 있습니다.

당황하지 마세요.

이제 OpenCode에 모델을 추가해 봅시다.

## 2. 모델 추가

왼쪽 하단에 있는 `+` 버튼을 클릭하여 기본 모델을 추가합니다.

엄청나게 많은 모델을 지원하며, 거의 모든 것이 포함되어 있는 것을 볼 수 있습니다.

GPT, Gemini, Claude를 사용할 수 있으며, 더 스크롤하면 GLM의 Coding Plan까지 지원합니다.

저처럼 "ChatGPT + Google" 구독 조합을 사용하고 있다면, 계속 따라오세요.

그렇지 않다면, 무료로 제공되는 GLM 4.7을 사용해도 됩니다. "빅 3(Big Three)" 모델과의 성능 차이는 있지만, 대부분의 시나리오에서 충분합니다.

Claude 멤버십에 대한 빠른 주의 사항: 멤버십이 있더라도 지금은 "Claude Max"와 같은 구독 방식으로 OpenCode에 연결하지 마세요. 정말로, 하지 마세요.

Anthropic은 최근 매우 엄격하게 대응하고 있습니다. 주말 동안 그들은 Claude Code 구독을 확인하는 OpenCode와 같은 서드파티(third-party) 채널을 차단했습니다.

심지어 많은 계정을 정지시켰습니다.

이는 상당한 반발을 불러왔습니다.

그러던 중, 예상치 못한 반전으로 OpenAI의 Codex가 OpenCode에 대한 직접 지원을 발표했습니다.

몇 시간 내에 OpenCode는 직접 인증 방식을 통해 ChatGPT 구독 패키지를 지원하게 되었습니다.

기업 간의 경쟁은 종종 놀랍도록 간단합니다.

OpenCode로 돌아와서, OpenAI를 클릭합니다. 모델 추가를 위한 두 가지 모드를 볼 수 있습니다.

ChatGPT 멤버와 API입니다.

차이점은 다음과 같습니다:

*   **API Key(API 키)**: 개발자들이 잘 아는 방식, 사용한 만큼 지불합니다.
*   **ChatGPT Pro/Plus**: ChatGPT 공식 웹사이트에서 구독하는 표준 멤버십입니다.

이 멤버십은 ChatGPT 사이트의 새로운 기능만 제공하는 것이 아닙니다. 실제로는 개발을 위한 Codex에 엄청난 양의 할당량(quota)이 포함되어 있습니다.

그리고 GPT-5.2-Codex는 속도가 엄청나게 느리지만, 논리와 코드에 있어서는 놀라울 정도로 정확합니다.

따라서 ChatGPT 멤버십이 있다면, 해당 옵션을 클릭하세요.

팝업창이 나타날 것입니다.

링크를 클릭하고, ChatGPT에 로그인하여 인증합니다.

돌아오면 모든 준비가 완료됩니다. OpenAI 모델 목록이 보일 것입니다.

즐기세요.

Gemini는 약간 더 까다롭습니다. Antigravity에서 Gemini 3 Pro와 Claude Opus 4.5 할당량을 가져오려면 플러그인이 필요합니다.

놀랍게도! Google의 Antigravity는 Claude Opus 4.5를 지원합니다.

따라서 OpenCode에서 개발을 위한 최고 수준의 모델을 원한다면, ChatGPT와 Gemini만 구독하면 됩니다. 이 두 가지만 있으면 "빅 3(Big Three)"의 모든 최고 모델에 접근할 수 있습니다.

먼저, 플러그인 `opencode-antigravity-auth`를 설치해 봅시다.

설치는 매우 간단합니다. OpenCode를 열고 다음 프롬프트(prompt)를 전송합니다.

```
Install the opencode-antigravity-auth plugin and add the Antigravity model definitions to ~/.config/opencode/opencode.json by following: https://raw.githubusercontent.com/NoeFabris/opencode-antigravity-auth/dev/README.md
```

AI 시대입니다. 플러그인 설치조차 채팅을 통해 이루어져야죠.

잠시 후에 완료될 것입니다.

그런 다음, 다시 `+` 버튼을 클릭하고 Google을 선택합니다.

새로운 옵션인 Google (Antigravity)을 통한 OAuth가 보일 것입니다. 만약 보이지 않는다면 OpenCode를 재시작하세요.

과정은 OpenAI와 완전히 동일합니다. 링크를 클릭하고, 인증하고, 로그인합니다.

성공입니다! 이제 모든 모델을 볼 수 있습니다.

Claude와 Gemini가 모두 있습니다.

좋습니다, "빅 3" 모델이 모두 설치되었습니다. 이제 세계에서 가장 럭셔리한 코딩 모델 라인업을 갖게 되었습니다.

물론, 구독 서비스가 없지만 AI 코딩을 경험하고 싶다면, GLM 4.7 또는 MiniMax 2.1을 선택하세요.

둘 다 현재 무료이며 일반적인 작업을 완벽하게 처리합니다.

이제 OpenCode에 모델이 설정되었으니, oh-my-opencode 플러그인을 설치해 봅시다.

## 3. oh-my-opencode 설치

oh-my-opencode는 커뮤니티 플러그인이며, 저는 OpenCode의 필수(must-have) 플러그인이라고 생각합니다.

게임 모드(mod)와 같다고 생각해보세요.

Skyrim을 플레이했던 것을 기억하시나요? 모드 없이는 뭔가 허전했습니다. 또는 Escape from Tarkov는 순정(vanilla)으로도 플레이할 수 있지만, 아이템 희귀도 모드를 설치하면 경험이 극적으로 향상됩니다.

oh-my-opencode는 OpenCode에 생명을 불어넣는 궁극의 플러그인입니다. 이 플러그인은 프로그래밍 에이전트를 모든 사람이 접근할 수 있도록 만듭니다.

문서에 따르면:

Windows에서 Linux로 처음 전환했을 때 설정을 조정하며 느꼈던 설렘을 기억하시나요? '해커 정신(hacker spirit)'이 희귀해진 시대에 OpenCode가 그것을 되찾아줍니다. 프로그래밍을 좋아한다면, OpenCode는 구속에서 벗어나는 상쾌한 느낌을 선사할 것입니다.

하지만 단점은? 높은 진입 장벽, 가파른 학습 곡선, 복잡한 설정입니다. 여러분의 시간은 소중합니다.

제가 해결했습니다. 당신이 하드코어 해커가 아니더라도 몇 분만 투자하면 생산성이 급증할 것입니다.

[https://github.com/code-yeongyu/oh-my-opencode](https://github.com/code-yeongyu/oh-my-opencode)

설정을 하거나 어떤 모델이 어떤 작업에 적합한지 추측하느라 시간을 낭비할 필요가 없습니다.

내장된 전문가 역할을 제공합니다.

*   **Oracle (GPT 5.2 Medium)**: 아키텍처(architecture) 및 검토를 담당합니다.
*   **Librarian (GLM-4.7)**: 문서에서 구현 내용을 찾습니다.
*   **Explorer (Grok Code)**: 코드베이스(codebases)를 빠르게 스캔합니다.
*   **Frontend (Gemini 3 Pro)**: UI 디자인 및 프런트엔드(frontend) 코드를 처리합니다.

메인 에이전트(main Agent) (Claude Opus 4.5 High)는 필요에 따라 이들을 스케줄링(scheduling)하여 여러 작업을 병렬로 실행합니다.

Claude Code, Command, Agent, Skill, MCP, Hooks 등 모든 것과 호환됩니다. 선별된 MCP와 완벽한 LSP(Language Server Protocol) 지원까지 포함합니다.

그야말로 구세주입니다.

oh-my-opencode 설치는 이전 플러그인만큼 간단합니다.

OpenCode에서 새로운 대화를 시작하고 다음 프롬프트를 전송합니다.

```
Install and configure by following the instructions here https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/refs/heads/master/README.md
```

네, 정말 이렇게 간단합니다.

잠시 후, 질문을 할 것입니다.

우리는 이미 모델을 구성했으니, 자신의 상황에 맞게 답변하면 됩니다. 저는 ChatGPT와 Gemini 구독을 사용하므로 다음과 같이 답변했습니다.

*   Claude는 없지만, `opencode-antigravity-auth`를 사용하여 Google을 통해 Claude 모델을 사용합니다.
*   네, 설정했습니다.
*   네, 설정했습니다.

만약 이 설정과 일치한다면 복사하여 붙여넣을 수 있습니다.

잠시 후, oh-my-opencode가 준비될 것입니다.

모든 에이전트가 해당 작업에 가장 적합한 모델로 지원된다는 것을 알게 될 것입니다. (구독 서비스가 없는 경우, 기본적으로 GLM-4.7이 사용됩니다.)

이걸로 끝입니다!

이제 OpenCode + oh-my-opencode를 즐기고 바이브 코딩(Vibe Coding) 여정을 시작할 수 있습니다.

나중에 OpenCode에 대해 더 깊이 알고 싶다면, 공식 문서를 강력히 추천합니다.

[Intro - Get started with OpenCode.](https://opencode.ai/)
opencode.ai

믿으세요, 바이브 코딩은 그렇게 어렵지 않습니다.

이전에는 단지 올바른 도구가 없었을 뿐입니다.

제 생각에 OpenCode + oh-my-opencode 조합은 모든 사람이 바이브 코딩을 시작하는 최고의 방법입니다.

OpenCode와 oh-my-opencode 팀의 놀라운 오픈소스 기여에 진심으로 감사드립니다.

곧 더 흥미롭고 유용한 코딩 튜토리얼을 공개할 예정이니, 계속 지켜봐 주세요.

이 글은 생성형 AI(Generative AI)에 게재되었습니다. 최신 AI 소식을 접하려면 LinkedIn에서 저희와 연결하고 Zeniteq을 팔로우하세요.

생성형 AI의 최신 소식과 업데이트를 받으려면 뉴스레터와 YouTube 채널을 구독하세요. 함께 AI의 미래를 만들어갑시다!

---

**태그:** Claude Code, AI 코딩, 바이브 코딩, Open Code

---

**게시:** 생성형 AI에 게시됨
**팔로워:** 8.2만 명
**최종 발행:** 2일 전

생성형 AI(Generative AI) 분야의 최신 뉴스, 연구, 개발 동향을 놓치지 마세요. AI 모델 업데이트, 심층 튜토리얼, 실제 적용 사례부터 사회 전반에 미치는 AI의 광범위한 영향까지 모든 것을 다룹니다. 함께하세요: jimclydegm@gmail.com

---

**작성자:** Evan | AI for Growth
**팔로워:** 111명
**팔로잉:** 320명

AI, SEO 및 성장 해킹 – 실제 사례를 간단하고 실행 가능하게. aiphotoprompt.net
팔로우

---

**댓글 (1개)**
BongBongOogle
의견을 남겨주세요.
취소
답변

Horst Herb
1월 18일

복잡한 프로젝트에 처음부터 사용해 봤습니다. 정보 수집 및 문서 작성 능력이 놀랍도록 뛰어났습니다. 심지어 훌륭했습니다. 단순한 정형화된 내용이 아니라 복잡한 작업을 위해 잘 조사된 내용이었습니다. 다른 방향을 제안했을 때…더 보기
답글