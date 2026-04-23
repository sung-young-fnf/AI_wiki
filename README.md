# article_wiki

사내 **AI/개발 주제 스크랩 Wiki**. 681개의 외부 기사(Medium·블로그·논문 등)를 카테고리별로 분류·요약·색인해 사내 구성원이 AI 도구/모델/기법 관련 정보를 빠르게 탐색할 수 있게 만든 지식 베이스입니다.

## 바로 시작하기

- **어디부터 봐야 하나?** → [`wiki_index.md`](./wiki_index.md) (9개 대분류 라우팅 테이블)
- **전체 기사 목록이 필요하다** → [`summary_table.md`](./summary_table.md) (681행, 각 행 = 파일명·요약·키워드·카테고리)
- **특정 기사의 원본 본문** → `{카테고리}/books/*.md` (평면) 또는 `{카테고리}/{하위}/books/*.md` (비대 카테고리)

## 구조

```
article_wiki/
├── wiki_index.md              ← 최상위 라우터 (여기서 시작)
├── summary_table.md           ← 681행 종합 표 (fulltext 검색 진입점)
├── _category_spec.md          ← 카테고리 정의·분류 기준
│
├── (비대 4개 — 하위 분류 보유)
│   AI_Coding_Tools/  LLM_Models/  AI_Engineering/  Dev_General/
│   ├── {대분류} Index.md      ← 하위 카테고리 라우팅 허브
│   └── {하위}/
│       ├── {하위} Index.md    ← 파일 목록 + 요약 + 키워드
│       └── books/*.md         ← 원본 기사
│
├── (평면 5개 — 하위 없음)
│   Knowledge_Systems/  Local_AI_Infra/  Productivity_PKM/
│   Industry_Trends/    Career_SelfDev/
│   ├── {대분류} Index.md      ← 파일 목록 + 요약 + 키워드
│   └── books/*.md
│
└── _pending/books/            ← 손상 파일·재수집 대기
```

## 카테고리 9개 (총 679 + _pending 2)

| 대분류 | 파일 | 구조 | 담는 것 |
|---|---|---|---|
| [AI_Coding_Tools](./AI_Coding_Tools/) | 144 | 4 하위 | Claude Code, OpenCode, MCP 서버, OpenClaw·Hermes 등 CLI 에이전트 플랫폼 |
| [LLM_Models](./LLM_Models/) | 60 | 6 하위 | Claude/Gemini/Gemma/GPT/Qwen·Kimi·DeepSeek 출시·벤치, Vision 모델, 아키텍처 이론 |
| [AI_Engineering](./AI_Engineering/) | 103 | 5 하위 | 프롬프트·컨텍스트 엔지니어링, 하네스 패턴, 바이브 코딩/SDD, AI 기초, 제품 관리 |
| [Knowledge_Systems](./Knowledge_Systems/) | 67 | 평면 | RAG, Graph RAG, PageIndex, 지식 그래프, 온톨로지, 시맨틱 레이어 |
| [Local_AI_Infra](./Local_AI_Infra/) | 46 | 평면 | Ollama·LM Studio·vLLM, Apple Silicon·GPU, 양자화, 로컬 LLM 실행 |
| [Productivity_PKM](./Productivity_PKM/) | 48 | 평면 | Obsidian·Notion·제2의 뇌, Karpathy LLM Wiki, Cotypist |
| [Dev_General](./Dev_General/) | 126 | 5 하위 | Python 라이브러리, SQL/Polars/DuckDB, React/HTML5, Docker/k8s, Rust/R |
| [Industry_Trends](./Industry_Trends/) | 35 | 평면 | 기업 뉴스·M&A·해고, AI 거품, NemoClaw, 보조금 시대 |
| [Career_SelfDev](./Career_SelfDev/) | 50 | 평면 | 자기계발, 책 추천, 투자, 부업, 새벽 4시 기상, 30일 실행 시스템 |

## 검색하는 방법

### 1) Index부터 탐색 (사람용)
1. [`wiki_index.md`](./wiki_index.md) 열기
2. 본인 질문 키워드를 라우팅 테이블에서 찾고 대분류 Index로 이동
3. 비대 카테고리(AI_Coding_Tools 등)는 하위 Index로 한 번 더 이동
4. 파일 목록 표에서 요약·키워드 보고 원본 기사로 점프

