# 번역 기사

- **원본 URL:** https://levelup.gitconnected.com/you-dont-need-to-buy-a-mac-mini-to-run-openclaw-27bd564c0c9d
- **번역 일시:** 2026-04-20 22:27:49

---

# OpenClaw 실행을 위해 Mac mini를 구매할 필요는 없습니다

당신의 MacBook은 이미 완벽한 랍스터 서식지입니다. 여기 안전하게 사용하는 방법이 있습니다.

오픈 소스 개인 AI 비서인 OpenClaw 🦞를 발견하셨군요. 이 서비스는 GitHub에서 19만 6천 개의 스타를 받았고, '몰티(Molty)'라는 우주 랍스터 마스코트가 있으며, WhatsApp, Telegram, Slack, Discord, iMessage, Microsoft Teams에 동시에 연결하는 대담함을 가지고 있습니다. 당신은 흥분했고, 이를 24시간 내내 실행하고 싶어 합니다. 애플 웹사이트에서 번쩍이는 Mac mini를 눈여겨보며 '장바구니에 추가(Add to Cart)' 버튼에 손가락을 위험할 정도로 가까이 대고 있을 것입니다.

신용카드를 내려놓으세요. 당신의 MacBook이면 충분합니다.

하지만 더 중요한 것은 하드웨어에 대해 이야기하기 전에 보안에 대해 먼저 논의해야 한다는 것입니다. OpenClaw는 장난감이 아닙니다. 이는 Bash 도구(bash tool)를 가지고 있고, 파일 시스템(file system)에 접근할 수 있으며, 당신이 사용했던 모든 메시징 플랫폼과 소통할 수 있는 AI 에이전트(AI agent)입니다. 당신의 시스템에 무제한 접근 권한을 부여하고 나중에 문제를 제기하는 것은 매우 안 좋은 결과를 초래할 수 있습니다.

보안이 먼저, 하드웨어는 그다음입니다. 시작해봅시다.

## 첫째: 당신이 실제로 무엇을 실행하는지 이해하세요

OpenClaw 자체 문서에서도 이에 대해 놀랍도록 솔직하게 설명하고 있습니다.

> 기본 설정: 주요 세션(main session)에서는 도구들이 호스트(host)에서 실행되므로, 에이전트(agent)는 당신만을 위해 작동할 때 모든 권한을 가집니다.

이 문장은 잠시 멈춰서 생각할 가치가 있습니다. 당신이 실행하려는 AI 비서는 기본적으로 당신의 사용자 전체 권한(full permissions)으로 시스템에서 임의의 Bash 명령어(bash commands)를 실행할 수 있습니다. 이는 당신의 파일을 읽고, 홈 디렉터리(home directory)에 쓰고, 네트워크 요청(network requests)을 하고, 시스템 프로세스(system processes)와 상호작용할 수 있습니다.

이는 비판이 아니라, 코드를 실행하고, 파일을 관리하며, 워크플로우(workflow)를 자동화하는 등 유용한 작업을 수행하는 데 실제로 필요합니다. 하지만 이는 설치 시에 내리는 보안 선택이 매우 중요하다는 것을 의미합니다. 좋은 소식은 macOS에 당신이 거의 사용해 본 적 없는, 내장되어 있고 검증된 샌드박싱(sandboxing) 도구가 숨겨져 있다는 것입니다.

## sandbox-exec를 소개합니다: macOS의 조용하지만 강력한 보안 가드

모든 Mac에는 `sandbox-exec`라는 명령어가 포함되어 있습니다. 이는 Tiger 시대부터 존재했습니다. 애플(Apple)은 앱 스토어(App Store) 앱을 위한 앱 샌드박스(App Sandbox)를 선호하여 몇 년 전(기술적으로는) 이를 더 이상 사용하지 않도록 권고했지만, 사실은 이렇습니다: macOS의 모든 주요 보안 의식 애플리케이션(application)은 여전히 이를 사용합니다. Claude Code, Firefox, Chrome, Homebrew(빌드 시), Swift Package Manager가 모두 이를 사용합니다. 사용 중단 권고(deprecation notice)는 사실상 "젖은 페인트" 표지판과 같아서, 페인트가 잘 작동하기 때문에 아무도 심각하게 받아들이지 않는 소프트웨어(software)의 비유입니다.

