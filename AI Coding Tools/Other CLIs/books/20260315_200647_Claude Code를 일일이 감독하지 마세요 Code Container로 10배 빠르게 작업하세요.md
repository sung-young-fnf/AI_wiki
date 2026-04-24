# 번역 기사

- **원본 URL:** https://medium.com/gitconnected/stop-babysitting-claude-code-get-work-done-10x-faster-with-code-container-fcd515381751
- **번역 일시:** 2026-03-15 20:06:47

---

# Claude Code를 일일이 감독하지 마세요. Code Container로 10배 빠르게 작업하세요.

저는 Claude Code, Codex, OpenCode를 루트 권한(root permissions), 승인 절차 없이, 네트워크 접근 권한을 부여하여 실행합니다. 아닙니다, 저는 미치지 않았습니다.

kevinMEH
팔로우
5분 독서
·
2026년 3월 5일

프로젝트를 열 때마다 제 AI 코딩 에이전트(AI coding harness)에게 루트 접근 권한(root access), 전체 파일 권한, 그리고 무제한 네트워크 접근 권한을 부여합니다. 승인 절차도, 샌드박스(sandboxing)도 없습니다. 감독도 하지 않습니다.

미친 소리처럼 들린다는 것을 압니다. 하지만 Code Container를 사용하면 실제로 완벽하게 안전합니다.*

혹시라도 악성 `rm -rf` 명령어가 실행될까 봐 매번 프롬프트(prompt)를 감시하는 대신, Code Container를 사용하여 모든 프로젝트를 격리된 컨테이너(isolated container)에 마운트(mount)합니다. 이 컨테이너 안에서는 제 에이전트가 모든 권한을 가지고 자유롭게 실행될 수 있습니다.

Code Container는 완전한 오픈 소스(open source)입니다. 여기에서 확인해 보세요: https://github.com/kevinMEH/code-container

## 일일이 감독하기

모든 것은 몇 달 전 시작되었습니다. 저는 모노레포(monorepo)를 분석하고 있었고, 저는 프론트엔드(frontend)를 분석하는 동안 Claude Code가 백엔드(backend)를 분석하기를 원했습니다. 병렬 작업으로 최대의 효율을 내고자 했습니다. 하지만 병렬 작업은 5초 만에 중단되었습니다. Claude Code가 저를 방해하며 물었습니다: "진행하시겠습니까?" 물론이죠. "진행하시겠습니까?" 물론이죠. "진행하시겠습니까?" 물론이죠.

저는 Claude Code의 권한 요청 때문에 계속해서 방해받았고, 결국 아무런 작업도 할 수 없었습니다. 하지만 동시에 시스템 전체를 날려버릴(nukes) 가능성 때문에 Claude Code에 모든 권한을 부여하고 싶지도 않았습니다.

Claude를 일일이 감독하거나 하드 디스크로 러시안룰렛(Russian roulette)을 하는 것 사이에서 갈등하던 저는, 세 번째 옵션을 만들기로 결정했습니다.

Code Container를 소개합니다. 모든 프로젝트를 위한 초경량 컨테이너(ultra-lightweight container)로, 에이전트가 자유롭게 실행될 수 있도록 합니다. 300밀리초(ms)의 빠른 시작 시간, 공유되는 설정(configurations) 및 대화 내역(conversations), 그리고 완벽한 시스템 격리(full system isolation)를 특징으로 합니다.

## Code Container 구축하기

아이디어는 간단했습니다. 제 프로젝트 디렉토리(project directory)를 도커 컨테이너(Docker container) 안에 마운트하고, 에이전트가 필요로 하는 모든 도구를 미리 설치한 다음, 완전히 제한 없이 실행되도록 하고 싶었습니다. 만약 무언가를 삭제하더라도, 컨테이너 안에서 삭제될 뿐입니다. 최악의 경우? 컨테이너를 제거하고 새로운 컨테이너를 만들면 됩니다. 제 실제 머신(machine)은 손상되지 않습니다.

첫 번째 버전은 개발하는 데 한 시간 정도밖에 걸리지 않았습니다. 단순히 도커 컨테이너를 실행하고, 프로젝트 디렉토리와 제 `~/.claude` 폴더를 마운트한 다음, 바로 Claude Code를 사용할 수 있도록 연결해 주는 간단한 셸 스크립트(shell script)였습니다.

그 후로 며칠에 한 번씩 스크립트를 개선하며 더 많은 기능을 추가했습니다. 자동 컨테이너 정리, OpenCode 및 Codex 지원, 공유 캐시(shared caches), 모든 프로젝트에서 공유되는 대화 내역, 그리고 사용 편의성 개선 등입니다. 컨테이너는 기능이 풍부해졌지만, 가볍고 빠르게 유지하기 위해 특별히 노력했습니다.

오늘날 저는 Codex나 OpenCode를 열어야 할 때마다 컨테이너를 사용하며, Code Container를 만든 이후로 매일 꾸준히 사용하고 있습니다.

