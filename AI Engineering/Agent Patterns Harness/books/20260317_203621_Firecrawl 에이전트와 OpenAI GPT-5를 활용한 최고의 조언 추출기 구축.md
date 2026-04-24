# 번역 기사

- **원본 URL:** https://levelup.gitconnected.com/build-a-best-advice-extractor-with-firecrawls-agent-and-openai-gpt-5-57da4548321d
- **번역 일시:** 2026-03-17 20:36:21

---

# Firecrawl 에이전트와 OpenAI GPT-5를 활용한 최고의 조언 추출기 구축

---

사이드바 메뉴
글쓰기
알림
21
홈
라이브러리
프로필
스토리
통계
팔로잉
The Latency Gambler
Generative AI
Simranjeet Singh
Joe Njenga
Algo Insights
Civil Learning
Artificial Intelligence in Plain English
Abdur Rahman
AI Advances
Level Up Coding
더 보기
Level Up Coding

코딩 튜토리얼 (tutorial) 및 뉴스. 개발자 홈페이지: gitconnected.com && skilled.dev && levelup.dev

팔로잉

회원 전용 스토리

**Mostafa Ibrahim**
팔로우
14분 분량 읽기 · 19시간 전 게시

120

이미지를 전체 크기로 보려면 Enter를 누르거나 클릭하세요.
사진: 저자

## 서론

진로에 대해 혼란스러워서 조언을 얻고 싶을 때, 해커 뉴스 (Hacker News)를 열고 "개발자를 위한 진로 조언"을 검색했다고 가정해 봅시다. 619개의 댓글이 달린 스레드 (thread)를 발견했습니다. 완벽하죠! 하지만 세 시간 후, 리크루터가 악한 존재인지에 대한 47가지 논쟁과 Rust에 대한 12가지 곁가지 이야기를 읽고도, 연봉 협상을 제안 전후 언제 해야 할지 여전히 모릅니다.

익숙한 이야기인가요? 더 나은 접근 방식이 있습니다.

저는 특정 주제에 대한 상위 해커 뉴스 (Hacker News) 토론을 스크랩 (scrape)하고, 최고 품질의 댓글을 추출하며, LLM (대규모 언어 모델)을 사용하여 "최고의 조언 10가지" 요약을 생성하는 도구를 만들었습니다. 수백 개의 댓글을 일일이 스크롤할 필요가 없습니다.

여러분은 다음을 구축하게 될 것입니다.

*   Firecrawl AI 기반의 웹 스크래핑 (web scraping) 도구로 HN (해커 뉴스) 토론을 자동으로 탐색합니다.
*   최고의 댓글을 포착하는 구조화된 데이터 추출 시스템.
*   모든 것을 실행 가능한 조언으로 종합하는 LLM 분석 시스템.

이 도구는 약 90초 만에 실행되며, 주제당 약 $0.042의 비용이 들고, 진로 조언, 연봉 협상, 기술 면접, 스타트업 (startup) 실수 등 모든 것에 적용할 수 있습니다.

이 튜토리얼 (tutorial)이 끝날 때쯤에는 자신만의 조언 추출 시스템을 갖게 될 것입니다.

### 요약 (TL;DR)

**구축할 것** — 해커 뉴스 (Hacker News) 토론을 자동으로 스크랩하고, 고품질 댓글을 추출하며, LLM을 사용하여 어떤 주제든 "최고의 조언 10가지" 요약을 생성하는 도구입니다.
**작동 방식** — Firecrawl Agent는 일반 영어 (plain English) 명령으로 5개의 토론 (25개 댓글)을 스크랩합니다. CSS 선택자 (CSS selectors)나 Selenium이 필요 없습니다. LLM이 원시 댓글을 실행 가능한 조언으로 종합합니다.
**비용 및 속도** — 실행당 약 $0.042, 총 90초 소요. Firecrawl과 W&B Inference 모두 무료 티어 (free tiers)를 제공합니다.
**주요 장점** — Firecrawl 프롬프트 (prompt) 하나로 50~100줄의 Selenium 코드를 대체합니다. 웹사이트의 HTML 구조가 업데이트되어도 유지 보수 (maintenance)가 필요 없습니다.
**실제 결과** — 619개 댓글이 달린 HN 스레드 (threads)에서 진로 조언을 추출했습니다. Rust에 대한 곁가지 이야기를 하나도 읽지 않고도 10가지 실행 가능한 팁을 얻었습니다.

## Firecrawl의 Agent를 사용하는 이유

이전에 Selenium 스크래퍼 (scraper)를 작성해 본 경험이 있다면, 어떤 작업인지 잘 아실 겁니다.

