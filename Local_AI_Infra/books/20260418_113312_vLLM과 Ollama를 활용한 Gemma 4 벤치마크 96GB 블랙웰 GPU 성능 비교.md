# 번역 기사

- **원본 URL:** https://allenkuo.medium.com/gemma-4-on-vllm-vs-ollama-benchmarks-on-a-96-gb-blackwell-gpu-804ca4845a21
- **번역 일시:** 2026-04-18 11:33:12

---

# vLLM과 Ollama를 활용한 Gemma 4 벤치마크: 96GB 블랙웰 GPU 성능 비교

업데이트 (2026-04-17): 이 글의 BF16과 Q4_K_M 비교에는 알려진 정밀도 비대칭성 문제가 있으며, 이는 '핵심 교훈 #2'에서 언급되었으나 완전한 NVFP4 비교는 보류되었습니다. 후속 연구에서 8계층 설치 과정, 공정한 벤치마크, 엔진 비교에 대해 다루었으며, 본문의 벤치마크는 '기본 설정 vLLM' 기준으로 유효합니다.

Google의 Gemma 4 제품군(E4B (8B), 26B MoE, 31B Dense)이 최근 출시되었으며, 필자는 동일한 RTX PRO 6000 Blackwell 워크스테이션에서 이 세 모델 모두를 vLLM과 Ollama를 사용하여 벤치마크했습니다. 그 결과는 각 엔진이 언제 탁월한 성능을 발휘하는지에 대한 명확한 시사점을 제공합니다.

**요약:** vLLM은 첫 토큰 생성 시간(TTFT, Time to First Token)(3배 더 빠름)과 동시 처리량(3배 더 높음)에서 우위를 보였습니다. Ollama는 단일 사용자 디코딩 속도(1.5배 더 빠름)에서 강세를 보였습니다. 놀랍게도 26B MoE (Mixture of Experts) 모델은 vLLM에서 더 작은 E4B 모델보다 빠르며, 4B급 속도로 26B급 지능을 제공하는 MVP로 나타났습니다.

### 테스트 설정

*   **하드웨어:** NVIDIA RTX PRO 6000 Blackwell Workstation Edition (VRAM 97,887 MiB, 전력 제한 400W)
*   **소프트웨어:**
    *   vLLM 0.19.1rc1 (WSL2 Ubuntu 24.04, BF16 정밀도 (precision), CUDA 그래프 (CUDA graphs) 활성화)
    *   Ollama 최신 안정 버전 (Windows 11 네이티브, Q4_K_M 양자화 (quantization))
*   **공통 프롬프트:** "uvx와 nvitop이 무엇인지 4가지 간결한 요약으로 설명해 주세요."
*   **설정:** `max_tokens=256`, `temperature=0`, 각 3회 예비 실행
*   **주요 차이점:** vLLM은 모델을 BF16 (16비트, 전체 정밀도)로 실행하는 반면, Ollama는 Q4_K_M (GGUF를 통한 4비트 양자화)을 사용합니다. 이는 동일한 가중치 (weight) 비교가 아니며, 각 엔진이 기본적으로 사용하는 방식을 반영합니다.

### Gemma 4 실행

**Ollama (Windows 네이티브):** 한 번의 명령으로 Q4_K_M GGUF 모델을 자동 다운로드하여 별도의 설정 없이 실행 가능합니다.

**vLLM (WSL2):** Gemma 4는 `gemma4` 아키텍처를 사용하며 vLLM 0.19 이상 및 `transformers` 5.5.0 이상이 필요합니다. vLLM 0.19가 `transformers` 구 버전을 고정하는 문제가 있어, `transformers`를 별도로 업그레이드해야 하는 복잡성이 있습니다.

모델 다운로드는 HuggingFace에서 BF16 safetensors 형태로 이루어지며, 모델별 디스크 크기는 E4B 약 15GiB, 26B 약 49GiB, 31B 약 59GiB입니다.

vLLM 실행 시 `--gpu-memory-utilization 0.80`과 같은 설정을 통해 VRAM을 효율적으로 관리하고, `torch.compile` 및 CUDA 그래프 캡처로 인해 첫 시작에는 2~5분 정도 소요될 수 있습니다. Gemma 4의 이질적인 헤드 차원 (head dimensions)은 `TRITON_ATTN` 백엔드를 자동으로 사용하게 합니다.

