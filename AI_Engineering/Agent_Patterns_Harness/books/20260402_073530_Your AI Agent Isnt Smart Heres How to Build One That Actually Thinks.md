# 번역 기사

- **원본 URL:** https://ai.plainenglish.io/i-built-an-ai-agent-that-plans-its-own-research-and-you-can-too-fea74fe7fb83
- **번역 일시:** 2026-04-02 07:35:30

---

# Your AI Agent Isn’t Smart. Here’s How to Build One That Actually Thinks

대부분의 AI 에이전트는 실질적인 지능을 갖추기보다는 겉모습만 번지르르한 프롬프트 체인(Prompt Chain)에 불과한 경우가 많습니다. 시스템 프롬프트를 작성하고 몇 가지 도구를 연결한 뒤 ReAct 루프를 추가하면 데모에서는 잘 작동하지만, 예상치 못한 오류가 발생하거나 데이터가 복잡해지면 금세 무너지고 맙니다.

이러한 한계를 극복하기 위해, 도구를 호출하기 전 전체 조사 계획을 수립하고, 단계별로 실행하며, 중간 결과가 예상과 다를 때 스스로 계획을 수정하는 '계획 수립 에이전트(Planning Agent)'를 구축하는 방법을 소개합니다.

### "ReAct" 방식의 문제점

ReAct(Reason-Act-Observe, 추론-행동-관찰)는 가장 일반적인 AI 에이전트 패턴입니다. 하지만 이 방식은 한 번에 한 단계만 생각하는 '탐욕적(Greedy)' 방식이라는 근본적인 결함이 있습니다.

예를 들어, 여러 주식의 지표를 비교해 달라는 요청을 받으면 ReAct 에이전트는 첫 번째 주식의 지표를 가져오는 데만 집중하다가 정작 중요한 '비교' 단계를 놓치기 일쑤입니다. 이는 모델의 성능 문제가 아니라 아키텍처의 문제입니다. 생각하는 작업과 실행하는 작업을 분리하지 않으면, 단일 추론 과정(Inference pass)이 너무 많은 역할을 수행하게 되어 집중력이 분산됩니다.

### 아키텍처: 4개의 노드와 1개의 루프

계획 수립 에이전트는 네 개의 노드로 구성된 유향 그래프(Directed Graph) 구조를 가집니다.

1.  **플래너(Planner):** 사용자의 쿼리를 분석해 구조화된 단계별 조사 계획을 생성합니다.
2.  **실행기(Executor):** 계획의 다음 단계를 가져와 지정된 도구를 호출하고 결과를 기록합니다.
3.  **재계획기(Replanner):** 각 실행 후 지금까지의 결과를 검토합니다. 오류가 발생하거나 예상 밖의 데이터가 나오면 남은 계획을 수정하고, 문제가 없으면 다음 단계를 진행합니다.
4.  **보고자(Reporter):** 모든 기록을 종합하여 최종 분석 보고서를 작성합니다.

이 구조의 핵심은 **실행기-재계획기 루프**입니다. 매 행동마다 계획을 재검토할 기회를 가짐으로써 에이전트는 자가 수정(Self-correction) 능력을 갖게 됩니다.

### 상태 관리(State Management): 시스템의 중추

랭그래프(LangGraph)에서 상태(State)는 노드 사이를 흐르는 공유 메모리입니다.

```python
class StrategyState(TypedDict, total=False):
    query: str # 사용자의 원본 요청
    plan: list[dict] # 정렬된 단계 목록
    scratchpad: Annotated[list[dict], operator.add] # 도구 실행 결과 축적
    current_step: int # 현재 진행 단계
    final_report: str # 최종 보고서
    replan_count: int # 무한 루프 방지를 위한 재계획 횟수 제한
```

`scratchpad` 필드에 `operator.add`를 사용하여 리스트를 덮어쓰지 않고 새로운 결과를 계속 추가하도록 설정한 것이 중요합니다. 이를 통해 각 노드는 이전 데이터를 신경 쓰지 않고 자신의 결과만 반환하면 됩니다.

### 플래너 노드: 행동하기 전에 생각하기

플래너는 자유 형식의 텍스트가 아닌, 구조화된 출력(Structured Output)을 반환합니다. Pydantic 모델을 사용하여 LLM이 파싱 가능한 계획을 생성하도록 강제합니다.