*   쿠키 배너 (cookie banner) 하나만 처리하는 데 47줄의 코드
*   예상대로 아무것도 로드 (load)되지 않아 곳곳에 `time.sleep(3)`을 배치
*   웹사이트가 CSS 클래스 (CSS class)를 `.comment-text`에서 `.comment-body`로 변경할 때마다 스크립트 (script)가 중단됩니다.

반면에 Firecrawl은 우리의 삶을 훨씬 쉽게 만들어 줍니다. 다음은 동일한 작업을 두 가지 다른 접근 방식으로 수행하는 모습입니다.

**기존 스크래핑 (Traditional Scraping) (Selenium/BeautifulSoup):**

```
├── 검색 URL로 이동
├── 자바스크립트 (JavaScript) 로드 대기
├── 검색 결과 HTML 파싱 (parsing)
├── 결과 반복 처리
├── 각 게시물 클릭
├── 댓글 로드 대기
├── "더보기" 버튼 처리
├── 중첩된 댓글 구조 파싱
├── 페이지네이션 (pagination) 처리
└── 150~200줄의 취약한 코드
```

**Firecrawl /agent:**

```
├── “HN으로 이동, X 검색, 게시물 클릭, 상위 댓글 추출”
└── 완료. 구조화된 JSON (JSON)이 반환됩니다.
```

기존 웹 스크래핑은 악몽과 같습니다. CSS 선택자 (CSS selectors) 작성뿐만 아니라, 헤드리스 브라우저 (headless browsers) 관리, 자바스크립트 렌더링 (JavaScript rendering) 처리, 속도 제한 (rate limits)을 위한 재시도 로직 (retry logic) 구축, 요소 (elements)가 로드되지 않을 때의 타이밍 문제 디버깅 (debugging), 웹사이트가 HTML 구조를 업데이트할 때마다 코드 유지 보수, 그리고 완전히 다른 아키텍처 (architecture)를 가진 여러 웹사이트에서 이 모든 것을 작동시키는 작업을 해야 합니다.

'간단한' 스크래퍼도 웹사이트가 클래스 이름 (class name) 하나를 변경하는 순간 작동하지 않는 200줄 이상의 취약한 코드가 됩니다.

Firecrawl의 Agent는 이 모든 것을 없애줍니다. 원하는 것을 일반 영어로 설명하면, 클릭, 스크롤, 대기, 자바스크립트 렌더링, 탐색을 자동으로 처리합니다. CSS 선택자도, 브라우저 자동화도, 유지 보수 골칫거리도 없습니다. 마치 당신의 지시를 실제로 읽고 복잡한 세부 사항을 파악하는 숙련된 엔지니어 (engineer)를 둔 것과 같습니다.

## 사전 준비물

그렇다면 최신 데이터를 활용하는 자신만의 AI Agent를 구축하려면 정확히 무엇이 필요할까요? 목록은 매우 간단합니다.

✅ Python 3.8 이상
✅ 5분
✅ 두 개의 API 키 (API keys) (둘 다 무료 티어 제공)
✅ 반복적인 연구 자동화에 대한 관심

## 아키텍처 (Architecture)

드디어 튜토리얼 (tutorial) 부분입니다! 아키텍처 (architecture)부터 시작해 보겠습니다. 코드를 두 가지 주요 단계로 나눌 것입니다.

이미지를 전체 크기로 보려면 Enter를 누르거나 클릭하세요.
사진: 저자

왜 두 단계일까요? 관심사의 분리 (Separation of concerns) 때문입니다. 1단계가 작동하지 않으면 스크래핑 (scraping) 문제라는 것을 알 수 있습니다. 2단계에서 쓸모없는 결과가 나오면 프롬프트 (prompt) 문제인 것이죠. 또한 스크래퍼를 건드리지 않고 LLM을 교체할 수 있어 실험할 때 유용합니다. 다만, 사용되는 LLM은 더 이상 문제가 되지 않으므로, 원하는 LLM으로 자유롭게 교체할 수 있습니다.

## 1단계: Firecrawl의 Agent를 사용하여 해커 뉴스 스크래핑

프로세스를 시작하려면 먼저 필요한 의존성 (dependencies)을 설치하고 임포트 (import)해야 합니다. 이 Firecrawl 튜토리얼에서는 다음을 사용할 것입니다.

```bash
!pip install firecrawl-py openai pydantic
```

이제 임포트와 설정을 구성해 보겠습니다.

각 임포트의 역할:

