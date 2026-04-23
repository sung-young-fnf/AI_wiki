# 번역 기사

- **원본 URL:** https://medium.com/tech-and-ai-guild/how-claude-code-and-obsidian-broke-personal-knowledge-management-d00dc8ae88d3
- **번역 일시:** 2026-04-18 19:42:39

---

## Claude Code와 Obsidian, 개인 지식 관리 시스템의 한계를 돌파하다: 손쉬운 제2의 뇌 구축

우리는 노트 필기를 저장 문제로만 생각합니다.

하지만 현실은 다릅니다. 인간의 유지보수 능력으로는 확장이 불가능하기 때문에 여러분의 개인 지식 기반(personal knowledge base)은 서서히 죽어가고 있습니다.

노션(Notion), 룸 리서치(Roam Research) 또는 옵시디언(Obsidian)으로 "제2의 뇌(Second Brain)"를 구축하려 시도해 본 적이 있다면, 그 끝이 어떻게 되는지 정확히 아실 겁니다.

처음에는 놀라운 규율로 시작합니다.

기사를 꼼꼼히 태그하고, 책 노트를 상호 참조하며, 프로젝트 파일을 정리합니다. 하지만 6개월 이내에 유지보수 부담이 여러분의 가용 컴퓨팅 자원을 초과하게 됩니다.

페이지 연결을 중단하고, 폴더 구조는 디지털 잡동사니 서랍으로 전락합니다. 결국 시스템을 포기하고 1년을 기다렸다가 새로운 앱으로 이 과정을 다시 시작합니다.

근본적인 문제는 우리가 여가 시간에 관계형 데이터베이스(relational database)를 수동으로 유지하려 한다는 점입니다.

어제, 저는 안드레이 카파시(Andrej Karpathy)가 처음 개념화하고 최근 개발자 커뮤니티에서 인기를 얻은 프레임워크를 접했습니다.

로컬 마크다운 인터페이스인 옵시디언(Obsidian)과 자율 터미널 에이전트(autonomous terminal agent)인 Claude Code를 연결함으로써 유지보수 병목 현상을 완전히 제거할 수 있습니다.

저는 제 개인 인프라에 이 시스템을 연결했습니다.

예를 들어, BoutPredict 아키텍처 노트, 무작위 킨들(Kindle) 하이라이트, 반쯤 완성된 스타트업 아이디어 등 모든 것을 이곳에 쏟아부었습니다.

이것은 정말 놀라운 경험입니다!

아침에 단 한 글자도 입력하기 전에, 이미 AI가 제 미결 작업을 색인화하고, 새로운 연구 내용을 상호 참조하며, 노트의 모순점을 표시해 줍니다.

이에 대해 더 자세히 논의해 봅시다.

현재 대부분의 사람들은 거대 언어 모델(LLMs)을 상태 비저장 함수(stateless functions)처럼 사용합니다.

PDF를 ChatGPT에 업로드하면, 검색 증강 생성(RAG, Retrieval-Augmented Generation) 검색을 실행하고, 관련 텍스트 덩어리를 찾아 요약을 내뱉습니다.

이는 일회성 질문에는 작동하지만, 지식의 축적은 전혀 없습니다.

일주일 후 복잡한 질문을 다시 던지면, LLM은 처음부터 지식을 재발견하고 다시 조립해야 합니다. 아무것도 축적되지 않습니다.

Claude + Obsidian 아키텍처는 근본적으로 다릅니다.

질의 시점에 원본 문서에서 검색하는 대신, Claude는 점진적으로 영구적인 위키(persistent wiki)를 구축하고 유지보수하는 백그라운드 작업자(background worker)로 작동합니다.

새로운 기사를 볼트(vault)에 넣으면 Claude는 단순히 색인만 하는 것이 아닙니다.

기사를 읽고, 변수를 추출하며, 요약 페이지를 생성하고, 주 색인을 업데이트하며, 새로운 데이터를 반영하도록 기존 개념 페이지를 능동적으로 수정합니다.

지식은 한 번 컴파일되어 최신 상태로 유지됩니다.

### 3계층 아키텍처

이 시스템을 구축하려면 로컬 옵시디언 볼트(데이터베이스)와 데스크톱에 설치된 Claude Code 명령줄 인터페이스(CLI, Command Line Interface) 도구가 필요합니다.

심지어 옵시디언을 열 필요도 없습니다. 옵시디언은 코드를 보는 데 사용하는 통합 개발 환경(IDE, Integrated Development Environment)일 뿐입니다.

