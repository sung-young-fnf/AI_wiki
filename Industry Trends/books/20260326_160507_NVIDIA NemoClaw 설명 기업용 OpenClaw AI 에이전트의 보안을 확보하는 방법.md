# 번역 기사

- **원본 URL:** https://pub.towardsai.net/nvidia-nemoclaw-explained-how-it-secures-openclaw-ai-agents-for-enterprise-deployment-6a606c2ddc33
- **번역 일시:** 2026-03-26 16:05:07

---

# NVIDIA NemoClaw 설명: 기업용 OpenClaw AI 에이전트의 보안을 확보하는 방법

## 사이드바 메뉴

글쓰기
알림
Towards AI

우리는 엔터프라이즈 AI를 구축합니다. 우리가 배운 것을 가르칩니다. Towards AI Academy에서 10만 명 이상의 AI 실무자들과 함께하세요. 무료: 6일 에이전트형 AI 엔지니어링 이메일 가이드: https://email-course.towardsai.net/

출판물 팔로우

사진 출처: Nvidia

멤버 전용 스토리

NVIDIA는 OpenClaw의 강력하지만 위험한 AI 에이전트를 어떻게 안전하고 기업용으로 준비된 플랫폼으로 전환했을까요?
Divy Yadav
8분 독서
·
2026년 3월 17일

---

3

젠슨 황(Jensen Huang)은 GTC 2026 기조연설 도중 잠시 멈춰 섰습니다. 3만 명의 청중을 바라보며 그는 기업 보안팀을 매우 불안하게 만드는 말을 꺼냈습니다.

“귀사의 기업 네트워크 내 AI 에이전트는 민감한 정보에 접근하고, 코드를 실행하며, 외부와 통신할 수 있습니다. 소리 내어 말해보세요. 생각해 보세요.”

그는 과장하는 것이 아니었습니다. 그는 인류 역사상 가장 빠르게 성장한 오픈소스 프로젝트인 OpenClaw, 즉 오픈소스 AI 에이전트를 설명하고 있었습니다.

NemoClaw는 그 문제에 대한 NVIDIA의 해답입니다. 여기서는 실제 중요한 점과 과장된 이야기가 가리고 있는 것, 그리고 이 솔루션이 여러분의 시간을 투자할 가치가 있는지 살펴보겠습니다.

사진 출처: 저자

## 첫째: OpenClaw가 급속도로 확산된 이유(그리고 기업들이 두려워한 이유)

사진 출처: 저자

NemoClaw를 이해하려면 먼저 OpenClaw가 무엇이며 왜 그렇게 빨리 혼란을 초래했는지 이해해야 합니다.

### AI 에이전트란 무엇인가?

AI 에이전트는 챗봇(chatbot)이 아닙니다. 질문에 단순히 답변만 하는 것이 아니라, '실행'합니다. 목표를 주면, 그 목표를 단계별로 나누고, 지속적인 개입 없이 각 단계를 실행합니다. 실제로 OpenClaw는 다음을 수행할 수 있습니다.

*   코드를 작성하고 테스트한 다음, 버그를 자동으로 수정합니다.
*   파일, 이메일, 캘린더를 관리합니다.
*   웹을 탐색하고 외부 API(Application Programming Interface)를 호출합니다.
*   사용자의 모니터링 없이 백그라운드에서 지속적으로 실행됩니다.

당신이 작업을 지시하면, 에이전트가 작업을 수행합니다.

### 이야기를 말해주는 숫자들:

*   약 60일 만에 GitHub 스타 25만 개 이상을 기록하며 React를 넘어섰습니다.
*   최고조에는 주간 방문자 수가 2백만 명 이상이었습니다.
*   황은 이를 “인류 역사상 가장 인기 있는 오픈소스 프로젝트”라고 불렀습니다.
*   HTML, Linux, Kubernetes와 비교했습니다.

