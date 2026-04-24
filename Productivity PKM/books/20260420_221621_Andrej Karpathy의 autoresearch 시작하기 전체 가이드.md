# 번역 기사

- **원본 URL:** https://medium.com/neuralnotions/getting-started-with-andrej-karpathys-autoresearch-full-guide-c2f3a80b9ce6
- **번역 일시:** 2026-04-20 22:16:21

---

Andrej Karpathy의 "autoresearch" 시작하기 — 전체 가이드

Andrej Karpathy의 새로운 프로젝트 'autoresearch'는 단일 GPU와 AI 코딩 에이전트가 구동하는 작지만 완전한 연구소입니다.

우리는 AGI(범용 인공지능)에 가까워지고 있는가? 아직은 아니지만, 점차 다가가고 있습니다.

핵심 아이디어는 모델 코드와 하이퍼파라미터(hyperparameters)를 수동으로 조정하는 대신, 사람이 마크다운(Markdown) 파일에 상위 수준의 지침을 작성하면 AI 에이전트가 훈련 스크립트 편집, 실험 실행, 개선 사항 유지와 같은 하위 수준 작업을 처리한다는 것입니다.

카르파티는 이를 이전 nanochat 훈련 코어의 단일 GPU, 단일 파일 버전으로 설명하며, 사람이 감독 역할로 물러나는 동안 에이전트가 무기한으로 반복 작업을 수행할 수 있도록 설계되었습니다.

저는 이 프로젝트를 직접 사용해 보았으며, 이 저장소(repo)를 사용하는 방법과 유용한 활용 사례에 대해 단계별로 안내해 드릴 것입니다.

### "autoresearch"는 실제로 무엇인가

Autoresearch는 실제 LLM(대규모 언어 모델) 훈련 루프를 AI 에이전트에 맡겨 스스로 설정을 개선하도록 요청하는 오픈소스 실험 프레임워크입니다.

기본적으로 사람이 지침을 작성하면 LLM이 백그라운드에서 자동으로 개선하고 자율적으로 연구를 수행하며, 단일 GPU에서 실행될 수 있습니다.

모든 변경 사항을 조율하는 대신, 사람은 마크다운으로 에이전트가 시도해야 할 내용을 설명하는 상위 수준의 '프로그램'을 작성하고, 에이전트는 단일 Python 훈련 스크립트를 반복적으로 편집하며, 고정된 시간 동안 훈련을 실행하고, 검증 지표(validation metrics)를 기반으로 자체 변경 사항을 유지할지 결정합니다.

카르파티는 이를 사람이 코드 한 줄 한 줄을 직접 작성하는 대신 에이전트를 조율하는 비중을 높이는 이전의 '에이전트 공학(agentic engineering)' 아이디어의 확장으로 명확히 제시합니다.

Autoresearch는 장난감 시뮬레이션이 아닙니다. 이 에이전트는 Karpathy의 nanochat 작업에서 파생된 실제 GPT 스타일 훈련 스크립트에 연결되어 있으며, 실제 데이터와 진정한 최적화 도구(optimizer) 및 모델 아키텍처(model architecture)로 작동합니다.

전체 저장소(repo)는 주로 단 3개의 파일로 구성됩니다.

### 세 가지 핵심 파일

저장소는 세 가지 핵심 파일을 중심으로 구성됩니다.

*   `prepare.py`
*   `train.py`
*   `program.md`

`prepare.py`는 훈련 데이터를 다운로드하고 BPE(Byte Pair Encoding) 토크나이저(tokenizer)를 맞추는 역할을 합니다. 이 파일은 고정된 환경의 일부로 간주되며 에이전트가 수정해서는 안 됩니다.

`train.py`는 약 630줄의 훈련 스크립트로, 컴팩트한 GPT 스타일 모델, 최적화 도구(Muon과 AdamW의 조합으로 알려짐), 그리고 전체 훈련 루프(training loop)를 정의합니다. 이 파일은 에이전트가 편집할 수 있는 유일한 파일이므로 모든 아키텍처(architectural) 및 하이퍼파라미터 변경 사항은 이 파일을 통해 이루어집니다.

`program.md`는 사람이 작성하고 반복 수정하는 마크다운 파일로, 에이전트의 지시서 역할을 합니다. 이는 에이전트가 `train.py`를 편집하고 실험을 평가하는 동안 어떻게 행동해야 하는지를 알려주는 메타 프로그램(meta-program)과도 같습니다.

이 디자인은 의도적으로 최소한으로 유지되어, 사람이 커피 한 잔을 마시면서 전체 훈련 스크립트를 읽고 핵심 구성 요소를 이해할 수 있도록 합니다. 이는 minGPT 및 nanoGPT와 같은 작고 독립적인 저장소(repo)에 대한 Karpathy의 오랜 선호를 반영합니다.

### 실험 루프 작동 방식

Autoresearch는 공정하면서도 완전히 자동화될 수 있도록 설계된 엄격한 실험 루프를 중심으로 작동합니다.

AI 에이전트는 `program.md`를 읽고 `train.py`를 열어 모델 아키텍처(model architecture) 또는 훈련 하이퍼파라미터(training hyperparameters)에 대한 일부 수정을 제안한 다음, 실제 시간으로 약 5분으로 엄격히 제한된 훈련 실행을 시작합니다.

각 실행이 끝날 때 에이전트는 검증 지표(validation metrics), 특히 언어 모델 품질을 측정하는 BPE(bits-per-byte, val_bpb) 지표를 검사하고, 최신 변경 사항을 커밋(commit)할지 또는 폐기할지 결정합니다.

