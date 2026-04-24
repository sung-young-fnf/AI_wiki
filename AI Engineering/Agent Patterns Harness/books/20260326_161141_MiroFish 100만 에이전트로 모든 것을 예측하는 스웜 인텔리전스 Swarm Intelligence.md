# 번역 기사

- **원본 URL:** https://agentnativedev.medium.com/mirofish-swarm-intelligence-with-1m-agents-that-can-predict-everything-114296323663
- **번역 일시:** 2026-03-26 16:11:41

---

# MiroFish: 100만 에이전트로 모든 것을 예측하는 스웜 인텔리전스 (Swarm Intelligence)

12분 분량의 읽을거리
·
2026년 3월 16일

---

최근 베이징의 한 20세 학부생이 제가 그동안 다루던 추상화 계층(abstraction layer) 전체를 다시 생각하게 만들었습니다.

그의 이름은 궈 항장(Guo Hangjiang)이며, 그는 10일 만에 미로피시(MiroFish)를 '바이브 코딩(vibe-coded, 직관적으로 빠르게 코딩했다는 의미)'했습니다.

이 프로젝트는 오픈AI(OpenAI), 구글(Google), 마이크로소프트(Microsoft)의 저장소들을 제치고 깃허브(GitHub) 전 세계 트렌드 1위를 차지했습니다.

간략한 데모 공개 24시간 만에, 중국의 전 최고 부자였던 천톈차오(Chen Tianqiao)는 이 프로젝트의 인큐베이팅(incubating)을 위해 410만 달러를 투자했습니다.

브라이언 뢰멜(Brian Roemmele)은 단일 미로피시(MiroFish) 시뮬레이션(simulation)에서 50만 개의 AI 에이전트(agent)를 생성하는 데 성공했으며(이에 대해서는 나중에 자세히 설명합니다), 수천 개의 에이전트로 성공적인 시뮬레이션을 실행하는 다른 사례들도 많습니다.

한 개발자는 미로피시(MiroFish)를 폴리마켓(Polymarket) 트레이딩 봇(trading bot)에 연결하여, 모든 거래 전에 2,847개의 디지털 휴먼(digital human)을 시뮬레이션했고, 338번의 거래에서 4,266달러의 수익을 보고했습니다.

미로피시(MiroFish)는 에이전트들을 조율하여 특정 작업을 완료하게 하는 대신, 고유한 성격, 기억, 사회적 연결을 가진 수천 개의 자율 에이전트(autonomous agent)를 생성하고, 이들을 시뮬레이션된 세계에 투입하여 자발적으로 발생하는 행동(emergent behavior)을 관찰합니다.

미로피시(MiroFish)는 카멜-AI(CAMEL-AI)의 OASIS (Open Agent Social Interaction Simulations) 프레임워크(framework)를 기반으로 하며, 팔로우(following), 댓글(commenting), 리포스팅(reposting), 좋아요(liking), 뮤트(muting), 검색(searching)과 같은 23가지의 다양한 소셜 액션(social action)을 통해 최대 100만 개의 에이전트로 확장(scale)할 수 있습니다.

이 프레임워크는 환경 로직(environment logic), 추천 시스템(recommendation system), 스케줄에 따라 에이전트를 활성화하는 시간 엔진(time engine), 그리고 GPU 전반에 걸쳐 LLM 호출을 분배하는 확장 가능한 추론 계층(scalable inference layer)을 처리합니다.

저는 천톈차오(Chen Tianqiao)의 투자 논리(investment thesis)가 미로피시(MiroFish) 자체에만 초점을 맞춘 것은 아니었다는 점을 강조하고 싶습니다.

그의 논리는 '슈퍼-개인(super-individual)'이라고 부르는 개념, 즉 AI 시대에는 한 사람이 과거에 회사 전체가 필요했던 일을 구축할 수 있다는 생각이었습니다.

궈는 10일 만에 미로피시(MiroFish)를 구축했으며, 오프라인 포크(offline fork)는 단 한 번의 클로드 코드(Claude Code) 세션(session)으로 만들어졌습니다. 폴리마켓(Polymarket) 통합(integration) 또한 단 한 명의 개발자가 구축했습니다.

저는 이것이 엔지니어링 팀 구조에 어떤 의미를 가지는지에 대해 고민해왔으며, 그 해답이 '모든 직원을 해고하고 한 명을 고용하는 것'이라고는 생각하지 않습니다.

해답은 더 미묘합니다. 의미 있는 소프트웨어(software) 산출의 단위는 팀에서 개인으로 이동했지만, 신뢰할 수 있는 소프트웨어 산출의 단위는 그렇지 않습니다.

