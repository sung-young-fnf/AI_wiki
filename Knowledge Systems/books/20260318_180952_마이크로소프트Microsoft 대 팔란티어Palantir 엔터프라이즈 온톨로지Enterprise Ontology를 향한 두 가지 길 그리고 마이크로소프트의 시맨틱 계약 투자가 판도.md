# 번역 기사

- **원본 URL:** https://pub.towardsai.net/microsoft-vs-palantir-two-paths-to-enterprise-ontology-and-why-microsofts-bet-on-semantic-6e72265dce21
- **번역 일시:** 2026-03-18 18:09:52

---

# 마이크로소프트(Microsoft) 대 팔란티어(Palantir): 엔터프라이즈 온톨로지(Enterprise Ontology)를 향한 두 가지 길 (그리고 마이크로소프트의 시맨틱 계약 투자가 판도를 바꾸는 이유)

마이크로소프트 패브릭 IQ(Microsoft Fabric IQ)가 온톨로지를 실제로 어떻게 구현하는지, 그리고 팔란티어(Palantir)의 접근 방식과 근본적으로 다른 이유에 대한 기술 심층 분석

🚀 **신규**: 48시간 만에 온토가드(OntoGuard)를 구축했습니다. 온토가드는 460만 달러 규모의 실수를 방지하는 AI 에이전트(AI agents)용 온톨로지 방화벽(firewall)입니다. 구축 사례 보기 →