*   **json:** 반환되는 데이터를 보기 좋게 출력 (pretty-printing)하기 위함
*   **datetime:** 보고서에 타임스탬프 (timestamp)를 찍기 위함
*   **Pydantic:** Firecrawl에서 원하는 데이터 구조를 정의하기 위함
*   **FirecrawlApp:** Firecrawl Python SDK (소프트웨어 개발 키트)
*   **OpenAI:** 호환 가능한 모든 OpenAI API (W&B Inference 포함)와 작동

```python
from datetime import datetime
from typing import List, Optional
from pydantic import BaseModel, Field
from firecrawl import FirecrawlApp
from openai import OpenAI
```

다음으로, API 키와 설정을 구성합니다. Firecrawl API 키는 [https://firecrawl.dev/app/api-keys](https://firecrawl.dev/app/api-keys)에서 얻을 수 있습니다 (무료 티어 제공, 신용 카드 불필요).

```python
FIRECRAWL_API_KEY = "Insert-Your-Firecrawl-API-Key"
```

간소화를 위해 API 인터페이스를 통해 사용하기 쉬운 LLM을 사용할 것입니다. 자신에게 가장 편리한 LLM을 활용할 수 있습니다.

```python
WANDB_API_KEY = "Insert-Your-WANDB-API-Key"
LLM_MODEL = "openai/gpt-oss-120b"
```

이번 실행에서는 진로 조언을 주제로 선택할 것입니다. 이 주제는 원하는 대로 변경할 수 있습니다.

```python
TOPIC = "career advice"
```

### 데이터 스키마 (Data Schema) 정의

Firecrawl은 기대하는 데이터의 형태를 알려주면 구조화된 JSON (JSON)을 반환할 수 있습니다. 우리는 Pydantic 모델을 계약처럼 사용합니다: "Firecrawl아, 이런 형태의 데이터를 줘."

각 스키마 (schema)가 나타내는 것:

*   **HNComment:** 텍스트, 작성자, 좋아요 (upvotes) 수를 포함하는 단일 댓글
*   **HNPost:** 메타데이터 (metadata)와 상위 댓글 목록을 포함하는 토론 게시물
*   **HNAdvice:** 전체 응답: 주제 + 게시물 목록

```python
class HNComment(BaseModel):
    content: str = Field(description="Comment text")
    author: Optional[str] = None
    points: Optional[int] = None

class HNPost(BaseModel):
    title: str
    hn_url: Optional[str] = None
    points: Optional[int] = None
    num_comments: Optional[int] = None
    top_comments: List[HNComment]

class HNAdvice(BaseModel):
    topic: str
    posts: List[HNPost]
```

이제 프롬프트 (prompt)입니다. 여기서 마법이 일어납니다. 프롬프트는 에이전트를 위한 지침서입니다. 구체적으로 작성하고, 단계를 번호 매기며, 무엇을 건너뛸지 알려주세요.

### 1단계: Firecrawl /agent로 해커 뉴스 스크랩

여기서 마법이 일어납니다. Firecrawl 에이전트에게 무엇을 할지 일반 영어 프롬프트로 알려주면, 에이전트가 모든 클릭, 대기, 추출을 처리합니다.

```python
def scrape_hn(topic: str) -> dict:
    app = FirecrawlApp(api_key=FIRECRAWL_API_KEY)

    prompt = f"""
    Find the best advice about "{topic}" from Hacker News.

    1. Go to: https://hn.algolia.com/?q={topic.replace(' ', '+')}&type=story&sort=byPopularity
    2. Find the top 5 most popular discussions
    3. Click into each discussion
    4. Extract: Title, Points, Number of comments, URL
    5. From each, extract top 5 insightful comments (skip jokes)
    """

    result = app.agent(prompt=prompt, schema=HNAdvice.model_json_schema())
    return result.data if hasattr(result, 'data') else result
```

여기서 일어나는 일을 자세히 살펴보겠습니다.

1.  **Firecrawl 초기화 (Initialize):** API 키로 앱 생성
2.  **프롬프트 구축 (Build the prompt):** 에이전트를 위한 지침서입니다.
    *   HN Algolia 검색 페이지에서 시작
    *   상위 5개 토론 찾기
    *   각 토론 클릭
    *   게시물 메타데이터 (metadata) + 상위 5개 댓글 추출
    *   농담은 건너뛰고, 실제 조언에 집중
3.  **에이전트 호출 (Call the agent):** 프롬프트와 스키마 (schema)를 `app.agent()`에 전달
4.  **응답 처리 (Handle the response):** 오류 확인, 데이터 추출
5.  **결과 출력 (Print results):** 어떤 데이터가 반환되었는지 정확히 볼 수 있도록 원시 JSON (JSON) 표시

### 우리가 실제로 얻은 것

이번 실행에서 얻은 수치입니다.

이미지를 전체 크기로 보려면 Enter를 누르거나 클릭하세요.

다음은 에이전트가 반환한 내용의 작은 샘플 (sample)입니다.

```
------------------------------------------------------------
   RAW DATA FROM FIRECRAWL:
------------------------------------------------------------
{
  "topic": "career advice",
  "posts": [
    {
      "title": "Career advice nobody gave me: Never ignore a recruiter",
      "hn_url": "https://news.ycombinator.com/item?id=30163676",
      "points": 790,
      "num_comments": 619,
      "top_comments": [
        {
          "content": "I also recommend a response to every recruiter, but you don't need to explain your privilege, you don't need to suck up to them, and you don't need to justify your actions. \"Hey ____. Before we move forward, can you provide me with the company name, a job description, and the expected compensation. Regards\"",
          "author": "Spinnaker_",
          "points": null
        },
        {
          "content": "Bad career advice I received early in my career: Don't talk compensation until late in the interviewing process after you've already convinced them to hire you. Compensation is the first thing I bring up now. \"I currently make X salary, Y annual bonus, and Z equity. This position will need to exceed all 3 by at least 20% before I even consider it. Does that sound doable? If not, let's not waste any more of each other's time.\"",
          "author": "DebtDeflation",
          "points": null
        },
        {
          "content": "Ignore recruiter if they: * are a part of a large firm * use multiple fonts, sizes, or any color in their emails * send an email _and_ an InMail * text or call you * jokingly or seriously refer to themselves as a stalker * automatically substitute in your skills or past company name * ask for your resume when they can obviously download the LinkedIn pdf * don't disclose comp * don't disclose the company name * use tracking pixels or redirect links * send an automated sequence of follow-up emails (4 follow-ups = bot) Write them back if they seem like a human! \"Not interested at this time, but let's keep in touch. Thanks for your time\" should do.",
          "author": "beeskneecaps",
          "points": null
        },
        {
          "content": "The first thing many recruiters will want to do is \"hop on a call\". Resist this urge. In fact, don't even give them your phone number. Force them to use email to contact you. A phone call is a good way of wasting your time. If you actually need to call them on the phone, call them. There are lots of techniques recruiters will use to waste your time. One common one is if pressed on compensation range you'll get the answer that it's \"competitive\". Use a template like this to simply filter out time-wasters. If they want to get on a call, resist giving concrete details or otherwise just give you bad vibes, just stop responding. They can't call you. They don't have your number. Move on.",
          "author": "cletus",
          "points": null
        },
```

## 2단계: LLM 분석

25개의 댓글도 좋지만, 여전히 텍스트의 장벽입니다. 일부 조언은 중복되고, 일부는 모순됩니다. 이 모든 것을 읽고 "실제로 중요한 것은 이것이다"라고 말해줄 똑똑한 친구가 필요합니다. 여기서 똑똑한 대규모 언어 모델 (Large Language Model, LLM)이 유용하게 사용됩니다.

```python
def analyze(data: dict) -> str:
    advice_text = ""
    for i, post in enumerate(data.get('posts', []), 1):
        advice_text += f"\n### {post.get('title', '')}\n"
        for comment in post.get('top_comments', []):
            advice_text += f"\n{comment.get('author', 'anon')}: {comment.get('content', '')[:500]}\n"

    client = OpenAI(api_key=WANDB_API_KEY, base_url="https://api.inference.wandb.ai/v1")

    response = client.chat.completions.create(
        model=LLM_MODEL,
        max_tokens=2000,
        messages=[
            {"role": "system", "content": "Summarize HN advice into Top 10 actionable tips."},
            {"role": "user", "content": f"Analyze advice about '{data.get('topic')}':\n{advice_text}\n\nCreate Top 10 Tips list."}
        ]
    )

    return response.choices[0].message.content

# 실행
data = scrape_hn(TOPIC)
tips = analyze(data)
print(tips)
```

우리는 LLM이 몇 가지 중요한 사항에 집중하도록 만들고 싶습니다.

1.  **긴 댓글 자르기 (Truncate long comments):** `content[:500]`을 주목하세요. 일부 HN 댓글은 에세이 (essay) 수준입니다. 잘라내기는 토큰 제한 (token limits) 내에 유지하고 LLM이 핵심 메시지에 집중하도록 강제합니다.
2.  **분석 프롬프트 구축 (Build the analysis prompt):** LLM에게 우리가 원하는 것을 정확히 알려줍니다.
    a) 댓글의 조언만 사용 (환각 (hallucination) 없음)
    b) 짧은 설명과 함께 상위 10개 목록 생성
    c) 핵심 패턴 (patterns) 식별
    d) 실용적인 내용 유지
