# 번역 기사

- **원본 URL:** https://medium.com/@richardhightower/the-great-framework-showdown-superpowers-vs-bmad-vs-speckit-vs-gsd-360983101c10
- **번역 일시:** 2026-03-22 12:24:52

---

# 선도적인 에이전트 기반 코딩 프레임워크 대결: Superpowers, BMAD, SpecKit, GSD 실무자 비교

Rick Hightower
팔로우
읽는 데 16분 소요
·
5일 전

217
8

다섯 가지 에이전트 기반 코딩 프레임워크(agentic coding frameworks)가 현재 총 170,000개 이상의 GitHub 스타를 보유하고 있으며, 각각 '감으로 하는 코딩'(vibe coding)을 체계적인 AI 지원 개발로 대체하겠다고 약속합니다. 이 실무자 비교는 BMAD의 엔터프라이즈 팀 시뮬레이션, SpecKit의 게이티드(gated) 사양 프로세스, OpenSpec의 브라운필드(brownfield) 우선 델타(delta) 사양, GSD의 컨텍스트 격리 웨이브 병렬 처리, 그리고 Superpowers의 TDD(Test-Driven Development) 기반 규율 시스템을 심층 분석합니다. 의사결정 매트릭스와 하이브리드 조합 전략은 팀이 상황에 맞는 적절한 도구를 선택하는 데 도움을 줍니다.

'감으로 코딩하는 시대'(vibe coding era)는 끝났습니다. 총 170,000개 이상의 GitHub 스타를 보유한 다섯 가지 프레임워크가 이를 대체할 새로운 패러다임을 정의하기 위해 경쟁하고 있습니다. 각 프레임워크는 AI 에이전트에 얼마나 많은 구조가 필요한지에 대해 다른 접근 방식을 취합니다. 잘못된 프레임워크를 선택하면 실제 프로덕션 코드를 한 줄도 작성하기 전에 수 주를 낭비할 수 있습니다.

이 시리즈의 첫 번째 기사에서 저는 Superpowers가 AI 코딩 에이전트를 반응적인 코드 생성기에서 규율 잡힌 엔지니어링 파트너로 어떻게 변화시키는지 살펴보았습니다. 이전에 저는 다른 시리즈에서 GSD를, 그리고 여러 다른 기사에서 SpecKit을 다루었습니다. 독자들의 반응은 저를 놀라게 했습니다. 독자들은 구조화된 AI 워크플로우(workflow)의 필요성에 대해 논쟁하지 않았습니다. 그 논쟁은 이미 끝난 문제입니다. 대신 가장 일반적인 질문은 "Superpowers는 BMAD, SpecKit, GSD, 그리고 OpenSpec과 어떻게 비교됩니까?"였습니다.

우리는 과거에도 비교 분석을 진행했지만, 이번에는 Superpowers를 포함시키고자 했습니다. 이 비교는 주관적이며 본질적으로 강한 의견이 반영되어 있음을 알아주시기 바랍니다. 저는 엔지니어링 자동화를 위한 이러한 에이전트 기반 도구들에 대한 여러분의 의견에 매우 관심이 많습니다. 개인적으로는 GSD와 Superpowers를 선호합니다.

합당한 질문입니다. 에이전트 기반 코딩 생태계는 폭발적으로 성장했습니다. 2026년 3월 현재, 이 다섯 가지 프레임워크는 총 170,000개 이상의 GitHub 스타를 보유하고 있습니다. 각 프레임워크는 '감으로 하는 코딩'을 보다 체계적인 방식으로 대체하겠다고 약속하지만, 문제에 접근하는 방식은 근본적으로 다릅니다. 자신의 상황에 맞지 않는 프레임워크를 선택하면 실제 프로덕션 코드를 한 줄도 작성하기 전에 수 주간의 설정 시간을 낭비하게 됩니다.

이 기사는 바로 그러한 비교입니다. 단순히 기능 목록을 나열하는 마케팅 활동이 아닌, 각 프레임워크가 언제 빛을 발하고 언제 한계를 드러내는지에 대한 실무자의 평가입니다.

## 공유된 세 가지 요소(Triad): 에이전트(Agent), 워크플로우(Workflow), 스킬(Skill)

차이점을 자세히 살펴보기 전에, 이들 프레임워크가 공통적으로 가지는 요소를 이해하는 것이 도움이 됩니다. 철학적 의견 차이에도 불구하고, 이 비교에 포함된 모든 프레임워크는 동일한 세 가지 기본 요소(primitives)를 중심으로 지능을 구성합니다.

**에이전트(Agent)**는 시스템이 채택하는 페르소나(persona) 또는 역할입니다. BMAD는 이들을 페르소나(분석가(Analyst), 설계자(Architect), 개발자(Developer))라고 부릅니다. GSD는 이들을 서브에이전트(subagent, 연구자, 기획자, 실행자)라고 부릅니다. Superpowers는 이들을 상황에 따라 트리거되는 스킬(skill, 때로는 게이트(gate)라고도 함)이라고 부릅니다. SpecKit은 CLI(Command Line Interface) 명령에 에이전트 동작을 내장합니다. 용어는 다르지만, 개념은 동일합니다. 즉, 정의된 책임을 가진 전문 지식의 경계가 있는 컨텍스트(bounded context)입니다.