황은 “오늘날 전 세계 모든 기업은 OpenClaw 전략이 필요합니다.”라고 말했습니다. “리눅스(Linux) 전략, HTTP(Hypertext Transfer Protocol) 전략, 모바일 전략이 필요했던 것과 마찬가지로 말이죠.”

### 그렇다면 왜 기업들은 OpenClaw를 금지했을까요?

OpenClaw는 의무적인 클라우드 구독 없이 사용자 장치에서 전적으로 실행됩니다. 파일 시스템, 이메일, 캘린더, 코드베이스, 텔레그램(Telegram), 디스코드(Discord)와 통합됩니다. 유용하죠. 하지만 기업 네트워크용으로 설계된 적은 없습니다.

### 빠르게 드러난 문제점들:

*   와이어드(Wired)에 의해 확인된 바와 같이, 메타(Meta)는 직원들이 회사 기기에서 OpenClaw를 실행하는 것을 제한했습니다.
*   치명적인 취약점(CVE-2026–25253, CVSS 8.8)으로 인해 악성 웹페이지를 통해 수 밀리초(millisecond) 만에 완전한 원격 코드 실행(Remote Code Execution) 체인이 허용되었습니다.
*   악의적인 행위자가 문서나 이메일에 명령어를 삽입하여 에이전트가 외부 지시를 따르도록 속이는 프롬프트 주입 공격(Prompt Injection Attack)은 어떤 익스플로잇(exploit)도 필요 없는 구조적 위험으로 기록되었습니다.

기능은 현실이었지만, 안전 장치(guardrails)는 그렇지 못했습니다.

## NemoClaw는 실제로 무엇인가

NemoClaw는 OpenClaw를 대체하는 것이 아닙니다. OpenClaw 아래에 설치되는 보안 및 인프라(infrastructure) 계층입니다.

이렇게 생각해 보세요.

*   OpenClaw는 시키는 것은 무엇이든 하고, 관련되어 보이는 것에 접근하며, 외부 세계와 자유롭게 소통하는 뛰어난 신입 직원입니다.
*   NemoClaw는 단 하나의 설치 명령(install command)으로 이루어진 고용 계약서, 출입증, 정책 매뉴얼, 그리고 보안 승인 절차입니다.

NemoClaw는 세 가지 핵심 구성 요소를 포함합니다.

*   **OpenShell 런타임(runtime):** 프로세스 수준에서 에이전트를 격리하는 샌드박스(sandboxed) 환경입니다. 네트워크 요청, 파일 접근, 추론(inference) 호출은 선언적 정책(declarative policy)에 의해 제어됩니다. 에이전트는 허용되지 않은 호스트에 접근할 수 없고, 정의된 디렉토리 외부를 읽거나 쓸 수 없으며, 권한 에스컬레이션(privilege escalation)이 차단됩니다.
*   **Nemotron 모델:** GeForce RTX 노트북부터 DGX Station에 이르기까지, 사용자의 하드웨어에서 로컬(locally)로 실행되는 NVIDIA의 오픈 모델입니다. 에이전트는 장치 내에서 완전히 작동하며, 데이터가 장치 외부로 나가지 않습니다.
*   **개인정보 보호 라우터(Privacy router):** 에이전트가 클라우드 기반 모델(GPT, Claude, Gemini)을 필요로 할 때, 이 라우터는 해당 호출을 가로채고, 차등 프라이버시(differential privacy)를 사용하여 개인 식별 정보(personally identifiable information)를 제거한 후, 통제된 백엔드(backend)를 통해 라우팅합니다.

전체 스택은 하나의 명령으로 설치됩니다.

```bash
curl -fsSL https://nvidia.com/nemoclaw.sh | bash
```

30초 만에 엔터프라이즈급 에이전트 보안을 구현합니다.

## NemoClaw vs OpenClaw: 비교

유용한 접근 방식은 인프라(infrastructure) 대 애플리케이션(application)입니다. OpenClaw는 에이전트가 어떻게 생각하고 행동하는지 정의하고, NemoClaw는 에이전트가 무엇을 할 수 있도록 허용되는지 정의합니다.