한 사람이 미로피시(MiroFish)를 구축할 수는 있지만, 이 시스템을 실제 중요한 상황에서 사용하려면 미로피시의 예측이 현실과 상관관계가 있는지 검증하고, 장기적인 신뢰성 테스트(reliability testing), 적대적 평가(adversarial evaluation), 실제 결과(real-world outcomes)에 대한 체계적인 벤치마킹(benchmarking)을 실행하는 것은 한 개인이 할 수 없습니다.

그럼에도 불구하고, 이 기술은 정말 인상적입니다.

이 글에서는 미로피시(MiroFish)의 내부 작동 방식, 에이전트 기반 AI(agentic AI)를 구축하는 모든 이에게 아키텍처(architecture)가 중요한 이유, 실제 실패 모드(failure mode)가 숨어 있는 곳, 그리고 시뮬레이션 기반 소프트웨어(simulation-driven software)의 다음 세대에 이것이 의미하는 바를 정확히 설명해 드릴 것입니다.

에이전트 메모리(agent memory), 환각 전파(hallucination propagation), 또는 다중 에이전트 시스템(multi-agent system)의 비용 경제성(cost economics) 문제로 어려움을 겪으셨다면, 이 프로젝트에 다른 어느 누구도 명확하게 설명하지 못한 구체적인 엔지니어링 교훈(engineering lesson)이 숨겨져 있으니 저와 함께 이 글을 계속 읽어주시길 바랍니다.

## 에이전트 기반 모델링 (Agent-based Modeling)

에이전트 기반 모델링(Agent-based modeling)은 1940년대 폰 노이만(Von Neumann)의 자기 복제 기계(self-reproducing machines)와 셀룰러 오토마타(cellular automata)로 거슬러 올라갑니다.

존 콘웨이(John Conway)의 '생명 게임(Game of Life)'은 단순한 규칙으로부터 발생하는 복잡성(emergent complexity)을 보여주었습니다.

단계별 에이전트 시뮬레이션(agent simulation)을 자동화하는 최초의 프레임워크인 시뮬라(Simula) 프로그래밍 언어(programming language)는 1960년대 중반에 등장했습니다.

수십 년 동안 에이전트 기반 모델(agent-based model)은 역학(epidemiology), 도시 계획(urban planning), 전산 경제학(computational economics) 분야의 주요 도구였습니다.

그렇다면 2026년 3월, 20세 학부생의 깃허브(GitHub) 프로젝트가 베테랑 엔지니어(engineer)들의 주목을 받는 이유는 무엇일까요?

여기에는 세 가지 요인이 작용하고 있습니다.

### (1) 거대 언어 모델(LLM)이 에이전트의 행동을 풍부하게 만들었습니다.

고전적인 에이전트 기반 모델은 규칙 기반 로직(rule-based logic), 즉 미리 정의된 매개변수(parameter)를 가진 'if-then-else' 상태 머신(state machine)을 사용합니다.

에이전트는 이웃의 의견을 수용할지 여부를 결정하는 신뢰 임계값 변수(trust threshold variable)를 가질 수 있습니다.

이는 거시적인 수준의 자발적 패턴(emergent pattern)에는 효과적이지만, 개별 에이전트의 행동은 로봇(robotic)처럼 느껴집니다.

LLM 기반 에이전트(LLM-backed agent)는 맥락(context)을 추론하고, 자연어 게시물을 생성하며, 미묘한 의견을 형성하고, 시뮬레이션된 사회적 상호작용(social interaction)을 통해 입장을 변경할 수 있습니다.

행동 표면 영역(behavioral surface area)이 폭발적으로 확장되었습니다.

### (2) 그래프RAG(GraphRAG)가 지식 기반(knowledge grounding)을 다루기 쉽게 만들었습니다.

미로피시(MiroFish)는 정책 초안, 재무 보고서, 뉴스 기사 등의 문서를 읽고, 모든 개체(entity)와 관계(relationship)를 지식 그래프(knowledge graph)로 추출합니다.

에이전트들은 이 그래프에 기반을 둡니다(grounded).

그들의 성격, 입장, 사회적 연결은 입력(input)의 실제 구조로부터 파생됩니다.

이것은 세계 구축(world-building)에 적용된 그래프RAG(GraphRAG)입니다.

### (3) 시뮬레이션 인프라(simulation infrastructure)가 성숙했습니다.

OASIS는 팔로우(following), 댓글(commenting), 리포스팅(reposting), 좋아요(liking), 뮤트(muting), 검색(searching)과 같은 23가지의 다양한 소셜 액션(social action)을 통해 최대 100만 개의 에이전트로 확장(scale)할 수 있습니다.

