# 번역 기사

- **원본 URL:** https://medium.com/data-science-collective/turboquant-compressing-kv-cache-4x-on-apple-silicon-how-i-doubled-the-usable-context-length-5a8bce975fe2
- **번역 일시:** 2026-04-20 22:12:04

---

# TurboQuant: Apple Silicon에서 KV 캐시 4배 압축 — MacBook에서 120B LLM의 사용 가능한 컨텍스트 길이를 2배로 늘린 방법

TurboQuant는 단순히 가중치(weights)만 압축하는 것이 아닙니다. 저는 TurboQuant의 KV 캐시 압축 기술을 MLX에 적용하여 GPT-OSS에서 런타임 메모리(runtime memory)를 3.5배 절감하면서도 품질 저하가 전혀 없도록 했습니다.

이 시리즈의 1부와 2부에서는 TurboQuant-MLX로 LLM 가중치를 압축했습니다. 가중치를 240GB에서 48~50GB로 줄여 120B 및 122B 파라미터(parameter) 모델을 64GB MacBook에 탑재할 수 있었습니다. 모델은 잘 작동했고 출력 품질도 우수했습니다. 저는 여기서 한 걸음 더 나아가 Google의 TurboQuant 논문을 기반으로 KV 캐시(KV Cache)를 압축하고자 했습니다.

이전 시리즈에서 저는 TurboQuant를 사용하여 가중치를 압축함으로써 64GB M4 MacBook에서 120B 및 122B 파라미터 모델을 실행할 수 있었습니다. 당시에는 실행을 위해 다른 모든 애플리케이션을 닫아야 하는 문제가 있었지만, 이제 KV 캐시 압축 덕분에 그럴 필요가 없어졌습니다.

LLM으로 텍스트를 생성할 때, 생성되는 모든 토큰(token)은 KV 캐시에 추가됩니다. 이는 어텐션(attention) 메커니즘이 재계산을 피하기 위해 저장하는 키(key) 및 값(value) 벡터입니다. 짧은 프롬프트(prompt)에서는 KV 캐시가 미미하지만, 긴 대화나 문서에서는 컨텍스트 길이(context length)에 비례하여 증가하며 모델 가중치 외에 수 기가바이트의 RAM을 소비할 수 있습니다.

GPT-OSS-120B의 전체 131K 컨텍스트 윈도우(context window)에서는 KV 캐시만으로도 float16 형식으로 9.2GB를 차지합니다. 이는 압축된 가중치 48GB에 추가되는 용량입니다. 64GB 머신에서는 활성화(activations), 운영 체제 및 기타 모든 것을 위해 약 7GB만 남게 됩니다. 실제로는 모델의 전체 컨텍스트 기능을 사용하기 훨씬 전에 메모리 부족 현상이 발생합니다.

원래 TurboQuant 논문(Zandieh et al., 2025)은 단순히 가중치만 압축하지 않습니다. 4장에서는 KV 캐시 압축 파이프라인(pipeline)을 설명합니다: 캐시에 저장되는 키 및 값 벡터에 PolarQuant (하더마드 회전(Hadamard rotation) + 로이드-맥스 코드북 양자화(Lloyd-Max codebook quantization))를 적용하는 것입니다. 이 논문은 "3.5비트(bit)에서 절대적인 품질 중립성"을 보고하는데, 이는 측정 가능한 품질 저하 없이 KV 캐시를 거의 5배 압축할 수 있음을 의미합니다.

저는 이를 MLX에 적용하고자 했습니다.

저는 PyPI 패키지를 개발했습니다. TurboQuant-MLX는 단일 PyPI 패키지로 제공되며, Xcode Command Line Tools와 CMake 3.27 이상이 설치된 모든 Apple Silicon Mac에서 다음 명령어로 설치할 수 있습니다:

```bash
pip install turboquant-mlx-full
```

이 패키지는 1부와 2부에서 다룬 2/3/4비트 가중치 압축과 본 기사의 주제인 KV 캐시 압축을 모두 제공합니다.

### KV 캐시 압축이 생각보다 중요한 이유

다음은 이 작업의 동기가 된 계산입니다.

