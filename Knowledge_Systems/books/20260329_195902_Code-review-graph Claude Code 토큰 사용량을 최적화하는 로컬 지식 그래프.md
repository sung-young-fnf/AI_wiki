# 번역 기사

- **원본 URL:** https://share.google/5pZqr6906IALo96yO
- **번역 일시:** 2026-03-29 19:59:02

---

# Code-review-graph: Claude Code 토큰 사용량을 최적화하는 로컬 지식 그래프

메인 콘텐츠 건너뛰기

회원가입
로그인

모든 게시글
소개
더 보기
바로가기
홈
시작하기
블로그
허브
튜토리얼
커뮤니티
게시판 목록
공지사항
읽을거리 & 정보 공유
자유 게시판
질문 & 답변
자주 묻는 질문 (FAQ)
행사 & 이벤트 홍보
모든 게시판
태그
geeknews
AI
파이토치 (PyTorch)
paper
LLM
모든 태그

읽을거리 & 정보 공유
claude-code
knowledge-graph
knowledge-base
code-graph
token-optimization
code-review-graph
3월 23일
1 / 1
3월 23일
6일 전
9bow 님 작성, 3월 23일
9bow
박정환
6일 전

1303×437 30.6 KB

## code-review-graph 소개

최근 AI 기반 코딩 어시스턴트인 Claude Code를 활용해 개발 생산성을 높이는 사례가 급증하고 있습니다. 하지만 Claude Code에 특정 커밋(commit)을 리뷰하거나 새로운 기능을 추가해 달라고 요청할 때, 모델이 전체 코드베이스(codebase)의 문맥(context)을 파악하기 위해 수많은 파일을 읽어 들이는 비효율성이 발생합니다. 소규모 프로젝트에서는 이러한 방식이 큰 문제가 되지 않지만, FastAPI(약 2,915개 파일)나 Next.js(약 27,732개 파일)와 같은 대규모 모노레포(monorepo) 환경에서는 상황이 전혀 다릅니다. 변경 사항과 전혀 관련 없는 수천 개의 파일까지 스캔(scan)하게 되면서 불필요한 토큰(token) 낭비가 기하급수적으로 발생합니다. 이는 개발자의 API 비용 부담을 가중시킬 뿐만 아니라, 프롬프트(prompt) 내에 노이즈(noise)를 증가시켜 오히려 코드 리뷰(code review)의 품질을 떨어뜨리는 주요 원인이 됩니다.

3150×2599 421 KB

이러한 문제를 근본적으로 해결하기 위해 등장한 오픈소스(open-source) 도구가 바로 **code-review-graph**입니다. code-review-graph 프로젝트는 Tree-sitter를 사용하여 사용자의 코드베이스를 분석하고, 함수, 클래스, 임포트(import), 호출(call) 및 상속(inheritance) 관계를 모두 포함하는 영구적인 구조적 지도(Knowledge Graph)를 구축합니다.

이렇게 추출된 모든 노드(node)와 엣지(edge) 데이터는 외부 서버나 클라우드(cloud)로 전송되지 않고 개발자 로컬(local) 환경의 SQLite 데이터베이스(database)에 안전하게 저장됩니다. 이후 개발자가 파일을 수정하거나 Git 커밋(commit)을 수행하면, 시스템이 변경된 파일과 그에 의존하는 파일들만 추적하여 2초 이내에 그래프(graph)를 다시 파싱(parsing)합니다. 결과적으로 Claude는 전체 코드를 맹목적으로 읽는 대신 이 로컬 지식 그래프를 쿼리(query)하여 무엇이 변경되었고 어떤 파일이 영향을 받는지 정확하고 빠르게 파악할 수 있게 됩니다.

3690×1567 414 KB

code-review-graph를 도입함으로써 얻을 수 있는 이점은 매우 강력하며 실제 프로덕션(production) 환경의 벤치마크(benchmark) 수치로도 증명되었습니다. 실제 프로젝트 저장소(repository)를 기준으로 코드 리뷰(code review) 시 평균 6.8배의 토큰(token)을 절약할 수 있으며, 대규모 모노레포(monorepo)에서의 실시간 코딩 작업에서는 최대 49배까지 토큰 사용량을 획기적으로 줄일 수 있습니다. 또한, 관련 없는 코드가 컨텍스트(context)에서 배제되면서 AI가 당면한 문제에만 온전히 집중할 수 있게 되어 코드 리뷰 품질 점수가 평균 7.2점에서 8.8점으로 향상되는 극적인 효과를 보였습니다.