3.  **LLM 호출 (Call the LLM):** OpenAI 호환 클라이언트 (client)와 함께 W&B Inference를 사용합니다. `base_url` 매개변수를 통해 동일한 OpenAI 라이브러리를 다른 제공업체와 함께 사용할 수 있습니다.
4.  **분석 반환 (Return the analysis):** LLM의 응답이 최종 결과물이 됩니다.

## 모든 것을 하나로 묶기: 메인 함수 (Main Function)

이제 두 단계를 결합하는 Main 메서드 (method)를 만들 것입니다.

```python
def main():
    print("\n" + "=" * 60)
    print("   HACKER NEWS 최고의 조언 추출기")
    print("   (단계별 버전)")
    print("=" * 60)

    # 1단계
    firecrawl_data = step1_scrape_hn(TOPIC)

    print("\n" + "=" * 60)
    input("👆 위 1단계 결과를 검토하세요. 2단계로 진행하려면 ENTER를 누르세요...")

    # 2단계
    llm_analysis = step2_analyze_with_llm(firecrawl_data)

    # 보고서 저장
    if firecrawl_data and llm_analysis:
        report = f"""# 해커 뉴스의 최고의 조언
**주제:** {TOPIC}
**날짜:** {datetime.now().strftime("%B %d, %Y")}

---

{llm_analysis}
"""
        filename = f"hn_advice_{datetime.now().strftime('%Y%m%d_%H%M%S')}.md"
        with open(filename, "w") as f:
            f.write(report)
        print(f"\n✅ 다음으로 저장됨: {filename}")

    return firecrawl_data, llm_analysis


if __name__ == "__main__":
    main()
```

