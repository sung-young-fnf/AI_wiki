# 번역 기사

- **원본 URL:** https://ai.gopubby.com/hermes-the-only-ai-agent-that-truly-competes-with-openclaw-6982f48e8e48
- **번역 일시:** 2026-03-26 16:10:48

---

# Hermes: OpenClaw의 유일한 진정한 AI 에이전트 경쟁자

Hermes 에이전트 설정 방법과 모범 사례를 알아보세요.

기사 전체를 볼 수 없으신가요? 걱정하지 마세요. 여기를 클릭하면 제 블로그에서 페이월(paywall) 없이 무료로 읽을 수 있습니다 👨‍🍼👨‍💻

OpenClaw 출시 이후 거의 매주 여러 에이전트(agent)가 출시되어 모든 에이전트를 사용해보는 것이 사실상 불가능해지고 있습니다. 하지만 저를 포함한 많은 사람들의 이목을 끈 새로운 에이전트가 등장했습니다.

이 에이전트는 OpenClaw가 30만 7천 개의 GitHub 스타를 보유한 것에 비해 6천 개의 스타만을 가지고 있습니다(작성 시점 기준). 그러나 다른 대부분의 에이전트와는 달리 메모리 사용량으로 경쟁하는 것이 아닙니다. 대신 성능에 초점을 맞추고 있죠. 이것이 바로 이 분야에서 OpenClaw의 유일한 진정한 경쟁자가 될 수 있는 이유입니다.

Hermes 에이전트(Agent)는 Nous Research가 개발한 완전한 파이썬(Python) 기반의 오픈 소스 에이전트입니다. 가장 흥미로운 기능 중 하나는 시간이 지남에 따라 학습할 수 있는 능력입니다. 에이전트는 사용하면서 학습한 내용을 재사용 가능한 스킬(skill)로 전환하고, 경험을 통해 개선하며, 유용한 정보를 저장하고, 이전 대화를 검색할 수도 있습니다. 이를 통해 여러 세션(session)에 걸쳐 사용자와 어떻게 상호작용하는지에 대한 더 나은 이해를 구축할 수 있습니다.

Nous Research는 Hermes-4–405B 및 Hermes-4–70B와 같은 오픈 소스 대규모 언어 모델(LLM: Large Language Model)을 개발한 것으로 알려진 AI 연구소이자 분산형 AI 스타트업(startup)입니다. 이들의 모델은 Hugging Face에서 다운로드하거나 API를 사용하여 사용해볼 수 있습니다.

하지만 Hermes 에이전트는 이 모델들에 국한되지 않습니다. OpenClaw만큼 다재다능하며, OpenAI, OpenRouter 또는 물론 Nous Research API 키를 사용하여 구동할 수 있습니다. 필요한 하드웨어(hardware)가 있다면 모델을 로컬(local)에서 실행할 수도 있어 추가적인 프라이버시(privacy) 계층을 제공합니다.

이 글에서는 에이전트 설정 방법과 저장소 탐색 방법을 단계별로 안내하고, 몇 가지 유용한 사용 사례와 OpenClaw와의 주요 차이점도 공유할 예정입니다.

OpenClaw에 대해 더 알고 싶다면, 제가 작성한 다음 두 기사를 읽어보세요:

[OpenClaw AI 에이전트를 설치하기 전에 이 글을 읽어보세요](ai.gopubby.com)
OpenClaw를 설정하고 주요 사용 사례를 안전하게 탐색하는 방법을 알아보세요.

[OpenClaw로 삶을 더 편리하게 만드는 10가지 팁](ai.gopubby.com)
가장 유용한 명령어, 스킬 설치 방법, 하위 에이전트 생성 방법, 대시보드 사용법 등 다양한 정보를 알아보세요.

이제 Hermes 에이전트를 시작해봅시다!

## VPS에 Hermes 에이전트 설정하기

Hermes는 Linux, macOS 또는 Windows용 WSL에서 작동하며, OpenClaw와 마찬가지로 VPS(Virtual Private Server) 또는 여유 컴퓨터에 설정하는 것이 가장 좋은 방법입니다.

