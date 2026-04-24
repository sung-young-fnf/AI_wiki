# 번역 기사

- **원본 URL:** https://medium.com/data-science-collective/run-claude-code-locally-on-apple-silicon-using-lm-studio-and-litellm-zero-cost-1416a6b984af
- **번역 일시:** 2026-03-01 19:47:40

---

# LM Studio 및 LiteLLM을 활용하여 Apple Silicon에서 Claude Code 로컬 실행하기 (무료)

**주요 하이라이트**

회원 전용 이야기

만주나트 자나르단 (Manjunath Janardhan)
팔로우
5분 독서
·
2026년 1월 21일

1.1천
12

macOS에서 MLX 모델을 사용하여 Qwen3-Coder-30B로 Claude Code를 실행하는 단계별 가이드

_이미지 출처: 만주나트 자나르단. Google Nano Banana Pro로 생성됨._

에이전트형 코딩 도구(Agentic coding tools)는 강력하지만, 대개 비용이 발생합니다. 최근까지 Claude Code를 사용한다는 것은 모든 요청을 Anthropic의 API를 통해 라우팅(routing)하고 토큰당 비용을 지불해야 한다는 의미였습니다.

이제는 더 이상 엄격히 그럴 필요가 없습니다.

Ollama는 최근 오픈소스 모델을 사용하여 Claude Code를 로컬에서 실행하는 것을 지원하기 시작했습니다. 이는 Windows, Linux 및 macOS Intel 사용자에게는 강력한 선택지입니다. 하지만 Apple Silicon (M-시리즈 Mac)에서는 Ollama가 MLX 모델을 지원하지 않아 제한적인 이점만을 제공합니다. Apple Silicon에서 MLX 모델은 GGUF 모델보다 훨씬 빠르고 효율적이며, 이는 Ollama가 M1, M2, M3 칩의 하드웨어 기능을 완전히 활용할 수 없다는 것을 의미합니다.

이 문제를 해결하기 위해, 최소한의 구성으로 LiteLLM을 사용하여 Claude Code를 LM Studio와 호환되도록 만들 수 있으며, 이를 통해 Apple Silicon에서 고성능 MLX 모델을 로컬로 실행할 수 있습니다.

이 글에서는 다음 구성 요소를 사용하여 macOS에서 Claude Code를 로컬로 실행하는 방법을 보여드리겠습니다.

*   로컬 LLM 추론(inference)을 위한 LM Studio
*   강력한 오픈소스 코딩 모델인 Qwen3-Coder-30B
*   Anthropic-to-OpenAI 프로토콜 브리지(protocol bridge) 역할을 하는 LiteLLM
*   클라우드 사용 및 API 비용 없음

이 설정은 macOS Apple Silicon에서 안정적으로 작동하며, 완전히 오프라인으로 실행되고 Docker가 필요하지 않습니다.

Windows 또는 Linux를 사용하는 경우, LiteLLM 프록시(proxy)를 설정하지 않고도 Ollama를 사용하여 Claude Code를 로컬에서 실행할 수 있습니다. 해당 플랫폼에 대해서는 다음 공식 문서를 참조하세요:

[Claude Code - Ollama](https://docs.ollama.com/guides/claude-code)
_Claude Code는 Anthropic의 에이전트형 코딩 도구로, 작업 디렉터리(working directory)의 코드를 읽고, 수정하고, 실행할 수 있습니다. 열기…_

## 구축할 내용

Claude Code는 Anthropic Messages API를 예상하는 반면, 대부분의 로컬 LLM 런타임은 OpenAI 호환 API를 노출합니다.

핵심 아이디어는 경량 번역 계층(translation layer)을 삽입하여 Claude Code가 로컬 모델과 연동되도록 하는 것입니다.

최종 아키텍처(architecture)는 다음과 같습니다:

_이미지 출처: 만주나트 자나르단. Google Nano Banana Pro로 생성됨._

이 설정이 완료되면, Claude Code는 Anthropic 클라우드와 동일하게 작동하며, 모든 것이 사용자 머신에서 실행된다는 점만 다릅니다.

### 사전 요구 사항

*   macOS (Apple Silicon에서 잘 작동함)
*   Python 3.10 이상
*   Node.js (Claude Code용)
*   LM Studio 설치됨
*   30B 모델을 위한 충분한 RAM

### 1단계: LM Studio 설정

1.  LM Studio를 엽니다.
2.  모델을 다운로드하고 로드합니다:
    *   **모델 이름:** `qwen/qwen3-coder-30b`
3.  **로컬 서버 활성화**
4.  서버가 다음 주소에서 실행 중인지 확인합니다:
    `http://localhost:1234/v1`

_이미지 출처: 만주나트 자나르단. qwen coder 30b 모델을 실행하는 LM Studio 로컬 서버._

이 엔드포인트(endpoint)는 LiteLLM이 사용할 OpenAI 호환 Chat Completions API를 노출합니다.

### 2단계: Python 가상 환경 생성

LiteLLM을 위한 깨끗한 환경을 생성합니다:

```bash
mkdir ~/litellm
cd ~/litellm
python3 -m venv venv
source venv/bin/activate
```

프록시(proxy) 지원과 함께 LiteLLM을 설치합니다:

```bash
pip install "litellm[proxy]"
```

### 3단계: LiteLLM 구성

`config.yaml` 파일을 생성합니다:

```yaml
model_list:
  - model_name: qwen3-coder
    litellm_params:
      model: openai/qwen/qwen3-coder-30b
      api_base: http://localhost:1234/v1
      api_key: lmstudio

  # Claude Code는 내부적으로 이 기본 모델로 시작합니다.
  - model_name: claude-haiku-4-5-20251001
    litellm_params:
      model: openai/qwen/qwen3-coder-30b
      api_base: http://localhost:1234/v1
      api_key: lmstudio

litellm_settings:
  drop_params: true
```

#### 이 구성이 중요한 이유

**모델 별칭(aliasing)**
Claude Code는 슬래시가 포함된 모델 이름을 안정적으로 처리하지 못합니다.
따라서 깔끔한 별칭(`qwen3-coder`)을 노출하면서도 이를 정확한 LM Studio 모델 ID에 매핑(mapping)합니다.

**Claude 기본 모델 매핑(mapping)**
Claude Code는 내부적으로 `claude-haiku-4-5-20251001`로 시작합니다.
이를 매핑하면 시작 오류를 방지할 수 있습니다.

**`drop_params: true`**
Claude Code는 로컬 모델이 지원하지 않는 Anthropic 관련 매개변수(parameter)를 보냅니다.
LiteLLM이 이를 안전하게 제거합니다.

### 4단계: LiteLLM 프록시(Proxy) 시작

동일한 디렉터리에서:

```bash
litellm --config config.yaml --port 4000
```

다음과 유사한 출력을 볼 수 있을 것입니다:

_이미지 출처: 만주나트 자나르단. 로컬에서 실행 중인 LiteLLM._

### 5단계: 설정 확인

Claude Code를 사용하기 전에 LiteLLM이 LM Studio와 통신할 수 있는지 확인합니다:

```bash
curl http://localhost:4000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer test" \
  -d '{
    "model": "qwen3-coder",
    "messages": [
      {"role": "user", "content": "Say hello"}
    ]
  }'
```

Qwen3-Coder-30B로부터 유효한 응답을 받아야 합니다.

```json
{"id":"chatcmpl-kchd8jwscaafs1wi0uyqsq","created":1769012041,
"model":"qwen/qwen3-coder-30b","object":"chat.completion",
"system_fingerprint":"qwen/qwen3-coder-30b",
"choices":[{"finish_reason":"stop","index":0,
"message":{"content":"Hello! It's nice to meet you!","role":"assistant"}}],
"usage":{"completion_tokens":10,"prompt_tokens":10,"total_tokens":20}}%                                              manjunm4@Mac medium %
```

### 6단계: Claude Code 설치 및 실행

Claude Code를 설치합니다:

```bash
npm install -g @anthropic-ai/claude-code
```

또는 macOS 또는 Linux에서

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Windows에서

```powershell
irm https://claude.ai/install.ps1 | iex
```

Claude Code가 LiteLLM을 가리키도록 설정합니다:

```bash
export ANTHROPIC_AUTH_TOKEN=litellm
export ANTHROPIC_BASE_URL=http://localhost:4000
```

로컬 모델로 Claude Code를 실행합니다:

```bash
claude --model qwen3-coder
```

_이미지 출처: 만주나트 자나르단. 로컬 모델을 실행하는 Claude CLI._

이제 Claude Code가 정상적으로 시작되고 프롬프트(prompt)에 응답하는 것을 볼 수 있을 것입니다.

## 이 설정으로 할 수 있는 것

이 구성을 통해 Claude Code는 다음을 수행할 수 있습니다:

*   다중 파일 코드베이스(codebase) 읽기 및 수정
*   테스트 및 셸(shell) 명령 실행
*   리팩터링(refactoring) 수행
*   기능 구현
*   실패한 빌드(build) 디버깅(debugging)

이 모든 것을 클라우드에 단 하나의 토큰도 보내지 않고 수행할 수 있습니다.

### 성능에 대한 참고 사항

1.  로컬 모델은 호스팅된 Claude 모델보다 느리며 전적으로 사용자 머신에 의존합니다. 제 M4 Pro 64GB 통합 메모리(Unified Memory) 환경에서는 잘 작동합니다. 더 나은 성능을 위해 더 낮은 모델을 시도해 보세요.

2.  Qwen3-Coder-30B는 특히 리팩터링(refactoring), 테스트 생성 및 리포지토리(repository) 규모 변경에서 뛰어난 성능을 발휘합니다.

## 마무리 생각

Claude Code의 에이전트형 워크플로(agentic workflow)는 Anthropic 클라우드에 묶여 있지 않습니다. 오직 Anthropic Messages API 계약만 필요로 합니다.

LM Studio, LiteLLM 및 강력한 오픈소스 코딩 모델을 결합하면 전체 경험을 로컬에서, 비공개로, 그리고 무료로 실행할 수 있습니다.

이 설정은 에이전트형 코딩(agentic coding)의 진입 장벽을 낮추고 일상적인 개발에 실용적으로 만듭니다.

### 지원

이 기사가 유익하고 가치 있다고 생각하셨다면, 여러분의 지원에 진심으로 감사드립니다:

*   다른 사람들이 이 콘텐츠를 발견할 수 있도록 Medium에서 '박수 👏'를 보내주세요 (최대 50번까지 박수를 보낼 수 있다는 것을 알고 계셨나요?). 여러분의 박수는 더 많은 독자에게 지식을 확산하는 데 도움이 될 것입니다.
*   AI 애호가 및 전문가 네트워크와 공유해 주세요.
*   간단한 영어로 AI 비디오를 설명하는 제 YouTube 채널을 구독해 주세요: [https://www.youtube.com/@AIBroEnglish](https://www.youtube.com/@AIBroEnglish)
*   LinkedIn에서 저와 연결해 주세요: [https://www.linkedin.com/in/manjunath-janardhan-54a5537/](https://www.linkedin.com/in/manjunath-janardhan-54a5537/)

읽어주셔서 감사합니다

Claude Code
Litellm
Apple Silicon
Lm Studio
Qwen

1.1천
12

**Data Science Collective에 게시됨**
90만 팔로워
·
최근 1일 전 게시됨

Medium 데이터 과학 커뮤니티의 조언, 통찰력 및 아이디어

팔로잉
**작성자: 만주나트 자나르단 (Manjunath Janardhan)**
583 팔로워
·
84명 팔로잉

Accenture의 AI/ML 전산 과학 선임 관리자로, 기업 AI, 생성형 AI(GenAI) 및 지능형 운영 구축에 21년 이상 경력을 보유하고 있습니다.

팔로우
**댓글 (12개)**
BongBongOogle

어떻게 생각하시나요?

취소
응답

**만주나트 자나르단 (Manjunath Janardhan)**

작성자

1월 31일

**업데이트:** LM Studio 0.4.1 MLX 모델이 이제 LiteLLM과 같은 프록시(proxy) 설정 없이 Claude Code를 지원합니다!
LM Studio를 업데이트하고 아래와 같이 환경 변수(env)를 설정한 후 Claude Code를 로컬에서 바로 사용할 수 있습니다!
`export ANTHROPIC_BASE_URL=http://localhost:1234`
…더 보기

49
2개 답글

답글

Jan L

1월 30일

자체 LLM을 사용하지 않는다면 왜 굳이 Claude Code를 사용하는 건가요? Claude Code가 Cursor와 같은 다른 도구들에 비해 어떤 장점이 있나요?

63
4개 답글

답글

Paco B

2월 1일

로컬 AI에 대한 더 많은 콘텐츠를 보게 되어 기쁩니다. 훌륭한 기사입니다, 정말 감사합니다 👍🏼

6
1개 답글

답글

모든 댓글 보기
**만주나트 자나르단 및 Data Science Collective의 더 많은 글**

**Data Science Collective에서**

**만주나트 자나르단 작성**

[OpenClaw 및 Ollama M2.5 클라우드/로컬을 사용하여 AI CoWork을 무료로 로컬에서 실행하는 방법: 무료 설정](https://medium.com/data-science-collective/how-to-run-ai-cowork-locally-with-openclaw-and-ollama-m2-5-cloud-locally-for-free-free-setup-d6c1c20140d3)
2월 15일

**Data Science Collective에서**

**한 헬루아 얀 (Han HELOIR YAN, Ph.D. ☕️) 작성**

[Claude Opus 4.6: 실제 변경 사항 및 중요한 이유](https://medium.com/data-science-collective/claude-opus-4-6-what-actually-changed-and-why-it-matters-2d3a3a9b3a04)
_적응형 사고, 100만 컨텍스트 및 Anthropic의 가장 스마트한 모델 뒤에 숨겨진 실제 장단점_
2월 8일

**Data Science Collective에서**

**파올로 페론 (Paolo Perrone) 작성**

[2026년 최고의 AI 에이전트 프레임워크(Agent Frameworks) (티어 목록)](https://medium.com/data-science-collective/the-best-ai-agent-frameworks-for-2026-tier-list-8e5c1e2d6b35)
_과대광고가 아닌 실제 운영 경험으로 순위가 매겨졌습니다. 모든 프레임워크를 출시해 본 사람의 관점에서._
1월 28일

**Data Science Collective에서**

**만주나트 자나르단 작성**

[Google의 TranslateGemma 번역 모델 로컬 실행: 완전 가이드](https://medium.com/data-science-collective/running-googles-translategemma-translation-model-locally-a-complete-guide-d5c2e08a6b32)
_제 노트북에 AI 번역을 완전히 오프라인으로 무료로 설정한 방법_
1월 17일
만주나트 자나르단의 모든 글 보기
Data Science Collective의 모든 글 보기
**Medium 추천**

**CodeX에서**

**메이헴코드 (MayhemCode) 작성**

[수천 명이 Mac Mini를 구매하여 빅 테크 AI 구독 문제에서 영원히 벗어나려는 이유 |…](https://medium.com/codex/why-thousands-are-buying-mac-minis-to-escape-issues-with-big-tech-ai-subscriptions-forever-f5c7c2d8c3f4)
_2026년 초에 이상한 일이 벌어졌습니다. Apple 매장에서 Mac Mini 재고가 부족해지기 시작했습니다. 기술 포럼은 설정 가이드로 폭주했습니다. 개발자들은…_
2월 15일

**AI Advances에서**

**호세 크레스포 박사 (Jose Crespo, PhD) 작성**

[Anthropic이 비트코인을 죽이고 있다](https://medium.com/ai-advances/anthropic-is-killing-bitcoin-7d5a5a7b6b3e)
_AI 네이티브(AI-native) 통화는 이미 존재합니다. 눈에 띄지 않게 숨어있으며, 암호화폐를 6자릿수 이상 능가합니다._
2월 17일

**레자 레즈바니 (Reza Rezvani) 작성**

[개발 시간을 60% 단축시킨 Claude Code 명령 10가지: 실용 가이드](https://medium.com/@rezarezvani/10-claude-code-commands-that-cut-my-dev-time-60-a-practical-guide-b3d5b2c5c3f7)
_팀 생산성을 혁신한 사용자 지정 슬래시 명령(slash commands), 서브 에이전트(subagents) 및 자동화 워크플로(automation workflows) — 복사-붙여넣기 템플릿과 함께 제공됩니다._
2025년 11월 20일

**제이콥 베넷 (Jacob Bennett) 작성**

[2026년 스태프 소프트웨어 엔지니어(Staff Software Engineer)로서 제가 실제로 사용하는 유료 구독 서비스 5가지](https://medium.com/@jacobbennett/the-5-paid-subscriptions-i-actually-use-in-2026-as-a-staff-software-engineer-5d7c7c8b8b32)
_제가 사용하는 도구들은 (대개) 넷플릭스보다 저렴합니다._
1월 19일

**Activated Thinker에서**

**셰인 콜린스 (Shane Collins) 작성**

[샘 알트만(Sam Altman)이 AI의 미래에 대한 8가지 냉혹한 진실을 밝혔다](https://medium.com/activated-thinker/sam-altman-just-dropped-8-hard-truths-about-the-future-of-ai-1d8a8b9c9d0e)
_솔직하고 대본 없는 Q&A에서 OpenAI CEO는 코딩, 스타트업 및 2026년 경제에 대한 가장 큰 오해들을 해체했습니다._
1월 28일

**Women in Technology에서**

**알리나 코프툰 (Alina Kovtun✨) 작성**

[디자인 패턴 암기 그만: 대신 이 결정 트리(Decision Tree)를 사용하세요](https://medium.com/women-in-technology/stop-memorizing-design-patterns-use-this-decision-tree-instead-2d3a3a9b3a04)
_문제점에 기반하여 디자인 패턴을 선택하세요: 어떤 객체지향(OO) 언어에서든 최소한의 과도한 엔지니어링으로 올바른 패턴을 적용하세요._
1월 29일
더 많은 추천 보기

도움말
상태
회사 소개
채용
보도 자료
블로그
개인정보처리방침
규칙
약관
음성 변환