## 우리가 얻은 결과

다음은 출력 결과의 샘플입니다.

1.  **적극성 및 투명성** – HN (해커 뉴스) 사용자들은 나중 단계까지 기다리기보다 모든 리크루터에게 회신하고 필수 세부 정보 (회사, 직무, 급여)를 일찍 요구해야 한다고 강조합니다.
2.  **시간 절약형 필터** – 낭비되는 노력을 피하기 위해 이메일로만 소통하는 방법과 리크루터의 위험 신호 (red-flags) 체크리스트 (checklist)가 반복적으로 권장됩니다.
3.  **양보다 질** – 무작정 지원하기보다는, 자신이 깊이 이해하고 간결한 가치 제안 (value proposition)을 할 수 있는 소수의 회사에 집중하세요.
4.  **네트워크 활용** – 추천, 내부 옹호자, 전문적인 태도 (복장)는 일관되게 신뢰도를 높이고 채용을 가속화합니다.
5.  **보상 규율** – 현재 급여와 요구하는 인상분을 미리 밝히세요. 연봉 협상을 금기시되는 주제가 아닌 데이터 기반 토론으로 다루세요.

전체 출력 결과는 기술에 구애받지 않는 (technology agnostic) 기술 구축, 낮은 소모율 (burn rate) 유지, 회사보다 팀 선택, 필요하다고 생각하는 것보다 조금 더 잘 차려입는 것에 대한 팁들로 이어졌습니다.

## 이것이 실제로 작동하는 이유

이러한 결과는 어려운 방법 대신 Firecrawl을 사용하여 얻을 수 있는 세 가지 실제 이점을 보여줍니다.

### 1. LLM은 깨끗한 텍스트를 얻습니다.

Firecrawl은 순수한 마크다운 (markdown)을 제공합니다. 무작위 HTML 태그, 탐색 메뉴, "뉴스레터 구독!" 팝업이 없습니다. 이것이 없으면 LLM에 다음과 같은 쓰레기 데이터를 공급하게 됩니다.

```html
<div class="comment"><span class="user">...</span>
<div class="votearrow">...</div>
<span class="comment-text">실제 조언 여기</span>
```

이러한 HTML 잡동사니는 3~5배 더 많은 토큰 (tokens)을 사용하고 LLM을 혼란스럽게 합니다. 결국 환각 (hallucinated) 통찰력이나 완전히 놓친 요점을 얻게 됩니다.

### 2. 댓글 스레딩 (Threading)이 제대로 작동합니다.

Firecrawl은 HN (해커 뉴스)의 중첩된 댓글 구조를 그대로 유지하므로, LLM은 어떤 조언이 추천 (upvoted)받고 어떤 조언이 반박되는지 파악할 수 있었습니다. 직접 스크래핑 (scraping)하면 모든 컨텍스트 (context)가 평탄화되거나, 각 웹사이트의 이상한 HTML 구조에 맞춰 사용자 정의 파서 (parsers)를 작성해야 합니다.

