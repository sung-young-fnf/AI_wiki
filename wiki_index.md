# AI 스크랩 Wiki Index

| 필드 | 값 |
|-----|-----|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (최상위) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-000 |
| 키워드 | `AI 스크랩`, `AI 기사`, `LLM 지식베이스`, `카테고리 인덱스`, `article wiki`, `AI wiki` |
| 관련문서 | [[AI_Coding_Tools Index]], [[LLM_Models Index]], [[AI_Engineering Index]], [[Knowledge_Systems Index]], [[Local_AI_Infra Index]], [[Productivity_PKM Index]], [[Dev_General Index]], [[Industry_Trends Index]], [[Career_SelfDev Index]] |

AI/개발 주제 스크랩 681개의 최상위 라우터. 사용자 질문의 키워드로 **대분류 Index**를 찾고, 거기서 다시 **하위 Index** 또는 **개별 파일**로 이동한다.

## 카테고리 9개 (총 679개 분류 + _pending 2)

| 대분류 | 파일 수 | 구조 | 핵심 키워드 |
|---|---|---|---|
| [[AI_Coding_Tools Index]] | 144 | **4 하위** | Claude Code, OpenCode, MCP 서버, OpenClaw |
| [[LLM_Models Index]] | 60 | **6 하위** | Gemma 4, Claude Mythos, GPT-5, Qwen, Nano Banana, Karpathy |
| [[AI_Engineering Index]] | 103 | **5 하위** | 프롬프트 캐싱, 컨텍스트 엔지니어링, 하네스, SDD, AI PM |
| [[Knowledge_Systems Index]] | 67 | 평면 | RAG, Graph RAG, PageIndex, 지식 그래프, 온톨로지, FEA |
| [[Local_AI_Infra Index]] | 46 | 평면 | Ollama, LM Studio, vLLM, Mac Silicon, 96GB 블랙웰, 양자화 |
| [[Productivity_PKM Index]] | 48 | 평면 | Obsidian, Notion, 제2의 뇌, Karpathy LLM Wiki, Cotypist |
| [[Dev_General Index]] | 126 | **5 하위** | Python 라이브러리, SQL, React, Docker, Rust |
| [[Industry_Trends Index]] | 35 | 평면 | Anthropic 인수, 해고, AI 거품, NemoClaw, 보조금 시대 |
| [[Career_SelfDev Index]] | 50 | 평면 | 자기계발, 책 추천, 부업, 30일 실행 시스템, 새벽 4시 기상 |
| _pending/books/ | 2 | (재수집 필요) | 손상 파일 (Medium 사이드바만 캡처됨) |

## 하위 카테고리 (총 20개)

| 대분류 | 하위 카테고리 |
|---|---|
| **AI_Coding_Tools** | [[Claude_Code Index]] (84) · [[Other_CLIs Index]] (20) · [[MCP_Servers Index]] (12) · [[Agent_Platforms Index]] (28) |
| **LLM_Models** | [[Claude Index]] (9) · [[Google_Gemini_Gemma Index]] (16) · [[OpenAI_GPT Index]] (1) · [[Open_Source_Models Index]] (13) · [[Vision_Multimodal Index]] (9) · [[Architecture_Theory Index]] (12) |
| **AI_Engineering** | [[Prompt_Context_Engineering Index]] (14) · [[Agent_Patterns_Harness Index]] (70) · [[Vibe_Coding_SDD Index]] (9) · [[AI_Foundations Index]] (7) · [[Product_Management Index]] (3) |
| **Dev_General** | [[Python_Libraries Index]] (35) · [[Data_Engineering Index]] (45) · [[Web_Frontend Index]] (17) · [[Infrastructure_DevOps Index]] (10) · [[Dev_Languages_Tools Index]] (19) |

## 사용 흐름