**워크플로우(Workflow)**는 에이전트를 파이프라인으로 연결하는 일련의 과정입니다. BMAD는 4단계 애자일(agile) 주기를 사용합니다. SpecKit은 명확한 체크포인트를 통해 게이티드(gated) 단계를 강제합니다. GSD는 실행을 종속성 순서에 따른 웨이브(wave) 형태로 조직합니다. Superpowers는 브레인스토밍(brainstorming)부터 코드 검토(code review)에 이르는 과정을 스킬들을 연결하여 자동 파이프라인으로 만듭니다. 모든 프레임워크는 구조화되지 않은 프롬프트(prompt)가 무의미한 결과물을 생성한다는 데 동의하며, 논쟁은 얼마나 많은 구조가 충분한지에 대한 것입니다.

**스킬(Skill)**은 에이전트가 수행하는 원자적 기능(atomic capabilities)입니다. PRD(Product Requirements Document) 작성, 테스트 실행, 다이어그램 생성, 코드 검토, 종속성 분석 등이 있습니다. 각 프레임워크는 다른 스킬들을 묶지만, 추상화(abstraction) 개념은 동일합니다. 즉, 정의된 입력과 출력을 가진 재사용 가능한 작업 단위입니다.

다음 다이어그램은 각 프레임워크가 이 공유된 세 가지 요소(트라이어드)를 어떻게 다르게 구현하는지 보여줍니다.

(이미지: 각 프레임워크의 공유된 트라이어드 구현 방식)

이 세 가지 요소(트라이어드)가 중요한 이유는, 프레임워크들이 마케팅에서 제시하는 것만큼 다르지 않다는 것을 의미하기 때문입니다. 이들은 모두 동일한 구조적 문제를 해결하고 있습니다. 즉, 복잡한 소프트웨어 개발을 AI 에이전트가 신뢰할 수 있게 실행할 수 있는 관리 가능한, 품질 보증(quality-gated) 단계로 분해하는 방법입니다. 차이점은 어떤 단계를 강조하는지, 게이트를 얼마나 엄격하게 적용하는지, 그리고 어떤 절충점(trade-off)을 수용하는지에 있습니다.

## 강력한 엔터프라이즈 시뮬레이터: BMAD 메서드 (BMAD Method)

