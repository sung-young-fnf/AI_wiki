# Vibe Coding SDD Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-03-03 |
| 키워드 | `바이브 코딩`, `Vibe Coding`, `agentic engineering`, `SDD`, `spec-driven`, `스펙 주도 개발`, `Spec Kit`, `GSD`, `OpenSpec`, `BMAD`, `Superpowers`, `Taskmaster AI`, `Maturity Spectrum`, `Agile`, `Waterfall`, `프롬프트 계약`, `AGENTBENCH` |
| 관련문서 | [[AI Engineering Index]], [[ArticleScrap Platform Index]] |

## 개요

바이브 코딩(Vibe Coding) 방법론·안티패턴·스펙 주도 개발(SDD)·Spec Kit·GSD·OpenSpec·BMAD·Superpowers·Taskmaster·Agile vs Waterfall.

## 파일 목록 (9개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260227_213942_나는 바이브 코딩을 그만두고 프롬프트 계약을 시작했다 클로드 코드는 도박이 아닌 실전 도구가 되었다]] | 전직 OpenAI 엔지니어가 제안한 프롬프트 계약(Prompt Contracts) 4개 조항(GOAL/CONSTRAINTS/FORMAT/FAILURE CONDITIONS)을 3주간 Claude Code에 적용한 실전기. CLAUDE.md에 Next.js App Router·Convex·Supabase·Clerk·Tailwind 스택을 "협상 불가" 제약으로 고정하자 되돌리기 3/1→1/10, 주고받기 3→1.2회, 규칙 위반 제로 달성. 실패 조건(useState 남용·150줄 초과 등)이 가드레일 역할. | 프롬프트 계약, Prompt Contracts, 바이브 코딩, Vibe Coding, CLAUDE.md, Failure Conditions, Convex, Clerk, 4개 조항 프레임워크 |
| [[20260315_214227_Claude Code를 활용한 스펙 중심 개발 Spec-driven Development]] | AWS 솔루션스 아키텍트 Heeki가 Claude Code에 적용한 스펙 중심 개발 실천기. Birgitta Böckeler의 spec-first/spec-anchored/spec-as-source 3단계 구분을 소개하며 AgentCore Gateway 인터셉터 CloudFormation 스택을 SAM 서버리스 모듈로 쪼개 반복하는 사양 기반 피드백 루프 워크플로를 제시. | 스펙 중심 개발, Spec-driven Development, Birgitta Böckeler, spec-first, spec-anchored, spec-as-source, AgentCore Gateway, SAM 서버리스, Claude Code SDD |
| [[20260318_184757_클로드 코드Claude Code에 수백만 개의 토큰을 소모했습니다 바이브 코딩Vibe Coding이 함정인 이유를 알려드립니다]] | Max 플랜으로 수백만 토큰을 소모한 저자가 정리한 Claude Code 안티패턴+핵심 전략 6가지. Ralph Loop(배시 루프 자율 실행) 거부, Shift+Tab으로 진입하는 Plan Mode 필수 사용, 커스텀 서브에이전트(Docs Explorer Agent)+Context 7 MCP, 자체 Next.js Best Practices 스킬, 암시보다 명시 등 Vibe Coding을 '고속 외골격' 관점에서 배격하는 실전 지침. | Ralph Loop 안티패턴, Plan Mode Shift+Tab, Docs Explorer Agent, Context 7 MCP, Claude Code Max 플랜, Vibe Coding 함정, Next.js Best Practices 스킬, 고속 외골격 |
| [[20260318_220236_에이전트 기반 코딩 GSD Spec Kit OpenSpec Taskmaster AI 비교 SDD 도구들의 차이점]] | Rick Hightower의 SDD 네 도구 비교(2026.02 기준 GitHub 스타 총 137k+). GSD(16.7k, 3개 런타임 Claude Code/OpenCode/Gemini CLI, 웨이브 병렬 실행 + 서브 에이전트별 새로운 컨텍스트 격리, `/gsd:discuss-phase`), Spec Kit(70.8k, 18개 에이전트, constitution→specify→plan→implement), OpenSpec(24.9k, 20+ 도구, 브라운필드 우선 변경 격리 `/opsx:`), Taskmaster AI(25.5k, Cursor MCP 최우선, 다중 모델 main/research/fallback, MIT+Commons Clause). 축: 실행 깊이(오케스트레이션 vs 위임), 컨텍스트 전략, 브라운필드/그린필드, 플랫폼 확장/심층, 라이선스. | GSD 컨텍스트 격리, Spec Kit constitution, OpenSpec /opsx:ff, Taskmaster AI Cursor MCP, Rick Hightower, SDD 비교, 웨이브 병렬 실행, 브라운필드 변경 격리, Commons Clause, PRD 작업 분해 |
| [[20260322_122452_선도적인 에이전트 기반 코딩 프레임워크 대결 Superpowers BMAD SpecKit GSD 실무자 비교]] | Rick Hightower의 5개 프레임워크 실무자 비교(2026-03 기준 총 170k+ GitHub 스타). 공유 트라이어드(에이전트/워크플로우/스킬) 위에 각 차별화: BMAD(40.2k, 12+ 페르소나 엔터프라이즈 팀 + 파티 모드 + 스케일 적응형), SpecKit(75.9k, 게이티드 constitution→specify→plan→tasks→implement, 아티팩트 800줄), OpenSpec(29.5k, 브라운필드 델타 ADDED/MODIFIED/REMOVED, 250줄), GSD(28.1k, 서브에이전트당 새 200K 컨텍스트 격리 + 웨이브 병렬 + 디버거 과학적 방법론), Superpowers(TDD '삭제-재작성' 철칙 + 설득 기반 가드레일). 컨텍스트 로트 50%+ 저하 연구. 하이브리드 SpecKit+GSD, BMAD+Superpowers, OpenSpec+Superpowers 제안. | Rick Hightower 프레임워크 대결, BMAD 파티 모드 페르소나, SpecKit 게이티드 프로세스, OpenSpec 델타 마커, GSD 웨이브 병렬 컨텍스트 격리, Superpowers TDD 삭제-재작성, 컨텍스트 로트 50%, 공유 트라이어드, 하이브리드 프레임워크 조합, skilz 마켓플레이스 |
| [[20260329_105023_2026년 디자인 바이브 코딩Vibe Coding은 끝났다 이제 무엇이 다가오는가]] | Michal Malewicz의 바이브 코딩 비판. AI가 만든 랜딩 페이지가 중간값으로 수렴해 98% 평균 웹사이트가 범람하는 '슬롭 쇼크'를 지적하고, 원인을 기술(craft) 붕괴와 프롬프트 사재기·도구 집착으로 분석. 해결책은 최소가시정밀도(MVP를 Minimum Visible Precision으로 확장), 기본기(타이포·간격·계층), 결정권 AI 위임 금지. AI는 승수일 뿐 0×n=0. | 바이브 코딩, Vibe Coding, Michal Malewicz, AI 슬롭, AI slop, 프롬프트 사재기, Minimum Visible Precision, 최소가시정밀도, design craft, 디자인 평균화 |
| [[20260410_104356_Beyond Vibe Coding The Maturity Spectrum]] | 바이브 코딩에서 에이전틱 엔지니어링과 멀티 에이전트 오케스트레이션까지 4단계 성숙도 스펙트럼을 제시한다. Karpathy, Devin, Anthropic 2026 트렌드 리포트와 EnrichLead, Moltbook 사례를 묶어 사양, 테스트, 거버넌스 없는 일회용 코드의 위험을 설명한다. | Vibe Coding, agentic engineering, multi-agent orchestration, Karpathy, Devin, EnrichLead, Moltbook, durable code |
| [[20260410_104457_Agile Is Dead AI Killed It Welcome Back Waterfall]] | AI 네이티브 개발에서는 스프린트와 스토리 포인트보다 전체 범위 명세와 아키텍처 문서가 더 중요하다고 주장한다. Scrum, planning poker, burn-down chart 같은 의식 비용을 비판하고 spec 중심 계획과 피드백 루프를 새 기본값으로 제안한다. | Agile, Waterfall, Scrum, story points, planning poker, specification, AI-augmented development, burn-down chart |
| [[20260421_142910_1년 간의 바이브 코딩 이후 AI는 노력과 전문성을 대체할 수 없다 범용성 맥락 붕괴 에이전틱 엔지니어링]] | 1년간의 바이브 코딩 열풍을 돌아보며 AI가 숙련과 검증을 대체하지 못한다는 반론을 전개한다. Steve Yegge와 Karpathy 논의를 엮어 맥락 붕괴, 과잉 일반화, 에이전틱 엔지니어링의 검토 역할을 짚는다. | vibe coding, agentic engineering, Steve Yegge, Andrej Karpathy, context collapse, code review |

## 관련 카테고리

* > 상위: [[AI Engineering Index]]
* > 최상위: [[ArticleScrap Platform Index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