```
사용자 질문 키워드
    ↓
[[wiki_index]] (이 파일)
    ↓ 대분류 매칭
[[{대분류} Index]]
    ↓ 비대 카테고리(4개)는 하위 라우팅
[[{하위} Index]]
    ↓ 파일 목록에서 선택
[[원본 .md]] (books/ 안의 실제 기사)
```

## 공용 키워드 — 카테고리 분기

다음 키워드는 여러 카테고리에서 등장. 문서 내용 맥락으로 분기 결정.

| 공용 키워드 | 분기 |
|---|---|
| `Claude Code` | 사용법·기능 → `AI_Coding_Tools/Claude_Code` · 시장 영향 → `Industry_Trends` |
| `Gemma 4` | 모델 분석 → `LLM_Models/Google_Gemini_Gemma` · 로컬 실행 → `Local_AI_Infra` |
| `RAG` | 원리·구축 → `Knowledge_Systems` · Claude Code로 만들기 → `AI_Coding_Tools/Claude_Code` |
| `MCP` | 서버 리뷰 → `AI_Coding_Tools/MCP_Servers` · 설계 논쟁/원리 → `AI_Engineering/Agent_Patterns_Harness` |
| `Obsidian` | PKM·제2의 뇌 → `Productivity_PKM` · Obsidian CLI 개발 → `Dev_General/Dev_Languages_Tools` |
| `Anthropic` | 신기능 → `AI_Coding_Tools/Claude_Code` · 기업 동향·M&A → `Industry_Trends` |
| `Python` | 일반 라이브러리 → `Dev_General/Python_Libraries` · AI 에이전트 구축 → `AI_Engineering` |
| `Karpathy` | nanoGPT/Backprop → `LLM_Models/Architecture_Theory` · LLM Wiki/Autoresearch → `Productivity_PKM` |
| `Nano Banana` | 모델·이미지 생성 → `LLM_Models/Vision_Multimodal` |
| `Bonsai` | 1-bit 모델 → `LLM_Models/Architecture_Theory` 또는 `Open_Source_Models` (맥락별) |

## 폴더 구조

```
article_wiki/
├── wiki_index.md            ← 이 파일
├── _category_spec.md        ← Phase 2 분류 기준 (Phase 1 산출물)
├── CLAUDE.md                ← Phase 2 병렬 세션 가이드
├── plan.md                  (프로젝트 루트)
├── tables/                  ← Phase 2 부분 표 10개
├── _phase3_analysis/        ← Phase 3 작업 산출물
├── _pending/books/          ← 손상 파일 2개 (재수집 필요)
│
├── (비대 4개 — 허브 + 하위)
│   AI_Coding_Tools/
│   ├── AI_Coding_Tools Index.md
│   ├── Claude_Code/
│   │   ├── Claude_Code Index.md
│   │   └── books/*.md
│   ├── Other_CLIs/...
│   ├── MCP_Servers/...
│   └── Agent_Platforms/...
│
└── (평면 5개 — Index + books)
    Knowledge_Systems/
    ├── Knowledge_Systems Index.md
    └── books/*.md
```

## 에스컬레이션

| 상황 | 대응 |
|---|---|
| 카테고리 분류 오류 발견 | @윤형도 보고 → Phase 4 재분류 |
| 새 카테고리 후보 발견 | `_pending/books/` 로 일단 이동 후 5개 모이면 신설 |
| 손상 파일 (재수집 필요) | `_pending/books/` 의 2개 — 원본 URL에서 재수집 |
| 검색 노이즈 많음 | 키워드 정규화 (Phase 5에서 동의어 merge) |

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|-----|-----|-----|------|
| v1.1 | 2026-04-23 | AI(claude-code) | **Phase 3 완료** — 20개 하위 폴더 신설, NEW 12 흡수 (Product_Management 신규), 비대 4개 카테고리 허브 전환, 분포 갱신 |
| v1.0 | 2026-04-23 | AI(claude-code) | 최초 작성 — 9개 카테고리 라우팅 |