카르파티는 이 고정 시간 설계를, 주어진 시간 예산 내에서 더 빠르게 수렴하기 때문에 단순히 더 나아 보이는 기존의 하이퍼파라미터 튜닝(hyperparameter tuning)과 대조합니다. 시간 창을 고정하고 최종 검증 손실(validation loss)을 비교함으로써, autoresearch는 하드웨어 차이를 보정하고 구성의 품질에 집중합니다.

만약 검증 손실이 허용할 수 없을 정도로 실행 시간을 늘리지 않으면서 개선된다면, 에이전트는 `train.py`의 새 버전으로 깃 커밋(git commit)을 기록합니다. 그렇지 않으면 이전 최적 버전으로 되돌아가 다른 수정을 시도하며, 효과적으로 모델 구성의 '풍경'을 오르면서 완벽하게 감사 가능한(auditable) 흔적을 남깁니다.

### 하드웨어 및 소프트웨어 요구 사항

Autoresearch에 대한 보고서는 분산 훈련(distributed training)보다는 단일 NVIDIA GPU 설정용으로 설계되어 운영 복잡성을 줄이면서도 적절한 성능의 가속기(accelerator)를 필요로 한다는 점을 강조합니다.

카르파티와 관련 문서에서는 이 시스템이 H100과 같은 고급 NVIDIA 하드웨어에서 테스트되었지만, 고정된 5분 시간 예산은 사용자들이 더 작은 GPU에서 실행하더라도 결과를 비교할 수 있도록 고안된 것입니다. 느린 GPU는 단순히 각 5분 창 내에서 더 적은 최적화 단계(optimization steps)를 완료합니다.

이 프로젝트는 Python 3.10 이상 버전을 대상으로 하며, 선택된 GPU에 적합한 최신 딥러닝 스택(deep learning stack, PyTorch 및 CUDA)을 사용합니다. 이는 NVIDIA 및 커뮤니티 설정 가이드에 설명된 일반적인 단일 GPU 연구 환경과 일치합니다.

최신 NVIDIA 드라이버 설치, CUDA 툴킷(toolkit) 및 cuDNN 버전 일치, 전용 Python 3.10 환경 생성과 같은 일반적인 GPU 설정 지침은 autoresearch용 머신을 준비할 때 직접 적용할 수 있습니다.

### 설정 방법

autoresearch 설정은 Karpathy의 대부분 저장소와 마찬가지로 GitHub 저장소를 클론(clone)하고 깨끗한 Python 환경을 준비하는 것으로 시작합니다.

```bash
git init
git remote add origin https://github.com/karpathy/autoresearch
git pull origin master
```

위 명령을 실행하면 저장소를 사용할 준비가 됩니다.

다음으로 Python 툴링인 'astral'이 필요합니다. 아래 명령을 사용하여 진행하세요.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

다음 단계는 저장소에 나열된 나머지 Python 종속성(dependencies)을 설치하는 것입니다. 코드베이스는 간결하며 복잡한 프레임워크 추상화(abstractions)를 피하므로, 대규모 프로덕션 시스템과 비교할 때 추가 패키지 목록은 많지 않습니다.

```bash
uv sync
```

종속성이 설치되면 토크나이저를 시작하고 간단한 실험을 실행할 수 있습니다.

```bash
uv run prepare.py
uv run train.py
```

위의 모든 명령이 성공적으로 완료되면, 모든 종속성이 준비되었고 간단한 실험을 진행할 수 있다는 의미입니다.

Codex 또는 Claude와 같은 어떤 에이전트에 접근할 수 있는지에 따라, 터미널에서 LLM을 시작하고 `program.md` 파일을 읽고 실험을 시작하는 간단한 실험을 LLM에게 요청하세요.

LLM은 `program.md`를 가벼운 기술로 사용하여 연구를 진행할 것입니다.

`자, program.md를 살펴보고 새로운 실험을 시작해 봅시다! 먼저 설정을 해볼까요.`

### AI 코딩 에이전트 선택 및 연결

Autoresearch는 자체적인 독점 에이전트 구현을 제공하지 않습니다. 대신, 파일을 읽고 `train.py`를 편집하며 제어된 환경에서 셸 명령(shell commands)을 실행할 수 있는 유능한 AI 코딩 도우미에 대한 접근을 전제로 합니다.

### Autoresearch 밤새도록 실행하기

에이전트가 연결되고 `program.md`가 합리적으로 보인다면, 가장 만족스러운 단계는 단순히 autoresearch를 실행하도록 두는 것입니다.

최신 NVIDIA GPU를 사용하면, 각 실험당 고정된 5분 훈련 시간 창 덕분에 8시간 밤샘 실행으로 수십 번의 완전한 훈련 시도를 수용할 수 있습니다. 고급 하드웨어의 경우 구현 세부 사항 및 모델 크기에 따라 약 백 번까지도 가능할 수 있습니다.

Autoresearch는 실제 훈련 루프(training loop)가 '고정된 시간 예산 내에서 상황을 개선하고 모든 것을 기록하라'는 단순한 명령을 가진 AI 에이전트에게 넘겨졌을 때 어떤 일이 일어나는지를 탐구하는 작지만 야심찬 실험입니다.

인간의 역할을 마크다운 '프로그램'을 작성하고 다듬는 것으로 바꾸고, 하위 수준의 반복 작업을 자율 루프에 위임함으로써, autoresearch는 단일 GPU가 소형의 상시 작동 연구소로 어떻게 사용될 수 있는지 재해석합니다.

초기의 블로그 게시물, 소셜 클립, 실제 실험들의 생태계는 에이전트가 코드를 편집하고, 실험을 실행하며, 자체적인 깃 히스토리(git histories)를 관리하는 이러한 패턴이 언어 모델 사전 훈련(pretraining)을 넘어 검색 파이프라인(retrieval pipelines), 도메인 적응(domain adaptation) 등의 분야로 확산될 가능성을 시사합니다.