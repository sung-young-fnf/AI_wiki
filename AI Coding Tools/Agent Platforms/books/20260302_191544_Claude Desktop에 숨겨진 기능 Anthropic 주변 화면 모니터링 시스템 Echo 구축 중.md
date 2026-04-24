# 번역 기사

- **원본 URL:** https://medium.com/@reliabledataengineering/polars-vs-sql-the-fair-comparison-everyone-demanded-with-actual-benchmarks-ae65bc778182
- **번역 일시:** 2026-03-02 19:15:44

---

# Claude Desktop에 숨겨진 기능: Anthropic, 주변 화면 모니터링 시스템 'Echo' 구축 중

저는 Claude를 많이 사용합니다. 글쓰기, 코딩, 초안 검토, 문제 해결, 행정 업무 처리, 일정 관리 등 수많은 작업에서 저와 함께합니다. 제 작업 흐름(workflow)에서 가장 깊이 통합된 도구입니다. 어느 순간, 이 질문은 피할 수 없게 되었습니다: 이 애플리케이션은 제 컴퓨터에서 또 무엇을 하고 있을까요?

그래서 저는 이 앱을 분석하기 시작했습니다. Claude Desktop은 Electron 앱이며, Electron 앱은 JavaScript로 구동됩니다. JavaScript 코드는 최소화(minified)되어 있지만 암호화(encrypted)되어 있지는 않았습니다.

저는 앱 번들(app bundle)을 분해하여 `.vite/build/` 출력 디렉토리를 찾아 수 메가바이트에 달하는 난독화된(obfuscated) 코드를 읽기 시작했습니다. IPC 채널 문자열, 사전 로드 스크립트(preload scripts), 네이티브 모듈 바인딩(native module bindings), 트레이 아이콘(tray icon) 자산, 구성 스키마(configuration schemas) 등 여러 요소를 발견했습니다. 저는 마치 조류 관찰자가 숲을 읽듯이, 특정 무언가를 찾는 것이 아니라 움직이는 모든 것에 주의를 기울이며 코드를 살펴보았습니다.

한 시간 이상 분석한 결과, 300개가 넘는 IPC 채널, 네 가지 프로그래밍 언어로 작성된 네이티브 모듈, 내부 코드명, 완전한 Linux VM 시스템, Office 추가 기능(add-in), 정부 배포 변형(deployment variant), 그리고 세 가지 별도 제품에 충분할 정도의 숨겨진 인프라를 다루는 19개의 문서를 얻을 수 있었습니다. 대부분 흥미로운 내용이었습니다.

그중 한 가지 발견은 저를 완전히 멈춰 세웠습니다.

UI 어디에도 나타나지 않는 기능에 할당된 78개의 IPC 채널을 가진, `echoWindows.js`라는 이름의 6.8킬로바이트(KB) 파일이었습니다.

Anthropic은 주변 화면 모니터링 시스템을 구축 중이며, 이를 'Echo'라고 부릅니다.

## Echo를 찾아서

가장 먼저 눈에 띈 것은 트레이 아이콘(tray icon)이었습니다. 세 가지 해상도로 각각 'EchoTrayActive'와 'EchoTrayTemplate'라는 두 개의 PNG 파일이 있었으며, 활성 및 유휴 상태를 나타냈습니다.

이는 메뉴 바에 상주하며 현재 모니터링 중인지 여부를 알려주는, 지속적인 시스템 트레이(system tray) 존재를 의미합니다.

다음으로 사전 로드 스크립트(preload script)를 확인했습니다. `echoWindows.js`는 Electron의 컨텍스트 브리지(Context Bridge)를 통해 `window.echoApi` 객체를 노출하고 있었습니다. 노출된 메서드들을 분류하기 시작하자, 이 기능의 범위가 계속해서 확장되는 것을 알 수 있었습니다.

