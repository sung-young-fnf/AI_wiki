# 번역 기사

- **원본 URL:** https://medium.com/ai-software-engineer/anthropic-launches-claude-managed-agents-that-make-agentic-ai-workflows-real-91134b6f2b56
- **번역 일시:** 2026-04-12 17:41:04

---

# Anthropic, 에이전트 기반 AI 워크플로우를 실현하는 'Claude Managed Agents' 출시

Claude Managed Agents를 사용하면 에이전트를 작동시키기 위해 여러 스크립트를 복잡하게 연결하느라 시간을 낭비할 필요가 없습니다. 최근 출시된 Claude Managed Agents를 테스트해 본 결과, AI 에이전트 워크플로우(workflow)에서 그동안 부족했던 핵심 연결 고리를 발견할 수 있었습니다.

AI 에이전트를 구축하거나 활용해 본 경험이 있다면 공통적인 문제에 직면했을 것입니다. 제품의 핵심 로직을 개발하는 시간보다 에이전트의 인프라를 구축하는 데 더 많은 시간을 소비하게 된다는 점입니다. 에이전트 루프(agent loop), 도구 실행(tool execution), 컨텍스트 관리(context management), 샌드박싱(sandboxing), 세션 처리(session handling) 등을 실제 로직을 작성하기도 전에 처음부터 직접 구현해야 합니다. 작업 도중 오류가 발생하면 제품 출시 대신 인프라 디버깅에 매달리게 됩니다.

Claude Managed Agents는 바로 이 문제를 해결하기 위해 등장했습니다. Anthropic은 단순히 루프를 감싸기 위한 또 다른 API를 제공하는 것에 그치지 않고, 이제 런타나임(runtime) 자체를 제공합니다. 이는 Claude가 파일을 읽고, 명령을 실행하며, 웹을 탐색하고, 스스로 코드를 실행할 수 있는 완전 관리형 클라우드 환경입니다. 세션 연속성(session continuity)과 컨텍스트 관리도 이미 처리되어 있습니다.

Anthropic은 이번에 두 가지를 선보였습니다.

1.  **Claude Managed Agents**: Anthropic의 인프라에서 실행되는 완전 호스팅형 에이전트 하네스(agent harness)입니다. 에이전트를 정의하고 작업을 실행하기만 하면, Anthropic의 클라우드가 컨테이너, 도구, 실행, 세션 등 모든 것을 처리합니다.
2.  **Claude Agent SDK** (구 Claude Code SDK): Claude Code를 구동하는 것과 동일한 에이전트 루프를 자체 애플리케이션에 내장할 수 있는 Python 및 TypeScript 라이브러리입니다. 사용자가 직접 호스팅해야 하지만, 까다로운 기능들은 이미 구현되어 있습니다.

두 방식 모두 동일한 문제를 해결하지만, 차이점은 에이전트가 실행되는 위치에 있습니다.

*   **SDK**: 에이전트가 사용자의 자체 인프라에서 실행됩니다.
*   **Managed Agents**: 에이전트가 Anthropic의 클라우드에서 실행되며, 모든 것이 관리됩니다.

개발자들에게 더 의미 있는 업데이트는 Managed Agents이며, 본문에서는 이를 중심으로 살펴보겠습니다.

### 4가지 핵심 구성 요소

Managed Agents는 네 가지 개념을 중심으로 구축되었습니다. 이 구조를 이해하면 시스템 전체를 파악할 수 있습니다.

*   **에이전트(Agent)**: 모델, 시스템 프롬프트, 도구, MCP 서버, 스킬 등을 포함한 설정값입니다. 한 번 정의하면 모든 세션에서 ID로 참조할 수 있습니다.
*   로직을 실행할 **환경(Environment)**: Python, Node.js, Go 등이 사전 설치된 클라우드 컨테이너로, 네트워크 액세스 규칙과 마운트된 파일이 포함된 작업 공간입니다.
*   **세션(Session)**: 특정 작업을 실행하기 위해 환경 내에서 실행되는 에이전트의 인스턴스입니다. 파일, 대화 기록, 컨텍스트가 세션 수명 동안 유지됩니다.
*   **이벤트(Events)**: 애플리케이션과 에이전트 사이를 흐르는 메시지입니다. 사용자 입력, 도구 결과, 상태 업데이트, 스트리밍 응답 등이 포함됩니다.

코드 예시는 다음과 같습니다. 에이전트를 한 번 생성합니다.