`sandbox-exec`는 애플의 "시트벨트(Seatbelt)" 정책 시스템을 구현합니다. 프로세스(process)가 무엇을 할 수 있는지 정확하게 지정하는 Scheme(Scheme-like syntax)과 유사한 구문(syntax, 실제 괄호 사용)으로 프로필(profile)을 작성합니다. 명시적으로 허용되지 않은 모든 것은 거부됩니다. 이는 매우 의심 많은 나이트클럽을 위한 매우 정밀한 게스트 패스(guest pass)를 작성하는 것과 같습니다.

가장 간단하고 유용한 프로필은 다음과 같습니다.

```scheme
(version 1)
(allow default)
(deny network*)
```

세 줄. 이것이 OpenClaw가 일반적으로 수행하는 모든 작업(파일 읽기, 코드 실행, 프로세스 관리)을 허용하지만, 어떠한 네트워크 연결(network connections)도 할 수 없게 만드는 완전한 샌드박스 정책(sandbox policy)입니다. 이는 Telegram에 연결하는 AI 비서에게는 아마 원치 않는 설정일 수 있지만, 시스템이 어떻게 작동하는지 보여줍니다.

## OpenClaw를 위한 실용적인 샌드박스 프로필

다음은 더 유용한 프로필입니다. 이를 `~/.openclaw/sandbox.sb`로 저장하세요.

```scheme
(version 1)

; 우선 관대하게 허용하고, 그 다음 구체적으로 제한
(allow default)

; API 호출 및 메시징 채널을 위한 아웃바운드 네트워크 허용
(allow network-outbound)

; 인바운드 연결 거부 - OpenClaw는 서버가 되어서는 안 됨
; (외부 클라이언트에서 Gateway WebSocket을 사용하는 경우 이 줄을 제거하세요)
(deny network-inbound
  (local tcp "*:*"))

; 파일 쓰기를 작업 공간 및 설정으로만 제한
; 이는 에이전트가 다른 곳에 기록하는 것을 방지함
(deny file-write*
  (subpath "/"))

(allow file-write*
  (subpath (string-append (param "HOME") "/.openclaw"))
  (subpath (string-append (param "HOME") "/.openclaw/workspace"))
  (subpath "/tmp"))

; 모든 곳에서의 읽기 허용 (OpenClaw는 시스템 라이브러리 읽기가 필요함)
(allow file-read*)
```

그런 다음 OpenClaw를 다음과 같이 실행합니다.

```bash
sandbox-exec -f ~/.openclaw/sandbox.sb \
  -D HOME=$HOME \
  openclaw gateway --port 18789
```

또는 샌드박스 없이 실수로 실행하는 일이 없도록 셸 별칭(shell alias)을 생성하세요.

```bash
# 당신의 ~/.zshrc 또는 ~/.bashrc 파일에 추가
alias openclaw-safe='sandbox-exec -f ~/.openclaw/sandbox.sb -D HOME=$HOME openclaw'
```

한 가지 중요한 경고가 있습니다: 프로필(profile)의 작은 오타가 나중에 명령어를 실행한 곳과는 동떨어진 곳에서 혼란스러운 런타임 오류(runtime failures)로 이어질 수 있습니다. 피드백 루프(feedback loop)가 원활하지 않습니다. 프로덕션(production) 용도로 사용하기 전에 항상 `openclaw agent -m "hello"`와 같은 간단한 명령어로 프로필을 테스트하고, 문제가 발생하면 `~/.openclaw` 로그(logs)를 확인하세요.