GitHub 스타: 40.2k | 라이선스: MIT | 최신 버전: v6.0.4 (2026년 3월) [GitHub](https://github.com/BMAD-Framework) | [문서](https://bmad.dev/docs)

BMAD는 '애자일 AI 기반 개발을 위한 획기적인 방법(Breakthrough Method for Agile AI-Driven Development)'의 약자입니다. 이 비교의 다른 프레임워크들이 전동 공구라면, BMAD는 완전한 기계 공장(machine shop)입니다.

### 작동 방식

BMAD는 12개 이상의 특수 AI 페르소나(persona)를 사용하여 전체 애자일 개발 팀을 시뮬레이션합니다. 각 페르소나는 전문 지식, 책임, 제약 조건 및 예상 결과물을 명시하는 "코드로서의 에이전트(Agent-as-Code)" 마크다운(Markdown) 파일로 정의됩니다. AI에게 단순히 코드를 작성하라고 요청하는 것이 아닙니다. 제품 관리자(Product Manager) 페르소나에게 PRD를 작성하도록 요청하고, 설계자(Architect) 페르소나에게 시스템을 설계하도록, 개발자(Developer) 페르소나에게 구현하도록, 그리고 스크럼 마스터(Scrum Master) 페르소나에게 백로그(backlog)의 우선순위를 정하도록 요청합니다.

이 방법론은 4단계 주기를 따릅니다. **분석(Analysis)** 단계에서는 문제와 제약 조건을 담은 1페이지 분량의 PRD를 생성합니다. **기획(Planning)** 단계에서는 PRD를 인수 기준(acceptance criteria)이 포함된 사용자 스토리(user story)로 분해합니다. **솔루션(Solutioning)** 단계에서는 설계자가 최소한의 설계를 생성하는 동안 개발자가 구현 단계를 제안합니다. **구현(Implementation)** 단계는 각 단계에서 업데이트된 아티팩트(artifact)를 통해 작은 스토리들을 반복적으로 진행합니다.

BMAD를 독특하게 만드는 것은 문서 중심(documentation-centric) 철학입니다. BMAD에서 소스 코드는 유일한 진실의 원천이 아닙니다. PRD, 아키텍처 스케치, 사용자 스토리는 코드와 함께 지속되는 일등 아티팩트(first-class artifacts)입니다. 모든 AI 작업(pass)은 일시적인 채팅 기록이 아닌 이러한 영구적인 문서로부터 작동하기 때문에 점진적이고 검증 가능합니다.

### 파티 모드 및 스케일 적응형 인텔리전스

BMAD의 "파티 모드(Party Mode)"는 단일 세션 내에서 여러 페르소나가 협업할 수 있도록 합니다. 별도의 에이전트 구성 간에 전환하는 대신, 개발자와 대화 중에 설계자를 호출하여 설계를 검토하거나, 제품 관리자가 인수 기준을 다듬는 동안 스크럼 마스터에게 스토리를 재정렬하도록 할 수 있습니다.

이 프레임워크는 또한 프로젝트 복잡성에 따라 문서화의 엄격도를 조절하는 스케일 적응형 인텔리전스(scale-adaptive intelligence)를 특징으로 합니다. 버그 수정은 가벼운 분석을 거치고, 새로운 마이크로서비스(microservice)는 아키텍처 다이어그램, API 계약(API contracts), 배포 사양(deployment specifications)을 포함한 전체 처리를 받습니다. 이는 프레임워크가 필요 없는 작업에 엔터프라이즈급 의례(ceremony)를 적용하는 것을 방지합니다.

### 장점

BMAD는 추적성(traceability)이 뛰어납니다. 모든 결정에는 기록(paper trail)이 남습니다. 모든 요구사항은 사용자 스토리로, 이는 구현 작업으로, 다시 테스트로 매핑됩니다. 감사 추적(audit trail), 규정 준수 문서(compliance documentation) 또는 팀 간의 인계가 필요한 조직의 경우, BMAD는 이러한 요구사항을 자연스럽게 충족하는 아티팩트를 생성합니다.

34개 이상의 핵심 워크플로우(workflow)는 브레인스토밍부터 배포에 이르는 전체 소프트웨어 개발 생명 주기(SDLC)를 포괄합니다. 1,468건의 커밋(commit)과 123명의 기여자(contributor)는 성숙하고 활발하게 개발 중인 생태계를 보여줍니다.

### 단점

학습 곡선(learning curve)이 가파릅니다. BMAD의 페르소나 시스템, 워크플로우 라이브러리, 아티팩트(artifact) 규칙을 내재화하는 데 시간이 걸립니다. 사이드 프로젝트를 만드는 개인 개발자에게 시뮬레이션된 6인 애자일 팀을 운영하는 오버헤드(overhead)는 비효율적입니다. 3개 항목의 백로그(backlog) 우선순위를 정하는 데 스크럼 마스터(Scrum Master) 페르소나가 필요하지 않습니다.

파일 기반 조정 모델(file-based coordination model)은 빠른 설계 변경이 필요할 때 반복(iteration) 속도를 늦출 수 있습니다. PRD를 업데이트하고, 이어서 아키텍처 스케치, 사용자 스토리, 구현 작업을 업데이트하는 과정은 문서 업데이트의 연쇄 반응을 일으켜 피드백 루프(feedback loop)에 마찰을 더합니다.

BMAD는 또한 일부 AI 코딩 환경과 호환성 문제가 있는 것으로 알려져 있습니다. 클로드 코드(Claude Code) 및 사용자 정의 시스템 프롬프트(custom system prompts)를 지원하는 다른 도구와 함께 작동하지만, 경험이 항상 원활한 것은 아닙니다.

### 실질적인 절충점

BMAD의 핵심 절충점(trade-off)은 포괄성(comprehensiveness) 대 민첩성(agility)입니다. 가장 철저한 문서화와 명확한 감사 추적을 얻을 수 있지만, 설정 시간과 연쇄적인 업데이트로 인한 대가를 치러야 합니다. 규정 준수 아티팩트(compliance artifacts)가 실제로 필요한 팀(예: 의료, 금융, 정부 계약)은 이러한 오버헤드(overhead)가 정당하다고 여길 것입니다. 금요일까지 기능을 출시해야 하는 팀은 답답함을 느낄 것입니다. 스케일 적응형 인텔리전스(scale-adaptive intelligence)는 도움이 되지만, 프레임워크가 작업이 '간단해서' 경량 처리가 필요한 시점을 정확하게 평가한다고 신뢰할 수 있을 때만 유효합니다.

## 사양 전문가: SpecKit 및 OpenSpec

두 프레임워크는 사양(specification) 계층에 특별히 초점을 맞추지만, 서로 다른 방향에서 접근합니다.

### SpecKit: GitHub의 게이티드 프로세스(Gated Process)

GitHub 스타: 75.9k | 라이선스: MIT | 최신 버전: v0.1.4 (2026년 2월) [GitHub](https://github.com/speckit) | [블로그](https://speckit.dev/blog)

SpecKit은 GitHub가 사양 중심 개발(spec-driven development) 분야에 공식적으로 진출한 프레임워크이며, 이 비교에서 가장 높은 스타 수를 기록한 것은 GitHub의 막대한 플랫폼 영향력을 반영합니다.

핵심 철학은 모호함 없이 명확하게 명시되어 있습니다. "사양은 코드를 섬기는 것이 아니라, 코드가 사양을 섬긴다." SpecKit은 사양을 구현에 정보를 제공하는 가이드가 아니라, 구현이 파생되는 권위 있는 소스(authoritative source)로 취급합니다.

**게이티드 프로세스(Gated Process)**. SpecKit은 명시적인 체크포인트를 통해 엄격하고 순차적인 워크플로우(workflow)를 강제합니다. 현재 단계가 유효성 검사를 통과하지 않으면 다음 단계로 진행할 수 없습니다.

*   **헌법(Constitution)** (`/speckit.constitution`): 프로젝트의 기본 원칙을 수립합니다.
*   **사양 정의(Specify)** (`/speckit.specify`): 구축할 대상을 정의하고, 구조화된 요구사항과 사용자 여정(user journey)을 생성합니다.
*   **계획(Plan)** (`/speckit.plan`): 기술 구현 전략을 수립하고, plan.md, research.md 및 관련 아티팩트(artifact)를 생성합니다.
*   **작업(Tasks)** (`/speckit.tasks`): 계획으로부터 실행 가능하고 테스트 가능한 작업 목록을 생성합니다.
*   **구현(Implement)** (`/speckit.implement`): 연결된 AI 에이전트를 통해 작업을 실행합니다.

선택적 단계로는 **명확화(Clarify)** (불충분하게 명시된 영역을 위해) 및 **분석(Analyze)** (`/speckit.analyze`를 통한 아티팩트 간 일관성 검사를 위해)이 있습니다.

**장점**. 게이티드(gated) 프로세스는 AI 코딩에서 가장 흔한 실패 모드인 요구사항이 명확해지기 전에 구현으로 서두르는 것을 방지합니다. 이 프레임워크는 살아있는 문서(living documentation) 역할을 하는 풍부한 아티팩트 세트(spec.md, plan.md, research.md, data-model.md, 계약서, 퀵스타트 가이드)를 생성합니다. Copilot, Claude Code, Cursor, Gemini CLI, Windsurf를 포함한 20개 이상의 AI 코딩 에이전트 지원을 통해 SpecKit은 가장 플랫폼에 구애받지 않는(platform-agnostic) 옵션입니다.

**단점**. 의례(ceremony)가 상당합니다. 독립적인 평가에 따르면 아티팩트 생성 및 검토에 기능당 1~3시간 이상이 소요됩니다. 구성 플래그(config flag) 추가 또는 유효성 검사(validation) 버그 수정과 같은 작은 변경 사항의 경우, 전체 사양 정의-계획-작업-구현 주기를 거치는 것은 압정(thumbtack)에 대고 해머를 휘두르는 것과 같습니다. 사양은 정적입니다. 구현 중에 자동으로 업데이트되지 않으므로, 장기 프로젝트에서는 문서 불일치(documentation drift)가 실제 위험입니다. SpecKit은 또한 다중 에이전트 오케스트레이션(multi-agent orchestration) 기능이 없습니다. `/speckit.implement` 명령은 연결된 단일 AI 에이전트에게 위임하지만, 병렬 처리(parallelism)나 에이전트 격리(agent isolation)를 관리하지는 않습니다.

**절충점**. SpecKit은 과도한 사양 정의(over-specifying) 비용이 항상 불충분한 사양 정의(under-specifying) 비용보다 적다고 봅니다. 요구사항이 불명확한 그린필드 프로젝트(greenfield projects)의 경우, 이러한 판단은 보통 성과를 거둡니다. 성숙한 코드베이스(codebase)에 대한 잘 이해된 변경 사항의 경우, 사양 오버헤드(specification overhead)는 순수한 마찰이 됩니다. 기능당 1~3시간 투자는 재작업(rework)에 대한 보험입니다. 그 보험이 가치 있는지는 첫 번째 시도에서 구현이 잘못될 경우의 비용에 따라 달라집니다.

### OpenSpec: 브라운필드 우선 대안(Brownfield-First Alternative)

GitHub 스타: 29.5k | 라이선스: MIT | 최신 버전: v1.2.0 (2026년 2월) [GitHub](https://github.com/openspec) | [사이트](https://openspec.dev)

SpecKit이 새로운 시스템을 처음부터 구축하기 위해 설계되었다면, OpenSpec은 기존 코드베이스(codebase)에서 작업하는 더 복잡한 현실을 위해 설계되었습니다.

OpenSpec의 철학은 네 가지 대조되는 개념으로 요약됩니다. "유연하며(fluid) 경직되지 않고(not rigid), 반복적이며(iterative) 폭포수 방식이 아니며(not waterfall), 쉬우며(easy) 복잡하지 않고(not complex), 그린필드(greenfield)뿐만 아니라 브라운필드(brownfield)를 위해 구축되었습니다." SpecKit이 엄격한 단계 게이트(phase gate)를 강제하는 반면, OpenSpec은 정해진 순서 없이 언제든지 모든 아티팩트(artifact)를 업데이트할 수 있도록 합니다.

**델타 사양(Delta Specs)**. 핵심 혁신은 델타 마커(delta marker)를 통한 변경 격리입니다. 기존 기능을 수정할 때 전체 사양을 다시 작성하는 대신, OpenSpec은 ADDED(추가됨), MODIFIED(수정됨), REMOVED(제거됨) 마커를 사용하여 현재 상태에 대한 변경 사항을 설명합니다. 각 변경 사항은 제안서, 사양, 설계 문서 및 작업이 포함된 자체 폴더(openspec/changes/<name>/)를 가집니다. 이는 한 변경 사항이 다른 변경 사항과 충돌하는 것을 방지합니다.

**장점**. OpenSpec은 변경당 약 250줄의 결과물을 생성하며, SpecKit의 약 800줄과 비교됩니다. 이러한 오버헤드(overhead) 감소는 하루에 프로덕션 시스템에 5가지 변경 사항을 적용할 때 중요합니다. 빠른 진행(fast-forward) 명령(`/opsx:ff`)은 모든 기획 아티팩트(planning artifact)를 한 번에 스캐폴딩(scaffold)하여 다단계 의례(ceremony)를 줄입니다. 20개 이상의 AI 도구 지원으로 SpecKit만큼 이식성(portability)이 뛰어납니다. 레거시 코드베이스(legacy codebase)에서 반복적인 유지보수, 리팩토링(refactoring), 점진적인 기능 개발(incremental feature development)을 수행하는 팀에게 OpenSpec은 가장 실용적인 선택입니다.

**단점**. SpecKit과 마찬가지로 OpenSpec의 사양은 정적입니다. 구현과 자동으로 동기화되지 않습니다. 다중 에이전트 오케스트레이션(multi-agent orchestration), 병렬 실행(parallel execution), 컨텍스트 격리(context isolation) 전략이 없습니다. 포괄적인 아키텍처 문서가 필요한 대규모 그린필드 프로젝트의 경우, OpenSpec의 경량 접근 방식은 불충분한 사양 깊이(specification depth)를 초래할 수 있습니다.

**절충점**. OpenSpec은 사양 깊이를 속도(velocity)와 맞바꿉니다. 변경 사항을 더 빠르게 배포할 수 있지만, 델타 사양이 누적된 변경 사항의 완전한 아키텍처적 영향을 포착하지 못할 위험을 감수해야 합니다. 몇 달간의 점진적인 수정 과정에서 사양이 설명하는 내용과 시스템이 실제로 수행하는 작업 간의 격차는 조용히 벌어질 수 있습니다.

### SpecKit vs. OpenSpec: 정면 대결

다음 표는 두 가지 사양 중심 프레임워크 간의 주요 차이점을 보여줍니다.

(이미지: SpecKit과 OpenSpec의 주요 차이점 비교표)

## 컨텍스트 엔지니어링 강자: GSD (Get “Stuff” Done)

GitHub 스타: 28.1k | 라이선스: MIT | 최신 버전: 활발히 개발 중 (2026년 3월) [GitHub](https://github.com/GSD-Framework) | [사이트](https://gsd.dev)

GSD는 사양 중심 프레임워크들이 대체로 간과하는 문제, 즉 AI 에이전트의 컨텍스트(context) 창이 가득 차고 출력 품질이 저하될 때 발생하는 상황을 해결합니다.

### 컨텍스트 로트 문제(Context Rot Problem)

컨텍스트 로트(Context Rot)는 AI 모델이 단일 세션 내에서 더 많은 토큰(token)을 처리함에 따라 발생하는 품질 저하 현상입니다. 연구 및 실무 경험에 따르면 예측 가능한 품질 저하 곡선이 나타납니다. 컨텍스트 활용률이 0~30%일 때 모델은 최고 품질로 작동합니다. 50% 이상에서는 서두르거나, 세부 사항을 건너뛰거나, 환각(hallucination) 현상이 증가합니다. 80% 이상이 되면 모델은 대화 초반에 설정된 요구사항을 잊을 수 있습니다.

이는 이론적인 우려가 아닙니다. 단일 클로드 코드(Claude Code) 세션에서 복잡한 기능을 작업해 본 모든 개발자는 긴 대화 후 모델의 출력 품질이 눈에 띄게 저하되는 것을 경험했을 것입니다. 스무 번째 파일 편집은 두 번째 편집보다 측정 가능하게 품질이 떨어집니다.

### GSD가 이를 해결하는 방법

GSD의 핵심 아키텍처 결정은 각 실행 단위(execution unit)마다 새로운 서브에이전트(subagent) 컨텍스트를 생성하는 것입니다. 모든 작업 컨텍스트를 단일하고 점점 더 부풀어 오르는 대화에 누적하는 대신, GSD는 각 작업에 자체적인 깨끗한 200,000 토큰(token) 컨텍스트 창(context window)을 제공합니다. 작업 50은 작업 1과 동일한 컨텍스트 품질을 얻는데, 이는 관련 프로젝트 아티팩트(artifact)만 로드된 새로운 창에서 시작하기 때문입니다.

실행 모델은 작업을 종속성 순서에 따른 "웨이브(wave)"로 구성합니다.

*   **웨이브 1**: 독립적인 작업들은 각각 자체의 새로운 컨텍스트에서 병렬로 실행됩니다.
*   **웨이브 2**: 웨이브 1의 결과에 의존하는 작업들은 병렬로 실행됩니다.
*   **웨이브 3**: 모든 이전 웨이브가 완료되어야 하는 작업들은 순차적으로 실행됩니다.

이 웨이브 기반 병렬 처리(wave-based parallelism)는 "수평 계층(horizontal layers)"(모든 모델 우선, 그 다음 모든 API, 그 다음 모든 UI)보다 "수직 슬라이스(vertical slices)"(엔드 투 엔드(end-to-end) 기능)를 선호합니다. 수직 슬라이스는 작업 간 종속성(inter-task dependency)을 최소화하여 병렬로 실행될 수 있는 작업 수를 최대화합니다.

### 다중 에이전트 오케스트라(Multi-Agent Orchestra)

GSD는 다음과 같은 특수 에이전트(agent)들을 배치합니다.

*   코드베이스(codebase)를 조사하고 컨텍스트를 동시에 수집하는 네 명의 병렬 연구자.
*   연구 결과를 구조화된 실행 계획으로 변환하는 기획자.
*   실행 전에 계획의 유효성을 검사하는 계획 검사기.
*   새로운 컨텍스트에서 작업을 구현하는 웨이브 기반 병렬 실행자.
*   완료된 작업을 사양에 대해 검증하는 검증자.
*   문제가 발생했을 때 과학적 방법론 가설 테스트(scientific-method hypothesis testing)를 사용하는 디버거(debugger) 에이전트.

디버거 에이전트는 특별히 언급할 가치가 있습니다. GSD 검증은 "우리가 어떤 작업을 수행했는가?"라고 묻는 대신, "이것이 작동하려면 무엇이 참(TRUE)이어야 하는가?"라고 묻습니다. 이 목표 역추적(goal-backward) 접근 방식은 구현 세부 사항보다는 관찰 가능한 동작을 테스트하여, 작업 중심 검증이 놓치는 버그를 찾아냅니다.

### 장점

GSD는 이 비교에서 가장 실행 중심적인 프레임워크입니다. SpecKit과 OpenSpec이 사양을 생성하고 BMAD가 포괄적인 문서를 생성하는 반면, GSD는 코드를 배포합니다. GSD의 컨텍스트 격리 아키텍처(context isolation architecture)는 AI 지원 개발에서 가장 큰 실제적인 문제인 장시간 세션 동안의 품질 저하를 직접적으로 해결합니다. 원자적 깃 커밋(atomic git commit, 작업당 하나)은 깔끔한 깃 바이섹트(git bisect) 디버깅 및 개별 작업 되돌리기(task reversion)를 가능하게 합니다. 아마존(Amazon), 구글(Google), 쇼피파이(Shopify)의 엔지니어들은 이를 프로덕션 환경에서 사용합니다.

### 단점

GSD는 토큰(token) 소모가 많습니다. 모든 작업에 대해 새로운 컨텍스트를 생성하는 것은 프로젝트 컨텍스트를 반복적으로 재전송하는 것을 의미하며, 이는 단일 세션 방식보다 API 크레딧(credit)을 더 빠르게 소모합니다. 이 프레임워크는 또한 기획 단계에서 대화형(conversational)이지 않습니다. 협력적인 사양 정제보다는 실행 속도를 최적화합니다. 플랫폼 지원은 SpecKit 또는 OpenSpec보다 좁으며, 현재 클로드 코드(Claude Code), 오픈코드(OpenCode), 제미니 CLI(Gemini CLI)를 지원하지만, 통합은 더욱 원활합니다. 간단한 작업의 경우 프레임워크의 강도가 과할 수 있지만, 별도의 처리(out of band)가 필요한 할 일, 디버깅 세션(debugging session), 일회성 작업(one offs) 등을 수행하고도 이를 기획 과정에 통합할 수 있는 도구들을 제공합니다.

### 실질적인 절충점

GSD의 핵심 절충점(trade-off)은 토큰 비용 대 출력 품질입니다. 새로운 컨텍스트는 수십 개의 작업에 걸쳐 일관된 품질을 보장하지만, API 지출을 증가시킵니다. 단일 세션에서 5달러가 들었을 50개 작업 기능은 GSD의 컨텍스트 격리(context isolation) 방식으로는 25~40달러가 들 수 있습니다. 넉넉한 API 예산으로 복잡한 기능을 구축하는 팀에게는 품질 향상이 비용을 정당화합니다. 예산이 제한된 팀이 간단한 작업을 수행하는 경우에는 오버헤드를 정당화하기 어렵습니다.

두 번째 절충점은 실행 속도 대 사양 깊이입니다. GSD는 다른 어떤 프레임워크보다 빠르게 코드를 작성하지만, 기획 단계는 BMAD나 SpecKit보다 협력적이지 않습니다. 요구사항이 잘못되었다면, GSD는 잘못된 계획을 매우 효율적으로 실행할 것입니다.

추론에 중요한 깨끗한 컨텍스트(context)로 작업하는 정밀성과 여러 수준(기획 및 실행)의 검증 단계(validation step)는 GSD가 사양과 요구사항을 매우 정확하게 따르도록 하며, 그 비용을 충분히 감수할 가치가 있게 만듭니다.

## Superpowers 재검토: 규율 집행자 (The Discipline Enforcer)

GitHub 스타: 증가 중 | 라이선스: MIT

이 시리즈의 첫 번째 기사에서 저는 Superpowers를 심층적으로 다루었습니다. 여기서는 위 프레임워크들과의 상대적인 위치를 설명하겠습니다.

Superpowers는 독특한 틈새시장(niche)을 차지합니다. 주로 사양 프레임워크(SpecKit 또는 OpenSpec과 같은)도 아니고, 엔터프라이즈 팀 시뮬레이터(BMAD와 같은)도 아니며, 컨텍스트 오케스트레이션 엔진(context orchestration engine, GSD와 같은)도 아닙니다. 이는 규율 강제 시스템(discipline enforcement system)입니다. 저는 GSD와 가장 가깝다고 생각하지만, TDD(Test-Driven Development)에 있어서는 훨씬 더 엄격합니다.

### 차별점

**강력한 게이트로서의 상호작용 브레인스토밍(Interactive brainstorming as a hard gate)**. 어떤 코드가 작성되기 전에, Superpowers는 개발자와 AI 에이전트 간의 구조화된 대화를 강제합니다. 이는 선택 사항이 아닙니다. 에이전트는 설계가 제시되고 승인될 때까지 코드 작성, 프로젝트 스캐폴딩(scaffolding), 또는 어떤 구현 작업도 수행하는 것이 명시적으로 금지됩니다. BMAD도 유사한 분석 단계를 가지고 있지만, Superpowers의 게이트는 더 강력합니다. 우회로가 없으며, "이것은 너무 간단해서 설계가 필요 없다"는 회피 경로(escape hatch)도 없습니다. GSD는 마일스톤(milestone) 논의와 기획 논의를 가지고 있지만, 이들은 건너뛸 수 있습니다.

**철칙으로서의 TDD(TDD as an iron law)**. 다른 프레임워크들은 테스트를 권장합니다. Superpowers는 실패하는 테스트가 존재하기 전에 에이전트가 프로덕션 코드를 작성하면 해당 코드를 삭제하는 '삭제 후 재작성(delete-and-rewrite)' 규칙으로 이를 강제합니다. 참조용으로 저장되지도 않고, 수정되지도 않습니다. 그냥 삭제됩니다. 이는 이 비교에서 가장 적극적인 테스트 강제 방식입니다.

**설득 기반의 가드레일(Persuasion-based guardrails)**. Superpowers는 AI 에이전트의 심리를 명시적으로 다룹니다. 모델이 단계를 건너뛰기 위해 사용하는 합리화("이것은 너무 간단해서 테스트할 필요 없다", "테스트는 나중에 작성하겠다", "빨리 고쳐보자")를 명명하고 이를 선제적으로 차단합니다. 스킬은 사회 공학(social engineering)에 대해 스트레스 테스트를 거칩니다. 즉, 제작자가 긴급 상황을 시뮬레이션하여 에이전트가 편법을 쓰도록 유도하고 순응도를 관찰합니다.

**2단계 검토가 포함된 서브에이전트 디스패치(Subagent dispatch with two-stage review)**. GSD와 마찬가지로 Superpowers는 컨텍스트 오염(context pollution)을 피하기 위해 새로운 서브에이전트(subagent) 컨텍스트를 사용합니다. 그러나 GSD가 병렬 실행 속도에 초점을 맞추는 반면, Superpowers는 품질 게이트(quality gate)에 초점을 맞춥니다. 각 서브에이전트의 출력은 워크플로우(workflow)가 진행되기 전에 사양 준수 검토(spec compliance review)와 코드 품질 검토(code quality review)를 거칩니다. GSD와 동일한 토큰 사용 제약(token usage constraints)을 가질 가능성이 높습니다. Superpowers는 더 많은 작업을 수행하며, 사용자는 더 많은 리소스를 사용합니다. 둘 다 컨텍스트 로트(context rot)를 피하려고 시도하며, 이는 비용이 들지만 정확성은 그 비용을 감수할 가치가 있습니다.

### 실질적인 절충점

Superpowers의 핵심 절충점(trade-off)은 신뢰성(reliability) 대 속도(velocity)입니다. 의무적인 브레인스토밍(brainstorming), TDD 강제, 그리고 2단계 검토는 이 비교에서 GSD가 근접한 2위인 어떤 프레임워크보다도 가장 일관되게 고품질의 결과물을 생성합니다. 그러나 모든 품질 게이트는 시간을 추가합니다. 순수한 클로드 코드(Claude Code) 세션으로 10분 걸리는 작업이 Superpowers의 전체 파이프라인을 거치면 30분이 걸릴 수 있습니다. 핵심은 버그, 재작업(rework), 아키텍처적 실수 방지를 통해 절약되는 시간이 사전 규율(upfront discipline)에 소요되는 시간을 초과하는가 하는 것입니다. 버그가 비싼 생산 시스템(production systems)의 경우, 대답은 거의 항상 '예'입니다. 프로토타입(prototype) 및 일회성 코드(throwaway code)의 경우, 대답은 거의 항상 '아니오'입니다.

### 비교 방식

Superpowers는 이 비교에서 가장 주관이 뚜렷한(opinionated) 프레임워크입니다. 유연성(flexibility)을 신뢰성(reliability)과 맞바꿉니다. 브레인스토밍 단계(brainstorming phase)를 건너뛸 수 없습니다. 테스트를 작성하기 전에 코드를 작성할 수 없습니다. 코드 검토(code review)를 우회할 수 없습니다. 이러한 제약 사항은 잘 이해된 작업을 빠르게 진행하려는 숙련된 개발자에게는 속도를 늦추지만, 속도보다 코드 품질이 더 중요한 팀에게는 가장 안전한 선택이 됩니다.

## 무기 선택: 의사결정 매트릭스

어떤 프레임워크도 보편적으로 최고일 수는 없습니다. 올바른 선택은 팀 규모, 프로젝트 유형, 품질 요구사항, 그리고 의례(ceremony)에 대한 허용 수준에 따라 달라집니다.

(이미지: 프레임워크별 의사결정 매트릭스 1)
(이미지: 프레임워크별 의사결정 매트릭스 2)

어떤 프레임워크를 선택해야 할지는 매우 주관적입니다.

### 빠른 의사결정 가이드

*   다수의 이해관계자(stakeholders)가 있는 엔터프라이즈 소프트웨어(enterprise software)를 구축하고, 감사 추적(audit trail)이 필요하며, 역할 기반 조직(role-based organization)의 이점을 누릴 만큼 충분히 큰 팀을 가지고 있다면 **BMAD**를 선택하세요. 규정 준수 및 인계 문서가 진정한 요구사항일 때 오버헤드(overhead)는 그만한 가치를 합니다.
*   새로운 프로젝트를 처음부터 시작하고 가장 엄격한 사양 프로세스(specification process)를 원한다면 **SpecKit**을 선택하세요. 게이티드 단계(gated phase)는 시기상조의 구현을 방지하며, 20개 이상의 에이전트 지원(agent support)은 어떤 단일 AI 도구에도 얽매이지 않음을 의미합니다. 아티팩트(artifact) 생성에 필요한 시간 투자를 재작업(rework)에 대한 보험으로 받아들이십시오.
*   기존 코드베이스(codebase)에서 작업 중이고 지속적인 변경 사항에 대한 경량의 반복적인 사양 정의(specification)가 필요하다면 **OpenSpec**을 선택하세요. 델타 마커(delta marker) 시스템과 낮은 의례(ceremony)는 일상적인 사용에 실용적입니다. 새로운 시스템을 위한 포괄적인 아키텍처 문서가 필요하다면 건너뛰세요.
*   병렬 실행(parallel execution)의 이점을 얻을 수 있는 많은 독립적인 구성 요소(component)를 가진 복잡한 기능을 구축한다면 **GSD**를 선택하세요. 컨텍스트 격리 아키텍처(context isolation architecture)는 장시간 개발 세션에 진정으로 우수합니다. 수십 개의 작업에 걸쳐 일관된 품질을 얻는 대가로 더 높은 토큰(token) 비용을 감수하십시오. 또한 GSD는 기존 코드베이스에 대한 탁월한 지원을 제공하며, 브라운필드 프로젝트(brownfield projects) 지원 전문가입니다.
*   많은 범주에서 다른 도구가 특정 영역에서 최고라면, GSD는 항상 두 번째로 좋은 선택입니다. 만약 빠르게 하나를 선택해야 한다면, GSD로 시작하는 것이 좋습니다.
*   코드 품질이 최우선 관심사이고 테스트되고 검토되며 규율 잡힌 결과물을 얻기 위해 더 느린 속도를 감수할 의향이 있다면 **Superpowers**를 선택하세요. 의무적인 브레인스토밍(brainstorming)과 TDD(Test-Driven Development) 강제는 이 비교에서 가장 신뢰할 수 있는 코드를 생성하지만, 모든 작업에 시간을 추가합니다.

### 하이브리드 접근 방식

이 프레임워크들은 상호 배타적이지 않습니다. 일부 실무자들은 이들을 결합하여 사용합니다.

*   **SpecKit + GSD**: SpecKit의 사양 프로세스(specification process)를 사용하여 요구사항을 정의한 다음, GSD의 실행 엔진(execution engine)으로 넘겨 병렬 구현을 진행합니다. 이 조합은 가장 강력한 사양 계층과 가장 강력한 실행 계층을 결합하지만, 도구 복잡성을 두 배로 증가시킵니다. 저는 이렇게 해본 적이 있습니다. 주로 SDD(Specification-Driven Development) 프로젝트를 GSD로 마이그레이션(migrate)할 때였습니다.
*   **BMAD + Superpowers**: BMAD의 페르소나 기반 기획(persona-based planning)을 아키텍처에 사용한 다음, Superpowers의 TDD 강제를 구현에 사용합니다. 이는 BMAD의 추적성(traceability)과 Superpowers의 코드 품질 보장을 원하는 엔터프라이즈 팀에 잘 맞습니다.
*   **OpenSpec + Superpowers**: 브라운필드 프로젝트의 변경 관리(change management)를 위해 OpenSpec의 델타 사양(delta spec)을 사용한 다음, 실행을 위해 Superpowers의 품질 게이트(quality gate)를 사용합니다. 테스트 규율을 여전히 강제하면서도 가장 낮은 의례(ceremony)를 가진 조합입니다.

이 모든 프레임워크가 공유하는 트라이어드 패턴(에이전트, 워크플로우, 스킬)은 조합을 가능하게 합니다. 이들은 독립적인 구성 요소이지, 단일체(monolith)가 아닙니다.

### 다음 단계

이번 비교는 에이전트 기반 코딩 생태계가 빠르게 성숙하고 있음을 보여줍니다. 프레임워크들은 단순히 예쁜 CLI(Command Line Interface)로 프롬프트(prompt)를 감싸는 것이 아니라, 전문화되고, 틈새시장(niche)을 찾으며, 진정한 아키텍처적 혁신을 개발하고 있습니다.

이 시리즈의 다음 기사에서는 프레임워크 비교에서 프레임워크 구축으로 나아가겠습니다. 즉, 이들 도구 각각의 최고의 아이디어를 결합하여 팀의 필요에 맞는 맞춤형 파이프라인(custom pipeline)으로 자신만의 에이전트 기반 워크플로우(agentic workflow)를 구축하는 방법입니다.

'감으로 코딩하는 시대'는 끝났습니다. 더 이상 AI 지원 개발에 구조를 추가해야 하는지에 대한 질문이 아니라, 얼마나 많은 구조를 어떤 방식으로 추가해야 하는지에 대한 질문입니다. 이 다섯 가지 프레임워크는 그 질문에 대한 다섯 가지 다른 답을 제시합니다. 당신의 임무는 자신의 상황에 맞는 답을 선택하는 것입니다.

이 모든 프레임워크는 컨텍스트 엔지니어링(context engineering)과 하네스 엔지니어링(harness engineering)에 의존합니다.

이 글은 에이전트 기반 소프트웨어 엔지니어링(Agentic Software Engineering) 시리즈의 5편 중 2편입니다. 1편 "감으로 하는 코딩(Vibe Coding)에서 실현 가능한 코딩(Viable Coding)으로: Superpowers가 AI 챗봇을 엔지니어링 파트너로 전환하는 방법"에서는 Superpowers 프레임워크를 심층적으로 다룹니다.

### 저자 소개

릭 하이타워(Rick Hightower)는 포춘 100대 금융 서비스 회사에서 ML/AI 개발을 이끌었던 기술 경영진이자 데이터 엔지니어입니다. 그는 클로드 코드(Claude Code), 제미니(Gemini), 코파일럿(Copilot), 커서(Cursor)를 포함한 30개 이상의 코딩 에이전트를 지원하는 범용 에이전트 스킬 설치기인 skilz를 만들었으며, 세계 최대의 에이전트 스킬 마켓플레이스(agentic skill marketplace)를 공동 설립했습니다. [링크드인(LinkedIn)](https://www.linkedin.com/in/rickhightower) 또는 [미디엄(Medium)](https://medium.com/@rickhightower)에서 릭 하이타워와 연결하세요.

릭은 수년간 생성형 AI 시스템(generative AI systems), 에이전트, 에이전트 기반 워크플로우(agentic workflow)를 적극적으로 개발해 왔습니다. 그는 수많은 에이전트 기반 프레임워크와 개발자 도구의 저자이며, AI 도입을 고려하는 팀에 깊이 있는 실용적 전문 지식을 제공합니다.

클로드 코드(Claude Code)
컨텍스트 엔지니어링(Context Engineering)
AI 하네스 엔지니어링(Ai Harness Engineering)
에이전트 AI(Agentic Ai)
AI 에이전트(AI Agent)