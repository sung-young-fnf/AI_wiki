# 번역 기사

- **원본 URL:** https://medium.com/@davidroliver/ralph-loops-teaching-claude-code-to-work-while-you-sleep-aebb434b4432
- **번역 일시:** 2026-04-02 07:31:07

---

# Ralph Loops: 자는 동안 Claude Code가 일하게 만드는 방법

저는 99가지의 인지 편향(cognitive biases)이 담긴 책을 읽고, 이를 Obsidian 보관소에 각 항목별로 구조화된 노트를 만들어 정리해야 했습니다. 각 노트에는 수십 개의 필드가 포함된 YAML 프론트매터(frontmatter), 분류, 세 가지 사례, 대응 전략, 학술적 참고 문헌이 필요했습니다. 이를 Claude Code와 대화하며 하나씩 처리한다면 99번의 개별 세션이 필요했을 것입니다. 이는 단순한 작업이 아니라 일종의 형벌에 가까웠습니다.

그래서 저는 자동화 루프를 구축했습니다. 몇 번의 시행착오 끝에, 단 한 번의 실패 없이 14분 만에 54개의 노트를 처리하는 세 번째 버전을 완성했습니다. 이 글은 Claude Code를 대화형으로만 사용해본 분들을 위해 자동화로 나아가는 방법을 설명합니다.

### 1단계: 메커니즘 이해

**`claude -p`란 무엇인가?**

일반적인 Claude Code 사용은 프롬프트를 입력하고 도구 호출을 승인하는 대화형 세션입니다. 하지만 Claude Code에는 헤드리스 모드(headless mode)인 `claude -p`가 있습니다. 프롬프트를 파이프로 연결하면 사람의 개입 없이 실행되고 결과를 반환합니다.

```bash
echo "이 파일을 요약해줘" | claude -p --model sonnet
```

이것이 자동화의 기본 구성 요소입니다. 한 번의 호출, 한 번의 작업, 한 번의 출력입니다.

**랄프 루프(Ralph Loop) 패턴**

랄프 루프는 `claude -p`를 Bash 스크립트로 감싸 반복 실행하는 패턴으로, 세 부분으로 구성됩니다.

1.  **추적 파일(Tracker file):** 완료된 항목과 남은 항목을 기록하는 마크다운 체크리스트입니다. 루프가 시작될 때마다 이 파일을 읽어 다음 작업 대상을 찾습니다.
2.  **프롬프트 파일(Prompt file):** 각 반복 작업에 대한 지침입니다. (예: "다음 9개 항목에 대해 JSON 형식으로 노트를 생성해줘.")
3.  **Bash 래퍼(Bash wrapper):** 모든 과정을 조율하는 스크립트입니다. 추적 파일을 읽고, 프롬프트를 작성하고, Claude를 호출하고, 출력을 처리하고, 추적 파일을 업데이트한 뒤 Git에 커밋합니다.

`--dangerously-skip-permissions` 플래그를 사용하면 도구 호출 시마다 승인을 묻지 않고 실행할 수 있습니다. 위험해 보일 수 있지만, 실행 전 프롬프트와 계획을 미리 검토하는 것으로 안전성을 확보합니다.

### 2단계: 시행착오와 교훈

**문제 1: 토큰 소모 문제**

`claude -p`가 호출될 때마다 Claude Code 환경 전체가 로드됩니다. 여기에는 프로젝트 지침(CLAUDE.md), 규칙(.claude/rules/), MCP 도구 스키마(Tool schemas), 자동 메모리 인덱스 등이 포함됩니다. 제 환경에서는 프롬프트가 시작되기도 전에 약 50,000 토큰의 컨텍스트(context)가 소모되었습니다. 15번의 루프를 돌면 기본 설정만으로 750,000 토큰을 소모하게 됩니다.

*   **교훈:** 컨텍스트 창(context window)에 무엇이 포함되는지 파악해야 합니다. 눈에 보이는 프롬프트보다 보이지 않는 환경 설정 토큰이 훨씬 더 클 수 있습니다.

**문제 2: 플래그의 함정**

최적화를 위해 두 가지 플래그를 시도했습니다.

*   `--bare`: 모든 환경 설정을 제거하고 프롬프트와 모델만 남깁니다. 하지만 이 플래그는 키체인 인증을 건너뛰기 때문에, 유료 구독(Max) 사용자의 경우 `ANTHROPIC_API_KEY` 환경 변수가 설정되어 있지 않으면 인증 오류가 발생합니다.
*   `--json-schema`: 모델이 스키마에 맞는 유효한 JSON을 출력하도록 강제합니다. 하지만 파일 경로(`--json-schema ./schema.json`)를 전달하면 무한 대기에 빠지는 버그가 있었습니다. 반면, 스키마를 인라인 문자열로 전달하면 즉시 작동했습니다.