GPT-OSS-120B는 36개 레이어(layer), 레이어당 8개 KV 헤드(head), 헤드 차원 64를 가집니다. 이는 하이브리드 어텐션(hybrid attention) 모델로, 일부 레이어는 전체 어텐션(KV 캐시가 컨텍스트에 따라 증가)을 사용하고 일부는 슬라이딩 윈도우 어텐션(sliding window attention, 고정된 128토큰 윈도우)을 사용합니다. float16 KV 캐시를 사용하는 131K 컨텍스트에서:

*   전체 어텐션 레이어: 캐시가 선형적으로 증가 → 긴 컨텍스트에서 메모리 지배
*   슬라이딩 윈도우 레이어: 128토큰으로 고정 → 무시할 수 있는 수준

131K 컨텍스트에서 TQ 3비트는 7.4GB의 RAM을 절약합니다. 이는 모델이 메모리에 들어갈 수 있는지 없는지를 결정하는 차이입니다.

Qwen3.5–122B-A10B에도 동일하게 적용됩니다. 이 모델은 48개 레이어를 가지지만, 그중 12개만이 증가하는 KV 캐시를 사용합니다(나머지는 GatedDeltaNet 선형 어텐션을 사용). 262K 컨텍스트에서 FP16 KV 캐시는 6.1GB이며, TQ 3비트는 이를 약 1.2GB로 압축합니다.

### TurboQuant KV 캐시 압축 작동 방식

이 접근 방식은 가중치 압축과 유사하지만, 추론(inference) 중 실시간으로 작동합니다:

1.  **유입되는 KV 벡터는 float16 형식으로 도착합니다.** 모델은 각 토큰에 대해 새로운 키 및 값 벡터를 정상적으로 계산합니다.
2.  **하더마드 회전(Hadamard rotation)** — 무작위 부호(random signs)를 곱한 다음 고속 하더마드 변환(fast Hadamard transform)을 적용합니다. 이는 크기(magnitude)를 모든 차원(dimensions)에 균일하게 분산시켜 양자화 오류(quantization errors)를 유발하는 이상치(outliers)를 제거합니다. 가중치 압축에 사용된 것과 동일한 회전 방식이 여기서도 작동합니다.
3.  **그룹별 RMS 정규화(Group-wise RMS normalization)** — 각 벡터를 64개 요소(elements) 그룹으로 분할하고, 그룹의 RMS(Root Mean Square)로 나눕니다. RMS는 float16 스케일 팩터(scale factor)로 저장됩니다.
4.  **로이드-맥스 코드북 양자화(Lloyd-Max codebook quantization)** — 각 정규화된 값(normalized value)을 미리 계산된 최적 코드북(optimal codebook)에서 가장 가까운 중심점(centroid)에 매핑(map)합니다. 3비트에서는 8개의 중심점이 있습니다. 3비트 인덱스(indices)는 uint32 배열(array)로 압축됩니다.
5.  **어텐션을 위해** — 모델이 어텐션 계산에 전체 캐시를 필요로 할 때, 역양자화(dequantize)를 수행합니다: 인덱스를 언팩(unpack)하고, 중심점을 찾고, 스케일을 곱하고, 역회전(inverse rotation)을 적용합니다. 역양자화된 float16 값은 MLX의 표준 `mx.fast.scaled_dot_product_attention`을 통해 흐릅니다.

압축된 표현은 압축된 인덱스(요소당 3비트)와 그룹별 스케일(float16)만 저장합니다. 회전으로 인해 분포가 0을 중심으로 대칭을 이루므로 바이어스(bias) 항은 필요하지 않습니다. 이는 float16 대비 3비트에서 4.6배, 4비트에서 3.8배의 압축률을 제공합니다.

### 엔지니어링 과제: 하이브리드 어텐션 및 어텐션 싱크

TurboQuant KV 캐시를 실제 모델에 적용하려면 현대 LLM이 MLX에서 어텐션을 구현하는 방식과 관련된 세 가지 문제를 해결해야 했습니다.

#### 문제 1: GPT-OSS는 어텐션 싱크를 사용합니다

GPT-OSS는 학습된 "싱크(sink)" 벡터를 어텐션 계산에 전달합니다. MLX의 양자화된 어텐션 경로(`quantized_scaled_dot_product_attention`)는 어텐션 싱크를 명시적으로 거부합니다:

```python
if hasattr(cache, "bits"):
   if sinks is not None:
       raise ValueError("Quantized SDPA does not support attention sinks.")
```

