# 번역 기사

- **원본 URL:** https://medium.com/@joe.njenga/i-just-tested-new-claude-code-routines-that-kill-your-boring-backlogs-4b0811b6f7a8
- **번역 일시:** 2026-04-18 11:25:00

---

# 클로드 코드 루틴 (Claude Code Routines): 지루한 백로그를 처리하는 자동화된 개발 작업

더 이상 지루하고 반복적인 개발 작업에 얽매일 필요가 없습니다. 한 번 설정해두면 잊어도 되는 새로운 방식이 있습니다.

적어도 새로 출시된 클로드 코드 루틴(Claude Code Routines)이 내세우는 약속은 그렇습니다. 저는 빠르게 테스트했고, 그 약속은 실현되었습니다.

오랫동안 클로드 코드(Claude Code)를 사용해 오셨다면, 소프트웨어 개발 주기 자동화에 탁월하다는 것을 이미 알고 계실 겁니다.

하지만 기존 자동화의 단점은 여전히 크론 작업(cron jobs), 인프라(infrastructure), MCP 서버(servers) 등을 직접 관리해야 한다는 점이었습니다.

클로드 코드 루틴은 이러한 문제를 해결하기 위해 설계되었습니다.

프롬프트(prompt), 저장소(repository), 커넥터(connectors)를 포함한 자동화 작업을 한 번 정의한 다음, 클로드에게 스케줄에 따라, API 호출(API call)을 통해, 또는 GitHub 이벤트(event)에 반응하여 언제 실행할지 알려주기만 하면 됩니다.

클로드 관리형 에이전트(Claude Managed Agents)와 마찬가지로, 이 루틴도 Anthropic이 관리하는 클라우드 인프라(cloud infrastructure)에서 실행되므로, 터미널(terminal)이 켜져 있든 꺼져 있든 계속 작동합니다.

현재 연구 프리뷰(research preview) 단계에 있으며, 저는 출시 이후 계속 테스트해왔습니다. 클로드 코드 루틴이 어떤 역할을 하는지 보여드리겠지만, 먼저 기본적인 작동 방식부터 설명해 드리겠습니다.

## 클로드 코드 루틴이란 무엇인가요?

루틴(routine)은 프롬프트, GitHub 저장소, 커넥터를 한 번에 패키징하여 실행되도록 예약된 저장된 클로드 코드 구성입니다.

이전에 설정했던 다른 자동화와 달리, 루틴은 Anthropic 관리형 클라우드 인프라에서 실행됩니다.

클로드가 작업을 실행하고 다음에 확인할 때 결과를 볼 수 있다는 점이 다릅니다.

모든 루틴에는 하나 이상의 트리거(trigger)가 있습니다:

*   **예약 (Scheduled)** — 설정한 주기에 따라 시간별, 야간별, 또는 주간별로 실행됩니다.
*   **API** — 고유한 엔드포인트(endpoint)로 무언가가 POST될 때 실행됩니다.
*   **GitHub 웹훅 (webhook)** — PR(Pull Request) 열기, 푸시(push), 이슈(issue) 등 저장소 이벤트에 반응합니다.

하나의 루틴에 여러 트리거를 중첩할 수 있습니다.

예를 들어, PR 검토 루틴은 스케줄에 따라 실행될 수 있고, 배포 파이프라인(deploy pipeline)에서 실행될 수도 있으며, 동일한 구성으로 모든 새 PR에 반응할 수도 있습니다.

클로드 코드 루틴은 완전히 자율적으로 실행됩니다. 즉, 실행 도중 승인 프롬프트가 필요하지 않습니다. 클로드는 제공된 저장소와 커넥터를 사용하여 프롬프트에 명시된 모든 것을 실행합니다.

따라서 프롬프트를 작성할 때 주의해야 하며, 루틴에 필요한 커넥터만 포함해야 합니다.

## 3가지 트리거 유형

루틴은 세 가지 방법으로 실행을 시작할 수 있도록 지원합니다. 하나만 사용하거나, 동일한 루틴에 세 가지 모두를 중첩하여 사용할 수 있습니다.

### 1) 예약 (Scheduled)

시간별, 일별, 주중별, 또는 주간별로 주기를 선택하면 클로드가 해당 스케줄에 따라 자동으로 실행됩니다.

