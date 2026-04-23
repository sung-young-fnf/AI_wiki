# 번역 기사

- **원본 URL:** https://medium.com/codetodeploy/openclaw-on-raspberry-pi-building-a-local-ai-automation-system-with-lm-studio-e65395df1c25
- **번역 일시:** 2026-02-22 20:08:49

---

# Raspberry Pi에서 OpenClaw 실행하기: LM Studio를 활용한 로컬 AI 자동화 시스템 구축

본 가이드는 Raspberry Pi와 로컬 LLM(거대언어모델)을 활용하여 OpenClaw를 '헤드리스(headless)', '상시 가동(always-on)'형 AI 자동화 엔진으로 구축하는 방법을 다룹니다.

---

### OpenClaw에 주목해야 하는 이유

최근 AI 에이전트와 자동화에 관한 논의를 지켜봐 왔다면 'OpenClaw'라는 이름을 자주 접했을 것입니다. OpenClaw는 단순한 챗봇 프레임워크가 아닙니다. 다음과 같은 기능을 수행하는 **장시간 실행형 AI 에이전트**를 구축하기 위한 시스템입니다.

*   WhatsApp, Telegram, Discord, iMessage 등을 통한 대화
*   컴퓨터 상에서 작업 실행
*   대화 전반의 정보 기억
*   지속적인 프롬프트(prompt) 입력 없이도 자율적 행동

특히 마지막 항목이 핵심입니다. 대부분의 AI 도구는 반응형(reactive)인 반면, OpenClaw는 스스로 '살아있는' 에이전트를 위해 설계되었습니다. 이 글에서는 LM Studio를 통해 제공되는 로컬 LLM을 기반으로 Raspberry Pi 4에 OpenClaw를 설정한 과정을 기록합니다.

### 핵심 개념: 제어 평면(Control Plane)으로서의 OpenClaw

개념적으로 OpenClaw는 일종의 게이트웨이(Gateway) 역할을 합니다. 18789 포트에서 실행되는 웹소켓(WebSocket) 서버인 게이트웨이는 중앙 집중식 메시징 및 제어 계층으로 작동합니다. WhatsApp, Telegram, Discord, 예약된 작업 등 모든 흐름이 이 단일 프로세스를 통과합니다.

플랫폼별로 일회성 봇을 만드는 대신, OpenClaw는 하나의 **제어 평면(control plane)**과 여러 통합 기능을 제공합니다. 이러한 설계 덕분에 일반적인 자동화 스크립트보다 확장성이 뛰어납니다.

### 에이전트의 '뇌': 마법이 아닌 마크다운(Markdown)

각 OpenClaw 에이전트는 워크스페이스 디렉토리 내에 존재합니다. 여기에는 복잡한 데이터베이스 스키마나 불투명한 설정 형식이 없으며, 오직 평범한 **마크다운(Markdown)** 파일만 존재합니다.

#### 1. SOUL.md — 코드로 정의된 페르소나
에이전트의 성격(personality)을 정의합니다. 이 파일은 모든 시스템 프롬프트에 주입됩니다. 에이전트의 성격이 문자 그대로 편집과 버전 관리가 가능한 텍스트 파일이라는 점은 매우 강력한 의도적 설계입니다.

#### 2. 메모리(memory/) — 1등 시민으로서의 기억
대화 전반에 걸친 장기 기억을 저장합니다. 즉, 에이전트가 단순히 마지막 메시지가 아니라 '사용자'를 기억하게 됩니다. 시간이 지날수록 상호작용은 단순 트랜잭션에서 맥락 중심(contextual)으로 변화합니다.

### 하트비트(Heartbeat): 프롬프트 없이 행동하는 원리
대부분의 AI는 사용자가 물어야 대답하고 멈춥니다. 하지만 OpenClaw 에이전트는 **하트비트(Heartbeat)** 메커니즘을 통해 능동적으로 행동합니다. 에이전트는 주기적으로 깨어나 지침을 가져오고, 작업을 수행한 뒤 다시 잠듭니다. 이는 본질적으로 **AI 행동을 위한 크론잡(cron job)**과 같습니다.

*주의: 하트비트는 자율성을 부여하지만 보안 고려 사항도 수반합니다. 이것이 제가 로컬 우선 배포와 네트워크 격리, 하드웨어 직접 제어를 선호하는 이유입니다.*