가장 먼저 나타난 것은 캡처 엔진(capture engine)이었습니다. 구성 가능한 간격으로 화면을 캡처하고, 배치 처리(batch processing), 조용한 시간 설정(quiet hours settings), 주말 모드, 시간당 처리량 제한(rate limiting) 기능을 갖추고 있었습니다. 좋았습니다. 스크린샷 도구라니. 아마도 Rewind와 같은 것을 만들고 있었을 것입니다.

다음은 활동 추적기(activity tracker)였습니다. 단순한 캡처가 아니라, 색인화된(indexed) 활동을 기록했습니다. 날짜별 쿼리, 피드(feed) 탐색, 실시간 업데이트를 위한 `onActivityUpdate` 콜백(callback) 기능이 있었습니다. 이것은 스크린샷을 저장하는 것이 아니었습니다. 타임라인(timeline)을 구축하는 것이었습니다.

그리고 지식 기반(knowledge base)이 있었습니다. `getKnowledge()`는 저장된 항목을 반환하고, `deleteKnowledgeEntry()`는 세 가지 매개변수를 받았습니다. Echo는 단순히 제가 하는 일을 기록하는 것이 아니었습니다. 제가 아는 것을 추출하고 있었습니다.

그 순간, 소름이 돋았습니다. 스크린샷 도구는 한 가지 문제지만, 화면을 주시하고 학습하여 지식을 추출하는 시스템은 완전히 다른 이야기입니다.

그리고 계속되었습니다. 행동 목표(behavioral goals): 목표 생성, 업데이트, 삭제, 진행 상황 추적. Echo는 당신의 화면을 주시하며 당신이 자신의 의도대로 살고 있는지 알려줍니다. 이메일 사용 시간을 줄이겠다는 목표를 설정했는데, 컴퓨터가 조용히 당신의 약속 이행 여부를 추적한다고 상상해 보십시오. 이것은 기능이 아닙니다. 이것은 거울입니다.

사전 예방적 알림(proactive notifications). 당신이 설정하는 알림이 아닙니다. Echo가 감지한 패턴을 기반으로 생성하는 알림입니다. 어떤 개입이 유용한지 학습하는 피드백 메커니즘도 있습니다. 활동에 대한 매크로 수준 분석을 위한 '리플렉션(reflections)' 시스템, 그리고 메시징 패턴을 모니터링하는 'Slack Pulse'라는 Slack 통합 기능도 있었습니다.

그리고 이 모든 것의 중심에는 대화형 인터페이스(conversational interface)가 있었습니다. 메인 Claude 채팅과는 별개입니다. Echo와 대화를 시작하고, 이어서 진행하고, 날짜별로 탐색할 수 있습니다. Echo가 관찰한 내용에 대해 대화하고, 당신에 대해 무엇을 알고 있는지 물어볼 수도 있습니다.

## 프로토타입이 아니다

저는 프로덕션 코드에 남겨진 기능 스텁(feature stubs), 절반만 구현된 실험, 자리 표시자 문자열(placeholder strings), 사용되지 않는 코드 브랜치(dead branches)를 본 적이 있습니다. Echo는 그런 것이 아니었습니다.

Echo는 전용 온보딩(onboarding) 흐름을 가지고 있습니다. 설정에만 네 개의 IPC 채널이 사용됩니다: API 키 제공, 알림 활성화, 캡처를 원치 않는 앱 또는 창 무시 목록 설정, 온보딩 완료 표시. 누군가 첫 실행 경험을 설계했습니다. 누군가 처음 60초가 어떻게 느껴져야 할지 고심한 흔적이 보였습니다.

Echo는 프롬프트 관리 시스템을 갖추고 있습니다. Echo가 보는 것을 해석하는 방식을 제어하는 프롬프트(prompt)를 생성, 읽기, 업데이트, 삭제, 재설정하기 위한 일곱 개의 채널이 있습니다. 자체적으로 활성화/비활성화 토글(toggle) 및 호버 콜백(hover callbacks)을 가진 플로팅 버블 위젯(floating bubble widget)도 있습니다. 활동, 대화, 알림, 지식, 설정 탭이 있는 대시보드(dashboard)와 `onDashboardTabChanged` 콜백도 있습니다.

