# OmniPulse 심층 리서치

> 대상 문서: `20260412_083714_Meta Tribe V2 공개 및 Claude 코드 유출 그리고 에이전트 신호 엔진 구축을 위한 나의 연구 접목 뇌파에서 죽은 은하계까지 웨이블릿 산란 변환Wavelet Scatt.md`
> 조사 방법: Brave Search + Perplexity Sonar + PyPI/piwheels 교차 검증
> 작성일: 2026-04-12

OmniPulse는 **개인 연구자가 3년간 구축한 오픈소스 신호 처리 AI 에이전트 프레임워크**입니다. 동명의 의료기기(J&J OMNYPULSE™)나 산업 드라이브(Magnetek)와는 별개의 프로젝트입니다.

---

## 1. 정체성 — "물리학 × 산업 엔지니어링의 가교"

- **PyPI 패키지로 실존 확인**: piwheels에 `omnipulse` 등재 — 오픈소스 배포 중
- **3년 여정**: "뇌 신호에서 감정 찾기"라는 신경과학 질문 → "죽은 은하계에서 고속 전파 폭발(FRB) 찾기"라는 천체물리 문제로 확장
- **핵심 통찰**: "잡음 속 1ms 간질 스파이크 탐지"와 "RFI 속 FRB 탐지"는 **수학적으로 동일한 문제** (순간적 에너지 폭발 + 극심한 잡음 + 푸리에 실패)

---

## 2. 수학 엔진 — Wavelet Scattering Transform (WST)

### 2.1 왜 WST인가 (딥러닝이 아닌)

| 문제 | CNN의 한계 | WST의 해법 |
|---|---|---|
| Sim-to-Real Gap | 시뮬레이션 훈련 → 실제 데이터 붕괴 | 학습 불필요, 수학적으로 고정된 웨이블릿 필터 |
| 변형(Deformation) 문제 | 작은 변형에 불안정 | Lipschitz 연속성으로 **수학적으로 보장된** 안정성 |
| 데이터 부족 | 라벨 필요 | 비지도·특징 추출 |

WST 이론 자체는 **Stéphane Mallat(École Normale Supérieure) + Joan Bruna** 그룹의 연구(2012~)에서 시작. OmniPulse는 그 이론의 **응용·엔지니어링 패키징**.

### 2.2 kymatio 엔진 활용

- **kymatio**: Mallat/Bruna 그룹이 만든 PyTorch/TF/JAX 백엔드 GPU 가속 WST 라이브러리 (JMLR 2020, arXiv:1812.11214)
- 기존 ScatNet(너비 우선) 대비 kymatio의 **깊이 우선 산란 트리 탐색**으로 GPU 메모리 병목 회피 → 대규모 시계열 배치 동시 처리
- OmniPulse는 kymatio를 "엔진"으로 삼고, 그 위에 **이상 탐지 파이프라인 + 에이전트 오케스트레이션**을 얹음

### 2.3 계층적 특징 추출

- **S₀ (DC 성분)**: 저역 통과 스케일링 필터 φ(t), 공간 창 2^J, "배경 잡음 기준선" 포착
- **S₁ (1차 산란)**: 첫 번째 웨이블릿 뱅크 Q₁, 에너지 스펙트럼 계측
- **S₂ (2차 산란) ★마법의 레이어**: Q₂와 재귀 컨볼루션 → **규모 간 시간 의존성 + 복소 진폭 변조 + 숨겨진 비선형 하모닉 구조** 추출 → 실제 이상 징후 vs 계측 오류 구별

---

## 3. 엔지니어링 아키텍처 — Claude Code에서 영감

문서에서 "**51만 2천 라인의 Claude 코드베이스**에서 직접 영감"이라 명시. 핵심은 **급진적 디커플링**.

### 3.1 이중 언어 마이크로서비스

| 레이어 | 역할 | 기술 스택 |
|---|---|---|
| **Python 과학 엔진 (근육)** | 신호 로딩, NaN/Inf 정제, WST 실행, PCA 압축 — **의사결정 없음** | PyTorch + Kymatio, 엄격한 타입, PyPI 배포 |
| **TypeScript 에이전트 오케스트레이터 (뇌)** | 상태 유지 "연구실 관리자" | Bun + React-reconciler (Ink) |

### 3.2 MCP 기반 통신 (REST API 거부)

- **Bun TypeScript 에이전트 = MCP Host**
- `bun spawn`으로 Python MCP Server 부팅 → 상태 유지 JSON-RPC 핸드셰이크
- 런타임에 **기능 표면(capability surfaces) 동적 발견** — 하드코딩된 엔드포인트 없음
- **Zod 스키마**로 교차 언어 통신 엄격 검증 (J 스케일, Q 웨이블릿, PCA 플래그 등) → LLM이 잘못된 수학 파라미터 생성 방지

### 3.3 Claude Code에서 빌려온 패턴

- **UI Context (React/Ink)**: 터미널에 React-reconciler → ANSI 코드 매핑 → 실시간 진행률, 대화형 승인 다이얼로그
- **KAIROS 영감 데몬 모드**: 사전 예방적(proactive) 백그라운드 폴링 — 원시 데이터 스트림 자동 감시
- **도구 호출**: `ExecuteWSTTool`, `ArtifactRejectionTool`, `QueryEngine` 등 명시적 도구화