이는 환경 로직(environment logic), 추천 시스템(recommendation system), 스케줄에 따라 에이전트를 활성화하는 시간 엔진(time engine), 그리고 GPU 전반에 걸쳐 LLM 호출을 분배하는 확장 가능한 추론 계층(scalable inference layer)을 처리합니다.

2년 전만 해도 이 인프라(infrastructure)를 처음부터 구축하는 것은 여러 팀이 수 분기 동안 진행해야 할 프로젝트였겠지만, 이제는 오픈소스 의존성(open-source dependency)이 되었습니다.

제어 플레인(control plane)과 메모리 플레인(memory plane)은 모든 다중 에이전트 시스템(multi-agent system)에서 가장 중요한 계층(layer) 중 두 가지입니다.

제 전자책 『에이전트 기반 SaaS 패턴(Agentic SaaS Patterns)』에서 저는 7가지 핵심 아키텍처 플레인(architectural plane)을 분석하고, 이러한 에이전트 기반 패턴(agentic pattern)이 실제 구현(implementation)에서 어떻게 작동하는지 보여주는 샘플 저장소(sample repository)를 포함하고 있습니다.

여기에서 다운로드할 수 있습니다: 『2026년을 선도할 에이전트 기반 SaaS 패턴(Agentic SaaS Patterns Winning in 2026)』 – 다른 곳에서는 찾을 수 없는 실제 사례, 아키텍처, 워크플로우(workflow)가 가득합니다.

## 미로피시(MiroFish)의 엔지니어링 해부학 (Engineering Anatomy)

### 문서 수집(Document Ingestion) 및 지식 그래프 구축(Knowledge Graph Construction)

이 시스템은 그래프RAG(GraphRAG)를 사용하여 개체(entity), 관계(relationship) 및 의미론적 클러스터(semantic cluster)를 추출합니다.

원래 구현(original implementation)에서는 이 지식 그래프(knowledge graph)가 젭 클라우드(Zep Cloud)에 있습니다. 오프라인 포크(offline fork)(nikmcfly의 MiroFish-Offline)에서는 로컬 네오포제이(Neo4j) 인스턴스(instance)에서 실행됩니다.

네오포제이(Neo4j)로의 전환은 한 줄의 아키텍처 변경(architectural change)으로 거대한 규제 준수(compliance) 영향을 가집니다.

```
# 원본: 젭 클라우드(Zep Cloud) (데이터가 사용자 기기를 떠납니다)
KNOWLEDGE_GRAPH_PROVIDER=zep_cloud
ZEP_API_KEY=your_key_here
  
# 오프라인 포크: 로컬 네오포제이(Neo4j) (데이터가 사용자 하드웨어에 보관됩니다)
KNOWLEDGE_GRAPH_PROVIDER=neo4j
NEO4J_URI=bolt://localhost:7687
NEO4J_USER=neo4j
NEO4J_PASSWORD=your_local_password
```

### 에이전트 생성(Agent Generation)

지식 그래프(knowledge graph)를 기반으로 시스템은 에이전트 페르소나(agent persona)를 자동으로 생성합니다.

각 에이전트는 다음을 받습니다.

*   고유한 전기(biography)와 성격 유형(personality type)
*   문서의 개체(entity)에서 파생된 뚜렷한 입장 또는 관점
*   장기 기억(long-term memory) (원래는 젭 클라우드(Zep Cloud)를 통해, 오프라인 포크(offline fork)에서는 로컬 네오포제이(Neo4j)를 통해)
*   이들의 상호작용 방식(예: 얼마나 쉽게 설득되는지, 얼마나 자주 게시물을 올리는지, 여론 주도자인지 팔로워(follower)인지)을 제어하는 행동 로직(behavioral logic)

환경 구성 에이전트(Environment Configuration Agent)는 시뮬레이션 매개변수(simulation parameter)를 설정합니다. 예를 들어, 물리 시뮬레이션(physics simulation)을 실행하기 전에 중력(gravity) 및 마찰 계수(friction coefficient)를 정의하는 것과 같습니다.

### 이중 플랫폼 병렬 시뮬레이션(Dual-Platform Parallel Simulation)

미로피시(MiroFish)는 트위터(Twitter)와 유사한 환경과 레딧(Reddit)과 유사한 환경, 두 가지 플랫폼에서 동시에 시뮬레이션을 실행합니다.

서로 다른 플랫폼 역학(platform dynamic)은 동일한 에이전트 집단에서 다른 자발적 행동(emergent behavior)을 생성합니다.