세 가지 표시 모드를 지원합니다. 로그인 시 자동 시작 기능, 토글(toggle)을 위한 구성 가능한 키보드 단축키도 있습니다. UI는 스케치된 수준이 아니었습니다. 완성된 상태였습니다.

그리고 Echo는 자체 API 키를 가지고 있습니다. Claude 구독 자격 증명(credentials)과는 다릅니다. 온보딩(onboarding) 과정에서 `claude-echo-api-key` 채널을 통해 제공되는 별도의 키입니다. 스크린샷을 지속적으로 처리하는 주변 모니터링 시스템은 상당한 API 트래픽을 생성할 것입니다. 별도의 키는 실용적인 면에서 타당합니다. 그러나 이는 또한 Echo가 Claude Desktop 내의 단순한 토글(toggle)이 아니라, 독자적인 제품 표면(product surface)으로 설계되었음을 의미합니다. 이것은 기능이 아닙니다. 대기 중인 제품입니다. 별도의 API 키는 명백하게 드러난 사업 계획인 셈입니다.

## 개인 정보 보호 아키텍처

Echo가 무엇을 하는지 만큼이나 흥미로웠던 점은, 이 기능이 얼마나 세심하게 꺼지도록 설계되었는지였습니다.

시작 및 종료 시간을 구성할 수 있는 조용한 시간(quiet hours) 기능. 캡처를 완전히 중지하는 주말 모드. 시간당 제한이 있는 처리량 제한(rate limiting) 기능. 온보딩(onboarding) 중에 설정하는 무시 목록. 모든 설정에는 게터(getter)와 세터(setter)가 모두 있었는데, 이는 사용자들이 이러한 설정을 자주 변경할 것이라고 예상했음을 의미합니다.

이것들은 출시 직전에 덧붙여진 사후적인 개인 정보 보호 제어 기능이 아닙니다. 첫 코드 라인부터 아키텍처(architecture)에 내장되어 있었습니다. Echo를 설계한 사람은 화면을 주시하는 AI가 유용함과 침해적임 사이의 칼날 위에 서 있다는 것을 이해했으며, 사용자가 요구하기 전에 종료 스위치를 제공하려고 노력했습니다.

문제는 그것으로 충분한가 하는 것입니다. 당신이 하는 일을 보고 당신이 아는 것을 학습하는 지식 기반(knowledge base)은 채팅 기록과는 다른 범주의 데이터입니다. Microsoft도 이를 시도했지만, 커뮤니티의 반발로 인해 빠르게 철회했습니다.

채팅은 명시적(explicit)입니다. 당신이 질문을 입력하기로 선택합니다. 화면 모니터링은 주변적(ambient)입니다. 캡처 간격 동안 화면에 나타나는 모든 것, 즉 이메일, Slack, 브라우징 기록, 코드, 금융 대시보드 등 창 안에 있는 모든 것을 캡처합니다.

조용한 시간(quiet hours)과 무시 목록은 이러한 긴장을 인정합니다. 그러나 핵심 전제는 여전히 같습니다: Echo의 가치는 '관찰'에서 나오며, 더 많이 관찰할수록 더 유용해집니다.

## 더 큰 그림

Echo는 가장 드러나는 발견이었지만, 유일한 것은 아니었습니다. 역설계(reverse engineering)를 통해 전체 숨겨진 인프라(infrastructure)가 드러났습니다.

