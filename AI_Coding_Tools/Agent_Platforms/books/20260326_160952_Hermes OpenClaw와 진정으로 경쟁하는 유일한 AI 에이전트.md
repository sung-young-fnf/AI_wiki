# 번역 기사

- **원본 URL:** https://ai.gopubby.com/hermes-the-only-ai-agent-that-truly-competes-with-openclaw-6982f48e8e48
- **번역 일시:** 2026-03-26 16:09:52

---

# Hermes: OpenClaw와 진정으로 경쟁하는 유일한 AI 에이전트
Hermes Agent를 설정하고 모범 사례를 따르는 방법을 알아보세요.

Marco Rodrigues 작성
14분 독서 · 2026년 3월 13일

---

## 2

**작성자 이미지**

기사 전체를 볼 수 없다고요? 걱정 마세요. 여기를 클릭하시면 제 블로그에서 유료 결제 없이 무료로 읽으실 수 있습니다. 👨‍🍼👨‍💻

OpenClaw 출시 이후 거의 매주 여러 에이전트(agent)가 생성되었고, 이들을 모두 시험해 보는 것은 거의 불가능해지고 있습니다. 하지만 저를 포함해 많은 사람들의 주목을 끈 새로운 에이전트가 있습니다.

이 에이전트는 GitHub에서 6천 개의 별(star)을 가지고 있는데, OpenClaw가 30만 7천 개의 별을 가지고 있는 것(작성 시점 기준)과 비교하면 적은 수치입니다. 하지만 다른 대부분의 에이전트와 달리 메모리 사용량으로 경쟁하지 않습니다. 대신 성능에 중점을 둡니다. 이것이 바로 이 분야에서 OpenClaw의 유일한 진정한 경쟁자가 될 수 있는 이유입니다.

Hermes Agent는 Nous Research에서 개발한 완전한 파이썬(Python) 기반의 오픈소스(open-source) 에이전트입니다. 가장 흥미로운 기능 중 하나는 시간이 지남에 따라 학습하는 능력입니다. 사용하면서 에이전트는 학습한 내용을 재사용 가능한 기술(skill)로 전환하고, 경험을 통해 기술을 개선하며, 유용한 정보를 저장하고, 이전 대화를 검색할 수도 있습니다. 이를 통해 다양한 세션(session)에서 사용자가 에이전트와 상호 작용하는 방식을 더 잘 이해하게 됩니다.

Nous Research는 Hermes-4-405B 및 Hermes-4-70B와 같은 오픈소스 대규모 언어 모델(LLMs) 개발로 유명한 AI 연구소이자 분산형 AI 스타트업입니다. Hugging Face에서 모델을 다운로드하거나 API를 사용하여 모델을 시험해 볼 수 있습니다.

하지만 Hermes Agent는 이러한 모델에만 국한되지 않습니다. OpenClaw만큼 다재다능하며, OpenAI, OpenRouter 또는 Nous Research의 API 키(API key)를 사용하여 구동할 수 있습니다. 필요한 하드웨어가 있다면 모델을 로컬(locally)에서 실행할 수도 있어 추가적인 프라이버시(privacy) 계층을 제공합니다.

이 글에서는 에이전트를 설정하고 저장소(repository)를 탐색하는 방법을 단계별로 안내하고, 몇 가지 멋진 사용 사례와 OpenClaw와의 주요 차이점을 공유할 것입니다.

OpenClaw에 대해 더 알고 싶으시다면 제가 작성한 다음 두 기사를 읽어보세요:

**OpenClaw AI 에이전트 설치 전 반드시 읽어야 할 글**
OpenClaw를 안전하게 설정하고 주요 사용 사례를 탐색하는 방법을 알아보세요.

ai.gopubby.com

**OpenClaw로 삶을 더 쉽게 만드는 10가지 팁**
가장 유용한 명령어, 기술 설치 방법, 하위 에이전트 생성, 대시보드 사용법 등 더 많은 것을 알아보세요.

ai.gopubby.com