---

## 4. 파운데이션 모델 통합 — Meta TRIBE v2 + TF-C

수학 엔진이 추출한 PCA 압축 이상징후 행렬은 다음 단계로 흐름.

- **Meta TRIBE v2**: Meta AI의 뇌 활동 예측 파운데이션 모델 (LLaMA, V-JEPA 기반 인코더 → Subject Block → fMRI 예측). 멀티모달 신호(이미지/오디오/텍스트) 공통 브레인 매핑
- **TF-C (Time-Frequency Consistency)**: 시간 도메인과 주파수 도메인 표현의 일관성을 자기지도 학습 대조 손실로 정렬
- **TFM-Tokenizer**: 연속 수학 → 이산 트랜스포머 토큰 변환
- **결합 효과**: TF-C 정렬 토큰 + TRIBE v2 Subject Block → **제로샷 이상 탐지**, 원시 수학 계수와 고수준 구조적 매핑 간극 해소

---

## 5. 실제 사용 사례

### 5.1 신경과학 — 뇌전증 스파이크 탐지

- EEG 잡음 속 1ms 간질 스파이크 탐지
- 관련 연구: WST는 뇌의 시공간 수용장(STRF) 모델링에 적합 — 청각·신경 음향 합성 벤치마크에서 유효성 입증됨

### 5.2 천체물리학 — 고속 전파 폭발(FRB) 탐지

- RFI(지구 전파 간섭) 속에서 먼 은하계 FRB 식별
- 외부 검증 (Breakthrough Listen, Oxford + NVIDIA 2024~2025):
  - **600배 처리 속도 향상**
  - **정확도 7%p 향상**
  - **거짓 양성 10배 감소**
  - Crab Pulsar 거대 펄스 실시간 탐지 성공
- OmniPulse는 이 흐름에 부합하는 **오픈소스·재현 가능 버전**을 지향

### 5.3 워크플로우 (Before/After)

- **Before**: 모놀리식 Jupyter 노트북 → 차원 불일치 → 커널 사망 → 메모리 누수 → 밤새 파이프라인 중단
- **After**: Bun 에이전트 시작 → 데몬이 데이터 폴링 → MCP로 Python 엔진에 위임 → WST 추출 → QueryEngine이 JSON 파싱 → 이상 시 `ArtifactRejectionTool` 자동 트리거 → 다음날 아침 "수학적으로 검증되고 파운데이션 모델로 분류된 이상 징후 폴더" 완성

---

## 6. 포지셔닝 — 왜 중요한가

| 측면 | 기존 과학 소프트웨어 | 기존 산업 AI 에이전트 | **OmniPulse** |
|---|---|---|---|
| 수학적 엄격성 | ✅ | ❌ | ✅ (WST Lipschitz 보장) |
| 에이전트 오케스트레이션 | ❌ | ✅ | ✅ (Claude Code 패턴) |
| 오픈소스·재현성 | 부분적 | 낮음 | ✅ (PyPI) |
| 공급업체 독립성 | - | 낮음 | ✅ (MCP 개방 표준) |
| 도메인 전이성 | 단일 도메인 | 범용이나 얕음 | ✅ (뇌 → 은하 동일 수학) |

핵심 주장: **"산업은 엔지니어링(에이전트, React, MCP)을 가지고 있고, 과학은 도메인 지식(웨이블릿, 신경과학, 천체물리학)을 가지고 있다. 둘 모두를 유창하게 지원하도록 구축된 것은 거의 없다."**

---

## 7. 관련 레퍼런스

- **Kymatio**: Andreux, Angles, Exarchakis, Leonarduzzi, **Mallat, Bruna** 외 (JMLR 2020, BSD 라이선스) — https://www.kymat.io
- **WST 원천 이론**: Mallat "Group Invariant Scattering" (2012)
- **Kymatio 논문**: arXiv:1812.11214, JMLR v21 19-047
- **Meta TRIBE v2**: Meta AI, 멀티모달 → 뇌 활동 예측 파운데이션 모델
- **TF-C**: 시간-주파수 일관성 자기지도 학습 (Zhang et al.)
- **FRB AI 벤치마크**: Breakthrough Listen / Oxford / NVIDIA 2024 발표
- **PyPI 확인**: https://www.piwheels.org/project/omnipulse

---

## 8. 평가 요약

OmniPulse는 **"한 명의 연구자가 Claude Code급 소프트웨어 아키텍처를 가져와 수십 년 된 수학 이론(WST)과 최신 파운데이션 모델(TRIBE v2, TF-C)을 결합한 과학 연구 자동화 플랫폼"** 입니다.

독창성은 기술 요소 각각이 아니라 **결합 방식**에 있으며, 특히 **MCP 기반 이중 언어 디커플링 + 도메인 불변 수학 엔진** 조합은 현재 과학-산업 경계에서 보기 드문 설계입니다.

**검증 포인트**:
1. PyPI 패키지 실제 코드 품질
2. FRB/EEG 벤치마크 재현성
3. TRIBE v2 통합의 실제 동작 여부
