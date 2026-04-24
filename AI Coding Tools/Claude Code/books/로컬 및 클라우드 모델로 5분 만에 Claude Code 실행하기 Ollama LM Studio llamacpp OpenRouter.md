---
원본 URL: https://medium.com/@luongnv89/run-claude-code-on-local-cloud-models-in-5-minutes-ollama-openrouter-llama-cpp-6dfeaee03cda
---

# 로컬 및 클라우드 모델로 5분 만에 Claude Code 실행하기 (Ollama, LM Studio, llama.cpp, OpenRouter)

이 포스트를 작성하게 된 이유는, Anthropic의 공식 모델이 아닌 다른 모델을 Claude Code(클로드 코드)에서 사용하기 위해 복잡한 해킹이나 어댑터를 동원해야 했던 과거의 가이드들 때문입니다. 이제는 훨씬 쉬워졌으며, 여전히 많은 비용을 절감할 수 있습니다.

초기에는 공식 Anthropic API 외의 다른 모델을 Claude Code에 연결하는 과정이 매우 번거로웠습니다. 통합 방식은 불안정했고, 설정 과정은 마치 과학 실험 같았으며, 업데이트가 있을 때마다 워크플로우(Workflow)가 깨질 위험이 컸습니다.

하지만 지금은 상황이 달라졌습니다. Claude Code는 이제 Ollama, LM Studio, llama.cpp, OpenRouter 등 다양한 대안 제공업체를 훨씬 더 잘 지원합니다. 따라서 이제 실제적인 질문은 다음과 같이 바뀌었습니다.

**"작동시킬 수 있을까?"** 가 아니라,
**"내 사용 사례(비용/속도/품질/개인정보 보호)에 어떤 옵션이 가장 적합하며, 어떻게 5분 안에 설정할 수 있을까?"** 입니다.

이 글은 최신 정보를 반영한 가이드입니다.

예제에 사용된 테스트 환경은 MacBook Pro M1(32GB RAM)과 Nvidia DGX Spark(120GB RAM, GB10 GPU)입니다. 가장 간단한 경로(Ollama 로컬 및 쉬운 클라우드 라우팅)부터 시작하여 더 유연한 설정 방법까지 살펴보겠습니다.

### 로컬 LLM 코딩을 위한 최소 사양 (원활한 체감 기준)

Claude Code와 로컬 모델의 조합을 단순 데모 수준이 아닌 실제 코딩 업무에 활용하려면 다음 사양을 권장합니다.

*   **RAM:** 32GB (Apple Silicon의 통합 메모리 또는 PC RAM)
*   **모델 크기:** 최소 24B(240억 개) 이상의 파라미터(Parameter)

16GB RAM에서도 더 작은 모델을 실행할 수 있지만, 편집 오류가 잦고 재시도가 많아져 전반적인 작업 속도가 느려지는 경향이 있습니다.

### 추천 시작 모델
*   **devstral-small-2 (24B):** 코딩 품질 면에서 좋은 시작점입니다.
*   **qwen3-coder:30b:** 코딩 능력이 더 뛰어나며, 32GB 사양에서 실용적으로 사용할 수 있습니다.
*   **GLM4.7-flash:q8_0:** 가성비와 지연 시간(Latency)의 균형이 뛰어납니다(양자화 모델).

---

### 왜 대안 모델을 사용해야 할까요?

솔직히 말해서 공식 API를 사용하는 Claude Code는 훌륭하지만, 비용이 빠르게 누적됩니다. 기능을 테스트하는 것만으로도 크레딧이 금방 소모되곤 합니다.

그래서 대안을 찾기 시작했습니다. Claude Code는 Anthropic API 형식을 지원하는 모든 제공업체와 호환됩니다. 현재 그런 업체는 매우 많습니다.

**핵심 요약:** 서드파티(Third-party) 대안을 사용하면 Claude Opus 4.5 대비 최대 98%의 비용을 절감할 수 있습니다. **DeepSeek V3.2**는 100만 토큰당 약 $0.28/$0.28/$0.42로 가장 저렴하며, **Ollama**와 같은 로컬 옵션은 완전히 무료입니다. 구독 모델을 선호한다면 **ZhipuGLM**($3/월)이나 **MiniMax**($10/월) 같은 저가형 옵션도 있습니다.

---

### 옵션 1: Ollama 로컬 실행

*   **소요 시간:** 5분 | **비용:** 무료 | **장점:** 개인정보 보호, 인터넷 연결 불필요

복잡한 설정 없이 바로 작동하는 것을 원한다면 Ollama가 정답입니다.

**1단계: Ollama 설치**
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**2단계: 모델 다운로드(Pull)**
32GB RAM 환경에서는 24B 모델을 편안하게 실행할 수 있습니다.
```bash
ollama pull devstral-small-2
```

**3단계: Claude Code 연결**
가장 간단한 방법:
```bash
ollama launch claude --model devstral-small-2
```

