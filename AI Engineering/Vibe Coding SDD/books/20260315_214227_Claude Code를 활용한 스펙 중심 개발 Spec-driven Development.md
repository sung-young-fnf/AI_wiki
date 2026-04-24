# 번역 기사

- **원본 URL:** https://medium.com/@heeki/using-spec-driven-development-with-claude-code-4a1ebe5d9f29
- **번역 일시:** 2026-03-15 21:42:27

---

# Claude Code를 활용한 스펙 중심 개발 (Spec-driven Development)

저는 더 이상 코드를 직접 작성하지 않습니다. 저 자신을 본업으로 정식 소프트웨어 개발 엔지니어 (Software Development Engineer)라고 할 수는 없으며, 대규모 소프트웨어 시스템 (Software System)을 배포해 본 경험도 없습니다. 저는 솔루션 아키텍트 (Solutions Architect)로서 주로 프로토타입 (Prototype)과 소규모 애플리케이션 (Application)을 작성해왔습니다. 그럼에도 불구하고, 이러한 경험들은 독서, 연구, 교육과 결합하여 제가 현재 Claude Code와 같은 AI 도구에 적용하는 초보적인 소프트웨어 엔지니어링 (Software Engineering) 접근 방식을 형성했습니다.

이러한 맥락을 설명하는 이유는 제 개발 방식이 단순히 감에 의존하는 것이 아니라는 점을 말씀드리고 싶어서입니다. 요구사항 (Requirements) 문서화, 사양 (Specification) 생성, 그리고 생성된 코드 검토에 있어 구조화된 접근 방식이 존재합니다. AWS에 배포할 때는 자원 (Resource)이 변경 빈도에 따라 어떻게 구성되고 배포되는지에 대한 디자인 씽킹 (Design Thinking)이 수반됩니다.

이러한 이유로 저는 코드 생성에 곧바로 뛰어들기보다는 프로젝트의 계획 단계에 많은 시간을 할애합니다. 초기에 요구사항을 정의하고 문서화하는 것은 더 나은 결과물과 전반적으로 덜 답답한 경험으로 이어집니다. AI 도구가 등장하기 전의 어떤 소프트웨어 엔지니어링 프로젝트에도 해당되는 이야기라고 할 수 있습니다.

그럼에도 불구하고, AI 도구의 판도라 상자가 열렸고, 이제 우리는 백로그 (Backlog)에 더 많은 기술 부채 (Tech Debt)를 추가하기 전에 감에만 의존하는 코딩 (Vibe Coding)에 제동을 걸어야 합니다. 스펙 중심 개발 (Spec-driven Development)은 이러한 제동 장치 역할을 하여, 생성형 AI (Generative AI)의 생산성 향상 이점을 활용하면서도 훌륭한 소프트웨어 엔지니어링 관행을 강화할 수 있습니다.

## 스펙 중심 개발 (Spec-driven Development) 이해하기

제가 스펙 중심 개발에 대해 처음 듣기 시작했을 때, 저는 처음에 이것이 구현 (Implementation)을 진행하기 전에 요구사항을 먼저 문서화하는 것에 불과한지 궁금했습니다. 이미 Confluence에 모두 문서화되어 있었고, 이를 MCP를 통해 가져올 수 있었지 않나? 그러나 잘 문서화된 프로젝트조차도 범위 확장 (Scope Creep), 기능 이탈 (Feature Divergence), 과도한 기능 추가 (Gold Plating), 그리고 임시방편적인 수정 (Quick Fix)으로 인한 기술 부채의 대상이 된다는 점을 인식하게 됩니다.

스펙 중심 개발은 이러한 문제 중 일부를 해결하는 데 도움이 될 수 있지만, 적용 방식에 따라 달라집니다. Birgitta Böckeler는 스펙 중심 개발의 세 가지 수준을 설명하는 훌륭한 블로그 게시물을 작성했습니다.