저의 초기 구현은 TQ 캐시에 `bits` 속성(attribute)을 노출했는데(양자화된 행렬 곱셈(matmul) 경로를 통해 라우팅하기 위함), 이는 GPT-OSS에서 충돌을 일으켰습니다.

해결책은 float16으로 역양자화된 값을 반환하고 표준 SDPA 경로를 통해 라우팅하는 것이었습니다. 이 방식은 싱크, 슬라이딩 윈도우, 인과 마스크(causal masks) 등 모든 어텐션 기능과 호환됩니다. 메모리 절감은 압축된 *저장 공간*에서 오는 것이며, 어텐션 계산 자체에서 오는 것은 아닙니다. 어텐션을 위한 임시 float16 배열은 MLX의 지연 평가 그래프(lazy evaluation graph)의 일부이며 생성 단계 사이에 지속되지 않습니다.

#### 문제 2: 하이브리드 캐시 아키텍처

GPT-OSS와 Qwen3.5 모두 레이어 전반에 걸쳐 이종(heterogeneous) 캐시 유형을 사용합니다:

*   GPT-OSS: 전체 어텐션 레이어용 `KVCache`, 슬라이딩 윈도우 레이어용 `RotatingKVCache`
*   Qwen3.5: 전체 어텐션 레이어용 `KVCache`, GatedDeltaNet 선형 어텐션 레이어용 `ArraysCache`

TQ 압축은 `KVCache` 인스턴스에만 적용됩니다. 변환 함수는 캐시 유형을 감지하고 나머지는 변경하지 않습니다:

```python
def convert_cache_to_turboquant(prompt_cache, tq_bits=3, ...):
    from mlx_lm.models.cache import KVCache
    new_cache = []

    for c in prompt_cache:
        if not isinstance(c, KVCache):
            new_cache.append(c)  # Leave RotatingKVCache, ArraysCache as-is
            continue
        tq = TurboQuantKVCache(tq_bits=tq_bits, ...)

        if c.keys is not None and c.offset > 0:
            tq.update_and_fetch(c.keys[..., :c.offset, :],
                                c.values[..., :c.offset, :])
        new_cache.append(tq)

    return new_cache
```

이는 어떤 모델 코드도 수정할 필요 없이 적용됩니다.

#### 문제 3: 프롬프트 우선 패턴

MLX의 내장 KV 양자화(`mlx_lm.generate`의 `kv_bits` 파라미터)는 전체 정밀도 캐시(full-precision cache)로 프롬프트를 처리한 다음 양자화된 형식으로 변환합니다. 이는 프롬프트 처리가 어텐션 패턴을 설정하는 데 정확한 KV 값으로부터 이점을 얻기 때문에 중요합니다.

저는 동일한 패턴을 따릅니다: 전체 프롬프트를 표준 float16 `KVCache`로 처리한 다음 `TurboQuantKVCache`로 변환합니다. 이후 생성되는 토큰들은 도착 시 TQ 압축됩니다. 이는 정확한 프롬프트 처리와 압축된 생성이라는 두 가지 장점을 모두 제공합니다.

### 결과: 3비트 TQ KV 캐시를 사용한 GPT-OSS-20B

저는 GPT-OSS-20B (24개 레이어, 12개 전체 어텐션 + 12개 슬라이딩 윈도우, 8개 KV 헤드, `head_dim=64`)에 672토큰 물리학 프롬프트와 200토큰 생성을 사용하여 테스트했습니다.

#### 출력 품질

FP16 기준선:
```
We have to produce a comprehensive analysis of each topic, 
explaining key principles, major experiments, and current open 
questions… Should be detailed…
```
TQ 3비트:
```
We need to produce a detailed analysis for each topic, connecting themes, 
and highlight promising directions. Provide key principles, major experiments,
 open questions. Should be comprehensive…
```

두 출력 모두 일관되고 주제에 적합하며 구조적으로 동일합니다. 문구는 다르지만 추론 품질은 같습니다.

#### 메모리 및 성능

총 872토큰(프롬프트 672개 + 생성 200개)에서 3.5배의 절감은 이미 이론적 최대치에 근접하고 있습니다. 더 긴 컨텍스트(32K 이상)에서는 전체 어텐션 레이어가 고정된 슬라이딩 윈도우 레이어보다 지배적이 되면서 절감률이 4.3배로 수렴합니다.

#### 왕복 품질 지표

