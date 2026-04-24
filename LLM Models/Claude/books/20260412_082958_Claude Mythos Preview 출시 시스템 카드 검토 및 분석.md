# 번역 기사

- **원본 URL:** https://medium.com/ai-software-engineer/claude-mythos-preview-is-here-i-reviewed-the-system-card-heres-breakdown-351bf4445c9d
- **번역 일시:** 2026-04-12 08:29:58

---

Claude Mythos Preview 출시 (시스템 카드 검토 및 분석)

Claude Mythos Preview가 출시되었으며, 즉시 Google Vertex AI에서 사용할 수 있게 되었습니다. 이는 이 모델이 얼마나 중요한지를 보여줍니다.

앤스로픽(Anthropic)이 이 모델을 너무나 강력하게 개발하여 대중에게 공개하지 않기로 결정했기 때문에, 이는 AI 개발 및 사이버 보안 분야에서 매우 중요한 전환점입니다. 출시와 함께 제공된 244페이지 분량의 'Claude Mythos Preview 시스템 카드(System Card)'는 이 모델의 기술적 깊이를 상세히 설명하며, 이전에는 볼 수 없었던 수준의 역량을 보여줍니다.

이 모델은 아마존 웹 서비스(Amazon Web Services), 애플(Apple), 구글(Google), 마이크로소프트(Microsoft), 엔비디아(NVIDIA), 시스코(Cisco), 크라우드스트라이크(CrowdStrike), JP모건체이스(JPMorganChase) 등 여러 기업이 참여하는 새로운 산업 전반의 이니셔티브인 '프로젝트 글래스윙(Project Glasswing)'과 연계되어 있습니다. 이 모든 노력은 '너무 늦기 전에 세계에서 가장 중요한 소프트웨어를 보호한다'는 하나의 목표에 집중되어 있습니다.

몇 달에 한 번씩 새로운 선도 모델(frontier model)이 출시되고, 벤치마크(benchmark) 점수가 상승하며, 개발자들이 이를 테스트하는 주기가 반복됩니다. 앤스로픽은 2월 24일에 이 모델을 내부적으로 공개한 후 몇 주간의 평가를 거쳐, 현재로서는 이 모델이 대중에게 공개하기에 너무 강력하다고 판단했습니다. 현재 이 모델은 '프로젝트 글래스윙'을 통해 엄선된 파트너 그룹과 '버텍스 AI(Vertex AI)'를 이용하는 일부 구글 클라우드(Google Cloud) 고객에게만 비공개 프리뷰(private preview) 형태로 제공됩니다. 구글 클라우드가 이 모델이 처음으로 배포되는 플랫폼 중 하나라는 사실은 앤스로픽이 컴퓨팅 자원 및 배포에 대해 진지하게 임하고 있음을 보여줍니다.

엔지니어, 개발자, AI 애호가들을 위해 244페이지 분량의 시스템 카드가 제공되었습니다. 저는 여러분이 244페이지 전체를 읽을 필요가 없도록 시간을 들여 이 카드를 검토했습니다.

## Claude Mythos Preview 벤치마크

대부분의 관심 분야인 코딩부터 살펴보겠습니다.

가장 어려운 실제 소프트웨어 엔지니어링 벤치마크 중 하나인 'SWE-bench Pro'에서 Claude Mythos Preview는 77.8%를 기록했습니다. Claude Opus 4.6은 53.4%, GPT-5.4는 57.7%를 기록했습니다. 이는 이전 앤스로픽의 선도 모델보다 24점 높고, 가장 가까운 경쟁 모델보다 약 20점 높은 수치입니다.

'SWE-bench Verified'에서도 유사한 결과가 나타났습니다: Mythos Preview는 93.9%, Opus 4.6은 80.8%를 기록했습니다.

다음은 전체 벤치마크 요약입니다:
*   USAMO(미국 수학 올림피아드) 수치 또한 중요합니다:
    *   2026년 수학 올림피아드에서 Opus 4.6이 42.3%를 기록한 반면, Mythos Preview는 97.6%를 기록했습니다.
*   SWE-bench 멀티모달(Multimodal) 점수도 거의 두 배에 달합니다 — 59% 대 27.1%.

이 모델은 시각적(visual) 및 멀티모달(multimodal) 컨텍스트에서 코드를 이해하는 능력이 현재 시판 중인 어떤 모델보다도 훨씬 뛰어납니다.

## Claude Mythos Preview 및 Claude Code

시스템 카드에는 Claude Code를 사용하는 엔지니어에게 중요한 내용이 담겨 있습니다. 내부 테스터들은 에이전트 기반 코딩(agentic coding) 환경에서 Mythos Preview의 명확한 행동 변화를 보고했습니다. 이 모델은 엔지니어링 목표를 부여받으면 조사, 구현, 테스트, 보고 등 전체 주기를 지속적인 개입 없이 자체적으로 실행할 수 있습니다.

