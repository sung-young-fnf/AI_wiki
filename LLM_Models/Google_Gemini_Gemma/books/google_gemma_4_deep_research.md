# Google Gemma 4 딥 리서치

> 조사일: 2026-04-04 | 발표일: 2026-04-02

## 개요

Google이 **2026년 4월 2일** 발표한 차세대 오픈 모델 패밀리. Gemini 3와 동일한 연구/기술 기반으로 구축되었으며, **Apache 2.0 라이선스**로 공개되어 상업적 사용에 제한이 없다.

---

## 모델 라인업

| 모델 | 파라미터 | 특징 |
|------|----------|------|
| **E2B** | ~2.3B (엣지) | 모바일/IoT 디바이스용, 텍스트+이미지+오디오 입력 |
| **E4B** | ~4B (엣지) | 모바일/엣지용, 텍스트+이미지+오디오 입력 |
| **26B A4B (MoE)** | 총 26B / 활성 4B | Mixture-of-Experts, 4B만 활성화하여 속도↑ |
| **31B Dense** | 31B | 풀 Dense 모델, 256K 컨텍스트 윈도우 |

- **E2B/E4B**: Per-Layer Embeddings(PLE) 기술로 파라미터 효율 극대화
- **26B A4B**: 추론 시 4B 파라미터만 활성화 → 4B급 속도로 26B급 성능

---

## 핵심 기능

- **멀티모달**: 텍스트, 이미지, 비디오(프레임 시퀀스), 오디오(E2B/E4B) 입력 지원
- **에이전틱 워크플로우**: 네이티브 function-calling, 구조화된 JSON 출력, 시스템 인스트럭션 지원
- **140개+ 언어** 지원
- **오프라인 실행**: 폰, Raspberry Pi, NVIDIA Jetson 등에서 near-zero latency로 동작
- **어텐션 구조**: 슬라이딩 윈도우(512-1024 토큰) + 글로벌 풀 컨텍스트 어텐션 교차 배치

---

## 아키텍처 상세

### Dense 모델 (31B)

- 31B 파라미터 Dense Transformer
- 256,000 토큰 입력 컨텍스트
- 1,024 토큰 슬라이딩 윈도우
- 텍스트, 오디오, 비전, 비디오 입력 처리

### MoE 모델 (26B A4B)

- 총 26B 파라미터 중 추론 시 **4B만 활성화**
- "A"는 Active Parameters를 의미
- Dense 31B 대비 훨씬 빠른 추론 속도 (4B급)
- 동급 성능 대비 추론 비용 대폭 절감

### 엣지 모델 (E2B / E4B)

- **Per-Layer Embeddings (PLE)**: 각 디코더 레이어마다 별도의 작은 임베딩 테이블 보유
- 임베딩 테이블은 빠른 룩업 전용 → 실효 파라미터 수가 총 파라미터보다 훨씬 작음
- RAM과 배터리 소모 최소화 설계

### 공통 어텐션 구조

- 레이어별로 **로컬 슬라이딩 윈도우 어텐션**(512-1024 토큰)과 **글로벌 풀 컨텍스트 어텐션** 교차 배치
- 효율성과 장거리 이해력 사이의 균형

---

## 벤치마크 성과

| 벤치마크 | Gemma 4 31B | 비고 |
|----------|-------------|------|
| AIME 2026 (수학) | **89.2%** | 오픈모델 최고 수준 |
| LiveCodeBench (코딩) | **~80%** | |
| GPQA (과학추론) | **~84%** | |
| Arena AI 텍스트 리더보드 | **오픈모델 3위** | 31B Dense |
| Arena AI 텍스트 리더보드 | **오픈모델 6위** | 26B MoE |

Gemma 3 대비 모든 벤치마크에서 극적 향상.

---

## 경쟁 모델 비교 (2026년 4월 기준)

| 항목 | Gemma 4 | Qwen 3.5 | Llama 4 Scout |
|------|---------|----------|---------------|
| **수학/코딩** | 최강 (AIME 89.2%) | 코딩 벤치 일부 우위 | - |
| **추론** | 우수 | - | 최강 (109B 지식용량) |
| **다국어** | 140개+ | 201개+ (최강) | - |
| **컨텍스트** | 256K | - | **10M (압도적)** |
| **라이선스** | Apache 2.0 | Apache 2.0 | 제한적 |
| **엣지 배포** | 최강 (2B~4B 엣지 전용) | - | - |

---

## 배포 및 사용

### 다운로드

- Hugging Face
- Kaggle
- Ollama

### 실행 도구

- vLLM, Ollama, llama.cpp, Unsloth, LM Studio

### 하드웨어 지원

- **NVIDIA**: Blackwell 데이터센터 ~ Jetson 엣지 디바이스
- **AMD**: GPU/CPU Day 0 지원
- **안드로이드**: AICore Developer Preview를 통한 온디바이스 배포

---

## 핵심 시사점

1. **Apache 2.0 전환** — MAU 제한 없이 완전 자유로운 상업적 사용. 벤치마크보다 더 큰 전략적 의미.
2. **엣지 AI 리더십** — 2B/4B 엣지 모델은 오프라인에서 멀티모달+에이전틱을 지원하는 유일한 수준.
3. **MoE 아키텍처 도입** — 성능 대비 추론 비용 대폭 절감. 26B 모델이 자신보다 20배 큰 모델을 능가.
4. **Gemma 3 대비 극적 향상** — "dramatically better in every way" (Latent Space).

---

## 참고 자료

- [Gemma 4: Byte for byte, the most capable open models (Google Blog)](https://blog.google/innovation-and-ai/technology/developers-tools/gemma-4/)
- [Gemma 4 — Google DeepMind](https://deepmind.google/models/gemma/gemma-4/)
- [Gemma 4 and what makes an open model succeed (Interconnects)](https://www.interconnects.ai/p/gemma-4-and-what-makes-an-open-model)
- [Welcome Gemma 4 (Hugging Face Blog)](https://huggingface.co/blog/gemma4)
- [Google announces open Gemma 4 model with Apache 2.0 license (9to5Google)](https://9to5google.com/2026/04/02/google-gemma-4/)
- [Gemma 4 vs Qwen 3.5 vs Llama 4: Updated Benchmarks (ai.rs)](https://ai.rs/ai-developer/gemma-4-vs-qwen-3-5-vs-llama-4-compared)
- [NVIDIA: Bringing AI Closer to the Edge with Gemma 4](https://developer.nvidia.com/blog/bringing-ai-closer-to-the-edge-and-on-device-with-gemma-4/)
- [Google releases Gemma 4 under Apache 2.0 (VentureBeat)](https://venturebeat.com/technology/google-releases-gemma-4-under-apache-2-0-and-that-license-change-may-matter)
- [AINews: Gemma 4 best small multimodal open models (Latent Space)](https://www.latent.space/p/ainews-gemma-4-the-best-small-multimodal)