## OpenClaw 자체의 보안 기능 (이것도 함께 사용하세요)

`sandbox-exec`는 OS 수준의 안전망입니다. 하지만 OpenClaw 자체에도 이해할 가치가 있는 계층화된 보안 모델(layered security model)이 있습니다.

*   **기본 DM 페어링(DM pairing).** 누군가 Telegram, WhatsApp 또는 Discord에서 봇(bot)에 메시지를 보내면, 페어링 코드 챌린지(pairing code challenge)를 받게 됩니다. 당신이 승인하기 전까지는 에이전트와 대화할 수 없습니다. 이는 선택 사항이 아니며, 기본적으로 활성화되어 있습니다. 절대로 비활성화하지 마세요. 문서에 따르면 위험한 DM 정책(DM policies)을 확인하려면 `openclaw doctor`를 실행하라고 합니다. 그렇게 하세요.

*   **비주요 세션(non-main sessions)을 위한 샌드박스 모드(Sandbox mode).** 그룹 채팅(group chats) 및 채널(channels)(즉, 당신이 아닌 다른 사용자)의 경우, OpenClaw에게 호스트 머신(host machine) 대신 Docker 컨테이너(Docker containers) 내에서 Bash를 실행하도록 지시할 수 있습니다.

    ```json
    {
      "agents": {
        "defaults": {
          "sandbox": {
            "mode": "non-main"
          }
        }
      }
    }
    ```

    이는 훌륭합니다. Discord 봇에 메시지를 보내는 무작위 사용자는 컨테이너화된 Bash 환경(containerized bash environment)을 얻게 됩니다. 반면, 직접 메시지를 보내는 당신은 모든 권한을 가집니다. 완벽한 분리입니다.

*   **어디에나 허용 목록(Allowlists).** 모든 채널에는 `allowFrom` 설정(configuration)이 있습니다. 프로덕션(production) 환경에서는 이를 와일드카드(wildcards)로 두지 마세요. 특정 사용자(users) 및 그룹(groups)으로 제한해야 합니다.

*   **상위 권한 모드(Elevated mode)는 선택 사항(opt-in)입니다.** 세션별 전체 호스트 권한(full host permissions per-session)을 사용하려면 채팅에서 명시적으로 `/elevated`를 켜야 합니다. 이는 기본적으로 활성화되어 있지 않습니다. 특별히 필요한 경우가 아니라면 비활성화 상태로 두세요.

## MacBook을 서버로 사용하는 설정

이제 Mac mini에 대한 이야기입니다. 사람들이 묻는 질문은 "전용 하드웨어(dedicated hardware)를 구매하지 않고도 OpenClaw를 24시간 내내 영구적으로 항상 켜져 있는 비서로 실행할 수 있나요?"입니다.

네. 방법은 다음과 같습니다.

*   **옵션 1: MacBook 덮개를 열어두기 (또는 `caffeinate` 사용).** 이것은 무료 옵션입니다. macOS의 내장 `caffeinate` 명령어 또는 Amphetamine과 같은 도구를 사용하여 절전 모드(sleep)를 방지하세요.

    ```bash
    # OpenClaw를 실행하는 동안 시스템을 무기한으로 활성 상태로 유지
    caffeinate -i sandbox-exec -f ~/.openclaw/sandbox.sb -D HOME=$HOME \
      openclaw gateway --port 18789 --install-daemon
    ```

    `--install-daemon` 플래그(flag)는 OpenClaw를 `launchd` 사용자 서비스(user service)로 설치하여 로그인 시 자동으로 시작되도록 합니다. 이는 권장되는 접근 방식이며, 당신이 계속 주시할 필요가 없다는 것을 의미합니다.

