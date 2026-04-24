# 번역 기사

- **원본 URL:** https://stevehedden.medium.com/open-knowledge-graphs-a-search-engine-for-ontologies-controlled-vocabularies-and-semantic-web-cfcf32a5babe
- **번역 일시:** 2026-03-29 18:07:49

---

# 오픈 지식 그래프(Open Knowledge Graphs): 온톨로지, 통제된 어휘 및 시맨틱 웹 도구를 위한 검색 엔진

_Steve Hedden 저_

_읽는 데 8분 소요 · 2026년 3월 16일_

Wikidata를 기반으로 구축되고 매일 업데이트되는 지식 그래프(knowledge graph) 리소스 카탈로그

오픈 지식 그래프(OKG)는 시맨틱 웹(semantic web)에 이미 유용한 정보가 많지만, 핵심 문제는 실제로 작동하는 것을 찾아내는 것이라는 간단한 전제에서 시작된 작은 프로젝트입니다.

지식 그래프(knowledge graph)를 구축하고자 한다면, 목표 중 하나는 아마도 정렬(alignment)일 것입니다. 따라서 가장 먼저 해야 할 일은 이미 존재하는 것을 기반으로 구축하고 기존 온톨로지(ontology) 또는 어휘(vocabulary)와 정렬하는 것입니다. 이러한 온톨로지, 분류체계(taxonomy) 및 어휘 중 상당수는 잘 설계되었고 활발히 유지보수되고 있습니다.

하지만 이를 찾아내는 것은 놀라울 정도로 어렵습니다.

OKG는 이 문제를 해결하고자 합니다. 현재(2026년 3월 기준) OKG는 다음을 색인하고 있습니다:

*   온톨로지/어휘 1,803개
*   시맨틱 웹 소프트웨어 도구 165개
*   색인된 페이지 660개

아래는 웹사이트, API, 그리고 GitHub에 있는 소스 코드 링크입니다.

*   웹사이트: https://openknowledgegraphs.com/
*   API: https://api.openknowledgegraphs.com/
*   소스 코드: https://github.com/SteveHedden/open-knowledge-graphs

이미 커뮤니티의 관심이 높습니다. 누군가 OKG를 위한 MCP 서버(MCP server)를 구축하기도 했습니다.

지식 그래프는 개체(entity)와 그들 간의 관계를 구조화하여 표현한 것입니다. 지식 그래프의 정의와 그 유용성에 대해 제가 최근에 작성한 게시물이 있습니다.

제가 지식 그래프를 좋아하는 주요 이유는 다음과 같습니다:

*   **다양한 응용 분야:** 검색 및 정보 검색(search and retrieval); 발견 및 설계(discovery and design); 재사용 및 재목적화(reuse and repurposing); 의사 결정 지원(decision support); 추천 및 개인화(recommendation and personalization); 그리고 거버넌스, 추적성 및 규제 준수(governance, traceability and regulatory compliance)에 활용할 수 있습니다.
*   **오늘날 보편화된 AI와는 직교적이면서도 상호 보완적입니다:** 현대 AI 시스템(특히 생성형 모델(generative models))은 예측적이고(predictive), 확률적이며(probabilistic), 설명 불가능합니다(unexplainable). 반면 지식 그래프(KG)는 규칙 기반이며(rules based), 결정론적이고(deterministic), 추적 가능합니다(traceable).
*   **오픈 표준(open standards)을 기반으로 합니다:** 지식 그래프는 시맨틱 웹(semantic web)과 깊이 연관되어 있습니다. 시맨틱 웹은 인간과 기계가 웹을 소비하고 기여하여 우리의 집단 지식을 향상시키는 월드 와이드 웹(World Wide Web)의 원래 비전이었습니다.

### OKG는 왜 존재할까요?

*   **이러한 리소스를 찾기 어렵습니다:** 시맨틱 웹 기술이나 온톨로지 또는 통제된 어휘와 같은 사전 구축된 지식 그래프 리소스를 찾으려면 많은 노력이 필요합니다. 제가 찾은 최고의 리소스는 Wikidata이며, OKG가 Wikidata의 데이터를 기반으로 구축된 이유이기도 합니다.
*   **작동하는 것을 기반으로 구축해야 합니다:** AI가 온톨로지와 어휘를 더 쉽게 만들 수 있게 됨에 따라, 이미 구축된 것을 기반으로 해야 합니다. AI는 어떤 주제에 대해서든 분류체계(taxonomy)를 처음부터 꽤 잘 구축할 수 있으며, 미래에는 더욱 발전할 것입니다. 하지만 동일한 도메인(domain)의 기존 분류체계를 참고한다면 훨씬 더 나은 결과를 낼 수 있습니다. 어쩌면 아예 새로 만들 필요 없이 이미 존재하는 것을 사용할 수도 있을 것입니다.
*   **생성이 쉬워질수록 조정이 더욱 중요해집니다:** AI가 리소스를 처음부터 생성하는 것을 더 쉽게 만들면서, 발견하고, 조정하고, 공유하고, 정렬하고, 협업하는 능력은 AI가 발견 및 조정을 수행하더라도 더욱 중요해집니다.
*   **지식 그래프는 지식을 확장하기 위한 것입니다:** 시맨틱 웹의 비전은 우리가 정보를 소비하고 기여하는 것입니다. 만약 기성 분류체계(off-the-shelf taxonomy)가 대부분의 원하는 것을 포함하고 있었지만, 이를 개선했다면, 다른 사람들이 배울 수 있도록 웹에 다시 기록할 수 있습니다. 모든 에이전트(agent)가 동일한 분류체계를 구축하는 대신, 우리는 서로에게서 배울 수 있습니다.