*   트위터(Twitter)와 유사한 환경은 리트윗(retweet) 및 짧은 형식의 게시물(short-form posting)을 통해 빠른 의견 확산(opinion cascading)을 선호합니다.
*   레딧(Reddit)과 유사한 환경은 스레드(threaded) 토론, 추천/비추천 역학(upvote/downvote dynamics), 그리고 더 긴 형식의 참여(longer-form engagement)를 선호합니다.

둘을 병렬(parallel)로 실행하는 것은 본질적으로 모든 시뮬레이션(simulation)에 대해 사회적 역학(social dynamics)에 대한 자연스러운 A/B 테스트(A/B test)를 실행하는 것입니다.

내부적으로 OASIS 프레임워크(framework)는 다음을 처리합니다.

*   **시간 엔진(Time Engine):** 스케줄(schedule) 및 시뮬레이션된 시간 진행(simulated time progression)에 따라 에이전트(agent)를 활성화합니다.
*   **추천 시스템(Recommendation System):** 어떤 콘텐츠(content)가 어떤 에이전트에게 노출될지 결정합니다 (알고리즘 피드(algorithmic feed) 모방).
*   **환경 서버(Environment Server):** 모든 게시물, 댓글, 좋아요, 팔로우(follow) 및 의견 변화를 데이터베이스(database)에 기록합니다.
*   **확장 가능한 추론기(Scalable Inferencer):** 사용 가능한 GPU에 걸쳐 LLM 추론(LLM inference)을 분산하고, 처리량(throughput)을 위해 에이전트 결정(agent decision)을 배치(batch) 처리합니다.

### 신의 시점(God’s Eye View): 변수 주입(Variable Injection)

시뮬레이션(simulation) 도중 언제든지 새로운 변수(variable)를 주입할 수 있습니다.

*   “연준(Fed)이 갑자기 금리를 50bp 인하합니다.”
*   “CEO가 즉시 사임합니다.”
*   “경쟁사가 절반 가격에 경쟁 제품을 출시합니다.”

시뮬레이션된 세계 전체가 실시간(real time)으로 재조정됩니다.

에이전트(agent)들은 반응하고, 새로운 의견을 형성하며, 동맹을 변경하고, 새로운 토론 스레드(discussion thread)를 생성합니다.

당신은 현실에서는 문자 그대로 실행할 수 없는 통제된 실험(controlled experiment)을 보고 있는 것입니다. 트위터(Twitter)가 어떻게 반응하는지 보기 위해 실제 세계에 연준(Fed) 금리 인하를 주입할 수는 없습니다.

그러면 예측(prediction)은 다음과 같은 다양한 통찰(insight)을 제공합니다.

*   1분기 마이너스 마감 (에이전트의 79%)
*   3월 전 사상 최고치(ATH) (에이전트의 4%)
*   6,400달러 미만으로 하락 (에이전트의 34%)
*   6,500~6,600달러 마감 (에이전트의 17%)

어떤 의미인지 아실 것입니다.

### 보고서 생성(Report Generation)

전용 리포트 에이전트(ReportAgent)가 시뮬레이션 결과(simulation outcome)를 분석하고 사람이 읽을 수 있는 예측(forecast)을 컴파일(compile)합니다.

또한 시뮬레이션된 세계에 직접 들어가 개별 에이전트에게 그들의 추론(reasoning)에 대해 질의하고, 리포트 에이전트(ReportAgent)에게 더 깊은 분석을 요청하거나, 자발적으로 발생한 특정 대화 스레드(conversation thread)를 조사할 수 있습니다.

미로피시(MiroFish)의 스택(stack)은 다음과 같습니다.

## 메모리 문제 (The Memory Problem)

브라이언 뢰멜(Brian Roemmele)은 단일 미로피시(MiroFish) 시뮬레이션(simulation)에서 50만 개의 AI 에이전트(agent)를 생성했습니다.

이는 확장 벤치마크(scaling benchmark)로서 인상적이지만, 저는 실제 엔지니어링 문제인 단기 기억 오염(short-term memory contamination)에 대해 경고해야 합니다.

다중 에이전트 시스템(multi-agent system)을 구축해 본 적이 있다면, 다음 내용은 고통스러울 정도로 익숙하게 들릴 것입니다.