**문제 3: 배치(Batch) 크기의 한계**

효율성을 위해 한 번에 처리하는 양을 3개에서 18개로 늘렸습니다. 하지만 모델이 한 번에 너무 많은 양의 구조화된 데이터를 생성하려다 보니 응답이 오지 않았습니다.

*   **교훈:** 배치 크기는 1 → 3 → 9 순으로 점진적으로 늘리며 테스트해야 합니다.

### 3단계: 해결책 - 역할의 분리

버전 1에서는 LLM이 파일 읽기, 웹 검색, 파일 쓰기, Git 커밋 등 모든 작업을 수행하게 했습니다. 이 모든 과정이 도구 호출과 토큰 소모를 유발했습니다. 해결책은 스크립트를 더 똑똑하게 만들고 LLM을 더 단순하게 사용하는 것이었습니다.

**버전 3의 역할 분담:**

*   **Bash 스크립트:** 추적 파일 읽기, 저장소 파일 검색, 프롬프트 데이터 주입, JSON 응답 파싱, 파일 쓰기, 체크리스트 업데이트, Git 커밋.
*   **LLM (Claude):** 노트 콘텐츠 생성 (오직 이 작업만 수행).

모델은 필요한 모든 정보를 프롬프트로 전달받고 구조화된 JSON을 반환하기만 하면 됩니다. 도구 호출이나 웹 검색은 일절 수행하지 않습니다. 이 방식을 통해 **토큰 소비량을 약 95% 절감**할 수 있었습니다.

### 활용 가능한 템플릿 스크립트

다음은 모든 배치 작업에 적용할 수 있는 범용 랄프 루프 스켈레톤(skeleton) 코드입니다.

```bash
#!/bin/bash
set -euo pipefail
TRACKER="tracker.md"
BATCH_SIZE=9
SCHEMA='{"type":"object","properties":{"files":{"type":"array","items":{"type":"object","required":["name","content"],"properties":{"name":{"type":"string"},"content":{"type":"string"}}}}},"required":["files"]}'

while true; do
  # 1. 남은 작업 확인
  unchecked=$(grep '^\- \[ \]' "$TRACKER" | head -n "$BATCH_SIZE")
  count=$(echo "$unchecked" | grep -c '.' || true)
  [ "$count" -eq 0 ] && echo "Done!" && break

  # 2. 프롬프트 생성 (필요한 데이터 주입)
  prompt="다음 항목들에 대해 노트를 생성해줘: \n$unchecked"

  # 3. Claude 호출 (도구 사용 안 함, JSON 출력)
  response=$(echo "$prompt" | claude -p - \
    --model sonnet \
    --output-format json \
    --json-schema "$SCHEMA" \
    --tools "" \
    --dangerously-skip-permissions)

  # 4. JSON에서 파일을 추출하여 저장
  file_count=$(echo "$response" | jq '.structured_output.files | length')
  for i in $(seq 0 $((file_count - 1))); do
    name=$(echo "$response" | jq -r ".structured_output.files[$i].name")
    content=$(echo "$response" | jq -r ".structured_output.files[$i].content")
    echo "$content" > "$name"
  done

  # 5. 추적 파일 업데이트 및 Git 커밋
  # (체크박스 업데이트 로직 추가)
  git add -A && git commit -m "Processed batch of $count"
done
```

### 요약 및 주의사항 (Gotchas)

1.  **`--bare` 사용 시 주의:** 구독형 플랜 사용 시 API 키 환경 변수가 필수입니다.
2.  **`--json-schema`는 인라인으로:** 파일 경로 대신 JSON 문자열을 직접 전달하세요.
3.  **단일 항목 테스트 필수:** 전체 루프를 돌리기 전, 하나의 항목으로 인증과 출력이 정상인지 반드시 확인하세요.
4.  **점진적 확장:** 배치 크기를 단계적으로 늘려 모델의 출력 토큰 한계를 넘지 않도록 관리하세요.
5.  **모니터링의 한계:** 현재 상태 업데이트는 단계별로만 이루어지며, 생성 중에는 화면이 멈춘 것처럼 보일 수 있습니다. 로그 파일을 `tail -f`로 확인하는 것이 더 확실합니다.