### 원칙

*   새로운 목록을 만드는 대신 이미 존재하는 지식을 드러내기
*   카탈로그 자체를 그래프(graph) 형태로 구조화하기
*   객관적인 메타데이터(metadata) 신호를 사용하여 리소스 순위 지정
*   에이전트(agent)가 직접 쿼리(query)할 수 있도록 API 접근 제공

### OKG란 무엇인가?

아이디어는 간단합니다. 기존의 모든 온톨로지(ontology), 분류체계(taxonomy), 그리고 기타 통제된 어휘(controlled vocabularies)(용어집, 사전 등)의 목록과 시각화 및 생성 도구와 같은 시맨틱 웹 소프트웨어를 제공하는 것입니다.

이 목록은 어떤 면에서는 Wikidata에 이미 존재합니다. Wikidata는 위키백과(Wikipedia)의 자매 프로젝트이자 가장 큰 무료 개방형 지식 그래프(knowledge graph)입니다. 누구나 편집할 수 있는 1억 2천만 개 이상의 데이터 개체(data entities)를 보유하고 있습니다. 그래프이기 때문에 SPARQL을 사용하여 쿼리(query)할 수 있습니다. 여기 SPARQL 쿼리 서비스가 있습니다.

```sparql
SELECT ?item ?itemLabel WHERE {
  ?item wdt:P31 wd:Q324254 .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
```

이 쿼리(query)는 '온톨로지(ontology)'의 인스턴스(instance)인 모든 개체(엔티티, 위키백과(Wikipedia)의 페이지와 유사)를 찾습니다. 즉, 모든 온톨로지를 찾습니다. 이 쿼리는 500개 이상의 항목을 반환합니다. '통제된 어휘(controlled vocabularies)' 또는 '시맨틱 웹 소프트웨어(semantic web software)'에 대해서도 동일한 작업을 수행할 수 있습니다. '통제된 어휘'는 용어집(glossaries), 시소러스(thesauri), 지적도(cadastres) 등 많은 것을 포함하는 광범위한 용어입니다. Wikidata에서는 분류체계(taxonomies)가 온톨로지나 통제된 어휘의 하위 클래스(subclass)가 아닙니다.

### 데이터 모델

Wikidata의 내재된 구조를 활용하여 이러한 리소스의 모델을 구축할 수 있습니다:

*   Ontologies
*   Taxonomies
*   Semantic Software
*   Controlled Vocabularies
    *   Thesaurus
    *   Glossaries
    *   Cadastres

위의 구조는 OKG의 핵심 온톨로지(core ontology) 구조입니다. 이러한 클래스(class)는 각 인스턴스(instance)로 채워집니다. Wikidata에서 시맨틱 웹 소프트웨어(semantic web software)의 인스턴스로 나열된 모든 개체(entity)를 OKG에서는 시맨틱 웹 소프트웨어의 인스턴스로 분류합니다. Wikidata에서 온톨로지(ontology)의 인스턴스로 나열된 모든 개체는 OKG에서 온톨로지의 인스턴스로 분류합니다. 분류체계(taxonomies)를 포함한 다른 모든 것은 통제된 어휘(controlled vocabularies)의 인스턴스로 분류합니다. 즉, 불필요하게 복잡해지는 경우를 제외하고는 Wikidata의 구조를 가능한 한 많이 반영합니다.

### 정기 업데이트

OKG는 매일 쿼리(query)를 실행하여 데이터를 업데이트하고 그 결과를 TTL 파일(TTL file)로 저장합니다. 이 TTL 파일은 웹페이지에 표시하기 위해 JSON으로 변환됩니다. TTL 파일과 JSON 파일 모두 직접 다운로드할 수 있습니다.

### 데이터 보강

