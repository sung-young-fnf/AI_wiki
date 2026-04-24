# 번역 기사

- **원본 URL:** https://share.google/c9rjx47qqUyahB2Sa
- **번역 일시:** 2026-03-29 19:53:32

---

# A.T.L.A.S: 500달러 GPU로 코딩 벤치마크에서 Claude Sonnet 능가 (github.com/itigges22)

6P (by GN⁺) 2일 전 | ★ 즐겨찾기 | 댓글 1개

A.T.L.A.S (Adaptive Test-time Learning and Autonomous Specialization)는 소비자용 GPU 단 한 대로 대형 모델(Large Model) 수준의 코드 생성 성능을 구현하는 자체 호스팅(self-hosted) AI 시스템입니다.

LiveCodeBench v5 기준으로 74.6% pass@1-v(k=3)를 기록하여 Claude 4.5 Sonnet(71.4%)을 능가했으며, 이전 버전 대비 두 배 가까운 성능 향상을 달성했습니다.

14B 파라미터(parameter) 모델(Qwen3-14B-Q4_K_M)을 동결(freeze)한 채로 제약 기반 생성(constraint-based generation), 자체 검증 및 수정 루프(self-verification and repair loop), 그리고 Geometric Lens 후보 선택 방식을 통해 고성능을 확보합니다.

클라우드나 API 호출 없이 로컬 환경에서 완전 자율적으로 실행되며, 비용은 전력비만 발생하므로 API 기반 모델(API-based model) 대비 비용 효율성(cost-efficiency)이 매우 높습니다.

RTX 5060 Ti 16GB GPU 환경에서 약 2시간 이내에 599개 과제를 처리하며, 대형 모델의 코드 생성 능력을 개인 하드웨어로 재현할 수 있습니다.

## 벤치마크 결과

*   LiveCodeBench v5: 74.6% pass@1-v(k=3), 599개 과제 수행
*   V3 파이프라인: PlanSearch + self-verified PR-CoT repair
*   GPQA Diamond: 47.0%, 198개 과제
*   SciCode: 14.7%, 341개 과제

pass@k-v(k=3)는 단일 시도(single attempt) 결과가 아니라, 3개의 후보 생성 후 렌즈(Lens) 선택 및 실패 시 반복 수정을 포함하는 방식입니다.

## V3 단계별 기여도 (Ablation Study)

*   A: 기본형 (V3 미적용) → 54.9%
*   B: Phase 1 (PlanSearch + BudgetForcing + DivSampling) → 67.3% (+12.4pp)
*   C: Phase 1+2 (Lens routing) → 67.3% (+0.0pp)
*   D: Phase 1+3 (self-verified refinement) → 74.6% (+7.3pp)

Phase 3은 모델이 자체 생성한 테스트 케이스(test case)로 내부 검증을 수행하며, 실제 정답은 사용하지 않습니다.

PR-CoT는 Phase 3에서 42개 문제 중 36개(85.7%)를 복구했습니다.

## 비용 및 성능 비교

| 시스템                    | LCB pass@1 | 과제당 비용(Cost per Task) | 비고(Notes)                     |
| :------------------------ | :--------- | :------------------------- | :------------------------------ |
| DeepSeek V3.2 Reasoning   | 86.2%      | ~$0.002                    | API, 단일 시도(single-shot)     |
| GPT-5 (high)              | 84.6%      | ~$0.043                    | API, 단일 시도                  |
| ATLAS V3                  | 74.6%      | ~$0.004                    | 로컬 전력만 사용, best-of-3 + repair 파이프라인 |
| Claude 4.5 Sonnet         | 71.4%      | ~$0.066                    | API, 단일 시도                  |
| Claude 4 Sonnet           | 65.5%      | ~$0.066                    | API, 단일 시도                  |

ATLAS는 전력비만 발생하며, API 비용은 없습니다.
165W GPU 기준으로 599개 과제를 수행하는 데 약 1시간 55분이 소요됩니다.
지연 시간(latency)은 길지만 비용 효율성(cost-efficiency)이 매우 높습니다.

## 작동 원리

### 전체 파이프라인

*   **Phase 1: Generate**
    *   PlanSearch: 제약 추출 및 다양한 계획 생성
    *   Budget Forcing: 토큰 사용량 제어
*   **검증 단계(Verify Phase)**
    *   Geometric Lens (C(x)): 5120차원 자체 임베딩(embedding) 기반 에너지 스코어링(energy scoring)
    *   샌드박스(Sandbox): 코드 실행 및 검증