*   에이전트 A가 생성 중 사실을 환각합니다.
*   에이전트 A가 이 환각된 사실을 시뮬레이션된 소셜 플랫폼(social platform)에 게시합니다.
*   에이전트 B, C, D는 이 게시물을 읽고 자신의 기억에 통합합니다.
*   이들은 자신의 게시물과 댓글에서 이를 공유합니다.
*   50번의 시뮬레이션 라운드(simulation round) 후, 전체 집단은 존재하지 않았던 정보에 기반하여 의사결정을 내립니다.

이것은 다중 에이전트 토론 시스템(multi-agent debate system)에서 온 용어인 '우즐 효과(Woozle Effect)'가 시뮬레이션 규모(simulation scale)로 적용된 것입니다.

다중 에이전트 토론에서, 각 라운드마다 10~20% 이상의 에이전트가 토론을 통해 오도되며, 초기 환각 수준(hallucination level)이 증가함에 따라 이 비율은 지속적으로 증가합니다.

이 효과는 기하급수적으로 증폭됩니다.

시뮬레이션 결과(simulation output)를 분석할 때쯤이면, 자발적 합의(emergent consensus) (측정하려는 것)와 환각 연쇄(hallucination cascade) (측정값을 손상시키는 것)를 구별할 수 없게 됩니다.

핵심적인 딜레마: 다중 에이전트 시뮬레이션을 강력하게 만드는 메커니즘, 즉 상호작용을 통해 에이전트들이 서로의 믿음에 영향을 미치는 메커니즘이 환각을 전파하는 바로 그 메커니즘입니다. 하나를 가지면서 다른 하나의 위험을 감수하지 않을 수는 없습니다.

이것은 미로피시(MiroFish)만의 고유한 문제가 아닙니다.

이는 에이전트가 정보를 공유하는 모든 다중 에이전트 시스템(multi-agent system)에서 근본적인 아키텍처적 과제(architectural challenge)이지만, 의견 수렴(opinion convergence)과 환각 수렴(hallucination convergence)이 겉보기에는 동일하게 보이기 때문에 의견 역학(opinion dynamics)을 모델링하도록 설계된 시뮬레이션 시스템에서는 특히 심각합니다.

### 환각 전파(Hallucination Propagation)

에이전트가 비판적이고 자가 수정(self-correcting)하도록 설계되었을 때조차도, 한 에이전트의 환각된 콘텐츠(hallucinated content)는 토론을 통해 다른 에이전트에게 확실히 전파됩니다.

맥락 인지 환각 분석 프레임워크(context-aware hallucination analysis framework), 토큰(token) 수준 방해 시퀀스 감지(disruption sequence detection), 의미론적 추론 기반 완화(semantic reasoning-based mitigation)와 같은 제안된 완화책들은 모두 계산 오버헤드(computational overhead)와 복잡성(complexity)을 증가시킵니다.

미로피시(MiroFish) 규모의 시뮬레이션(수천 개의 에이전트, 수백 라운드)의 경우, 모든 에이전트 상호작용(agent interaction)에 대해 토큰(token)별 환각 감지(hallucination detection)를 구현하는 것은 엄청나게 비용이 많이 들 수 있습니다.

실용적인 질문은 “시뮬레이션의 합의가 진정한 자발적 역학(genuine emergent dynamics)이 아닌 환각 연쇄(hallucination cascade)에 의해 주도되었을 때 이를 감지할 수 있는가?”입니다.

저는 이에 대한 명확한 답을 가지고 있지 않으며 아직 아무도 가지고 있지 않지만, 이것이 다중 에이전트 시뮬레이션 엔지니어링(multi-agent simulation engineering)에서 가장 중요하고 아직 해결되지 않은 문제라고 주장하고 싶습니다.

## 오프라인 포크(Offline Fork) — 생각보다 더 중요한 이유

미로피시(MiroFish) 오프라인(offline) 버전은 단 한 번의 클로드 코드(Claude Code) 세션(session)에서 두 가지 중요한 변경 사항을 만들었습니다.

*   젭 클라우드(Zep Cloud)를 로컬 네오포제이(Neo4j)로 대체: 모든 지식 그래프(knowledge graph) 작업이 사용자 하드웨어(hardware)에서 실행됩니다.
*   20개 파일을 중국어에서 영어로 번역: 전체 UI(User Interface)와 코드베이스(codebase)가 이제 중국어 비원어민도 접근할 수 있습니다.

클라우드(cloud) 의존성(dependency)이 없으며 사용자의 문서가 네트워크를 벗어나지 않습니다.

PR 위기 테스트를 위한 시뮬레이션 엔진(simulation engine)을 구축할 때(예: 회사 보도 자료를 게시하기 전에 수천 명의 사람이 어떻게 반응할지 시뮬레이션하는 경우), 시스템에 제공하는 문서는 정의상 기밀입니다.