### 3. 지루한 모든 인프라 (Infrastructure)를 건너뛸 수 있습니다.

Firecrawl이 없다면, 이 튜토리얼은 절반이 다음 내용에 할애되었을 것입니다.

*   BeautifulSoup으로 HTML 파싱 (Parsing)
*   요청 실패 시 재시도 로직 (retry logic) 추가
*   차단되지 않도록 속도 제한 (rate limiting)
*   다른 웹사이트를 위한 다른 추출기 작성
*   페이지네이션 (pagination) 처리

이는 웹사이트가 디자인을 변경할 때마다 쉽게 중단되는 100줄 이상의 코드입니다. Firecrawl은 이 모든 것을 단일 API 호출 (API call)로 전환했습니다.

우리 AI Agent에 대한 솔직한 평가:

이미지를 전체 크기로 보려면 Enter를 누르거나 클릭하세요.

완벽한가요? 아닙니다. 619개의 댓글을 읽는 것보다 나은가요? 분명히 그렇습니다.

## 문제가 발생했을 때 (그리고 해결 방법)

이것을 구축하면서 성공 사례보다 실패 모드 (failure modes)에 대해 더 많이 배웠습니다. 무엇이 고장 났고 어떻게 고쳤는지 설명합니다.

리크루터 조언이 우리 결과에서 지배적이었다는 점을 주목하셨나요? 이는 한 게시물의 댓글이 다른 게시물보다 강력해서 그들을 압도했기 때문입니다.

이것을 고치기 위해 LLM 프롬프트 (prompt)에 다음 줄을 추가할 수 있습니다.

```
"Include at least one tip from EACH of the 5 posts.
Don't let any single discussion dominate the list."
```

이렇게 하면 LLM이 가장 목소리 큰 토론뿐만 아니라 모든 토론에서 지혜를 이끌어내도록 강제합니다.

## 다른 주제 시도하기

이 도구의 아름다움은? 한 줄만 변경하면 어떤 주제든 지혜를 추출할 수 있습니다. 잘 작동하는 몇 가지 주제는 다음과 같습니다.

이미지를 전체 크기로 보려면 Enter를 누르거나 클릭하세요.

## 내가 배운 것 + 다음에 구축할 것

여러분은 수백 개의 댓글을 읽고 핵심 내용을 요약해 주는 개인 연구 비서 (research assistant)를 방금 구축했습니다. 두 번의 API 호출, 약 50줄의 코드, 실행당 몇 센트의 비용으로 말이죠.

Firecrawl의 Agent는 Selenium 스크립트 (script)를 작성하는 것보다 더 나은 일을 해야 할 사람들을 위한 웹 스크래핑이라고 할 수 있습니다. 원하는 것을 설명하고, 구조화된 데이터를 다시 받으세요. 이것을 LLM과 결합하면 거의 모든 것을 가리킬 수 있는 지식 추출 AI를 갖게 됩니다.

다음에 무엇을 구축할지에 대한 아이디어를 원하세요?

*   채용 게시판을 스크랩 (scrape)하여 역할별 급여 범위 추출
*   Reddit에서 제품 리뷰를 가져와 감정 요약
*   컨퍼런스 (conference) 강연 토론을 추출하여 가장 추천받는 세션 (sessions) 찾기
*   자신만의 "인터넷은 X에 대해 어떻게 생각하는가?" 도구 구축

## 자주 묻는 질문 (FAQ)

**Firecrawl이란 무엇인가요?**

Firecrawl은 "에이전트 (agent)" 모드를 가진 웹 스크래핑 도구로, CSS 선택자 (CSS selectors)와 브라우저 자동화 (browser automation) 코드를 작성하는 대신 일반 영어로 스크래핑 작업을 설명할 수 있습니다. "HN (해커 뉴스)으로 이동하여 인기 토론을 찾고 댓글을 추출하라"고 지시하면, 클릭, 대기, 탐색을 자동으로 처리합니다. 에이전트는 정의한 Pydantic 스키마 (schema)와 일치하는 구조화된 JSON (JSON)을 반환합니다.

**Selenium이나 웹 스크래핑에 대해 알아야 하나요?**

아니요. 그것이 Firecrawl을 사용하는 핵심 이유입니다. 기존 웹 스크래핑은 헤드리스 브라우저 (headless browsers) 관리, 타이밍 문제 처리, 웹사이트 업데이트 시 작동하지 않는 CSS 선택자 작성, 재시도 로직 (retry logic) 구축을 요구합니다. Firecrawl 에이전트는 이 모든 것을 추상화합니다. 자연어 프롬프트 (prompt)를 작성하면 에이전트가 구현 세부 사항을 파악합니다.