*   **Phase 3: Repair**
    *   Self-Test Generation: 모델이 자체 입출력 쌍(input/output pair) 생성
    *   PR-CoT Repair: 다중 관점 체인오브소트(Chain-of-Thought, CoT) 기반 코드 수정

단일 llama-server 인스턴스(instance)가 K3s 상에서 실행되며, 추측적 디코딩(speculative decoding)과 자체 임베딩 생성을 동시에 수행합니다.

Geometric Lens는 후보 중 최적의 코드를 선택합니다(혼합 결과 과제에서 87.8%의 정확도를 보였습니다).
실패한 과제는 Phase 3으로 이동하여 자체 테스트를 생성하고 반복 수정을 수행합니다.

## 설치 및 실행

GitHub 저장소(repository)를 클론(clone)한 후 설정 파일 복사 및 설치 스크립트 실행
`benchmark/v3_runner.py`로 V3 벤치마크 실행
세부 설치 절차는 `docs/SETUP.md`를 참고하세요.

## 하드웨어 및 재현

| 자원(Resource) | 최소(Minimum) | 테스트 환경(Test Environment) |
| :------------- | :------------ | :---------------------------- |
| GPU VRAM       | 16 GB         | RTX 5060 Ti 16 GB             |
| 시스템 RAM     | 14 GB         | 16 GB                         |
| Python         | 3.10+         | 3.11                          |
| OS             | RHEL 9 / Ubuntu 24 | RHEL 9 (Proxmox VM)           |

Proxmox VM + VFIO GPU 패스스루(passthrough) 환경에서 재현되었습니다.
16GB 이상 VRAM을 가진 다른 NVIDIA GPU에서도 가능하지만, 드라이버 및 VRAM 설정 조정이 필요합니다.

**주요 조정 변수:**

*   `--parallel` 슬롯 수 (기본 2개, VRAM 부족 시 1개로 감소)
*   KV 캐시 양자화(KV cache quantization, Q4_0)
*   슬롯당 컨텍스트 길이(context length, 기본 20480 토큰)
*   CUDA 12.8 버전 테스트 완료
*   V3.1에서 이식성(portability) 개선 예정

## 로드맵

### V3.0 (완료, 2026-03-05)

*   Qwen3-14B-Q4_K_M 기반, 74.6% LCB 성능
*   PlanSearch + BudgetForcing + Geometric Lens + PR-CoT 파이프라인 완성

### 알려진 한계

*   LCB 전용 최적화: GPQA, SciCode 등 다른 벤치마크(benchmark) 최적화 미흡
*   Phase 2 (Lens routing): 데이터셋(dataset) 부족으로 효과 미미(+0.0pp)
*   G(x) metric tensor 비활성화: C(x) 미훈련으로 의미 있는 기하 구조(geometric structure) 부재
*   단일 스레드(single thread) 처리: 과제 병렬화(task parallelization) 미지원
*   SandboxAdapter stdio 버그: 입력 구분 기능 비활성화(V3.1에서 수정 예정)

### V3.1 (진행 중)

*   모델 교체: Qwen3-14B → Qwen3.5-9B (DeltaNet 선형 어텐션(linear attention), 3~4배 속도 향상)
*   렌즈(Lens) 재학습: 실시간 피드백(feedback) 기반 C(x) 재보정
*   Phase 2 재설계: G(x) 재구현 또는 제거, SandboxAdapter 버그 수정
*   병렬 처리(parallel processing) 도입: 과제 병렬 실행으로 처리 속도 향상
*   확장된 벤치마크 스위트(benchmark suite): 코딩 외 추론 및 지식 평가 포함

### 예정된 V3.1 벤치마크

*   코딩: LiveCodeBench v5, SciCode, 추가 오염 저항형 데이터셋(pollution-resistant dataset)
*   추론/지식: GPQA Diamond, AA-LCR, AA-Omniscience, Humanity’s Last Exam, CritPt 등
*   Confidence Router가 과제 난이도에 따라 경로를 선택:
    *   단순 질의(simple query) → RAG(Retrieval-Augmented Generation) 기반 빠른 추론(~30초)
    *   복잡한 코딩 문제 → 전체 파이프라인(~20분)
*   목표: 80~90% LCB pass@1-v(k=3) 및 더 빠른 처리 속도

## 라이선스

A.T.L.A.S Source Available License v1.0 적용

---

▲
GN⁺ 2일 전  [-]

## Hacker News 의견들