프롬프트 구성 시 두 가지 핵심 기법을 사용했습니다:
1.  **매개변수 값 명시:** LLM이 존재하지 않는 티커(Ticker)나 지표(Metric)를 추측하지 않도록 유효한 값의 목록을 제공합니다. 이는 환각(Hallucination)을 방지하는 가장 효과적인 방법입니다.
2.  **도구 제한:** 사용할 수 있는 도구 이외의 가상의 도구를 계획에 넣지 않도록 명확히 지시합니다.

### 실행기 노드: 한 번에 한 단계씩

실행기는 가장 단순한 노드입니다. 계획된 단계를 도구 레지스트리(Tool Registry)를 통해 실행하고 결과를 기록합니다.

여기에는 `MAX_STEPS`라는 안전장치가 있어, 재계획기가 계속 단계를 추가하더라도 무한히 API 비용이 발생하는 것을 방지합니다. 또한 도구 호출 중 발생하는 예외를 잡아 "Error:" 문자열로 반환함으로써 재계획기가 이를 인지하고 대처할 수 있게 합니다.

### 재계획기 노드: 자가 수정의 핵심

재계획기는 매 실행 후 현재까지의 진행 상황을 검토합니다.

```python
# 재계획기 결정 스키마
class ReplanDecision(BaseModel):
    reasoning: str # 진행 상황 분석 이유
    should_replan: bool # 계획 수정 여부
    updated_steps: list[PlanStep] # 수정된 남은 단계들
```

재계획기는 `scratchpad`의 내용을 요약하여 검토하며, 예상된 도구가 실패했거나 새로운 사실이 발견되었을 때 남은 계획을 동적으로 수정합니다. `MAX_REPLANS`를 2회 정도로 제한하여 분석 마비(Analysis paralysis)에 빠지지 않고 결론을 낼 수 있도록 유도합니다.

### 그래프 연결: LangGraph 조립

네 개의 노드를 연결하여 워크플로우를 완성합니다.

```python
workflow = StateGraph(StrategyState)
workflow.add_node("planner", planner_node)
workflow.add_node("executor", executor_node)
workflow.add_node("replanner", replanner_node)
workflow.add_node("report", report_node)

workflow.set_entry_point("planner")
workflow.add_edge("planner", "executor")
workflow.add_edge("executor", "replanner")
workflow.add_conditional_edges(
    "replanner",
    should_continue_execution,
    {"executor": "executor", "report": "report"}
)
workflow.add_edge("report", END)
```

이 그래프는 실행기-재계획기 사이의 조건부 엣지(Conditional edge)를 통해 모든 단계가 완료될 때까지 루프를 돌며, 완료 시 보고서 노드로 이동합니다.

### 향후 발전 방향

현재 시스템은 가상 데이터를 사용하지만, 아키텍처는 즉시 실무에 적용 가능한 수준입니다.

1.  **실제 금융 데이터 연동:** Yahoo Finance 또는 Alpha Vantage와 같은 실제 API로 교체할 수 있습니다.
2.  **영구 메모리(Persistent Memory):** 벡터 스토어(Vector Store)를 추가하여 이전 분석 결과를 참고하게 함으로써 에이전트를 진화하는 분석가로 만들 수 있습니다.
3.  **인간 개입(Human-in-the-loop):** 실행 전 인간이 계획을 승인하는 단계를 추가하여 안전성을 높일 수 있습니다.

### 결론

계획-실행-재계획 루프는 에이전트의 안정성을 획기적으로 높여줍니다. 구조화된 출력과 도구 레지스트리, 안전 제한 장치 등의 패턴은 이제 명확히 정립되었습니다.

하지만 여전히 시스템의 지능은 기저에 깔린 LLM의 품질에 달려 있습니다. 이 그래프 아키텍처는 모델을 더 똑똑하게 만드는 것이 아니라, 모델이 가진 지능을 가장 효과적으로 발휘할 수 있는 **프레임워크**를 제공하는 것입니다. 프로젝트 관리 계층은 해결되었지만, 데이터의 이상 유무를 판단하는 '판단력'의 영역은 여전히 우리가 프롬프트를 통해 정교하게 다듬어 나가야 할 과제입니다.