사진 출처: 저자

## 보안 계층은 실제로 어떻게 작동하는가

사진 출처: 저자

NemoClaw를 설치하면, 네 가지 보안 계층이 각기 다른 지점에서 잠겨 있는 샌드박스 환경이 생성됩니다.

| 계층        | 보호 대상                  | 잠금 시점                         |
| :---------- | :------------------------- | :-------------------------------- |
| **네트워크**  | 무단 외부 연결 차단        | 런타임(runtime) 시 핫 리로드 가능 |
| **파일 시스템** | `/sandbox` 및 `/tmp` 외부의 읽기 및 쓰기 차단 | 샌드박스 생성 시 잠김             |
| **프로세스**  | 권한 에스컬레이션 및 위험한 시스템 호출 차단 | 샌드박스 생성 시 잠김             |
| **추론**    | 모든 모델 API 호출 가로채기 및 라우팅 | 런타임(runtime) 시 핫 리로드 가능 |

### 실제로 정책 시행은 어떻게 작동하는가:

*   에이전트가 허용 목록(allowlist)에 없는 호스트에 접근하려고 하면, OpenShell은 해당 요청을 차단하고 운영자 터미널(operator terminal)에 표시합니다.
*   사용자가 승인하거나 거부합니다.
*   승인되면, 해당 호스트는 이후 정책에 추가됩니다.
*   이 시스템은 모든 가능한 연결을 미리 지정하도록 요구하는 대신, 실제 에이전트 행동으로부터 학습합니다.

### 특히 규정 준수(compliance) 팀을 위해:

샌드박스 설계도(blueprint)는 버전이 관리됩니다. 상태 간 변경 사항을 비교하고(diff changes), 이전 정책으로 되돌리며(roll back), 무엇이 언제 변경되었는지 정확히 검토할 수 있습니다. 이러한 감사 추적(audit trail)이 기업 배포를 이론적 가능성뿐 아니라 현실적으로 만듭니다.

## 황이 설명한 더 큰 변화: SaaS가 AaaS로 변화 중

이것은 GTC 기조연설에서 하드웨어 발표보다 덜 다루어졌지만, 소프트웨어 제품을 개발하는 모든 이에게 더 중요한 부분입니다.

황은 모든 제품 관리자의 화이트보드에 적혀 있어야 할 한 문장을 말했습니다.

“모든 SaaS(Software as a Service) 기업은 AaaS(Agentic as a Service) 기업이 될 것입니다.”

### 이러한 변화가 실제로 의미하는 것:

**기존 SaaS 모델:**

*   소프트웨어를 구축합니다.
*   사람이 로그인하여 워크플로우를 클릭하고 작동시킵니다.
*   결과는 사람의 품질과 가용성에 따라 달라집니다.

**서비스형 에이전트(AaaS) 모델:**

*   소프트웨어를 대신 작동시키는 AI 에이전트를 배포합니다.
*   정책과 목표를 정의합니다.
*   에이전트가 매 단계마다 사람의 개입 없이 24시간 실행을 처리합니다.

이것은 미래 예측이 아닙니다. GTC에서 확인된 출시 파트너에는 Adobe, Salesforce, CrowdStrike, Dell, Cisco, Google이 포함됩니다. 이들은 단순히 트렌드에 편승하는 기업들이 아닙니다. 이들의 기업 고객들은 이미 이를 요구하고 있습니다.

### 토큰 예산(token budget)의 변화:

황은 기조연설을 멈추고 이렇게 말했습니다. “미래에는 모든 엔지니어가 연간 토큰 예산을 갖게 될 것입니다. 그들은 기본급으로 연간 수십만 달러를 벌게 될 것이고, 저는 그 위에 아마도 그 절반 정도를 토큰으로 지급하여 그들의 역량을 10배 증폭시킬 것입니다.”