**비용은 얼마나 드나요?**

Firecrawl의 무료 티어 (free tier)에는 500페이지 크레딧 (credits)이 포함됩니다. 각 실행은 대략 6페이지 (검색 결과 페이지 1개 + 토론 페이지 5개)를 사용하므로, 약 80회 무료 실행으로 테스트할 수 있습니다. 그 이후에는 월 $16부터 시작하는 유료 요금제 (paid plans)가 3,000 크레딧을 제공합니다. LLM 비용은 제공업체에 따라 다르며, W&B Inference는 분석당 약 $0.01를 청구합니다. 무료 티어를 완료한 후 실행당 총 비용은 약 $0.042입니다.

**다른 LLM을 사용할 수 있나요?**

네. 코드는 OpenAI의 클라이언트 라이브러리 (client library)를 사용하며, 이는 모든 OpenAI 호환 API와 작동합니다. OpenAI GPT-5, Anthropic Claude (어댑터 (adapter) 사용), OpenRouter 또는 LiteLLM을 통한 로컬 모델 (local models)로 교체할 수 있습니다. OpenAI 클라이언트 초기화 시 `base_url`과 `api_key` 매개변수만 변경하면 됩니다.

**해커 뉴스 (Hacker News) 외 다른 웹사이트도 스크랩할 수 있나요?**

네, 하지만 대상 웹사이트의 구조에 맞춰 프롬프트 (prompt)와 Pydantic 스키마 (schemas)를 수정해야 합니다. 동일한 패턴은 Reddit, Stack Overflow, Dev.to, Product Hunt 또는 모든 공개 웹사이트에 적용됩니다. Firecrawl은 자바스크립트 (JavaScript)로 렌더링 (rendered)된 콘텐츠 (content)를 자동으로 처리하므로, 동적 웹사이트도 잘 작동합니다. 공격적인 봇 방지 조치 (anti-bot measures)가 있는 웹사이트 (예: LinkedIn)는 실패할 수 있습니다.

**실행하는 데 얼마나 걸리나요?**

Firecrawl의 스크래핑 (scraping)은 탐색해야 하는 페이지 수에 따라 30~90초가 걸립니다. LLM 분석은 5~10초가 추가됩니다. 총 실행 시간은 일반적으로 시작부터 끝까지 60~100초입니다. cron 작업 (cron jobs) 또는 GitHub Actions를 사용하여 주간 자동 요약을 위해 스케줄 (schedule)에 따라 실행할 수 있습니다.

**Firecrawl이 웹사이트를 스크랩할 수 없으면 어떻게 되나요?**

Firecrawl은 인증 (authentication)이 필요 없는 공개 웹사이트에서 가장 잘 작동합니다. 공격적인 봇 감지 (anti-bot detection) 기능이 있는 웹사이트는 요청을 차단할 수 있습니다. 해커 뉴스 (Hacker News)는 개방적이며 봇 차단 조치를 사용하지 않으므로 안정적으로 작동합니다. 스크래핑이 실패하면 Firecrawl은 응답에 오류를 반환하며, 코드는 분석을 건너뛰어 우아하게 처리합니다.

**댓글을 500자로 자르는 이유는 무엇인가요?**

두 가지 이유가 있습니다. 첫째, 일부 HN 댓글은 2000단어가 넘고, 전체 에세이 (essay)를 LLM에 공급하면 토큰 제한 (token limits)에 빠르게 도달할 것입니다. 둘째, 자르기는 LLM이 세부 사항에 얽매이지 않고 핵심 메시지를 추출하도록 강제합니다. 전체 댓글이 필요한 경우 `[:500]` 슬라이스 (slice)를 제거할 수 있지만, 더 높은 토큰 비용을 예상해야 합니다.

**스케줄에 따라 실행할 수 있나요?**

네. 코드를 cron 작업 (cron job) 또는 GitHub Action에 래핑 (wrap)하여 매주 실행할 수 있습니다. 한 번의 실행으로 여러 주제를 처리하고 결과를 자신에게 이메일로 보낼 수 있습니다. 이 코드는 무상태 (stateless)이므로 스케줄링 (scheduling)에 추가 인프라 (infrastructure)가 필요하지 않으며, 타이머 (timer)를 설정하고 스크립트를 가리키면 됩니다.

**1단계와 2단계 사이의 일시 정지는 무엇을 위한 것인가요?**