클라우드(cloud) 연동, 원격 분석(Telemetry), 회원 가입 등의 번거로운 절차가 일절 없으며, 프로젝트 내부의 `.code-review-graph/` 디렉터리(directory)에 생성되는 단일 SQLite 파일만으로 모든 기능이 완벽하게 동작하므로 프라이버시(privacy)와 보안을 중시하는 기업 환경에도 매우 적합합니다.

## code-review-graph의 주요 기능

4800×1003 274 KB

code-review-graph는 개발자의 작업 흐름을 방해하지 않으면서도 강력한 성능을 발휘하도록 다양한 최신 기술 스택(tech stack)과 아키텍처(architecture)를 채택하고 있습니다. 프로젝트의 주요 아키텍처와 기술적 세부 사항은 다음과 같습니다.

### Tree-sitter 기반 다국어 AST 파싱

code-review-graph의 핵심은 코드의 구문 분석(AST, Abstract Syntax Tree)을 빠르고 정확하게 수행하는 Tree-sitter에 있습니다. Tree-sitter는 현재 Python, TypeScript, JavaScript, Go, Rust, Java, C#, Ruby, Kotlin, Swift, PHP, C/C++ 등 총 12개의 주요 프로그래밍 언어를 공식적으로 지원합니다.

각 언어별 파서(parser)를 통해 코드 내의 모든 식별자(identifier, 함수명, 클래스명 등)는 충돌을 방지하기 위해 정규화된 이름(Qualified names, 예: `src/auth.py::AuthService.login`)으로 노드(node)화되어 그래프(graph)에 저장됩니다.

### Blast Radius (영향 범위) 분석

특정 파일이나 함수가 변경되었을 때, 이 도구는 해당 변경이 프로젝트 전체에 미치는 파급 효과인 '영향 범위(Blast Radius)'를 계산합니다. NetworkX 라이브러리(library)를 활용하여 넓이 우선 탐색(BFS, Breadth-First Search) 방식으로 방향성 그래프(directed graph)를 순회하며, 변경된 함수를 호출하는 모든 호출자(Caller), 의존성 파일(Dependent), 그리고 관련 테스트 코드를 즉각적으로 식별합니다.

또한, 리소스(resource) 고갈을 방지하기 위해 BFS 탐색은 최대 500개 노드(node)로 제한되도록 설계되었습니다. 이 정보를 통해 Claude는 전체 파일이 아닌, 정확히 영향을 받는 파일 집합만을 선별하여 읽을 수 있습니다.

### SHA-256 기반 초고속 증분 업데이트 (Incremental Engine)

초기 그래프 빌드(build) 이후에는 전체 저장소(repository)를 다시 스캔(scan)하지 않는 스마트한 증분 업데이트(Incremental Update) 엔진(engine)이 작동합니다. PostEdit 및 PostGit 훅(hook)을 통해 파일 저장이나 Git 커밋(commit) 이벤트가 발생하면 백그라운드(background)에서 즉시 업데이트가 트리거(trigger)됩니다.

이때 SHA-256 해시(hash) 비교를 통해 내용이 실제로 수정되지 않은 파일은 파싱(parsing)을 완전히 건너뛰며(Hash Skip), 변경된 파일과 그 종속 파일만 다시 파싱합니다. 약 2,900개의 파일이 있는 프로젝트에서도 이러한 증분 업데이트는 2초 이내에 완료되므로 개발자의 워크플로우(workflow)를 전혀 지연시키지 않습니다.

### 로컬 우선주의와 SQLite 데이터베이스 최적화

모든 관계 데이터는 프로젝트 최상단 디렉터리(directory)가 아닌 `.code-review-graph/` 디렉터리 내부의 단일 SQLite 파일에 저장되어 관리의 편의성을 높이고 `.gitignore` 설정 충돌을 방지합니다. 다수의 동시 읽기 작업을 원활하게 처리하기 위해 SQLite의 WAL(Write-Ahead Logging) 모드(mode)를 사용하며 별도의 외부 데이터베이스(database)가 필요하지 않습니다.

옵션으로 제공되는 벡터 임베딩(Vector Embeddings) 기능 역시 별도의 벡터 데이터베이스(Vector Database)를 띄울 필요 없이 동일한 SQLite 파일 내에 바이너리 블롭(Binary Blobs) 형태로 안전하게 저장됩니다.

## code-review-graph 설치 및 사용 방법

code-review-graph는 Python 3.10 이상 및 uv 패키지 관리자(package manager)가 설치된 환경에서 동작합니다. 터미널(terminal)에서 다음 명령어를 통해 플러그인(plugin) 또는 패키지(package) 형태로 손쉽게 설치할 수 있습니다.

