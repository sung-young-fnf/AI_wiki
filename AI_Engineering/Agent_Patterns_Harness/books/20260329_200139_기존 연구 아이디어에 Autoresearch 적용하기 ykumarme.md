# 번역 기사

- **원본 URL:** https://share.google/ANpxiJx6DSInFAdRE
- **번역 일시:** 2026-03-29 20:01:39

---

# 기존 연구 아이디어에 Autoresearch 적용하기 (ykumar.me)

GeekNews 최신글 | 댓글 | 예전글 | Ask | Show | GN⁺ | Weekly | 글 등록 | 로그인
▲
4P by GN⁺ 5일 전 | ★ 즐겨찾기 | 댓글 1개

Autoresearch 시스템은 LLM 에이전트(LLM agent)가 `train.py` 파일을 반복적으로 수정하여 성능을 개선하는 제약 최적화 루프(constrained optimization loop) 구조를 가지며, 가설 설정부터 평가까지 자동 순환을 수행합니다.
실험은 컨테이너 기반 샌드박스(sandbox) 환경에서 실행되어 네트워크 접근 및 임의 코드 실행을 차단합니다.
Ukiyo-eVG 데이터셋(Ukiyo-eVG dataset)을 사용하여 약 11,000장의 일본 목판화 이미지와 해당 주석 정보를 학습에 활용했으며, CLIP 기반 모델(CLIP-based model)로 Mean Rank 34.30, Recall@5(R@5) 약 53%의 성능을 달성했습니다.
주요 개선은 temperature 파라미터 완화(temperature parameter relaxation, Mean Rank -113)와 하이퍼파라미터 튜닝(hyperparameter tuning, Mean Rank -30)으로, 하루 42회 실험 중 13회 커밋(commit)을 통해 54%의 성능 향상을 기록했습니다.
LLM 에이전트는 명확히 정의된 탐색 공간(search space) 내에서는 효과적이지만, 구조 변경 단계(structural modification phase) 이후에는 불안정성이 커지면서 완전한 자율 연구(fully autonomous research)에는 한계가 드러났습니다.

## 핵심 아이디어
Autoresearch는 LLM 에이전트를 중심으로 하는 제약 최적화 루프(constrained optimization loop) 구조로, 에이전트가 `train.py` 파일을 수정하며 평가 지표를 반복적으로 개선합니다.
에이전트는 `program.md`의 지침을 읽고, `scratchpad.md`를 작업 메모(working memory)로 사용하여 실험 과정을 기록합니다.
탐색은 여러 단계(phase)로 구성되며, 초기에는 하이퍼파라미터 튜닝(hyperparameter tuning)에서 시작하여 소규모 구조 변경으로 이어지고, 이후에는 제약을 최소화한 자유 탐색(free exploration)으로 확장됩니다.
전체 루프는 가설 설정 → 코드 수정 → 학습 → 평가 → 커밋(commit) 또는 되돌리기(revert) → 반복의 순환 구조(cyclical structure)로 설계됩니다.
각 실험은 약 5분 내에 완료되도록 제한하여 빠른 반복과 과적합 방지(prevent overfitting)를 유도합니다.
에이전트는 시간 제한 내에서 `train.py` 파일을 자유롭게 수정할 수 있습니다.

## 샌드박싱(Sandboxing)
임의 코드 실행 위험을 방지하기 위해 컨테이너(container) 환경에서 학습 루프를 실행하고 네트워크 접근을 차단합니다.
`run.sh` 스크립트가 전체 실험 흐름을 관리하며, Claude Code는 `train.py`와 `program.md` 파일만 수정할 수 있습니다.
Python 직접 실행, `pip` 설치, 네트워크 접근, `git push` 등은 모두 제한됩니다.
관련 구현은 GitHub 저장소(repository)에서 공개되었습니다.

## 데이터셋(Dataset)
원래 연구에서 사용된 의료 X-ray 데이터셋(medical X-ray dataset)에 접근할 수 없어, Ukiyo-eVG 데이터셋을 새로 사용했습니다.
약 11,000개의 일본 목판화 이미지와 문구-바운딩박스 주석(phrase-bounding box annotations)을 포함합니다.
바운딩박스(bounding box)를 가우시안 히트맵(Gaussian heatmap)으로 변환하여 모델 입력에 추가했으며, 이는 원래 eCLIP 논문의 전문가 주의(attention) 메커니즘과 유사한 방식으로 적용되었습니다.
히트맵은 모델이 특정 영역에 집중하도록 유도합니다.

