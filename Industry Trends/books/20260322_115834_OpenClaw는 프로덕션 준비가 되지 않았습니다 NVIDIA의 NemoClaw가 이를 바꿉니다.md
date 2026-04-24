# 번역 기사

- **원본 URL:** https://agentnativedev.medium.com/openclaw-was-never-ready-for-production-nvidias-nemoclaw-changes-that-268ce03ded95
- **번역 일시:** 2026-03-22 11:58:34

---

# OpenClaw는 프로덕션 준비가 되지 않았습니다. NVIDIA의 NemoClaw가 이를 바꿉니다.

OpenClaw 생태계에 깊이 관여한 사람에게 "테넌트(tenant) 데이터를 어떻게 격리합니까?" 또는 "어떤 정책이 실제로 아웃바운드(outbound) 네트워크 접근을 제어합니까?"와 같은 간단한 질문을 하면 대화는 빠르게 모호해지는 경향이 있습니다.

아주 최근까지 솔직한 답변은 보통 "저희는 조심하려고 노력합니다."와 같은 식이었습니다.

OpenClaw를 흥미롭게 만들었던 바로 그 아키텍처(architecture)는 동시에 프로덕션(production) 환경에서 신뢰하기 어렵게 만들었습니다.

보안 사고가 쌓였고, 에이전트(agent) 신원, 아웃바운드(outbound) 접근, 비밀(secrets) 처리 및 배포 경계에 대한 질문은 심각한 프로덕션(production) 워크로드(workload)에 중요해졌습니다. 아무도 막대한 운영 부담을 원치 않기 때문입니다.

그래서 Meta는 이를 회사 기기에서 금지했고 LangChain은 거리를 두었습니다.

이것이 NVIDIA가 GTC 2026에서 NemoClaw를 통해 뛰어든 간극입니다. NemoClaw는 자율 에이전트(autonomous agent)를 위한 아키텍처적으로 건전한(architecturally sound) 인프라(infrastructure) 수준의 보안 공백을 해결합니다.

_전체 크기로 이미지를 보려면 Enter 키를 누르거나 클릭하세요._

## NemoClaw란 무엇인가요?

OpenClaw는 어시스턴트(assistant)이며, NemoClaw는 OpenClaw를 둘러싼 NVIDIA의 통제된 런타임(runtime) 및 보안 계층입니다.

NemoClaw는 "NVIDIA OpenShell용 OpenClaw 플러그인(plugin)"입니다.

이 플러그인(plugin)은 `openclaw nemoclaw` 아래에 명령어를 등록하는 경량의 타입스크립트(TypeScript) 패키지입니다. OpenClaw 게이트웨이(gateway)와 인-프로세스(in-process)로 실행되며 사용자 대면 CLI(Command Line Interface) 상호작용을 처리합니다.

_전체 크기로 이미지를 보려면 Enter 키를 누르거나 클릭하세요._

이러한 차이점은 프로젝트를 평가하는 방식을 전적으로 변화시킵니다.

*   어시스턴트(assistant) 경험 자체를 원한다면 여전히 OpenClaw를 평가하는 것입니다.
*   해당 어시스턴트(assistant)에 대한 더 엄격한 런타임(runtime) 제어를 원한다면 (예: 샌드박싱(sandboxing), 정책 시행, 추론 라우팅(inference routing), 배포 거버넌스(deployment governance)), NemoClaw와 OpenShell을 함께 평가하는 것입니다.

이것은 애플리케이션(application)이 프로덕션(production) 환경에서 안전하게 실행되도록 하는 인프라(infrastructure)입니다.

## 세 가지 계층으로 구성된 아키텍처(Architecture)

NemoClaw의 아키텍처(architecture)는 세 가지 개별적인 계층으로 나뉘며, 각 계층은 다른 종류의 문제를 해결합니다.

### 계층 1: OpenShell (샌드박스 런타임 - Sandbox Runtime)

OpenShell은 NVIDIA의 자율 에이전트(autonomous agent)를 위한 오픈 소스(open-source) 보안 런타임(runtime)입니다.

모든 에이전트(agent)는 다음을 기반으로 구축된 강화된 샌드박스(sandbox) 내에서 실행됩니다.

*   **Landlock**: 파일 시스템(filesystem) 접근 제어를 위한 리눅스(Linux) 보안 모듈(module).
*   **seccomp**: 커널(kernel) 수준에서 권한 상승 및 위험한 시스템 호출(syscall)을 차단하는 시스템 호출 필터링.
*   **네트워크 네임스페이스(Network namespaces)**: 에이전트(agent)별 완전한 네트워크 격리. 공유 도커(Docker) 네트워킹이 아닙니다. 실제 네임스페이스(namespace) 수준 격리입니다.
*   **프로세스 격리(Process isolation)**: 손상된 종속성(dependency)이 권한을 상승시키거나 커널(kernel) 수준 작업을 실행하려고 해도 샌드박스(sandbox)가 이를 방지합니다.

저는 `--privileged` 옵션이 조용히 활성화된 도커(Docker) 컨테이너(container)에 불과한 샌드박스(sandbox)형 에이전트(agent) 플랫폼을 너무 많이 보았습니다.