저는 에이전트(agent)에게 큰 코드 블록 생성을 기대하지 않습니다. 대신 로그를 훑거나 여러 소스 파일(source file)을 분석하여 테스트 실패 원인을 설명하는 데 훨씬 유용합니다. 이러한 능력을 평가하는 디버깅 벤치마크(debugging benchmark)가 필요하다고 생각합니다. 빌드 시스템(build system)이나 CLI(Command Line Interface) 숙련도를 측정하는 테스트도 있으면 좋겠습니다.

저도 동의합니다. 코드베이스(codebase) 전체에 일관된 소규모 변경을 적용할 때 특히 유용합니다. 예를 들어, 앱 전체를 하드 삭제(hard delete)에서 소프트 삭제(soft delete)로 리팩터링(refactoring)했는데, 삭제 로직(logic)과 쿼리(query) 모두 수정해야 했습니다. 수동으로 하면 지루하고 실수하기 쉬운데, 에이전트가 빠르게 처리해줘서 정말 고마웠습니다. 이러한 장기적 작업에는 SWE Bench Pro나 Terminal Bench 2가 적합합니다. SWE Bench Pro는 아직 과도하게 최적화(optimization)되지 않아 신뢰할 만합니다. 반면 일반 SWE나 LCB는 이미 수치 부풀리기 경쟁으로 실효성이 떨어집니다. 빌드 시스템 관련 테스트는 CompileBench(Quesma의 벤치마크)에서 다룹니다. 참고로, 저는 Quesma의 창립자입니다.

저는 하루 종일 대규모 코드 생성만 합니다. 이제는 회사든 사이드 프로젝트(side project)든 직접 코드를 거의 쓰지 않습니다. 주로 Rust와 TypeScript로 개발자 도구(developer tool)를 만드는 일을 합니다. 저도 환경 구성을 에이전트에게 맡깁니다. `kubectl` / `helm`으로 배포하고 문제 발생 시 에이전트가 직접 디버깅(debugging)합니다. 몇 시간 걸리던 일이 순식간에 끝나서 놀라울 정도입니다.

개발자들에게 MiniMax, Kimi 같은 모델을 실제 업무에 사용해 보라고 권하고 싶습니다. 하지만 단점도 빠르게 드러납니다 — 추론 토큰(inference token) 사용량 증가, 느린 출력(output), 품질 저하 등입니다. 그래도 모델 라우팅(model routing)과 reasoning budget을 잘 관리하면 비용을 크게 절약할 수 있습니다. 앱과 프롬프트(prompt)를 최적화(optimize)하여 출력 토큰을 줄이는 것도 중요합니다.

저도 Kimi로 괜찮은 결과를 얻고 있습니다. 하지만 어려운 작업에서는 단순한 토큰 단가보다 효율이 더 중요합니다. 링크에 나온 접근법은 Sonnet과 Opus에도 효과가 있습니다. 다만 MiniMax나 Qwen은 아직 그 수준에 미치지 못합니다. 결국 어떤 작업에 어떤 모델이 비용 효율적인지 구분하는 하네스(harness) 설계가 핵심입니다. 저는 SOTA(State-Of-The-Art) 이하 모델은 쓰지 않습니다. Opus 4.6 medium을 써봤다가 바로 후회했습니다. 품질 차이가 너무 큽니다. 이 링크에서 보듯, MiniMax는 비코딩 작업에서는 성능이 낮습니다. `aibenchy` 비교 결과, 저는 MiniMax를 매일 코딩용으로 사용합니다. 토큰 사용량은 신경 쓰지 않습니다. 월 10유로 요금제에서 5시간마다 1500회 요청이 가능합니다. 실제로는 Opus나 Sonnet보다 느리지 않습니다. 벤치마크(benchmark)에서는 Anthropic 모델이 더 좋아 보이지만, 현실 작업에서는 차이가 거의 없습니다. MiniMax가 막히면 Opus로 전환하고, 다시 MiniMax로 돌아오면 됩니다. Opus는 예산을 빨리 소모하지만 MiniMax는 사실상 무제한입니다. Kimi는 최근 저의 디버깅(debugging)용 주력 모델입니다. Claude나 GPT보다 문제를 더 빨리 찾아냅니다. 하지만 문맥 일관성(contextual consistency) 문제가 심각합니다 — 코드 재작성 시 작은 오차(error)가 생겨 전체를 망칠 수 있습니다.

지금은 가격 경쟁의 끝없는 레이스(race)입니다. DeepSeek이 단일 실행(single-shot) 기준으로 모든 모델을 이기고, 비용도 로컬 전력의 절반 수준입니다.

| 시스템                    | LCB pass@1 | 과제당 비용(Cost per Task) | 비고(Notes)                     |
| :------------------------ | :--------- | :------------------------- | :------------------------------ |
| DeepSeek V3.2 Reasoning   | 86.2%      | ~$0.002                    | API, 단일 시도(single-shot)     |
| ATLAS V3                  | 74.6%      | ~$0.004                    | 로컬 전력만 사용, best-of-3 + repair 파이프라인 |