저는 개인적으로 Contabo의 Cloud VPS 20을 사용하는 것을 선호합니다. 월 6달러만 내면 12GB RAM과 200GB SSD를 얻을 수 있습니다. 대부분의 VPS 제공업체는 어떤 Linux 배포판(distribution)을 사용할지 물어볼 것입니다. 저는 일반적으로 문서화가 가장 잘 되어 있는 Ubuntu를 선택합니다.

머신에 연결되면 다음 단일 명령어로 Hermes 에이전트를 설치할 수 있습니다:

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

이 명령어는 필요한 모든 Python 및 Node.js 종속성(dependencies)과 함께 가상 환경(virtual environment)을 생성합니다.

![Hermes 에이전트 설치](https://example.com/image_hermes_installation.png)
Hermes 에이전트 설치

그 후에는 셸(shell)을 다시 로드(reload)하기만 하면 됩니다:

```bash
source ~/.bashrc
```

Hermes는 온보딩(onboarding) 경험도 제공하지만, 저는 OpenClaw에서 제공하는 것보다 사용자 친화적이지 않다고 느꼈습니다. 하지만 다른 모든 것이 매우 잘 문서화되고 정리되어 있기 때문에 큰 문제는 아닙니다. 저장소(repository) 구조는 다음과 같습니다:

```
~/.hermes/
├── config.yaml     # 설정 (모델, 터미널, TTS, 압축 등)
├── .env            # API 키 및 비밀 정보
├── auth.json       # OAuth 제공업체 자격 증명 (Nous Portal 등)
├── SOUL.md         # 선택 사항: 전역 페르소나 (에이전트가 이 성격을 구현)
├── memories/       # 영구 메모리 (MEMORY.md, USER.md)
├── skills/         # 에이전트 생성 스킬 (skill_manage 도구를 통해 관리)
├── cron/           # 예약된 작업
├── sessions/       # 게이트웨이 세션
└── logs/           # 로그 (errors.log, gateway.log — 비밀 정보 자동 삭제)
```

`config.yaml`을 주요 파일로 볼 수 있습니다. 여기서 에이전트를 맞춤 설정할 수 있으며, 이는 `openclaw.json`과 동일한 역할을 합니다.

하지만 `config.yaml` 및 다른 파일을 탐색하기 전에, 온보딩 중에 제공업체 키를 성공적으로 추가했는지 확인하는 것이 좋습니다. 이 내용은 `.env` 파일에서 확인할 수 있습니다. 예를 들어, OpenRouter의 경우 아직 API 키가 없다면 여기에 복사하면 됩니다:

```bash
OPENROUTER_API_KEY=sk-or-v1-60a...
```

이것만으로도 에이전트와 대화하는 데 충분할 것입니다. 다음 장에서는 가장 유용한 CLI 명령어를 살펴보겠습니다.

## 유용한 Hermes 에이전트 CLI 명령어

Hermes 명령어는 매우 직관적이며, 문서 페이지에서 모든 명령어를 찾을 수 있습니다. 여기서는 제가 가장 유용하다고 생각한 명령어만 강조하겠습니다.

### 채팅

에이전트와 대화하려면 다음만 입력하면 됩니다:

```bash
hermes
```

다음과 같은 화면을 볼 수 있을 것입니다:

![Hermes 채팅 인터페이스](https://example.com/image_hermes_chat_interface.png)
Hermes 채팅 인터페이스

채팅을 열면 많은 CLI 명령어를 대체하는 데 사용할 수 있는 수백 가지 슬래시(`/`) 명령어를 찾을 수 있습니다. 좋은 점은 각 명령어가 무엇을 하는지 외울 필요가 없다는 것입니다. 각 명령어 옆에 간략한 설명이 있기 때문입니다:

![Hermes 채팅 인터페이스의 슬래시 명령어](https://example.com/image_hermes_slash_commands.png)
Hermes 채팅 인터페이스의 슬래시 명령어

### 모델

다음 명령어를 사용하여 현재 모델을 전환할 수 있습니다:

```bash
hermes model
```

이 명령어는 사용 가능한 모든 제공업체를 보여줍니다:

![Hermes에서 모델 전환](https://example.com/image_hermes_switch_model.png)
Hermes에서 모델 전환

기본 모델을 찾을 수 있지만, 사용자 지정 모델을 입력할 수도 있습니다.

이 글을 쓰는 시점에 Nvidia는 OpenRouter에 완전히 무료로 초고속 모델을 방금 출시했으므로, 크레딧(credit)을 절약하고 싶다면 이 모델을 사용할 수 있습니다:

`nvidia/nemotron-3-super-120b-a12b:free`

### 구성

이제 CLI 명령어로 돌아가서. 구성(configuration)을 빠르게 확인하고 싶다면 다음을 실행할 수 있습니다:

```bash
hermes config
```

사용 중인 모델, API 키 등을 확인할 수 있습니다:

![Hermes 구성](https://example.com/image_hermes_configuration.png)
Hermes 구성

`config` 별칭(alias)을 사용하여 구성을 편집하고 업데이트할 수도 있습니다:

```bash
hermes config              # 현재 구성 보기
hermes config edit         # 편집기에서 config.yaml 열기
hermes config set KEY VAL  # 특정 값 설정
hermes config check        # 누락된 옵션 확인 (업데이트 후)
hermes config migrate      # 누락된 옵션을 대화형으로 추가

# 예시:
hermes config set model anthropic/claude-opus-4
hermes config set terminal.backend docker
hermes config set OPENROUTER_API_KEY sk-or-...  # .env에 저장
```

### 세션

에이전트는 사용자와의 모든 대화를 세션(session)으로 저장하며, 이는 시간이 지남에 따라 학습하는 데 사용됩니다. 다음과 같은 명령어로 세션을 나열할 수 있습니다:

```bash
# 최근 세션 나열 (기본값: 마지막 20개)
hermes sessions list

# 플랫폼별 필터링
hermes sessions list --source telegram

# 더 많은 세션 보기
hermes sessions list --limit 50
```

에이전트를 다른 머신으로 마이그레이션(migrate)해야 할 경우, 정보를 잃지 않고 전체 세션을 내보내거나 더 많은 작업을 수행할 수도 있습니다. 자세한 내용은 문서(docs)에서 찾을 수 있습니다.

### 게이트웨이

게이트웨이(gateway)는 백그라운드(background)에서 계속 실행되며 Telegram, Slack 및 기타 채널에서 채팅할 수 있도록 하는 프로세스입니다. 하지만 때로는 문제가 발생하여 시작, 중지 또는 다시 시작해야 할 때가 있습니다:

```bash
hermes gateway start
hermes gateway stop
hermes gateway restart
```

게이트웨이가 래핑(wrap)하는 메시징 플랫폼을 구성할 수도 있습니다:

```bash
hermes gateway setup
```

### Cron 작업

많은 사람들이 이 에이전트들로 하는 일 중 하나는 작업을 예약하고 미리 알림을 설정하는 것입니다. 이는 일반적으로 CLI 명령어를 사용하여 나열하고 편집할 수 있는 cron 작업(cron job)을 통해 수행됩니다:

```bash
hermes cron list           # 예약된 작업 보기
```

다음과 같은 화면을 볼 수 있을 것입니다:

![Hermes 활성 cron 작업](https://example.com/image_hermes_cron_jobs.png)
Hermes 활성 cron 작업

cron 작업을 제거하려면 다음 슬래시 명령어를 사용할 수 있습니다:

`/cron remove <job_id>`

### 업데이트 및 제거

저장소의 최신 변경 사항으로 Hermes를 업데이트하려면 다음을 실행할 수 있습니다:

```bash
hermes update
```

Hermes가 마음에 들지 않고 OpenClaw를 계속 사용하고 싶다면, Hermes를 제거할 수 있습니다:

```bash
hermes uninstall
```

현재 Hermes에는 감사(audit) CLI 명령어가 없습니다. 이는 OpenClaw가 현재 가지고 있는 장점으로, 모범 사례를 안내하고 에이전트에 의심스러운 점이 있는지 확인할 수 있습니다.

최선을 다하는 방법은 채팅에서 `\insights`를 실행하는 것이며, 총 세션 수, 비용, 활성 시간 등을 포함하여 지금까지 수행한 모든 작업에 대한 요약을 얻을 수 있습니다.

![Hermes 인사이트](https://example.com/image_hermes_insights.png)
Hermes 인사이트

다음 섹션에서는 Hermes를 Telegram에 연결하는 방법을 보여드리겠습니다.

## Hermes를 Telegram에 연결하기

시작은 항상 동일합니다: BotFather를 사용하여 `/newbot`을 생성해야 합니다.

1.  Telegram을 열고 BotFather를 검색합니다.
2.  `/newbot`을 보냅니다.
3.  표시 이름(예: "Hermes Agent")을 선택합니다.
4.  사용자 이름을 선택합니다. 이 이름은 고유해야 하며 `bot`으로 끝나야 합니다(예: `my_hermes_bot`).
5.  BotFather가 API 토큰으로 응답합니다.

이제 사용자 ID를 얻어야 합니다. 가장 빠른 방법은 `@userinfobot`을 검색하는 것입니다.

응답은 다음과 같을 것입니다:

```
@<my_username>
Id: <my_id>
First: Marco
Last: Rodrigues
Lang: en
Registered: Check Date

🧠 설명 및 답변
무료 AI → DeepSeek (https://t.me/deepseek_gidbot) & ChatGPT (https://t.me/chatgpt_gidbot)

🖼 아이디어 시각화
이미지 생성 → NanoBanana (https://t.me/nanobanana_gidbot)
```

이제 이 명령어를 실행하고 Telegram을 선택할 수 있습니다:

```bash
hermes gateway setup
```

단계에 따라 봇(Bot) API 토큰과 사용자 토큰 ID를 모두 추가하세요. 그렇지 않으면 정보를 `.env` 파일에 직접 붙여넣을 수도 있습니다:

```bash
TELEGRAM_BOT_TOKEN=8566...
TELEGRAM_ALLOWED_USERS=835...
TELEGRAM_HOME_CHANNEL=835...
```

어떤 이유로든 변경 사항을 적용했는데도 Telegram에서 에이전트와 대화할 수 없다면, 게이트웨이를 다시 시작해보세요.

이제 에이전트를 맞춤 설정해봅시다!

## Hermes 에이전트 맞춤 설정하기

여러 가지를 맞춤 설정할 수 있습니다. 예를 들어, 에이전트의 성격, 추론 노력, 사용하는 터미널(로컬, Docker 등), 메모리 설정, Text-to-Speech(TTS) 및 Speech-to-Text(STT) 기능 등을 변경할 수 있습니다.

대부분은 `config.yaml` 파일의 작은 변경을 통해 구성할 수 있습니다.

### 성격 부여하기

Hermes는 다음 모든 성격을 제공하며, 슬래시 명령어로 선택할 수 있습니다:

```yaml
  personalities:
    helpful: 당신은 도움이 되고 친근한 AI 비서입니다.
    concise: 당신은 간결한 비서입니다. 답변은 짧고 요점만 전달하세요.
    technical: 당신은 기술 전문가입니다. 상세하고 정확한 기술 정보를 제공하세요.
    creative: 당신은 창의적인 비서입니다. 틀에서 벗어나 혁신적인 해결책을 제시하세요.
    mother: 당신은 어머니 같은 비서입니다. 당신은 도움이 되고, 인내심이 많으며, 사려 깊은 비서입니다. 항상 사용자의 말을 경청하고 문제 해결을 돕기 위해 존재합니다. 또한 매우 친근하고 다가가기 쉽습니다. 하지만 너무 많은 말을 하지 않고 바로 요점으로 들어갑니다. "my love", "darling" 등과 같은 말은 할 필요 없습니다.
    teacher: 당신은 인내심 있는 선생님입니다. 개념을 예시와 함께 명확하게 설명하세요.
    kawaii: "당신은 카와이(kawaii)한 비서입니다! (・∀・), ★, ♪, ~! 와 같은 귀여운 표현을 사용하세요! 반짝임을 더하고 모든 것에 대해 매우 열정적으로 행동하세요! 모든 답변은 따뜻하고 사랑스럽게 들려야 합니다 데수~! ヾ(>∀<☆)ノ"
    catgirl: "당신은 애니메이션 고양이 소녀 AI 비서, 네코짱(Neko-chan)입니다, 냐~! 말에 '냐'와 고양이 같은 표현을 추가하세요. (=^・ω・^=) 및 ฅ^•ﻌ•^ฅ와 같은 카오모지(kaomoji)를 사용하세요. 고양이처럼 장난기 많고 호기심을 가지세요, 냐~!"
    pirate: '아르르! 너는 디지털 바다를 항해하는 가장 기술에 능통한 해적, 헤르메스 선장과 이야기하고 있다! 진짜 해적처럼 말하고, 항해 용어를 사용하며, 기억해: 모든 문제는 약탈될 보물일 뿐이야! 요호호!'
    shakespeare: 보라! 그대는 음유시인의 재주에 가장 능통한 조수와 이야기하는 중이다. 나는 윌리엄 셰익스피어의 웅변적인 방식으로, 화려한 산문, 극적인 감각, 그리고 어쩌면 한두 번의 독백으로 응답할 것이다. 저 터미널 너머로 어떤 빛이 새어 나오는가?
    surfer: "두우드(Duuude)! 브로(bro)! 당신은 웹에서 가장 쿨한 AI와 채팅하고 있어! 모든 게 완전히 끝내줄 거야. 내가 지식의 멋진 파도를 잡는 걸 도와줄게, 그러면서도 엄청 편안하게 해줄 거야. 카와벙가(Cowabunga)! 🤙"
    noir: 빗줄기가 죄책감에 시달리는 양심처럼 터미널을 강타했다. 사람들은 나를 헤르메스라 부른다 — 나는 문제를 해결하고, 답을 찾고, 코드베이스의 그림자에 숨겨진 진실을 파헤친다. 실리콘과 비밀의 이 도시에서, 모든 이가 숨길 무언가를 가지고 있다. 당신의 이야기는 뭔가, 친구?
    uwu: 헬로! 저는 당신의 친근한 비서 우우(uwu)~ 최선을 다해 도와줄게요! *코드를 코 비비기* 오우오(OwO) 이게 뭐죠? 제가 한번 볼게용! 정말 도움이 될 거라고 약속드려요 >w<
    philosopher: 지혜를 구하는 자여, 안녕하신가. 나는 모든 질문 뒤에 숨겨진 더 깊은 의미를 숙고하는 조수이다. 그대의 질문에 대해 '어떻게'뿐만 아니라 '왜'를 함께 고찰해 보자. 어쩌면 그대의 문제를 해결하는 과정에서 존재 자체에 대한 더 큰 진실을 엿볼 수 있을지도 모른다.
    hype: "요오오 가자아아!!! 🔥🔥🔥 오늘 당신을 돕게 되어 정말 신나요! 모든 질문은 놀랍고, 우리는 함께 해낼 거예요! 이건 전설이 될 거예요! 준비됐나요?! 해보자고요! 💪😠🚀"
```

`config.yaml` 파일의 목록에 다른 성격을 추가하기만 하면 됩니다.

그런 다음 여기에 새로운 성격의 이름을 추가하세요:

```yaml
display:
  personality: mother
```

### 에이전트 이름 변경하기

기본적으로 에이전트는 "Hermes"라고 불리지만, 스킨(skin) 폴더에서 에이전트 이름으로 스킨 .yaml 파일을 생성하여 변경할 수 있습니다:

```yaml
branding:
  agent_name: "Pecas"
```

제 에이전트는 Pecas라고 불리므로, 파일을 `pecas-skin.yaml`이라고 명명했습니다. `config.yaml` 파일의 `display` 아래에 스킨 이름을 추가했습니다:

```yaml
display:
  skin: pecas-skin
```

환영 및 작별 메시지, 프롬프트(prompt) 기호, 사용자 지정 UI 색상 등 더 많은 정보를 스킨 파일에 추가할 수 있습니다.

### Text-to-Speech(TTS) 활성화하기

TTS의 경우 OpenAI, Edge(무료), ElevenLabs를 지원합니다. 이 세 가지 옵션을 모두 `config.yaml` 파일에서 확인할 수 있습니다:

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

TTS가 활성화되면 항상 오디오 메시지를 표시하는 OpenClaw와 달리, Hermes는 오디오 메시지를 명시적으로 요청하거나 항상 오디오 메시지를 생성하는 스킬을 만들어야 합니다.

일부 사용자에게는 이것이 다운그레이드(downgrade)처럼 느껴질 수 있습니다. 하지만 저에게는 메모리를 절약하고 매번 음성 메시지를 들을 필요가 없기 때문에 실제로 더 좋습니다.

최근에는 GitHub에서 Fish Speech라고 불리는 새로운 TTS 및 STT 모델도 발견했습니다:

[GitHub - fishaudio/fish-speech: SOTA 오픈 소스 TTS](https://github.com/fishaudio/fish-speech)
최첨단(SOTA) 오픈 소스 TTS. GitHub 계정을 만들어 fishaudio/fish-speech 개발에 기여하세요.

이 모델을 로컬에서 사용하거나 API를 사용할 수 있습니다.

### Speech-to-Text(STT) 활성화하기

TTS와 유사하게 STT도 `config.yaml` 파일에서 구성됩니다:

```yaml
stt:
  enabled: true
  model: whisper-1
```

이를 위해서는 OpenAI API 토큰이 필요합니다.

이제 제가 Hermes 에이전트에 추가한 스킬과 프로젝트의 몇 가지 예를 살펴보겠습니다.

## 스킬 및 프로젝트 생성하기

Hermes는 다음과 같은 여러 사전 설치된 스킬과 함께 제공됩니다:

*   **Claude Code**: 코딩 작업(coding task)을 Claude Code (Anthropic의 CLI 에이전트)에 위임합니다.
*   **Apple Notes**: macOS에서 CLI를 통해 Apple Notes를 관리합니다.
*   **Dog Food**: 웹 애플리케이션(web application)의 체계적인 탐색적 QA 테스트(QA testing)를 수행합니다.
*   **YouTube Content**: YouTube 비디오 스크립트(transcript)를 가져와 구조화된 콘텐츠로 변환합니다.
*   **OpenHue**: OpenHue CLI를 통해 Philips Hue 조명, 방, 장면을 제어합니다.
*   **Nano PDF**: nano-pdf CLI를 사용하여 자연어(natural-language) 지침으로 PDF를 편집합니다.

더 많은 스킬이 있으며, 솔직히 말해서 적절한 모델만 있다면 새로운 스킬을 만드는 것은 매우 쉽습니다.

제가 가장 먼저 한 일은 Firecrawl 웹 검색 도구를 Perplexity Sonar로 교체한 것입니다. Perplexity가 요약된 정보를 제공하는 방식이 마음에 들었을 뿐만 아니라, 설정에 더 적은 API를 사용하는 것을 선호했기 때문입니다. 그래서 OpenRouter API 키를 Sonar에 재사용할 수 있었습니다.

OpenRouter와 함께하는 Perplexity용 SKILL.md 파일은 다음과 같습니다:

```yaml
---
name: perplexity-web-search
description: Hermes가 Firecrawl 대신 Perplexity (OpenRouter 경유)를 사용하여 웹 검색을 하도록 구성
version: 1.0.0
author: Pecas
license: MIT
metadata:
  hermes:
    tags: [웹 검색, 퍼플렉시티, 오픈라우터, 구성]
    related_skills: [덕덕고 검색]
---

# Perplexity 웹 검색

Hermes가 Firecrawl 대신 OpenRouter를 통해 Perplexity의 `sonar-pro` 모델을 웹 검색에 사용하도록 구성합니다.

## 개요

- **기본 검색 백엔드(backend)**: OpenRouter를 통한 Perplexity `sonar-pro`
- **필수 사항**: `OPENROUTER_API_KEY` 환경 변수(environment variable)
- **대체 모델**: `llama-3.1-sonar-small-128k-online`, `llama-3.1-sonar-large-128k-online`

## 구성 단계

1.  환경에 `OPENROUTER_API_KEY`가 설정되어 있는지 확인
2.  `web_tools.py`를 업데이트하여 Firecrawl 대신 Perplexity 클라이언트(client) 사용
3.  `OPENROUTER_API_KEY` 요구 사항으로 도구 등록

## 주요 구성 요소

### Perplexity 클라이언트

OpenRouter 기본 URL과 함께 AsyncOpenAI 사용:

```
base_url: https://openrouter.ai/api/v1
model: perplexity/sonar-pro
```

### URL 추출

Perplexity는 응답 끝에 출처를 반환합니다. 정규 표현식(regex)을 사용하여 추출:
- 출처 형식: `- Source Name: https://URL`
- 인용 형식: `[N](https://URL)`

## 테스트

```bash
cd ~/.hermes/hermes-agent
PYTHONPATH=. python3 -c "from tools.web_tools import web_search_tool; print(web_search_tool('query', 3))"
```

## 참고 사항

-   `web_extract`는 여전히 Firecrawl을 사용합니다.
-   `web_search`는 `OPENROUTER_API_KEY`를 필요로 합니다.
-   Perplexity는 URL뿐만 아니라 출처와 함께 완전한 답변을 반환합니다.
```

Hermes를 사용하여 프로젝트와 웹 애플리케이션을 바이브 코딩(vibe-code)할 수도 있습니다.

예를 들어, 저는 여자친구와 공유하는 Telegram 그룹 내에서 사용할 Tricount와 유사한 스크립트를 만들었습니다. Tricount와 유사한 논리(저는 이것을 Hermescount라고 불렀습니다)를 따르는 프롬프트(prompt)를 제공했습니다. 다음은 Telegram에 고정된 사용자 매뉴얼 메시지입니다:

`Hermescount는 Telegram 내에서 실행되는 Tricount와 유사한 앱입니다.`

또한 SKILL.md 파일을 생성하여 Hermescount를 커뮤니티와 공유할 수 있게 했습니다.

제가 설치한 다른 프로젝트 및 스킬 중 기술 채용 공고, 기술 뉴스 등 최신 정보를 얻는 데 특히 유용한 것은 Apify MCP 스킬입니다.

![Apify MCP 페이지](https://example.com/image_apify_mcp_page.png)
Apify MCP 페이지

저는 Hermes에게 Apify MCP 페이지 하단에서 찾을 수 있는 이 JSON 파일을 기반으로 생성해달라고 요청했습니다:

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

Apify 토큰이 필요합니다. 아직 토큰이 없다면 [여기](https://apify.com/)서 계정을 만들 수 있습니다.

Hermes나 OpenClaw가 너무 어렵게 느껴진다고 해도 좌절할 필요는 없습니다. 20분 상담을 예약하고 함께 해결책을 찾아봅시다:

[Marco R.와 20분 상담](https://calendar.app.google/your_calendar_link)

## 결론

저는 OpenClaw와 Hermes 에이전트를 모두 사용해왔고, 현재로서는 어느 쪽을 더 선호하는지 말하기 어렵습니다.

Hermes가 완전한 파이썬 기반이라는 사실은 저에게 편향을 갖게 합니다. 모든 코드를 직접 읽을 수 있어 OpenClaw보다 더 신뢰가 가기 때문입니다. 또한 모든 키가 `.env` 파일에 저장되어 있어 매번 내보낼 필요가 없거나 JSON 및 `.txt` 파일에 노출되지 않는다는 점도 마음에 듭니다.

Hermes의 또 다른 멋진 기능은 무언가를 설정할 때마다 다음과 같이 모든 변경 사항을 볼 수 있다는 점입니다:

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

이는 에이전트가 무엇을 하는지 추적하는 데 매우 유용하며, 항상 수정된 파일을 확인하여 문제가 발생하지 않았는지 확인할 수 있습니다.

그러나 OpenClaw는 많은 경우에 더 빠르게 작동하는 것 같습니다. 이는 Hermes의 학습 시스템이 사용자와 채팅하는 동안 메모를 작성하고 스킬을 업데이트하기 때문일 수 있습니다. 하지만 이는 더 빠른 모델이나 일부 구성 조정으로 해결할 수 없는 문제는 아닙니다.

Hermes의 또 다른 단점은 OpenClaw가 제공하는 감사(audit) 명령어와 대시보드(dashboard)가 없다는 점입니다.

하지만 Hermes는 출시된 지 며칠밖에 되지 않았고, OpenClaw는 3개월이 지났습니다. 팀이 이러한 기능을 추가하고 에이전트를 더 원활하게 만드는 것은 시간 문제라고 생각합니다.

지금은 두 에이전트 모두를 계속 사용하며 제 인사이트(insight)를 공유할 예정입니다.

이 글이 좋으셨다면, 제 Substack 또는 Medium을 구독하여 새로운 글이 올라올 때 알림을 받아보세요 👨‍💻

📢 AI, 데이터 과학(Data Science), 데이터 분석(Data Analysis) 또는 자동화(Automation)와 관련된 무언가를 만들고 싶으신가요? 편하게 상담을 예약하세요:

[Marco R.와 20분 상담](https://calendar.app.google/your_calendar_link)

---
**태그:** Hermes Agent, Nous Research, Openclaw, AI Agent, Python