[OntoGuard: 커서 AI(Cursor AI)를 사용하여 48시간 만에 AI 에이전트용 온톨로지 방화벽을 구축하다](https://medium.com/@pankajk/ontoguard-i-built-an-ontology-firewall-for-ai-agents-in-48-hours-using-cursor-ai-b3f021799276)

[모든 것을 바꾼 460만 달러 규모의 실수](https://medium.com/@pankajk/the-4-6m-mistake-that-changed-everything-c9e3e7f45c2f)

## 이 기사를 촉발시킨 질문

마이크로소프트 이그나이트 2025(Microsoft Ignite 2025) 발표에 대한 제 분석을 게시한 후, 독자 중 한 분이 모든 엔터프라이즈 아키텍트(enterprise architect)의 마음속에 있던 질문을 던졌습니다.

"팔란티어(Palantir)는 꽤 오랫동안 자사 제품에 온톨로지를 사용해 왔습니다. 팔란티어의 접근 방식과 마이크로소프트가 제공하는 것의 차이점을 어떻게 설명할 수 있습니까?"

이는 정확히 물어야 할 질문입니다. 그리고 그 답은 엔터프라이즈 AI(Enterprise AI)가 나아갈 방향에 대한 흥미로운 통찰을 제공합니다.

요약하자면, 팔란티어는 정보 분석 작업(그래프 데이터(graph data) + 인간 추론(human reasoning))을 위한 온톨로지를 구축했습니다. 반면, 마이크로소프트는 에이전트 시스템(agentic systems)(시맨틱 계약(semantic contracts) + 자율적 행동(autonomous action))을 위해 온톨로지를 구축했습니다. 동일한 기술이지만, 구현 철학은 근본적으로 다릅니다.

마이크로소프트가 이를 정확히 어떻게 수행하고 있으며, 왜 이것이 중요한지 보여드리겠습니다.

## 1부: 마이크로소프트(Microsoft)가 패브릭 IQ(Fabric IQ)에서 온톨로지를 실제로 구현하는 방법

### 아키텍처(Architecture): 세 개의 계층, 하나의 시맨틱 기반(Semantic Foundation)

마이크로소프트의 구현은 단순히 "온톨로지 지원을 추가했다"는 것을 넘어섭니다. 이는 비즈니스 컨텍스트(business context)가 엔터프라이즈 AI 스택(stack)을 통해 흐르는 방식에 대한 완전한 재구상입니다.

#### 계층 1: 온톨로지 아이템(Ontology Item) (기반)

핵심에는 마이크로소프트가 온톨로지 아이템(Ontology Item)이라고 부르는 것이 있습니다. 이는 데이터 레이크하우스(data lakehouse), 파워 BI(Power BI) 모델, 그리고 데이터 파이프라인(data pipelines)과 함께 존재하는 최상위 패브릭(Fabric) 객체입니다.

이는 데이터에 대한 메타데이터(metadata)가 아닙니다. 이는 다음을 정의하는 정형 시맨틱 모델(formal semantic model)입니다.

*   **엔티티 유형(Entity Types)** — 단순히 테이블 스키마(table schemas)가 아닌 비즈니스 개념:
    *   **고객(Customer)**
        *   속성(Properties): 고객 ID(CustomerID), 등급(Tier), 위험 점수(RiskScore), 평생 가치(LifetimeValue)
        *   제약 조건(Constraints): 위험 점수(RiskScore)는 0-100이어야 함
        *   관계(Relationships): 여러 주문(Orders)을 가짐, 지역(Region)에 속함
*   **유형화된 관계(Typed Relationships)** — 단순히 외래 키(foreign keys)가 아닌 시맨틱 링크(semantic links):
    *   선적(Shipment) --[속함]--> 고객(Customer)
    *   선적(Shipment) --[포함]--> 라인 아이템(LineItem)
    *   선적(Shipment) --[모니터링됨]--> IoT 센서(IoTSensor)
    *   선적(Shipment) --[규정됨]--> 규정 준수 규칙(ComplianceRule)
*   **비즈니스 규칙(Business Rules)** — 유효한 상태를 정의하는 실행 가능한 로직(logic):
    *   규칙(Rule): "고위험 선적 경보(High-Risk Shipment Alert)"
        *   만약(IF): 선적(Shipment).위험 점수(RiskScore) > 80
        *   그리고(AND): 선적(Shipment).상태(Status) = "운송 중(In Transit)"
        *   그리고(AND): IoT 센서(IoTSensor).온도(Temperature) > 임계값(Threshold)
        *   그러면(THEN): 작업 트리거(Trigger_Action)(고객 알림(Notify_Customer), 운영팀에 에스컬레이션(Escalate_to_Operations))
*   **허용된 작업(Permitted Actions)** — 에이전트(agents)가 수행하도록 허용된 작업:
    *   작업(Action): 선적 경로 변경(Reroute_Shipment)
        *   필요 조건(Requires): 운영 관리자 역할(Operations_Manager_Role) 또는 시스템 비상 재정의(System_Emergency_Override)
        *   유효성 검사(Validates): 목적지(Destination)는 유효한 창고(Warehouse)여야 함
        *   트리거(Triggers): 선적 상태 업데이트(Update_Shipment_Status), 감사 추적 로그(Log_Audit_Trail)

#### 계층 2: 원레이크(OneLake)와의 시맨틱 통합(Semantic Integration)

이 부분이 기술적으로 흥미로운 부분입니다. 온톨로지는 단순히 문서로 존재하는 것이 아니라, 원레이크(OneLake)의 데이터와 적극적으로 연결됩니다.

마이크로소프트는 시맨틱 바인딩(semantic bindings)이라고 부르는 것을 사용합니다.

*   엔티티(Entity): 고객(Customer)
    *   온톨로지 정의(Ontology Definition): [고객이 누구인지에 대한 비즈니스 개념]
    *   데이터 바인딩(Data Binding): 원레이크(OneLake) 내 customers_table
    *   매핑(Mapping):
        *   Customer.CustomerID → customers.customer_id
        *   Customer.Tier → CASE WHEN annual_revenue > 1M THEN 'Enterprise'...
        *   Customer.RiskScore → ml_models.risk_predictions.score

온톨로지는 물리적 데이터 위에 시맨틱 추상화 계층(semantic abstraction layer)이 됩니다. AI 에이전트(AI agent)가 “고위험 엔터프라이즈 고객은 누구인가?”라고 물을 때, SQL을 작성하는 것이 아니라 온톨로지 수준에서 추론하는 것입니다.

#### 계층 3: 시맨틱 계약(Semantic Contracts)을 통한 에이전트 통합(Agent Integration)

이것이 마이크로소프트의 핵심 혁신입니다. 에이전트(agents)는 데이터를 쿼리(query)하는 것이 아니라 온톨로지를 쿼리합니다.

패브릭(Fabric) 데이터 에이전트(data agent)를 구축할 때, 다음을 제공할 필요가 없습니다.

*   데이터베이스 연결 문자열(Database connection strings)
*   스키마 문서화(Schema documentation)
*   예제 쿼리(Example queries)

대신 시맨틱 권한을 부여합니다.

*   에이전트(Agent): "공급망 모니터(Supply Chain Monitor)"
    *   읽기 가능(Can Read): 선적(Shipment), 고객(Customer), IoT 센서(IoTSensor), 규정 준수 규칙(ComplianceRule)
    *   쓰기 가능(Can Write): 선적(Shipment).상태(Status), 경보(Alert)
    *   실행 가능(Can Execute): 고객 알림(Notify_Customer), 운영팀에 에스컬레이션(Escalate_to_Operations)
    *   컨텍스트(Context): 상태(Status)가 ['운송 중(In Transit)', '지연됨(Delayed)']에 해당하는 모든 선적(Shipments)

에이전트는 다음을 상속받습니다.

*   이 엔티티(entities)가 무엇을 의미하는지 (온톨로지 정의에서)
*   어떻게 서로 관련되어 있는지 (관계 그래프(relationship graph)에서)
*   무엇이 유효한지 (비즈니스 규칙에서)
*   무엇을 할 수 있는지 (허용된 작업에서)

데이터 스키마(data schemas)에 대한 프롬프트 엔지니어링(prompt engineering)도, RAG 파이프라인(RAG pipeline) 연결도 필요 없습니다. 시맨틱 계약(semantic contract) 자체가 기반(grounding)이 됩니다.

## 2부: 팔란티어(Palantir) 대 마이크로소프트(Microsoft) — 온톨로지의 두 가지 철학

### 팔란티어(Palantir)의 접근 방식: 인간 분석가를 위한 온톨로지

팔란티어의 파운드리(Foundry) 플랫폼은 엔터프라이즈 온톨로지를 개척했지만, 다른 사용 사례(use case)에 최적화되었습니다.

**팔란티어(Palantir) 모델:**

*   **그래프 우선(Graph-first):** 모든 것이 노드(nodes)와 엣지(edges)로, 관계 탐색(relationship traversal)에 최적화
*   **인간 개입(Human-in-the-loop):** 분석가(analysts)가 온톨로지 그래프(ontology graph)를 탐색하여 조사를 구축
*   **정보 분석(Intelligence) 중심:** 테러 방지, 사기 탐지, 복잡한 조사에 맞게 설계
*   **폐쇄형 생태계(ecosystem):** 온톨로지가 팔란티어 플랫폼 내에 존재

**사용 사례(Use Case):** 사기 조사를 하는 분석가(analyst)가 링크를 따라갑니다. 용의자 → 은행 계좌 → 거래 → 회사 → 소유주 → 용의자2

온톨로지는 연결을 가시적이고 탐색 가능하게 만들어 인간 추론을 가능하게 합니다.

### 마이크로소프트(Microsoft)의 접근 방식: 자율 에이전트(Autonomous Agents)를 위한 온톨로지

마이크로소프트의 패브릭 IQ(Fabric IQ)는 근본적으로 다른 문제에 최적화되어 있습니다.

**마이크로소프트(Microsoft) 모델:**

*   **시맨틱 우선(Semantic-first):** 엔티티(Entities)는 단순히 그래프 노드(graph nodes)가 아니라 규칙을 가진 비즈니스 개념
*   **에이전트 자율(Agent-autonomous):** AI 에이전트(AI agents)가 인간의 개입 없이 온톨로지 위에서 추론
*   **운영(Operations) 중심:** 공급망, 재무, 고객 운영 등 비즈니스 프로세스에 맞게 설계
*   **개방형 생태계(ecosystem):** 온톨로지가 파워 BI(Power BI), 애저(Azure), 서드파티(third-party) 도구와 연결

**사용 사례(Use Case):** 에이전트가 선적(shipments)을 모니터링하고, 온도 위반(Temperature_Violation) → 위험 선적(Shipment_at_Risk) → 고객 영향(Customer_Impact) → 자동 경로 변경(Auto_Reroute)을 감지합니다.

온톨로지는 허용되는 것에 대한 시맨틱 계약(semantic contracts)을 제공함으로써 자율적인 행동을 가능하게 합니다.

### 주요 차이점: 그래프 데이터(Graph Data) 대 시맨틱 계약(Semantic Contracts)

| 차원(Dimension)          | 팔란티어(Palantir)                                   | 마이크로소프트(Microsoft)                                      |
| :----------------------- | :--------------------------------------------------- | :------------------------------------------------------------- |
| **주요 목표(Primary Goal)** | 인간 분석가가 연결을 찾도록 지원                         | AI 에이전트가 자율적인 행동을 취하도록 지원                            |
| **온톨로지 구조(Ontology Structure)** | 그래프(graph)(탐색에 최적화된 노드/엣지)                     | 시맨틱 모델(semantic model)(추론에 최적화된 엔티티/규칙)                    |
| **의사 결정 방식(Decision Making)** | 인간이 분석하고 시스템이 제시                             | 에이전트가 추론하고 시스템이 검증                                     |
| **통합(Integration)**      | 독점 플랫폼                                          | 개방형 표준(MCP, semantic layer)                               |
| **최적 사용처(Best For)**   | 복잡한 조사, 정보 분석 작업                             | 운영 자동화, 비즈니스 프로세스                                      |

## 3부: 대부분의 기업에 마이크로소프트(Microsoft)의 구현 방식이 더 중요한 이유

### 데이터 플랫폼(Data Platform) 소유권 문제

이 질문이 밝혀낸 전략적 통찰은 다음과 같습니다.

**어떤 온톨로지 기술을 사용하는지보다 누가 시맨틱 계층(semantic layer)을 소유하는지가 더 중요합니다.**

**팔란티어(Palantir)의 접근 방식:** "데이터를 저희 플랫폼으로 가져오시면, 저희가 온톨로지를 구축해 드리겠습니다."

*   세계적 수준의 온톨로지 툴링(tooling)을 얻게 됩니다.
*   하지만 시맨틱 이해가 특정 벤더(vendor)의 사일로(silo)에 갇히게 됩니다.
*   다른 도구(파워 BI(Power BI), 세일즈포스(Salesforce), SAP)는 이에 접근할 수 없습니다.

**마이크로소프트(Microsoft)의 접근 방식:** "데이터가 이미 존재하는 곳에 온톨로지를 구축하십시오."

*   온톨로지는 패브릭(Fabric) 아이템으로, 버전 관리(version-controlled) 및 거버넌스(governed)됩니다.
*   생태계(ecosystem) 내의 모든 도구(tool)가 이를 사용할 수 있습니다.
*   시맨틱 계층(semantic layer)이 벤더 종속(vendor lock-in)이 아닌 인프라(infrastructure)가 됩니다.

### 노코드(No-Code) 민주화 전략

대부분의 기업은 온톨로지를 구축하기 위해 팔란티어(Palantir) 컨설턴트(consultants)를 고용할 수 없습니다. 마이크로소프트의 전략은 다음과 같습니다.

**이미 2천만 개의 파워 BI(Power BI) 시맨틱 모델(semantic models)을 구축한 비즈니스 분석가(business analysts)는 이를 완전한 온톨로지로 발전시킬 수 있습니다.**

작업 흐름(workflow):

*   기존 파워 BI(Power BI) 모델로 시작
*   패브릭 IQ(Fabric IQ)가 시맨틱 풍부화(semantic enrichments)를 제안: " '주문(Order)'에 위험 점수(RiskScore)가 있어야 할까요?"
*   비즈니스 분석가(business analyst)가 규칙 추가: "5만 달러 초과 주문은 승인 필요"
*   AI 에이전트(AI agents)가 이제 사용자 정의 코드(custom code) 없이 이러한 제약 조건을 이해

이는 엔터프라이즈(enterprise) 규모의 온톨로지 민주화입니다.

### 에이전트 AI(Agentic AI)의 적시성

마이크로소프트의 타이밍이 완벽한 이유는 다음과 같습니다.

**2015–2020:** 팔란티어(Palantir)의 접근 방식이 합리적이었습니다. AI는 자율적인 의사 결정(autonomous decisions)을 내릴 준비가 되어 있지 않았습니다. 인간의 개입(human-in-the-loop)이 필요했습니다. 그래프 탐색(Graph exploration)이 올바른 사용자 인터페이스(UI)였습니다.

**2025년 이후:** 대규모 언어 모델(LLMs)은 추론하고, 계획하며, 행동할 수 있습니다. 병목 현상은 인간 분석이 아니라 에이전트(agents)에게 신뢰성 있게 행동할 수 있는 충분히 구조화된 컨텍스트(context)를 제공하는 것입니다. 시맨틱 계약(Semantic contracts)이 이를 해결합니다.

마이크로소프트는 단순히 팔란티어의 숙제를 베끼는 것이 아닙니다. 이들은 다음 문제를 해결하고 있습니다. 100개의 AI 에이전트가 기업 내에서 혼돈 없이 어떻게 협력하도록 할 것인가?

**답변: 시맨틱 운영 체제(semantic operating system)로서의 공유 온톨로지(Shared ontology).**

## 4부: 기술 구현 세부 사항

### 마이크로소프트가 온톨로지를 데이터에 바인딩(Bind)하는 방법

내부적으로 패브릭 IQ(Fabric IQ)는 3단계 바인딩(binding) 모델을 사용합니다.

#### 계층 1: 논리적 정의(Logical Definition) (순수 온톨로지)

*   엔티티(Entity): 고가치 고객(HighValueCustomer)
    *   정의(Definition): "평생 가치(lifetime value) 10만 달러 초과 또는 엔터프라이즈(enterprise) 등급의 고객"
    *   아직 물리적 매핑(mapping) 없음

#### 계층 2: 물리적 바인딩(Physical Binding) (데이터 레이크하우스)

*   바인딩(Binding): 고가치 고객(HighValueCustomer) → SQL 쿼리(SQL query)
    *   SELECT customer_id, tier, lifetime_value
    *   FROM customers
    *   WHERE lifetime_value > 100000 OR tier = 'Enterprise'

#### 계층 3: 계산된 속성(Computed Properties) (실시간 풍부화)

*   속성(Property): 현재 위험 점수(CurrentRiskScore)
    *   출처(Source): 애저 ML(Azure ML) 모델 엔드포인트(endpoint)
    *   새로고침(Refresh): 접근 시 실시간
    *   캐시(Cache): 5분

이는 온톨로지가 다음을 참조할 수 있음을 의미합니다.

*   원레이크(OneLake)의 정적 데이터
*   실시간 센서(sensor) 데이터
*   ML 모델 예측
*   외부 API 호출

이 모든 것을 하나의 시맨틱 인터페이스(semantic interface)를 통해 가능합니다.

### 에이전트(Agents)가 온톨로지를 실제로 사용하는 방법

패브릭(Fabric) 데이터 에이전트(data agent)가 실행될 때, 다음 과정이 발생합니다.

#### 단계 1: 시맨틱 쿼리 계획(Semantic Query Planning)

에이전트가 받음: "고가치 고객(high-value customers) 중 위험한 선적(at-risk shipments)을 가진 고객이 있으면 알려줘"

온톨로지 쿼리 플래너(Ontology Query Planner)가 다음으로 번역:
1.  "고가치 고객" 해결 → 고가치 고객(HighValueCustomer) 엔티티(entity)
2.  "위험한 선적" 해결 → 위험 점수(RiskScore) > 80인 선적(Shipment)
3.  관계 경로 찾기: 고가치 고객(HighValueCustomer) --[가짐]--> 선적(Shipment)
4.  에이전트 권한 확인: ✓ 두 엔티티(entities) 모두 읽기 가능

#### 단계 2: 물리적 실행(Physical Execution)

최적의 데이터 쿼리(data query) 생성:

```sql
SELECT c.customer_id, s.shipment_id, s.risk_score
FROM customers c
JOIN shipments s ON c.customer_id = s.customer_id
WHERE c.lifetime_value > 100000 
AND s.risk_score > 80
AND s.status = 'In Transit'
```

#### 단계 3: 시맨틱 응답(Semantic Response)

에이전트에게 원시 행(raw rows)이 아닌 유형화된 엔티티(typed entities)로 반환:

```json
[
  { 
    type: "CustomerAtRisk",
    customer: HighValueCustomer(id=123, tier="Enterprise"),
    shipments: [Shipment(id=456, risk_score=85, issue="TempAlert")]
  }
]
```

에이전트는 SQL을 전혀 보지 않습니다. 오직 비즈니스 개념으로만 추론합니다.

## 5부: 이것이 미래인 이유 (그리고 당신에게 의미하는 것)

### 융합 가설(Convergence Thesis)

저는 두 세계가 충돌하는 것을 보고 있다고 믿습니다.

*   **팔란티어(Palantir) 세계:** 정교하고 그래프 기반이며 인간 분석가 중심의 온톨로지 플랫폼(ontology platforms)
*   **마이크로소프트(Microsoft) 세계:** 민주화된, 비즈니스 시맨틱 기반의 에이전트 중심 온톨로지 플랫폼

미래는 한쪽이 다른 쪽을 대체하는 것이 아닙니다. 그것은 다음과 같습니다.

*   정보 분석, 보안, 사기 조사 등 **팔란티어(Palantir) 방식의 깊이 있는 활용**
*   모든 비즈니스 기능 전반의 운영 AI(operational AI)를 위한 **마이크로소프트(Microsoft) 방식의 폭넓은 활용**

### 데이터 플랫폼(Data Platforms)이 온톨로지를 소유해야 하는 이유

독자의 질문은 전략적 핵심을 꿰뚫습니다. 온톨로지 계층(ontology layer)이 AI 벤더(AI vendor)에 의해 소유되어야 할까요, 아니면 데이터 플랫폼(data platform)에 의해 소유되어야 할까요?

마이크로소프트의 전략(그리고 저도 동의합니다): **데이터 플랫폼이 승리합니다.**

그 이유는 다음과 같습니다.

**온톨로지가 AI 도구 내에 존재한다면:**

*   해당 도구의 에이전트(agents)에게만 기반을 제공할 수 있습니다.
*   BI 도구, 데이터 파이프라인(data pipelines) 또는 다른 시스템에 도움이 될 수 없습니다.
*   새로운 도구가 추가될 때마다 시맨틱 이해를 다시 구축해야 합니다.

**온톨로지가 데이터 플랫폼 내에 존재한다면:**

*   모든 도구(에이전트(agents), BI, 앱(apps), API)가 이를 사용할 수 있습니다.
*   데이터 레이크하우스(data lakehouse)처럼 인프라(infrastructure)가 됩니다.
*   시맨틱 이해를 한 번 구축하면 모든 곳에서 활용할 수 있습니다.

이것이 패브릭 IQ(Fabric IQ)가 중요한 이유입니다. 마이크로소프트는 온톨로지를 기능이 아닌 인프라(infrastructure)로 만들고 있습니다.

### 다음 아키텍처(Architecture) 결정에 대한 의미

현재 온톨로지 플랫폼(ontology platforms)을 평가하고 있다면, 다음을 고려해야 합니다.

**팔란티어(Palantir) 방식을 선택해야 하는 경우:**

*   복잡한 조사(사기, 보안, 정보 분석)를 수행하는 경우
*   세계적 수준의 그래프 탐색(graph traversal) 및 분석가 사용자 경험(analyst UX)이 필요한 경우
*   프리미엄 기능(premium capabilities)을 위해 플랫폼 종속(platform lock-in)을 감수할 수 있는 경우
*   인간 전문 지식이 병목 현상이지 에이전트 자동화(agent automation)가 아닌 경우

**마이크로소프트(Microsoft) 방식을 선택해야 하는 경우:**

*   대규모로 운영 AI 에이전트(operational AI agents)를 구축하는 경우
*   전체 기술 스택(tech stack)에서 시맨틱 이해가 필요한 경우
*   비즈니스 분석가(business analysts)가 온톨로지를 구축하고 유지 관리하기를 원하는 경우
*   에이전트 조정(agent coordination) 및 자율적 행동(autonomous action)이 목표인 경우

**두 가지 모두 선택해야 하는 경우:**

*   조사 및 운영 요구 사항을 모두 가진 대기업인 경우
*   팔란티어(Palantir)의 그래프(graph)를 패브릭(Fabric)의 시맨틱 계층(semantic layer)에 연결하는 데 투자할 수 있는 경우
*   다른 사용 사례(use cases)에 대해 최고 수준의 솔루션이 필요한 경우

### 진정한 질문: 무엇을 최적화하고 있는가?

팔란티어(Palantir)는 복잡하고 고위험의 조사에서 인간의 의미 파악(sensemaking)에 최적화되었습니다.

마이크로소프트는 대규모의 운영 워크플로우(operational workflows)에서 에이전트 조정(agent coordination)에 최적화되었습니다.

어느 한쪽이 "더 좋다"고 할 수는 없습니다. 이들은 서로 다른 문제를 해결하고 있습니다.

하지만 저의 예측은 이렇습니다. 90%의 기업은 팔란티어(Palantir) 모델보다 마이크로소프트(Microsoft) 모델을 더 필요로 합니다. 그 이유는 다음과 같습니다.

*   조사 요구 사항보다 운영 자동화(operational automation) 요구 사항이 더 많습니다.
*   10명의 분석가가 그래프를 탐색하는 것이 아니라 100개의 에이전트(agents)가 함께 작동해야 합니다.
*   별도의 플랫폼(platform)만이 아니라 파워 BI(Power BI)에서도 시맨틱 이해가 필요합니다.

정보 분석 작업, 사기 탐지, 복잡한 조사를 수행하는 나머지 10%의 경우, 팔란티어(Palantir)의 접근 방식은 타의 추종을 불허합니다.

## 다음 예정: 첫 번째 시맨틱 계약(Semantic Contract) 구축하기

다음 기사에서는 다음 방법을 정확히 보여드리겠습니다.

*   기존 파워 BI(Power BI) 모델 사용하기
*   이를 패브릭(Fabric) 온톨로지(ontology)로 발전시키기
*   시맨틱 계약(semantic contracts)을 통해 이를 사용하는 에이전트(agent) 구축하기
*   거버넌스(governance)를 통해 프로덕션(production)에 배포하기

코드 예제, 실제 스키마(schemas), 프로덕션 패턴(production patterns)과 함께 말이죠.

"학술적 연습으로서의 온톨로지" 시대는 끝났기 때문입니다.

"인프라(infrastructure)로서의 온톨로지" 시대가 시작되었습니다.

이 대화를 발전시키는 어려운 질문을 던져주신 모든 분들께 진심으로 감사드립니다. 팔란티어(Palantir) 대 마이크로소프트(Microsoft) 또는 자체 온톨로지 솔루션 구축 문제로 고민하고 계시다면, 특정 사용 사례(use case)를 댓글로 남겨주세요. 다음 글에서 다루겠습니다.

👏 온톨로지 구현에 대한 생각이 바뀌었다면 박수를 쳐주세요.
💬 팔란티어/마이크로소프트/맞춤형 온톨로지 경험을 댓글로 공유해주세요.
🔁 AI 벤더(vendor)와 데이터 플랫폼(data platform) 중 결정하려는 아키텍트(architects)와 공유해주세요.

온톨로지, 시맨틱 웹(semantic web) 기술, AI 에이전트(AI agent) 아키텍처(architectures)에 대한 더 많은 기술적 탐구를 원하시면 제 깃허브(GitHub)를 확인해주세요: [https://github.com/cloudbadal007](https://github.com/cloudbadal007)