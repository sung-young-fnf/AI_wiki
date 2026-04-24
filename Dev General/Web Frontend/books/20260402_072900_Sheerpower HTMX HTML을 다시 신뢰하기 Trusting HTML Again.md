# 번역 기사

- **원본 URL:** https://medium.com/@yashbatra11111/sheerpower-htmx-trusting-html-again-f826cd62cf82
- **번역 일시:** 2026-04-02 07:29:00

---

# Sheerpower + HTMX — HTML을 다시 신뢰하기 (Trusting HTML Again)

오늘날 웹에서 단순한 무언가를 구축한다는 것은 기묘한 평온함을 줍니다. 지루하다는 뜻이 아니라, 시스템의 모든 부분을 이해할 수 있을 만큼 '단순'하다는 의미입니다. 버튼 하나를 클릭했을 때 수많은 보이지 않는 상태 전이(state transitions)가 발생하지 않고, '요소 검사(inspect element)'만으로도 충분히 디버깅이 가능한 그런 세계입니다. Sheerpower와 HTMX가 함께 작동할 때 바로 이런 환경이 조성됩니다.

많은 개발자가 거대한 자바스크립트 번들(JavaScript bundles)을 전송하고 복잡한 API를 유지 관리하는 데 익숙해진 나머지, 웹의 본질적인 강점을 잊곤 합니다. 바로 **"서버는 HTML을 보내고, 브라우저는 HTML을 렌더링한다"**는 원칙입니다.

서버 측의 Sheerpower와 브라우저 측의 HTMX 조합은 이 철학을 현대적인 감각으로 재현합니다. 성능 저하나 상호작용의 손실, 개발자의 고통 없이 말입니다. 이것은 과거의 웹에 대한 향수가 아니라, 기술을 다시 신뢰할 때 현대 웹이 도달할 수 있는 모습입니다.

### 핵심 아이디어
*   **Sheerpower:** 패스트CGI(FastCGI) 핸들러를 상주시켜 데이터를 한 번만 로드하고 즉각적으로 응답합니다.
*   **HTMX:** 마이크로 요청을 보내고 HTML을 페이지에 직접 교체(swap)합니다.

JSON도, 하이드레이션(hydration)도, 클라이언트 측 상태 머신(state machines)도 필요 없습니다. 그저 HTML이 들어가고 HTML이 나올 뿐입니다.

### Sheerpower란 무엇인가
Sheerpower는 비즈니스 애플리케이션 구축을 위해 설계된 프로그래밍 언어입니다. 다음 요소에 집중합니다:
*   빠른 컴파일
*   정밀한 산술 연산 (16자리 보장)
*   대규모 데이터 처리량
*   명확하고 가독성 높은 구문

복잡함 없이 밀리초(ms) 미만의 응답 시간을 가진 사전 검색, 검색 엔진, 분석 도구 및 데이터 집약적인 API를 구축할 수 있습니다. Sheerpower에서는 코드가 의도를 표현하고, 언어가 메모리와 동시성을 관리하며, 모든 것은 배후에서 최적화됩니다.

#### Sheerpower 런타임 모델
1.  **Sheerpower FastCGI 앱:** 한 번만 시작됩니다.
2.  **데이터 로드:** TSV, 설정, 데이터 등을 한 번만 로드하여 메모리 내 클러스터(cluster)를 구축합니다.
3.  **요청 및 응답:** 재부팅이나 재로드 없이 요청을 즉시 처리합니다.

이 모델만으로도 일반적인 웹 서버에서 발생하는 오버헤드의 90%를 제거할 수 있습니다.

### 2025년에 이 방식이 중요한 이유
현대의 웹 스택은 강력하지만 비대합니다. 번들러, 하이드레이션 프레임워크, 디핑(diffing) 알고리즘, 클라이언트 측 라우터, 거대한 node_modules 폴더 등 수많은 도구가 존재하며, 각 도구는 자신이 만들어낸 문제를 해결하겠다고 약속합니다.

하지만 대시보드, CRUD 도구, 관리자 패널, 검색 UI 등 앱의 80%는 순수 HTML만으로 충분합니다. HTMX와 Sheerpower는 다음과 같은 이점을 통해 합리성을 되찾아줍니다:
*   서버가 HTML을 렌더링하고 브라우저가 이를 교체함
*   로직의 중복 없음
*   API 접착 코드가 필요 없음
*   프론트엔드 번들이 없음