*   **옵션 2: 원격 접속(remote access)을 위해 Tailscale 사용.** OpenClaw는 내장된 Tailscale 지원 기능을 제공합니다. 설정(config)에서 `gateway.tailscale.mode: "serve"`로 설정하면, 게이트웨이 대시보드(Gateway dashboard)를 당신의 테일넷(tailnet) 내 어디에서든 안전하게 접근할 수 있으며, 어떠한 것도 공용 인터넷(public internet)에 노출되지 않습니다. 이는 당신의 MacBook이 아파트 구석에 덮개 닫힌 채 놓여 있어도, 당신의 휴대폰이나 다른 기기를 통해 접근할 수 있다는 의미입니다.

*   **옵션 3: 작은 리눅스 장치(Linux box)에 원격 게이트웨이(Remote gateway) 설치.** 항상 켜져 있는 상태를 원하고 MacBook을 계속 사용해야 한다면, OpenClaw는 게이트웨이(gateway)와 장치 노드(device node)를 분리하는 것을 지원합니다. 저렴한 리눅스 장치에서 게이트웨이를 실행하고, MacBook(또는 iPhone, Android)을 음성, 카메라, 화면 캡처와 같은 장치 로컬(device-local) 작업용 노드(node)로 페어링(pair)하세요. 게이트웨이 자체는 최소한의 하드웨어(hardware)에서도 잘 작동합니다.

이는 우리를 다음으로 이끕니다…

## 초절약형 옵션: 10달러 하드웨어의 PicoClaw

최대의 비용 효율성(cost efficiency)과 최소의 복잡성(complexity)을 원한다면, 1년 전에는 없었던 세 번째 옵션인 PicoClaw가 있습니다.

PicoClaw는 Sipeed 팀(RISC-V 하드웨어 개발사)이 Go 언어로 OpenClaw 개념을 재구현한 것입니다. 주요 수치는 거의 터무니없을 정도입니다: 10MB 미만의 RAM, 1초 미만의 부팅 시간, 10달러짜리 RISC-V 보드에서 실행됩니다. Telegram, Discord, QQ, DingTalk 및 OpenClaw와 동일한 API 제공업체(Anthropic, OpenAI, OpenRouter, Gemini, DeepSeek)를 지원합니다. 이는 단일 자가 포함(self-contained) 바이너리(binary)입니다.

README에 있는 비교 표가 모든 것을 말해줍니다.

트레이드오프(tradeoff): PicoClaw는 훨씬 더 단순합니다. macOS 컴패니언 앱(companion app), 음성 활성화(voice wake), 라이브 캔버스(live Canvas), BlueBubbles/iMessage 통합(integration) 또는 OpenClaw가 제공하는 완전한 다중 에이전트 라우팅(multi-agent routing) 기능이 없습니다. 이는 "웹을 검색하고 코드를 실행할 수 있는 Telegram용 AI만 있으면 돼" 버전이며, 솔직히 대부분의 사람들이 실제로 필요로 하는 전부입니다.

PicoClaw의 보안 모델(security model)의 경우, 리눅스(Linux) 계층에서 작업하게 됩니다. 전용 장치(dedicated device)에서 실행되므로, 공격 표면(attack surface)이 이미 훨씬 작습니다 — 주력 시스템(main machine)이 아닙니다. 표준적인 모범 사례(best practices)가 적용됩니다: 비루트 사용자(non-root user)로 실행하고, 방화벽(firewall)을 사용하여 인바운드 연결(inbound connections)을 제한하며, 설정 파일(config)의 `allowFrom` 목록을 엄격하게 유지하고, 적절한 리소스 제한(resource limits)을 가진 Docker 컨테이너(Docker container) 또는 systemd 서비스(systemd service) 내에서 실행하는 것을 고려하세요.

## 보안 사고방식 요약

터미널 접근 권한(terminal access)과 메시징 통합(messaging integrations)을 가진 AI 에이전트를 실행하는 것은 합법적으로 강력하면서도 합법적으로 위험합니다. 다음은 유지해야 할 사고방식(mental model)입니다.