현재 제가 수행하는 유일한 데이터 보강(data enrichment)은 온톨로지(ontology)와 어휘(vocabulary)를 주제별로 분류하는 것입니다. 약 1,800개의 리소스가 있으며, 이를 하나의 긴 목록으로 나열하는 것은 다소 부담스럽습니다. 사용자가 자신의 도메인(domain)으로 더 쉽게 이동할 수 있도록 상단에 주제 필터(filter)를 추가했습니다. 각 어휘의 제목과 설명을 사용하여 LLM(Large Language Model)으로 분류했습니다.

### 품질/관련성별 정렬

리소스는 품질별로 정렬됩니다. 어휘의 품질을 측정하기 위해 설명(description), 공식 웹페이지(official webpage), 라이선스(license)와 같은 속성(properties)을 사용합니다. 소프트웨어의 경우, 설명과 라이선스를 사용하며, 최신 릴리스(release) 날짜를 기준으로도 정렬합니다. 정기적으로 업데이트되는 소프트웨어가 더 관련성이 높다는 이론에 기반합니다. 일부 저장소(repository)는 몇 년 동안 업데이트되지 않았습니다.

### API

API는 기본 JSON 데이터를 직접 쿼리(query)하고 JSON 형식으로 결과를 반환합니다. 사용자가 리소스의 정확한 이름을 알 필요가 없도록 시맨틱 검색(semantic search)을 활성화했습니다. 예를 들어, '영화'를 검색하면 저의 '필름 클럽 지식 그래프(Film Club Knowledge Graph)'가 반환될 것입니다.

### SEO (검색 엔진 최적화)

주요 사용 사례는 사람이나 에이전트(agent)가 이미 존재하는 것을 기반으로 지식 그래프(knowledge graph) 또는 그 일부를 구축하도록 돕는 것입니다. 에이전트가 OKG의 존재를 안다면, API 자체를 찾아 시작점으로 사용할 관련 리소스를 찾을 가능성이 높습니다. 하지만 에이전트가 처음부터 OKG에 대해 어떻게 알 수 있을까요? 에이전트가 이미 존재하는 것을 기반으로 구축하려고 한다면, 웹 검색으로 시작할 가능성이 높습니다. OKG가 검색 결과에 나타나지 않는다면, 사용될 가능성은 낮습니다.

검색 결과를 개선하기 위해 구글(Google)의 지침 원칙에 따라 랜딩 페이지(landing page)의 HTML에 구조화된 데이터(structured data)를 삽입했습니다. 좋은 문서화(설명, 라이선스, 공식 웹페이지)를 갖춘 리소스에는 자체 HTML 페이지를 제공하여 구글에서도 색인될 수 있도록 했습니다. 약 660개의 리소스가 충분히 잘 문서화되어 자체 페이지를 갖게 됩니다. 이 HTML은 물론 원래 Wikidata 페이지와 해당 공식 웹사이트로 다시 연결될 것입니다.

### 다음 단계

OKG를 더욱 개선할 수 있는 몇 가지 즉각적인 다음 단계는 다음과 같습니다.

*   **기본 Wikidata 데이터 개선:** OKG는 기본 데이터만큼만 유용하며, 이를 개선하는 유일한 방법은 Wikidata를 직접 업데이트하는 것입니다. 여기에 GitHub 저장소(repo)의 이슈(issue)로 데이터 개선 목록을 만들었습니다. 몇 가지 기본적인 정리 작업은 다음과 같습니다: 더 나은 설명, 홈페이지 URL 추가, 깨진 URL 제거 또는 교체, 그리고 생성자 추가. Wikidata는 봇 편집(bot editing)을 지원하므로, 즐겨 사용하는 에이전트(agent)를 사용하여 데이터를 정리하고 보강할 수도 있습니다. 도움을 주고 싶은 사람들을 위해 GitHub 저장소에 이슈 목록을 만들었습니다.
*   **에이전트(agent)와의 통합:** 가능한 통합 대상: LangChain 도구, LlamaIndex 리트리버(retriever), MCP 서버(MCP server), AI 에이전트(AI agent)용 OpenAPI 도구. 이러한 방식으로, 에이전트는 "개방형 라이선스(open license)를 가진 생물 다양성 온톨로지를 찾아줘"라고 질문하고 자동으로 하나를 검색할 수 있습니다. MCP 서버는 Arthur Sarazin에 의해 이미 구축되었습니다.
*   **품질 신호 및 신뢰 지표:** 현재 우리는 메타데이터(metadata) 필드를 사용하여 리소스 순위를 매깁니다. 추가적인 신호는 다음을 포함할 수 있습니다: 인용 횟수(citation count), 온톨로지를 사용하는 다운스트림 프로젝트(downstream projects) 수, GitHub 활동, 어휘의 마지막 업데이트 날짜. 각 리소스에 대한 "신뢰 점수" 또는 "성숙도 점수"를 만들 수 있습니다. 그러나 이러한 지표는 객관적이고 가능한 한 편향되지 않아야 합니다.
*   **리소스 형식 식별:** 기본 코드(주로 GitHub에 있음)에 대한 링크가 있는 온톨로지(ontology) 및 어휘(vocabulary)의 경우, 형식이 매우 다양합니다. 형식이나 언어에 대한 설명(RDFS, SKOS, OWL, SHACL 등)이 있다면 도움이 될 것입니다.
*   **온톨로지, 통제된 어휘 및 소프트웨어 외 확장:** 현재 OKG는 어휘와 소프트웨어에 중점을 둡니다. 우리는 해당 어휘를 사용하는 데이터셋(dataset), 이를 사용하여 구축된 지식 그래프(knowledge graph)의 예시, SPARQL 엔드포인트(endpoint) 링크 등을 포함할 수 있습니다.