## Claude Code와의 실험 설정
Claude Code가 기존 연구 코드를 최신 Python 환경으로 업그레이드하고, 새로운 데이터셋 로딩 및 실험 루프 스캐폴딩(experiment loop scaffolding)을 작성했습니다.
교차 검증 분할(cross-validation split), 평가 로직(evaluation logic), 그리고 `program.md`의 초기 아이디어를 설정했습니다.
평가 지표로는 Mean Rank를 사용했으며, 최종 보고에는 Recall@K를 병행했습니다.
Mean Rank는 직관적 판단용으로 사용되었으나, 이상치(outliers)에 덜 민감한 Median Rank가 더 적절했을 것이라고 언급되었습니다.
모델 구성: CLIP 백본(backbone)은 ViT-Small(22M) + DistilBERT(66M) + HeatmapProcessor로 총 약 90M 파라미터(parameter)였습니다.
학습: 800 스텝(steps, 약 3분/실험, RTX 4090 기준)
평가: 1,000장 테스트 세트(test set)에서 Mean Rank 및 Recall@K 측정
기준 성능: 검증(Val) Mean Rank 344.68, img→txt Recall@1(R@1) 17.2%, txt→img Recall@1(R@1) 16.5%

## 실험 결과
하루 동안 총 42회 실험이 진행되었고, 그중 13회 커밋(commit), 29회 되돌림(revert)이 수행되었습니다.
Mean Rank가 344.68에서 157.43으로 54% 감소했습니다.
전체 데이터셋(dataset)으로 최종 학습(training) 시, 테스트 점수(test score)가 검증 점수(validation score)보다 더 높게 나타났습니다.
이는 짧은 800 스텝(step) 실험이 언더피팅(underfitting) 상태였음을 시사합니다.
최종 테스트 성능: Mean Rank 34.30, img→txt Recall@5(R@5) 53.0%, txt→img Recall@5(R@5) 51.4%

## 주요 개선 포인트
### Temperature clamp 수정 (-113 Mean Rank)
코드 내 학습 가능한 temperature 파라미터(parameter)가 2로 고정되어 있었는데, 에이전트가 이를 완화하면서 성능이 크게 향상되었습니다.
이는 전체 개선 중 가장 큰 단일 효과(single effect)를 보였습니다.

### Optuna++ (-30 Mean Rank)
이후 개선은 주로 하이퍼파라미터 튜닝에서 발생했습니다.
투영 차원 증가 및 학습률 재조정(learning rate readjustment)으로 추가 30포인트 개선이 이루어졌습니다.
인간이 반복적으로 수행하는 지루한 작업을 에이전트가 더 빠르고 체계적으로 수행했습니다.

## 수익 감소 구간(Diminishing Returns)
4단계(구조 변경) 이후부터는 LLM의 가설 성공률이 급감했습니다.
주의(attention) 메커니즘 변경이나 대담한 아이디어(moonshot) 시도는 대부분 실패했습니다.
탐색 후반부에서는 무작위적인 시도가 많았습니다.

## 샌드박스의 중요성
Claude Code가 가끔 권한을 잊고 잘못된 bash 호출(call)을 시도하거나, 학습 대기 중 루프를 중단하는 등 불안정한 행동을 보였습니다.
완전한 자율 실행(autonomous execution)에는 아직 한계가 있습니다.

## 마무리 관찰
전체 과정에서 초기 90%는 매끄럽게 진행되었으나, 마지막 10%는 많은 개입이 필요했습니다.
LLM 에이전트는 명확히 정의된 탐색 공간(search space) 내에서는 효과적으로 ML 연구를 수행할 수 있습니다.
Autoresearch의 커밋-되돌리기(commit-revert) 루프는 구조화된 탐색 전략(structured exploration strategy)으로 유용합니다.
그러나 미지의 영역으로 확장되면 최적화 루프가 불안정해집니다.
실험당 한 번의 변경만 허용하는 제약이 대규모 아이디어 탐색에는 지나치게 엄격했을 가능성이 있습니다.
향후에는 계획 단계 추가나 하위 에이전트(subagent) 도입이 개선 방향으로 제시됩니다.
실험 종료 후, Claude Code와의 협업은 일상으로 돌아가며 마무리되었습니다.

