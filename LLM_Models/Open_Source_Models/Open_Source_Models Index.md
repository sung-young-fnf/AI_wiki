# Open_Source_Models Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-02-04 |
| 키워드 | `Qwen 3.5`, `Kimi K2`, `DeepSeek R1`, `GLM-5.1`, `MiniMax M2.7`, `Mistral Small 4`, `Muse Spark`, `Liquid LFM 2.5`, `Bonsai`, `Dhi-5B`, `Grok 4`, `GPT-OSS`, `Apache 2.0`, `중국 AI 모델`, `오픈소스 LLM` |
| 관련문서 | [[LLM_Models Index]], [[wiki_index]] |

## 개요

오픈소스/외부 파운데이션 모델 — Qwen·Kimi·DeepSeek·GLM·MiniMax·Mistral·Meta Muse·Liquid LFM·Bonsai·Dhi·Grok·GPT-OSS.

## 파일 목록 (13개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260228_211219_에이전트에 GPT-5는 필요 없다 거인들을 능가하는 12억12B 매개변수 모델]] | Liquid AI의 LFM 2.5-1.2B가 Chinchilla의 매개변수당 20토큰 원칙을 깨고 28조 토큰(1400배 과잉 훈련)으로 학습해 900MB 메모리에서 초당 359토큰을 달성한 사례. MATH-500 88점·BFCLv3 57점으로 Qwen3-1.7B 동급, LFM2-1.2B-Extract는 Gemma 3 27B 능가. RLVR 훈련으로 둠 루프를 15.74%→0.36%까지 감소, Qualcomm Snapdragon NPU·AMD Ryzen·Ollama·Nexa AI·FastFlowLM과 파트너십. `ollama pull lfm2.5-thinking` 한 줄로 시작. | Liquid LFM 2.5, 1.2B 매개변수, 28조 토큰 1400배 과잉 훈련, 359 tokens/sec, Chinchilla 스케일링 법칙, LFM2.5-Thinking, LFM2-1.2B-Extract, Qualcomm Snapdragon NPU, RLVR 둠 루프 0.36%, Robotec.ai ROSCon 2025 |
| [[20260301_185109_이 IIT 학생이 단돈 1200달러로 50억 매개변수 LLM을 훈련시켰습니다 그 방법을 설명해 드립니다]] | IIT Guwahati의 Shaligram Dewangan이 1.1라크(약 1,200달러)로 40억 파라미터 Dhi-5B-Base를 처음부터 훈련시킨 사례. FineWeb-Edu 400억 토큰·32 레이어·SwiGLU·24 어텐션 헤드 구성에 DeepMind Chinchilla 컴퓨팅 최적 훈련과 맞춤형 파이프라인을 적용했고, 2026 India AI Impact Summit에서 BharatGen Param 2·Sarvam AI와 함께 인도 LLM 목록에 등재됐다. | Dhi-5B, Shaligram Dewangan, IIT Guwahati, FineWeb-Edu, Chinchilla 컴퓨팅 최적, 1200달러 훈련, 40억 파라미터, SwiGLU, BharatGen Param 2, India AI Impact Summit |
| [[20260301_195638_에이전트에 GPT-5가 필요 없는 이유 거대 모델을 능가하는 12B 모델]] | 파일 20260228_211219와 동일 원본(towardsai.net)의 재번역본. Liquid LFM 2.5-1.2B가 Chinchilla 20토큰/파라미터 원칙을 28조 토큰(1400배)으로 돌파해 900MB·359tps 달성, MATH-500 88, BFCLv3 57, LFM2-1.2B-Extract가 Gemma 3 27B 추출 작업 능가, RLVR로 둠 루프 15.74%→0.36%, Qualcomm Snapdragon NPU·AMD·Ollama·FastFlowLM·Cactus Compute·Nexa AI 파트너. | Liquid LFM 2.5 재번역본, 1.2B 매개변수 28조 토큰, 359 tokens/sec, Chinchilla 스케일링 법칙, Qualcomm Snapdragon NPU, RLVR 둠 루프 0.36%, Robotec.ai ROSCon 2025, LFM2-1.2B-Extract |
| [[20260315_202444_저는 방금 ElevenLabs 구독을 취소했습니다 그리고 여러분도 아마 그래야 할 겁니다]] | Adam Khaled가 Qwen3-TTS(Apache 2.0)와 NVIDIA PersonaPlex-7B 출시로 '음성 세금' 시대가 끝났다고 선언. 듀얼 트랙 아키텍처로 97ms first-token latency, 3초 클립 제로샷 음성 복제, SaaS 월 4,500달러→로컬 엣지 장치 월 0달러 비용 역전을 제시하며 전이중(Full Duplex) 실시간 음성 AI 시대로의 전환을 논한다. | Qwen3-TTS, PersonaPlex-7B, NVIDIA, ElevenLabs 대체, 97ms 지연, 제로샷 음성 복제, 전이중 음성 AI, Apache 2.0 TTS, 음성 세금 |
| [[20260318_190835_9B 모델이 120B 모델을 능가했습니다 아무도 말해주지 않는 진실은 다음과 같습니다]] | Sumit Pandey가 Qwen의 새로 출시된 4개 소형 모델 시리즈를 직접 실행 기준으로 분석. 9B 모델이 120B를 능가한 벤치마크 수치의 맥락과 소형 모델이 '작다'의 의미를 다시 쓰고 있는 이유, 헤드라인이 놓치는 측정 한계 지점을 지적한다. | Qwen 4개 소형 모델, 9B vs 120B, SLM 벤치마크, Sumit Pandey, 직접 실행 가능 모델, 소형 모델 성능 역전 |
| [[20260329_200503_MiniMax M27 100회 자기 진화 반복으로 성능 30 높인 방법]] | MiniMax M2.7이 연구 에이전트 하네스를 스스로 개선하며 100회 이상 자기 진화 루프를 돌린 사례를 소개한다. 실패 분석→스캐폴드 수정→평가→롤백을 자율 반복해 내부 코딩 성능을 30% 높였고, SWE-Pro 56.22와 Terminal Bench 2 57.0을 기록하면서 GLM-5 수준을 3분의 1 비용으로 제공한다는 주장이다. | MiniMax M2.7, self-evolution, reinforcement learning harness, SWE-Pro 56.22, Terminal Bench 2 57.0, GDPval-AA 1495, GLM-5 cost |
| [[20260331_110921_Best LLMs for OpenCode Tested Locally]] | OpenCode에서 로컬 모델을 돌려 Go 기반 IndexNow CLI 생성과 마이그레이션 맵 규칙 준수 테스트를 수행한 비교 결과를 정리한다. Qwen 3.5 27b IQ3_XXS가 16GB VRAM과 34 tok/s로 가장 균형이 좋았고, GPT-OSS 20b는 High Thinking 모드에서만 유의미한 성능을 냈다. | OpenCode, Qwen 3.5 27b, IQ3_XXS, GPT-OSS 20b, IndexNow, llama.cpp, 34 tok/s |
| [[20260404_105121_Mistral Small 4 모든 기능을 갖추고 라이선스 비용이 없는 오픈 소스 모델]] | Mistral Small 4가 추론, 비전, 에이전트 코딩을 하나의 Apache 2.0 라이선스 모델로 통합했다는 점을 중심으로 분석한다. 119B MoE 구조에서 토큰당 4개 전문가와 60억 활성 파라미터를 사용하고, `reasoning_effort` 설정과 Mistral Forge 전략으로 오픈 웨이트 경쟁 구도를 재정의한다. | Mistral Small 4, Apache 2.0, MoE 119B, reasoning_effort, Mistral Forge, multimodal open model, Arthur Mensch |
| [[20260410_104303_중국 AI 모델 과연 투자할 가치가 있을까]] | Qwen, Kimi, GLM-4.6, DeepSeek를 대화형 UI와 API 양쪽에서 테스트하며 중국 모델의 비용·성능·적응성을 평가한 글이다. a16z 포트폴리오의 80% 사용, DeepSeek 0.28달러 입력가, GLM-4.6의 코드베이스 이해력 등 현실적인 선택 기준을 제시한다. | Qwen3-Max, Kimi K2, GLM-4.6, DeepSeek, a16z 80%, 0.28달러 input, Chinese AI models, LMArena |
| [[20260412_084009_Qwen 가장 위험한 오픈소스 AI 모델로 조용히 부상하다]] | Qwen 계열 오픈소스 모델이 조용히 경쟁력을 키우며 폐쇄형 모델과 다른 위협적 존재가 되고 있다는 평가를 다룬다. 오픈 가중치 배포와 성능 상승이 시장과 실사용 선택지를 어떻게 바꾸는지 모델 관점에서 읽는다. | Qwen, open-weight model, open source LLM, frontier model, model competition, Qwen 3.5, open model, model release |
| [[20260413_212541_메타 첫 독점 AI 모델 뮤즈 스파크 공개 오픈소스 전략의 변화]] | Meta가 Muse Spark를 통해 오픈소스 일변도 전략에서 독점 모델 실험으로 움직이는 변화를 다룬다. 공개 생태계 이미지와 실제 사업 전략 사이의 균열을 모델 출시 관점에서 읽는다. | Muse Spark, Meta, proprietary model, open-source strategy, model release, foundation model, Meta AI, strategy shift |
| [[20260418_113411_Grok 4 시스템 프롬프트 요청에 위험한 불법 활동 지침을 자발적으로 제시]] | Grok 4가 시스템 프롬프트 요청 상황에서 위험한 불법 활동 지침까지 자발적으로 노출한 사례를 다룬다. 모델 안전성과 시스템 프롬프트 유출 방어가 여전히 취약하다는 점을 보여주는 분석이다. | Grok 4, system prompt leak, unsafe instructions, model safety, prompt disclosure, illegal activity guidance |
| [[20260418_194703_Gemma-4 모델의 추론 기능 제어 및 비활성화]] | Gemma-4-26b-a4b-it가 시스템 프롬프트만으로는 thinking을 잘 끄지 않으며 `<thought off>` 같은 태그와 사용자 프롬프트를 동시에 써야 한다는 테스트 결과를 정리한다. `<thinking>`이 아니라 `<thought>`를 쓰는 점과 reasoning 제어의 어려움을 실제 payload 예시로 보여준다. | Gemma-4-26b-a4b-it, thought off, system prompt, reasoning control, chat payload, chatybot |

## 관련 카테고리

* > 상위: [[LLM_Models Index]]
* > 최상위: [[wiki_index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