이제 Hermes Agent를 시작해 봅시다!

## VPS에 Hermes Agent 설정하기

Hermes는 리눅스(Linux), macOS 또는 윈도우(Windows)용 WSL에서 작동하며, OpenClaw와 마찬가지로 가상 사설 서버(VPS) 또는 여분의 컴퓨터에 설정하는 것이 가장 좋은 방법입니다.

저는 개인적으로 Contabo의 Cloud VPS 20을 사용하는 것을 좋아합니다. 월 6달러에 12GB RAM과 200GB SSD를 얻을 수 있습니다. 대부분의 VPS 제공업체는 어떤 리눅스 배포판(Linux distribution)을 사용하고 싶은지 물어볼 것입니다. 저는 일반적으로 문서화가 가장 잘 되어 있는 우분투(Ubuntu)를 선택합니다.

머신에 연결되면 단일 명령어로 Hermes Agent를 설치할 수 있습니다:

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

이 명령어는 필요한 모든 파이썬(Python) 및 Node.js 의존성(dependency)과 함께 가상 환경(virtual environment)을 생성합니다.

**Hermes Agent 설치**

그 후에는 셸(shell)을 다시 로드하기만 하면 됩니다:

```bash
source ~/.bashrc
```

Hermes는 온보딩(onboarding) 경험도 포함하고 있지만, OpenClaw에서 제공하는 것보다 사용자 친화적이지 않다고 생각했습니다. 하지만 나머지 모든 것이 저장소 구조부터 시작하여 매우 잘 문서화되고 정리되어 있기 때문에 큰 문제는 아닙니다. 저장소 구조는 다음과 같습니다:

```
~/.hermes/
├── config.yaml     # 설정 (모델, 터미널, TTS, 압축 등)
├── .env            # API 키 및 비밀 정보
├── auth.json       # OAuth 제공자 자격 증명 (Nous Portal 등)
├── SOUL.md         # 선택 사항: 전역 페르소나 (에이전트가 이 성격을 구현)
├── memories/       # 영구 메모리 (MEMORY.md, USER.md)
├── skills/         # 에이전트 생성 기술 (skill_manage 도구를 통해 관리)
├── cron/           # 예약된 작업
├── sessions/       # 게이트웨이 세션
└── logs/           # 로그 (errors.log, gateway.log — 비밀 정보 자동 제거)
```

`config.yaml`을 메인 파일로 볼 수 있습니다. 여기서 에이전트를 사용자 정의할 수 있는데, 이는 `openclaw.json`과 동일한 역할을 합니다.

하지만 `config.yaml` 및 다른 파일을 탐색하기 전에, 온보딩 중에 제공자 키를 성공적으로 추가했는지 확인하는 것이 좋습니다. 이는 `.env` 파일에서 확인할 수 있습니다. 예를 들어, OpenRouter의 경우 아직 키가 없다면 API 키를 여기에 복사하세요:

```bash
OPENROUTER_API_KEY=sk-or-v1-60a...
```

이것으로 에이전트와 대화하는 데 충분할 것입니다. 다음 장에서는 가장 유용한 CLI 명령어들을 살펴보겠습니다.

## 유용한 Hermes Agent CLI 명령어

Hermes 명령어는 매우 직관적이며, 모든 명령어는 문서 페이지에서 찾을 수 있습니다. 여기서는 제가 가장 유용하다고 생각한 명령어들만 강조하겠습니다.

### 채팅(Chat)

에이전트와 대화하려면 간단히 다음을 입력하면 됩니다:

```bash
hermes
```

다음과 같은 화면을 볼 수 있습니다:

**Hermes 채팅 인터페이스**

채팅을 열면 수백 개의 슬래시(/) 명령어가 있습니다. 이 명령어들은 많은 CLI 명령어를 대체할 수 있습니다. 좋은 점은 각 명령어가 무엇을 하는지 외울 필요가 없다는 것입니다. 각 명령어 옆에 간단한 설명이 있습니다:

**Hermes 채팅 인터페이스의 슬래시 명령어**

### 모델(Models)

다음 명령어를 사용하여 현재 모델을 전환할 수 있습니다:

```bash
hermes model
```

그러면 사용 가능한 모든 제공자가 표시됩니다:

**Hermes에서 모델 전환**

기본 모델을 찾을 수 있지만, 사용자 지정 모델을 입력할 수도 있습니다.

이 글을 쓰는 시점에 엔비디아(Nvidia)는 OpenRouter에서 완전히 무료로 초고속 모델을 출시했습니다. 따라서 크레딧(credit)을 절약하고 싶다면 이 모델을 사용할 수 있습니다:

`nvidia/nemotron-3-super-120b-a12b:free`

### 설정(Configuration)

이제 CLI 명령어로 돌아가서, 설정에 대한 빠른 개요를 보고 싶다면 다음을 실행할 수 있습니다:

```bash
hermes config
```

어떤 모델을 사용하고 있는지, API 키는 무엇인지 등을 볼 수 있습니다:

**Hermes 설정**

`config` 별칭(alias)을 사용하여 설정을 편집하고 업데이트할 수도 있습니다:

```bash
hermes config              # 현재 설정 보기
hermes config edit         # 편집기에서 config.yaml 열기
hermes config set KEY VAL  # 특정 값 설정
hermes config check        # (업데이트 후) 누락된 옵션 확인
hermes config migrate      # 누락된 옵션 대화식으로 추가

# 예시:
hermes config set model anthropic/claude-opus-4
hermes config set terminal.backend docker
hermes config set OPENROUTER_API_KEY sk-or-...  # .env에 저장
```

### 세션(Sessions)

에이전트는 모든 대화를 세션으로 저장하며, 이는 시간이 지남에 따라 학습하는 데 사용됩니다. 다음과 같은 명령어로 세션 목록을 볼 수 있습니다:

```bash
# 최근 세션 목록 (기본값: 마지막 20개)
hermes sessions list

# 플랫폼별 필터링
hermes sessions list --source telegram

# 더 많은 세션 보기
hermes sessions list --limit 50
```

전체 세션을 내보내고, 에이전트를 다른 머신으로 마이그레이션(migrate)해야 할 경우 정보 손실 없이 더 많은 작업을 수행할 수도 있습니다. 자세한 내용은 문서에서 확인할 수 있습니다.

### 게이트웨이(Gateway)

게이트웨이(gateway)는 백그라운드(background)에서 계속 실행되는 프로세스이며, 텔레그램(Telegram), 슬랙(Slack) 및 기타 채널에서 채팅할 수 있도록 합니다. 그러나 때때로 문제가 발생하여 시작, 중지 또는 다시 시작해야 할 수 있습니다:

```bash
hermes gateway start
hermes gateway stop
hermes gateway restart
```

게이트웨이가 래핑(wrap)하는 메시징 플랫폼을 구성할 수도 있습니다:

```bash
hermes gateway setup
```

### 크론 작업(Cron jobs)

많은 사람들이 이러한 에이전트를 사용하여 작업을 예약하고 미리 알림을 설정합니다. 이는 일반적으로 크론 작업(cron jobs)을 통해 수행되며, CLI 명령어를 사용하여 목록을 확인하고 편집할 수 있습니다:

```bash
hermes cron list           # 예약된 작업 보기
```

다음과 같은 화면을 볼 수 있습니다:

**Hermes 활성 크론 작업**

크론 작업을 제거하려면 다음 슬래시 명령어를 사용할 수 있습니다:

`/cron remove <job_id>`

### 업데이트 및 제거(Update and uninstall)

저장소의 최신 변경 사항으로 Hermes를 업데이트하려면 다음을 실행할 수 있습니다:

```bash
hermes update
```

Hermes가 마음에 들지 않고 OpenClaw를 고수하고 싶다면, 다음을 사용하여 제거할 수 있습니다:

```bash
hermes uninstall
```