이는 자바스크립트에 반대하는 것이 아니라, **단순함을 지향**하는 것입니다.

### 시간과 자원을 아끼는 서버, Sheerpower
1.  **지속성 패스트CGI 핸들러:** 한 번 실행되어 계속 서비스합니다. 파일을 다시 읽거나 구조를 재구성할 필요가 없습니다.
2.  **클러스터(메모리 내 구조):** TSV나 데이터셋을 로드하여 고성능 메모리 인덱스를 즉시 구축합니다. 사전, 검색 인덱스, 지식 베이스 등에 적합합니다.
3.  **직관적인 템플릿:** 복잡한 템플릿 언어나 DSL, JSX 없이 단순한 변수 마커([[variable]])가 포함된 HTML을 사용합니다.
4.  **인지적 부하 제로:** 6개월 후에 다시 코드를 봐도 모든 것을 즉시 이해할 수 있습니다.

#### HTMX + Sheerpower 요청 흐름
1.  **브라우저 (HTMX):** `hx-get` 또는 `hx-post` 요청 전송
2.  **Sheerpower 서버:** 메모리 내 데이터를 조회하여 HTML 조각(fragment) 생성 및 응답
3.  **브라우저:** HTMX가 반환된 HTML을 대상 div에 교체 삽입

### Sheerpower 방식의 코드 예시
**1. TSV 파일을 메모리로 로드 (클러스터)**
```sheerpower
cluster words: word$, def$
cluster input name "@words.tsv", headers 0: words
```
단 두 줄로 데이터를 읽고 즉시 쿼리 가능한 고성능 메모리 구조를 만듭니다.

**2. HTMX 요청 처리**
```sheerpower
word = http_get("word")

if words::exists(word)
    def = words::lookup(word)
else
    def = "No exact match found."
endif

html = "<div class='result'><b>" & word & "</b><br>" & def & "</div>"
http_response(html)
```
의도가 명확하며 불필요한 절차가 없습니다.

### HTMX: 가벼운 HTML의 초능력
HTMX는 프레임워크가 아니라 확장된 HTML입니다. 다음 기능을 제공합니다:
*   `hx-get`: HTML 가져오기
*   `hx-post`: 페이지 새로고침 없이 제출
*   `hx-target`: 변경할 DOM 영역 선택
*   `hx-trigger`: 사용자 이벤트 트리거
*   `hx-swap`: DOM 교체 방식 설정

**HTMX 검색 폼 예시:**
```html
<form hx-get="/scripts/spiis.dll/word/get_def.html"
      hx-target="#definition"
      hx-trigger="submit">
  <input name="word" placeholder="Search..." required>
</form>

<div id="definition">단어를 입력하여 검색을 시작하세요.</div>
```
100% 선언적이며 읽기 쉽습니다.

### 아키텍처 비교
| 기존 SPA | HTMX + Sheerpower |
| :--- | :--- |
| JS 프레임워크 사용 | 프레임워크 없음 |
| JSON API 필요 | JSON 불필요 |
| 클라이언트 상태 관리 | 클라이언트 상태 머신 없음 |
| 하이드레이션(Hydration) | 하이드레이션 없음 |
| 번들러 및 빌드 단계 | 빌드 단계 없음 |

### 이 모델이 적합한 경우
*   관리 도구 및 CRUD 앱
*   검색 인터페이스 및 내부 대시보드
*   보고서 및 사전/자동 완성 UI
*   **오프라인 우선(Offline-first) 앱:** Sheerpower 런타임은 메모리 상주형이며 외부 API 의존성 없이 로컬에서 완벽하게 작동합니다.

실시간 협업 앱이나 캔버스 중심의 에디터, 거대한 클라이언트 상태가 필요한 앱이 아니라면 이 모델로 충분합니다.

### 시작하기
1.  순수 HTML로 시작합니다.
2.  HTMX 속성을 추가합니다.
3.  단순한 Sheerpower 핸들러를 작성합니다.
4.  데이터를 한 번 로드하고 HTML 조각을 반환합니다.

HTML은 잘 작동합니다. 브라우저는 HTML을 렌더링하는 데 탁월하고, 서버는 이를 생성하는 데 최적화되어 있습니다. Sheerpower와 HTMX는 '구식'이 아니라 우리가 잠시 잊고 있었던 **유행을 타지 않는(timeless) 길**입니다. 불필요한 복잡함에 지쳤다면 이 조합을 시도해 보십시오. 다시 웹과 사랑에 빠질지도 모릅니다.