저는 두 타겟 모델의 헤드 차원(head dimension)에 걸쳐 양자화-역양자화(quantize-dequantize) 왕복 품질을 측정했습니다:

3비트에서 원본 KV 벡터와 재구성된 KV 벡터 간의 코사인 유사도(cosine similarity)는 지속적으로 0.98 이상입니다. 하더마드 회전은 크기를 분산시켜 코드북이 값을 효율적으로 표현할 수 있도록 제 역할을 하고 있습니다.

#### 속도: 소형 모델에서는 트레이드오프, 대형 모델에서는 이점

GPT-OSS-20B에서 TQ KV 캐시는 FP16의 90.6 tok/s 대비 29.9 tok/s로 작동합니다. 이는 약 3배 느린 수치입니다. 이는 매 생성 단계마다 어텐션 계산을 위해 전체 압축 캐시를 float16으로 역양자화하기 때문입니다. 비용은 컨텍스트 길이에 비례하여 증가하며, 원래 빠르게 실행되는 소형 모델(90 tok/s)에서는 역양자화 오버헤드(overhead)가 지배적입니다.

그런 다음 저는 GPT-OSS-120B에서 동일한 테스트를 실행했고 반대의 결과를 얻었습니다:

120B 모델에서는 추론 계산 자체가 메모리 대역폭 제한(memory-bandwidth-bound)이 심해서, KV 캐시를 4배 줄이면 역양자화 연산으로 추가되는 시간보다 전체 단계 시간을 더 많이 단축시킵니다. 4배 더 작은 캐시는 어텐션 단계당 메모리 트래픽(memory traffic)을 줄이는 것을 의미하며, 이는 Apple Silicon의 통합 메모리(unified memory)에서 지배적인 비용입니다. 압축은 단순히 여유 공간(headroom)을 제공하는 것을 넘어 처리량(throughput)을 높여줍니다.

이는 양자화에 대한 일반적인 인식을 뒤집습니다. 작고 빠른 모델에서는 모든 양자화 방식이 품질 대 속도 트레이드오프(tradeoff)입니다. 크고 느린 모델에서는 공격적인 양자화가 *두 가지 측면에서 동시에 이점*이 될 수 있습니다: 더 적은 메모리와 더 많은 초당 토큰 수.

메모리 절감 효과는 구체적입니다: GPT-OSS-120B에서 131K 컨텍스트를 사용할 때 7.2GB의 RAM을 절약할 수 있습니다. 이는 모델이 MacBook에 적합한지 아니면 대화 도중에 메모리 부족으로 중단되는지의 차이입니다.

### 가중치 및 KV 캐시 압축 결합: 이중 압축 문제

여기서 1부, 2부, 3부가 함께 다루어지지만, 예상치 못한 결과가 있었습니다.

#### 단순한 접근 방식의 실패

저의 첫 번째 직관은 두 가지를 모두 중첩하는 것이었습니다: TQ 3비트 가중치(2부에서 다룸)와 TQ 3비트 KV 캐시를 함께 사용하는 것입니다. 저는 TQ 압축된 GPT-OSS-20B 모델을 로드하고 3비트 KV 캐시 압축을 활성화했습니다.

출력은 처음에는 일관성이 있었습니다 — "하늘의 푸른색은 대기에 의한 햇빛 산란 때문이다" — 하지만 30토큰 이내에 "The blue? The blue? The blue?"와 같이 반복되는 형태로 붕괴되었습니다.

문제는 누적되는 양자화 노이즈(quantization noise)였습니다. 가중치 양자화된 모델이 생성하는 KV 벡터는 이미 압축된 가중치로부터 노이즈를 포함하고 있습니다. 이러한 노이즈가 있는 벡터를 두 번 압축하면 모델의 허용 임계값(tolerance threshold)을 넘어섭니다. 3비트 양자화 두 겹은 너무 공격적입니다.

#### 이중 압축을 위한 4비트 KV의 적정점

해결책: 가중치가 이미 압축된 경우 KV 캐시에 4비트 TQ를 사용하는 것입니다. 저는 TQ 3비트 가중치를 사용하는 GPT-OSS-20B에서 세 가지 구성을 모두 테스트했습니다:

TQ 3비트 가중치와 TQ 4비트 KV를 사용했을 때 깔끔하고 정확한 응답을 생성했습니다:
```
The sky appears blue because of a physical phenomenon called Rayleigh 
scattering. Light travels through the atmosphere and is scattered by the 
molecules and particles in the air. Short-wavelength light (blue, violet) 
is scattered more strongly than long-wavelength light (red, orange).
```
이는 가중치 압축 외에 3.9배의 KV 캐시 절감 효과를 제공하며, 품질 저하는 전혀 없습니다.

#### 100B+ 모델은 3비트 + 3비트를 잘 견딥니다

저는 "가중치가 TQ 압축된 경우 4비트 KV를 사용하라"는 규칙이 보편적으로 적용될 것이라고 생각했습니다. 하지만 GPT-OSS-20B를 망가뜨렸던 구성인 TQ 3비트 가중치와 TQ 3비트 KV 캐시를 사용하여 Qwen3.5–122B-A10B에서 테스트했을 때 완벽하게 작동했습니다. 이어서 GPT-OSS-120B에서도 동일한 테스트를 했고, 같은 결과를 얻었습니다.

Qwen3.5–122B-A10B ("Why is the sky blue?", 16 프롬프트 토큰):
```
The thinking output was character-for-character similar across both runs: identifying sunlight as a mix of wavelengths, explaining why short wavelengths scatter more strongly, walking through Rayleigh scattering, even mentioning sunset reddening as a corollary.
```

GPT-OSS-120B (벨 부등식(Bell inequalities), AdS/CFT, 인플레이션(inflation) 및 암흑 물질(dark matter)을 다루는 144토큰 물리학 프롬프트):

세 가지 주목할 만한 점이 있습니다:

1.  120B에서도 3비트 KV가 작동합니다. 반복 붕괴나 드리프트(drift)가 없었습니다. 출력은 FP16 기준선과 동일한 `<|channel|>analysis` 계획 모드와 동일한 논리적 구조를 유지했습니다.
2.  총 344토큰에서 3.8배의 KV 캐시 절감 효과. 전체 131K 컨텍스트에서는 약 2.4GB 절감으로 예상됩니다.
3.  120B에서는 TQ 3비트가 FP16보다 빠릅니다(8.7 tok/s 대 6.4 tok/s). 이는 TQ가 3배 느렸던 20B 결과와 정반대입니다. 120B에서는 역양자화 오버헤드가 4배 작은 캐시에서 얻는 메모리 대역폭 절감 효과에 비해 미미합니다. 모델이 클수록 압축은 처리량에 더 큰 도움이 됩니다.

이제 패턴이 명확해졌습니다: 이중 압축 허용치는 모델 크기에 비례하며, 속도 이점도 마찬가지입니다. GPT-OSS-20B는 12개의 전체 어텐션 레이어와 적당한 수준의 중복성(redundancy)을 가지고 있어 양자화 노이즈가 빠르게 누적되며, 모델이 원래 빠르기 때문에 역양자화 오버헤드가 눈에 뜁니다. 120B 및 122B에서는 두 가지 효과가 모두 반전됩니다: 모델은 누적된 3비트 노이즈를 흡수할 충분한 중복성을 가지고 *있으며*, 충분히 느려서 KV 캐시 메모리 트래픽의 어떤 감소든 초당 토큰 수에서 순이득이 됩니다.

이는 2부에서 보았듯이, 대형 모델이 소형 모델보다 공격적인 가중치 압축을 더 잘 견디며, KV 캐시 압축에도 동일하게 적용됩니다.

### GPT-OSS-120B의 전체 스택

64GB M4 Max의 GPT-OSS-120B에서 TQ 3비트 가중치와 TQ 3비트 KV 캐시를 결합(이 스케일에서 작동 확인):

TurboQuant 없이는 GPT-OSS-120B는 컨텍스트가 0일 때도 64GB에 들어가지 않습니다. TQ 가중치 압축만으로는 적합하지만 중간 컨텍스트 길이에서 메모리 부족이 발생합니다. 3비트 가중치 및 KV 캐시 압축을 모두 사용하면 모델을 전체 131K 컨텍스트 윈도우에서 실행할 수 있으며 여전히 14GB의 여유 공간을 확보할 수 있습니다. 또한, 생성 속도는 FP16 캐시 기준선보다 느리지 않고 오히려 더 빠릅니다.

