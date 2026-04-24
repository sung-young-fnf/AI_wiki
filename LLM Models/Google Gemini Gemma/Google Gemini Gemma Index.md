# Google Gemini Gemma Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-02-02 |
| 키워드 | `Gemma 4`, `Gemini 3`, `Demis Hassabis`, `Apache 2.0`, `MoE`, `26B-A4B`, `31B Dense`, `256K context`, `multimodal`, `Per-Layer Embeddings`, `Gemma 4 visual guide`, `Gemini prompting` |
| 관련문서 | [[LLM Models Index]], [[ArticleScrap Platform Index]] |

## 개요

Google 산하 모델 — Gemma 4 시리즈(MoE/multimodal/Apache 2.0/tooling)·Gemini 3 출시·Gemini 프롬프팅·Demis Hassabis.

## 파일 목록 (16개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260318_214721_Gemini 3를 GPT-4처럼 사용하고 계시다면 실패할 수밖에 없습니다]] | Adham Khaled(Towards AI)가 Gemini 3를 GPT-4 사용법으로 다루면 실패하는 이유와 '탈 바이브(Post-Vibe)' 시대 Gemini 3 전용 사용 설명서를 제시한 프롬프팅 가이드. | Gemini 3 프롬프팅, Post-Vibe, GPT-4 vs Gemini 3, Adham Khaled, Towards AI, Gemini 3 사용 설명서, 프롬프트 스타일 차이 |
| [[20260329_193614_2026년 최고의 오픈소스 LLM]] | 2026년 오픈 웨이트 모델이 장문 컨텍스트와 에이전트형 추론에서 폐쇄형 모델을 빠르게 따라잡고 있다는 관점에서 주요 모델을 비교한다. GLM 4.6의 200K 컨텍스트, Qwen3-235B의 1M 컨텍스트, gpt-oss-120B·DeepSeek-R1·Kimi-K2·Nemotron·Mistral Small의 강점을 통해 미래 경쟁력은 모델 소유보다 배포 아키텍처에 있다는 결론을 제시한다. | GLM 4.6, gpt-oss-120B, Qwen3-235B, DeepSeek-R1-0528, DeepSeek-V3.2-Exp, Kimi-K2-Instruct-0905, Nemotron-Super-49B, Mistral-Small-3.2 |
| [[20260404_185104_Gemma 4 Googles Open Source Drop]] | Google의 Gemma 4 공개를 중심으로 오픈소스 모델 라인업 변화와 성능 포지션을 다룬다. 새 오픈 웨이트 모델의 배포 전략, 경쟁 모델과의 위치, 개발자 활용 가능성을 요약하는 출시 분석 글이다. | Gemma 4, Google open model, open-source drop, open weights, model release |
| [[20260405_123733_Gemma 4 구글이 개발자를 위해 공개한 새로운 오픈소스 AI 모델]] | Gemma 4를 Apache 2.0 라이선스, 네이티브 함수 호출, 128K~256K 컨텍스트, 멀티모달 입력을 갖춘 개발자용 오픈 모델 패밀리로 소개한다. E2B부터 31B까지 다양한 크기와 Ollama, Hugging Face, LM Studio, Claude Code 연동 경로를 함께 정리해 실무 도입 관점이 강하다. | Gemma 4, Apache 2.0, 31B Dense, 26B MoE, 256K context, Ollama, Claude Code integration |
| [[20260405_123753_Gemma 4 구글의 오픈 소스 회심작과 커뮤니티의 폭발적인 반응]] | Gemma 4의 E2B, E4B, 31B, 26B A4B 라인업과 PLE, Shared KV Cache, Dual RoPE 같은 구조 개선을 해설한다. MLX 96개 양자화, Unsloth 19개 패키지, ONNX WebGPU 지원처럼 데이제로 커뮤니티 반응이 강력했다는 점을 강조한다. | Gemma 4, PLE, Shared KV Cache, Dual RoPE, MLX quantization, Unsloth, WebGPU ONNX |
| [[20260405_123814_Google Just Dropped Gemma 4 for Free The Tooling Is Still Broken]] | Gemma 4가 Apache 2.0과 강한 벤치마크 성능을 갖췄지만, llama.cpp의 ubatch 충돌과 Transformers, PEFT 호환성 문제 때문에 초기 툴체인이 아직 불안정하다고 정리한다. transformers 개발 브랜치, Unsloth, Ollama RC를 우회책으로 제시하며 실제 배포 시 주의점을 모아 준다. | Gemma 4 tooling, llama.cpp ubatch, Transformers dev branch, Gemma4ClippableLinear, Unsloth, Ollama rc1 |
| [[20260405_124113_Gemma 4 비주얼 가이드 온디바이스부터 대규모 추론까지 아우르는 차세대 멀티모달 LLM]] | Gemma 4의 E2B, E4B, 31B, 26B A4B를 아우르며 PLE, interleaving attention, K=V, p-RoPE 같은 기술 선택이 왜 온디바이스와 대규모 추론을 동시에 겨냥하는지 해설한다. 네이티브 오디오, 가변 해상도 비전, 4B 활성 파라미터 MoE로 효율과 실용성을 동시에 추구한 모델 패밀리라는 분석이다. | Gemma 4 visual guide, PLE, interleaving attention, K=V, p-RoPE, native audio, 26B A4B |
| [[20260405_124219_Google releases Gemma 4 open models]] | Gemma 4의 31B Dense, 26B-A4B MoE, E4B, E2B를 비교하며 Apache 2.0과 네이티브 오디오 지원이 오픈 모델 생태계에 미치는 의미를 분석한다. AIME 2026, LiveCodeBench 향상과 동시에 HLE 도구 활용은 Qwen 3.5보다 뒤처진다는 균형 잡힌 관점이 특징이다. | Gemma 4, 31B Dense, 26B-A4B, Apache 2.0, AIME 2026, LiveCodeBench, HLE benchmark |
| [[20260409_074811_Google의 Gemma 4 출시 오픈 AI의 규칙을 다시 쓰다]] | Google DeepMind가 2026년 4월 2일 공개한 Gemma 4를 Apache 2.0, E2B와 E4B 엣지 모델, 31B Dense와 26B MoE 축으로 분석한 글이다. AIME 2026 89.2%, Codeforces 2150, Pixel용 Gemini Nano 4 기반 등 성능과 배포 전략을 통해 Google이 오픈 모델 시장 규칙을 다시 쓰고 있다고 본다. | Gemma 4, Apache 2.0, AIME 2026 89.2%, Codeforces 2150, Demis Hassabis, E4B, 31B Dense, 26B MoE |
| [[20260412_084058_Googles Gemma 4 Changes Everything for Open Source AI]] | Gemma 4가 오픈소스 생태계에서 갖는 의미를 성능, 접근성, 실사용 가능성 측면에서 해석한다. Google의 공개 모델 전략이 Qwen과 DeepSeek 중심 판을 어떻게 흔드는지에 초점을 둔다. | Gemma 4, Google, open source AI, open-weight model, model release, benchmark, open model ecosystem, frontier model |
| [[20260413_150248_4가지 Gemma 4 모델 테스트 26B 모델의 최고의 편법]] | Gemma 4 라인업 4개를 비교하고 26B 모델이 갖는 실전 장점을 테스트 중심으로 분석한다. 모델 크기별 차이와 우회적 운용법을 통해 공개 모델 선택 기준을 제시한다. | Gemma 4, 26B model, model comparison, benchmark, model lineup, open-weight model, inference tradeoff, Gemma testing |
| [[20260413_212723_Gemma 4 시작하기 다중 모드 및 다국어 AI 실용 가이드]] | Gemma 4의 멀티모달과 다국어 특성을 입문자 관점에서 정리한 실용 가이드다. 출시 직후 어떤 역량이 추가되었고 어떤 사용 사례에서 경쟁력이 있는지 모델 자체에 초점을 맞춘다. | Gemma 4, multimodal, multilingual, model guide, Google, open-weight model, model capabilities, practical guide |
| [[20260418_113129_Gemma 4와 Claude GPT-54 비교 테스트 Python 개발 현장에서의 실제 성능]] | Python 개발 실무에서 Gemma 4와 Claude GPT-5.4의 코딩 성능을 직접 비교한 글이다. 모델 크기나 벤치마크보다 실제 코드 생성과 수정 품질을 기준으로 두 모델의 장단점을 살펴본다. | Gemma 4, GPT-5.4, Python coding benchmark, model comparison, real-world coding test |
| [[20260418_193736_구글의 혁신적인 오픈소스 AI 모델 Gemma 4 왜 아무도 주목하지 않는가]] | Gemma 4가 Apache 2.0 라이선스와 e2b·e4b·26b·31b 라인업, 오디오·비전 지원, 256K 컨텍스트를 제공하면서도 조용히 공개됐다는 점을 강조한다. Raspberry Pi 오프라인 실행과 완전 개방 라이선스를 근거로 2026년 가장 중요한 오픈 모델 발표 중 하나로 평가한다. | Gemma 4, Apache 2.0, e2b, e4b, 31b, Raspberry Pi, 256K context |
| [[gemma4_comprehensive_summary]] | Gemma 4 제품군과 구조적 특징, 로컬 실행성과 생태계 반응을 종합 정리한 문서다. E2B, E4B, 26B A4B, 31B 모델과 Apache 2.0 전환, Ollama와 MLX 지원, M1 32GB 운용 팁까지 함께 다룬다. | Gemma 4, E2B, E4B, 26B A4B, 31B Dense, Apache 2.0, MLX, Ollama |
| [[google_gemma_4_deep_research]] | Google Gemma 4의 모델 라인업과 아키텍처, 배포 경로를 심층 정리한 리서치 문서다. 256K 컨텍스트, Per-Layer Embeddings, multimodal 입력, Ollama와 vLLM 지원처럼 실제 도입에 필요한 세부 정보가 중심이다. | Google Gemma 4, Per-Layer Embeddings, 256K context, multimodal, vLLM, Ollama, Apache 2.0 |

## 관련 카테고리

* > 상위: [[LLM Models Index]]
* > 최상위: [[ArticleScrap Platform Index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