## 감사 표시
* Ukiyo-eVG 데이터셋: 약 11,000장의 일본 목판화 이미지와 문구-바운딩박스 주석(phrase-bounding box annotations)을 포함합니다.
* Autoresearch: Andrej Karpathy의 원래 아이디어 기반입니다.

▲
GN⁺ 5일 전 [-]

### Hacker News 의견들
* 메인 링크가 느리다면 archive.is 버전을 시도해볼 것을 제안합니다.

* 저는 종종 LLM을 활용하여 기존 연구를 탐색하거나 문제를 다른 방식으로 생각해 보곤 합니다. 결과의 90%는 제 도메인(domain)에 맞지 않지만, 나머지 10%는 꽤 유용했습니다. 하지만 LLM이 추천한 모든 것을 실제로 시도하게 하는 에이전트(agent)를 두는 것은 비용(cost)이 너무 큽니다. 추천 목록에는 종종 유지보수(maintenance)가 잘 안 되는 니치 라이브러리(niche library)가 많습니다. 반면, 회사의 "전문 컨설턴트"들도 비슷하게 터무니없는 제안을 하곤 해서, 차라리 에이전트가 그들을 대신 상대해 줬으면 합니다.

* 에이전트의 가치는 사용자가 쉬는 동안 자동으로 실험을 반복할 수 있다는 점에 있습니다. 단, 한 번의 테스트(test)가 빠를 때만 의미가 있습니다. 제 업무에서는 테스트 하나에 반나절이 걸려서 밤새 돌리기는 어렵습니다. 어떤 도메인에서 일하시는지 궁금합니다.

* 저는 LLM이 기억하기 귀찮은 짧은 문장이나 틀려도 상관없는 부분에서 유용하다고 느낍니다. MCP 서버나 AGENTS.md 같은 것을 세팅(setting)하는 사람들을 보면, 결국 LLM이 광고된 대로 작동하지 않는다는 증거 같습니다. 특정 워크플로우(workflow)에 맞게 잘 튜닝(tuning)하면 훌륭하지만, 그게 스케일(scale)할 수 있을지는 의문입니다. 막대한 자금이 훈련(training)과 인프라(infrastructure)를 떠받치지 않는다면 지속 가능한 비즈니스 모델(business model)이 될 수 있을까요? 비용이 문제일 수도 있습니다. 저는 Claude Code를 가볍게 사용하는데, Max 플랜(plan)에서도 토큰(token)이 거의 떨어지지 않습니다.

* "에이전트가 하이퍼파라미터 최적화 알고리즘(hyperparameter optimization algorithm)처럼 행동했다"는 표현이 인상적이었습니다. 핵심은 `program.md`라는 시스템 프롬프트(prompt) 파일 하나로, "train.py 개선 → 학습 실행 → 평가 → 결과 기록"을 반복하는 구조입니다. 나머지는 임의의 ML 모델(model)일 뿐입니다.

* 작동 중인 코드를 LLM에 주고 버그 수정, 성능 측정, 테스트 커버리지 평가(test coverage evaluation)를 반복하는 방식이 저희 팀의 표준 접근법입니다. 반복마다 다른 모델을 사용하면 새로운 시각을 얻는 느낌이라 좋았습니다.

* 이 방식을 특정 언어나 프레임워크(framework)에 특화된 로컬 LLM(Local LLM) 학습에 적용할 수 있을지 궁금합니다.

* "Autoresearch"가 왜 이렇게 화제가 되었는지 의문이었습니다. AI/ML의 병목(bottleneck)은 항상 데이터 품질이나 컴퓨팅 자원(computing resources)이라고 생각했는데, 이것이 그걸 개선하는지는 모르겠습니다.