### 결과 수치

#### 디코딩 속도 (초당 토큰 수, 단일 요청)

Ollama는 모든 모델에서 디코딩 속도에서 우위를 보였습니다. 이는 "텍스트가 얼마나 빨리 나타나는가"에 대한 지표입니다.

*   E4B — Ollama 196 tok/s vs vLLM 124 tok/s (Ollama 1.6배 빠름)
*   26B — Ollama 181 tok/s vs vLLM 131 tok/s (Ollama 1.4배 빠름)
*   31B — Ollama 58 tok/s vs vLLM 22 tok/s (Ollama 2.6배 빠름)

모델 크기가 커질수록 격차가 벌어집니다. 31B Dense 모델의 경우, BF16 가중치 (weights)는 59GiB로, GPU는 대부분의 시간을 메모리 버스 (memory bus)를 통해 데이터를 이동하는 데 소비합니다. Ollama의 Q4_K_M은 이를 약 18GiB로 줄여 엄청난 대역폭 (bandwidth) 이점을 제공합니다.

#### 첫 토큰 생성 시간 (TTFT, Time to First Token)

vLLM은 전반적으로 TTFT에서 우위를 보였습니다. 이는 "첫 글자가 나타나기까지 얼마나 걸리는가"에 대한 지표입니다.

*   E4B — vLLM 58ms vs Ollama 184ms (vLLM 3.2배 빠름)
*   26B — vLLM 68ms vs Ollama 194ms (vLLM 2.9배 빠름)
*   31B — vLLM 92ms vs Ollama 201ms (vLLM 2.2배 빠름)

58ms는 사람이 인지하기 어렵지만, 184ms는 눈에 띄는 일시 정지입니다. TTFT가 10~20단계에 걸쳐 곱해지는 에이전트 루프 (agent loops)에서는 몇 초의 차이로 누적됩니다.

#### 동시 처리량 (4개 병렬 요청)

이것이 vLLM 아키텍처 (architecture)의 진정한 차별점입니다. 연속 배치 (Continuous Batching)는 여러 요청이 동일한 GPU 배치 (batch)를 공유하도록 합니다.

*   E4B — vLLM 441 tok/s vs Ollama 124 tok/s (vLLM 3.6배 높음)
*   26B — vLLM 316 tok/s vs Ollama 124 tok/s (vLLM 2.5배 높음)
*   31B — vLLM 93 tok/s vs Ollama 58 tok/s (vLLM 1.6배 높음)

Ollama는 요청을 순차적으로 처리합니다(하나가 완료된 후 다음 요청 시작). vLLM은 요청들을 교차 (interleave) 처리합니다. 4명의 동시 사용자 환경에서 vLLM은 최대 3.6배의 전체 처리량을 제공합니다.

#### VRAM 사용량

이는 데스크톱 사용자에게 Ollama의 가장 강력한 이점입니다.

*   E4B — Ollama 29 GiB vs vLLM 89 GiB
*   26B — Ollama 51 GiB vs vLLM 90 GiB
*   31B — Ollama 83 GiB vs vLLM 90 GiB

vLLM은 시작 시 KV 캐시 풀 (KV cache pool)을 미리 할당하여 모델 크기와 상관없이 고정된 약 90GiB를 소비합니다 (`--gpu-memory-utilization 0.80` 설정 시). Ollama는 모델이 필요한 만큼만 사용합니다. ComfyUI, 게임 또는 다른 GPU 앱을 함께 실행하는 데스크톱 환경에서는 Ollama가 VRAM 여유 공간을 더 많이 남겨둡니다. vLLM은 VRAM을 독점하는 경향이 있습니다.

### 놀라운 결과: 26B MoE 모델, vLLM에서 E4B보다 빠르다

가장 예상치 못한 결과는 260억 개 파라미터 (parameters)를 가진 26B MoE 모델이 vLLM에서 131 tok/s로 디코딩되어, 80억 개 파라미터의 E4B 모델(124 tok/s)보다 빨랐다는 점입니다.