_전체 크기로 이미지를 보려면 Enter 키를 누르거나 클릭하세요._

### 계층 2: NemoClaw (정책 및 배포 브리지 - Policy and Deployment Bridge)

NemoClaw는 OpenClaw를 OpenShell에 연결하고, 어시스턴트(assistant)가 실행되는 방식에 대한 정책 인식 제어 기능을 추가합니다.

NemoClaw는 이 생태계에서 블루프린트(blueprint)라고 불리는, 다음을 오케스트레이션(orchestrate)하는 버전 관리형 구성 파일(configuration file)을 도입합니다.

*   샌드박스(sandbox) 생성 매개변수
*   보안 정책 정의
*   추론 제공자 라우팅(inference provider routing)
*   세션 모니터링 규칙
*   운영자 승인 워크플로우(workflow)

단 하나의 명령어로 완전히 격리된 에이전트(agent) 환경을 구동합니다.

```bash
curl -fsSL https://nvidia.com/nemoclaw.sh | bash
nemoclaw onboard
```

`onboard` 명령어는 추론 제공자 연결, 파일 시스템(filesystem) 정책 설정 및 네트워크 접근 규칙 구성 과정을 안내합니다.

### 계층 3: 추론 라우팅(Inference Routing)

추론 계층은 세 가지 개별적인 프로파일(profile)을 지원합니다.

*   **NVIDIA 호스팅 추론(NVIDIA-hosted inference)**: NVIDIA의 관리형 엔드포인트(endpoint)로의 클라우드 API(Cloud API) 호출.
*   **로컬 NIM (NVIDIA 추론 마이크로서비스 - Inference Microservice)**: Nemotron과 같은 모델(model)을 NVIDIA 하드웨어에서 로컬(local)로 실행.
*   **자체 호스팅 vLLM (Self-hosted vLLM)**: 자체 인프라(infrastructure)에서 오픈 모델(open model) 실행.

이 세 가지 모두 동일한 정책 계층, 동일한 보안 제어 및 동일한 감사 추적(audit trail)에 의해 관리됩니다.

_전체 크기로 이미지를 보려면 Enter 키를 누르거나 클릭하세요._

이는 제가 지금까지 대화했던 모든 헬스케어(healthcare), 금융, 법률 분야의 잠재 고객으로부터 들었던 "우리는 외부 API(API)로 데이터를 보낼 수 없습니다. 절대로 안 됩니다."라는 문제를 해결합니다.

규제 산업에 AI 에이전트(AI agent) 서비스를 판매하려 했던 사람이라면 이것이 얼마나 혁신적인지 알 것입니다. 대화는 "데이터 상주(data residency) 문제는 나중에 해결하죠."에서 "여기 정책 파일(policy file)이 있습니다. 직접 감사(audit)해 보세요."로 바뀝니다.

## NemoClaw로 빠르게 시작하기

설정은 표준 필수 구성 요소(prerequisites)로 상당히 쉽습니다.

### 필수 구성 요소

NemoClaw를 사용하기 전에 다음 사항을 확인하십시오.

*   **리눅스(Linux)**: Ubuntu 22.04 이상 버전. Landlock 및 seccomp 통합은 리눅스(Linux) 전용입니다. macOS를 사용 중인 경우 VM(Virtual Machine) 또는 원격 서버(remote server)가 필요합니다.
*   **도커(Docker)**: 컨테이너(container) 기반 구성 요소에 필요합니다.
*   **GitHub CLI**: OpenShell 바이너리(binary)는 GitHub 릴리스(release)를 통해 배포되므로, `gh`가 인증되어 있어야 합니다.
*   **NVIDIA GPU (권장되지만 필수는 아님)**: NemoClaw는 하드웨어에 구애받지 않지만, 로컬 NIM 추론(local NIM inference)은 명백히 NVIDIA 하드웨어를 필요로 합니다.

### 1단계: NemoClaw CLI 설치

```bash
curl -fsSL https://nvidia.com/nemoclaw.sh | bash
```

`nemoclaw` CLI는 샌드박스(sandbox)화된 OpenClaw 에이전트(agent)를 설정하고 관리하는 주요 진입점입니다. 이는 샌드박스(sandbox) 생성, 정책 적용 및 추론 제공자 설정을 OpenShell CLI를 통해 오케스트레이션(orchestrate)하는 파이썬(Python) 아티팩트(artifact)인 버전 관리형 블루프린트(blueprint)에 중요한 작업을 위임합니다.

_전체 크기로 이미지를 보려면 Enter 키를 누르거나 클릭하세요._

설치가 완료되면 실행 환경 요약이 확인됩니다.

```
──────────────────────────────────────────────────
Sandbox      my-assistant (Landlock + seccomp + netns)
Model        nvidia/nemotron-3-super-120b-a12b (NVIDIA Cloud API)
──────────────────────────────────────────────────
Run:         nemoclaw my-assistant connect
Status:      nemoclaw my-assistant status
Logs:        nemoclaw my-assistant logs --follow
──────────────────────────────────────────────────

[INFO]  === Installation complete ===
```