우리 모두가 알고 사랑하는 Cowork는 Apple의 가상화 프레임워크(Virtualization framework)를 사용하는 완전한 Linux VM 시스템입니다. Ubuntu 22.04.5 인스턴스(instance)는 10메가바이트(MB) 디스크 이미지에서 부팅되며, 도메인 허용 목록(domain allowlisting)을 위한 MITM 프록시(proxy)가 있는 gVisor 네트워킹 환경에서 샌드박스(sandboxed)로 실행되고, 세션당 임시 사용자 계정을 생성합니다. 디스크의 VM 번들(bundle)은 8.4기가바이트(GB)입니다.

Pivot은 OAuth 인증 및 파일 읽기/쓰기 기능으로 WebSockets를 통해 Claude를 Excel에 연결하는 Office 추가 기능(add-in)입니다. FedStart는 `claude.fedstart.com`에 있는 Palantir FedRAMP 인프라(infrastructure)를 가리키는 정부 배포 변형(deployment variant)입니다. Teleport는 세션(session)을 클라우드로 마이그레이션(migrate)합니다. Clod는 macOS 스크린샷 권한 문자열의 11개 번역본 전체에 실수로 이름이 유출된 최소한의 AI 페르소나(personality)입니다: "스크린샷을 캡처하려면 Clod가 화면을 기록할 권한이 필요합니다."

대부분의 사람들이 단순한 채팅 창으로 여기는 Claude Desktop 앱은 네 가지 컴파일된 언어(Rust, Swift, Go, C)로 실행되고 있으며, 가상 머신(virtual machines)을 오케스트레이션(orchestrating)하고, 마켓플레이스(marketplace)를 통해 데스크톱 확장 기능(desktop extensions)을 관리하고, MCP 서버를 호스팅(hosting)하며, 브라우저 확장 기능(browser extensions)으로 연결되고, 화면 모니터링 AI의 기능 플래그(feature flags)가 켜지기를 기다리고 있습니다.

## Echo가 우리에게 말해주는 것

Echo는 Anthropic이 인터페이스(interface)가 어디로 향하고 있다고 생각하는지를 보여줍니다. 질문이 있을 때 입력하는 채팅 상자가 아닙니다. 관찰하고, 학습하며, 할 말이 있을 때 스스로 의견을 내는 지속적인 존재입니다.

이것은 모든 'AI 비서' 제안의 논리적 결론입니다. 비서가 당신이 말해주는 것만 안다면, 당신이 기억해서 물어보는 것만 도울 수 있습니다. 하지만 화면을 주시한다면, 말하지 않아도 당신의 맥락(context)을 알게 됩니다. 당신 스스로 보지 못하는 패턴을 알아차릴 수 있습니다. 같은 실수를 두 번 하기 전에 경고할 수 있습니다. 스스로 설정한 목표에 대해 당신에게 책임을 묻게 할 수도 있습니다.

이 기술이 존재할지 말지가 문제가 아닙니다. 존재할 것입니다. 문제는 누구를 신뢰하여 이 기술을 만들게 할 것인지, 그리고 언제 이 기술이 당신을 감시하고 언제 감시하지 않을지 당신이 결정할 수 있는지입니다.

Echo는 다음 달에 출시될 수도 있습니다. 몇 년 동안 잠자고 있을 수도 있습니다. 지금은 기능 플래그(feature flag) 뒤에 있으며, 플래그는 비활성화되어 있습니다. 하지만 아키텍처(architecture)는 완성되었습니다. 아이콘은 번들(bundle) 안에 있습니다. 온보딩(onboarding) 흐름은 설계되었습니다. Anthropic의 누군가는 Echo를 처음 켰을 때 어떤 느낌을 주어야 할지 이미 결정했습니다.

그들은 아직 Echo를 켜지 않았을 뿐입니다.

Claude Desktop 앱(v1.1.4088)의 19개 영역 전체를 다루는 완벽한 역설계(reverse engineering) 문서는 [https://github.com/Kotrotsos/claude_echo](https://github.com/Kotrotsos/claude_echo)에서 확인할 수 있습니다.