이는 아키텍처 (architecture)의 차이 때문입니다. Gemma 4 26B는 MoE (Mixture of Experts) 모델로, 총 260억 개의 파라미터 중 토큰당 약 40억 개만 활성화됩니다. 실제로 필요한 컴퓨팅 및 메모리 대역폭 (memory bandwidth)은 활성화되는 파라미터 수에 의해 결정됩니다.

Ollama에서도 비슷한 패턴이 관찰되었는데, 26B는 181 tok/s로 E4B의 196 tok/s에 근접했습니다. 이는 3배 더 큰 모델임에도 불구하고 놀라운 속도입니다. 약간의 속도 저하는 전문가 라우팅 (expert routing)을 위해 더 큰 가중치 (weight) 파일이 더 많은 메모리 대역폭을 필요로 하기 때문입니다.

실용적인 관점에서 26B MoE는 거의 4B급 속도로 26B급 지능을 제공합니다. (예를 들어, E4B가 uvx를 GPU 모니터링 도구로 착각한 반면, 26B MoE는 Python 도구 러너로 정확히 식별했습니다.) 이는 vLLM에서 5%, Ollama에서 8%만 더 느리게 실행되었습니다.

### 31B 모델의 문제: BF16이 해로울 때

31B Dense 모델은 데스크톱 하드웨어에서 vLLM의 약점을 드러냅니다. 22 tok/s의 디코딩 속도는 Ollama의 58 tok/s보다 2.6배 느려 매우 답답한 수준입니다.

근본적인 원인은 간단합니다. BF16으로 된 31B 모델은 약 59GiB의 가중치 (weights)를 필요로 합니다. 각 디코딩 단계 (decode step)에서 전체 가중치 행렬 (weight matrix)을 읽어야 합니다. RTX PRO 6000의 1.8 TB/s GDDR7 버스에서:

*   **BF16:** 59 GiB / 1.8 TB/s = 토큰당 33ms → 이론적 최대치 약 30 tok/s
*   **Q4_K_M:** 약 18 GiB / 1.8 TB/s = 토큰당 10ms → 이론적 최대치 약 100 tok/s

vLLM의 22 tok/s는 BF16의 이론적 한계에 가깝습니다. Ollama의 58 tok/s는 4비트 양자화 (quantization)를 통해 메모리 트래픽 (memory traffic)을 3.3배 줄인 덕분입니다.

단일 GPU에서 약 20B 이상의 파라미터 (parameters)를 가진 모델의 경우, 양자화는 선택 사항이 아니라 '사용 가능 여부'를 결정하는 중요한 요소입니다. vLLM이 이 분야에서 경쟁하려면 AWQ/GPTQ 양자화된 가중치 (quantized weights)를 지원해야 할 것입니다.

### 언제 무엇을 사용할 것인가

**vLLM + Gemma 4 26B MoE 사용 시기:**
*   에이전트 시스템 구축 (낮은 TTFT + 빠른 프리필 + 동시 다중 에이전트 (multi-agent))
*   API를 통한 다중 사용자 서비스
*   긴 컨텍스트 (context)를 사용하는 RAG (Retrieval Augmented Generation) 파이프라인 (pipelines) 실행
*   순수 타이핑 속도보다 품질이 더 중요할 때

**Ollama + Gemma 4 26B 사용 시기:**
*   단일 사용자 대화형 채팅 (181 tok/s는 즉각적인 느낌을 줍니다)
*   ComfyUI, 게임 등 다른 GPU 앱과의 데스크톱 동시 사용
*   빠른 실험 (한 번의 명령으로 설정 가능)
*   VRAM이 제한적일 때 (51 GiB vs 90 GiB)

**Ollama + Gemma 4 E4B 사용 시기:**
*   최대 디코딩 속도가 필요할 때 (196 tok/s)
*   최소 VRAM 점유 공간 (29 GiB)
*   틈새 주제에 대한 환각 (hallucination)이 허용 가능한 작업