그런 다음 TUI(Text User Interface) 또는 CLI(Command Line Interface)를 통해 샌드박스(sandbox)에 연결하고 에이전트(agent)와 대화할 수 있습니다.

```bash
$ nemoclaw my-assistant connect
```

이것이 전부입니다.

"에이전트 SaaS 패턴(Agentic SaaS Patterns)" 전자책에서 저는 7가지 핵심 아키텍처(architecture) 평면을 분석하고, 이러한 에이전트(agent) 패턴이 실제 구현에서 어떻게 작동하는지 보여주는 샘플 저장소(repository)를 포함했습니다.

여기에서 다운로드할 수 있습니다: [2026년 에이전트 SaaS 패턴 승리(Agentic SaaS Patterns Winning in 2026)](https://agentnativedev.gumroad.com/l/agentic-saas-patterns), 실제 사례, 아키텍처(architecture) 및 다른 곳에서는 찾을 수 없는 워크플로우(workflow)로 가득합니다.

## NemoClaw 대 OpenClaw

_전체 크기로 이미지를 보려면 Enter 키를 누르거나 클릭하세요._

NemoClaw는 오픈 소스(open-source) 투명성과 커널(kernel) 수준 보안, 그리고 로컬 추론(local inference) 지원을 결합한 훌륭한 옵션입니다.

## NemoClaw가 (아직) 해결하지 못한 것

모든 도구가 모든 것을 해결할 수는 없으며, 한계에 대해 솔직한 것이 고객과의 신뢰를 구축하는 방법입니다.

### 관측 가능성(Observability)은 아직 초기 단계

NemoClaw는 세션 모니터링(session monitoring)과 기본적인 활동 로그(log)를 제공하지만, 프로덕션(production) 인프라(infrastructure)에서 기대하는 관측 가능성(observability) 수준에는 아직 미치지 못합니다.

Datadog, Grafana 또는 OpenTelemetry와의 내장된 통합 기능은 없습니다.

NemoClaw가 제공하는 기능 위에 자체 모니터링 파이프라인(pipeline)을 구축해야 합니다.

### 비기술직 운영자를 위한 GUI(Graphical User Interface) 없음

NemoClaw의 모든 것은 CLI(Command Line Interface) 기반으로 구동됩니다. 이는 개발자에게는 괜찮지만, 제품 팀이 에이전트(agent) 배포를 관리해야 하는 경우 교육을 받거나 CLI(CLI) 위에 구축된 맞춤형 대시보드(dashboard)가 필요할 것입니다.

온보딩(onboarding) 흐름은 상호작용적이고 합리적으로 사용자 친화적이지만, 일상적인 운영 (샌드박스(sandbox) 관리, 네트워크 승인 요청 검토, 정책 업데이트 등)은 터미널(terminal) 사용에 익숙해야 합니다.

### 테스트 프레임워크(Testing Framework)는 아직 존재하지 않음

프로덕션(production)에 배포하기 전에 샌드박스(sandbox) 내에서 에이전트(agent) 동작을 테스트할 수 있는 내장된 방법이 없습니다. 정책 변경을 위한 드라이 런(dry-run) 모드도 없습니다. 정책 파일(policy file)이 에이전트(agent)에 필요한 워크플로우(workflow)를 실제로 허용하는지 검증하기 위한 시뮬레이션(simulation) 환경도 없습니다.

### 멀티 클라우드 오케스트레이션(Multi-Cloud Orchestration)은 범위 외

NemoClaw는 단일 호스트(host) 또는 클러스터(cluster)에 에이전트(agent)를 배포합니다. AWS, GCP 및 온프레미스(on-premises) 환경에 걸쳐 동시에 일관된 정책 시행과 함께 에이전트(agent)를 실행해야 하는 경우, NemoClaw는 환경 간 오케스트레이션(orchestration)을 처리하지 않습니다. 그 위에 페더레이션(federation) 또는 자체 제어 플레인(control plane)이 필요할 것입니다.

### 문서화는 아직 따라잡는 중

여기서 공정하게 말하자면, 문서는 알파 소프트웨어(alpha software)로서는 좋지만 포괄적이지는 않습니다. 특정 동작을 이해하기 위해 원치 않게 소스 코드(source code)를 더 자주 읽게 되었습니다.

플러그인(plugin) 측 명령어와 호스트(host) 측 명령어 간의 구분이 항상 명확하지는 않습니다. 일부 기능은 GitHub README에 문서화되어 있지만 공식 문서 사이트에는 없고, 그 반대인 경우도 있습니다.

이는 알파 소프트웨어(alpha software)에 대해 예상되는 바이므로, 이 점을 염두에 두고 접근하십시오.

## 결론

밤잠을 설치게 만드는 질문들로 글을 마무리하고 싶습니다.

*   다중 에이전트(multi-agent) 시스템을 위한 정책 모델(policy model)은 어떻게 발전할까요?
*   에이전트(agent)가 자체 정책을 발전시켜야 할 때는 어떻게 될까요?

NemoClaw를 사용하여 구축하고 있거나 고려 중이라면, 여러분의 경험에 대해 듣고 싶습니다.