### Claude Code 플러그인으로 설치 (권장)

가장 간편한 방법으로, Claude Code 내부의 마켓플레이스(marketplace)를 통해 직접 설치하고 구성할 수 있습니다:

```bash
claude plugin marketplace add tirth8205/code-review-graph
claude plugin install code-review-graph@code-review-graph
```

### Pip를 이용한 글로벌/가상환경 설치

또는, Python의 기본 패키지 관리자를 사용하여 설치할 수도 있습니다:

```bash
pip install code-review-graph
code-review-graph install
```

설치가 완료된 후 Claude Code를 재시작하고, 프로젝트 디렉터리(directory)에서 Claude에게 `/build-graph` 또는 아래와 같이 프롬프트(prompt)를 입력하면 초기 지식 그래프(Knowledge Graph) 구축이 시작됩니다. 500개 파일 기준 초기 빌드(build)에는 약 10초가 소요되며 이후로는 자동 업데이트(auto update)됩니다.

```
"Build the code review graph for this project."
```

## 라이선스

code-review-graph 프로젝트는 오픈소스 커뮤니티(open-source community)의 발전을 위해 상업적 이용 및 수정이 자유로운 MIT License로 공개 및 배포되고 있습니다.

---

[Code-review-graph 관련 HackerNews 게시글](https://news.ycombinator.com/item?id=39649989)
`news.ycombinator.com`
Code-review-graph: persistent code graph that cuts Claude Code token usage
12점 — 댓글 2개 — tirthkanani — 2026년 3월 9일 오후 7시 22분

[Code-review-graph 프로젝트 GitHub 저장소](https://github.com/tirth8205/code-review-graph)
`github.com`
GitHub - tirth8205/code-review-graph: Local knowledge graph for Claude Code. Builds a persistent map of your codebase so Claude reads only what matters — 코드 리뷰 시 토큰 6.8배 절약, 일상 코딩 작업 시 최대 49배 절약.

더 읽어보기

*   [Claw Compactor: AI 에이전트 토큰 비용을 최대 97% 절감하는 오픈소스 토큰 압축 엔진](link-to-article)
*   [Graphiti: 시간의 흐름에 따라 변화하는 지식을 다루기 위한 지식 그래프 생성 도구](link-to-article)
*   [Memori: LLM 및 AI 에이전트를 위한 SQL 네이티브 메모리 계층](link-to-article)
*   [Parlant: LLM 에이전트 행동 제어 및 신뢰성 보장을 위한 오픈소스 행동 제어 프레임워크 (Behavior Guidance Framework)](link-to-article)
*   [OpenAI Codex의 핵심, 에이전트 루프(Agent Loop) 및 아키텍처 심층 분석](link-to-article)

---

이 글은 GPT 모델을 기반으로 정리된 내용으로, 원문의 내용 또는 의도와 다르게 정리되었을 수 있습니다. 관심 있는 내용이라면 원문도 함께 참고해 주시기 바랍니다. 글을 읽으시면서 어색하거나 잘못된 내용을 발견하시면 댓글로 알려주시기를 부탁드립니다.

파이토치 한국 사용자 모임이 정리한 이 글이 유용하셨나요? 회원으로 가입하시면 주요 게시글들을 이메일로 보내드립니다! (기본은 주간(Weekly) 발송이지만, 일간(Daily)으로 변경도 가능합니다.)

아래에 '좋아요'를 눌러주시면 새로운 소식들을 정리하고 공유하는 데 큰 힘이 됩니다.

1

---

## Understand-Anything: 코드베이스를 인터랙티브 지식 그래프로 변환하는 Claude Code 플러그인
3.3천 조회수
9 링크
읽기 5분

딥러닝(Deep Learning) 또는 파이토치(PyTorch)에 관심이 있으신가요? 회원 가입하시면 이메일로 새로운 소식을 보내드립니다!

파이토치 한국 사용자 모임에 오신 것을 환영합니다! 혹시 파이토치(PyTorch)나 인공지능(AI), 딥러닝(Deep Learning)에 관심이 있으신가요? 저희 PyTorch.KR은 파이토치와 딥러닝을 배우고 함께 성장하는 것을 목표로 하고 있습니다. 지금 커뮤니티에 가입하고 관심 있는 게시물에 '좋아요'를 눌러보는 건 어떠세요? 에러(error)를 만나셨나요? 궁금한 것이 있으시다면 언제든 질문 & 답변 게시판에 질문을 올려주세요. 더불어, 회원 가입 시 알려주신 이메일 주소로 새로운 게시물이 올라오면 알려드립니다. 파이토치 한국 사용자 모임과 함께해 보시는 건 어떠세요?

(GitHub 계정 등으로) 바로 가입하기
나중에 알려주실래요?
조금 더 둘러볼게요!

## 새 글 또는 읽지 않은 글
토픽 목록, 버튼이 있는 열 머리글은 정렬 가능합니다.

| 게시글                                                                        | 댓글 | 조회수 | 활동    |
| :---------------------------------------------------------------------------- | :--- | :----- | :------ |
| 왜 데이터 청킹(Data Chunking)이 LLM 처리에서 필수적일까 <br/> 읽을거리 & 정보 공유 | 2    | 375    | 6일 전  |
| OpenCLI: 주요 웹사이트와 데스크톱 앱을 CLI 명령어로 제어하는 AI 네이티브 유니버설 허브 프로젝트 <br/> opensource, ai-agent, typescript, cli, nodejs, web-scraping, opencli, browser-automation | 0    | 132    | 3일 전  |
| clsh: 자신의 macOS 터미널 환경을 모바일에서 접근할 수 있도록 만들어주는 오픈소스 도구 <br/> claude-code, coding-assistant, macos, remote-control, mobile-access, tmux, clsh | 0    | 234    | 12일 전 |
| LLM 아키텍처 갤러리 (LLM Architecture Gallery): Sebastian Raschka가 공개한, GPT-2부터 최신 오픈웨이트 모델까지의 주요 LLM 구조 시각화 <br/> visualization, llm-architecture, sebastian-raschka, llm-architecture-gallery | 0    | 3.2천  | 12일 전 |
| Anthropic, 프런티어 AI 시대의 사회적 과제 해결을 위한 Anthropic Institute 설립 발표 <br/> anthropic, red-teaming, powerful-ai, machines-of-loving-grace, anthropic-institute | 0    | 183    | 12일 전 |
| LLM의 초전도체 연구 질문 응답 능력 평가: 선별된 데이터 소스의 중요성 (feat. Google Research, Cornell, Harvard) <br/> paper, google, benchmark, notebooklm, google-research, harvard, cornell-university, ai-for-science-research | 0    | 92     | 3일 전  |
| Claw Compactor: AI 에이전트 토큰 비용을 최대 97% 절감하는 오픈소스 토큰 압축 엔진 <br/> prompt-compression, open-compress, token-compaction, claw-compactor | 0    | 382    | 11일 전 |
| cmux: AI 코딩 에이전트를 위해 설계된, Ghostty 기반의 네이티브 macOS 터미널 앱 <br/> opensource, claude-code, macos, notification, ghostty, terminal, cmux, ai-coding-agent | 0    | 202    | 4일 전  |
| Mistral Vibe: Mistral이 공개한, 터미널 네이티브 오픈소스 AI 코딩 비서 <br/> vibe-coding, coding-assistant, mistral, mistral-vibe | 0    | 147    | 2일 전  |
| GSD(Get Shit Done): AI 코딩의 ‘컨텍스트 오염(Context Rot)’을 막아주는 강력한 스펙 주도 개발 시스템 <br/> agent-harness, harness-engineering, context-rot, get-shit-done, gsd | 0    | 305    | 10일 전 |
| Quotio: macOS를 위한 올인원 AI 코딩 어시스턴트 할당량 관리 및 자동 대체 동작 기능(Auto Fail-over) 도구 <br/> vibe-coding, coding-assistant, llm-tool-usage, llm-usage, quotio | 0    | 183    | 5일 전  |
| [GN] AI 보조 코딩이 당신의 업무에 어떤 영향을 주고 있나요? (via HackerNews) <br/> geeknews, code-agent, code-assistant, hackernews | 0    | 256    | 10일 전 |

더 읽을거리가 필요하신가요? **읽을거리 & 정보 공유**에서 다른 글을 찾아보거나 최근 글을 살펴보세요.
Powered by Discourse

---

### 파이토치 한국어 커뮤니티 🇰🇷
🔥 파이토치 한국 사용자 모임 🇰🇷 에 오신 것을 환영합니다! 파이토치 및 인공지능 관련 소식과 질문/답변, 행사, 이벤트 등의 정보를 나누며 함께 성장하는 것을 목표로 합니다. 함께해 주세요! ❤️

파이토치 한국 사용자 모임
사용자 모임 소개
기여해 주신 분들
리소스
행동 강령
게시판별 RSS
전체 게시판
공지사항
읽을거리 & 정보 공유
자유 게시판
질문 & 답변
행사 & 이벤트 홍보
홈페이지 튜토리얼 블로그