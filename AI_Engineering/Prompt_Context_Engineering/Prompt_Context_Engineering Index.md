# Prompt_Context_Engineering Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-03-01 |
| 키워드 | `프롬프트 캐싱`, `prompt caching`, `컨텍스트 엔지니어링`, `context engineering`, `XML 태그`, `XML tags`, `메가 프롬프트`, `mega prompt`, `200달러 프롬프트`, `EmotionPrompt`, `인센티브 프롬프팅`, `프롬프트 계약`, `Prompt Contracts`, `Bsharat 26 원칙`, `WebMCP`, `TOON`, `lost-in-middle` |
| 관련문서 | [[AI_Engineering Index]], [[wiki_index]] |

## 개요

프롬프트·컨텍스트 엔지니어링 — 프롬프트 캐싱·컨텍스트 엔지니어링 6기술·XML 태그·메가 프롬프트·200달러 프롬프트·EmotionPrompt·인센티브·프롬프트 계약·TOON·WebMCP.

## 파일 목록 (14개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260227_223253_컨텍스트 엔지니어링을 위한 에이전트 스킬]] | muratcankoylan/Agent-Skills-for-Context-Engineering(GitHub 스타 12.2k) 저장소 README 번역. context-fundamentals·context-degradation(lost-in-middle/U자형 어텐션)·context-compression·multi-agent-patterns·memory-systems(Cognee 포함)·filesystem-context·hosted-agents·evaluation·advanced-evaluation(LLM-as-a-Judge)·project-development·bdi-mental-states(RDF→신념/욕망/의도) 등 14종 스킬을 Claude Code 플러그인 마켓플레이스·Cursor `.rules`·커스텀 에이전트에 탑재하는 방법 제공. 북경대 MCE 논문이 "정적 스킬 아키텍처" 기초로 인용. | Agent Skills for Context Engineering, muratcankoylan, 중간 실종 lost-in-middle, U자형 어텐션, BDI 온톨로지, LLM-as-a-Judge, Cognee, Claude Code Plugin Marketplace, digital-brain-skill, Book SFT Pipeline |
| [[20260228_211253_200달러 프롬프트 실험 내가 실수로 클로드를 45 더 똑똑하게 만든 방법]] | 인센티브·감정·도전·심호흡·상세 페르소나 등 5가지 심리적 프롬프팅 기법을 실험과 학술 논문(EmotionPrompt arXiv:2307.11760, OPRO arXiv:2309.03409, Bsharat 26 원칙 arXiv:2312.16171, ExpertPrompting arXiv:2305.14688, Role-Play NAACL 2024)으로 뒷받침. "200달러 팁" 품질 45%↑, "심호흡" GSM8K 34%→80.2%, "완벽히 못 풀 거야" 도전 115%↑, 상세 페르소나 24%→84%, 공손 표현은 토큰 낭비. 전체 기법을 합친 만능 템플릿 제시. | 인센티브 프롬프팅, 200달러 팁, EmotionPrompt, 심호흡 프롬프트 OPRO, ExpertPrompting, Bsharat 26 원칙, GSM8K 80.2%, 도전 프롬프트 115%, 프레이밍 효과, 자체 신뢰도 평가 |
| [[20260228_221030_앤트로픽 AI 에이전트의 가장 큰 숨겨진 비용 자동 프롬프트 캐싱을 마침내 해결하다]] | Claude API의 자동 프롬프트 캐싱(최상위 cache_control ephemeral 하나로 가장 긴 접두사 자동 탐지) 출시로 50턴 세션의 500k 정적 토큰 오버헤드가 58.5k로 감소(캐시 히트 비용은 정가 10%). Claude Code 팀 5가지 운영 원칙: 정적→동적 순서 고정, 세션 중간 도구 교체 금지(EnterPlanMode/ExitPlanMode를 별도 도구로), 시스템 프롬프트 편집 대신 `<system-reminder>` 태그, 모델 전환 금지(캐시 고유), 압축 시 캐시 안전 포킹. | 자동 프롬프트 캐싱, cache_control ephemeral, 접두사 일치, 캐시 히트 10% 가격, EnterPlanMode ExitPlanMode, 캐시 안전 포킹, system-reminder 태그, Thariq, prefill decode 단계, Joe Njenga |
| [[20260228_222127_Gemini 3를 GPT-4처럼 사용하고 있다면 실패할 수밖에 없습니다]] | Gemini 3가 GPT-4식 "바이브 프롬프팅"에 실패하는 이유를 분석한 프롬프팅 매뉴얼. 추론 아키텍처가 temperature=1.0에 조정돼 낮추면 무한 루프, 장황한 정중함은 노이즈로 간주, 100만 토큰 컨텍스트에는 "연결 구문"이 필요. XML 태그로 role/context/task/constraints 분리 시 지시 준수율 40% ↑, 코딩에서는 Gemini 3 Flash(SWE-bench 78%)가 Pro(76.2%)를 앞서며 "코드 삭제 버그" 없음. return_thought_signatures·media_resolution=high는 필수 API 파라미터. | Gemini 3 프롬프팅, XML 태그 40% 향상, temperature 1.0 강제, Thought Signatures, media_resolution high, Gemini 3 Flash SWE-bench 78%, 코드 삭제 버그, LMArena Elo 1500, GPQA Diamond 91.9%, 연결 구문 Bridge Phrase |
| [[20260301_184306_컨텍스트 엔지니어링 2026년에 실제로 중요한 6가지 기술 종합 가이드]] | Towards AI가 2026년 운영 시스템을 위한 컨텍스트 엔지니어링 6대 기법을 정리. 프롬프트 엔지니어링은 모델이 '어떻게 말할지'를, 컨텍스트 엔지니어링은 '무엇을 볼지'를 제어한다는 구분 아래 선택적 검색(크로스-인코더 재순위화+코사인 0.9 중복 제거+메타데이터 필터)이 첫 번째. "중간에서 길을 잃는(lost in the middle)" 효과, 노이즈 제거 시 15~30% 정확도 향상 및 20~40% 토큰 절약 수치를 제시. | 컨텍스트 엔지니어링, context engineering, lost in the middle, 크로스-인코더 재순위화, 코사인 유사도 0.9, 작업 인식 필터, Towards AI, 동적 컨텍스트 파이프라인 |
| [[20260301_202953_200달러 프롬프트 실험 우연히 Claude를 45 더 똑똑하게 만들었습니다 방법은 다음과 같습니다]] | 파일 20260228_211253과 동일 원본(medium ichigoSan)의 재번역본. 인센티브/감정/도전/심호흡/페르소나 5가지 심리적 프롬프팅을 arXiv 5편(EmotionPrompt 2307.11760, OPRO 2309.03409, Bsharat 26원칙 2312.16171, ExpertPrompting 2305.14688, Role-Play NAACL 2024)으로 뒷받침, GSM8K 34→80.2%·도전 115%↑, 공손 표현은 토큰 낭비. | 인센티브 프롬프팅 재번역본, 200달러 팁, EmotionPrompt, 심호흡 OPRO, ExpertPrompting 상세 페르소나, Bsharat 26 원칙, GSM8K 80.2%, 자체 신뢰도 평가 0.9, Kitchen Sink 만능 템플릿, Thebes 실험 |
| [[20260402_073153_AI 시대 JSON은 저물고 있는가 JSON Is Dying in the AI Era]] | LLM과 구조화 데이터를 주고받을 때 JSON의 따옴표와 중괄호가 토큰 낭비를 만든다고 지적하며 TOON이라는 토큰 지향 포맷을 대안으로 제안한다. JSON 68토큰 대비 TOON 48토큰, 배열 예시에서는 1481 대 993 토큰으로 줄어드는 비교가 핵심 근거다. | TOON, token oriented object notation, JSON token cost, 68 vs 48 tokens, LLM serialization, structured prompting |
| [[20260404_105021_하네스 엔지니어링 vs 컨텍스트 엔지니어링 모델은 CPU 하네스는 OS다]] | 컨텍스트 엔지니어링이 단일 추론의 입력 설계라면, 하네스 엔지니어링은 전체 에이전트 운영체제를 설계하는 일이라는 구분을 체계적으로 정리한다. SWE-agent, SWE-Bench Mobile, Stripe 사례를 바탕으로 hooks, lint, structural tests, garbage collection, failure log 같은 시스템 레벨 가드레일이 모델 성능보다 더 큰 차이를 만든다고 설명한다. | harness engineering, context engineering, SWE-agent, SWE-Bench Mobile, Stripe AI PRs, garbage collection agent, lifecycle hooks, architectural constraints |
| [[20260404_185234_Stop Writing Blob-Prompts Anthropics XML Tags Turn Claude Into a Contract Machine]] | 구조 없는 blob prompt 대신 XML 태그로 문맥, 지시, 예시, 포맷을 분리하면 형식 오류율이 내려간다는 Anthropic식 프롬프트 설계법을 설명한다. `<instructions>`, `<context>`, `<formatting>`, `<examples>`, `<thinking>` 같은 구획을 ‘계약서’처럼 써서 10회 실행 기준 format-break rate를 줄이는 것이 핵심이다. | XML tags, structured prompting, format-break rate, Anthropic prompt design, contract machine, blob prompts, prompt reliability |
| [[20260409_074603_AI 에이전트에게 필요한 5가지 기술 그리고 메가 프롬프트가 발목을 잡는 이유]] | 5만 토큰짜리 메가 프롬프트가 비용과 정확도를 동시에 망치는 문제를 지적하며, 스킬 아키텍처와 점진적 로딩으로 전환하자고 제안한다. YAML frontmatter가 있는 `SKILL.md`, 3단계 progressive disclosure, Pydantic AI 런타임 예시를 통해 크로스프레임워크 에이전트 구조를 설명한다. | mega prompt, SKILL.md, progressive disclosure, Pydantic AI, YAML frontmatter, lost-in-the-middle, agent skills, Micheal Lanham |
| [[20260409_075913_5000회 이상의 실패한 프롬프트 끝에 마침내 프리미엄 사진을 대체할 수 있는 프롬프트를 만들었습니다 지금 바로 따라해보세요]] | 5000회 이상의 이미지 프롬프트 실패 끝에 프리미엄 스톡 사진을 대체할 수 있는 프롬프트 조합을 찾았다는 실전 글이다. 이미지 생성 품질을 높이기 위한 반복 실험과 프롬프트 구조를 공유하며, 핵심은 생성 모델보다 프롬프트 설계에 있다고 주장한다. | 5000 prompt failures, premium photo prompt, image generation, prompt engineering, stock photo replacement, visual prompt recipe |
| [[20260412_082711_Unlock Claude AI의 초능력을 10가지 메가 프롬프트로 해제하세요]] | 연구 보고서, 백서, UI 설계처럼 복합 작업을 메가 프롬프트로 구조화하면 Claude 성능이 크게 높아진다고 설명한다. task-method-knowledge prompting의 성공률 30퍼센트에서 97퍼센트 이상, 비용 76퍼센트 절감 같은 수치를 근거로 역할, 제약, 산출물 형식 설계를 강조한다. | mega prompt, task-method-knowledge prompting, chain-of-thought, white paper, UI prompt, Claude, 97% success rate, 76% cost reduction |
| [[20260421_073557_AI 에이전트를 위한 풀스택 컨텍스트 엔지니어링]] | 컨텍스트 엔지니어링을 단순 메모리 저장이 아닌 상태 정의, 주입, 실시간 추출, 통합, 평가까지 이어지는 파이프라인으로 다룬다. OpenAI Agents SDK와 LiteLLM 라우팅, writer-critic 패턴, 장기 메모리 통합을 포함한 풀스택 설계가 핵심이다. | contextual engineering, OpenAI Agents SDK, LiteLLM, writer-critic, long-term memory, evaluation engine |
| [[앤스로픽 AI 에이전트의 최대 숨은 비용 문제를 해결하다 자동 프롬프트 캐싱]] | Anthropic 에이전트 운영에서 숨어 있던 토큰 비용을 줄이기 위해 자동 프롬프트 캐싱을 적용하는 방법을 다룬다. 긴 컨텍스트를 반복 재전송하는 문제를 캐시 계층으로 완화해 비용과 지연을 동시에 낮추는 엔지니어링 패턴이 핵심이다. | prompt caching, Anthropic, agent cost optimization, token reuse, context cache, latency reduction |

## 관련 카테고리

* > 상위: [[AI_Engineering Index]]
* > 최상위: [[wiki_index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