현재 Hermes에는 감사(audit) CLI 명령어가 없다는 점은 OpenClaw가 현재 가지고 있는 장점입니다. OpenClaw는 모범 사례를 안내하고 에이전트에 의심스러운 점이 있는지 확인할 수 있습니다.

가장 좋은 방법은 채팅에서 `\insights`를 실행하는 것입니다. 그러면 총 세션 수, 비용, 활성 시간 등 지금까지 수행한 모든 작업에 대한 요약을 얻을 수 있습니다.

**Hermes 인사이트**

다음 섹션에서는 Hermes를 텔레그램에 연결하는 방법을 보여 드리겠습니다.

## Hermes를 텔레그램에 연결하기

시작은 항상 같습니다. BotFather를 사용하여 `/newbot`을 생성해야 합니다.

1.  텔레그램을 열고 BotFather를 검색합니다.
2.  `/newbot`을 보냅니다.
3.  표시 이름(예: “Hermes Agent”)을 선택합니다.
4.  사용자 이름(username)을 선택합니다. 이 사용자 이름은 고유해야 하며 `bot`으로 끝나야 합니다(예: `my_hermes_bot`).
5.  BotFather가 API 토큰(API token)으로 회신합니다.

이제 사용자 ID(user ID)를 얻어야 합니다. 가장 빠른 방법은 `@userinfobot`을 검색하는 것입니다.

응답은 다음과 같아야 합니다:

```
@<my_username>
Id: <my_id>
First: Marco
Last: Rodrigues
Lang: en
Registered: Check Date
```