---

### Raspberry Pi가 OpenClaw에 적합한 이유

OpenClaw는 강력한 GPU보다는 다음과 같은 요소가 필요합니다.
*   신뢰성 (Reliability)
*   낮은 전력 소비 (Low power usage)
*   상시 가동 가능성 (Always-on availability)

따라서 **Raspberry Pi 4 (8GB RAM)**는 최적의 선택입니다. 제 구성에서는 다음과 같이 역할을 분담했습니다.
*   **Raspberry Pi:** OpenClaw 실행, 에이전트 관리, 메모리, 스케줄링 및 통합 처리.
*   **외부 PC (M4 MacBook Pro):** 무거운 LLM 추론(inference) 처리.

#### 운영체제 선택: Ubuntu Server
HDMI나 키보드 연결 없이 SSH 우선 워크플로우를 위해 **Ubuntu 25 Server**를 선택했습니다. 데스크톱 환경이 필요한 경우, ARM에서 불안정한 GNOME 대신 가벼운 **XFCE**와 **xRDP**를 설치하여 안정적인 원격 접속 환경을 구축했습니다.

---

### 기술적 설정 요약

#### 1. Node.js 버전 문제
OpenClaw는 **Node.js 22 이상**을 요구합니다. 기본 리포지토리의 Node 20을 삭제하고 NodeSource를 통해 22 버전을 설치해야 오류를 방지할 수 있습니다.

#### 2. LM Studio를 활용한 로컬 LLM 실행
무거운 모델을 Pi에서 직접 돌리는 대신, 다른 머신의 LM Studio에서 모델을 호스팅합니다.
*   **핵심 설정:** LM Studio에서 'Serve on Local Network(로컬 네트워크 서비스)' 옵션을 활성화해야 Pi에서 접속이 가능합니다.
*   **OpenClaw 연결:** `~/.openclaw/openclaw.json` 설정 파일에서 LM Studio를 프로바이더(provider)로 구성합니다. 이를 통해 모든 데이터가 로컬 네트워크 내에 머물면서도 클라우드 LLM처럼 작동하게 됩니다.

---

### 실제 활용 사례: AI 자동화
설정이 완료되면 OpenClaw는 스케줄링, 컨텍스트 주입, 메시지 전달을 처리합니다.
*   **예시:** 매일 아침 정해진 시간에 에이전트가 메시지를 생성하여 WhatsApp으로 자동 전송 (시간대 인식 기능 포함).

### 클라우드 모델(Claude/Anthropic)에서 벗어난 이유: 비용
처음에는 Anthropic의 Claude를 사용해 보았습니다. 하지만 단 한 명의 에이전트가 매일 아침 인사 메시지를 보내는 간단한 실험만으로도 **하루 약 0.95달러(약 1,300원)**의 비용이 발생했습니다. 

학습이나 개인 프로젝트용으로 하루 1달러는 예상보다 빠르게 누적됩니다. 반면 로컬 모델(LM Studio를 통한 gpt-oss-20b 등)로 전환하면:
*   토큰당 비용 제로(0)
*   사용량에 대한 불안감 해소
*   무제한 실험 가능

---

### 대안적인 실행 방법

1.  **Docker 활용:** 하드웨어 관리의 번거로움 없이 가장 빠르게 시작할 수 있는 방법입니다.
2.  **가상 머신(VM):** 별도의 하드웨어가 없다면 가상 환경에서 Ubuntu를 실행하여 테스트할 수 있습니다.

### 결론

OpenClaw는 단순한 AI 도구가 아니라, **AI를 사용하는 자동화 프레임워크**입니다. 이를 Raspberry Pi와 로컬 LLM에서 실행하면 다음과 같은 장점을 얻습니다.
*   **프라이버시 보호**
*   **신뢰성 및 상시 가동**
*   **예측 가능한 비용**

실험 비용이 '0'이 될 때 AI 자동화는 훨씬 더 흥미로워집니다. 실제적인 행동을 수행하는 AI 에이전트에 관심이 있다면 OpenClaw는 충분히 탐구해 볼 가치가 있습니다.

---
**참고 링크:**
*   OpenClaw GitHub: `https://github.com/openclaw/openclaw`
*   LM Studio: `https://lmstudio.ai`