```python
import anthropic

client = anthropic.Anthropic()

# 에이전트 생성 — 한 번만 수행하고 ID로 재사용
agent = client.beta.agents.create(
    model="claude-sonnet-4-6",
    system="You are a software engineer. Fix bugs, run tests, and report results.",
    tools=[{"type": "bash"}, {"type": "file_operations"}, {"type": "web_search"}],
    name="bug-fixer"
)

print(agent.id)  # 이 ID를 저장하여 세션에서 참조합니다.
```

그 다음, 세션을 시작하고 작업을 전달합니다.

```python
# 에이전트에 필요한 패키지가 포함된 환경 생성
environment = client.beta.environments.create(
    packages=["pytest", "requests"]
)

# 에이전트와 환경을 참조하여 세션 시작
session = client.beta.sessions.create(
    agent_id=agent.id,
    environment_id=environment.id
)

# 작업을 이벤트로 전달
response = client.beta.sessions.events.create(
    session_id=session.id,
    content="Find and fix the failing tests in auth.py"
)
```

이 설정만으로 에이전트 정의, 환경, 세션이 모두 준비되며, 이후의 작업은 Claude가 알아서 처리합니다.

### 에이전트 루프(Agent Loop)의 작동 방식

세션을 시작하면 Claude는 한 번 응답하고 멈추는 것이 아니라, 작업이 완료될 때까지 다음과 같은 연속적인 루프를 실행합니다: **프롬프트 수신 $\rightarrow$ 작업 평가 $\rightarrow$ 도구 호출 $\rightarrow$ 결과 처리 $\rightarrow$ 반복**. 각 전체 과정을 하나의 '턴(turn)'이라고 합니다.

예를 들어, "auth.py의 실패하는 테스트를 찾아 수정하라"는 작업을 수행할 때의 과정은 다음과 같습니다.

*   **1턴**: Claude가 Bash를 통해 테스트 스위트를 실행합니다. 3개의 실패 결과가 반환됩니다.
*   **2턴**: Claude가 문제를 파악하기 위해 `auth.py` 파일을 읽습니다.
*   **3턴**: Claude가 파일을 수정하고 테스트를 다시 실행합니다. 모든 테스트가 통과합니다.
*   **최종 턴**: 도구 호출 없이 텍ext 응답을 반환하며 작업을 완료합니다.

실시간으로 진행 상황을 확인하는 코드는 다음과 같습니다.

```python
import anthropic

client = anthropic.Anthropic()

async for event in client.beta.sessions.stream(
    session_id=session.id,
    event={
        "type": "user",
        "content": "Find and fix the failing tests in auth.py"
    }
):
    # Claude가 각 턴에서 수행하는 작업을 확인
    if event.type == "assistant_message":
        print(event.content)

    # 최종 결과 캡처
    if event.type == "result":
        print(f"Done: {event.result}")
        print(f"Turns used: {event.num_turns}")
        print(f"Cost: ${event.total_cost_usd:.4f}")
        session_id = event.session_id  # 나중에 재개하기 위해 저장
```

이 스트림을 통해 모든 도구 호출, 결과, 턴의 진행 상황을 실시간으로 모니터링할 수 있습니다.

### 컨텍스트 관리(Context Management)

직접 에이전트를 구축할 때 가장 자주 발생하는 문제는 컨텍스트(context)입니다. 매 턴마다 프롬프트, 도구 정의, 파일 읽기 결과, 명령 출력, 대화 기록이 컨텍스트 창(context window)에 쌓이게 됩니다. 한계치에 도달하면 세션이 중단됩니다.

Managed Agents는 이를 자동으로 처리합니다. 컨텍스트 창이 한계에 가까워지면, 시스템은 최근의 대화와 주요 결정 사항은 유지하면서 오래된 기록을 요약하여 공간을 확보하는 압축(compaction) 과정을 수행합니다. 덕분에 세션 중단 없이 작업을 계속할 수 있습니다.

### 세 가지 제어 기능

1.  **노력 수준(Effort level)**: 각 턴에서 Claude가 얼마나 깊게 추론할지를 결정합니다. 토큰 사용량과 비용에 영향을 미치므로 신중하게 설정해야 합니다.
2.  **최대 턴 및 예산(Max turns and budget)**: 루프가 무한히 실행되는 것을 방지하기 위해 상한선을 설정합니다.
    ```python
    session = client.beta.sessions.create(
        agent_id=agent.id,
        environment_id=environment.id,
        max_turns=20,           # 20번의 도구 사용 턴 후 중단
        max_budget_usd=0.50     # 또는 비용이 $0.50에 도달하면 중단
    )
    ```