120B 및 122B 데이터를 바탕으로 정제된 규칙: FP16 가중치 모델의 경우 모든 스케일에서 3비트 KV가 올바른 기본값입니다. TQ 압축 가중치의 경우, 노이즈 예산(noise budget)이 빠듯한 소형 모델(~20B)에서는 4비트 KV를 사용하고, 중복성이 누적 노이즈를 흡수하는 100B+ 모델에서는 3비트 KV를 사용합니다. GPT-OSS-120B와 Qwen3.5–122B 모두 3비트 가중치 + 3비트 KV 구성에서 깔끔하게 실행되며, 120B에서는 캐시 메모리 대역폭(bandwidth) 절감 효과가 역양자화 오버헤드를 상회하기 때문에 3비트 KV가 FP16보다 실제로 더 빠릅니다.

### 제가 배운 점

*   **3비트가 KV 캐시의 적정점(sweet spot)입니다.** 3비트에서 TQ는 KV 벡터에서 4.3배 압축률과 함께 0.98 이상의 코사인 유사도(cosine similarity)를 달성합니다. 4비트에서는 추가적인 충실도(fidelity, 0.995 코사인)가 눈에 띄게 더 나은 출력으로 이어지지 않으면서 20% 더 많은 메모리를 소비합니다. 2비트에서는 압축률이 우수하지만(7.1배) 품질이 저하됩니다.
*   **소형 모델은 KV 캐시 압축에 대한 유효한 테스트 대상이 아닙니다.** 저는 2개의 KV 헤드만 있는 0.5B 모델에서 디버깅하는 데 상당한 시간을 보냈습니다. MLX의 내장 `kv_bits=4`를 포함하여 모든 KV 양자화 방식이 이 모델에서는 실패합니다. 평균화할 헤드가 2개밖에 없을 때 헤드당 양자화 노이즈가 너무 높습니다. 8개의 KV 헤드를 가진 GPT-OSS-20B에서는 3비트 및 4비트 TQ 모두 일관된 출력을 생성합니다. 하더마드 회전(Hadamard rotation)을 효과적으로 만드는 CLT(중심 극한 정리, Central Limit Theorem) 근사는 충분한 차원(dimensionality)을 요구합니다.
*   **어텐션 싱크(attention sinks)는 호환성 함정입니다.** GPT-OSS는 학습된 어텐션 싱크 벡터를 사용합니다. MLX의 양자화된 어텐션 경로는 싱크와 함께 충돌합니다. 양자화된 어텐션 API를 통해 라우팅되는 모든 KV 캐시 압축은 일부 모델에서는 조용히 실패하고 다른 모델에서는 충돌할 것입니다. 이론적으로 양자화된 경로가 더 빠르더라도 float16을 반환하고 표준 어텐션 경로를 사용하는 것이 더 강력합니다.
*   **프롬프트 우선 변환 패턴이 중요합니다.** 전체 정밀도 캐시로 프롬프트를 처리한 다음 TQ로 변환하는 것은 MLX의 내장 KV 양자화가 사용하는 동일한 패턴을 따릅니다. 이는 압축이 노이즈를 도입하기 전에 모델이 정확한 값으로 어텐션 패턴을 설정하도록 보장합니다.

### 다음 단계

*   GPT-OSS-120B 및 Qwen3.5–122B에서 확장된 컨텍스트 길이로 테스트하여 메모리 예상치(memory projections)의 실제 유효성 검증
*   QJL 잔차 보정(residual correction) — 원래 TurboQuant 논문은 1비트 존슨-린덴스트라우스 투영(Johnson-Lindenstrauss projection)을 사용하여 양자화 잔차(quantization residuals)를 보정합니다. 이를 추가하면 동일한 비트 폭에서 품질을 FP16에 더 가깝게 만들 수 있으며, 잠재적으로 2비트 사용을 가능하게 할 수 있습니다.
*   점진적 역양자화(Incremental dequantization) — 현재 구현은 매 단계마다 전체 캐시를 역양자화합니다. 역양자화된 결과를 캐싱하고 새로운 토큰만 처리하면 단계별 오버헤드를 완전히 제거할 수 있지만, 압축된 표현과 압축 해제된 표현을 모두 저장하는 비용이 발생합니다.
*   압축된 모델을 HuggingFace에 업로드하고, HuggingFace에서 압축된 모델을 직접 다운로드하는 기능 지원.
*   LM Studio 및 Ollama와 통합.

### 직접 사용해보기

