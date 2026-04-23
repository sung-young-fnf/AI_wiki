# 번역 기사

- **원본 URL:** https://pub.towardsai.net/nvidia-nemoclaw-openshell-fastest-way-to-install-bbfb82b08ea7
- **번역 일시:** 2026-03-29 18:06:28

---

# NVIDIA NemoClaw + OpenShell: 가장 빠른 설치 방법

## Towards AI

엔터프라이즈 AI를 구축하고, 배운 것을 가르칩니다. Towards AI Academy에서 10만 명 이상의 AI 실무자들과 함께하세요. 무료: 6일 에이전트 AI 엔지니어링 이메일 가이드: [https://email-course.towardsai.net/](https://email-course.towardsai.net/)

---

지난 2주 동안, 우리는 자체 머신에서 실행되는 AI 에이전트(AI agent)인 OpenClaw의 인기가 급격히 반전되는 것을 목격했습니다.

Telegram (텔레그램) 및 WhatsApp (왓츠앱)과 같은 일상적인 채팅 앱을 통해 명령을 내릴 수 있으며, 이메일, 일정, 파일 등을 처리할 수 있습니다. 단순히 "질문에 답하는" 것을 넘어 실제로 행동하는 AI의 대표적인 사례로 빠르게 인기를 얻었지만, 보안 문제가 지적되어 왔습니다.

무엇이 우려되는가? OpenClaw는 "단일 신뢰 운영자(one trusted operator)" 모델을 기반으로 합니다. 즉, "단일 신뢰 사용자가 자신의 비서를 제어한다"는 의미입니다. 이는 다중 사용자가 있는 공유 환경이나 적대적 입력(adversarial input)이 존재하는 경우 강력한 보호를 제공하도록 설계되지 않았습니다. 이는 OpenClaw의 문제라기보다는, 자율 에이전트(autonomous agents)가 본질적으로 강력한 권한을 가지고 있기 때문입니다.

이는 개인 사용에는 용인될 수 있지만, 비즈니스 애플리케이션(business applications)에서는 갑자기 심각한 문제가 됩니다. OpenClaw의 약점은 성능 부족이 아니라 약한 신뢰 경계(trust boundary)에 있습니다.

그러나 하드웨어부터 소프트웨어까지 AI의 모든 측면을 제어하고자 하는 엔비디아(NVIDIA)는 이러한 장애물을 간과하지 않을 것입니다. 엔비디아는 이 문제를 해결하기 위해 공식적으로 "NemoClaw"를 발표했습니다.

제가 이해하기로는, NemoClaw는 기본적으로 OpenClaw를 그대로 사용하지만, OpenClaw를 감싸서 더 안전하게 만드는 OpenShell 소프트웨어에 의존합니다.

OpenClaw는 OpenShell 샌드박스 환경(sandbox environment) 내에서 실행되며, 모든 에이전트의 네트워크 통신, 파일 접근 및 추론 호출(inference calls)이 정책(policies)을 통해 제어됩니다. NemoClaw는 이 시스템의 구성 및 설정을 담당하는 것으로 보입니다.

따라서 OpenShell이 NemoClaw를 안전하게 만드는 핵심 구성 요소라고 생각합니다.

## NemoClaw란 무엇인가?

NemoClaw는 새로운 AI 에이전트(AI Agent)가 아니라, OpenClaw의 기업용 보안 배포판(enterprise-grade security distribution)입니다.

비유하자면, OpenClaw가 리눅스 커널(Linux kernel)이라면, NemoClaw는 레드햇 엔터프라이즈 리눅스(Red Hat Enterprise Linux)입니다. 커널은 동일하지만, 엔터프라이즈 수준의 보안, 감사 및 제어 기능으로 감싸져 있습니다.

구체적으로 NemoClaw는 세 가지 핵심 구성 요소로 이루어져 있습니다.

### 1. OpenShell — 보안 샌드박스 런타임 (Secure Sandbox Runtime)

이것이 NemoClaw의 핵심입니다. OpenShell이 하는 일은 간단합니다. OpenClaw 에이전트를 격리된 샌드박스(isolated sandbox)에 넣고 실행하는 것입니다.

*   에이전트는 허용된 파일에만 접근할 수 있으며, 사용자의 사진이나 채팅 기록을 엿볼 수 없습니다.
*   정책 엔진(policy engine)이 모든 네트워크 접근을 검토하며, 승인되지 않은 연결은 차단됩니다.
*   민감한 자격 증명(sensitive credentials) (API 키, 비밀번호)은 샌드박스 파일 시스템(sandboxed file system)에 저장되지 않고, 런타임(runtime)에 환경 변수(environment variables)로 주입됩니다.
*   개발자는 YAML (야믈) 문법을 사용하여 보안 정책(security policies)을 정의하며, 규칙은 재시작 없이 핫 업데이트(hot updates)를 지원합니다.

### NVIDIA NemoClaw 작동 방식:

NemoClaw의 아키텍처(architecture)는 여러 계층의 협력을 통해 안전한 에이전트 실행을 달성합니다. 여기서는 NemoClaw의 구성 모듈과 작동 원리를 정리하겠습니다.

NemoClaw는 아래 표에 표시된 바와 같이 네 가지 주요 구성 요소로 구성됩니다. (표는 제공되지 않았습니다.)

NemoClaw는 본질적으로 작고 가벼운 타입스크립트(TypeScript) 플러그인(plugin)입니다. 이 플러그인은 특정 `openclaw nemoclaw` 명령을 수신하는 데만 전념하며, 어떠한 무거운 처리도 수행하지 않습니다.

그렇다면 실제 작업을 수행하는 것은 무엇일까요? 바로 파이썬(Python) 기반의 설계 문서인 블루프린트(blueprint)입니다. 이 블루프린트가 샌드박스를 생성하고, 규칙을 적용하며, 이를 AI 모델에 연결하는 역할을 담당합니다.

특히 중요한 것은 OpenShell의 "프로세스 외 정책 강제(out-of-process policy enforcement)" 설계 철학입니다. 과거처럼 프롬프트(prompts)를 통해 에이전트 내부에 규칙을 내장하는 대신, 정책은 에이전트 외부에서 강제됩니다. 이는 에이전트가 손상되더라도 정책을 우회할 수 없다는 것을 의미합니다.

이는 권한이 각 세션(session)별로 검증되는 브라우저의 탭 격리 모델(tab isolation model)과 유사합니다.

나아가 프라이버시 라우터(privacy router)는 추론 요청(inference requests) 라우팅을 담당합니다. 매우 민감한 데이터는 로컬 Nemotron (네모트론) 모델에 의해 처리되고, 필요에 따라 클라우드(cloud)의 프론티어 모델(Frontier models)로 쿼리(queries)가 전송되는 하이브리드 접근 방식(hybrid approach)이 사용됩니다.

## OpenClaw 대 NemoClaw

테스트를 진행한 후, 두 시스템의 사용 방식에서 차이점이 명확해졌습니다.

*   **표준 OpenClaw** — 이는 개인 사용에 좋은 선택으로 보입니다. OpenClaw의 가장 큰 매력은 무엇이든 할 수 있는 자유이며, NemoClaw의 제한은 이를 심각하게 제약합니다. 특정 기능 사용 불가 또는 자유로운 검색 제한은 사용을 번거롭게 만듭니다.

*   **NemoClaw** — 기밀 정보가 필요한 작업과 같이 민감한 작업을 에이전트에게 맡기려면 NemoClaw 환경이 적합합니다. 기업에서 OpenClaw를 구현하는 경우 사실상 필수적이 될 것입니다.
    *   정책을 통해 민감한 코드(sensitive code)가 외부 API (Application Programming Interface)로 유출될 위험을 제어할 수 있습니다.
    *   애플리케이션 기반 네트워크 제어는 내부 통제 요구 사항을 충족할 가능성이 더 높습니다.
    *   정책이 YAML (야믈)로 관리되므로, 깃옵스(GitOps)와 유사한 작업이 가능합니다 (정책 변경은 풀 리퀘스트(pull requests)를 통해 검토됩니다).

"에이전트가 허가 없이 외부로 데이터를 전송하지 않는다는 것을 증명할 수 있습니까?"라는 질문에 정책 파일을 보여주며 "기본적으로는 거부(deny by default)이므로, 이 허용 목록(permission list)에 있는 대상에게만 통신이 허용됩니다"라고 답할 수 있다는 것은 엄청난 이점입니다.

선택 기준 요약:

*   기밀 데이터가 없는 개인 사용의 경우, 일반 OpenClaw로 충분합니다.
*   기밀 데이터 또는 비즈니스 사용 → NemoClaw 고려
*   기업 도입 → NemoClaw는 사실상 필수적입니다.

실제로는 두 개의 OpenClaw 인스턴스(instance)를 별도로 사용하는 것이 가장 좋은 접근 방식입니다. 하나는 일상적인 용도로, 다른 하나는 NemoClaw를 사용하여 기밀 작업을 처리하는 용도입니다.

## 시작하기 전에! 🦸🏻‍♀️

이 주제가 마음에 드시고 저를 응원하고 싶으시다면:

*   제 글에 50번 박수를 쳐주세요. 정말 큰 도움이 될 것입니다.👏
*   Medium (미디엄)에서 저를 팔로우하고 구독하여 최신 글을 무료로 받아보세요🫶
*   가족이 되어주세요 — 유튜브 채널을 구독하세요.

## NVIDIA NemoClaw 사용 방법

여기서는 NemoClaw를 실제로 설정하고 AI 에이전트를 실행하는 단계를 설명합니다. NemoClaw는 클라우드 추론(cloud inference)을 사용하는 프로파일(profiles)과 로컬 추론(local inference)을 사용하는 프로파일을 가지고 있으므로, 각 방법을 소개하겠습니다.

설치를 시작하기 전에, 시스템이 올바른 환경을 갖추고 있는지 확인해야 합니다. 여러분의 머신에 적합한 환경이 갖춰져 있는지 확인하세요.

*   Ubuntu (우분투) 22.04 LTS 또는 최신 버전
*   Node.js (노드제이에스) 20+
*   npm (엔피엠) 10+
*   Docker (도커) 설치됨

만약 이미 Docker가 설치되어 있다면, 이 부분은 건너뛸 수 있습니다.

하지만 아직 Docker가 없다면 걱정하지 마세요. 가장 쉬운 방법은 Docker Desktop (도커 데스크톱)을 설치하는 것입니다. 공식 웹사이트로 이동하여 Mac (맥)용 Docker Desktop을 다운로드하세요. Mac에 맞는 올바른 버전을 선택해야 합니다.

이제 Docker가 설치되었으니, 다음 단계는 NemoClaw를 설치하는 것입니다.

터미널(terminal)을 열고 다음 명령어를 실행하세요:

```bash
curl -fsSL https://nvidia.com/nemoclaw.sh | bash
```

이 명령어는 NemoClaw의 공식 설치 스크립트(install script)를 다운로드합니다. 설치에는 몇 분이 소요될 수 있으니, 완료될 때까지 기다려 주세요.

```bash
nemoclaw onboard
```

설치가 완료되면 온보딩 위자드(onboarding wizard)를 실행합니다.

이 위자드는 게이트웨이 시작, 샌드박스 생성, 정책 적용, 그리고 추론 제공자(inference provider) 구성을 한 번에 처리합니다. 추론 제공자로 NVIDIA Cloud API (엔비디아 클라우드 API) (API 키 필요) 또는 Ollama (올라마) (로컬 추론) 중에서 선택할 수 있습니다.

`nemoclaw onboard`를 처음 실행했을 때, 연달아 두 가지 문제(blockers)에 직면했습니다. 첫 번째 오류는 Docker가 실행되고 있지 않다는 것이었습니다. 그래서 macOS (맥OS)에서 다음 명령어로 Docker를 수동으로 실행했습니다:

```bash
open -a Docker
```

그런 다음 Docker Desktop이 완전히 시작될 때까지 몇 초 기다렸고, 다음 명령어로 실행 중임을 확인했습니다:

```bash
docker info
```

그 후 온보드 명령어를 다시 실행했지만, 이번에는 새로운 오류가 발생했습니다. 포트(Port) 18789가 이미 Node (노드) 프로세스(process)에 의해 사용 중이었습니다. 그래서 간단한 해결책으로 프로세스 ID (PID)를 사용하여 해당 프로세스를 강제 종료했습니다:

```bash
sudo kill 46524
```

온보드를 다시 실행했지만… 동일한 오류가 발생했고, PID는 달랐습니다. 그때 무언가가 자동으로 프로세스를 다시 시작하고 있다는 것을 깨달았습니다.

그래서 PID로 종료하는 대신, 이름으로 종료했습니다:

```bash
pkill -f openclaw-gateway
```

하지만 프로세스는 계속 다시 시작되었습니다. 이 시점에서 이것이 무작위 프로세스가 아니라 macOS가 자동으로 재시작하는 관리형 서비스(managed service)라는 것이 명확해졌습니다. 이를 확인하기 위해 다음 명령어를 사용하여 macOS 서비스 관리자(service manager) 내부를 살펴보았습니다:

```bash
launchctl list | grep -i openclaw
```

그리고 그것이 거기 있었습니다. OpenClaw 게이트웨이(gateway)라는 서비스가 백그라운드(background)에서 실행 중이었습니다. 다음 명령어를 사용하여 서비스 파일을 검색했습니다:

```bash
ls ~/Library/LaunchAgents/ | grep openclaw
```

이것은 macOS에게 프로세스를 계속 유지하라고 알려주는 서비스 구성 파일(service config file)을 보여주었습니다. 저는 먼저 이전 방식으로 언로드(unload)를 시도했습니다:

```bash
launchctl unload ~/Library/LaunchAgents/ai.openclaw.gateway.plist
```

하지만 최신 macOS 버전에서는 해당 명령어가 더 이상 작동하지 않습니다. 그래서 대신 최신 명령어를 사용했습니다:

```bash
launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/ai.openclaw.gateway.plist
```

이것이 마침내 서비스를 완전히 중지시켰습니다. 그 후 NemoClaw onboard를 한 번 더 실행했고, 이번에는 포트가 비어 있었으며, 두 가지 검사 모두 통과했고, 샌드박스가 올바르게 시작되었습니다.

### OpenClaw 설치

다음 명령어를 사용하여 샌드박스에 진입한 후:

```bash
nemoclaw y connect
```

`nemoclaw connect` 이 명령어는 컨테이너(container) 내의 대화형 셸(interactive shell)로 연결합니다. 호스트(host)의 디렉토리(directories)는 마운트(mounted)되지 않으며, 대신 컨테이너의 작업 공간인 `/sandbox`가 작업 영역이 됩니다. 호스트와 완전히 격리된 환경에서 OpenClaw가 사전 설치된 상태로 시작됩니다.

샌드박스는 생성 시 네트워크로부터 거의 완전히 차단됩니다. `nemoclaw onboard`는 NemoClaw가 자동으로 정책을 적용하여, OpenClaw 기능에 필요한 엔드포인트(endpoints) (Anthropic API, GitHub 등)만 허용 목록(allow list)에 추가합니다. 이 설계는 "완전히 열림 → 제한"이 아니라 "완전히 닫힘 → 필요한 구멍만 열기" 방식입니다.

AI 에이전트와 대화하기 위해 Claude (클로드)를 실행하려고 시도했지만… "command not found (명령어를 찾을 수 없음)" 오류가 발생했습니다. 다음으로 `npm install -g @anthropic-ai/claude-code` 명령어로 설치를 시도했지만… 403 오류가 발생했습니다. 샌드박스가 보안상의 이유로 이 패키지(package)를 차단한다는 것을 알게 되었습니다. 그때 흥미로운 점을 발견했는데, OpenClaw가 이미 샌드박스에 설치되어 있었다는 것입니다. `openclaw --help`를 실행하자… 방대한 명령어 목록이 나타났습니다. 이것은 단순히 Claude의 CLI (Command Line Interface)가 아니라, 이미 내장된 완전한 에이전트 인터페이스였습니다. 그래서 다음 명령어를 실행했습니다:

```bash
openclaw nemoclaw onboard
```

이것은 추론 엔드포인트(inference endpoint)를 구성했습니다. 이미 `inference.local`을 가리키는 설정이 있었고, 이는 `nvidia/nemotron-3-super-120b-a12b` 모델을 사용하여 NVIDIA Cloud API를 통해 라우팅(routing)되고 있었습니다.

기존 설정(config)을 유지한 다음, `openclaw configure`를 실행했습니다. 위자드를 따라 OpenAI를 제공업체(provider)로 선택하고 (NVIDIA의 API가 OpenAI 호환이므로), 기존 `OPENAI_API_KEY`를 사용했으며, 동일한 `nvidia/nemotron-3-super-120b-a12b` 모델을 선택했습니다. 마지막으로 터미널 채팅 인터페이스(terminal chat interface)를 실행했습니다:

```bash
openclaw tui
```

그리고 그렇게, 저는 샌드박스 안에서 AI 에이전트와 대화하고 있었습니다. 모든 것이 설정되었고, 안전하며, 바로 사용할 준비가 되었습니다.

### NVIDIA API 키 발급받기

build.nvidia.com으로 이동하여 NVIDIA 계정으로 로그인하세요.

로그인 후, Nemotron 3 (네모트론 3)로 이동하여 Nemotron 3 Super 120B를 클릭한 다음, API 키 생성 버튼을 클릭하세요.

이 키를 복사하세요. 이 키는 샌드박스와 OpenClaw가 NVIDIA 서버에 연결하는 데 사용될 것입니다. 이 키를 비공개로 유지하고 공개적으로 공유하지 마세요. 키를 받으면 샌드박스를 구성하고 AI 에이전트와 대화할 준비가 된 것입니다.

샌드박스에 들어간 후, 메시지를 보낼 때마다 에이전트가 계속 HTTP 403 오류를 반환하는 것을 발견했습니다. 알고 보니, NVIDIA API 키는 호스트 머신(host machine)에 설정되어 있었지만, 샌드박스 자체에는 전달되지 않았습니다.

샌드박스 내에서 `export OPENAI_API_KEY="nvapi-..."` 명령어로 설정하려고 시도했지만… 작동하지 않았습니다. 게이트웨이가 이미 호스트에서 실행 중이었기 때문에 샌드박스 내에서 재시작하는 것은 실패했습니다:

```bash
openclaw gateway restart
```

다음으로 `nemoclaw y status` 명령어로 샌드박스 상태를 확인했습니다.

그런 다음 구성 파일(config files)이 어디에 있는지 찾기 위해 Docker 컨테이너를 직접 파고들었습니다:

```bash
docker exec openshell-cluster-nemoclaw find /run/k3s/containerd -name "config.json" -path "*/sandbox/.nemoclaw/*"
```

수정해야 할 두 개의 파일을 발견했습니다: `/sandbox/.nemoclaw/config.json` — 여기에는 `"credentialEnv": "OPENAI_API_KEY"`가 있었고, `/sandbox/.openclaw/agents/main/agent/auth-profiles.json` — 여기도 동일하게 `"source": "env"`가 있었습니다.

컨테이너 내부에서 두 파일을 직접 패치(patched)하여 환경 변수(env var) 참조를 실제 API 키로 교체했습니다. 기본적으로 `"source": "env"`를 `"source": "literal"`로 변경하고 키를 바로 그곳에 넣었습니다.

그 후, `nemoclaw y connect` 명령어로 다시 연결했습니다. 터미널 인터페이스를 실행했습니다:

```bash
openclaw tui
```

메시지를 보냈고… 마침내 통과했습니다. 더 이상 403 오류는 없었습니다. 에이전트가 제대로 작동하고 있었습니다.

TUI (터미널 사용자 인터페이스)가 열리자, 저는 "hey how are you"라고 입력하고 Enter (엔터)를 눌렀습니다. 모델은 콜드 스타트(cold start) 상태의 120B 파라미터 모델(parameter model)이었기 때문에 응답하는 데 시간이 걸렸습니다. 보시다시피, NemoClaw를 성공적으로 설정했습니다. 에이전트가 우리에게 응답했습니다.

## 저의 인상:

*   NemoClaw는 OpenClaw의 "모든 것이 허용되는" 문제를 커널 수준 격리(kernel-level isolation)와 애플리케이션 수준 네트워크 제어로 해결합니다.
*   아직 알파(alpha) 단계이고 다소 미흡한 부분이 있지만, 에이전트 기반 보안(agent-based security)에 있어 유망한 방향을 제시합니다.

개인 사용의 경우, 일반 OpenClaw를 자유롭게 사용할 수 있지만, 기밀 작업은 NemoClaw 환경에서 격리하세요. 기업용으로는 NemoClaw를 표준으로 삼고 깃옵스(GitOps)로 정책을 관리하세요.

이러한 사용 구분이 가장 실용적인 해결책으로 보입니다.

🧙‍♂️ 저는 AI 생성 전문가입니다! 프로젝트 협업을 원하시면 여기로 문의하시거나 저와의 1대1 컨설팅 콜을 예약하세요.