3.  **권한 모드(Permission mode)**: Claude가 사용자 확인 없이 수행할 수 있는 범위를 제어합니다.
    *   `default`: 사전 승인되지 않은 작업은 콜백을 통해 승인을 요청합니다.
    *   `acceptEdits`: 파일 수정은 자동 승인하지만, 셸 명령은 여전히 승인이 필요합니다.
    *   `bypassPermissions`: 격리된 컨테이너나 CI 환경에서 사용하며, 모든 작업을 확인 없이 실행합니다.

### 작업 중 제어 가능

세션이 실행 중인 상태에서도 추가 이벤트를 보내 작업을 재지시하거나, 컨텍텍스트를 추가하거나, 중단할 수 있습니다.

```python
# 세션 중간에 수정 사항 전달
client.beta.sessions.events.create(
    session_id=session.id,
    content="Actually, only fix the auth_token tests — leave the login tests alone"
)
```

Claude가 잘못된 방향으로 가고 있다면 작업이 끝날 때까지 기다릴 필요가 없습니다. 또한 세션은 지속되므로, 세션 ID를 저장해 두었다가 나중에 중단된 지점부터 재개할 수도 있습니다.

### Claude Managed Agent 도구 세트

에이전트를 수동으로 구축할 때 가장 번거로운 작업 중 하나가 도구 실행(tool execution)입니다. 래퍼(wrapper)를 작성하고, 에러를 처리하며, 결과를 다시 컨텍스트로 가져오는 작업이 Managed Agents에서는 이미 완료되어 있습니다.

**주요 도구 목록:**

*   **파일 작업(File operations)**: 읽기(Read), 생성(Write), 수정(Edit)
*   **검색(Search)**: 패턴 기반 파일 찾기(Glob), 정규표록 기반 내용 검색(Grep)
*   **실행(Execution)**: 셸 명령, 스크립트, Git 작업, 패키지 설치, 테스트 실행(Bash)
*   **웹(Web)**: 인터넷 검색(WebSearch), URL 페이지 내용 가져오기(WebFetch)
*   **오케스트레이션(Orchestration)**: 하위 에이전트 생성(Task), 재사용 가능한 워크플로우 호출(Skill), 사용자 질문 요청(AskUserQuestion), 작업 추적(TodoWrite)

기본 도구 외에도 MCP(Model Context Protocol) 서버를 통해 외부 서비스를 연결하거나, 사용자 정의 도구를 정의하여 사용할 수 있습니다.

### 시작하기

Claude Managed Agents는 현재 베타 버전입니다. 모든 API 계정에서 기본적으로 사용 가능하며, Claude API 키가 있다면 바로 시작할 수 있습니다.

**필요 사항:**
*   `platform.claude.com`에서 발급받은 Claude API 키
*   모든 요청에 `managed-agents-2026-04-01` 베타 헤더 포함 (SDK 사용 시 자동 설정됨)
*   Python 또는 TypeScript SDK

**설치 및 실행 예시:**

```bash
pip install anthropic
export ANTHROPIC_API_KEY=your-api-key
```

단 20줄 미만의 코드로 첫 에이전트를 구동할 수 있습니다.

```python
import anthropic
import asyncio

client = anthropic.Anthropic()

async def main():
    agent = client.beta.agents.create(
        model="claulate-sonnet-4-6",
        system="You are a helpful engineering assistant.",
        tools=[{"type": "bash"}, {"type": "file_operations"}],
        name="my-first-agent"
    )

    environment = client.beta.environments.create(
        packages=["pytest"]
    )

    session = client.beta.sessions.create(
        agent_id=agent.id,
        environment_id=environment.id,
        max_turns=10,
        max_budget_usd=0.25
    )

    async for event in client.beta.sessions.stream(
        session_id=session.id,
        event={"type": "user", "content": "List all Python files in this directory"}
    ):
        if event.type == "result":
            print(event.result)
            print(f"Cost: ${event.total_cost_usd:.4f}")

asyncio.run(main())
```

### 마치며

Claude Managed Agents는 에이전트 루프, 컨텍스트 관리, 도구 실행, 세션 연속성 측면에서 매우 탁월한 아이디어입니다. 과거에는 몇 주가 걸리던 인프라 구축 작업이 이제는 설정 파일 하나와 API 호출 한 번으로 가능해졌습니다. 이는 상용 수준의 에이전트를 빠르게 구축하는 데 있어 엄청난 진전입니다.

물론, 출력물의 품질은 여전히 입력값의 품질에 달려 있다는 트레이드오프(trade-off)는 존재합니다.