시간은 현지 시간대(local timezone)로 설정되며 자동으로 변환됩니다. 한 가지 알아둘 점은, 분산(stagger)으로 인해 예약된 시간보다 몇 분 늦게 실행될 수 있지만, 각 루틴에 대해서는 일관성을 유지합니다.

두 시간마다 또는 매월 첫째 날과 같이 사용자 지정이 필요한 경우, UI에서 가장 가까운 사전 설정을 선택한 다음, CLI(Command Line Interface)에서 `/schedule update`를 실행하여 특정 크론 표현식(cron expression)을 설정할 수 있습니다. 최소 간격은 1시간입니다.

이것은 야간 백로그 분류(Nightly backlog triage), 주간 문서 불일치 확인(doc drift checks), 일일 PR 요약(PR summaries) 등 사용자가 신경 쓰지 않아도 자동으로 발생해야 하는 모든 작업에 가장 먼저 시도해야 할 트리거입니다.

### 2) API 트리거 (API trigger)

API 트리거가 있는 모든 루틴은 고유한 HTTP 엔드포인트(endpoint)와 베어러 토큰(bearer token)을 받습니다. 여기에 POST 요청을 보내면 클로드가 새 세션을 시작하고, 세션 URL(session URL)을 받게 됩니다.

요청 본문(request body)에는 선택적 `text` 필드가 있습니다. 여기에 넣는 모든 내용은 클로드의 프롬프트에 컨텍스트(context)로 추가됩니다.

경고 페이로드(alert payload), 스택 트레이스(stack trace), 또는 배포 로그(deploy log)를 전달할 수 있으며, 클로드는 이를 사용하여 맹목적으로 실행하는 대신 구체적인 응답을 제공합니다.

```bash
curl -X POST https://api.anthropic.com/v1/claude_code/routines/YOUR_ROUTINE_ID/fire \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "anthropic-beta: experimental-cc-routine-2026-04-01" \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{"text": "Check the latest 3 commits for any potential breaking changes."}'
```

성공적인 호출은 다음을 반환합니다:

```json
{
  "type": "routine_fire",
  "claude_code_session_id": "session_01HJKLMNOPQRSTUVWXYZ",
  "claude_code_session_url": "https://claude.ai/code/session_01HJKLMNOPQRSTUVWXYZ"
}
```

이것을 Datadog, 배포 파이프라인, 또는 HTTP 호출을 할 수 있는 모든 내부 도구에 연결할 수 있습니다. `/fire` 엔드포인트는 현재 `experimental-cc-routine-2026-04-01` 베타(beta) 헤더(header) 아래에 있으므로, API 구현은 향후 변경될 수 있습니다.

### 3) GitHub 웹훅 (Webhook)

클로드는 사용자의 저장소를 감시하며 일치하는 이벤트가 발생하면 즉시 새 세션을 시작합니다.

PR, 푸시, 이슈, 릴리즈(releases), 워크플로우 실행(workflow runs) 등 거의 모든 것에 구독할 수 있습니다.

특히 PR의 경우, 필터를 사용하여 더욱 세분화할 수 있습니다:

*   작성자(Author), 제목(title), 본문(body), 기준 브랜치(base branch), 헤드 브랜치(head branch)
*   라벨(Labels), 드래프트 상태(draft status), 포크 상태(fork status)

따라서 모든 PR에서 트리거하는 대신, 특정 작성자의 드래프트가 아닌 PR이 `main` 브랜치를 대상으로 하거나 특정 모듈에 영향을 미칠 때만 실행하도록 설정할 수 있습니다.

저장소에 클로드 GitHub 앱(App)이 설치되어 있어야 합니다. 설치되어 있지 않으면 트리거 설정 과정에서 안내해 줍니다.

## 클로드 코드 루틴 테스트하기

루틴을 처음부터 만들기 전에, Anthropic은 루틴 페이지에 8가지 기본 템플릿을 제공합니다.

스케줄과 필요한 커넥터가 사전 구성된 8가지 즉시 사용 가능한 루틴:

*   **브리핑 (Briefing)** — 캘린더, 이메일, 메시지 요약. Google Calendar, Gmail, Slack과 연동됩니다.
*   **이메일 분류 (Email triage)** — 받은 편지함을 분류하고 긴급한 항목에 대한 회신 초안을 작성합니다. Gmail과 연동됩니다.
*   **시스템 상태 확인 (System health check)** — 인프라의 오류, 중단, 성능 문제를 모니터링합니다. PagerDuty, Datadog, Sentry와 연동됩니다.
*   **이슈 분류 (Issue triage)** — 들어오는 이슈, 버그, 기능 요청을 검토하고 분류합니다. Linear와 연동됩니다.
*   **PR 검토 요약 (PR review digest)** — 열려 있는 PR, 검토 상태, 주의가 필요한 항목에 대한 개요를 제공합니다.
*   **종속성 업데이트 확인 (Dependency update check)** — 오래된 패키지, 보안 패치, 주요 변경 사항(breaking changes)을 스캔합니다.
*   **릴리즈 노트 초안 작성기 (Release notes drafter)** — PR이 `main` 브랜치에 병합될 때마다 릴리즈 노트 초안을 작성합니다. 풀 리퀘스트(pull request)가 닫힐 때 트리거됩니다.
*   **불안정한 테스트 추적기 (Flaky test tracker)** — 최근 CI(Continuous Integration) 실행에서 간헐적으로 통과 및 실패하는 테스트를 찾아냅니다.

실제 프롬프트가 어떻게 생겼는지 확인하기 위해 세 가지 템플릿을 열어보고 트리거를 생성했습니다.

### 1) 이메일 분류 (Email Triage)

Anthropic이 작성한 이 프롬프트는 연구해볼 가치가 있습니다:

```
새롭거나 읽지 않은 이메일이 있는지 받은 편지함을 검토합니다. 각 이메일에 대해:

1. 다음 중 하나로 분류합니다: 긴급 (Urgent), 조치 필요 (Action Needed), 참고용 (FYI), 낮은 우선순위 (Low Priority)
2. 핵심 내용을 한 문장으로 요약합니다.
3. 긴급 또는 조치 필요로 표시된 이메일에 대한 회신 초안을 작성합니다.
결과를 카테고리별로 그룹화하고, 긴급 항목을 먼저 제시합니다. 작성된 회신은 전문적이고 간결한 어조를 유지합니다.
받은 편지함이 비어 있거나 모든 처리가 완료된 경우, 간략하게 확인 메시지를 보냅니다.
```

이는 주중 오후 6시에 Gmail과 연결되어 실행됩니다.

### 2) PR 검토 요약 (PR Review Digest)

기본 프롬프트는 다음과 같습니다:

```
연결된 저장소의 모든 열려 있는 Pull Request를 검토합니다. 각 PR에 대해 다음을 보고합니다:
1. 제목과 작성자
2. 열려 있던 기간
3. 검토 상태 - 승인됨, 변경 요청됨, 또는 검토 대기 중
4. CI 상태 - 통과, 실패, 또는 보류 중
5. 병합 충돌 - 브랜치가 최신 상태인지 여부
3일 이상 열려 있거나 실패한 검사가 있는 PR을 강조 표시합니다. 가장 오래된 순서대로 정렬합니다.
열려 있는 PR이 없는 경우, 간략하게 확인 메시지를 보냅니다.
```

PR당 5가지 데이터 포인트를 연령순으로 정렬하고, 오래되거나 문제가 있는 항목은 플래그(flag)를 지정합니다. 주중 오후 9시에 실행되므로, 하루 업무를 마칠 때쯤 요약본이 준비되어 있습니다.

### 3) 종속성 업데이트 확인 (Dependency Update Check)

이 루틴을 더욱 효과적으로 만들기 위해 Context7 MCP를 추가했습니다.

프롬프트는 다음과 같습니다:

```
연결된 저장소의 모든 종속성에 대해 사용 가능한 업데이트를 확인합니다. 각 오래된 종속성에 대해:

1. 현재 버전 대 최신 버전
2. 업데이트 유형 - 주요 (major), 부 (minor), 또는 패치 (patch)
3. 변경 로그에 명시된 주요 변경 사항 (breaking changes)
4. 보안 권고 - 알려진 CVE(Common Vulnerabilities and Exposures) 또는 취약점
보안 관련 업데이트를 우선시합니다. 결과를 저장소와 심각도별로 그룹화합니다.
모든 것이 최신 상태인 경우, 간략하게 확인 메시지를 보냅니다.
```

종속성당 4가지 데이터 포인트: 버전 차이, 업데이트 심각도, 변경 로그 노트, CVE입니다. 보안 업데이트가 먼저 표시됩니다. 매주 월요일 오후 9시 30분에 실행됩니다.

