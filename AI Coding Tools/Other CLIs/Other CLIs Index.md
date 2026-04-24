# Other CLIs Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-01-02 |
| 키워드 | `OpenCode`, `Cursor`, `Codex`, `Mistral Vibe`, `MiniMax CLI`, `cmux`, `Aider`, `Claw Code`, `gstack`, `Perplexity`, `agent-browser`, `10가지 CLI 도구`, `Goose`, `Repomix`, `Nano Banana`, `Strix` |
| 관련문서 | [[AI Coding Tools Index]], [[ArticleScrap Platform Index]] |

## 개요

OpenCode·Cursor·Codex·Mistral Vibe·MiniMax CLI·cmux·Aider·Claw Code·gstack 등 Claude Code 대체/경쟁/협업 CLI 도구 모음. AI 코딩 도구 비교·신규 도구 리뷰 포함.

## 파일 목록 (20개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260222_203746_한 달 만에 깃허브 스타 3만 개를 획득한 이 도구 드디어 직접 사용해 보았습니다]] | SST 팀이 만든 오픈소스 터미널 AI 에이전트 OpenCode가 한 달 만에 GitHub 스타 39,800→71,900으로 급증한 사용기. 2026-01-09 Anthropic이 제3자 앱 Claude 자격 증명 차단(벤더 락인) 이후 공급자 독립성이 부각됐고, 70개 이상 공급자·Plan/Build 모드·AGENTS.md·버퍼 기반 터미널 UI가 강점. GLM-4.7(Lite $6/Pro $30/Max $60)을 Claude Code Max($100) 대안으로 제시. | OpenCode, SST, AGENTS.md, GLM-4.7, Plan mode, Build mode, 벤더 락인, Claude Code 구독 정책, 터미널 네이티브 에이전트 |
| [[20260228_222202_이 도구는 한 달 만에 GitHub에서 3만 개의 스타를 얻었습니다 드디어 사용해 보았습니다]] | 파일 20260222_203746(OpenCode 사용기)과 동일 원본(medium.com/vibe-coding)의 재번역본. SST 팀의 터미널 네이티브 AI 코딩 에이전트 OpenCode가 스타 39,800→71,900 급증, Anthropic 1/9 제3자 앱 차단 이후 공급자 독립성 부각, Plan/Build 모드·AGENTS.md·GLM-4.7(Max $60 vs Claude Max $100) 비교. 재번역 중복본. | OpenCode, SST 팀, Claude Code 벤더 락인, GLM-4.7, AGENTS.md, Plan Build 모드, 터미널 버퍼 시스템, 재번역 중복본, 65만 월간 사용자 |
| [[20260301_183724_OpenCode 최고의 Claude Code 대체재]] | 90.4k GitHub 스타의 OpenCode를 Claude Code의 오픈소스 대안으로 분석. Claude Code는 Opus 4.5 기반 CORE Bench Hard 95% 정확도로 기준점을 세웠지만 관리형 모델 통합·비용 제어 한계·에이전트 커스터마이징 제약이 존재한다고 지적. OpenCode는 npm i -g opencode-ai로 설치하며 75개 이상 모델 공급업체 지원, 640+ 기여자, 월 150만 개발자 사용, 에이전트 동작과 모델 선택 분리, GitHub Copilot 연동이 특징. | OpenCode, Claude Code, opencode-ai, CORE Bench Hard, Opus 4.5, 75개 모델 공급업체, GitHub Copilot 연동, CLI 에이전트, 오픈소스 대안 |
| [[20260315_200647_Claude Code를 일일이 감독하지 마세요 Code Container로 10배 빠르게 작업하세요]] | kevinMEH가 공개한 Code Container(github.com/kevinMEH/code-container) — Docker로 프로젝트별 `code-{name}-{hash}` 컨테이너를 생성해 Claude Code/Codex/OpenCode에 루트 권한을 부여하고 `~/.codex`·`~/.config/opencode`를 마운트, SSH/Git은 호스트에서 읽기 전용 마운트. 신규 컨테이너 1초, 재개 즉시, 종료 시 자동 중지. GLM 4.7 + OpenCode 조합으로 병렬 작업을 전환 없이 진행, "진행하시겠습니까?" 프롬프트 지옥 탈출. | Code Container kevinMEH, Docker 프로젝트별 격리, Claude Code 루트 권한 샌드박스, OpenCode Codex GLM 4.7, `container` 명령 1초 시작, `~/.codex` 마운트 공유 캐시, SSH Git 읽기 전용, 병렬 컨테이너 여러 개 |
| [[20260315_202628_챗GPT와 제미니는 잊으세요 당신을 깜짝 놀라게 할 새로운 AI 도구들]] | Nitin이 추천하는 신규 AI 도구 소개(Nano Banana 이미지 편집으로 Canva 대체, Strix 오픈소스 침투 테스트 AI 에이전트 등)로 이미지 생성·보안·웹 제작을 아우르는 범용 AI 도구 리뷰 (NEW 제안: AI_Tool_Showcase). | Nano Banana, Strix, AI 도구 리뷰, 오픈소스 펜테스팅, 이미지 편집 AI, Nitin, 신규 AI 도구 |
| [[20260315_202658_첫 번째 Claude Code 에이전트 스킬 구축하기 수많은 시간을 절약해 주는 간단한 프로젝트 메모리 시스템]] | Rick Hightower가 300줄 미만 Claude Code 'project-memory' 스킬로 세션 간 AI 기억 상실을 해결한 실전 튜토리얼. CLAUDE.md와 마크다운 결합으로 결정·버그·포트 번호 같은 지식 축적, Codex·GitHub Copilot·OpenCode·Gemini CLI·Qwen Code·Kimi K2 Code·Cursor 14+ 플랫폼용 범용 설치 관리자 skilz를 소개한다. | project-memory 스킬, skilz, Claude Code Skills, Rick Hightower, CLAUDE.md, AI 기억 상실, 에이전틱 스킬 마켓플레이스, Kimi K2 Code |
| [[20260317_072359_Claude Code 서브 에이전트 및 웹 검색을 무료로 실행하는 방법]] | Joe Njenga의 Ollama 기반 Claude Code 무료 실행 가이드 후속편. 서브 에이전트·웹 검색을 비용 없이 돌리기 위한 새 변형을 다루며 MiniMax M2.5와의 결합, Ollama·Codex·OpenCode 조합 설정을 안내하는 연재물. | Ollama Claude Code, 서브 에이전트 무료 실행, 웹 검색 무료, Joe Njenga, MiniMax M2.5, Codex OpenCode 조합, 토큰 절약 |
| [[20260318_220257_Perplexity 컴퓨터 내가 몇 시간 걸리던 일을 단 7분 만에 해냈다]] | 2026-02-25 출시된 Perplexity Computer는 Claude Opus 4.6 오케스트레이션 + Gemini 심층연구 + Grok 사실확인 + Nano Banana 이미지 + Veo 3.1 비디오 등 19개 AI 모델을 조정하는 다중 모델 에이전트. 모델 벤치마크 비교 작업을 7분 7초·363.20 크레딧으로 완수(build_spreadsheet.py 자동 작성/디버그, 33개 인용, 10개 불일치 탐지). Ask 2026에서 Personal Computer(Mac mini 기반 24시간 상주 Gmail/Slack/GitHub/Notion/Salesforce 연결), Search/Agent/Embeddings/Sandbox 4개 API, Statista·CB Insights·PitchBook 유료 데이터 통합 발표. Pro 4,000 보너스 크레딧, Max $200/월 30,000 크레딧. 7개 분야 복사용 프롬프트 제공. | Perplexity Computer, Ask 2026, 19개 모델 조정, Claude Opus 4.6 오케스트레이터, Nano Banana 2, Veo 3.1, Personal Computer Mac mini, 363 크레딧, build_spreadsheet.py, 벤치마크 불일치 감지 |
| [[20260329_181423_AI 시대 터미널을 초고속으로 향상시키는 7가지 CLI 도구]] | AI 기반 터미널 워크플로우를 위한 7가지 CLI 도구 소개 2026년 에디션. Aider(AI 페어 프로그래머, 파일 편집+자동 Git 커밋, 음성-코드 변환 Voice-to-Code), Gemini CLI(무료 티어, Gemini Flash/Pro 터미널 직접 접근, 에이전트형 파일 시스템 탐색)가 핵심이다. Aider는 deepseek/sonnet/o3-mini 멀티 모델 지원, pip install aider-install로 설치. | Aider, Gemini CLI, Voice-to-Code, Gemini Flash, AI 페어 프로그래머, 터미널 AI 에이전트, aider-install |
| [[20260329_193526_실전 가이드 OpenCode 사용법 오픈소스 Claude Code 대체재]] | OpenCode를 Claude Code 대체재로 설정하는 실전 입문서로, 전용 클라이언트에 ChatGPT 멤버십·Google Antigravity OAuth·GLM 4.7을 연결하는 과정을 안내한다. `opencode-antigravity-auth`와 `oh-my-opencode`를 설치해 Oracle, Librarian, Explorer 같은 역할형 에이전트를 붙이는 구성이 핵심이다. | OpenCode, oh-my-opencode, opencode-antigravity-auth, Antigravity, ChatGPT Pro, Gemini Ultra, GLM 4.7, Oracle agent |
| [[20260329_201915_2026년 개발자들이 주목해야 할 10가지 현대적인 에이전트형 AI 도구]] | 개발자가 반복 명령과 도구 전환에 쓰는 시간을 줄이기 위해 2026년에 눈여겨볼 에이전트형 개발 도구들을 정리한다. Goose, Claude Code, Repomix, Flowise, Portkey 같은 도구를 예로 들며 자율 실행, 코드베이스 압축, 시각적 오케스트레이션, 라우팅·폴백 자동화가 각각 어떤 작업에 맞는지 소개한다. | Goose, Claude Code, Repomix, Flowise, Portkey, agentic tools, context packing, LLM gateway, fallback routing |
| [[20260404_104403_AI 에이전트를 위해 설계된 브라우저 자동화 CLI 출시]] | Vercel의 agent-browser CLI가 접근성 트리 ref 기반 인터페이스와 50개 이상 명령어로 브라우저 자동화를 AI 에이전트 친화적으로 바꾸는 도구임을 소개한다. npm install과 snapshot -i, click @e3 같은 명령을 통해 Claude Code나 Cursor에 브라우저 조작 능력을 붙이는 방법이 핵심이다. | agent-browser, Vercel, accessibility tree, snapshot -i, click @ref, browser automation CLI, Chromium install |
| [[20260404_184241_Claw Code Claude Code 에이전트 하네스 클론이 급부상하는 이유 별점 11만 개 돌파]] | Claude Code 스타일의 에이전트 하네스를 복제한 Claw Code가 급격히 인기를 얻는 배경을 분석한다. 도구 사용 방식, 에이전트 하네스 설계, 커뮤니티 확산 속도와 별점 증가가 핵심 포인트다. | Claw Code, Claude Code clone, agent harness, open-source coding agent, GitHub stars |
| [[20260409_075629_OpenCode 개발자 세계를 강타한 선도적인 오픈소스 AI 코딩 에이전트]] | OpenCode를 선도적인 오픈소스 AI 코딩 에이전트로 소개하며 설치와 특징, 개발자 채택 이유를 정리한 글이다. Claude Code 대안으로서의 위치와 오픈소스 워크플로 장점을 강조한다. | OpenCode, open-source coding agent, Claude Code alternative, developer adoption, AI coding CLI |
| [[20260412_082539_Cursor Claude Code 그리고 Codex는 모두 최신 모델을 사용하지만 결과는 완전히 다릅니다 그 이유는 다음과 같습니다 모델은 상품이며 하네스가 제품입니다 Cursor]] | Cursor Cloud Agents가 격리 VM, 비디오 증명, 다중 모델 라우팅으로 자체 병합 PR의 35퍼센트를 생성하는 구조를 해부한다. GPT-5와 Claude와 Gemini보다 중요한 것은 하네스이며 Cursor, Claude Code, Codex의 차이는 실행 환경과 검증 계층에 있다고 주장한다. | Cursor Cloud Agents, harness, VM isolation, video artifact, Cursor 3 Glass, GPT-5, Claude Code, Codex |
| [[20260413_211933_Mistral Vibe 터미널 기반 AI 코딩 어시스턴트]] | Mistral Vibe를 터미널 기반 AI 코딩 어시스턴트로 소개하며 설치와 사용 맥락을 정리한다. 독립 CLI 도구가 Claude Code와 Cursor 외 대안이 되는지 평가하는 툴 리뷰 성격이다. | Mistral Vibe, terminal coding assistant, CLI agent, coding assistant, Mistral, terminal workflow, AI coding tool, developer CLI |
| [[20260413_212043_Mistral Vibe 터미널 기반 AI 코딩 어시스턴트]] | Mistral Vibe를 터미널 기반 AI 코딩 어시스턴트로 소개하며 설치와 사용 맥락을 정리한다. 독립 CLI 도구가 Claude Code와 Cursor 외 대안이 되는지 평가하는 툴 리뷰 성격이다. | Mistral Vibe, terminal coding assistant, CLI agent, coding assistant, Mistral, terminal workflow, AI coding tool, developer CLI |
| [[20260415_143410_클로드 코드Claude Code 활용도를 높여줄 10가지 CLI 도구]] | Claude Code와 함께 쓰면 효율이 올라가는 10가지 CLI 도구를 정리해 터미널 워크플로를 확장한다. 에이전트 본체보다 주변 유틸리티가 체감 생산성에 미치는 영향을 보여주는 툴 큐레이션이다. | Claude Code, CLI tools, terminal workflow, developer utilities, shell tools, agent workflow, Claude Code stack, command-line toolkit |
| [[20260419_151005_cmux 심층 리서치 AI 코딩 에이전트를 위한 macOS 네이티브 터미널]] | `manaflow-ai/cmux`를 Ghostty 기반 macOS 네이티브 터미널 앱으로 소개하며, Claude Code·Codex·Aider 같은 AI 에이전트 멀티세션 오케스트레이션에 특화된 기능을 정리한다. 파란 링 알림, 내장 브라우저, Unix 도메인 소켓 API, GitHub 14.7K 스타 등이 차별점이다. | cmux, Ghostty, Unix domain socket API, Claude Code Teams, blue ring notifications, macOS terminal, manaflow-ai/cmux |
| [[20260420_223053_MiniMax CLI 출시 및 사용 후기 단순한 코딩 에이전트 복제본이 아니다]] | MiniMax CLI가 Claude Code 복제품이 아니라 텍스트, 이미지, 비디오, 음성, 음악을 터미널에서 생성하는 멀티모달 생성 도구라는 점을 직접 테스트로 확인한다. Hailuo AI와 Talkie 기반 회사 배경, 7개 기능 축을 통해 코딩 에이전트와 다른 사용 맥락을 분명히 한다. | MiniMax CLI, Hailuo AI, Talkie, multimodal generation, text image video speech music, terminal tool |

## 관련 카테고리

* > 상위: [[AI Coding Tools Index]]
* > 최상위: [[ArticleScrap Platform Index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