### 2) 키워드 fulltext 검색 (LLM/도구용)
- [`summary_table.md`](./summary_table.md) 한 파일이 681행의 단일 인덱스
- 키워드 grep → 해당 파일명·카테고리·요약 즉시 확인
- MCP Filesystem 서버가 이 파일을 읽고 검색 응답에 활용

### 3) 공용 키워드 — 카테고리 분기

같은 키워드라도 맥락에 따라 다른 카테고리로 분기됩니다.

| 공용 키워드 | 분기 |
|---|---|
| `Claude Code` | 사용법 → AI_Coding_Tools · 시장 영향 → Industry_Trends |
| `Gemma 4` | 모델 분석 → LLM_Models · 로컬 실행 → Local_AI_Infra |
| `RAG` | 원리·구축 → Knowledge_Systems · 도구로 만들기 → AI_Coding_Tools |
| `MCP` | 서버 리뷰 → AI_Coding_Tools/MCP_Servers · 설계 논쟁 → AI_Engineering |
| `Obsidian` | PKM → Productivity_PKM · CLI 개발 → Dev_General |

## 기여 방법

### 새 기사 추가
1. 원본 `.md` 파일을 `{카테고리}/books/` (평면) 또는 `{카테고리}/{하위}/books/` (비대)에 배치
2. [`_category_spec.md`](./_category_spec.md) 기준으로 가장 맞는 카테고리 선택
3. 해당 `{카테고리} Index.md` 파일 목록 테이블에 **한 줄 추가**:
   ```markdown
   | [[파일명]] | 2~3문장 요약 | 키워드1, 키워드2, ... |
   ```
4. `summary_table.md` 에도 동일한 줄을 append
5. 메타블록의 `최종수정`과 `변경 이력` 갱신

### 어느 카테고리에도 안 맞으면
- 일단 `_pending/books/` 로 이동
- `summary_table.md` 에 `카테고리: ?`, `플래그: NEW`, 요약 뒤에 `(NEW 제안: XXX)` 메모
- 같은 `NEW 제안` 주제가 5개 이상 쌓이면 신규 카테고리 승격 검토

### 키워드 품질
- **일반어 금지**: `AI`, `LLM`, `개발`, `방법`, `가이드` 단독 사용 X
- **고유명사·수치·제품명**: `Claude Opus 4.7`, `96GB 블랙웰`, `PageIndex`, `Fixed Entity Architecture` 등
- **한/영 동의어 쌍**: `프롬프트 캐싱, prompt caching`
- **판별 테스트**: "이 단어로 검색했을 때 왜 이 문서가 떠야 하는가" 한 문장으로 설명 가능해야 채택
- 5~10개 중 **최소 3개는 이 문서 아니면 안 나올 법한 고유 용어**

자세한 가이드: [`CLAUDE.md`](./CLAUDE.md)


## 메타블록 규약

모든 `Index.md` 상단에 다음 필드가 있습니다:

| 필드 | 값 |
|---|---|
| 도메인 | `AI스크랩` |
| 플랫폼 | `article_wiki` |
| 유형 | `인덱스` / `인덱스 (대분류 허브)` / `인덱스 (중분류)` |
| 상태 | `초안` / `검토중` / `확정` |
| 소유자 | `@윤형도` |
| 최종수정 | `YYYY-MM-DD` |
| 문서ID | `IX-AW-NN` (대분류) / `IX-AW-NN-MM` (중분류) |
| 키워드 | 검색용 고유어 10~20개 |
| 관련문서 | `[[wiki_index]]`, `[[상위 Index]]`, `[[관련 하위 Index]]` |

## 참고 문서

- [`wiki_index.md`](./wiki_index.md) — 최상위 라우터
- [`_category_spec.md`](./_category_spec.md) — 카테고리 분류 기준 (Phase 2 세션용)
- [`CLAUDE.md`](./CLAUDE.md) — 병렬 세션 작업 가이드 (기여자 + AI용)
- [`AGENTS.md`](./AGENTS.md) — 에이전트 가이드 (OpenCode/Codex 등 호환)
- [`summary_table.md`](./summary_table.md) — 681행 종합 표

## 소유자 · 문의

- **소유자**: @윤형도 
- **카테고리 분류 오류·추가 제안**: Issues 또는 직접 연락