아직 공개되지 않았으며, 시뮬레이션의 전체 가치는 정보가 공개되기 전에 실행하는 데 달려 있습니다.

이를 클라우드 API(cloud API)를 통해 실행하는 것은 데이터 상주 문제(data residency problem)를 야기합니다.

클라우드 공급자(cloud provider)가 얼마나 신뢰할 수 있는지는 중요하지 않습니다.

위협 모델(threat model)에는 다음이 포함됩니다.

*   문서 내용을 캡처하는 API 로그(API log)
*   저장된 데이터에 접근할 수 있는 클라우드 공급자 직원
*   데이터가 처리되는 위치에 대한 규제 요구 사항(regulatory requirements)
*   보도 자료 내용이 유출될 경우의 경쟁 정보 위험(competitive intelligence risk)

로컬 네오포제이(Neo4j)는 이러한 전체 위협 표면(threat surface)을 제거하며, 비교적 깔끔한 교체(단 한 번의 클로드 코드 세션)였다는 사실은 원래 아키텍처(architecture)가 얼마나 잘 모듈화(modularized)되었는지에 대해 시사하는 바가 큽니다.

하지만 로컬(local)로 실행하는 것이 비용이 들지 않는 것은 아닙니다. 올라마(Ollama)를 통한 소규모 오픈소스 모델(open-source model) (CPU 전용)은 성능 저하(performance hit)를 겪을 것이기 때문입니다.

시뮬레이션(simulation)의 정확도(accuracy)는 인간 행동에 대한 미묘한 추론(nuanced reasoning)을 위한 LLM의 역량에 달려 있으며, 7B 매개변수(parameter) 모델은 GPT-5 또는 오퍼스(Opus)와 동일한 행동적 풍부함(behavioral richness)을 생성하지 않을 것입니다.

하지만 올라마(Ollama)를 통한 14B-70B 매개변수 모델은 다중 에이전트 시나리오(multi-agent scenario)를 적절히 처리하며, 시뮬레이션 파이프라인(simulation pipeline) 설계는 원시 모델(raw model) 성능만큼 중요합니다.

게다가 오프라인 아키텍처(offline architecture)는 로컬 모델(local model)에만 국한되지 않습니다. 원한다면 원격 API(remote API)를 가리킬 수도 있습니다.

오프라인(offline)은 옵션(option)이지, 제한(restriction)이 아닙니다.

```
# 올라마(Ollama)를 통한 로컬 모델(local model)
LLM_PROVIDER=ollama
LLM_MODEL=llama3:70b
LLM_BASE_URL=http://localhost:11434/v1
  
# 또는 원격 API(remote API) - 선택 사항
LLM_PROVIDER=openai
LLM_MODEL=gpt-4o
LLM_API_KEY=your_key_here
```

배포 토폴로지(deployment topology)는 아키텍처적 제약(architectural constraint)이 아닌 구성 결정(configuration decision)입니다.

## 미로피시(MiroFish)로부터 얻은 엔지니어링 통찰 (Engineering Insights)

다음은 여러분의 작업에 적용할 수 있는 몇 가지 패턴(pattern)입니다.

### 1. 생성적 기질(generative substrates)로서의 지식 그래프(knowledge graph)

세계 구축을 위한 그래프RAG(GraphRAG) 패턴(pattern)은 진정하며 이전 가능합니다. 개체 추출 파이프라인(entity extraction pipeline)이 있다면 시뮬레이션 시스템(simulation system)의 기반을 갖춘 것입니다.

“검색을 위한 지식 그래프”에서 “에이전트 생성을 위한 지식 그래프”로 전환하는 데 필요한 한계 엔지니어링 노력(marginal engineering effort)은 보이는 것보다 적습니다.

그래프RAG(GraphRAG) 시스템(system)을 구축할 때, 검색(retrieval) 및 생성적 사용 사례(generative use case)를 모두 고려하여 개체(entity) 및 관계 스키마(relationship schema)를 설계할 수 있습니다.

챗봇(chatbot)의 맥락 창(context window)을 구동하는 동일한 그래프가 시뮬레이션의 사회적 구조(social structure)를 구동할 수 있습니다.

### 2. 메모리 아키텍처(Memory architecture)가 병목 현상(bottleneck)입니다.

다중 에이전트 시뮬레이션(multi-agent simulation)에서의 환각 전파(hallucination propagation) 문제는 근본적으로 메모리 아키텍처(memory architecture) 문제입니다.