## 3가지 사용자 지정 사용 사례

### 1) 야간 백로그 분류 (Nightly Backlog Triage) (사용자 지정)

템플릿은 좋은 시작점이지만, 제가 처음부터 만든 예시를 소개합니다.

매주 평일 밤, 클로드는 지난 24시간 동안 열린 이슈를 확인하고, 라벨을 적용하고, 언급된 모듈을 기반으로 담당자를 할당한 다음, Slack에 요약을 게시합니다.

이것을 사용하면 팀은 다음과 같은 큐(queue)로 스탠드업(standup)을 시작할 수 있습니다:

```
매주 평일 밤, 지난 24시간 동안 열린 모든 이슈를 확인합니다.
각 이슈에 대해:
- 제목과 설명에 따라 올바른 라벨(버그, 기능, 문서, 질문)을 적용합니다.
- 모듈이나 영역이 언급된 경우 관련 팀원에게 할당합니다.
- 각 이슈 제목과 수행된 작업을 포함하여 Slack #dev-standup 채널에 글머리 기호 요약을 게시합니다.
```

GitHub 및 Slack 커넥터가 모두 필요합니다.

### 2) PR 코드 검토 (PR code Review) (사용자 지정)

이 루틴은 새로운 PR을 감시하고, 팀의 체크리스트를 실행하며, 사람이 검토하기 전에 인라인(inline) 댓글을 남깁니다.

검토자들은 설계 결정에 집중할 수 있습니다:

```
main 브랜치에 대해 새로운 PR이 열리면, 다음 체크리스트를 사용하여 검토합니다:
1. 하드코딩된 비밀 키나 API 키가 있나요?
2. 새로운 함수에 단위 테스트가 누락되었나요?
3. 모든 비동기 호출에 오류 처리(error handling)가 구현되어 있나요?
발견된 각 문제에 대해 인라인 댓글을 남깁니다.
PR 상단에 요약 댓글을 추가합니다.
```

필터를 `Is draft = false` 및 `Base branch = main`으로 설정하여 클로드가 WIP(작업 중) 브랜치가 아닌 준비된 PR에만 실행되도록 합니다.

참고: 클로드는 기본적으로 `claude/-` 접두사가 붙은 브랜치에 푸시(push)합니다. 명시적으로 허용하지 않는 한 보호된 브랜치(protected branches)에는 접근하지 않습니다. 저는 이것을 제 관행으로 유지합니다.

### 3) 배포 검증 (Deploy Verification) (사용자 지정)

이것은 API 트리거를 CD 파이프라인(CD pipeline)과 연결합니다.

각 프로덕션 배포(production deploy) 후, 파이프라인은 텍스트 필드에 빌드 컨텍스트(build context)를 포함하여 루틴의 엔드포인트로 POST 요청을 보냅니다.

클로드는 오류 로그를 스캔하고 스모크 테스트(smoke checks)를 실행하며, 릴리즈 채널(release channel)에 승인/불가(go/no-go) 결과를 게시합니다.

```bash
curl -X POST https://api.anthropic.com/v1/claude_code/routines/YOUR_ROUTINE_ID/fire \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "anthropic-beta: experimental-cc-routine-2026-04-01" \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{"text": "Production deploy completed for build #4821. Check error logs and run smoke checks against the staging endpoint."}'
```

여기서 `text` 필드는 많은 역할을 수행합니다. 이것이 없으면 클로드는 루틴 프롬프트를 맹목적으로 실행합니다.

## 최종 의견

클로드 코드 루틴은 그 약속을 이행합니다.

한 번 설정하고 언제 실행할지 알려주면, 워크플로우(workflow)의 지루한 부분을 처리해 줍니다.

기본 템플릿은 좋은 진입점이지만, 사용자 지정 프롬프트를 작성하고 API 트리거를 통해 루틴을 기존 도구에 연결하면 더 많은 유연성을 얻을 수 있습니다.

가격 책정에 있어서는 한계를 현실적으로 인식해야 합니다. 몇 개의 루틴이 실행되면 프로(Pro) 요금제의 하루 5회 실행은 예상보다 빠르게 토큰(tokens)을 소진할 것입니다. 추가 사용 옵션이 도움이 되지만, 루틴이 자주 실행될수록 비용은 증가합니다.