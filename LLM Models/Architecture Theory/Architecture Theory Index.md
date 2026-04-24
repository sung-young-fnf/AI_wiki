# Architecture Theory Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-02-06 |
| 키워드 | `Karpathy`, `microGPT`, `nanoGPT`, `Backpropagation`, `Adam optimizer`, `RLM`, `Recursive Language Models`, `JEPA`, `VL-JEPA`, `LeWorldModel`, `Yann LeCun`, `선형 어텐션`, `Mamba SSM`, `TTT`, `Attention Residuals`, `SDPO`, `Synthetic Reasoning Distillation`, `Bonsai 1-bit` |
| 관련문서 | [[LLM Models Index]], [[ArticleScrap Platform Index]] |

## 개요

모델 원리·구현 분석·옵티마이저·아키텍처 이론 — Karpathy nanoGPT·Backpropagation·Adam·RLM·JEPA·LeWorldModel·선형 어텐션·Mamba·Attention Residuals·SDPO.

## 파일 목록 (12개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260227_214525_코딩의 종말은 취소되었습니다 당신의 AI 비서가 빠르게 무능해지는 이유]] | 트랜스포머 어텐션의 O(n²) 맥락 복잡도가 AGI 실현 불가 근거라는 현직 웹 개발자(AI 연구자 겸직)의 주장. 프로젝트 초반엔 빠르던 Gemini Code Assist·Copilot·Cursor·Codex·Antigravity가 맥락 팽창에 따라 응답 시간이 1→2→5분으로 기하급수 증가하며, LLM은 정적이라 작업 중 학습 없음. 샘 알트만의 "2028 AGI" 전망 반박. | 트랜스포머 O(n²), 어텐션 메커니즘, AGI 한계, Gemini Code Assist, 샘 알트만 2028 AGI, 맥락 팽창, Antigravity, 통계적 예측기 |
| [[20260228_222913_안드레이 카르파티 단 243줄의 파이썬으로 완전한 GPT를 구축하다]] | 2026-02-11 Karpathy가 공개한 microGPT — PyTorch/TensorFlow 없이 os/math/random/argparse만 쓴 243줄 파이썬 파일로 아기 이름 학습·생성 GPT 완성. Value 클래스 40줄 오토그래드(연쇄 법칙 + 위상 정렬 역전파), wte/wpe 임베딩, Q/K/V 멀티헤드 어텐션, 제곱 ReLU MLP, 가중치 공유, Adam 옵티마이저 순수 구현. micrograd(2020)→minGPT→nanoGPT→llm.c(2024)→microGPT(2026) 6년 압축 여정. | Karpathy microGPT, 243줄 파이썬, 순수 파이썬 GPT, Value 오토그래드, 위상 정렬 역전파, 제곱 ReLU, 가중치 공유 weight tying, micrograd minGPT nanoGPT llm.c, 아기 이름 생성, 4000 파라미터 |
| [[20260322_122503_LLM의 기억력을 100배 향상시킬 수 있는 한 가지 수학적 기법]] | Nikhil Anand가 트랜스포머 O(t²) 셀프어텐션 한계를 선형 어텐션 M_t 행렬 메모리 상태(O(1))로 극복하는 수학적 원리를 설명. RNN(벡터 은닉상태) vs 트랜스포머(O(t²)) vs 선형 어텐션(행렬 은닉상태 O(1)) 3자 비교로 Mamba, SSM, TTT(Test-Time Training), 하이브리드 아키텍처의 공통 수학적 기반 분석. 항등 커널 함수 기반 선형화와 100배 메모리 효율 가능성 제시. | 선형 어텐션, M_t 행렬 메모리 상태, 항등 커널 함수, Mamba SSM, TTT Test-Time Training, 하이브리드 트랜스포머, O(t²) 셀프어텐션, 벡터-행렬 은닉상태, Nikhil Anand |
| [[20260326_160626_하룻밤 새 100번 스스로 개선된 AI 아무도 막지 못했다]] | MiniMax M2.7이 MLE-Bench Lite 66.6% 달성(Gemini 3.1 동률). Andrej Karpathy의 AutoResearch(밤새 100+ 실험 자동 루프) 개념을 산업 규모로 구현해 M2.7이 자체 워크플로 파일 수정(온도 보정·오류 패턴 감지) 100회 이상 반복으로 내부 벤치마크 30% 개선, RL팀 업무 30~50% 대체. 0.30달러/1M 입력토큰. ICLR 2026 재귀적 자기 개선 최초 학술 워크숍 개최. OpenRoom(AI가 자연어로 앱 작동) 동시 출시. | MiniMax M2.7, AutoResearch Karpathy, 재귀적 자기 개선 recursive self-improvement, MLE-Bench Lite 66.6%, 워크플로 자기 수정, OpenRoom 브라우저 에이전트, ICLR 2026 자기 개선 워크숍, 0.30달러 1M 토큰, Polsia 에이전트 기업 |
| [[20260402_073733_2026년 등장할 새로운 유형의 AI LLM보다 뛰어난가]] | Meta의 VL-JEPA 논문을 바탕으로 다음 단어 예측이 아니라 의미를 예측하는 joint embedding 아키텍처가 차세대 지능의 후보라고 소개한다. selective decoding과 Yann LeCun의 비언어 중심 지능론을 연결해, 말보다 이해를 우선하는 하이브리드 모델 시대를 전망한다. | VL-JEPA, Yann LeCun, selective decoding, joint embedding predictive architecture, Meta research, multimodal understanding |
| [[20260404_104650_Backpropagation Is All You Need]] | 딥러닝의 핵심 알고리즘인 역전파를 퍼셉트론부터 트랜스포머까지 이어지는 역사와 수학으로 설명한다. 연쇄 법칙, 손실 함수, gradient descent, vanishing gradient, ReLU, Xavier 초기화, residual connection 같은 개념이 현대 모델 학습을 가능하게 한 과정을 정리한다. | backpropagation, chain rule, gradient descent, vanishing gradient, ReLU, Xavier initialization, residual connection, transformer training |
| [[20260404_104919_10년 만에 Adam에 도전하는 첫 번째 옵티마이저 모델 훈련 비용을 절반으로 절감하다]] | 오랫동안 표준이던 Adam에 도전하는 새로운 옵티마이저가 모델 훈련 비용을 절반 수준까지 줄일 가능성을 제시한다. 학습 안정성과 비용 효율, 대규모 모델 훈련 스택에 미치는 영향을 중심으로 기존 optimizer 패러다임 변화를 다룬다. | Adam optimizer, training cost reduction, new optimizer, model training, optimization algorithm, half-cost training |
| [[20260404_185017_RLM 인공지능의 궁극적 진화 재귀적 언어 모델Recursive Language Models]] | Recursive Language Models라는 개념을 통해 언어 모델이 자기 호출 구조를 활용해 더 긴 추론과 구조적 사고를 수행할 가능성을 다룬다. 단일 패스 생성에서 벗어나 재귀적 분해와 반복 추론을 활용하는 모델 진화 방향을 설명하는 이론 중심 글이다. | Recursive Language Models, RLM, recursive reasoning, model architecture, long-horizon inference, iterative generation |
| [[20260409_075445_초소형 AI 드디어 준비되었나 저렴한 AI를 향하여 Bonsai의 1비트 모델에 주목해야 할 이유]] | 초소형 AI와 저가 추론 시대를 열 후보로 Bonsai의 1비트 모델을 소개한다. 모델 경량화와 비용 절감을 중심으로 소형 모델이 실제 배포 가능 단계에 가까워졌다는 점을 강조한다. | Bonsai, 1-bit model, tiny AI, low-cost inference, compact model, model compression |
| [[20260409_163131_Build and Train an LLM from Scratch]] | 완전한 디코더 전용 트랜스포머와 토크나이저를 만든 뒤 WikiText-103로 텍스트 생성 모델을 end-to-end 학습시키는 튜토리얼이다. 5억3592만3053자 학습 데이터, 문자 단위 토크나이저 1251 vocabulary, Hugging Face datasets 로딩까지 포함해 밑바닥부터 LLM 학습 과정을 설명한다. | WikiText-103, decoder-only transformer, tokenizer, Hugging Face datasets, 535923053 characters, vocabulary 1251, LLM training |
| [[20260418_113456_Kimi 모든 AI 모델의 10년 된 근본 결함을 해결하다 어텐션 잔차Attention Residuals의 혁신]] | Kimi가 Attention Residuals라는 개념으로 기존 모델의 10년 된 구조적 결함을 해결한다고 소개하는 모델 분석 글이다. 아키텍처 수준 혁신을 통해 추론 품질과 효율을 개선하려는 시도를 다룬다. | Kimi, Attention Residuals, model architecture, foundational flaw, inference improvement, Moonshot AI |
| [[20260420_222441_르쿤의 월드 모델LeWorldModel 과장된 서사와 실제 기술적 기여]] | LeWorldModel이 Yann LeCun의 순수한 발명이 아니라 Schmidhuber, Sutton, Ha의 월드 모델 계보 위에 있으며, 진짜 기여는 JEPA 학습을 단순화하는 SIGReg 정규화에 있다는 점을 짚는다. 1500만 파라미터, 192차원 잠재 벡터, 단일 GPU 훈련이라는 구체 수치와 함께 과장된 투자 서사를 비판한다. | LeWorldModel, Yann LeCun, SIGReg, JEPA, Jürgen Schmidhuber, Dyna, 15M parameters |

## 관련 카테고리

* > 상위: [[LLM Models Index]]
* > 최상위: [[ArticleScrap Platform Index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