## 작동 방식

전체 프로젝트는 셸 스크립트와 도커파일(Dockerfile)로 구성되어 있습니다. 어떤 프로젝트 디렉토리에서든 `container` 명령을 실행하면 다음과 같이 작동합니다:

*   해당 프로젝트에 한정된(scoped) 컨테이너(`code-{project-name}-{path-hash}` 이름으로)를 생성하거나 재개합니다.
*   프로젝트 디렉토리를 컨테이너 내 `/root/{project-name}` 경로에 마운트합니다.
*   에이전트 설정 파일(`~/.codex`, `~/.config/opencode` 등)을 컨테이너 내에 마운트합니다.
*   OpenCode, Codex, Claude Code가 바로 실행될 준비가 된 `bash` 셸(shell)로 진입합니다.

종료하면 컨테이너는 자동으로 중지됩니다. 다음번에 동일한 프로젝트에서 `container`를 실행하면, 설치된 패키지(packages), 셸 기록(shell history) 등 모든 것이 컨테이너별로 유지되어 마지막에 떠났던 상태 그대로입니다.

`npm` 및 `pip` 캐시(caches)와 같은 리소스, 에이전트 설정(harness configs), 대화 내역은 모든 컨테이너에서 공유되므로, 어떤 프로젝트에 있든 에이전트는 중단했던 지점부터 즉시 작업을 이어갈 수 있습니다. SSH 키(SSH keys)와 Git 설정(Git config)은 호스트(host)에서 읽기 전용으로 마운트되므로, 커밋(commits) 및 푸시(pushes)는 정상적으로 작동합니다.

컨테이너는 또한 매우 가볍습니다. 새로운 컨테이너를 시작하는 데 약 1초가 걸립니다. 기존 컨테이너를 재개하는 것은 즉각적입니다.

## 저의 작업 흐름 (Workflow)

저는 대부분의 작업에 GLM 4.7과 OpenCode를 사용하고 있으며, 복잡한 작업이 필요할 때는 Codex로 전환합니다. 작업 루틴은 매우 간단합니다: 프로젝트 디렉토리로 `cd`한 다음, `container`를 실행하고, `opencode`를 시작한 뒤, 저의 작업을 병렬로 수행합니다.

컨테이너 안에서는 에이전트가 원하는 모든 것을 할 수 있도록 완전한 권한을 부여합니다. 완전한 권한. 어떠한 중단도 없습니다.

심지어 동일한 프로젝트에 대해 여러 컨테이너를 실행할 수도 있어서, 제 시스템에 어떤 문제가 발생할지 걱정할 필요 없이 생산성을 크게 높일 수 있습니다.

솔직히 말해서, 저는 더 이상 컨테이너 밖에서 에이전트를 실행하지 않습니다. 너무 빠르고 원활해서 그럴 이유가 없습니다. 사실 이 방법을 저 혼자만 사용하려고 했었습니다. 너무나 자연스럽게 제 작업 흐름의 일부가 되어 모든 사람이 이렇게 하는 줄 착각할 정도였습니다. 그러다 한 친구가 사용해보고 싶다고 해서, "아예 오픈 소스로 공개하는 건 어떨까?" 하고 생각했습니다. 그래서 지금 여기에 이렇게 글을 쓰고 있습니다.

추신: 아직 Claude Code나 Anthropic의 비싼 모델 및 사기성 사용 제한에서 벗어나지 못했다면, 당신은 기회를 놓치고 있는 겁니다. GLM과 OpenCode를 시도해 보세요.

## container 시작하기

시스템에 따라 설정에 약 5분 정도 소요됩니다:

```bash
git clone https://github.com/kevinMEH/code-container
cd code-container
# `container` 명령을 전역으로 설치
ln -s "$(pwd)/container.sh" /usr/local/bin/container
# 에이전트 설정을 code-container로 복사
./copy-configs.sh
# 이미지 빌드 (약 5분, 한 번만)
container --build
```

그 다음, 어떤 프로젝트에서든:

```bash
cd /path/to/your/project
container
```

설정 파일도, 프로젝트별 설정도 필요 없습니다. 그저 `container` 명령만 입력하면 됩니다.

Code Container는 완전한 오픈 소스이며 기여를 환영합니다: https://github.com/kevinMEH/code-container

추가적인 개발 도구(development tools)나 패키지(packages) (GCC, Go, Rust 등)를 추가하고 싶다면, 단순히 Dockerfile을 커스터마이징(customizing)하면 됩니다. 마운트 지점(mounting points)을 더 추가하고 싶다면, `container.sh` 상단에 있는 핵심 `docker run -it` 명령을 수정하면 됩니다.

에이전트에 완전한 권한을 부여하는 것을 망설였거나 모든 것을 수동으로 승인해 왔다면, `container`를 한번 사용해보고 어떤지 알려주세요.

오늘은 여기까지입니다. 다음 시간에 또 만나요!