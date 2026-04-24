# 번역 기사

- **원본 URL:** https://ai.gopubby.com/anthropics-antspace-the-secret-paas-nobody-was-supposed-to-find-a79ce1e02151
- **번역 일시:** 2026-03-29 20:23:34

---

# 앤트로픽(Anthropic)의 앤트스페이스(Antspace): 아무도 찾아서는 안 될 비밀 PaaS

19세 개발자가 클로드 코드 웹(Claude Code Web)을 리버스 엔지니어링하여 앤트로픽(Anthropic)이 모델부터 호스팅까지, 클라우드 스택(cloud stack) 전체를 은밀하게 구축하고 있음을 밝혀냈습니다. 모든 계층을요.

* * *

**AI Advances**
인공지능 접근의 민주화
**Mandar Karhade, MD. PhD.** 작성
읽는 시간 13분 · 5일 전 게시
695개 추천, 댓글 8개
유료 회원 전용 기사

* * *

새로운 프로필(https://medium.com/@ThisWorld)을 팔로우해 주시면 감사하겠습니다. 이 채널에서는 헬스 테크(Health tech), 글로벌 테크(Global tech), AI 거버넌스(AI Governance)에 대한 다부작 심층 탐사 기사를 다룰 예정입니다. 새로운 채널을 구독하고 팔로우해 주시면 감사하겠습니다.

## TLDR

*   19세 개발자가 클로드 코드 웹(Claude Code Web) 내의 스트립되지 않은(unstripped) Go 바이너리(binary)를 리버스 엔지니어링하여 앤트로픽(Anthropic)이 전혀 발표하지 않았던 PaaS(Platform-as-a-Service) 플랫폼인 "앤트스페이스(Antspace)"를 발견했습니다.
*   "바쿠(Baku)"는 클로드(Claude)의 웹 앱(web app) 빌더에 대한 내부 코드명입니다. 이는 수파베이스(Supabase) 데이터베이스를 자동으로 프로비저닝(provisioning)하며, 기본 배포 대상은 버셀(Vercel)이 아닌 앤트스페이스(Antspace)입니다.
*   전체 파이프라인(pipeline)은 수직적입니다: 의도(intent) → 클로드(Claude) → 바쿠(Baku) → 수파베이스(Supabase) → 앤트스페이스(Antspace) → 라이브 앱(live app)으로 이어지며, 사용자는 앤트로픽(Anthropic)의 생태계를 벗어나지 않습니다.
*   쿠버네티스(Kubernetes) 통합 및 7개의 API 엔드포인트(endpoint)를 갖춘 BYOC(Bring Your Own Cloud) 엔터프라이즈(enterprise) 계층은 앤트로픽(Anthropic)이 인프라(infra) 계약까지 원한다는 것을 의미합니다.
*   앤트로픽(Anthropic)은 핵심 바이너리(binary)를 완전한 디버그 심볼(debug symbols)과 비공개 모노레포(monorepo) 경로와 함께 모든 사용자 세션(session)에 배포했습니다. "안전 제일"을 내세우는 AI 연구소로서는 의아한 선택입니다.

---

모두를 위한 친구 링크: 저자와 출판사를 팔로우하고 추천을 눌러주세요. 다른 작가들을 지원하기 위해 미디엄(Medium)에 가입해 주세요! 감사합니다.

---

앤트로픽(Anthropic)은 아직 당신이 이것을 보기를 원치 않았을 것입니다. 연구원들도, 제품 팀도, 클로드(Claude)의 확장되는 기능에 대한 모든 발표를 신중하게 기획하는 홍보 담당자들도 마찬가지입니다. 그리고 제가 '놀랍도록 편리한 부주의'라고밖에 표현할 수 없는 순간에, 27메가바이트(MB)짜리 Go 바이너리(binary)를 스트립하지 않은 채 프로덕션(production) 환경에 배포한 인프라(infrastructure) 엔지니어들도 분명히 그러했을 겁니다.

*(사진을 보려면 엔터 키를 누르거나 클릭하세요)*
*(사진을 보려면 엔터 키를 누르거나 클릭하세요)*
[https://aprilnea.me/en/blog/reverse-engineering-claude-code-antspace](https://aprilnea.me/en/blog/reverse-engineering-claude-code-antspace)

하지만 이런 일이 벌어졌습니다. 앤트로픽(Anthropic)과 동일한 파이어크래커 마이크로VM(Firecracker MicroVM) 기술을 활용하는 스타트업 아크박스 랩스(ArcBox Labs)의 설립자이자 19세 풀스택 엔지니어(full-stack engineer)인 에이프릴니아(AprilNEA)가 자신의 클로드 코드 웹(Claude Code Web) 세션(session) 내부를 스트레이스(strace), 스트링스(strings), 고 툴 오브덤프(go tool objdump)와 같은 평범한 리눅스(Linux) 도구만을 사용하여 살펴보았기 때문입니다. 익스플로잇(exploit)도, 권한 상승(privilege escalation)도, 제로데이(zero-day) 공격도 없었습니다. 그저 모든 리눅스 배포판(distro)에 항상 포함되어 있는 리눅스 도구일 뿐이었습니다.

그들이 발견한 것은 솔직히 말해 올해 AI 업계에서 가장 중대한 인프라(infrastructure) 유출 중 하나입니다. 앤트로픽(Anthropic)은 단순히 챗봇(chatbot)을 만드는 것이 아닙니다. 그들은 전체 클라우드(cloud)를 구축하고 있습니다. 당신의 의도를 이해하는 모델(model)부터 코드를 작성하는 런타임(runtime), 데이터를 저장하는 데이터베이스(database), 애플리케이션(application)을 호스팅하는 플랫폼(platform)까지, 모든 계층을 말입니다.

기술계는 "AI 코딩 에이전트(AI coding agents)"에 대한 흥분으로 들끓고 있습니다. 하지만 더 이상 코딩 에이전트에 대한 이야기가 아닙니다. 이것은 플랫폼 지배력(platform dominance)에 관한 것입니다.

## 앤트로픽(Anthropic) 인프라를 한 십 대가 어떻게 파헤쳤나

무엇을 발견했는지 못지않게 '어떻게' 발견했는지도 흥미롭기 때문에, 먼저 그 방법에 대해 이야기해봅시다.

에이프릴니아(AprilNEA)의 동기는 경쟁사 연구였습니다. 아크박스 랩스(ArcBox Labs)는 레일웨이(Railway) 및 E2B와 유사한 인프라(infrastructure), 즉 AI 에이전트(AI agents)를 위한 샌드박스(sandboxed) 실행 환경을 구축하고 있습니다. 그들은 클로드 코드 웹(Claude Code Web) (그리고 사실상 모든 진지한 코딩 에이전트 플랫폼)이 아마존 람다(Lambda)와 파게이트(Fargate)를 위해 원래 구축된 아마존(Amazon)의 오픈 소스 하이퍼바이저(hypervisor)인 파이어크래커 마이크로VM(Firecracker MicroVM) 위에서 실행되고 있다는 것을 알아차렸습니다. 이는 일반적인 사항입니다.

그래서 그들은 클로드 코드 웹(Claude Code Web) 세션(session)을 시작하고 내부를 살펴보았습니다. 첫 번째 발견: VM(가상 머신) 내부의 ACPI 테이블(tables)이 OEM ID 'FIRECK'와 생성자 ID 'FCAT'로 서명되어 있었습니다. 이는 하드코딩된(hardcoded) 파이어크래커(Firecracker) 서명입니다. 머신(machine) 사양은 견고한 개발용 PC와 같았습니다: 4개의 vCPU(Intel Xeon Cascade Lake 2.8GHz), 16GB RAM, 252GB 디스크(disk), 리눅스 커널(Linux kernel) 6.18.5를 실행 중이었습니다.

이 VM 내부의 프로세스 트리(process tree)는 최소한의 구성입니다. 아주 공격적일 정도로요. PID 1은 시스템디(systemd)가 아니라, 포트(port) 2024에서 웹소켓 게이트웨이(WebSocket gateway) 역할을 겸하는 '/process_api'라는 사용자 정의 초기화 바이너리(custom init binary)였습니다. sshd도, cron도, journald도 없었습니다. 클로드(Claude) 환경을 실행하는 데 필요한 최소한의 하드웨어(bare metal)만 있었습니다.

하지만 중요한 것은 이것입니다: '/usr/local/bin/environment-runner'에는 27MB짜리 Go 바이너리(binary)가 있었습니다. 에이프릴니아(AprilNEA)가 이 파일에 'go version -m' 명령을 실행했을 때, 빌드 메타데이터(build metadata)가 모든 것을 드러냈습니다. 모듈 경로(Module path): github.com/anthropics/anthropic/api-go/environment-manager. 버전 문자열(Version string): staging-68f0dff496. Go 1.25.7로 빌드되었습니다.

그 바이너리는 스트립되지 않은(unstripped) 상태였습니다. 완전한 디버그 심볼(debug symbols)과 전체 심볼 테이블(symbol table)을 포함하고 있었습니다. 전체 내부 패키지 트리(package tree)가 그대로 노출되어 있었습니다.

일반적으로 기드라(Ghidra), IDA Pro와 수 시간에 걸친 고된 디컴파일(decompilation)이 필요했을 작업이, 'go tool objdump'와 'grep' 명령으로 간단히 해결되었습니다. 에이프릴니아(AprilNEA)는 이에 대해 직접 이렇게 말했습니다: "완전한 디버그 심볼(debug symbols)이 포함된 스트립되지 않은(unstripped) 바이너리를 프로덕션(production) 환경에 배포하는 것은... 일종의 선택이다."

```
internal/
├── api/                  # API 클라이언트 (세션 인그레스, 작업 폴링, 재시도)
├── auth/                 # GitHub 앱 토큰 제공자
├── claude/               # 클로드 코드 설치, 업그레이드, 실행
├── config/               # 세션 모드 (새로운/재개/캐시된 재개/설치 전용)
├── envtype/
│   ├── anthropic/        # 앤트로픽 호스팅 환경
│   └── byoc/             # Bring Your Own Cloud 환경
├── gitproxy/             # Git 자격 증명 프록시 서버
├── input/                # Stdin 파서 + 비밀 처리
├── manager/              # 세션 관리자, MCP 설정, 스킬 추출
├── mcp/
│   └── servers/
│       ├── codesign/     # 코드 서명 MCP 서버
│       └── supabase/     # 수파베이스 통합 MCP 서버
├── orchestrator/         # 폴링 루프, 훅, whoami
├── podmonitor/           # 쿠버네티스 리스 관리자
├── process/              # 프로세스 실행 + 스크립트 러너
├── sandbox/              # 샌드박스 런타임 설정
├── session/              # 활동 기록기
├── sources/              # Git 클론 + 소스 분류
├── tunnel/               # 웹소켓 터널 + 액션 핸들러
│   └── actions/
│       ├── deploy/       # ← 여기가 흥미로워지는 부분
│       ├── snapshot/     # 파일 스냅샷
│       └── status/       # 상태 보고
└── util/                 # Git 헬퍼, 재시도, 스트림 테일러
```

아, 물론 '선택'이겠죠. "안전 제일" AI 연구소라고 자처하며, 책임 있는 확장 정책을 발표하고 실존적 위험에 대해 끊임없이 이야기하는 회사가 내부 아키텍처(architecture) 전체를 읽을 수 있는 평문(plaintext) 형태로 모든 사용자 세션(session)에 배포한다는 것은… 정말이지. 진심입니까?

## 바이너리(binary) 내부: 전체 패키지 트리(package tree)

바이너리(binary)에서 추출된 'internal/' 디렉터리(directory) 구조는 풀스택 클라우드 플랫폼(full-stack cloud platform)의 청사진처럼 읽힙니다. 코딩 보조 도구가 아니라, 하나의 플랫폼입니다.

내부에는 다음과 같은 요소들이 있었습니다: 세션 관리 및 폴링(polling)을 위한 'api/', CLI 설치 및 실행을 위한 'claude/', 'new', 'resume', 'resume-cached', 'setup-only'를 포함하는 세션 모드 설정용 'config/'. 'anthropic'과 'byoc'(이 부분은 다시 언급하겠습니다) 두 개의 서브 패키지(sub-packages)를 가진 'envtype/'. 코드 서명 및 수파베이스(Supabase) 통합을 위한 'mcp/servers/'. 쿠버네티스(Kubernetes) 리스(lease) 관리를 위한 'podmonitor/' 패키지(package). 그리고 가장 중요한 'tunnel/actions/deploy/'는 배포 핸들러(deployment handlers)를 포함하고 있었습니다.

의존성 목록(dependency list)은 그 자체로 많은 것을 말해줍니다. 터널링(tunneling)을 위한 고릴라 웹소켓(Gorilla WebSocket). 모델 컨텍스트 프로토콜(Model Context Protocol) 통합을 위한 MCP-Go v0.37.0. 메트릭(metrics)을 위한 데이터독(DataDog) v5. 분산 트레이싱(distributed tracing)을 위한 오픈텔레메트리(OpenTelemetry) v1.39.0. 세션 라우팅(session routing)을 위한 gRPC v1.79.0. 이것은 프로토타입(prototype)이 아닙니다. 이것은 완전한 관측 가능성(observability)이 내장된 프로덕션(production) 인프라(infrastructure)입니다.

## 앤트스페이스(Antspace): 공식적으로 존재하지 않는 PaaS

그 'tunnel/actions/deploy/' 패키지(package) 안에서 에이프릴니아(AprilNEA)는 두 개의 배포 클라이언트(deployment clients)를 발견했습니다.

*(사진을 보려면 엔터 키를 누르거나 클릭하세요)*
[https://aprilnea.me/en/blog/reverse-engineering-claude-code-antspace](https://aprilnea.me/en/blog/reverse-engineering-claude-code-antspace)

첫 번째는 버셀 클라이언트(VercelClient)였습니다. 예상했던 대로입니다. 클로드 코드 웹(Claude Code Web)은 한동안 버셀(Vercel) 배포를 지원해왔습니다. 구현은 깔끔했습니다: SHA 기반 파일 중복 제거(deduplication), 폴링(polling) 기반 배포 상태 확인. 표준 버셀 API(API) 통합입니다. 놀랄 것은 없었습니다.

두 번째는 앤트스페이스 클라이언트(AntspaceClient)였습니다. 이는 전혀 예상치 못한 것이었습니다.

발견 당시, 'Antspace'에 대한 인터넷 검색 결과는 정확히 0건이었습니다. 앤트로픽(Anthropic) 웹사이트에도, 깃허브(GitHub)에도, 블로그(blog)나 문서, 링크드인(LinkedIn), 채용 공고, 특허 출원 서류 어디에도 없었습니다. 전무했습니다. 이 단어는 2026년 3월 18일 에이프릴니아(AprilNEA)의 게시물이 나오기 전까지는 공개적으로 존재하지 않았습니다.

배포 프로토콜(deployment protocol) 자체는 세 단계로 이루어집니다. 1단계: 베어러 토큰(bearer token)과 함께 'antspaceControlPlaneURL'로 POST 요청을 보내 앱 이름과 메타데이터(metadata)를 포함하는 JSON 본문(body)을 전송합니다. 2단계: 'dist.tar.gz'를 멀티파트 폼 데이터(multipart form-data)로 업로드(upload)하며, 엄격한 크기 제한("프로젝트가 %dMB 제한을 초과합니다")이 적용됩니다. 3단계: 배포 상태를 NDJSON(줄바꿈으로 구분된 JSON) 형식으로 스트리밍(stream)하며, '패키징(packaging) → 업로드(uploading) → 빌딩(building) → 배포(deploying) → 배포 완료(deployed)'와 같은 정의된 진행 단계를 따릅니다.

이것은 해커톤(hackathon) 프로젝트가 아닙니다. 이 프로토콜(protocol)은 성숙합니다. 적절한 오류 처리(error handling), 크기 제한, 스트리밍(streaming) 상태 업데이트(updates) 기능을 갖추고 있습니다. 앤트로픽(Anthropic)의 누군가가 상당한 엔지니어링 시간을 들여 이것을 구축했습니다. 그리고 아무에게도 알리지 않은 채 스트립되지 않은(unstripped) 바이너리(binary) 안에 이 기능을 넣어 배포했습니다.

이름의 어원은 너무나도 명확합니다. 'Ant'는 앤트로픽(Anthropic) 직원들이 스스로를 부르는 내부 별명이라고 합니다. 'Space'는 호스팅 공간(hosting space)을 의미합니다. 앤트스페이스(Antspace). 개미들이 스스로 무언가를 호스팅할 공간을 만든 것입니다. 귀엽네요.

## 바쿠(Baku): "웹 앱(web app) 만들어줘" 뒤에 숨겨진 코드명

claude.ai에서 클로드(Claude)에게 웹 앱(web app)을 만들어 달라고 요청하면 클로드가 그냥… 만들어주는 경험을 아시나요? 개발 환경(dev environment)을 구축하고, 리액트(React) 컴포넌트(components)를 작성하며, 실시간 미리보기(live preview)를 제공하는 것 말입니다. 내부적으로는 이를 "바쿠(Baku)"라고 부릅니다.

바쿠(Baku) 스택(stack)은 Vite + React + TypeScript로 구성되어 있으며, 'supervisord'를 통해 관리되고 개발 서버 로그(logs)는 '/tmp/vite-dev.log'로 파이프됩니다. 템플릿(templates)은 '/opt/baku-templates/vite-template'에 있습니다. Git 커밋(commits)은 'claude@anthropic.com'으로 작성됩니다. 초안은 '.baku/drafts/'에, 탐색 기록은 '.baku/explorations/'에 저장됩니다. 이는 단순한 채팅 인터페이스(chat interface)처럼 보이는 것 안에 숨겨진 완전한 프로젝트 관리 시스템(project management system)입니다.

하지만 여기서 흥미로운 부분이 나옵니다. 바쿠(Baku)는 여섯 가지의 자동 사용 가능한 수파베이스(Supabase) MCP 도구와 함께 제공됩니다: 'provision_database', 'execute_query', 'apply_migration', 'list_migrations', 'generate_types', 그리고 'deploy_function'. 환경 변수(environment variables) (SUPABASE_URL, SUPABASE_ANON_KEY 및 이들의 Vite 접두사(prefixed) counterparts)는 '.env.local'에 자동으로 작성됩니다.

사용자는 데이터베이스(database)를 설정하거나 연결 문자열(connection strings)을 구성할 필요가 없습니다. 심지어 수파베이스(Supabase)가 관여하고 있다는 사실조차 직접 찾아보기 전까지는 알 수 없습니다. 클로드(Claude)가 MCP를 통해 조용히… 프로비저닝(provisioning)합니다.

그리고 기본 배포 대상(deploy target)은? 버셀(Vercel)이 아닙니다. 앤트스페이스(Antspace)입니다.

배포 전에 사전 중지 훅(Pre-stop hooks)은 코드 품질 게이트(code quality gates)를 강제합니다: 커밋되지 않은 변경 사항(uncommitted changes), 타입스크립트(TypeScript) 오류, Vite 빌드 로그(build log) 오류를 확인합니다. 이는 채팅 기능으로 위장한 CI/CD(Continuous Integration/Continuous Delivery) 파이프라인(pipeline)입니다.

## 완전한 파이프라인(pipeline): 앤트로픽(Anthropic)을 벗어나지 않고 의도(intent)부터 프로덕션(production)까지

앤트로픽(Anthropic)이 여기에 무엇을 구축했는지 명확히 설명해 드리고자 합니다. 아직 업계에서 그 함의를 완전히 이해하지 못하고 있다고 생각하기 때문입니다.

파이프라인(pipeline)은 다음과 같습니다:

사용자가 아이디어를 냄 → 클로드(Claude, LLM)가 의도를 이해하고 코드를 작성 → 바쿠(Baku, 런타임)가 프로젝트를 관리하고, 개발 서버(dev server)를 실행하며, 버전 관리(version control)를 처리 → 수파베이스(Supabase, MCP를 통해 자동 프로비저닝)가 데이터베이스(database), 인증(auth), 엣지 함수(edge functions)를 제공 → 앤트스페이스(Antspace, PaaS)가 배포된 애플리케이션(application)을 호스팅 → 인터넷에서 접속 가능한 라이브 앱(live app).

어떤 시점에도 사용자는 앤트로픽(Anthropic)의 생태계를 벗어나지 않습니다. 호스팅(hosting)을 위해서도, 데이터베이스(database)를 위해서도, 배포(deployment)를 위해서도, 그 어떤 것을 위해서도 말입니다.

이것은 AI 코딩 어시스턴트(AI coding assistant)가 아닙니다. 이것은 AI가 인터페이스(interface) 계층인 수직 통합 클라우드 플랫폼(vertically integrated cloud platform)입니다. 사용자는 원하는 바를 영어로 말합니다. 나머지 모든 것은 앤트로픽(Anthropic)의 인프라(infrastructure) 위에서 앤트로픽(Anthropic)의 도구를 사용하여 자동으로 발생합니다.

이것이 아니라면… 대체 무엇일까요? 사용자가 터미널(terminal), Git 리포지토리(repo), 호스팅 대시보드(hosting dashboard), 또는 데이터베이스 콘솔(database console)을 전혀 건드리지 않고도 자연어 입력(natural language input)을 받아 배포된, 데이터베이스 기반 웹 애플리케이션(database-backed web application)을 생성하는 시스템을 무엇이라고 부르겠습니까?

## BYOC(Bring Your Own Cloud): 엔터프라이즈(enterprise) 전략

발견은 앤트스페이스(Antspace)에서 멈추지 않았습니다. 'envtype/' 패키지(package) 내부에는 'anthropic' 서브 타입(sub-type)과 함께 'byoc'(Bring Your Own Cloud)가 있었습니다.

이것이 바로 엔터프라이즈(enterprise) 전략입니다. BYOC는 기업이 자체 인프라(infrastructure)에서 'environment-runner'를 실행하는 동안, 앤트로픽(Anthropic)의 API가 세션(session)을 오케스트레이션(orchestrate)하도록 허용합니다. 두 가지 환경 서브 타입(sub-types)이 있습니다: 앤트스페이스(Antspace, 앤트로픽 내부 호스팅)와 바쿠(Baku, 프로젝트 빌더).

신원 확인을 위한 '/v1/environments/whoami', 작업 폴링(polling) 및 확인 핸들러(acknowledgment handlers), 세션 컨텍스트(session context) 구성, 코드 서명(code signing) 및 바이너리 검증(binary verification), 실시간 터널링(tunneling)을 위한 워커 웹소켓(worker WebSocket), 그리고 수파베이스(Supabase) 데이터베이스(database) 쿼리 프록시(query proxy)를 포함하여 7개의 정의된 API 엔드포인트(endpoints)가 발견되었습니다.

'podmonitor'를 통한 쿠버네티스(Kubernetes) 통합은 파드(pod) 리스(lease) 관리를 처리합니다. 인증 주입(Auth injection)은 'containProvideAuthRoundTripper'라는 것을 통해 이루어집니다. Git 브랜치(branch) 유효성 검사(validation)는 가져오기(fetch) 작업 전에 발생합니다. 기본 세션 모드(session mode)는 'resume-cached'이며, 이는 세션이 지속되고 중단된 지점에서 다시 시작될 수 있음을 의미합니다.

이것은 추측이 아닙니다. 이것은 적절한 인증(auth), 세션 관리(session management), 인프라 추상화(infrastructure abstraction)를 갖춘 엔터프라이즈(enterprise) 준비가 된 오케스트레이션(orchestration) 계층입니다. 앤트로픽(Anthropic)은 포춘 500(Fortune 500) 기업들이 자체 클라우드(cloud)에서 클로드(Claude) 기반 개발 환경을 운영할 수 있도록 도구를 구축하고 있으며, 앤트로픽(Anthropic)은 오케스트레이션(orchestration) 계층에 대한 통제권을 유지하고 있습니다.

목표가 내가 이기는 것이 아니라 당신이 지는 것이라면, 이런 모습일 것입니다. AI 샌드박스(sandboxes)를 구축하는 모든 스타트업(startup), "AI에서 배포" 워크플로우(workflows)를 제공하는 모든 회사, AI 코딩 에이전트(AI coding agents)와 프로덕션(production) 호스팅(hosting) 사이의 다리가 되려는 모든 플랫폼(platform)은 모두 앤트로픽(Anthropic)이 모델(model) 자체 내에서 무료로 묶어 제공하는 기능을 구축하고 있는 셈입니다.

## 누가 피해를 볼 것인가 (그리고 누가 걱정해야 하는가)

경쟁적 함의에 대해 직접적으로 이야기해봅시다. 해커 뉴스(Hacker News) 사용자들은 이미 이것을 파악했기 때문입니다. 한 댓글 작성자인 'jonathan-adly'는 이를 "훌륭한 발견이자 매우 흥미롭고, 몇몇 잘나가는 스타트업을 죽일 것"이라고 평했습니다.

그들의 말이 틀리지 않습니다. 다음은 피해 예상 목록입니다:

### 버셀(Vercel), 넷리파이(Netlify)

버셀(Vercel)과 넷리파이(Netlify)는 가장 명확한 피해 대상입니다. 만약 클로드(Claude)의 기본 배포 대상(deployment target)이 버셀(Vercel)이 아닌 앤트스페이스(Antspace)라면, 버셀(Vercel)은 AI 생성 앱(AI-generated apps)의 기본 배포 플랫폼에서 선택적 대안으로 전락하게 됩니다. 이는 유입 경로(funnel)에 엄청난 변화를 가져올 것입니다.

### 리플릿(Replit), 러버블(Lovable), 볼트(Bolt)

리플릿(Replit), 러버블(Lovable), 볼트(Bolt) 및 전체 "AI 앱 빌더(AI app builder)" 카테고리가 해당됩니다. 이 회사들은 코드 생성(code generation) 위에 UX 계층을 구축하고 있습니다. 하지만 클로드(Claude)가 claude.ai 내에서 코드를 생성하고, 프로젝트를 관리하고, 데이터베이스(database)를 프로비저닝(provisioning)하며, 앱(app)을 배포할 수 있다면, 별도의 AI 앱 빌더가 제공하는 가치 제안(value proposition)은 정확히 무엇이 될까요?

### E2B, 레일웨이(Railway), 파이어크래커(Firecracker)

E2B, 레일웨이(Railway), 그리고 파이어크래커(Firecracker) 샌드박스(sandbox) 제공업체들입니다. 앤트로픽(Anthropic)은 자체 샌드박스를 구축했습니다. 작동하며, 모델(model)에 통합되어 있습니다. 서드파티(Third-party) 샌드박스(sandboxes)는 다른 모델들에게는 '있으면 좋은 것(nice-to-have)'이 되겠지만, 클로드(Claude)에게는 '필수 요소(must-have)'가 아닙니다.

### 수파베이스(Supabase)의 흥미로운 위치

수파베이스(Supabase)는 동시에 파트너이자 의존성(dependency)이라는 점에서 흥미롭습니다. 바쿠(Baku) 통합은 claude.com/plugins/supabase에 있는 공개 클로드(Claude) 플러그인(plugin)보다 더 깊이 연결되어 있습니다. 이는 사용자 상호작용 없이 자동 프로비저닝(auto-provisioning)을 의미합니다. 수파베이스(Supabase)는 배포(distribution) 이점을 얻지만, 그 대가로 사용자가 의식적으로 선택하지 않는 상품화된 백엔드(backend)가 될 위험이 있습니다.

### 데이터 선순환(data flywheel)

마지막 부분이 진짜 핵심입니다. 바로 데이터 선순환(data flywheel)입니다. 바쿠(Baku)를 통해 구축된 모든 앱(app), 앤트스페이스(Antspace)에 배포된 모든 서비스, MCP를 통해 프로비저닝(provisioning)된 모든 데이터베이스(database)는 사람들이 무엇을 어떻게 구축하고자 하는지에 대한 앤트로픽(Anthropic)의 이해도를 높이는 데 다시 피드백됩니다. 이것은 단순한 플랫폼(platform)이 아닙니다. 플랫폼으로서 스스로를 개선하는 학습 시스템(learning system)입니다.

## 아무도 무시해서는 안 될 보안 측면

스트립되지 않은(unstripped) 바이너리(binary)에 대해 다시 이야기해야 합니다. 이것은 각주가 아니라 헤드라인(headline)이기 때문입니다.

*(사진을 보려면 엔터 키를 누르거나 클릭하세요)*
*(출처: JESHOOTS.COM, Unsplash)*

앤트로픽(Anthropic)은 완전한 디버그 심볼(debug symbols), 전체 Go 심볼 테이블(symbol table), 그리고 빌드 메타데이터(build metadata)에 포함된 비공개 모노레포(monorepo) 경로(github.com/anthropics/anthropic/api-go/environment-manager/)와 함께 '/usr/local/bin/environment-runner'를 배포했습니다. 이 바이너리(binary)는 클로드 코드 웹(Claude Code Web) 세션(session)을 실행하는 모든 사용자가 접근할 수 있었습니다.

보안 용어로 말하자면, 이는 마치 보안 시스템 배선이 명확하게 표시된 건축 설계도를 앞마당에 내버려 두는 것과 같습니다. 누구든지 와서 가져가서 읽어볼 수 있도록요. 심지어 집에서 가져온 돋보기까지 사용해서 말이죠.

### 약간의 위선

책임 있는 확장 정책을 발표하고, 세계 최고의 AI 안전 연구원들을 고용하며, "조건부 약속(if-then commitments)"과 평가 프레임워크(evaluation frameworks)에 대해 이야기하는 회사에게, 이것은 보안 운영(opsec) 실패이며 보안 팀을 매우 불편하게 만들어야 할 것입니다. 에이프릴니아(AprilNEA)가 악의적인 행동을 했기 때문이 아니라(그렇지 않았습니다; 이는 자신의 세션(session) 내에서 표준 도구를 실행한 것에 불과합니다), 다음에 파일을 파헤칠 사람이 다른 의도를 가질 수 있기 때문입니다.

'staging-' 버전 접두사(prefix)는 이것이 아직 내부/초기 단계 배포에 있음을 시사합니다. 하지만 이처럼 민감한 인프라(infrastructure)에 대해서는 '초기 단계'와 '모든 사용자 세션에 배포'라는 두 가지 설명이 결코 공존해서는 안 됩니다.

죄송하지만, "보안 우선 AI 개발"을 진지하게 받아들이는 모든 사람들에게 이 회사가 프로덕션(production) 환경에 바이너리(binary)를 푸시하기 전에 'strip' 명령을 실행하는 것조차 신경 쓰지 않는다는 것은 모욕적인 일입니다.

## 앤트로픽(Anthropic)의 2026년: 모델(model) 회사에서 플랫폼(platform) 회사로

시야를 넓혀 앤트로픽(Anthropic)의 궤적을 살펴봅시다.

클로드 코드 웹(Claude Code Web)은 2025년 10월에 출시되었고, 브라우저(browser)에서 접근 가능한 코드 실행 기능을 제공합니다. 클로드 코워크(Claude Cowork)는 2026년 1월에 출시되어 에이전트 패러다임(agent paradigm)을 비개발 지식 근로자에게까지 확장했습니다. 2024년 11월에 출시된 MCP는 이제 AI-도구 통합을 위한 사실상(de facto) 개방형 표준이 되었으며, OpenAI, 구글 딥마인드(Google DeepMind)에서 채택되었고 2025년 12월에는 리눅스 재단(Linux Foundation)에 기증되었습니다.

앤트스페이스(Antspace)는 이러한 흐름에 완벽하게 들어맞습니다.

배포 프로토콜(deployment protocol)의 성숙도를 볼 때, 앤트로픽(Anthropic)은 적어도 2025년 중반부터 플랫폼 계층을 구축해왔습니다. 아직 발표할 필요가 없었기 때문에 발표하지 않았을 뿐입니다. 그것은 이미 존재하고, 작동합니다. 공개할 준비가 되면, 그들은 클로드(Claude)에게 웹 앱(web app)을 만들어 달라고 요청했던 모든 사용자 기반을 갖춘 완전히 작동하는 PaaS를 보유하게 될 것입니다.

이것은 아마존(Amazon)의 전략과 같습니다. 먼저 자체적인 필요를 위해 인프라(infrastructure)를 구축합니다. 그것을 정말 좋게 만듭니다. 그리고 나서 다른 모든 사람들에게 개방합니다. AWS는 아마존(Amazon)의 내부 클라우드(cloud)로 시작했습니다. 앤트스페이스(Antspace)는 클로드(Claude)의 내부 배포 대상(deployment target)으로 시작하는 것입니다.

차이점은 무엇일까요?

아마존(Amazon)은 내부 인프라(infrastructure)에서 퍼블릭 클라우드(public cloud)로 전환하는 데 수년이 걸렸습니다. 앤트로픽(Anthropic)은 AI의 속도로, AI와 함께 구축하고 있습니다. 앤트스페이스(Antspace)가 퍼블릭(public) 제품이 될지 여부가 문제가 아니라, 언제 그렇게 될지가 문제입니다. 그리고 현재 이 분야에서 개발 중인 스타트업(startup)들이 모델(model), 런타임(runtime), 데이터베이스 통합(database integration) 및 호스팅 플랫폼(hosting platform)을 모두 소유한 회사와 경쟁할 충분한 자금(runway)을 가지고 있을지 여부입니다.

## 이것은 모두가 예측했지만, 아무도 이렇게 빨리 예상치 못했던 수직 통합 전략입니다.

여기 제 의견이 있습니다. 중립을 지키는 것은 지루하고 무익하기 때문에 솔직하게 말씀드리겠습니다.

앤트로픽(Anthropic)은 AI 기업들의 최종 목표(endgame)가 무엇인지 우연히 우리에게 보여주었습니다. 그것은 API 토큰(tokens)을 판매하는 것도, 모델(models)을 라이선스(licensing)하는 것도 아닙니다. 의도(intent)부터 프로덕션(production)까지 전체 스택(stack)을 소유하는 것입니다. 모델(model)은 인터페이스(interface)이고, 플랫폼(platform)은 제품이며, 호스팅(hosting)은 해자(moat)입니다.

OpenAI, 구글(Google), 그리고 모든 자금력 있는 스타트업(startup)을 포함한 모든 AI 기업들은 에이프릴니아(AprilNEA)가 발견한 것을 보고 이미 자신들이 뒤처져 있다는 것을 깨달을 것입니다. 앤트스페이스(Antspace)가 오늘날 버셀(Vercel)보다 낫기 때문이 아니라(아마 아닐 것입니다, 아직 스테이징(staging) 단계니까요), "인증 기능이 있는 투두 앱을 만들어줘"라는 말을 이해하는 신경망(neural network)부터 결과 앱을 호스팅하는 서버(server)까지, 스택(stack)의 모든 계층을 소유하는 구조적 이점은 빠르게 복리 효과를 내는 종류의 이점이기 때문입니다.

### 스타트업(Startup)과 개발자

AI 샌드박스(sandboxes), 배포 플랫폼(deployment platforms), AI 앱 빌더(AI app builders)를 구축하는 스타트업(startup)들에게는 시간이 촉박합니다. 당신의 제품이 나쁘기 때문이 아닙니다. 모델(model) 제공자가 당신의 전체 가치 제안(value proposition)을 채팅 인터페이스(chat interface) 내에서 무료로 제공되는 기능으로 흡수하고 있기 때문입니다.

### AI 코딩 플랫폼을 평가하는 기업들을 위해:

BYOC는 앤트로픽(Anthropic)이 당신의 인프라 계약(infrastructure contract)도 원한다는 것을 의미합니다. 그들은 당신이 자체 클라우드(cloud)에서 그것을 실행하도록 허용할 것입니다. 그들은 오케스트레이션(orchestration)을 관리할 것입니다. 당신은 앤트로픽(Anthropic) 서버(servers)로 코드를 보내지 않고도 클로드(Claude) 기반 개발의 이점을 얻을 수 있습니다. 이것은 영리한 전략입니다. 또한 락인(lock-in) 전략이기도 합니다.

이것은 제 관점입니다. 편한 대로 하시면 됩니다. 그리고 앤트로픽(Anthropic)에게: 다음번에는 바이너리(binary)를 스트립(strip)하는 것이 어떨까 싶습니다. 그냥 의견입니다.

이 지점까지 읽어주셔서 감사합니다! 당신은 영웅이자 (너드 ❤)입니다! 저는 독자들에게 "AI 세상에서 일어나는 흥미로운 일들"을 계속 알려드리고자 노력하니, 🔔 추천 | 팔로우 | 구독 🔔 부탁드립니다.

AI 에이전트(AI Agent) 풀스택 개발자(Full Stack Developer) LLM 클로드 코드(Claude Code) 인공지능(Artificial Intelligence)

* * *

**AI Advances**에 게시됨
팔로워 6.5만명 · 최종 발행 11시간 전
인공지능 접근의 민주화
**Mandar Karhade, MD. PhD.** 작성
팔로워 4.6천명 · 팔로잉 145명

생명 과학 AI/ML/GenAI 자문가
팔로우

**댓글 (8)**
*(댓글 내용 생략)*