에이전트는 건물 출입 권한을 가진 신뢰할 수 있는 직원입니다. 당신은 그들이 업무를 수행할 수 있기를 원하지만, 동시에 잠긴 파일 캐비닛, 키 카드 기록, 보안 카메라도 원할 것입니다. 첫날부터 신입 직원에게 마스터 키를 주지는 않을 것입니다. 대신 필요한 것에 대한 접근 권한을 부여하고, 신뢰가 쌓임에 따라 이를 확장할 것입니다.

MacBook에서 OpenClaw를 사용하는 경우, 이는 다음과 같습니다: OS 수준에서 `sandbox-exec` 사용, 채널 세션(channel sessions)에는 `sandbox.mode: "non-main"` 설정, 모든 채널에 엄격한 허용 목록(allowlists), 원격 접속(remote access)을 위한 Tailscale serve (funnel 아님), 그리고 데몬(daemon)을 루트(root)가 아닌 사용자 계정(user account)으로 설치하는 것입니다. 설치 후 `openclaw doctor`를 실행하고 그 출력을 반드시 확인하세요.

저렴한 하드웨어의 PicoClaw의 경우, 격리(isolation)는 물리적입니다. 장치 자체가 샌드박스입니다. 명시적으로 설정한 채널을 통해서만 인터넷에 접근할 수 있도록 하세요.

## 빠른 시작 체크리스트

### MacBook에서 OpenClaw 사용 시:

*   `npm install -g openclaw@latest`
*   `openclaw onboard --install-daemon`
*   위 프로필로 `~/.openclaw/sandbox.sb` 파일 생성
*   `~/.openclaw/openclaw.json`에 `agents.defaults.sandbox.mode: "non-main"` 추가
*   활성화하는 모든 채널에 엄격한 `allowFrom` 목록 설정
*   원격 접속이 필요한 경우 `gateway.tailscale.mode: "serve"` 설정
*   `openclaw doctor`를 실행하고 플래그(flag)가 표시하는 모든 문제를 해결
*   별칭(`alias`)을 통해 실행: `openclaw-safe gateway`

### 10달러 하드웨어에서 PicoClaw 사용 시:

*   LicheeRV-Nano (또는 모든 리눅스 SBC)에 리눅스(Linux) 플래시
*   릴리스(releases)에서 당신의 아키텍처(architecture)에 맞는 PicoClaw 바이너리(binary) 다운로드
*   `picoclaw onboard`
*   당신의 API 키(API keys)와 엄격한 `allowFrom` 목록으로 `~/.picoclaw/config.json` 설정
*   `picoclaw gateway` 실행 (또는 systemd 서비스로 설정)

## 결론

Mac mini는 훌륭한 하드웨어입니다. 하지만 599달러가 들며, 사람들이 OpenClaw를 위해 Mac mini가 필요하다고 생각하는 주된 이유는 "서버(server)"는 "전용 하드웨어"를 의미한다는 습관적인 가정 때문입니다. 당신의 MacBook M-시리즈 칩은 10년 전 서버들이 사용하던 것과 비교하면 슈퍼컴퓨터(supercomputer)입니다. 특히 백그라운드에서 데몬 모드(daemon mode)로 실행하면, 개인 AI 에이전트를 완벽하게 호스팅(hosting)할 수 있습니다.

599달러를 아끼세요. `sandbox-exec`를 설정하는 데 20분을 투자하세요. `openclaw doctor`를 실행하세요. 그리고 정말로 큰돈을 들이지 않고 '서비스형 하드웨어(hardware-as-a-service)' 경험을 원한다면, PicoClaw를 실행하는 10달러짜리 RISC-V 보드는 진정으로 인상적입니다: "皮皮虾，我们走！(띠피시아, 워먼쩌우!)"라고 말하는 것보다 더 빠르게 부팅되는 랍스터급 AI 비서입니다 🦞🦐.

---

OpenClaw: github.com/openclaw/openclaw
PicoClaw: github.com/sipeed/picoclaw