또는 수동 설정( `~/.zshrc` 또는 `~/.bashrc`에 추가):
```bash
export ANTHROPIC_AUTH_TOKEN="ollama"
export ANTHROPIC_API_KEY=""
export ANTHROPIC_BASE_URL="http://localhost:11434"
```
설정 적용 후:
```bash
source ~/.zshrc
claude --model devstral-small-2
```

이제 로컬에서 Claude Code가 실행됩니다!

---

### 옵션 2: llama.cpp + HuggingFace

*   **소요 시간:** 15–20분 | **비용:** 무료 | **장점:** HuggingFace의 모든 모델 사용 가능

Ollama도 좋지만, 특정 모델을 사용하고 싶을 때는 llama.cpp가 유용합니다.

**1단계: llama.cpp 빌드(Build)**
*   **macOS (Apple Silicon):** `cmake`를 사용하여 Metal 가속을 활성화합니다.
*   **Linux (NVIDIA GPU):** `CUDA`를 활성화하여 빌드합니다.

**2단계: 서버 시작 (모델 다운로드 포함)**
HuggingFace에서 코딩에 적합하고 도구 호출(Tool calling)이 가능한 모델을 찾습니다. 예를 들어 `qwen3-coder`를 사용할 경우:
```bash
llama-server -hf bartowski/cerebras_Qwen3-Coder-REAP-25B-A3B-GGUF:Q4_K_M \
    --alias "Qwen3-Coder-REAP-25B-A3B-GGUF" \
    --port 8000 \
    --jinja \
    --kv-unified \
    --ctx-size 64000
```
*주의: `--jinja` 플래그는 필수입니다. 이 설정이 없으면 도구 호출이 작동하지 않습니다.*

**3단계: Claude Code 연결**
```bash
export ANTHROPIC_BASE_URL="http://localhost:8000"
claude --model Qwen3-Coder-REAP-25B-A3B-GGUF
```

---

### 옵션 3: LM Studio

*   **소요 시간:** 5분 | **비용:** 무료 | **장점:** 사용자 친화적인 GUI, 최적의 모델 선택 용이

LM Studio는 하드웨어에 맞는 모델을 쉽게 찾을 수 있어 제가 선호하는 도구 중 하나입니다.

1.  **LM Studio 설치:** [lmstudio.ai](https://lmstudio.ai/)에서 다운로드합니다.
2.  **모델 다운로드:** 앱 내 검색 탭에서 원하는 모델을 받아 로컬 서버를 실행합니다(기본 포트 1234).
3.  **환경 변수 설정:**
    ```bash
    export ANTHROPIC_BASE_URL=http://localhost:1234
    export ANTHROPIC_AUTH_TOKEN=lmstudio
    claude --model qwen/qwen3-coder-30b
    ```

---

### 옵션 4: Ollama 클라우드 모델

*   **소요 시간:** 2분 | **비용:** 사용량 기반 결제 | **장점:** 로컬 워크플로우에서 클라우드 성능 활용

최근 Ollama에 `:cloud` 변형 모델이 추가되었습니다. 로컬 모델과 동일한 명령어를 사용하지만 연산은 클라우드 인프라에서 수행됩니다. API 키를 직접 관리할 필요가 없습니다.

```bash
ollama pull kimi-k2.5:cloud
ollama launch claude --model kimi-k2.5:cloud
```

---

### 옵션 5: 클라우드 제공업체 API 직접 연결

*   **소요 시간:** 2분 | **비용:** 사용량 기반 결제 | **장점:** 직접적인 API 접근, 세밀한 제어

**OpenRouter (통합 어댑터):** 하나의 키로 수많은 모델에 접근할 수 있습니다.
```bash
export ANTHROPIC_BASE_URL=https://openrouter.ai/api
export ANTHROPIC_AUTH_TOKEN=YOUR_OPENROUTER_KEY
export ANTHROPIC_API_KEY="" # 반드시 비워두어야 합니다.
export ANTHROPIC_MODEL="openai/gpt-oss-120b:free"
```
*참고: `ANTHROPIC_API_KEY`를 비워두는 이유는 Claude Code가 Anthropic 서버로 직접 인증을 시도하는 것을 방지하기 위함입니다.*

---

### 마치며

**핵심 결론:** Claude Code는 놀라울 정도로 유연합니다. 더 이상 Anthropic의 API에만 갇혀 있을 필요가 없습니다.

*   **완벽한 개인정보 보호**가 필요하다면, Mac M1(32GB)에서 **devstral-2-small (24B)** 모델을 사용하는 것이 적절한 시작점입니다.
*   **속도와 고품질**을 원하며 개인정보 이슈에서 자유롭다면, **클라우드 제공업체(Kimi, Minimax, DeepSeek 등)**가 매우 좋은 선택지입니다.
*   하지만 여전히 **최고의 품질**이 최우선이라면, 현재로서는 **Opus 4.5**가 품질과 속도 면에서 여전히 정점에 있습니다.

여러분이 어떤 설정을 사용하고 계신지 공유해 주세요! 더 자세한 사용법은 제 [GitHub 저장소](https://github.com/luongnv89/claude-howto)에서 확인하실 수 있습니다.