그는 덧붙였습니다. “‘얼마나 많은 토큰이 제안에 포함되어 있습니까?’라는 질문은 이미 실리콘밸리(Silicon Valley)의 채용 질문입니다. 그는 이것이 2030년의 예측이 아니라, NVIDIA에서 지금 일어나고 있는 일이라고 설명했습니다.

### 비즈니스 모델(business model)의 함의:

만약 AI 에이전트가 영업팀을 대신하여 CRM(고객 관계 관리)을 운영할 수 있다면, 더 이상 팀이 사용하는 소프트웨어에 비용을 지불하는 것이 아닙니다. 팀을 대신하여 에이전트가 사용하는 소프트웨어에 비용을 지불하게 됩니다. 이는 스택(stack) 내 모든 벤더(vendor)에게 다른 가격 모델, 다른 조달(procurement) 대화, 그리고 다른 통합 표면(integration surface)을 의미합니다.

## 실제로 NemoClaw로 전환해야 할까요?

다음과 같은 경우에는 NemoClaw가 필요 없습니다.

*   개인 생산성 목적으로 자신의 장치에서 OpenClaw를 사용하는 경우
*   로컬(local) 환경에서 에이전트를 실험하는 개발자인 경우
*   귀하의 업무가 민감한 기업 데이터나 네트워크 연결 엔터프라이즈 시스템을 다루지 않는 경우
*   새로운 것보다 안정성을 원하는 경우. NemoClaw는 알파(alpha) 소프트웨어이며 문서화된 미완성 부분이 있습니다.

다음과 같은 경우에는 NemoClaw를 평가해봐야 합니다.

*   민감한 데이터를 처리하는 기업 네트워크에 AI 에이전트를 배포하려는 경우
*   귀하의 산업에 규정 준수(compliance) 요구 사항이 있는 경우: 의료, 금융, 법률, 정부
*   내부 시스템, 이메일 또는 데이터베이스(database)에 접근하는 워크플로우를 자동화하려는 경우
*   보안팀이 조직 내 OpenClaw를 차단했고 승인된 진행 경로가 필요한 경우
*   SaaS 또는 플랫폼 제품을 구축하고 있으며 기업 고객에게 에이전트 기능을 제공하는 방법을 고려 중인 경우

결론: NemoClaw는 OpenClaw가 할 수 없는 새로운 기능을 제공하지 않습니다. OpenClaw가 차단되었던 곳에서 OpenClaw의 잠재력을 해제합니다. 이미 자유롭게 OpenClaw를 실행하고 있었다면 NemoClaw는 추가적인 부담(overhead)입니다. 실행이 차단되었었다면 NemoClaw는 해결책(key)입니다.

## NemoClaw 시작하는 방법

설치하기 전에 다음을 확인하세요.

*   리눅스 우분투(Linux Ubuntu) 22.04 LTS 이상
*   Node.js 20+ 및 npm 10+ (누락된 경우 설치 관리자가 처리)
*   도커(Docker) 설치 및 실행 중
*   NVIDIA OpenShell 설치됨
*   build.nvidia.com에서 NVIDIA API 키(API key)

### 설치:

```bash
# Step 1: Install
curl -fsSL https://nvidia.com/nemoclaw.sh | bash

# Step 2: Connect to your sandbox
nemoclaw my-assistant connect

# Step 3: Launch the interactive agent interface
openclaw tui

# Or send a single test message
openclaw agent --agent main --local -m "hello" --session-id test
```

온보딩 마법사(onboarding wizard)는 샌드박스 생성, 추론 구성, 초기 보안 정책 적용을 자동으로 처리합니다.

### 시작하기 전에 알아야 할 네 가지 사항:

*   NemoClaw는 새로운 OpenClaw 설치를 요구합니다. 기존 설정을 마이그레이션(migrating)하는 것은 아직 지원되지 않습니다.
*   `openclaw nemoclaw Plugin` 명령은 아직 개발 중입니다. 현재로서는 `nemoclaw host CLI`(명령줄 인터페이스)를 기본 인터페이스로 사용하세요.
*   문제가 발생하면, NemoClaw 수준의 상태를 확인하려면 `nemoclaw <name> status`를 실행하고, 기본 샌드박스 상태를 확인하려면 `openshell sandbox list`를 실행하세요. 오류는 두 계층 모두에서 발생할 수 있습니다.
*   깃허브(GitHub)에 문제를 제기하세요. 이 단계에서는 프로젝트가 커뮤니티 피드백을 적극적으로 수집하고 있습니다.

지원 하드웨어: GeForce RTX PC 및 노트북, RTX PRO 워크스테이션(workstation), DGX Station, DGX Spark. 하드웨어 독립적(Hardware-agnostic)이므로 AMD 및 Intel 머신도 작동합니다. Dell은 NemoClaw와 OpenShell이 사전 설치된 시스템을 출시하는 최초의 하드웨어 파트너입니다.

## 이 모든 것이 어디로 향하고 있는가

OpenClaw는 운영 체제(Operating System)의 전환점입니다. 그 아키텍처(architecture)는 OS가 수행하는 작업과 직접적으로 연결됩니다.

*   자원 관리
*   파일 시스템 접근 제어
*   도구 및 주변 장치 연결
*   프로세스 스케줄링
*   서브프로세스 생성
*   런타임(runtime) 연결

황은 무대에서 이를 명확히 밝히며, OpenClaw의 아키텍처를 설명할 때 “운영 체제를 설명하는 데 사용하는 것과 동일한 구문”을 사용했다고 말했습니다.

NemoClaw는 그 OS의 엔터프라이즈 배포판(distribution)입니다.

레드햇(Red Hat)이 리눅스(Linux)를 데이터 센터(data centre)에 적합하게 만든 것과 마찬가지로, NemoClaw는 OpenClaw를 기업 네트워크에 적합하게 만들려고 시도하고 있습니다.

### 솔직한 평가:

성공 여부는 아직 확실하지 않은 세 가지에 달려 있습니다.

*   알파 버전의 미완성 부분이 얼마나 빨리 개선되는지
*   파트너 통합(Salesforce, CrowdStrike, Google)이 실제로 출시되고 잘 작동하는지
*   기업 보안 커뮤니티가 독립적인 검토 후 NVIDIA의 샌드박스 구현을 신뢰하는지

기능적인 측면은 이미 확정되었습니다. 거버넌스(governance) 인프라 문제는 아직 구축 중입니다. NemoClaw는 애플리케이션 계층이 아닌 인프라 계층에서 이 문제에 답하려는 첫 번째 진지한 시도입니다.

만약 기업 고객을 위한 제품을 개발한다면, 에이전트는 소프트웨어가 실행되는 운영 계층이 되고 있습니다. NemoClaw는 그 아래의 거버넌스 계층입니다. 이 두 가지 사실 모두 지금 당장 계획에 포함되어야 합니다.

인공지능
머신러닝
프로그래밍
기술
데이터 과학

---

3

**Towards AI 게재**
팔로워 11.7만 명
·
최근 발행 7시간 전

우리는 엔터프라이즈 AI를 구축합니다. 우리가 배운 것을 가르칩니다. Towards AI Academy에서 10만 명 이상의 AI 실무자들과 함께하세요. 무료: 6일 에이전트형 AI 엔지니어링 이메일 가이드: https://email-course.towardsai.net/

팔로우
**글쓴이: Divy Yadav**
팔로워 1.2천 명
·
팔로잉 35명

저는 실제 AI 시스템이 어떻게 작동하는지, 그리고 프로덕션 환경에 적합하고 탄력적인 AI 애플리케이션을 간단하고 실용적인 방식으로 구축하는 방법에 대해 글을 씁니다. https://github.com/dvy246

답변 (3)
모든 답변 보기

도움말
상태
소개
채용
언론
블로그
개인정보처리방침
규칙
약관
음성 변환