에이전트(agent)들이 사회적 환경을 공유하고 서로의 결과물을 읽을 수 있을 때, 모든 환각은 다른 모든 에이전트에게 잠재적인 영구 기억 항목(persistent memory entry)이 됩니다.

메모리 출처 추적(memory provenance tracking)을 구현할 수 있습니다.

에이전트의 기억 속에 있는 모든 사실은 그 출처에 대한 메타데이터(metadata)를 포함해야 합니다. 즉, 원본 문서에서 추출되었는지, 에이전트 자체에 의해 생성되었는지, 아니면 다른 에이전트의 결과물에서 흡수되었는지 여부입니다.

이를 통해 시뮬레이션의 합의가 기반 지식(grounded knowledge)으로 거슬러 올라가는지, 아니면 환각 연쇄(hallucination cascade)로 인한 것인지 사후 분석(post-hoc analysis)이 가능해집니다.

### 3. 일류 디자인 패턴(design pattern)으로서의 이중 환경 테스트(Dual-environment testing)

동일한 에이전트(agent) 집단을 두 가지 다른 환경 구성(트위터 유사 vs. 레딧 유사)에서 실행하는 것은 본질적으로 모든 시뮬레이션(simulation)에 대해 자연 실험(natural experiment)을 실행하는 것입니다. 이는 구현하기 저렴하며 각 시뮬레이션 실행의 분석 가치(analytical value)를 극적으로 높입니다.

어떤 다중 에이전트 시스템(multi-agent system)을 구축하든, 동일한 에이전트 집단을 최소 두 가지 다른 상호작용 토폴로지(interaction topology)를 통해 실행하고 그 결과를 비교할 수 있습니다.

상반된 결과는 어떤 발견이 견고하고 어떤 것이 특정 상호작용 구조(interaction structure)의 인공물(artifact)인지 드러냅니다.

### 4. 비용 곡선(cost curve)이 사용 사례(use case)를 결정합니다.

수천 개의 LLM 기반 에이전트(LLM-backed agent)를 수백 라운드 동안 실행하는 것은 비용이 많이 듭니다.

각 에이전트 결정은 최소 한 번의 LLM 호출(LLM call)을 필요로 합니다. GPT-4o 가격으로도 1,000개의 에이전트를 100 라운드 동안 시뮬레이션하는 데는 실행당 수백 달러의 비용이 들 수 있습니다.

핵심은 비용 인지형 에이전트 스케줄링(cost-aware agent scheduling)으로 시뮬레이션 파이프라인(simulation pipeline)을 설계하는 것입니다. 즉, 모든 에이전트가 모든 라운드에서 행동할 필요는 없으며, 모든 결정에 전체 LLM 호출이 필요한 것은 아닙니다. 일부는 규칙 기반(rule-based)으로 처리하고 LLM 호출은 모호한 상황을 위해 남겨둘 수 있습니다.

비용 효율적인 대규모 시뮬레이션(cost-viable simulation at scale)을 위해서는 하이브리드 규칙 기반(hybrid rule-based) / LLM 기반 에이전트 아키텍처(LLM-backed agent architecture)가 필요할 가능성이 높습니다.

## 열정과 한계 (Enthusiasm and Limitations)

미로피시(MiroFish)는 에이전트 기반 AI(agentic AI) 커뮤니티(community)에 중요한 아키텍처 패턴(architectural pattern)을 제시합니다.

그래프RAG(GraphRAG)에서 시뮬레이션(simulation)으로 이어지는 파이프라인(pipeline), 이중 플랫폼 테스트(dual-platform testing) 접근 방식, 에이전트 로직(agent logic)과 인프라(infrastructure)의 깔끔한 분리는 다른 시뮬레이션 기반 프로젝트(simulation based project)에서도 복제될 것으로 예상되는 건전한 엔지니어링 결정(engineering decision)입니다.

그렇긴 하지만, 저는 한계점(limitation)에 대해서도 솔직하게 이야기하고 싶습니다.