디버깅 (Debugging)을 위한 것입니다. Firecrawl이 비어 있거나 잘못된 형식의 데이터를 반환하면, LLM API 호출을 낭비하기 전에 이를 확인할 수 있습니다. `input()` 줄을 통해 원시 JSON (JSON)을 검토하고 1단계가 실패한 경우 중단할 수 있습니다. 스케줄에 따라 스크립트를 무인으로 실행하는 경우 이 줄을 제거하세요.

웹 스크래핑
AI 에이전트
머신러닝
인공지능

120

Level Up Coding에 게시됨
팔로워 30.6만 명
· 최근 게시: 19시간 전

코딩 튜토리얼 및 뉴스. 개발자 홈페이지: gitconnected.com && skilled.dev && levelup.dev

팔로잉
작성자: Mostafa Ibrahim
팔로워 1.5천 명
· 팔로잉 346명

소프트웨어 엔지니어 (Software Eng.), 유니버시티 칼리지 런던 컴퓨터 과학 졸업. 헬스케어 분야 머신러닝 (Machine Learning)에 열정적. AI 분야 최고 작가.

팔로우
아직 반응 없음
BongBongOogle

어떻게 생각하시나요?

취소
응답
Mostafa Ibrahim 및 Level Up Coding의 다른 글

Towards AI
작성자: Mostafa Ibrahim
대부분의 다중 에이전트 시스템이 실패하는 이유 — 그리고 실제로 작동하는 것은 무엇인가
다중 에이전트 시스템은 지금 어디에나 있습니다. 모든 것을 하나의 AI에 의존하는 대신, 이러한 아키텍처는 작업을 더 작은…
1일 전

Level Up Coding
작성자: Christian Bernecker
순수 Python으로 처음부터 AI Agent 구축하기
이론에서 구현까지: Python으로 견고하고 자체 수정 가능한 AI Agent를 처음부터 구축하기
2월 17일

Level Up Coding
작성자: Pushkar Singh
Git 사용 방식을 조용히 바꾼 6가지 명령어
더 큰 코드베이스 (codebases)에서 작업할 때 디버깅 (debugging) 프로세스, 커밋 (commit) 규율, 자신감을 조용히 향상시킨 명령어들.
2월 15일

The Techlife
작성자: Mostafa Ibrahim
서평: Scikit-learn 및 TensorFlow를 이용한 실습 머신러닝
이 책에 대한 포괄적인 개요, 책을 읽고 배운 점, 그리고 이 책을 구매해야 하는지 여부
2022년 10월 14일
Mostafa Ibrahim의 모든 글 보기
Level Up Coding의 모든 글 보기
Medium 추천

Level Up Coding
작성자: Gaurav Shrivastav
종단 간 (End-to-End) 다중 에이전트 AI 심층 연구 계획 앱 구축
LangGraph, Tavily, GPT-4o를 사용하여 계획 우선 (planning-first) 다중 에이전트 연구 파이프라인 (pipeline)을 설계하여 기반이 있는 장문의 보고서를 생성한 방법.
2월 21일

Towards AI
작성자: Florian June
Cog-RAG: 검색 전에 생각하는 RAG (Retrieval-Augmented Generation)에 두뇌 부여하기
RAG (Retrieval-Augmented Generation)는 이제 LLM이 기반을 유지하도록 돕는 표준적인 방법입니다. 기본적인 아이디어는 익숙합니다: 문서를 …으로 나누기.
2월 17일

Generative AI
작성자: Jim Clyde Monge
OpenClaw에 대해 아마 몰랐을 5가지 흥미로운 사실
모든 사용자가 알아야 할 OpenClaw에 대한 5가지 사실입니다.
2월 19일

AI Advances
작성자: Jose Crespo, PhD
Anthropic이 비트코인을 죽이고 있다
AI 기반 통화는 이미 존재하며 — 평범한 곳에 숨어 암호화폐보다 6자리수 더 나은 성능을 보인다.
2월 17일

Coding Nexus
작성자: Minervee
토큰 낭비 중단: 워크플로 메모리를 사용하여 LLM을 실제로 똑똑하게 만들기
AI Agent "세뇌" 이해하기 (모델 변경 없이)
2025년 11월 8일

Data Science Collective
작성자: Farhad Malik
연구에서 거래 실행까지: 다중 에이전트 투자 플랫폼 구축
Google ADK, A2A 및 MCP를 사용하여 다중 에이전트 투자 시스템을 구축하고 설계하기
2월 20일
더 많은 추천 보기

도움말
상태
정보
채용
언론
블로그
개인정보처리방침
규칙
약관
텍스트 음성 변환