**Gemma 4 31B 모델을 vLLM BF16으로 사용하는 것은 피해야 합니다.** 22 tok/s의 속도로는 경쟁력이 없습니다. 31B 모델은 Ollama (Q4_K_M으로 58 tok/s)를 사용하거나 AWQ/GPTQ 변형을 기다리는 것이 좋습니다.

### Qwen3.5 결과와의 비교

이전에 동일한 하드웨어에서 Qwen3.5–9B를 벤치마크했습니다. 그 패턴은 다음과 같이 일관됩니다.

*   vLLM은 두 모델 제품군 모두에서 TTFT와 동시성에서 우위를 차지합니다.
*   Ollama는 두 모델 제품군 모두에서 단일 사용자 디코딩에서 우위를 차지합니다.
*   비슷한 파라미터 (parameter) 수를 비교할 때 디코딩 격차가 줄어듭니다 (Qwen3.5–9B: 1.5배, Gemma 4 E4B: 1.6배).
*   양자화 (quantization) 이점은 모델 크기가 커질수록 증가합니다 (E4B 1.6배 → 31B 2.6배).

Gemma 4 26B MoE 결과는 독특하며, Qwen3.5 테스트에는 해당되는 모델이 없었습니다. MoE 아키텍처 (architecture)는 추론 효율성 (inference efficiency) 면에서 진정한 승리이며, 더 작은 모델 비용으로 더 큰 모델의 지능을 제공합니다.

### 핵심 교훈

1.  **MoE 모델은 로컬 추론 (local inference)의 최적 지점입니다.** Gemma 4 26B는 VRAM 비용 대비 최고의 품질을 제공하며, vLLM에서 131 tok/s, Ollama에서 181 tok/s로 일상적인 사용에 적합합니다.
2.  **BF16 vs Q4_K_M이 실제 차별점이며, 엔진 자체의 차이는 아닙니다.** vLLM과 Ollama 간의 디코딩 속도 격차는 대부분 엔진 아키텍처 (architecture)보다는 정밀도 (precision)에서 기인합니다. vLLM이 Q4 (GGUF 지원 또는 이와 동등한 방식)를 사용한다면 격차는 크게 줄어들 것입니다.
3.  **TTFT와 동시성은 vLLM의 구조적 이점입니다.** 이는 PagedAttention 및 연속 배치 (Continuous Batching)와 같은 아키텍처 (architectural) 기능에서 비롯되며, 양자화 (quantization) 변경으로는 복제할 수 없습니다. 작업 부하가 TTFT에 민감하다면 (에이전트, RAG, 대화형 도구 등), 정밀도 (precision)에 관계없이 vLLM이 유리합니다.
4.  **데스크톱 VRAM 관리는 vLLM의 가장 큰 약점입니다.** 시작 시 90GiB를 미리 할당하는 것은 서버 환경을 가정한 것으로 데스크톱 사용에는 적합하지 않습니다. 이것이 필자가 네이티브 Windows LLM 엔진 프로젝트를 시작한 핵심 동기입니다.

**업데이트:** 후속 연구에서 확인된 바에 따르면, NVFP4는 BF16의 대부분의 격차를 해소하지만 (E4B 디코딩 124→149 tok/s, TTFT 58→17ms), Q4_K_M은 여전히 단일 사용자 디코딩에서 24~30% 우위를 보입니다. vLLM은 양자화에 관계없이 TTFT 및 동시성 이점을 유지합니다.

### 테스트 환경

*   **GPU:** NVIDIA RTX PRO 6000 Blackwell (97,887 MiB, 전력 제한 400W)
*   **호스트 OS:** Windows 11 Pro
*   **vLLM:** 0.19.1rc1 @ WSL2 Ubuntu 24.04, BF16, CUDA 그래프 (CUDA graphs), `--gpu-memory-utilization 0.80`
*   **Ollama:** 최신 안정 버전, Windows 네이티브, Q4_K_M (GGUF)
*   **모델:** `google/gemma-4-E4B-it`, `google/gemma-4–26B-A4B-it`, `google/gemma-4–31B-it`
*   **벤치마크 날짜:** 2026–04–08

벤치마크는 동일한 프롬프트, 파라미터 (parameters), 하드웨어에서 수행되었습니다.