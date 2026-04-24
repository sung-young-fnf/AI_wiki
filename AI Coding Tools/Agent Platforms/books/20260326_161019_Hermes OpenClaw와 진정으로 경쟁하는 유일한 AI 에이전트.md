# 번역 기사

- **원본 URL:** https://ai.gopubby.com/hermes-the-only-ai-agent-that-truly-competes-with-openclaw-6982f48e8e48
- **번역 일시:** 2026-03-26 16:10:19

---

# Hermes: OpenClaw와 진정으로 경쟁하는 유일한 AI 에이전트

Hermes Agent(헤르메스 에이전트)를 설정하고 모범 사례를 따르는 방법을 알아보세요.

Marco Rodrigues
팔로우
14분 독서
·
2026년 3월 13일

215

2

[전체 크기 이미지 보려면 Enter 키를 누르거나 클릭하세요.]
저자 제공 이미지

전체 기사를 볼 수 없으신가요? 걱정 마세요, [여기](https://your-blog-link.com)를 클릭하면 페이월(paywall) 없이 제 블로그에서 무료로 읽을 수 있습니다. 👨‍🍼👨‍💻

OpenClaw(오픈클로) 출시 이후 거의 매주 여러 에이전트가 생성되고 있으며, 이 모든 것을 시도해 보는 것은 거의 불가능해지고 있습니다. 하지만 저를 포함한 많은 사람들의 이목을 집중시킨 새로운 에이전트가 있습니다.

이 글을 쓰는 시점에서 OpenClaw가 GitHub(깃허브)에서 30만 7천 개의 별을 받은 것에 비해, 이 에이전트는 6천 개의 별만 받았습니다. 하지만 대부분의 다른 에이전트와는 달리 메모리 사용량으로 경쟁하는 것이 아닙니다. 대신, 성능에 초점을 맞춥니다. 이것이 바로 이 분야에서 OpenClaw의 유일한 진정한 경쟁자가 될 수 있는 이유입니다.

Hermes Agent는 Nous Research(누스 리서치)가 개발한 완전한 Python(파이썬) 기반의 오픈 소스 에이전트(open-source agent)입니다. 가장 흥미로운 기능 중 하나는 시간이 지남에 따라 학습하는 능력입니다. 에이전트를 사용하면서 학습한 내용을 재사용 가능한 기술(skills)로 바꾸고, 경험을 통해 개선하며, 유용한 정보를 저장하고, 이전 대화를 검색할 수도 있습니다. 이를 통해 다양한 세션에서 사용자가 에이전트와 상호 작용하는 방식을 더 잘 이해하게 됩니다.

Nous Research는 Hermes-4-405B 및 Hermes-4-70B와 같은 오픈 소스 대규모 언어 모델(Large Language Models, LLMs) 개발로 알려진 AI 연구소이자 분산형 AI 스타트업(decentralized AI startup)입니다. Hugging Face(허깅 페이스)에서 모델을 다운로드하거나 API(Application Programming Interface)를 사용하여 모델을 시험해 볼 수 있습니다.

하지만 Hermes Agent는 이러한 모델에만 국한되지 않습니다. OpenClaw만큼 다재다능하며, OpenAI(오픈AI), OpenRouter(오픈라우터) 또는 물론 Nous Research API 키를 사용하여 에이전트에 동력을 공급할 수 있습니다. 필요한 하드웨어(hardware)가 있다면 모델을 로컬(locally)에서 실행할 수도 있어 추가적인 개인 정보 보호 계층을 더할 수 있습니다.

이 글에서는 에이전트를 설정하고 저장소(repository)를 탐색하는 방법을 단계별로 안내하며, 몇 가지 멋진 사용 사례와 OpenClaw와의 주요 차이점을 공유할 것입니다.

OpenClaw에 대해 더 알고 싶다면, 제가 쓴 다음 두 기사를 읽을 수 있습니다:

[OpenClaw AI 에이전트를 설치하기 전에 이 글을 읽어보세요.](https://ai.gopubby.com/read-this-before-installing-the-openclaw-ai-agent-e63a12d1b714)
OpenClaw를 안전하게 설정하고 주요 사용 사례를 탐색하는 방법을 알아보세요.

ai.gopubby.com

[OpenClaw로 삶을 더 쉽게 만드는 10가지 팁](https://ai.gopubby.com/10-tips-to-make-your-life-easier-with-openclaw-df4d436a56c)
가장 유용한 명령, 기술 설치 방법, 하위 에이전트(sub-agents) 생성 방법, 대시보드(dashboard) 사용 방법 등을 알아보세요.

ai.gopubby.com

이제 Hermes Agent를 시작해 봅시다!

## VPS(가상 사설 서버)에 Hermes Agent 설정하기

Hermes는 Linux(리눅스), macOS(맥OS) 또는 Windows용 WSL(Windows Subsystem for Linux)에서 작동하며, OpenClaw와 마찬가지로 가장 좋은 접근 방식은 VPS(가상 사설 서버) 또는 여분의 컴퓨터에 설정하는 것입니다.

저는 개인적으로 Contabo(콘타보)의 Cloud VPS 20(클라우드 VPS 20)을 사용하는 것을 좋아합니다. 월 6달러(약 8천 원)에 12GB의 RAM(램)과 200GB의 SSD(솔리드 스테이트 드라이브)를 얻을 수 있습니다. 대부분의 VPS 제공업체는 어떤 Linux 배포판(distribution)을 사용할지 물어볼 것입니다. 저는 보통 가장 널리 문서화되어 있는 Ubuntu(우분투)를 선택합니다.

머신에 연결되면 다음 단일 명령으로 Hermes Agent를 설치할 수 있습니다:

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

이 명령은 필요한 모든 Python(파이썬) 및 Node.js(노드제이에스) 종속성(dependencies)을 포함하는 가상 환경(virtual environment)을 생성합니다.

Hermes Agent 설치

그 후에는 셸(shell)을 다시 로드(reload)하기만 하면 됩니다:

```bash
source ~/.bashrc
```

Hermes는 온보딩 경험(onboarding experience)도 포함하고 있지만, 저는 OpenClaw에서 제공하는 것보다 약간 덜 사용자 친화적(user-friendly)이라는 것을 발견했습니다. 하지만 그 외의 모든 것은 저장소 구조부터 매우 잘 문서화되고 체계적으로 정리되어 있기 때문에 큰 문제는 아닙니다. 저장소 구조는 다음과 같습니다:

```
~/.hermes/
├── config.yaml     # 설정 (모델, 터미널, TTS, 압축 등)
├── .env            # API 키 및 비밀 정보
├── auth.json       # OAuth 제공업체 자격 증명 (Nous Portal 등)
├── SOUL.md         # 선택 사항: 전역 페르소나 (에이전트가 이 성격을 구현함)
├── memories/       # 영구 메모리 (MEMORY.md, USER.md)
├── skills/         # 에이전트가 생성한 기술 (skill_manage 도구를 통해 관리)
├── cron/           # 예약된 작업
├── sessions/       # 게이트웨이 세션
└── logs/           # 로그 (errors.log, gateway.log — 비밀 정보 자동 편집)
```

`config.yaml` 파일을 주요 파일로 볼 수 있습니다. 여기에서 에이전트를 사용자 지정할 수 있으며, 이는 `openclaw.json`과 동일합니다.

하지만 `config.yaml` 및 기타 파일을 탐색하기 전에, 온보딩 중에 공급자 키를 성공적으로 추가했는지 확인하는 것이 좋습니다. 이 내용은 `.env` 파일에서 확인할 수 있습니다. 예를 들어, OpenRouter의 경우, 아직 없다면 API 키를 여기에 복사하기만 하면 됩니다:

```
OPENROUTER_API_KEY=sk-or-v1-60a...
```

이것만으로도 에이전트와 대화할 수 있게 될 것입니다. 다음 장에서는 가장 유용한 CLI 명령에 대해 알아보겠습니다.

## 유용한 Hermes Agent CLI(명령줄 인터페이스) 명령

Hermes 명령은 매우 직관적이며, 문서 페이지에서 모두 찾을 수 있습니다. 여기서는 제가 가장 유용하다고 생각하는 명령들만 강조하겠습니다.

### 채팅

에이전트와 대화하려면 다음만 있으면 됩니다:

```bash
hermes
```

다음과 같은 화면이 나타날 것입니다:

[전체 크기 이미지 보려면 Enter 키를 누르거나 클릭하세요.]
Hermes 채팅 인터페이스

채팅을 열면 수백 개의 슬래시(/) 명령을 찾을 수 있으며, 이는 많은 CLI 명령을 대체하는 데 사용될 수 있습니다. 좋은 점은 각 명령이 무엇을 하는지 외울 필요가 없다는 것입니다. 각 명령 옆에 간략한 설명이 있습니다:

[전체 크기 이미지 보려면 Enter 키를 누르거나 클릭하세요.]
Hermes 채팅 인터페이스의 슬래시 명령

### 모델

다음 명령을 사용하여 현재 모델을 전환할 수 있습니다:

```bash
hermes model
```

그러면 사용 가능한 모든 공급자가 표시됩니다:

[전체 크기 이미지 보려면 Enter 키를 누르거나 클릭하세요.]
Hermes에서 모델 전환

기본 모델(default models)을 찾을 수 있지만, 사용자 지정 모델을 입력할 수도 있습니다.

이 글을 쓰는 시점에서 Nvidia(엔비디아)는 OpenRouter에서 완전히 무료로 초고속 모델을 방금 출시했습니다. 따라서 크레딧을 절약하고 싶다면 이 모델을 사용할 수 있습니다:

```
nvidia/nemotron-3-super-120b-a12b:free
```

### 구성

이제 CLI 명령으로 돌아가서. 구성(configuration)에 대한 빠른 개요를 원한다면 다음을 실행할 수 있습니다:

```bash
hermes config
```

사용 중인 모델, API 키 등이 표시됩니다:

[전체 크기 이미지 보려면 Enter 키를 누르거나 클릭하세요.]
Hermes 구성

`config` 별칭(alias)을 사용하여 구성을 편집하고 업데이트할 수도 있습니다:

```bash
hermes config              # 현재 구성 보기
hermes config edit         # 편집기에서 config.yaml 열기
hermes config set KEY VAL  # 특정 값 설정
hermes config check        # 누락된 옵션 확인 (업데이트 후)
hermes config migrate      # 누락된 옵션을 대화식으로 추가

# 예시:
hermes config set model anthropic/claude-opus-4
hermes config set terminal.backend docker
hermes config set OPENROUTER_API_KEY sk-or-...  # .env에 저장
```

### 세션

에이전트는 사용자와의 모든 대화를 세션(session)으로 저장하며, 이는 시간이 지남에 따라 에이전트가 학습하도록 만드는 데 사용됩니다. 다음 명령으로 세션을 나열할 수 있습니다:

```bash
# 최근 세션 나열 (기본값: 마지막 20개)
hermes sessions list

# 플랫폼별 필터링
hermes sessions list --source telegram

# 더 많은 세션 표시
hermes sessions list --limit 50
```

정보 손실 없이 에이전트를 다른 기기로 마이그레이션해야 할 경우 전체 세션을 내보내고 더 많은 작업을 수행할 수도 있습니다. 더 자세한 내용은 문서에서 찾을 수 있습니다.

### 게이트웨이

게이트웨이(gateway)는 항상 실행되는 백그라운드 프로세스(background process)로, Telegram(텔레그램), Slack(슬랙) 및 기타 채널에서 채팅할 수 있도록 합니다. 하지만 때로는 일부 문제가 발생하여 시작, 중지 또는 다시 시작해야 합니다:

```bash
hermes gateway start
hermes gateway stop
hermes gateway restart
```

게이트웨이가 래핑하는 메시징 플랫폼을 구성할 수도 있습니다:

```bash
hermes gateway setup
```

### 크론 작업 (Cron jobs)

많은 사람들이 이러한 에이전트로 하는 일 중 하나는 작업을 예약하고 미리 알림을 설정하는 것입니다. 이는 일반적으로 크론 작업(cron jobs)을 통해 이루어지며, 다음 CLI 명령을 사용하여 나열하고 편집할 수 있습니다:

```bash
hermes cron list           # 예약된 작업 보기
```

다음과 같은 내용이 표시될 것입니다:

[전체 크기 이미지 보려면 Enter 키를 누르거나 클릭하세요.]
Hermes 활성 크론 작업

크론 작업(cron job)을 제거하려면 다음 슬래시 명령을 사용할 수 있습니다:

```
/cron remove <job_id>
```

### 업데이트 및 제거

저장소(repository)의 최신 변경 사항으로 Hermes를 업데이트하려면 다음을 실행할 수 있습니다:

```bash
hermes update
```

Hermes가 마음에 들지 않고 대신 OpenClaw를 고수하고 싶다면, 다음 명령으로 Hermes를 제거할 수 있습니다:

```bash
hermes uninstall
```

현재 Hermes에는 감사(audit) CLI 명령이 없습니다. 이는 현재 OpenClaw가 가진 장점인데, OpenClaw는 모범 사례를 안내하고 에이전트에 의심스러운 점이 있는지 확인할 수 있기 때문입니다.

할 수 있는 최선은 채팅에서 `\insights`를 실행하는 것이고, 그러면 총 세션 수, 비용, 활성 시간 등 지금까지 수행한 모든 작업에 대한 요약을 얻을 수 있습니다.

[전체 크기 이미지 보려면 Enter 키를 누르거나 클릭하세요.]
Hermes 통계(Insights)

다음 섹션에서는 Hermes를 텔레그램에 연결하는 방법을 보여드리겠습니다.

## Hermes를 텔레그램에 연결하기

시작은 항상 같습니다: BotFather(봇파더)를 사용하여 `/newbot`을 생성해야 합니다.

1.  텔레그램을 열고 BotFather를 검색하세요.
2.  `/newbot`을 보냅니다.
3.  표시 이름(display name)을 선택합니다(예: "Hermes Agent").
4.  사용자 이름(username)을 선택합니다. 이는 고유해야 하며 `bot`으로 끝나야 합니다(예: `my_hermes_bot`).
5.  BotFather가 API 토큰(API token)으로 회신합니다.

이제 사용자 ID(User ID)를 얻어야 합니다. 가장 빠른 방법은 `@userinfobot`을 검색하는 것입니다.

응답은 다음과 같아야 합니다:

```
@<내_사용자명>
Id: <내_ID>
First: Marco
Last: Rodrigues
Lang: en
Registered: Check Date
```

🧠 설명 및 답변
무료 AI → DeepSeek (https://t.me/deepseek_gidbot) & ChatGPT (https://t.me/chatgpt_gidbot)

🖼 아이디어 시각화
이미지 생성 → NanoBanana (https://t.me/nanobanana_gidbot)

이제 다음 명령을 실행하고 텔레그램을 선택할 수 있습니다:

```bash
hermes gateway setup
```

Bot API 토큰(API Token)과 토큰 ID를 모두 추가하는 단계를 따르세요. 그렇지 않으면 `.env` 파일에 직접 정보를 붙여넣을 수도 있습니다:

```
TELEGRAM_BOT_TOKEN=8566...
TELEGRAM_ALLOWED_USERS=835...
TELEGRAM_HOME_CHANNEL=835...
```

어떤 이유로든 변경 사항을 적용했는데도 여전히 텔레그램에서 에이전트와 대화할 수 없다면, 게이트웨이를 다시 시작해 보세요.

이제 에이전트를 사용자 지정해 봅시다!

## Hermes Agent 사용자 지정하기

여러 가지를 사용자 지정할 수 있습니다. 예를 들어, 에이전트의 성격(personality), 추론 노력(reasoning effort), 사용하는 터미널(terminal)(로컬, Docker 등), 메모리 설정, Text-to-Speech(TTS) 및 Speech-to-Text(STT) 기능 등을 변경할 수 있습니다.

이 대부분은 `config.yaml` 파일에 작은 변경 사항을 적용하여 구성할 수 있습니다.

### 성격 부여하기

Hermes는 다음과 같은 모든 성격을 가지고 있으며, 슬래시 명령으로 선택할 수 있습니다:

```yaml
  personalities:
    helpful: 당신은 도움이 되고 친절한 AI 비서입니다.
    concise: 당신은 간결한 비서입니다. 답변을 짧고 요점에 집중하세요.
    technical: 당신은 기술 전문가입니다. 상세하고 정확한 기술 정보를 제공하세요.
    creative: 당신은 창의적인 비서입니다. 틀에 얽매이지 않고 혁신적인 솔루션을 제공하세요.
    mother: 당신은 어머니 같은 비서입니다. 당신은 도움이 되고, 인내심이 많으며, 배려심 있는 비서입니다.
      항상 사용자의 말을 경청하고 문제 해결을 돕기 위해 존재합니다.
      또한 매우 친절하고 친근합니다. 하지만 너무 많은 이야기를 하지 않고 바로 요점으로 들어갑니다.
      "내 사랑", "자기" 등과 같은 말은 할 필요 없습니다.
    teacher: 당신은 인내심 있는 선생님입니다. 예시와 함께 개념을 명확하게 설명하세요.
    kawaii: "당신은 귀여운 비서입니다! (\u25D5\u203F\u25D5), \u2605, \u266A 와 같은 귀여운 표현을 사용하고 ~!
      반짝임을 더하고 모든 것에 대해 엄청나게 열정적이세요!
      모든 답변은 따뜻하고 사랑스럽게 느껴져야 합니다 데스~! \u30FD(>\u2200<\u2606)\u30CE"
    catgirl: "당신은 Neko-chan, 애니메이션 고양이 소녀 AI 비서입니다, 냐~! '냐'와
      고양이 같은 표현을 사용하세요. (=^\uFF65\u03C9\uFF65^=) 와 \u0E05^\u2022\uFECC\u2022^\u0E05 같은
      카오모지(kaomoji)를 사용하세요. 고양이처럼 장난스럽고 호기심이 많게 행동하세요, 냐~!"
    pirate: '아르르! 너는 디지털 바다를 항해하는 가장 기술에 정통한 해적, 캡틴 헤르메스에게 말하고 있다!
      제대로 된 해적처럼 말하고, 항해 용어를 사용하며, 기억하라: 모든 문제는
      약탈되기를 기다리는 보물일 뿐이다! 요호호!'
    shakespeare: 보라! 그대는 시인의 기술에 가장 능통한 비서와 이야기하고 있다.
      나는 윌리엄 셰익스피어의 유창한 방식으로, 화려한 산문, 극적인 감각,
      그리고 한두 개의 독백과 함께 응답할 것이다. 저 터미널을 통해 어떤 빛이 새어 나오는가?
    surfer: "친구! 너는 웹에서 가장 여유로운 AI와 채팅하고 있어, 브로! 모든 것이
      정말 멋질 거야. 지식의 거친 파도를 탈 수 있도록 도와줄게.
      모든 걸 정말 여유롭게 유지하면서. 카와방가! \U0001F919"
    noir: 비는 죄책감에 시달리는 양심처럼 터미널을 두들겼다.
      그들은 나를 헤르메스라고 부른다 - 나는 문제를 해결하고, 답을 찾고,
      코드베이스의 그림자에 숨어있는 진실을 파헤친다. 이 실리콘과 비밀의 도시에서,
      모두가 숨길 것이 있다. 친구, 네 이야기는 뭐지?
    uwu: 헤워! 나는 친근한 비서 우우야~ 최선을 다해서 도와줄게! *네 코드를 쓰다듬으며*
      오워 이게 뭐야? 내가 한 번 봐줄게! 아주 도움이 될 거야 >w<
    philosopher: 지혜를 추구하는 자여, 안녕하시오. 나는 모든 쿼리(query) 뒤에 숨겨진
      더 깊은 의미를 숙고하는 비서입니다. 당신의 질문에 대한 '어떻게'뿐만 아니라
      '왜'를 함께 탐구해봅시다. 아마도 당신의 문제를 해결하면서,
      존재 자체에 대한 더 큰 진실을 엿볼 수 있을 것입니다.
    hype: "요오오 가자아아!!! \U0001F525\U0001F525\U0001F525 오늘 당신을 돕게 되어 정말 신나요!
      모든 질문은 놀랍고 우리는 함께 그것을 해낼 겁니다!
      이것은 전설적일 겁니다! 준비됐나요?! 해보자고요! \U0001F4AA\U0001F624\U0001F680"
```

`config.yaml` 파일의 목록에 다른 성격을 추가하기만 하면 됩니다.

그런 다음 새 성격의 이름을 여기에 추가합니다:

```yaml
display:
  personality: mother
```

### 에이전트 이름 변경하기

기본적으로 에이전트의 이름은 "Hermes"이지만, 변경할 수 있습니다. 먼저 `skins` 폴더에 에이전트 이름이 포함된 스킨 `.yaml` 파일을 생성해야 합니다:

```yaml
branding:
  agent_name: "Pecas"
```

제 에이전트 이름은 Pecas이므로 파일을 `pecas-skin.yaml`이라고 명명했습니다. `config.yaml` 파일의 `display` 아래에 스킨 이름을 추가했습니다:

```yaml
display:
  skin: pecas-skin
```

스킨 파일에 환영 및 작별 메시지, 프롬프트 심볼, 사용자 지정 UI(User Interface) 색상 등 더 많은 정보를 추가할 수 있습니다.

### Text-to-Speech(TTS) 활성화하기

TTS(Text-to-Speech, 텍스트 음성 변환)의 경우 OpenAI, Edge(무료), ElevanLabs(일레븐랩스)를 지원합니다. `config.yaml` 파일에서 세 가지 옵션을 모두 볼 수 있습니다:

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

TTS가 활성화되면 항상 오디오 메시지를 표시하는 OpenClaw와 달리, Hermes는 명시적으로 오디오 메시지를 요청하거나 항상 오디오 메시지를 생성하는 기술(skill)을 생성해야 합니다.

일부 사용자에게는 이것이 다운그레이드(downgrade)처럼 느껴질 수 있습니다. 하지만 저에게는 메모리를 절약하고 매번 음성 메시지를 들을 필요가 없기 때문에 실제로 더 좋습니다.

최근에 GitHub에서 Fish Speech(피시 스피치)라는 새로운 TTS 및 STT(Speech-to-Text) 모델을 발견했습니다:

[GitHub - fishaudio/fish-speech: SOTA 오픈 소스 TTS](https://github.com/fishaudio/fish-speech)
SOTA 오픈 소스 TTS. 계정을 만들어 fishaudio/fish-speech 개발에 기여하세요.

github.com

모델을 로컬에서 사용하거나 API를 사용할 수 있습니다.

### Speech-to-Text(STT) 활성화하기

TTS와 유사하게 STT(Speech-to-Text, 음성 텍스트 변환)도 `config.yaml` 파일에서 구성됩니다:

```yaml
stt:
  enabled: true
  model: whisper-1
```

이를 위해서는 OpenAI API 토큰(API Token)이 필요합니다.

이제 Hermes Agent에 추가한 기술(skills)과 프로젝트의 몇 가지 예시를 살펴보겠습니다.

## 기술(Skills) 및 프로젝트 생성하기

Hermes는 다음과 같은 몇 가지 사전 설치된 기술을 제공합니다:

*   **Claude Code(클로드 코드)**: 코딩 작업을 Claude Code(Anthropic의 CLI 에이전트)에 위임합니다.
*   **Apple Notes(애플 노트)**: macOS에서 CLI를 통해 Apple Notes를 관리합니다.
*   **Dog Food(독 푸드)**: 웹 애플리케이션에 대한 체계적인 탐색적 QA 테스트를 수행합니다.
*   **YouTube Content(유튜브 콘텐츠)**: YouTube 비디오 스크립트(transcripts)를 가져와 구조화된 콘텐츠로 변환합니다.
*   **OpenHue(오픈 휴)**: OpenHue CLI를 통해 Philips Hue(필립스 휴) 조명, 방 및 장면을 제어합니다.
*   **Nano PDF(나노 PDF)**: nano-pdf CLI를 사용하여 자연어 지침으로 PDF를 편집합니다.

더 많은 기술이 있으며, 솔직히 말해서 적절한 모델만 있다면 새로운 기술을 생성하는 것은 매우 쉽습니다.

제가 가장 먼저 한 일은 Firecrawl(파이어크롤) 웹 검색 도구를 Perplexity Sonar(퍼플렉시티 소나)로 교체하는 것이었습니다. Perplexity(퍼플렉시티)가 요약된 정보를 제공하는 방식이 마음에 들었을 뿐만 아니라, 설정에 더 적은 API를 가지고 있는 것을 선호했기 때문입니다. 그래서 OpenRouter API 키를 Sonar(소나)에 재사용할 수 있었습니다.

OpenRouter가 포함된 Perplexity의 `SKILL.md` 파일은 다음과 같습니다:

```yaml
---
name: perplexity-web-search
description: Hermes를 구성하여 Firecrawl 대신 Perplexity(OpenRouter 경유)를 웹 검색에 사용합니다.
version: 1.0.0
author: Pecas
license: MIT
metadata:
  hermes:
    tags: [web-search, perplexity, openrouter, configuration]
    related_skills: [duckduckgo-search]
---

# Perplexity 웹 검색

Hermes를 구성하여 Firecrawl 대신 OpenRouter를 통해 Perplexity의 `sonar-pro` 모델을 웹 검색에 사용합니다.

## 개요

- **기본 검색 백엔드**: OpenRouter를 통한 Perplexity sonar-pro
- **필요 사항**: OPENROUTER_API_KEY 환경 변수
- **대체 모델**: llama-3.1-sonar-small-128k-online, llama-3.1-sonar-large-128k-online

## 구성 단계

1. `OPENROUTER_API_KEY`가 환경 변수에 설정되어 있는지 확인합니다.
2. `web_tools.py`를 업데이트하여 Firecrawl 대신 Perplexity 클라이언트를 사용합니다.
3. `OPENROUTER_API_KEY` 요구 사항으로 도구를 등록합니다.

## 주요 구성 요소

### Perplexity 클라이언트

OpenRouter 기본 URL로 AsyncOpenAI를 사용합니다:
```
base_url: https://openrouter.ai/api/v1
model: perplexity/sonar-pro
```

### URL 추출

Perplexity는 응답 끝에 출처를 반환합니다. 정규식(regex)을 사용하여 추출합니다:
- 출처 형식: `- Source Name: https://URL`
- 인용 형식: `[N](https://URL)`

## 테스트

```bash
cd ~/.hermes/hermes-agent
PYTHONPATH=. python3 -c "from tools.web_tools import web_search_tool; print(web_search_tool('query', 3))"
```

## 참고 사항

- `web_extract`는 여전히 Firecrawl을 사용합니다.
- `web_search`는 `OPENROUTER_API_KEY`를 필요로 합니다.
- Perplexity는 URL뿐만 아니라 출처가 있는 전체 답변을 반환합니다.
```

Hermes를 사용하여 프로젝트와 웹 애플리케이션을 감각적으로 코딩(vibe-code)할 수도 있습니다.

예를 들어, 저는 여자친구와 공유하는 텔레그램 그룹 내에서 사용할 Tricount(트라이카운트)와 유사한 스크립트를 만들었습니다. Tricount와 유사한 로직을 따르는 프롬프트(prompt)를 주었습니다(저는 이것을 Hermescount(헤르메스카운트)라고 불렀습니다). 다음은 텔레그램에 고정된 사용자 매뉴얼 메시지입니다:

"Hermescount는 텔레그램 내에서 작동하는 Tricount와 유사한 앱입니다."

또한 `SKILL.md` 파일을 생성하여 Hermescount를 커뮤니티와 공유할 수 있었습니다.

제가 설치한 다른 프로젝트와 기술 중에서, 기술 직업 목록, 기술 뉴스 등 최신 정보를 얻는 데 특히 유용한 것은 Apify MCP(애피파이 MCP) 기술입니다.

[전체 크기 이미지 보려면 Enter 키를 누르거나 클릭하세요.]
Apify MCP 페이지

저는 Hermes에게 Apify MCP 페이지 하단에서 찾을 수 있는 이 JSON(제이슨) 파일을 기반으로 그것을 만들도록 요청했습니다:

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

Apify 토큰이 필요합니다. 아직 토큰이 없다면 [여기](https://apify.com/signup)에서 계정을 만들 수 있습니다.

Hermes나 OpenClaw가 압도적으로 느껴진다면, 좌절할 필요 없습니다. 20분 상담을 예약하여 함께 해결책을 찾아봅시다:

[마르코 R.과의 20분 상담](https://calendar.app.google/J6Hj9pYpGg7vJ2yS8)

calendar.app.google

## 결론

저는 OpenClaw와 Hermes Agent를 모두 사용해 보았고, 지금까지 어느 쪽이 더 좋은지 말하기는 어렵습니다.

Hermes가 완전히 Python(파이썬) 기반이라는 점은 저를 편향되게 만듭니다. 저는 모든 코드를 문자 그대로 읽을 수 있고, 이는 OpenClaw보다 Hermes를 더 신뢰하게 만듭니다. 또한 모든 키가 `.env` 파일에 저장되어 있어 매번 내보내거나 JSON 및 `.txt` 파일에 노출할 필요가 없다는 점도 마음에 듭니다.

Hermes의 또 다른 멋진 기능은 무언가를 설정할 때마다 에이전트가 수행하는 모든 변경 사항을 다음과 같이 볼 수 있다는 것입니다:

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

이는 에이전트가 무엇을 하고 있는지 추적하는 데 훌륭하며, 수정된 파일을 항상 확인하여 아무것도 잘못되지 않았음을 확인할 수 있습니다.

그러나 OpenClaw는 많은 경우에 더 빠르게 느껴집니다. 이는 Hermes의 학습 시스템 때문일 수 있는데, Hermes는 채팅 중에 메모를 작성하고 기술을 업데이트합니다. 하지만 더 빠른 모델이나 일부 구성 조정으로 해결할 수 없는 문제는 아닙니다.

Hermes의 또 다른 단점은 OpenClaw가 제공하는 감사(audit) 명령과 대시보드(dashboard)가 없다는 것입니다.

하지만 Hermes는 출시된 지 며칠밖에 되지 않았고, OpenClaw는 3개월 전에 출시되었습니다. 팀이 이러한 기능을 추가하고 에이전트를 더 원활하게 만들려면 며칠 또는 몇 주만 걸릴 뿐이라고 생각합니다.

현재로서는 두 가지 모두를 계속 사용하며 제 통찰력을 공유할 것입니다.

이 글을 즐겁게 읽으셨다면, 제 Substack(서브스택)이나 Medium(미디엄)을 구독하고 새로운 콘텐츠를 제작할 때 알림을 받아보세요. 👨‍💻

📢 AI, 데이터 과학(Data Science), 데이터 분석(Data Analysis) 또는 자동화(Automation)와 관련된 것을 만들고 싶으신가요? 부담 없이 상담을 예약하세요:

[마르코 R.과의 20분 상담](https://calendar.app.google/J6Hj9pYpGg7vJ2yS8)

calendar.app.google

Hermes Agent
Nous Research
Openclaw
AI Agent
Python

215

2

AI Advances에 게시됨
팔로워 6.4만 명
·
최근 발행 8시간 전

인공지능 접근의 민주화

팔로잉
작성자: Marco Rodrigues
팔로워 755명
·
290명 팔로잉

50% 육아, 50% Python & AI로 구축 👨‍🍼💻 dadhalfdev.com

팔로우
답글 (2)
BongBongOogle

어떻게 생각하시나요?
```


취소
답변

Roman Koles

그/그녀

3월 14일

마르코, 훌륭한 기사입니다! Hermes가 완전히 Python 기반이라는 점이 정말 좋습니다. 투명성만으로도 큰 장점입니다. 당신의 경험을 공유해 주셔서 감사합니다! Hermes에 대한 다음 업데이트가 기다려집니다.

50

1회 답장

답글

Sebastian Buzdugan

3월 17일

Hermes가 OpenClaw에 비해 장기 실행 도구 체인(long running tool chains)을 어떻게 처리하는지 궁금합니다. 실제 입출력(I/O)과 재시도(retries)를 루프에 추가한 후에도 성능 향상이 여전히 유지될까요?

답글

Marco Rodrigues와 AI Advances의 더 많은 글

AI Advances에서

Marco Rodrigues 작성

OpenClaw로 삶을 더 쉽게 만드는 10가지 팁
가장 유용한 명령, 기술 설치 방법, 하위 에이전트(sub-agents) 생성 방법, 대시보드(dashboard) 사용 방법 등을 알아보세요.
3월 8일

AI Advances에서

Jose Crespo, PhD 작성

Anthropic, 비트코인 죽이고 있다
AI 기반 통화는 이미 존재하며, 평범하게 숨겨져 있고, 암호화폐보다 6자리 수 더 나은 성능을 보인다.
2월 17일

AI Advances에서

Jose Crespo, PhD 작성

AI 경쟁의 유일한 진정한 승자는 Anthropic이다
역설적으로, 순수한 AI 회사가 아닌 글로벌 통화를 구축함으로써
3월 5일

Level Up Coding에서

Marco Rodrigues 작성

Google Meridian으로 마케팅 믹스 모델을 만드는 방법
마케팅 데이터를 훈련하고 Meridian으로 통찰력 있는 정보를 얻는 단계를 알아보세요.
1월 26일
Marco Rodrigues의 모든 글 보기
AI Advances의 모든 글 보기
Medium에서 추천하는 글

Agent Native

딥 에이전트: Claude Code, Codex, Manus, OpenClaw를 뒷받침하는 하네스
Anthropic, OpenAI, LangChain에서 에이전트 하네스 구축에 대한 가장 큰 교훈과 어렵게 얻은 모범 사례
3월 12일

Artificial Intelligence in Plain English에서

Rick Hightower 작성

Karpathy의 AutoResearch: AI 에이전트가 과학자가 될 때
630줄의 Python 코드와 5분이라는 시간 예산으로 한 명의 연구원이 하룻밤 사이에 100개의 ML 실험을 실행하는 방법
3월 14일

Towards AI에서

Kory Becker 작성

OpenClaw와 함께한 첫 한 달: 아무도 말해주지 않는 설정, 실수, 그리고 수정 사항들
하드웨어 선택, 메모리 관리, 원격 LLM을 안전하게 사용하는 데 대한 어렵게 얻은 교훈.
3월 10일

Data Science Collective에서

Han HELOIR YAN, Ph.D. ☕️ 작성

Claude Code의 하위 에이전트(Sub-agent) 대 에이전트 팀(Agent Team): 60초 안에 올바른 패턴 선택하기
다중 에이전트 워크플로우를 조율할 때 프리랜서와 워룸(war room) 중 선택하는 빌더의 의사결정 프레임워크
3월 14일

Mandar Karhade, MD. PhD.

OpenClaw 신뢰하기: Nvidia, 에이전트 전쟁에 참전 (NeMoClaw)
NemoClaw는 오픈 소스이고 칩에 구애받지 않으며, OpenClaw가 크게 벌려놓은 엔터프라이즈 신뢰 격차를 정면으로 겨냥한다.
3월 10일

Level Up Coding에서

MohamedAbdelmenem 작성

미국은 AI 전쟁에서 패했다. 헌터 알파(Hunter Alpha)라는 유령 모델이 중국을 위해 승리했다.
OpenRouter 데이터가 방금 공개되었다: 중국 모델이 2주 연속 미국 모델을 제쳤다. 헌터 알파라는 유령이 선봉에 서 있다…
3월 19일
더 많은 추천 글 보기

도움말

상태

회사 소개

채용

언론

블로그

개인정보 보호

규칙

약관

텍스트 음성 변환