본 기사의 모든 내용은 단일 PyPI 패키지로 제공됩니다. Xcode Command Line Tools와 CMake 3.27 이상이 설치된 모든 Apple Silicon Mac에서 다음 명령어를 통해 설치할 수 있습니다:

```bash
pip install turboquant-mlx-full
```

이 패키지는 설치 시 작은 Metal 확장(약 1분 소요)을 빌드하며 `turboquant_mlx`로 임포트(import)할 수 있습니다. `-full` 접미사는 PyPI 이름에만 붙는데, 더 짧은 이름이 다른 프로젝트에 의해 선점되었기 때문입니다.

#### 모든 MLX 모델에서의 KV 캐시 압축

최소한의 통합은 두 줄입니다: 일반 float16 캐시로 프롬프트를 처리한 다음, `convert_cache_to_turboquant`를 한 번 호출합니다.

```python
import mlx.core as mx
from mlx_lm import load
from mlx_lm.models.cache import make_prompt_cache
from turboquant_mlx.layers.polar_kv_cache import convert_cache_to_turboquant

model, tokenizer = load("openai/gpt-oss-20b")

prompt = "Why is the sky blue?"
input_ids = mx.array(tokenizer.encode(prompt))[None]

# Process the prompt at full precision...
cache = make_prompt_cache(model)
_ = model(input_ids, cache=cache)

# ...then compress the cache for the rest of generation.
cache = convert_cache_to_turboquant(cache, tq_bits=3, group_size=64)

# Continue your normal token-by-token loop, passing `cache=cache` into model().
```

GPT-OSS 및 Qwen3.5와 같은 하이브리드 어텐션 모델은 자동으로 처리됩니다. `RotatingKVCache` (슬라이딩 윈도우) 및 `ArraysCache` (선형 어텐션) 레이어는 변경 없이 전달되며, 전체 어텐션 `KVCache` 레이어만 압축됩니다. 어떤 레이어가 어떤 어텐션 유형을 사용하는지 알 필요가 없습니다.

#### 실행 가능한 병렬 비교

이 패키지에는 FP16과 TurboQuant 3비트를 동일한 프롬프트에서 연속으로 실행하는 데모 스크립트가 포함되어 있어, 사용자 자신의 머신과 모델에서 메모리 및 속도 차이를 확인할 수 있습니다:

```bash
python -m turboquant_mlx.demo_kv \
  --model openai/gpt-oss-20b \
  --prompt "Why is the sky blue?" \
  --max-tokens 200 \
  --compare
```

64GB M4 Max에서 이 스크립트는 두 출력 스트림을 모두 인쇄한 다음, 하단에 각 구성의 초당 토큰 수와 KV 캐시 크기를 보여주는 비교 라인을 출력합니다.

#### 전체 스택: 압축된 가중치 + 압축된 KV 캐시

위 표의 GPT-OSS-120B-131K-컨텍스트 구성을 재현하려면(TQ 3비트 가중치 + TQ 3비트 KV), 먼저 가중치를 한 번 변환합니다:

```bash
turboquant-convert --hf-path openai/gpt-oss-120b --bits 3 --mlx-path gpt-oss-120b-tq3
```

그런 다음 `demo_kv`를 변환된 모델로 지정합니다:

```bash
python -m turboquant_mlx.demo_kv \
  --model gpt-oss-120b-tq3 \
  --prompt "Explain Bell inequalities, AdS/CFT, inflation, and dark matter" \
  --max-tokens 500 \
  --tq-bits 3
```

본 기사에서 도출된 경험 규칙(rule of thumb)에 따라: 노이즈 예산(noise budget)이 빠듯한 소형 모델(~20B)에서는 `—tq-bits 4`를 사용하고, 100B+ 모델에서는 `—tq-bits 3`를 사용합니다.

본 기사에서 보고된 모든 수치 — 4.6배 압축, 131K 컨텍스트에서 7.4GB 절감, 120B 모델에서의 속도 역전 — 는 이 단일 패키지에서 재현 가능합니다. 설치 시 Metal 커널(kernels), 변환 CLI(Command Line Interface), Python 통합 레이어(integration layer) 및 데모 스크립트가 함께 제공됩니다. 제가 테스트하지 않은 구성을 시도해 보신다면, 수치를 공유해 주십시오. 저장소(repo)의 이슈(issues) 및 PR(Pull Requests)을 환영합니다.