저는 로컬(local)에서 돌릴 수 있다면 전기요금 0.004달러쯤은 감수하겠습니다. 하지만 여전히 1989년 톈안먼 사건 같은 질문에는 답하지 않습니다… 여러 오픈 모델(Open Model)을 테스트했지만, DeepSeek 3.2만이 진짜 SOTA(State-Of-The-Art) 수준입니다. DeepSeek에도 이 접근법을 적용할 수 있습니다. 여러 해답을 생성하고, 작은 모델로 유망한 후보를 선택해 테스트 후 피드백(feedback)을 반복하는 방식입니다. 일종의 유전 알고리즘(genetic algorithm)처럼 점진적으로 수렴(converge)합니다. “전기요금보다 싸다”는 것이 무슨 뜻인지 설명해 줄 수 있나요?

작은 모델들은 테스트에 맞춰 과도하게 미세조정(fine-tuning)되어 실제 환경에서는 성능이 형편없습니다.

저는 항상 회의적입니다. 벤치마크(benchmark)는 통과하지만 실제로는 범용성(generality)이 떨어지는 경우가 많습니다. 그래도 모델을 슬림화하려는 시도 자체는 흥미롭습니다.

언어와 분야에 따라 차이가 큽니다. 시스템 프로그래밍(C++, Rust)에서는 여전히 Sonnet 4.5 수준과 큰 격차(gap)가 있습니다. 오픈 모델들은 구문 오류(syntax error) 해결에 너무 많은 시간을 쓰고, 논리적 일관성(logical consistency)을 잃는 경우가 많습니다. 로컬 GPU를 충분히 갖고 있지만, 클라우드 모델(cloud model)의 라이선스(license) 문제도 걱정됩니다. ATLAS의 접근법은 꽤 영리합니다. 여러 솔루션(solution)을 생성하고, 각 코드의 임베딩 지문(embedding fingerprint)을 계산하여 정확도를 예측합니다. 작은 신경망(neural network)인 Cost Field가 이를 점수화하여 가장 가능성 높은 코드를 선택합니다. 덕분에 테스트 시간을 줄이면서도 88% 정확도로 올바른 해답을 고릅니다. 모델을 줄이면 각 뉴런(neuron)이 여러 역할을 맡게 되어 일반화 능력(generalization ability)이 떨어집니다. 오히려 큰 모델이 구조적으로 단순할 수 있습니다.

읽는 사이에 GPU 가격이 1000달러가 되어버렸네요.

이 AI가 작성한 프로젝트는 LiveCodeBench와 완전히 다른 방식으로 자체 벤치마크를 실행합니다. README 파일에는 ATLAS 점수가 599개 LCB 작업을 기반으로 한 V3 파이프라인(best-of-3 + Lens + iterative repair) 결과라고 명시되어 있습니다. 반면 경쟁 모델 점수는 단일 실행(single run, pass@1) 기준이라 비교가 불공정합니다. Sonnet이나 GPT5.4도 같은 방식으로 테스트하면 더 높은 점수를 낼 수 있습니다. README 파일에는 실제로 사용되지 않는 구조들이 많아 AI 자동 생성 코드의 허술함이 드러납니다.

이러한 벤치마크(benchmark)가 특정 문제 전용 최적화(problem-specific optimization)에만 효과적인지 궁금합니다. 결국 우리는 일반성(generality)을 압축할 수 없는 한계를 배우게 될 것입니다.

“Geometric Lens routing”이라는 표현이 너무 이상합니다. 그냥 GPT가 만들어낸 용어처럼 들립니다.

회의적이기는 하지만, 이러한 오픈 모델(Open Model) 실험은 정말 반갑네요. 중상급 PC에서도 로컬(local)로 모델을 돌릴 수 있다면 큰 진전입니다.

답변달기
GeekNews가 처음이신가요? 여기를 읽어주세요
GeekNews는 개발·기술·스타트업 소식을 매일 소개하는 뉴스 커뮤니티입니다.
매일 올라오는 글을 둘러보시고, 놓치고 싶지 않다면 Weekly 뉴스레터를 구독해보세요.

처음 오셨나요 사이트 이용법 FAQ About 이용약관 개인정보 처리방침   | Blog Lists RSS   | Bookmarklet
X (Twitter) Facebook   |   긱뉴스봇 : Slack 잔디 Discord Teams Dooray! Google Chat Swit
검색