* 사실 이런 시도는 예전부터 있었습니다. AutoML 분야(field)가 그 예인데, 실제로는 잘 안 됐습니다. 베이지안 최적화(Bayesian optimization)나 가우시안 프로세스(Gaussian Process) 같은 접근 방식도 있었지만, 결국 랜덤 서치(random search)가 더 나았습니다. LLM은 문헌을 보고 상식적인 추론을 할 수 있다는 점이 다릅니다. 완벽하진 않지만, 기존 방법보다 나을 가능성은 있습니다. 단순한 하이퍼파라미터 튜닝(hyperparameter tuning)을 넘어 비파라메트릭 구조 변경(non-parametric architectural changes)도 가능하다는 점이 다릅니다. 완전히 새로운 개념은 아니지만, 덜 무차별 대입(brute-force) 방식이기를 기대하는 듯합니다. "Swarm optimization" 같은 기존 기법도 있지만, LLM은 과거 연구를 학습하여 중요한 축에 집중할 수 있다는 점이 다릅니다. 즉, 이미 누군가 수행한 연구를 LLM이 활용할 수 있습니다. "데이터나 컴퓨트가 병목(bottleneck)"이라는 말에는 동의하지 않습니다. ML의 핵심은 같은 입력 X로 더 나은 함수 매핑(function mapping)을 찾는 것입니다. 단순히 컴퓨트(compute)를 늘린다고 해결되지 않습니다. 결국 Autoresearch는 생각 자체를 LLM에 위임하는 방식입니다.

* 결과적으로는 작동했습니다. LLM이 버그를 찾고 최적화도 수행했습니다.

* 하지만 실제로는 대부분의 개선이 버그 수정 + Optuna 튜닝(tuning) 덕분이었습니다. 이런 것은 Claude Code로도 금방 할 수 있습니다. Autoresearch의 진짜 가치는 아키텍처 탐색(architecture exploration)에 있을 듯합니다. 누가 탐색적 모델링(exploratory modeling)에 써본 경험이 있는지 궁금합니다.

* 커밋 로그(commit log, GitHub 링크)를 보니 대부분이 하이퍼파라미터 튜닝이었습니다. 그 정도면 토큰(token) 비용(cost)이 아깝다고 느낍니다.

* Autoresearch에 비용 추정 및 정렬 단계(cost estimation and prioritization step)를 추가하여 사람이 검토 후 실행하도록 하면 효율적일 듯합니다. LoRa 어댑터(adapter)로 비용 피드백(feedback)을 주는 식으로 개선 가능합니다. 사실 Optuna나 `skopt` 같은 오픈소스 툴(tool)로 GPU 없이도 가능합니다.

* 원 논문에서는 의료 X-ray 데이터를 사용했지만, 접근 권한이 없어 Ukiyo-eVG(일본 목판화 11,000장)로 대체했다고 합니다. 이상한 전환처럼 보였습니다. 무료 의료 이미지 데이터는 Cancer Imaging Archive에도 많습니다.

* 맞는 말입니다. 다만 의료 데이터를 에이전트에 맡기기엔 부담스러웠고, 도메인 전이(domain transfer)를 실험해보고 싶었습니다.

* 누군가 이런 실험을 해주길 바랐는데, 실제로 해줘서 반가웠습니다. "훈련이 끝나기를 기다리다 지쳐 대화를 종료했다"는 부분이 웃겼습니다. 결과 공유에 감사합니다.

* 고맙습니다, 즐겁게 읽었다는 답변을 남깁니다.

* 이것은 자동화된 연구라기보다 구조화된 시행착오에 가깝습니다. 결국 핵심은 평가 지표의 품질입니다. 그것이 약하면 잘못된 방향으로 더 빨리 최적화할 뿐입니다.

* 좋은 피트니스 함수(fitness function) 설계는 예나 지금이나 어려운 일입니다. 결국 그것이 바로 과학적 방법론(scientific methodology) 아닌가 하는 의견도 있습니다.

---

답변 달기 | 처음 오셨나요? | 사이트 이용법 | FAQ | About | 이용약관 | 개인정보 처리방침
| 블로그 목록 | RSS | 북마클릿(Bookmarklet)
X (트위터) | 페이스북
| 긱뉴스봇: Slack | 잔디 | Discord | Teams | Dooray! | Google Chat | Swit
검색