Claude는 프로그래머입니다.

이 시스템은 세 가지 고유한 계층에 의존합니다.

*   **계층 1: 원천 데이터(Raw Sources, 불변 데이터).** 이곳에 원본 입력물을 덤프합니다. PDF, 웹 클리핑 기사, 팟캐스트 녹취록, 회의록 등입니다. 이 파일들은 절대 편집하지 않습니다. 이들은 여러분의 진실의 원천(source of truth)입니다.
*   **계층 2: 위키(The Wiki, 컴파일된 출력).** 이는 엔티티 페이지, 개념, 색인 등 Claude가 전적으로 소유하는 마크다운(markdown) 파일 디렉터리입니다. 여러분은 읽기만 하고, LLM이 씁니다.
*   **계층 3: 스키마(The Schema).** 이것은 루트 디렉터리에 있는 단일 설정 파일(예: CLAUDE.md)입니다. 이 파일은 에이전트에게 여러분의 지식 체계가 어떻게 구성되어 있는지 정확히 알려줍니다. 데이터를 수집하고, 변경 사항을 기록하며, 페이지 형식을 지정하는 규칙을 정의합니다.

만약 처음부터 시작한다면, Claude와 대화를 시작하여 20분 동안 자신이 누구인지, 무엇을 구축하고 있는지, 그리고 현재의 병목 현상이 무엇인지 이야기하세요.

그 대화 기록을 볼트 안에 `Memory.md`로 저장하세요. 이제 시스템은 기본 컨텍스트를 가지게 됩니다.

이제 아키텍처가 설정되면, 실제 일상적인 관리는 전적으로 터미널(terminal)에서 이루어집니다.

시스템이 스스로 유지보수되도록 특정 운영 루프(operational loops)를 실행합니다.

예를 들어, 새로운 연구 자료를 찾거나 고객 통화를 마치면, 원본 텍스트를 소스 폴더에 넣고 다음 명령을 실행합니다.

```bash
claude -p "I just added a new file to /raw-sources. Read it, extract the key ideas, write a summary page to /wiki/summaries/, update index.md with a link, and update any existing concept pages that this new data connects to. Show me every file you touched." --allowedTools Bash,Write,Read
```

저는 가만히 앉아 있고, 터미널이 작동하기 시작합니다.

10초 만에 Claude는 15개의 다른 마크다운 파일을 건드립니다.

새로운 개념을 연결하고, 모순을 표시하며, 업데이트를 기록합니다. 장부 정리 비용은 0입니다.

그리고 일주일에 한 번, 지식 기반이 낡는 것을 방지하기 위해 상태 점검(health check)을 실행해야 합니다.

다음은 린팅(linting) 명령입니다.

```bash
claude -p "Read every file in /wiki/. Find: contradictions between pages, orphan pages with no inbound links, concepts mentioned repeatedly but with no dedicated page, and claims that seem outdated based on newer files. Write a health report to /wiki/lint-report.md with specific fixes." --allowedTools Bash,Write,Read
```

유지보수는 더 이상 여러분의 일이 아닙니다.

시스템이 어떤 개념이 잘못되었거나 추가 연구가 필요한지 알려줍니다.

사실, 모든 것을 자동화할 수도 있습니다.

Claude Code가 CLI를 통해 실행되므로, 운영체제 크론 작업(OS chron jobs)에 연결할 수 있습니다.

매일 아침 7시에 스크립트가 실행되도록 설정하여, `Memory.md`를 읽고 지난 24시간 동안 추가된 파일을 확인하며, 커피를 마시기도 전에 깔끔하게 합성된 일일 브리핑을 터미널에 직접 출력할 수 있습니다.

1945년, 배니버 부시(Vannevar Bush)는 "멤엑스(Memex)"를 묘사하는 에세이를 썼습니다. 이는 한 개인의 모든 책, 기록, 통신을 연상 경로(associative trails)로 기계적으로 연결하여 저장하는 이론적인 개인용 장치였습니다.

80년 동안 우리는 저장 용량을 가지고 있었지만, 연상 경로를 유지할 엔진이 부족했습니다. 인간은 자신의 전체 삶을 수동으로 상호 참조할 규율이 단순히 부족했습니다.

인터페이스(옵시디언)와 유지보수 작업자(Claude Code)를 분리함으로써, 우리는 마침내 장부 정리 문제를 해결했습니다. 여러분의 임무는 고품질 입력을 선별하고, 날카로운 질문을 던지며, 실행하는 것입니다. 파일링은 기계에게 맡기세요.