*   **실제 결과(real-world outcome)에 대한 공개된 벤치마크(benchmark)가 없습니다.** 미로피시(MiroFish)의 예측이 특정 사용 사례(use case)에서 무작위(random)보다 나은지 알 수 없습니다. 시간만이 알려줄 것입니다.
*   **환각 전파(hallucination propagation)는 미해결 문제입니다.** 수천 개의 에이전트가 수백 라운드에 걸쳐 정보를 교환하는 시뮬레이션 규모에서는 환각 연쇄(hallucination cascade)가 알려진 비용 효율적인 해결책(cost-effective solution)이 없는 최우선 문제(first-order problem)입니다.
*   **LLM 편향(LLM bias)은 시뮬레이션 편향(simulation bias)이 됩니다.** 기본 모델(underlying model)이 인간 행동에 대한 이해에 체계적인 공백(systematic gap)을 가지고 있다면, 시뮬레이션은 그 공백을 물려받게 되고, 시뮬레이션의 규모는 그 공백이 포괄적인 범위인 것처럼 느끼게 합니다.
*   **대규모 비용은 대부분의 사용 사례(use case)에 대해 감당하기 어렵습니다.** 수천 개의 완전한 LLM 기반 에이전트(full LLM-backed agent)를 실행하는 것은 비용이 많이 듭니다. 추론 비용(inference cost)이 크게 감소하거나 하이브리드 아키텍처(hybrid architecture)가 표준이 될 때까지, 이는 시뮬레이션 실행당 비용이 의사결정 가치(decision value)에 의해 정당화되는 고가치 시나리오(high-value scenario)로의 실제 배포(practical deployment)를 제한합니다.
*   **시뮬레이션된 인간은 실제 인간이 아닙니다.** 이것은 분명하게 들리지만, 명확히 언급할 가치가 있습니다. 에이전트 페르소나(agent persona)가 얼마나 정교하고, 메모리 아키텍처(memory architecture)가 얼마나 풍부하며, 사회적 역학(social dynamics)이 얼마나 미묘하든 관계없이, 에이전트들은 인간처럼 들리는 텍스트를 생성하는 것이지 인간적인 추론(reasoning)을 하는 것이 아닙니다. 지도는 영토가 아닙니다.

저는 앞으로 몇 달 동안 제 작업에서 이러한 질문들 중 일부를 탐구할 것입니다.

이 분야에서 개발하고 계시다면, 댓글로 의견을 듣고 싶습니다.

## 추가 기사 (Bonus Articles)

*   [클로드/코덱스(Claude/Codex)를 대체할 7가지 로컬 LLM 패밀리(일상 업무용)](agentnativedev.medium.com)
    *   일상 업무에서 놀라운 수준의 실제 성능을 제공하는 로컬 실행 가능한 오픈소스 모델 패밀리…

*   [오픈팽(OpenFang)이 나오기 전까지 30개 이상의 오픈클로(OpenClaw) 대안을 무시했던 이유](agentnativedev.medium.com)
    *   완전한 오픈소스 에이전트 운영 체제, 전적으로 러스트(Rust)로 작성되었으며 단일 32MB 바이너리(binary)로 180ms…

*   [게리 탄(Garry Tan)의 gstack: 엔지니어링 팀처럼 클로드(Claude)를 운영하기](agentnativedev.medium.com)
    *   클로드 코드(Claude Code)에 설치하는 8가지 독자적인 슬래시 명령, 각각 고유한 시스템 프롬프트(system prompt)와 우선순위를 가짐.

*   [Qwen 3.5 35B-A3B: 800달러짜리 GPU가 프론티어 클래스 AI 워크스테이션(AI Workstation)이 된 이유](agentnativedev.medium.com)
    *   저는 꽤 오랫동안 로컬 모델을 실행해 왔고, 그 한계가 어디쯤인지 잘 알고 있다고 생각했지만…

*   [GET SH\*T DONE: 클로드 코드(Claude Code) 및 코덱스(Codex)를 위한 메타 프롬프팅(Meta-prompting) 및 스펙 기반 개발(Spec-driven Development)](agentnativedev.medium.com)
    *   GSD(“Get Shit Done”)는 모델의 맥락 창(context window)이 가득 차면서 발생하는 품질 저하(context rot) 문제를 해결하는 것을 목표로 합니다.

*   [잊지 않는 오픈클로(OpenClaw) 메모리 시스템(Memory Systems): QMD, Mem0, Cognee, Obsidian](agentnativedev.medium.com)
    *   에이전트가 이전에 지시한 결정을 무작위로 무시한 적이 있다면… 그것은 무작위가 아닙니다.

AI 에이전트
에이전트 기반 AI
세계 시뮬레이션
예측 시장
다중 에이전트 시뮬레이션

---

작성자: Agent Native
팔로워 7.9K명
·
팔로우 0명

하이퍼스케일러(Hyperscalers), 오픈소스(open-source) 개발, 스타트업 활동 및 에이전트 기반 AI(agentic AI)를 형성하는 신흥 기업 패턴.

댓글 (12)
BongBongOogle

의견이 어떠신가요?

취소
답글 달기
모든 답글 보기

도움말

상태

정보

채용

언론

블로그

개인정보처리방침

규칙

약관

텍스트 음성 변환