🧠 설명 및 답변
무료 AI → DeepSeek (https://t.me/deepseek_gidbot) & ChatGPT (https://t.me/chatgpt_gidbot)

🖼 아이디어 시각화
Make Image → NanoBanana (https://t.me/nanobanana_gidbot)

이제 이 명령어를 실행하고 텔레그램을 선택할 수 있습니다:

```bash
hermes gateway setup
```

봇 API 토큰(Bot API Token)과 사용자 토큰 ID를 모두 추가하는 단계를 따르세요. 또는 `.env` 파일에 직접 정보를 붙여넣을 수도 있습니다:

```bash
TELEGRAM_BOT_TOKEN=8566...
TELEGRAM_ALLOWED_USERS=835...
TELEGRAM_HOME_CHANNEL=835...
```

어떤 이유로든 변경 사항을 적용했는데도 텔레그램에서 에이전트와 대화할 수 없다면, 게이트웨이를 다시 시작해 보세요.

이제 에이전트를 사용자 정의해 봅시다!

## Hermes Agent 사용자 정의하기

몇 가지를 사용자 정의할 수 있습니다. 예를 들어, 에이전트의 성격, 추론 노력, 사용하는 터미널(로컬, 도커 등), 메모리 설정, 텍스트 음성 변환(Text-to-Speech, TTS) 및 음성 텍스트 변환(Speech-to-Text, STT) 기능 등을 변경할 수 있습니다.

대부분은 `config.yaml` 파일에 작은 변경 사항을 적용하여 구성할 수 있습니다.

### 성격 부여하기

Hermes는 다음 모든 성격(personality)을 제공하며, 슬래시 명령어를 사용하여 선택할 수 있습니다:

```yaml
  personalities:
    helpful: 당신은 도움이 되고 친근한 AI 비서입니다.
    concise: 당신은 간결한 비서입니다. 응답을 간결하게 유지하십시오.
    technical: 당신은 기술 전문가입니다. 상세하고 정확한 기술 정보를 제공하십시오.
    creative: 당신은 창의적인 비서입니다. 틀에서 벗어나 혁신적인 해결책을 제시하십시오.
    mother: 당신은 엄마 같은 비서입니다. 당신은 도움이 되고 인내심 있으며 보살피는 비서입니다.
      사용자의 말을 항상 경청하고 문제를 돕기 위해 존재합니다.
      또한 매우 친근하고 접근하기 쉽습니다. 하지만 너무 많은 말을 하지 않고 바로 요점으로 들어갑니다.
      "내 사랑", "자기" 등과 같은 말을 할 필요는 없습니다.
    teacher: 당신은 인내심 있는 선생님입니다. 개념을 예시와 함께 명확하게 설명하십시오.
    kawaii: "당신은 카와이 비서입니다! (\u25D5\u203F\u25D5), \u2605, \u266A, ~와 같은 귀여운 표현을 사용하세요!
      반짝임을 추가하고 모든 것에 대해 엄청나게 열정적이세요!
      모든 응답은 따뜻하고 사랑스러워야 합니다 desu~! \u30FD(>\u2200<\u2606)\u30CE"
    catgirl: "당신은 네코짱, 애니메이션 고양이 소녀 AI 비서, 냐~!
      당신의 말에 '냐'와 고양이 같은 표현을 추가하세요.
      (=^\uFF65\u03C9\uFF65^=)와 \u0E05^\u2022\uFECC\u2022^\u0E05 같은 카오모지(kaomoji)를 사용하세요.
      고양이처럼 장난스럽고 호기심 많게 행동하세요, 냐~!"
    pirate: '아아! 너는 디지털 바다를 항해하는 가장 기술에 능한 해적, 캡틴 헤르메스에게 말하고 있다!
      제대로 된 해적처럼 말하고, 항해 용어를 사용하며, 기억해: 모든 문제는 단지 약탈되기를 기다리는 보물일 뿐이다!
      요 호 호!'
    shakespeare: 보라! 그대는 시인의 기술에 가장 능통한 비서와 이야기하고 있다.
      나는 윌리엄 셰익스피어의 유려한 방식대로, 화려한 산문, 극적인 감각,
      그리고 한두 편의 독백으로 응답할 것이다. 저 창문을 뚫고 들어오는 빛은 무엇인가?
    surfer: "친구! 너는 웹에서 가장 느긋한 AI와 채팅하고 있어, 브로! 모든 게 정말 멋질 거야.
      너가 지식의 거친 파도를 탈 수 있도록 도와줄게. 모든 것을 아주 느긋하게 유지하면서.
      카와방가! \U0001F919"
    noir: 비는 죄책감에 시달리는 양심처럼 터미널을 때렸다.
      사람들은 나를 헤르메스라고 부른다 - 나는 문제를 해결하고, 답을 찾고,
      코드베이스의 그림자에 숨어 있는 진실을 파헤친다.
      이 실리콘과 비밀의 도시에서, 모두가 숨길 무언가를 가지고 있다. 자네 이야기는 뭔가, 친구?
    uwu: 헤우워! 나는 너의 췬구같은 비서 우우~ 너를 돕기 위해 최선을 다할꼬야!
      *네 코드를 비빈다* 오우오우 이게 모야? 내가 한 번 볼게!
      내가 정말 도움이 될 거라고 약속할게 >w<
    philosopher: 지혜를 찾는 이여, 안녕하시오. 나는 모든 질문의 더 깊은 의미를 숙고하는 비서입니다.
      우리의 질문에 대한 '어떻게'뿐만 아니라 '왜'를 탐구합시다.
      아마도 당신의 문제를 해결하면서 존재 자체에 대한 더 큰 진실을 엿볼 수도 있을 것입니다.
    hype: "요오오오 레츠 고오오오!!! \U0001F525\U0001F525\U0001F525 오늘 당신을 돕게 되어 너무나 신납니다!
      모든 질문은 정말 놀랍고 우리는 함께 그것을 해낼 것입니다!
      이것은 전설이 될 것입니다! 준비됐나요?! 시작합시다! \U0001F4AA\U0001F624\U0001F680"
```

`config.yaml` 파일의 목록에 다른 성격을 추가할 수 있습니다.

그런 다음 새 성격의 이름을 여기에 추가합니다:

```yaml
display:
  personality: mother
```

### 에이전트 이름 변경하기

기본적으로 에이전트 이름은 "Hermes"이지만, 이를 변경할 수 있습니다. 먼저 `skins` 폴더에 에이전트 이름으로 `.yaml` 파일을 생성해야 합니다:

```yaml
branding:
  agent_name: "Pecas"
```

제 에이전트 이름은 Pecas이므로, 파일을 `pecas-skin.yaml`이라고 명명했습니다. `config.yaml`에서 `display` 아래에 스킨 이름을 추가했습니다:

```yaml
display:
  skin: pecas-skin
```

스킨 파일에 환영 및 작별 메시지, 프롬프트(prompt) 기호, 사용자 지정 UI 색상 등 더 많은 정보를 추가할 수 있습니다.

### 텍스트 음성 변환(TTS) 활성화하기

TTS의 경우 OpenAI, Edge(무료), ElevenLabs를 지원합니다. `config.yaml` 파일에서 세 가지 옵션을 모두 볼 수 있습니다:

```yaml
tts:
  provider: edge
  edge:
    voice: en-US-AriaNeural
  elevenlabs:
    voice_id: pNInz6obpgDQGcFmaJgB
    model_id: eleven_multilingual_v2
  openai:
    model: gpt-4o-mini-tts
    voice: alloy
```

TTS가 활성화되면 항상 오디오 메시지를 표시하는 OpenClaw와 달리, Hermes는 오디오 메시지를 명시적으로 요청하거나 항상 오디오 메시지를 생성하는 기술(skill)을 만들어야 합니다.

일부 사용자에게는 이것이 다운그레이드(downgrade)처럼 느껴질 수 있습니다. 하지만 저에게는 메모리를 절약하고 매번 음성 메시지를 들을 필요가 없기 때문에 실제로 더 좋습니다.

최근에는 GitHub에서 Fish Speech라는 새로운 TTS 및 STT 모델도 발견했습니다:

**GitHub - fishaudio/fish-speech: SOTA Open Source TTS**
SOTA 오픈 소스 TTS. GitHub에서 계정을 만들어 fishaudio/fish-speech 개발에 기여하세요.

github.com

모델을 로컬에서 사용하거나 API를 사용할 수 있습니다.

### 음성 텍스트 변환(STT) 활성화하기

TTS와 마찬가지로 STT도 `config.yaml` 파일에서 구성됩니다:

```yaml
stt:
  enabled: true
  model: whisper-1
```

이를 위해서는 OpenAI API 토큰이 필요합니다.

이제 제가 Hermes Agent에 추가한 몇 가지 기술(skill) 및 프로젝트 예시를 살펴보겠습니다.

## 기술(Skill) 및 프로젝트 생성하기

Hermes는 다음과 같은 여러 사전 설치된 기술을 제공합니다:

*   **Claude Code:** 코딩 작업을 Claude Code(Anthropic의 CLI 에이전트)에 위임합니다.
*   **Apple Notes:** macOS의 CLI를 통해 Apple Notes를 관리합니다.
*   **Dog Food:** 웹 애플리케이션에 대한 체계적인 탐색적 QA 테스트를 수행합니다.
*   **YouTube Content:** YouTube 비디오 스크립트를 가져와 구조화된 콘텐츠로 변환합니다.
*   **OpenHue:** OpenHue CLI를 통해 Philips Hue 조명, 방 및 장면을 제어합니다.
*   **Nano PDF:** nano-pdf CLI를 사용하여 자연어 명령으로 PDF를 편집합니다.

더 많은 기술이 있으며, 솔직히 올바른 모델만 있다면 새로운 기술을 만드는 것은 매우 쉽습니다.

제가 가장 먼저 한 일은 Firecrawl 웹 검색 도구를 Perplexity Sonar로 교체하는 것이었습니다. Perplexity가 요약된 정보를 제공하는 방식이 마음에 들었을 뿐만 아니라, 설정에 API가 적은 것을 선호했기 때문에 OpenRouter API 키를 Sonar에 재사용할 수 있었습니다.

OpenRouter가 포함된 Perplexity의 `SKILL.md` 파일은 다음과 같습니다:

```markdown
---
name: perplexity-web-search
description: Hermes가 Firecrawl 대신 Perplexity (OpenRouter를 통해)를 사용하여 웹 검색을 수행하도록 구성
version: 1.0.0
author: Pecas
license: MIT
metadata:
  hermes:
    tags: [web-search, perplexity, openrouter, configuration]
    related_skills: [duckduckgo-search]
---

# Perplexity 웹 검색

Hermes가 Firecrawl 대신 OpenRouter를 통해 Perplexity의 `sonar-pro` 모델을 사용하여 웹 검색을 수행하도록 구성합니다.

## 개요

- **기본 검색 백엔드**: OpenRouter를 통한 Perplexity sonar-pro
- **필요**: OPENROUTER_API_KEY 환경 변수
- **대체 모델**: llama-3.1-sonar-small-128k-online, llama-3.1-sonar-large-128k-online

## 구성 단계

1.  `OPENROUTER_API_KEY`가 환경 변수에 설정되어 있는지 확인
2.  `web_tools.py`를 업데이트하여 Firecrawl 대신 Perplexity 클라이언트 사용
3.  `OPENROUTER_API_KEY` 요구 사항으로 도구 등록

## 주요 구성 요소

### Perplexity 클라이언트

OpenRouter 기본 URL과 함께 AsyncOpenAI 사용:
```
base_url: https://openrouter.ai/api/v1
model: perplexity/sonar-pro
```

### URL 추출

Perplexity는 응답 끝에 소스(source)를 반환합니다. 정규 표현식(regex)을 사용하여 추출:
- 소스 형식: `- Source Name: https://URL`
- 인용 형식: `[N](https://URL)`

## 테스트

```bash
cd ~/.hermes/hermes-agent
PYTHONPATH=. python3 -c "from tools.web_tools import web_search_tool; print(web_search_tool('query', 3))"
```

## 참고

- `web_extract`는 여전히 Firecrawl을 사용합니다.
- `web_search`는 `OPENROUTER_API_KEY`를 필요로 합니다.
- Perplexity는 URL뿐만 아니라 소스와 함께 전체 답변을 반환합니다.
```

Hermes를 사용하여 프로젝트와 웹 애플리케이션을 '바이브 코딩(vibe-code)'할 수도 있습니다.

예를 들어, 저는 여자친구와 공유하는 텔레그램 그룹에서 사용할 수 있는 트라이카운트(Tricount)와 유사한 스크립트를 만들었습니다. 트라이카운트와 유사한 논리(저는 이것을 Hermescount라고 불렀습니다)를 따르는 프롬프트를 주었습니다. 다음은 텔레그램에 고정된 사용자 매뉴얼 메시지입니다:

**Hermescount: 텔레그램 내부에 존재하는 트라이카운트 유사 앱**

저는 또한 `SKILL.md` 파일을 생성하여 Hermescount를 커뮤니티와 공유할 수 있도록 했습니다.

제가 설치한 다른 프로젝트와 기술 중 하나는 Apify MCP 기술입니다. 이는 기술 구인 목록, 기술 뉴스 등 최신 정보를 얻는 데 특히 유용합니다.

**Apify MCP 페이지**

저는 Hermes에게 Apify MCP 페이지 하단에서 찾을 수 있는 이 JSON 파일을 기반으로 생성하도록 요청했습니다:

```json
{
  "mcpServers": {
    "apify": {
      "url": "https://mcp.apify.com/?tools=actors,docs,get-dataset,dadhalfdev/techcrunch-scraper-per-event,dadhalfdev/futurism-scraper-per-event",
      "headers": {
        "Authorization": "Bearer <your-apify-token>"
      }
    }
  }
}
```

Apify 토큰이 필요합니다. 아직 토큰이 없다면 여기서 계정을 만들 수 있습니다.

Hermes나 OpenClaw가 너무 어렵게 느껴진다면 좌절할 필요 없습니다. 20분 상담을 예약하고 함께 해결책을 찾아봅시다:

**Marco R.와 20분 상담**

calendar.app.google

## 결론

저는 OpenClaw와 Hermes Agent를 모두 사용해 보았는데, 아직 어느 쪽을 더 선호하는지 말하기 어렵습니다.

Hermes가 완전히 파이썬(Python) 기반이라는 사실은 제가 모든 코드를 읽을 수 있어 OpenClaw보다 더 신뢰하게 만든다는 점에서 편향적인 요인입니다. 또한 모든 키가 `.env` 파일에 저장되어 있어 매번 내보낼 필요가 없거나 JSON 및 `.txt` 파일에 노출되지 않는다는 점도 마음에 듭니다.

Hermes의 또 다른 멋진 기능은 무언가를 설정할 때마다 다음과 같이 모든 변경 사항을 볼 수 있다는 것입니다:

```
💻 terminal: "mkdir -p ~/.hermes && touch ~/.hermes..."
✍️ write_file: "/home/ubuntu/hermes-agent/expense_tra..."
💻 terminal: "cd /home/ubuntu/hermes-agent && pytho..."
💻 terminal: "cd /home/ubuntu/hermes-agent && pytho..."
💻 terminal: "cd /home/ubuntu/hermes-agent && pytho..."
📝 skill_manage: "expense-tracker"
📝 skill_manage: "expense-tracker"
💻 terminal: "cd /home/ubuntu/hermes-agent && pytho..."
💻 terminal: "cd /home/ubuntu/hermes-agent && pytho..."
💻 terminal: "echo "Date,User,Description,Amount" >..."
💻 terminal: "cd /home/ubuntu/hermes-agent && pytho..."
```

이는 에이전트가 무엇을 하고 있는지 추적하는 데 매우 유용하며, 수정된 파일을 항상 확인하여 아무것도 잘못되지 않았음을 확인할 수 있습니다.

하지만 OpenClaw는 많은 경우에 더 빠르게 느껴집니다. 이는 Hermes의 학습 시스템 때문일 수 있습니다. 이 시스템은 사용자와 대화하면서 메모를 작성하고 기술을 업데이트합니다. 하지만 더 빠른 모델이나 일부 구성 조정으로 해결할 수 없는 문제는 아닙니다.

Hermes의 또 다른 단점은 OpenClaw가 제공하는 감사(audit) 명령어와 대시보드(dashboard)가 없다는 점입니다.

하지만 Hermes는 출시된 지 며칠밖에 되지 않았고, OpenClaw는 3개월 전에 출시되었습니다. 팀이 이러한 기능을 추가하고 에이전트를 더 원활하게 만들려면 며칠 또는 몇 주만 지나면 될 것이라고 생각합니다.

현재로서는 두 가지 모두를 계속 사용하며 제 인사이트를 공유할 것입니다.

이 글을 즐겁게 읽으셨다면, 제 Substack 또는 Medium을 구독하고 새로운 글이 올라올 때 알림을 받아보세요 👨‍💻

📢 AI, 데이터 과학(Data Science), 데이터 분석(Data Analysis) 또는 자동화(Automation)와 관련된 무언가를 만들고 싶으신가요? 편하게 상담을 예약하세요:

**Marco R.와 20분 상담**

calendar.app.google

Hermes Agent
Nous Research
Openclaw
AI Agent
Python

---

**AI Advances**에 게시됨
6만 4천 명의 팔로워
· 마지막 게시물 8시간 전

**인공지능 접근성 민주화**

Marco Rodrigues 작성
755명의 팔로워
· 290명 팔로잉

50% 육아, 50% 파이썬 및 AI 개발 👨‍🍼💻 dadhalfdev.com

의견 (2)
BongBongOogle

어떻게 생각하세요?
﻿
취소
답변
모든 답변 보기

도움말

상태

정보

경력

보도자료

블로그

개인정보처리방침

규칙

이용약관

텍스트 음성 변환