### 관련 아이디어

OKG의 원칙과 일치하며 시간이 지남에 따라 프로젝트를 개선하는 데 도움이 될 수 있는 다른 아이디어는 다음과 같습니다.

*   **AI를 사용한 지식 그래프 구축 도구:** 궁극적인 목표가 OKG에서 찾은 리소스를 사용하여 그래프를 구성하는 것이라면, 이를 지원하는 더 많은 도구가 필요합니다. 일부 도구는 이미 AI를 사용하여 그래프 구조를 자동으로 생성하지만, 일반적으로 처음부터 시작합니다. 대신, 해당 도구들은 먼저 OKG에서 해당 도메인(domain)의 기존 온톨로지(ontology)와 어휘(vocabulary)를 검색해야 합니다. 그런 다음 Wikidata를 사용하여 추가 구조와 관계를 추론할 수 있습니다. 많은 경우, 그래프의 온톨로지는 Wikidata 자체에 암묵적으로 존재할 수 있습니다. OKG는 이러한 리소스들을 재사용할 수 있도록 단순히 드러내고 정리하는 역할을 합니다.
*   **퍼블리시 백 파이프라인(publish-back pipeline):** 기존 어휘를 사용하여 그래프를 구축하고 확장한 후, 새로운 용어를 적절한 어휘(문서화, 네임스페이스(namespace), Wikidata 항목 포함)로 다시 게시하는 데 도움이 되는 도구가 필요합니다.
*   **온톨로지 차이/변경 로그 도구(Ontology diff/changelog tool):** 온톨로지가 시간이 지남에 따라 어떻게 진화하는지 추적합니다. 어휘가 업데이트될 때, 무엇이 변경되었는지(새로운 클래스(class), 사용 중단된 용어(deprecated terms), 이름이 변경된 속성(properties) 등) 보여줍니다. 외부 온톨로지에 의존하는 모든 사람에게 유용합니다.
*   **지식 그래프 스타터 키트(Knowledge graph starter kits):** OKG의 도메인 온톨로지(domain ontology)와 스키마(schema) 및 샘플 데이터(sample data)를 결합한 사전 구축된 템플릿(template)입니다. 예를 들어, "금융 지식 그래프 시작하기"는 FIBO(Financial Industry Business Ontology) + 기본 그래프 구조 + 거래 상대방, 상품, 거래와 같은 개체(entity)에 대한 예시 삼중항(triples)을 가져옵니다.
*   **어휘 추천 엔진:** "X에 대한 그래프를 구축 중입니다" → 다음 3가지 온톨로지를 결합해야 합니다. 이들의 중첩되는 부분과 아직 정의해야 할 사항이 있습니다.
*   **그래프 정렬 및 매핑:** 다른 어휘들은 종종 겹치지만 다른 용어를 사용합니다. 우리는 어휘 간에 중첩되는 개념이나 용어를 감지하고 매핑(mapping)을 제안할 수 있습니다.

### 결론

시스템이 온톨로지(ontology)와 지식 그래프(knowledge graph) 구축에 능숙해질수록, 문제는 생성에 있는 것이 아니라 발견과 조정에 있습니다. 에이전트(agent)가 처음부터 하나를 구축하기 전에 잘 유지보수된 어휘를 발견할 수 있다면, 모두에게 이득이 됩니다. 카탈로그, API, 구글에 색인된 개별 페이지들은 모두 그러한 발견을 가능하게 하는 방법일 뿐입니다. 온톨로지, 어휘 또는 지식 그래프와 함께 작업한다면 한번 살펴보시고, 누락되거나 손상된 부분을 발견하면 Wikidata에서 수정해 주십시오.

---
**태그:** 인공지능(Artificial Intelligence), 링크드 데이터(Linked Data), 지식 그래프(Knowledge Graph), 온톨로지(Ontology), 데이터 엔지니어링(Data Engineering)