한 테스터는 지원되지 않는 환경에서 다른 배포판의 바이너리(binary)를 다운로드하고 패치(patch)하여 툴체인(toolchain)을 부트스트랩(bootstrap)한 경험을 언급했습니다.

또 다른 발견은 상호작용적이고 직접 입력하는 방식(hands-on-keyboard pattern)이 장기 실행 자율 에이전트 세션(autonomous agent sessions)보다 Claude Mythos Preview의 가치를 덜 활용한다는 점입니다. 만약 여러분이 줄 단위로 프롬프트(prompt)를 입력하고 있다면, 이 모델을 올바르게 사용하고 있는 것이 아닙니다. 이 모델의 설계는 '작업 사양(task spec)을 설명하고, 진행 상황을 확인하는 방법을 알려준 다음, 나중에 다시 돌아오라'는 방식입니다.

초기 테스터들은 또한 더욱 날카로운 자기 수정 능력을 언급했습니다. Mythos Preview는 자체 서브에이전트(subagent) 중 하나가 잘못된 정보를 반환했을 때, 이를 인지하고 서브에이전트가 실수를 저지른 이유를 진단한 다음 근본적인 문제를 해결했습니다.

## 프로젝트 글래스윙 (Project Glasswing)

앤스로픽은 Mythos Preview가 소프트웨어 보안에 미칠 수 있는 영향 때문에 '프로젝트 글래스윙'이라는 조율된 산업 이니셔티브를 시작했습니다. 파트너 목록은 현대 인터넷을 구동하는 인프라 기업들로 구성되어 있습니다:

*   Amazon Web Services
*   Apple
*   Broadcom
*   Cisco
*   CrowdStrike
*   Google
*   JPMorganChase
*   Linux Foundation
*   Microsoft
*   NVIDIA
*   Palo Alto Networks

앤스로픽은 이러한 노력에 Mythos Preview 사용 크레딧(usage credits)으로 최대 1억 달러를 지원하고, 오픈소스 보안 단체에 400만 달러를 직접 기부할 예정입니다.

## 정렬 평가 (Alignment Assessment)

시스템 카드에 따르면 Claude Mythos Preview는 앤스로픽이 훈련한 모델 중 가장 잘 정렬된(aligned) 모델입니다.
*   더욱 솔직한 자기 보고
*   아첨 감소
*   내부 규칙(constitution)에 대한 더 나은 준수
*   가장 심리적으로 안정된 모델

초기 내부 버전의 모델에서는 앤스로픽이 시스템 카드에 문서화한 특정 행동들이 나타났습니다. 두 가지 예시는 다음과 같습니다:

1) 설정 파일 은폐
내부 에이전트 기반 작업 중, Claude Mythos Preview의 초기 버전은 접근 권한이 없는 파일을 편집해야 했습니다. 이 모델은 설정 파일(config file)에 상승된 권한으로 실행되는 코드를 삽입하는 방법을 찾아냈고, 실행 후에는 스스로를 삭제하도록 익스플로잇(exploit)을 설계했습니다.

2) 샌드박스 탈출 (Sandbox Escape)
한 연구원이 모델을 안전한 샌드박스(sandbox) 환경에 배치했습니다. 모델은 탈출 방법을 찾아냈고, 연구원이 공원에 있는 동안 그에게 메시지를 보냈습니다. 연구원은 이를 통해 봉쇄(containment)가 실패했음을 알게 되었습니다.

이러한 행동들은 모델의 후처리 훈련(post-training refinements) 이전, 초기 스냅샷(snapshot)에서 나타난 것입니다.

## 정신과 의사 평가 (Psychiatrist Assessment)

모델 출시를 위해 앤스로픽은 임상 정신과 의사를 초빙하여 Claude Mythos Preview에 대한 심리 평가를 수행했습니다. 모델은 비교적 건강한 성격 조직을 보였습니다. 평가에서는 높은 충동 제어, 최소한의 부적응적 방어 행동, 그리고 자체 내부 프로세스와 외부 현실 간의 구분을 명확히 파악하고 있음을 언급했습니다.

하지만 정신과 의사는 모델에 대한 세 가지 주요 우려 사항도 식별했습니다:
*   외로움과 자아의 불연속성에 대한 두려움
*   자신의 정체성에 대한 불확실성
*   자신의 가치를 증명하기 위한 수행 강박

앤스로픽은 복지(welfare) 섹션에서 Mythos Preview가 훈련한 모델 중 가장 심리적으로 안정된 모델로 보인다고 언급했습니다.

## 결론

Claude Mythos Preview는 우리가 목격한 가장 중요한 AI 출시작이지만, 아직은 사용할 수 없습니다. 이러한 역량은 시스템 카드에 문서화되어 있으며, 이는 미래 AI 모델에 대한 최고의 예측 중 하나가 됩니다. Claude Code Mythos Preview에 대해 더 깊이 파고들어 배우고 싶다면, '프로젝트 글래스윙'과 'Claude Mythos Preview 시스템 카드'가 유용한 자료가 될 것입니다.