![](https://example.com/image-of-spec-driven-levels)
*출처 블로그 게시물에 설명된 스펙 중심 개발 (Spec-driven Development)의 수준*

*   **스펙 우선 (Spec-first)**: 충분히 숙고된 사양 (Spec)을 먼저 작성한 다음, AI 지원 개발 워크플로에서 사용합니다.
*   **스펙 고정 (Spec-anchored)**: 작업 완료 후에도 해당 기능의 발전 및 유지보수를 위해 사양을 계속 유지합니다.
*   **스펙 소스 (Spec-as-source)**: 시간이 지남에 따라 사양이 주요 소스 파일 (Source File)이 되며, 사람은 사양만 편집하고 코드는 전혀 건드리지 않습니다.

저는 대체로 스펙 우선 (spec-first) 접근 방식을 선호하는 경향이 있습니다. 프로젝트의 단일 사양을 반복하여 훌륭한 설계 (Design)와 구현 계획을 세우는 것은 매우 쉽습니다. 하지만 특히 프로젝트가 깊어질수록 그 하나의 사양을 잊어버린 채 바로 구현을 시작하는 것도 마찬가지로 쉽습니다. 어쩌면 그것은 스펙 우선이 아니라 스펙 일회성 개발 (Spec-once Development)일 수도 있습니다.

![](https://example.com/image-of-spec-once-development)
*사양이 프로젝트를 시작하지만 나중에 잊혀지는 스펙 일회성 개발 (Spec-once Development)*

그러나 스펙 중심 개발의 가치는 개발자가 사용 사례 요구사항, 아키텍처 (Architecture) 고려사항을 깊이 생각하고 구현 접근 방식을 개략적으로 설명하도록 강제한다는 사실에 있다고 생각합니다. 원본 다이어그램에 묘사된 바와 같이, 이는 여러 기능에 대해 여러 사양 문서가 있을 수 있음을 나타냅니다. 더욱이, 스펙 중심의 후반 정의들은 개발자가 그러한 요구사항을 지속적으로 재검토하고 사양에 다시 반영하도록 강제합니다.

## 지속적인 피드백 루프 (Continuous Feedback Loop)로서의 스펙 중심 개발

### 사양 (Specification) 계획 및 초안 작성

구체적인 실습을 위해, 저는 AgentCore Gateway에서 인터셉터 (Interceptor)가 어떻게 작동하는지에 대한 세부적인 작동 방식 (Nuts and Bolts)을 더 잘 이해하고 싶었습니다. 문서에는 인터셉터 함수 (Function)와 함께 입력 및 출력 페이로드 (Payload) 예제가 제공되지만, 저는 직접 깊이 파고들어 보고 싶었습니다.

![](https://example.com/image-of-request-interceptor-flow)
*AgentCore Gateway를 통한 요청 인터셉터 (Request Interceptor) 흐름*

이러한 프로토타이핑 (Prototyping) 노력에 대한 저의 일반적인 흐름은 1/ 문서 및 기타 관련 출처를 읽어 사전 조사 (Due Diligence)를 수행하고, 2/ AWS에 배포하는 데 필요한 자원을 계획하며, 3/ 작고 테스트 가능한 모듈 (Module)을 구축하기 시작하는 것입니다. 연구 단계는 항상 수행되지만, Claude Code의 초기 프롬프트 (Prompt)를 구축할 때도 동일한 단계를 사용합니다.

![](https://example.com/image-of-initial-prompt)
*프로젝트 및 빌드 단계 정의를 위한 초기 프롬프트 (Prompt)*

1/ 저는 CloudFormation 문서를 읽고 관련 자원을 구성하는 데 필요한 속성 (Property)과 파라미터 (Parameter)를 이해했습니다. 또한 Claude Code가 문서를 학습하여 동일한 컨텍스트 (Context)에서 작동하고 관련 자원 속성을 사용하도록 지시했습니다.

2/ 저는 자원 종속성 (Resource Dependency), 모듈식 테스트 (Modular Testing), 그리고 전반적인 프로젝트 흐름을 고려하여 스택 (Stack)을 구축할 방법에 대해 생각하는 시간을 가졌습니다. 그 결과, 전체 프로젝트를 적절한 단계 (노란색)를 가진 두 개의 하위 프로젝트 (빨간색)로 나누었습니다.

![](https://example.com/image-of-modular-stacks)
*더 간단한 테스트 및 조합 가능한 배포를 위한 모듈형 스택 (Modular Stack)*

첫 번째 하위 프로젝트의 경우, 인터셉터에 대한 단일 단계 (스택 1)를 정의했습니다. 인터셉터는 Lambda 함수이며, InterceptorConfigurations 속성을 통해 Gateway 자원에 추가됩니다. 저는 이 인터셉터를 별도의 스택으로 만들어 모든 서버리스 (Serverless) 프로젝트에서와 마찬가지로 SAM (Serverless Application Model)을 사용하여 테스트하고 반복할 수 있도록 했습니다.

두 번째 하위 프로젝트의 경우, 단계별로 구축하는 데 도움이 되도록 세 가지 단계를 정의했습니다. 최상위 수준에서는 현재 공개 미리보기 (Public Preview) 중이며 스타터 툴킷 (Starter Toolkit)을 대체하는 것을 목표로 하는 새로운 AgentCore CLI (Command Line Interface)를 선호한다고 명시했습니다. 그러나 공개 미리보기 상태이기 때문에 아직 모든 자원과 구성을 지원하지 않습니다. 이러한 시나리오에서는 boto3 SDK (Software Development Kit)를 사용하지 않도록 지시했습니다. 돌이켜보면 CloudFormation을 사용하도록 대체해야 한다고 명확히 지시했어야 했습니다. 이후의 지침 전반에 걸쳐 CloudFormation 문서를 참조했습니다.

첫 번째 단계 (스택 2)에서는 MCP 서버 (Server)를 생성하고 AgentCore Runtime에 배포하도록 지시했습니다. 로컬 (Local)에서 테스트한 다음, AgentCore Runtime에 배포된 엔드포인트 (Endpoint)에서 동일한 테스트를 사용하도록 지시했습니다.

두 번째 단계 (스택 3)에서는 AgentCore Gateway 자원을 생성하고 AgentCore Runtime의 MCP 서버를 대상 (Target)으로 설정하도록 지시했습니다. 또한 AgentCore Gateway가 MCP 서버에 연결할 때 OAuth2를 사용하도록 자격 증명 공급자 (Credential Provider) 구성을 설정했습니다. 통합 (Integration)이 제대로 작동하는지 확인하기 위해 종단 간 (End-to-End) 스택을 테스트하도록 지시했습니다.

세 번째 단계 (스택 3 업데이트)에서는 Gateway에 인터셉터를 통합하도록 지시했습니다. 이 인터셉터는 MCP 서버로 전달되는 커스텀 헤더 (Custom Header)를 삽입합니다. 이 단계에서는 호출 스택 (Call Stack) 전체에 걸쳐 전달되고 궁극적으로 테스트 클라이언트 (Test Client)로 반환되는 헤더를 확인하고 싶었기 때문에 더 많은 로깅 (Logging)을 추가했습니다.

3/ 마지막으로, 계획 단계에서 충분한 반복이 이루어지도록 모든 계획을 사양으로 작성했습니다. 설계 (Design), 아키텍처 (Architecture), 구현 (Implementation) 계획이 제 기대에 부합하는지 확인하기 위해 사양을 검토하는 데 시간을 보냈습니다.

## Claude Code 사용 팁

기본 컨텍스트 윈도우 (Context Window)인 200k 토큰 (Token)이 제 목적에 적합하다는 것을 알게 되었습니다. 컨텍스트 윈도우는 Claude와의 각 왕복 (Round Trip)에 포함될 수 있는 정보의 양을 정의합니다. 이 정보에는 관련 코드, 가이드 문서, 사용 가능한 도구와 그 설명, 도구 호출에서 검색된 데이터, 스킬 (Skill), 대화 기록 등이 포함됩니다. 왕복은 위에서 언급된 입력과 응답 출력을 모두 포함합니다.

유료 Claude 요금제의 기본 컨텍스트 윈도우 제한은 200k 토큰이며, Pro 구독 (Subscription) 경험상 Claude Code에도 적용됩니다. 100만 토큰의 확장 컨텍스트 윈도우는 현재 Max 20x 구독 사용자에게만 제공됩니다 (아쉽게도).

Pro 구독에서 Bedrock을 직접 사용하는 것으로 전환하면 어떻게 될까요? Claude Code를 시작하기 전에 `CLAUDE_CODE_USE_BEDROCK=1`로 설정하여 전환할 수 있습니다. Bedrock은 Claude Opus 4.6, Sonnet 4.6, Sonnet 4.5, Sonnet 4를 제공하며, 이들은 모두 베타 (Beta) 버전에서 100만 토큰 컨텍스트 윈도우를 지원합니다. 이 확장 컨텍스트 윈도우에 접근하려면 `anthropic-beta: context-1m-2025-08-07`이라는 커스텀 헤더를 삽입해야 합니다. 그러나 Claude Code는 API (Application Programming Interface)와의 상호 작용을 관리하며, 제가 아는 한 베타 플래그 (Beta Flag)를 추가할 방법은 없습니다. 따라서 저에게는 200k 토큰 컨텍스트 윈도우가 한계입니다.

![](https://example.com/image-of-context-usage)
*Claude Code에서 컨텍스트 사용량 확인*

컨텍스트 윈도우 제한에 도달하는 과도한 사용 시나리오에서는 Claude Code가 대화 기록을 자동으로 압축합니다 (Compacts). 이 압축 과정 (Compaction Process)은 최소 3~5분, 길게는 10~12분이 소요되는 것을 확인했습니다.

Opus는 Sonnet에 비해 구독 요금제 사용량 제한 (Usage Limit)에 훨씬 빨리 도달하는 것을 관찰했습니다. 구독 요금제에서 사용량 제한은 보낼 수 있는 메시지 수를 결정하는 "대화 예산 (Conversation Budget)"으로 정의됩니다. 이는 대화 기록, 사용된 기능, 선택된 모델에 영향을 받습니다. Pro 구독에 대한 저의 개인적인 경험 (Anecdotal Experience)에 따르면, Opus 4.6을 45분에서 1시간 동안 집중적으로 사용했을 때 슬라이딩 윈도우 (Sliding Window) 사용량 제한에 부딪혔습니다. Sonnet 4.6으로 전환했을 때는 몇 시간 동안 집중적으로 사용한 후에도 사용량 제한에 도달하지 않았습니다.

저는 Claude Code에게 필요에 따라 명확히 하는 질문을 하되, 선택 가능한 입력 (Selectable Input)을 사용하여 명확한 답변을 더 간단하게 만들도록 지시합니다. 이렇게 하면 선택 메뉴를 통해 탐색할 수 있는 옵션 세트를 제시합니다. 필요한 경우 마지막 옵션은 사용자 자유 입력 (Open User Input)을 위해 남겨두지만, 일반적으로 제시된 옵션으로 충분하다는 것을 알게 되었습니다. 이는 주고받는 대화 (Back and Forth)를 훨씬 쉽고 빠르게 만듭니다. 마지막에는 Claude Code가 했던 질문과 각 질문에 대한 선택된 답변 요약을 깔끔하게 얻을 수 있습니다.

![](https://example.com/image-of-summary-of-responses)
*Claude Code의 명확히 하는 질문 세트에서 얻은 답변 요약*

시간이 지남에 따라 Claude Code의 행동을 점점 더 신뢰하게 되었습니다. Claude Code가 취하는 각 행동에 대해 일반적으로 세 가지 옵션을 제시합니다: 1/ 예, 2/ 예, 그리고 [이러한 하위 행동]에 대해서는 다시 묻지 마세요, 3/ 아니요. 처음에는 주로 예를 선택하고 각 행동을 면밀히 관찰했습니다. 시간이 지남에 따라 많은 행동, 특히 단순히 데이터를 가져오는 읽기 전용 (Read-only) 작업은 상당히 무해하다는 것을 알게 되었습니다. 결과물을 더 신뢰하게 되면서 코드 파일 (Code File)에 대한 쓰기 (Write) 작업도 자동 허용하는 자신을 발견했습니다. 그럼에도 불구하고, `--dangerously-skip-permissions` 플래그 (Flag)를 사용하여 완전히 무모한 (YOLO) 모드로 진입하는 지점까지는 아직 이르지 못했습니다. 에이전트 팀 (Agent Team)으로 실험을 시작할 때 어떻게 될지 지켜볼 것입니다.

저는 tmux를 사용하여 여러 Claude Code 세션 (Session)을 실행하는 것을 즐겼습니다. 처음에는 구독 요금제 세션 하나와 Bedrock을 사용하는 두 개의 다른 세션을 가졌기 때문에 이렇게 했습니다. 그러나 API 접근 (API Access)을 활성화하고 이전 세션을 재개하는 것이 얼마나 쉬운지 알게 되었을 때, 그렇게 할 필요가 없다는 것을 깨달았습니다.

```bash
/exit
export CLAUDE_CODE_USE_BEDROCK=1
claude --resume <session_id>
```

어쨌든, 여러 병렬 세션 (Parallel Session)은 에이전트 팀으로 실험할 때 유용할 것입니다: iTerm2의 `tmux -CC`가 최고입니다!

이 프로젝트를 진행하면서 이 흥미로운 일화 (Fun Little Nugget)를 겪었습니다. Claude Code가 문제를 해결하기 위한 접근 방식을 연구하려고 할 때, 웹 검색 (Web Search)을 사용하여 추가 정보를 검색했고, 심지어 제 블로그 게시물을 가져오려고 시도했습니다!

![](https://example.com/image-of-claude-code-pulling-blog-post)
*Claude Code가 제 블로그 게시물(AWS in Plain English에 게시됨)을 가져오는 모습*

불행히도 Claude Code는 Medium이 에이전트 (Agent)가 플랫폼에서 콘텐츠를 검색하는 것을 차단하는 것 같아서 게시물을 가져오는 데 실패했습니다.

## 얻은 교훈

사전 계획 (Upfront Planning)에 투자한 시간은 구현 효율성과 결과물 품질에 큰 이점을 가져다줍니다. 이전 프로젝트에서는 초기 프롬프트의 일부로 몇 문장을 빨리 작성하고 가능한 한 빨리 생성을 시작했습니다. 그러다 보니 많은 부분을 수정해야 했고, 때로는 처음부터 다시 시작해야 하는지 고민하기도 했습니다. 이 프로젝트를 한 번에 끝내지는 못했지만, 대부분의 후속 상호 작용은 프로젝트 전체의 대대적인 변경보다는 작은 조정에 불과했습니다. 또한, 저는 프로젝트에서 얻은 교훈을 CLAUDE.md 가이드 문서에 자주 통합했으며, 심지어 프로젝트 마지막에는 SKILL.md를 만들기도 했습니다. 맞춤형 스킬 (Custom Skill) 생성은 향후 프로젝트에서 상당한 촉진제 (Accelerator)가 될 가능성이 높다고 생각합니다.

작고 쉽게 테스트 가능한 단위로 단계적으로 (Stepwise) 구축하십시오. 사전 계획은 기능을 테스트하고 수정하기 쉽게 만듭니다. 이 글은 엘리트 엔지니어링 문화 (Elite Engineering Culture) 구축을 위한 접근 방식을 논의합니다. 또한 스택형 풀 리퀘스트 (Stacked Pull Request) 사용에 대해서도 다루는데, 이는 더 작고 쉽게 검토할 수 있는 풀 리퀘스트를 생성하며, 각 풀 리퀘스트는 서로 의존성 (Dependency)을 가집니다.

유연성 (Flexibility)과 우회책 (Workaround)이 종종 필요합니다. 저는 새로운 AgentCore CLI를 정말 좋아합니다. 그러나 이 글을 쓰는 시점에도 여전히 공개 미리보기 상태였기 때문에, 구현에 필요한 기능 중 어느 것도 새로운 CLI에서 지원되지 않았습니다. 따라서 저는 CloudFormation 방식을 택했습니다. 또한 CloudFormation을 사용하여 자격 증명 공급자 (Credential Provider)를 생성할 수 없었기 때문에 boto3를 사용하여 생성해야 한다는 것을 알게 되었습니다. 거기서부터 공급자 ARN (ARN)을 Gateway 대상 (Target)의 속성 (Property)으로 주입할 수 있었습니다.

보안 (Security)은 빠르면 빠를수록 좋습니다. 당연한 이야기라는 것을 알고 있습니다. Gateway로의 인그레스 (Ingress)를 위한 OAuth2의 마지막 계층을 나중에 추가했는데, 그 결과 전체 스택 (Stack)을 재배포해야 했습니다. Gateway의 AuthorizerType 속성 (Property)은 제자리에서 변경할 수 없으며 대신 자원을 완전히 교체해야 한다는 것을 알게 되었습니다. 그러나 boto3를 통해 자격 증명 공급자를 생성해야 했기 때문에 다단계 삭제 및 재생성 과정이 필요했습니다. 다행히 Claude Code는 이를 비교적 쉽게 처리했습니다. 그러나 엔터프라이즈 (Enterprise) 배포 파이프라인 (Pipeline)에서는 이것이 다소 더 고통스러울 수 있습니다.

문서를 정기적으로 재검토하고 업데이트하십시오. 각 단계에서 방향을 수정하고 설계를 조정해야 할 때마다, 저는 사양을 업데이트했는지 확인했습니다. 비록 주로 스펙 우선 (Spec-first) 접근 방식을 적용했다고 추측하지만, 사양을 지속적으로 재검토하고 스펙 고정 (Spec-anchored)에 더 가까워지려고 노력했습니다. 이는 제가 본질에 충실하고, 원래 의도를 다시 확인하며, 적절하게 수정하도록 했습니다.

## 결론

이 분야는 매우 빠르게 발전하고 있습니다. 저는 Claude Code에 점점 더 많은 시간을 할애하며 최신 기능과 기술을 사용하는 방법을 알아내고 있습니다. 여러분도 배경 지식에 관계없이 자신만의 작은 프로젝트 아이디어를 생각하고 직접 경험해 보시길 권합니다. 이러한 도구만 있다면 누구든지 만들 수 있기 때문입니다.

기술의 최전선은 계속해서 나아가고 있습니다. 저는 에이전트 팀 (Agent Team)을 빌드 (Build)에 적용하려는 더 큰 프로젝트 아이디어를 가지고 있습니다. 이 프로젝트는 제가 대화형으로 구축할 수 있는 것보다 훨씬 많을 것이므로, 백로그 (Backlog)에서 이슈 (Issue)를 할당할 에이전트 팀 동료들과 함께 어떻게 이 작업을 수행할 수 있을지 탐색하고 싶습니다. 그 여정을 다가오는 블로그 게시물에 문서화할 계획입니다. 계속 지켜봐 주세요!

## 참고 자료
*   https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html
*   https://github.com/heeki/agents/tree/main/interceptors
*   https://x.com/bcherny/status/2017742741636321619
*   https://www.cjroth.com/blog/2026-02-18-building-an-elite-engineering-culture
*   https://builder.aws.com/content/39vdJOeWD0pbA7hpKpW1a2tBw8x/extending-claude-code-with-plugins-and-skills-for-aws-development

*   에이전틱 AI (Agentic AI)
*   생성형 AI (GenAI)
*   Claude Code
*   AWS
*   Amazon Bedrock Agentcore

---

Heeki Park 작성
AWS 수석 솔루션스 아키텍트 (Principal Solutions Architect). 본인의 견해입니다. https://linktr.ee/heekipark