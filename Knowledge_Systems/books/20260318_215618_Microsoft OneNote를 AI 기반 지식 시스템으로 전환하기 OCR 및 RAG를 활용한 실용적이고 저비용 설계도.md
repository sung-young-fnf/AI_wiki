# 번역 기사

- **원본 URL:** https://medium.com/towards-artificial-intelligence/turning-microsoft-onenote-into-an-ai-powered-knowledge-system-a-practical-low-cost-blueprint-32d8082c6d73
- **번역 일시:** 2026-03-18 21:56:18

---

# Microsoft OneNote를 AI 기반 지식 시스템으로 전환하기: OCR 및 RAG를 활용한 실용적이고 저비용 설계도

우리는 엔터프라이즈 AI를 구축하고 배운 것을 가르칩니다. Towards AI Academy에서 10만 명 이상의 AI 전문가와 함께하세요. 무료: 6일 에이전트형 AI 엔지니어링 이메일 가이드: https://email-course.towardsai.net/

**발행물 팔로우**

**멤버 전용 기사**

**닐 파텔**
16분 소요 · 2026년 2월 27일

---

## 고급 기술 개요 (High Level Tech Overview)

많은 조직에서 Microsoft OneNote는 조용히 중앙 지식 저장소(knowledge repository)가 되고 있습니다. 회의록, 스캔된 문서, PDF, Excel 파일, 이미지, 이메일 등이 시간이 지남에 따라 축적되어 풍부하지만 대부분 비정형적인 기관의 기억 보관소를 형성합니다.

문제는 데이터 부족이 아닙니다.
이 데이터가 기계 판독 불가능(machine-readable)하고, 의미론적으로 검색 불가능하며(semantically searchable), 대규모로 쉽게 분석할 수 없다는 것입니다.

이 글은 OneNote를 정적인 노트 필기 도구에서 지능형 지식 시스템으로 전환하는 계층형 아키텍처(layered architecture)를 다룹니다. Microsoft Graph, Azure Document Intelligence, 벡터 임베딩(vector embeddings) 및 GPT-4o를 결합하여 비정형 콘텐츠를 구조화되고 추적 가능하며 대화형 지능으로 변환합니다.

이 예시는 OneNote에 중점을 두지만, 동일한 아키텍처는 산업 전반에 걸쳐 Microsoft 365 환경에 적용될 수 있습니다.

여기서 시연된 OCR과 의미론적 검색(semantic retrieval) 패턴은 광범위한 분석 및 자동화 이니셔티브의 기반을 형성합니다.

더 중요한 것은, 이것은 혁신적인 인공지능(AI) 변환이 아니라는 점입니다.
조직이 다음을 위해 구현할 수 있는 반복 가능하고 저비용의 개념 증명(Proof-of-Concept, POC) 프레임워크입니다.

*   AI 가치를 신속하게 검증합니다.
*   측정 가능한 비즈니스 영향을 시연합니다.
*   전체 규모 배포 전에 경영진의 신뢰를 구축합니다.

대부분의 AI 개념 증명(POC) 이니셔티브는 상당한 자본 투자를 필요로 하지 않습니다. 많은 경우, 잘 설계된 POC는 몇백 달러로 구현할 수 있으며, 기존 엔터프라이즈 라이선스 계약이 없는 경우에도 몇천 달러를 넘는 경우는 거의 없습니다. POC의 목표는 최종 프로덕션 시스템을 구축하는 것이 아니라, 실현 가능성, 측정 가능한 영향 및 전략적 가치를 입증하는 것입니다.

패턴은 간단합니다.

**추출(Extract) → 정규화(Normalize) → 이해(Understand) → 검색(Retrieve) → 대화(Converse)**

사려 깊게 구현되면 이 패턴은 검색, 분석, 자동화 및 의사 결정 지원의 기반이 됩니다.

이 아이디어를 현실로 구현하기 위해 아키텍처는 네 개의 모듈형 계층(modular layers)으로 구성됩니다. 각 계층은 이전 계층 위에 지능과 유용성을 구축하여 확장 가능하고 탄력적이며 프로덕션 준비가 된 기반을 제공합니다.

## 기술 스택 (한눈에) (Technology Stack (At a Glance))

아키텍처 자체는 의도적으로 도구에 구애받지 않지만, 이 구현은 개념 증명(POC)에 적합한 현대적이고 비용 효율적인 기술 스택(technology stack)을 활용합니다.

*   **Microsoft Graph API**: OneNote 페이지, 메타데이터 및 첨부 파일 URL을 원본에서 직접 추출하는 데 사용됩니다. 많은 조직의 경우 Graph API 접근은 이미 Microsoft 365 라이선스에 포함되어 있어 추가 비용이 최소화됩니다.
*   **Azure Document Intelligence** (사전 구축 레이아웃 및 문서 모델 (Prebuilt Layout & Document models)): PDF, 이미지 및 스캔된 문서에서 텍스트, 표, 키-값 쌍(key-value pairs)의 광학 문자 인식(OCR) 및 구조화된 추출을 수행합니다.
*   **ChromaDB** (벡터 저장소 (Vector Store)): 계층 전반에 걸쳐 임베딩(embeddings)과 메타데이터(metadata)를 저장하는 데 사용되는 오픈 소스 벡터 데이터베이스입니다.
*   **Azure OpenAI Embeddings** (ai-coe-embeddings-3-large): 정확한 유사도 검색(similarity search) 및 Top-K 검색(retrieval)을 지원하기 위해 고품질 의미론적 임베딩(semantic embeddings)을 생성합니다.
*   **GPT-4o**: 검색된 콘텐츠 전반에 걸쳐 출처 추적성(source traceability)을 유지하면서 자연어 질의응답, 요약 및 합성을 지원합니다.

## 1계층: OneNote에서 구조화된 추출 (Structured Extraction from OneNote)

**1계층 개요**

**목표:** OneNote 페이지에서 깨끗하고 추적 가능한 텍스트와 첨부 파일 URL을 안정적으로 가져와, 완전한 출처 추적성을 갖춘 검색 가능한 저장소에 저장합니다.

1계층은 전체 시스템의 기반입니다. 이 계층은 의도적으로 "AI 중심적(AI-heavy)"이지 않습니다. 그 목적은 OneNote를 일관되게 탐색하고, 콘텐츠를 정규화하며, 첨부 파일 포인터를 캡처하고, 모든 것을 영구적인 식별자(durable identifiers)로 저장할 수 있는 탄력적인 수집 파이프라인(ingestion pipeline)을 구축하는 것입니다. 1계층이 불안정하면 OCR, 임베딩, 대화형 검색과 같은 모든 하위 기능이 취약해집니다.

### 상세 보기 (Detailed View)

### Microsoft Graph를 통한 탄력적인 OneNote 수집

Microsoft Graph API를 사용하여 반복 가능한 계층 구조로 OneNote 콘텐츠를 탐색합니다.

*   노트북을 가져옵니다.
*   각 노트북 내의 섹션을 가져옵니다.
*   각 섹션 내의 페이지를 가져옵니다 (@odata.nextLink를 통한 페이지네이션 지원 포함).
*   `/pages/{page_id}/content`를 통해 각 페이지의 원시 HTML 콘텐츠를 가져옵니다.

단순한 API 호출 대신, 모든 요청은 `robust_get()` 헬퍼 함수로 래핑됩니다. 이 함수는 HTTP 429 비율 제한(rate limits)을 감지하고, 부분적인 실행을 방지하기 위해 쿨다운(cooldown) 동작을 적용하며, 200이 아닌 응답에 대해서는 명확한 진단과 함께 빠르게 실패(fail fast)합니다. 이는 엔터프라이즈 수집 파이프라인에서 중요한 고려 사항인 조용한 데이터 손상(silent data corruption)을 방지합니다.

### 멱등성(Idempotency) 및 안전한 재시작

OneNote 파이프라인은 장기 실행될 수 있으며 때때로 중단될 수 있습니다. 재처리를 피하기 위해 각 페이지에는 결정론적 로그 키(deterministic log key)가 할당됩니다.

`notebook_name | page_id | page_title`

해당 키가 `processed_text_underwriting.log`에 존재하면 해당 페이지는 건너뛰어집니다. 이는 다음을 제공합니다.

*   안전한 재시작
*   중복 수집 없음
*   API 사용량 감소
*   결정론적 실행 동작

1계층은 상태를 손상시키지 않고 언제든지 재시작할 수 있도록 설계되었습니다.

### HTML 정규화 및 신호 추출

페이지의 HTML이 검색되면, 이를 하위 계층에 친화적인 텍스트로 정규화합니다.

*   BeautifulSoup를 사용하여 HTML을 파싱합니다.
*   `p`, `h1`, `h2`, `h3`, `li` 태그에서 텍스트 블록을 가져옵니다.
*   블록을 깨끗한 새 줄로 구분된 문서 문자열로 결합합니다.

### 첨부 파일 검색

1계층은 첨부 파일을 다운로드하지 않습니다. 그 책임은 2계층에 있습니다. 대신, 첨부 파일 참조를 식별하고 분류합니다.

HTML은 다음을 스캔합니다.

*   Excel, PDF 및 이메일 MIME 타입(mimetypes)을 포함하는 `<object>` 태그
*   임베디드 이미지(embedded images)를 위한 `<img>` 태그

이들은 유형별 URL 목록으로 분류됩니다.

*   `excel_urls`
*   `pdf_urls`
*   `image_urls`
*   `email_urls`

이 URL들은 메타데이터(metadata) 포인터로 저장되어 2계층이 이를 결정론적으로 다운로드하고 정규화할 수 있도록 합니다.

1계층은 첨부 파일 처리가 아닌 첨부 파일 인식(attachment awareness)을 확립합니다.

### 선택적 보강: 암호 추출

일부 엔터프라이즈 환경에서는 민감한 Excel 첨부 파일이 암호로 보호될 수 있습니다. 페이지에 알려진 암호 패턴(예: 조직의 관례 기반)이 포함되어 있으면, 사용자 지정 정규식(regex) 로직을 사용하여 암호를 추출하고 메타데이터에 저장합니다.

이 보강 단계는 2계층이 수동 개입 없이 보호된 통합 문서(workbooks)를 열 수 있도록 합니다. 구현은 유연하며 조직의 명명 규칙(naming conventions)에 맞게 조정할 수 있습니다.

### ChromaDB 영속성 — 페이지 수준 지능

1계층은 OneNote 페이지당 하나의 레코드를 `onenotes_pipeline` ChromaDB 컬렉션에 기록합니다.

각 레코드에는 다음이 포함됩니다.

```json
metadata = {
"uuid": str(uuid.uuid4()), --> 내부 추적을 위한 무작위 UUID
"page_id": page_id, --> OneNote 페이지 식별자 (추적성에 중요)
"onenote_name": notebook_name,
"page_header": page_title.replace(" ", "_"), --> 밑줄로 정규화된 페이지 제목
"image_urls": str(image_urls), --> Graph에서 호스팅되는 이미지 URL의 문자열화된 목록
"excel_urls": str(excel_urls), --> 감지된 Excel 첨부 파일의 문자열화된 목록
"pdf_urls": str(pdf_urls), --> 감지된 PDF 첨부 파일의 문자열화된 목록
"email_urls": str(email_urls), --> 감지된 이메일 첨부 파일의 문자열화된 목록
"cleaned_html_text": clean_text, --> 편의를 위한 정제된 HTML 텍스트
"summaryontext": response.choices[0].message.content,--> HTML 텍스트를 요약하기 위한 GPT 호출 결과
"source_type": "onenote_page",
"excel_password": excel_password or "" --> 정규식 조건에 따라 OneNote에서 추출된 Excel 암호
}
```

페이지에 텍스트가 없더라도 파이프라인은 대체 요약("요약할 내용 없음.")과 자리 표시자 문자열(placeholder string) 임베딩을 저장하여 레코드가 검색 가능하고 일관성을 유지하도록 합니다.

### 1계층의 아키텍처적 결과

1계층의 완료 시점:

*   모든 OneNote 페이지는 결정론적 식별자(deterministic identity)를 가집니다.
*   깨끗한 텍스트가 추출되고 정규화됩니다.
*   첨부 파일 URL이 검색되고 분류됩니다.
*   메타데이터가 완전한 추적성을 가지고 저장됩니다.
*   페이지 수준 임베딩(page-level embeddings)이 생성됩니다.

아직 첨부 파일은 다운로드되지 않았습니다.
OCR은 수행되지 않았습니다.

1계층은 시스템의 중추를 확립합니다. 즉, 모든 하위 지능이 의존하는 안정적이고 재시작 가능하며 추적 가능한 수집 계층입니다.

## 2계층: 첨부 파일 및 파일 정규화 (Attachments & File Normalization)

**2계층 개요**

**목표:** 모든 OneNote 첨부 파일을 OCR 준비 자산(OCR-ready assets)으로 다운로드, 정규화 및 표준화하는 동시에 원본 페이지로의 완전한 추적성을 유지합니다.

1계층은 OneNote 페이지 콘텐츠에 중점을 두지만, 가장 가치 있는 정보의 대부분은 종종 첨부 파일(PDF, Excel 파일, 이미지, 이메일) 내에 존재합니다. 이러한 파일은 다양한 형식과 구조로 도착하여 균일하게 처리하기 어렵습니다.

2계층은 그 간극을 메우기 위해 존재합니다.

원시 첨부 파일을 OCR에 직접 푸시하는 대신, 이 계층은 모든 파일을 하위 계층의 OCR 및 검색 로직이 안정적으로 처리할 수 있는 일관되고 예측 가능한 표현으로 변환합니다.

### 상세 보기 (Detailed View)

### 정규화가 중요한 이유

실제 엔터프라이즈 환경에서 첨부 파일은 거의 깨끗하지 않습니다. Excel 통합 문서(workbooks)는 여러 시트(일부는 숨겨져 있고, 일부는 구조화되어 있으며, 일부는 자유 형식)를 포함할 수 있습니다. PDF는 수십 페이지에 걸쳐 있을 수 있으며 종종 내부에 임베디드 PDF를 포함합니다. 이미지는 구조적 힌트가 없는 독립형 스캔일 수 있습니다. 이메일은 일관되지 않은 내부 서식을 가진 .msg 또는 .eml 파일로 도착할 수 있습니다.

모든 것이 영구적인 로컬 경로와 결정론적 식별자를 가진 깨끗한 페이지 수준 아티팩트(artifact)가 됩니다.

### 1단계 — 탄력적인 첨부 파일 다운로드

2계층은 1계층 메타데이터에 저장된 첨부 파일 URL을 읽는 것으로 시작합니다.

*   `excel_urls`
*   `pdf_urls`
*   `image_urls`
*   `email_urls`

파일은 엔터프라이즈 안정성을 위해 설계된 `robust_get()` 헬퍼를 사용하여 다운로드됩니다. 이 함수는 HTTP 429 비율 제한(rate limits)을 감지하고 지수 백오프(exponential backoff)로 재시도하며, 중복을 방지하기 위해 완료된 다운로드를 기록하고, 상태를 손상시키지 않고 안전한 재시작을 지원합니다.

이러한 설계는 파이프라인을 멱등성(idempotent)으로 만듭니다. 실행이 중간에 중단되더라도 이전에 처리된 파일을 다시 다운로드하거나 덮어쓰지 않고 안전하게 재개할 수 있습니다.

### 2단계 — 형식별 정규화

첨부 파일이 다운로드되면, 유형에 따라 처리됩니다.

**Excel 파일 → Excel 시트 수준 PDF → 페이지 수준 PDF**

Excel 파일은 가장 높은 구조적 가변성(variability)을 가집니다. 단일 통합 문서(workbook)에는 여러 시트, 숨겨진 시트, 표, 주석 및 일관되지 않은 레이아웃이 포함될 수 있습니다.

Excel을 표준화하려면:

*   통합 문서가 열립니다 (해당하는 경우 1계층에서 추출된 암호를 전달).
*   각 시트가 PDF로 내보내집니다.
*   각 시트 수준 PDF가 단일 페이지 PDF로 분할됩니다.

**PDF → 페이지 수준 PDF (임베디드 PDF 포함)**

PDF는 Excel 파일과 유사한 정규화 과정을 따르지만, 복잡성이 한 층 더 있습니다. 엔터프라이즈 문서 세트에서는 PDF 내부에 다른 PDF가 임베디드(embedded)되어 있는 경우가 흔합니다. 예를 들어, 부록, 지원 문서, 스캔된 증빙 자료 또는 동일 파일 내에 패키지된 중첩 첨부 파일 등이 있습니다.

외부 PDF를 단일 아티팩트로 취급하고 임베디드 문서를 무시하면, 조용히 콘텐츠를 놓칠 위험이 있습니다.

이를 방지하기 위해 2계층은 구조화된 감지 단계(structured detection step)를 수행합니다.

*   PDF를 다운로드합니다.
*   임베디드 PDF 객체를 검사합니다.
*   주 PDF를 단일 페이지 PDF로 분할합니다.
*   임베디드 PDF를 추출합니다.
*   임베디드 PDF도 단일 페이지 아티팩트로 분할합니다.

임베디드 PDF를 자체 처리 경로로 분리하는 이유는 구조적인 것뿐만 아니라 운영적인 측면 때문입니다.

엔터프라이즈 환경, 특히 개념 증명(POC) 또는 프로덕션 출시 시 이해 관계자들은 파이프라인이 제공된 모든 문서를 성공적으로 추출했는지 확인하기 위해 수동 유효성 검사(human validation)를 수행하는 경우가 많습니다. 임베디드 PDF를 명시적으로 감지하고 추출함으로써, 중첩된 아티팩트가 간과되지 않았음을 확신 있게 보여줄 수 있습니다.

**이미지 → 직접 OCR 자산**

독립형 이미지(.jpg, .png)는 구조적 변환이 필요하지 않습니다. 이들은 원래 노트북 및 페이지에 맞춰 결정론적 파일명(deterministic filenames)으로 다운로드되고 저장됩니다.

**이메일 → HTML 미리보기 + 구조화된 텍스트**

이메일은 약간 다르게 처리됩니다. .msg 및 .eml 파일은 Python 유틸리티를 사용하여 다운로드되고 단순화된 HTML 미리보기(HTML previews)로 변환됩니다. 이러한 미리보기는 로컬에 저장되며 나중에 3계층에서 파싱됩니다.

### 3단계 — ChromaDB로의 완전한 추적성

정규화 후, 각 아티팩트는 `layer2_attachments` ChromaDB 컬렉션에 기록됩니다.

2계층은 임베딩(embeddings)을 생성하지 않습니다. 대신, 구조화된 자산 레지스트리(structured asset registry) 역할을 합니다. 각 레코드에는 다음이 저장됩니다.

*   결정론적 로컬 파일 경로 (예: `pdf_split_file_path`, `sheet_pdf_path`)
*   분할된 아티팩트의 페이지 번호
*   원본 유형 (`excel`, `pdf`, `pdf-embedded`, `image`, `email`)
*   원본 파일명
*   첨부 파일 인덱스
*   원본 URL
*   전체 1계층 메타데이터
*   진정한 외래 키(foreign keys) (`layer1_id`, `layer1_key`)
*   전달된 컨텍스트 필드(`cleaned_html_text`, `summaryontext`)
*   암호 메타데이터 (해당하는 경우)
*   수집 타임스탬프

### 아키텍처적 결과

2계층 완료 시점:

*   모든 첨부 파일이 안전하게 다운로드되었습니다.
*   모든 복잡한 문서가 페이지 수준 아티팩트로 분할되었습니다.
*   모든 파일은 결정론적 식별자를 가집니다.
*   모든 아티팩트는 완전한 1계층 계보(lineage)를 유지합니다.
*   아직 임베딩은 생성되지 않았습니다.

2계층은 의도적으로 지능 중립적(intelligence-neutral)입니다.

*   문서를 해석하지 않습니다.
*   콘텐츠를 임베딩하지 않습니다.
*   첨부 파일을 요약하지 않습니다.

대신, 3계층이 OCR과 구조화된 지능을 확신을 가지고 적용할 수 있는 깨끗하고 정규화된 기반(substrate)을 준비합니다.

### 2계층 Excel 첨부 파일용 ChromaDB 메타데이터 예시

```json
metadata = {
"filename": split_filename, // 저장되는 단일 페이지 분할 PDF의 이름
"pdf_split_file_name": split_filename, // 명시적인 분할 PDF 파일명 필드 (하위 일관성 유지에 도움)
"pdf_split_file_path": sp, // 분할 PDF 페이지의 로컬 경로 (OCR 준비 자산)
"pdf_split_page_no": page_no, // 추적성 및 순서 지정을 위한 페이지 번호
"sheet_pdf_path": pdf_path, // 시트별 PDF 경로 (분할 전)
"file_path": local_path, // 원본 다운로드된 Excel 통합 문서의 경로
"source_type": "excel", // 이 레코드가 Excel 첨부 파일 파이프라인에서 왔음을 선언합니다. 이것은 변경됩니다: IMAGE, PDF, PDF-EMBEDDED, EMAIL

#### Cross-layer identity / traceability ####
"notebook_name": notebook, // 파일 시스템에 사용된 정제된 노트북 폴더 이름
"notebook_name_raw": notebook_raw, // 1계층의 원본 노트북 이름 (강력한 조인을 위해 유지)
"onenote_name": notebook_raw, // 다른 파이프라인 코드와의 호환성을 위한 미러 필드
"page_header": page_header, // 폴더/경로에 사용된 정제된 OneNote 페이지 제목
"page_header_raw": page_raw, // 원본 1계층 페이지 제목 (정확한 출처 레이블)
"page_id": page_id, // OneNote 페이지 식별자 (1계층으로의 중요한 외래 키)
"uuid": uuid_val, // 추적을 위해 1계층에서 복사된 (또는 이전에 생성된) UUID

#### True foreign keys ####
"layer1_id": l1_id, // 이 첨부 파일이 속한 1계층 페이지의 정확한 ChromaDB ID
"layer1_key": l1_key, // 사람/디버그 복합 키 (로그 및 문제 해결에 유용)

#### Attachment-level traceability ####
"attachment_index": idx, // Excel URL 목록에서 이 파일이 해당하는 첨부 파일의 인덱스
"original_excel_filename": original_filename, // Graph / OneNote 메타데이터에서 받은 원본 파일명
"source_url": file_url, // Excel 파일의 원본 Microsoft Graph 다운로드 URL

#### Carry-over fields from Layer 1 ####

"cleaned_html_text": cleaned_text, // 나중에 OCR 출력을 문맥화할 수 있도록 1계층에서 복사됨
"summaryontext": summary_text, // 노트북 수준 인사이트를 위해 1계층 GPT 요약이 2계층으로 전달됨
"excel_password": excel_password, // 1계층 정규식 추출 암호 (보호된 통합 문서 열기에 사용)

#### Ingestion timing ####
"ingested_at": time.time(), // 이 분할 PDF 레코드가 생성된 타임스탬프
}
```

## 3계층: OCR 및 임베딩 (OCR & Embeddings)

**3계층 개요**

**목표:** 정규화된 첨부 파일 자산(attachment assets)을 구조화되고 검색 가능한 지능으로 변환하는 동시에 완전한 교차 계층 추적성(cross-layer traceability)을 유지합니다.

3계층은 세 가지 주요 작업을 수행합니다.

*   2계층에서 첨부 파일 위치 및 컨텍스트를 검색합니다.
*   Azure Document Intelligence API를 사용하여 구조화된 콘텐츠를 추출합니다.
    *   `prebuilt-layout`
    *   `prebuilt-document`
*   임베딩(Embeddings)을 생성하고 풍부한 지능을 저장합니다.
    *   `ai-coe-embeddings-3-large`

### OCR + 임베딩이 기본적인 이유

문서가 OCR 처리되고, 구조화되고, 임베딩되면 더 이상 단순한 파일이 아니라 구조화된 데이터 자산(structured data assets)이 됩니다.

여기에서 조직은 다음을 수행할 수 있습니다.

*   추출된 필드로부터 구조화된 대시보드를 구축합니다.
*   분류 모델(classification models)을 훈련합니다.
*   프로젝트 또는 클라이언트 전반의 패턴을 감지합니다.
*   워크플로우 라우팅(workflow routing)을 자동화합니다.
*   의미론적 신호(semantic signals) 기반의 알림을 트리거합니다.

대화형 검색(conversational retrieval)은 단지 첫 번째 가시적인 사용 사례일 뿐입니다. 더 깊은 가치는 비정형 엔터프라이즈 콘텐츠를 분석 가능한 데이터로 변환하는 데 있습니다.

### 1. 2계층에서 첨부 파일 위치 및 컨텍스트 검색

2계층은 이미 모든 첨부 파일을 정규화하고 다음을 저장했습니다.

*   로컬 파일 경로 (예: `file_path`, `pdf_split_file_path`, `sheet_pdf_path`)
*   노트북 및 페이지 식별자
*   원본 URL
*   1계층에서 온 정제된 HTML 페이지 컨텍스트
*   OneNote로 다시 연결되는 추적성 키

3계층은 이 정보를 사용하여 디스크에 저장된 파일을 찾고, 각 파일이 어떤 노트북과 페이지에 속하는지 이해하며, 파이프라인 전체에서 완전한 교차 계층 추적성(cross-layer traceability)을 유지합니다.

### 2. Azure Document Intelligence (이중 모델 전략)

3계층에서는 Azure 포털의 Azure AI 서비스 → Document Intelligence 아래에서 관리형 서비스(managed service)로 제공되는 Azure AI Document Intelligence (이전 명칭: Azure Form Recognizer)를 활용합니다.

REST API를 통해 노출되는 두 가지 사전 구축 모델(prebuilt models)을 특별히 사용합니다.

`prebuilt-layout` 모델은 선 위치 지정(line positioning) 및 레이아웃 흐름(layout flow)을 포함하여 문서의 완전한 텍스트 콘텐츠를 캡처하는 데 중점을 둡니다. 이는 고정밀 OCR 추출에 이상적입니다.

`prebuilt-document` 모델은 레이아웃 추출을 기반으로 하며 다음과 같은 구조화된 정보를 식별하는 데 최적화되어 있습니다.

*   키-값 쌍 (예: “보험 번호: 12345”)
*   표
*   양식 스타일 필드(Form-style fields)

우리의 보험 인수 문서(underwriting documents)의 경우, 두 모델을 모두 호출하여 다음을 캡처했습니다.

*   완전한 문서 텍스트
*   구조화된 양식 필드
*   표 형식 데이터(Tabular data)
*   기계 판독 가능한 JSON 출력

테스트 중에 OCR 추출을 위해 GPT-4o를 평가했습니다. 하지만 Azure Document Intelligence (`prebuilt-layout` 및 `prebuilt-document`)는 이 사용 사례에 대해 일관되게 더 높은 구조적 정확도를 제공했습니다.

### 3. 다중 스레드 이진 처리(Multi-Threaded Binary Processing)

실제로는 일부 노트북에 100개 이상의 원시 이미지, 여러 Excel 통합 문서, 수많은 PDF 및 여러 임베디드 문서가 포함되어 있었습니다.

대규모 성능을 보장하기 위해 `ThreadPoolExecutor`를 구현했습니다. 각 이미지 또는 분할된 PDF 페이지는 Azure Document Intelligence로 병렬(parallel)로 전송되어, API 제약을 준수하면서 총 처리 시간을 획기적으로 단축합니다.

이를 통해 시스템은 다음을 수행할 수 있습니다.

*   대규모 노트북에 걸쳐 확장합니다.
*   API 지연 시간(latency)을 최적화합니다.
*   서비스에 과부하를 주지 않고 처리량(throughput)을 유지합니다.

이메일 처리는 간단한 HTML 파싱(parsing)으로 인해 경량(lightweight)이며 순차적(sequential)으로 유지됩니다.

### 임베딩 전 구조화된 결과 병합

3계층에서 가장 중요한 아키텍처 결정 중 하나는 임베딩 텍스트를 어떻게 구성하는가입니다.

*   우리는 원시 JSON을 임베딩하지 않습니다.
*   우리는 OCR 텍스트만 임베딩하지 않습니다.

대신, 세 가지 구성 요소를 선별된 의미론적 표현으로 병합합니다.

`embedding_text = layout_markdown + key_value_pairs + cleaned_html_text`

이는 모든 임베딩이 다음을 이해한다는 것을 의미합니다.

*   문서의 전체 OCR 텍스트
*   문서에서 추출된 구조화된 필드
*   원본 OneNote 페이지의 문맥적 설명(contextual commentary)

구조화된 문서 지능과 사람이 작성한 컨텍스트의 이러한 융합은 하위 RAG 검색 품질을 극적으로 향상시킵니다. 사용자가 질문을 하면, 시스템은 단순히 PDF 페이지를 검색하는 것이 아니라, 해당 보험 인수 설명(underwriting narrative)과 연결된 의미론적으로 풍부한 아티팩트(semantically enriched artifact)를 검색합니다.

### 이메일 처리

이메일은 약간 다른 경로를 따릅니다.
2계층은 이메일 첨부 파일을 HTML 미리보기로 저장했습니다. 3계층에서는 다음을 수행합니다.

*   BeautifulSoup를 사용하여 HTML을 파싱합니다.
*   깨끗한 일반 텍스트로 변환합니다.
*   2계층의 정제된 HTML 페이지 컨텍스트와 병합합니다.
*   동일한 벡터 파이프라인(vector pipeline)을 사용하여 임베딩합니다.

`embedding_text = email_plain_text + cleaned_html_text`

이는 이메일 대화가 PDF 및 이미지와 동일한 의미론적 공간(semantic space)에서 검색 가능하도록 보장합니다.

### 임베딩 및 영속성

선별된 임베딩 텍스트가 구성되면, Azure OpenAI의 `ai-coe-embeddings-3-large` 모델을 사용하여 고품질 의미론적 임베딩을 생성합니다.

```python
chroma_ocr = chromadb.PersistentClient(path="Pipeline_OCR")
ocr_layer_collection = chroma_ocr.get_or_create_collection(
    name="ocr_layer",
    metadata={"hnsw:space": "cosine"}
)
```

우리는 이를 이전 계층과 의도적으로 분리합니다. 1계층은 페이지 수준 지능을 저장합니다. 2계층은 정규화된 첨부 파일 아티팩트를 저장합니다. 3계층은 의미론적으로 풍부한 문서 지능을 저장합니다. OCR 임베딩을 자체 벡터 컬렉션으로 분리함으로써, 빠른 Top-K 유사성 검색(similarity retrieval)을 가능하게 하면서 깔끔한 아키텍처 경계(architectural boundaries)를 유지합니다.

컬렉션은 `hnsw:space="cosine"`으로 구성되어 있으며, 이는 유사도가 HNSW (계층적 내비게이션 가능 작은 세상 (Hierarchical Navigable Small World)) 인덱스 내에서 코사인 거리(cosine distance)를 사용하여 계산됨을 의미합니다. 이를 통해 강력한 의미론적 정확도를 유지하면서 대규모에서 효율적인 최근접 이웃 검색(nearest-neighbor search)이 가능합니다.

## 4계층: Streamlit UI (수동 유효성 검사 + 대화형 검색) (Streamlit UI (Human Validation + Conversational Retrieval))

**목표:** 파이프라인을 사용 가능하게 만드는 경량의 최종 사용자 인터페이스(end-user interface)를 제공하여, 추출된 아티팩트의 시각적 유효성 검사(visual validation)와 풍부해진 3계층 지식 기반(knowledge base)에 대한 대화형 검색 경험을 가능하게 합니다.

3계층 이후, 우리는 원시 OneNote 첨부 파일을 의미론적으로 검색 가능한 "지능 아티팩트"(OCR 텍스트, 표, 키-값 쌍, 풍부해진 메타데이터 및 임베딩)로 변환했습니다. 4계층은 이것이 사용자에게 실질적인 것이 되는 곳입니다. 분석가에게 JSON을 검사하거나 파일 디렉토리를 탐색하도록 강요하는 대신, OCR 미리보기(OCR Preview)와 채팅(Chat)이라는 두 가지 주요 워크플로우를 가진 깔끔한 Streamlit 애플리케이션을 노출합니다.

런타임에 UI는 3계층 ChromaDB (`Underwriting_Pipeline_OCR_v2`, 컬렉션 `ocr_layer`)에 직접 연결하며, 이 DB는 이미 코사인 인덱싱된 임베딩(cosine-indexed embeddings)과 감사 및 검색에 필요한 풍부한 추출 결과물을 저장하고 있습니다.

### 탭 1 — OCR 미리보기 (유효성 검사 + 투명성) (OCR Preview (Validation + Transparency))

**1번 탭 흐름 개요**

이것은 모든 첨부 파일을 정확하게 처리했음을 고객에게 설득하고 검증하기 위해 순전히 만들어졌습니다.

OCR 미리보기 탭은 휴먼 인 더 루프(human-in-the-loop) 유효성 검사를 위해 설계되었습니다. 엔터프라이즈 배포에서 가장 중요한 도입 질문 중 하나는 간단합니다. “모든 것을 추출했고, 올바르게 보이나요?” 이 탭은 그 질문에 빠르게 답합니다.

**파일 검색 (로컬 디스크 인덱스)**
2계층은 노트북 기반 폴더 구조(이미지, 분할 PDF, 임베디드 분할 PDF 등) 아래에 결정론적 온디스크 아티팩트(deterministic on-disk artifacts)를 생성했습니다. UI에서는 로컬 출력 디렉토리를 스캔하여 캐시된 파일 인덱스를 구축합니다.

*   `images/` (JPG/PNG)
*   `excel_split_pdfs/` (Excel 시트에서 생성된 PDF 페이지)
*   `pdfs_split/` (분할된 PDF 페이지)
*   `pdf_embedding_split/` (임베디드 PDF에서 분할된 페이지)

이는 파이프라인이 디스크에 출력을 구체화하는 방식과 일치하는 간단한 탐색 경험을 제공합니다: 노트북 → 카테고리 → 파일.

**지연 로드 미리보기(lazy-load previews) (빠른 사용자 경험, 무거운 로딩 없음)**
모든 것을 미리 로드하는 대신, UI는 "미리보기 로드(Load Preview)" 버튼을 사용하여 사용자가 아티팩트를 선택할 때만 데이터를 가져옵니다. 선택되면, 앱은 결정론적 3계층 문서 ID를 구성합니다.
`{notebook}_{category}_{filename}`
...그리고 ChromaDB에서 해당 레코드를 검색합니다.

**진정한 “병렬(side-by-side)” 유효성 검사**

실제 구현 레이아웃을 보여주는 더미/마스킹된 미리보기

미리보기 경험은 의도적으로 두 개의 열로 분할됩니다.

*   왼쪽 패널: 디스크에서 렌더링된 실제 파일 (이미지/PDF)
*   오른쪽 패널: 3계층에서 추출된 모델 출력 및 메타데이터

PDF의 경우, UI는 PyMuPDF를 사용하여 처음 몇 페이지를 이미지로 변환함으로써 브라우저 iframe 렌더링 문제를 방지합니다. PyMuPDF는 보안이 강화된(locked-down) 엔터프라이즈 브라우저에서 안정적으로 작동합니다.

### 탭 2 — 이중 모드 채팅 (검색 + 합성) (Dual-Mode Chat (Retrieval + Synthesis))

**2번 탭 흐름 개요**

**검색은 임베딩 기반(embedding-native)입니다 (재 OCR 없음, 재처리 없음).**

워크플로우는 다음과 같습니다.

*   3계층에서 사용된 것과 동일한 임베딩 모델을 사용하여 사용자 질문을 임베딩합니다.
*   ChromaDB 코사인 유사도(cosine similarity)를 사용하여 노트북별 Top-K 가장 유사한 청크(chunks)를 검색합니다.
*   검색된 컨텍스트를 GPT-4o에 전달하여 다음 중 하나를 합성합니다: 직접적인 질의응답(Q&A) 응답, 또는 구조화된 요약(Summary).

이 설계는 채팅 중에 문서 재처리를 방지합니다. 모든 것은 상위 계층에서 생성된 저장된 의미론적 아티팩트에 의해 구동됩니다.

**자동 vs 명시적 의도 라우팅(intent routing)**

UI는 세 가지 모드를 지원합니다.

*   자동(Auto): 사용자가 요약을 요청하는지 질의응답(Q&A)을 요청하는지 감지합니다.
*   질의응답(Q&A): 인용(citations)과 함께 직접 답변합니다.
*   요약(Summary): 인용과 함께 구조화된 개요를 생성합니다.

이는 상호 작용을 유연하게 만듭니다. 동일한 인터페이스로 빠른 사실 조회("브로커는 누구인가요?")와 더 높은 수준의 합성("이 클라이언트/노트북을 요약해 주세요.")을 처리할 수 있습니다.

**다중 노트북 지원 (클라이언트/프로젝트 전반에 걸쳐 확장)**

앱은 세 가지 검색 범위를 지원합니다.

*   수동 노트북 선택 (사용자가 검색할 노트북을 선택)
*   자동 감지 (질문에서 노트북/클라이언트 이름을 감지하기 위한 휴리스틱스 + 퍼지 매칭(fuzzy matching))
*   전역 폴백(Global fallback) (노트북이 지정되지 않은 경우, 모든 노트북에서 검색)

이는 실제 배포에서 중요합니다. 왜냐하면 사용자는 질문을 하기 전에 "검색을 구성"하기를 거의 원하지 않기 때문입니다. 그들은 단지 시스템이 올바른 컨텍스트를 찾기를 원합니다.

실제 구현 레이아웃을 보여주는 더미/마스킹된 미리보기

## 최종 의견: 노트에서 지능으로 (Final Thoughts: From Notes to Intelligence)

단절된 OneNote 페이지, 첨부 파일, 이메일, 스프레드시트로 시작된 것이 구조화되고 검색 가능한 지식 시스템으로 전환되었습니다.

Microsoft Graph, Azure Document Intelligence (사전 구축 문서 및 레이아웃 모델 (prebuilt-document and prebuilt-layout models)), 벡터 임베딩(vector embeddings) 및 GPT-4o 합성을 결합하여, 일상적인 엔터프라이즈 콘텐츠가 기계 판독 가능한 지능으로 어떻게 변환될 수 있는지 시연했습니다. OCR 계층은 PDF, 이미지 및 임베디드 문서에서 구조를 추출합니다. 벡터 계층은 의미론적 검색(semantic retrieval)을 가능하게 합니다. 합성 계층은 원본 소스에 대한 완전한 추적성(traceability)을 유지하면서 근거 있고 인용이 뒷받침되는 답변을 제공합니다.

높은 수준에서, 이 개념 증명(POC)은 다음을 보여줍니다.

*   **캡처(Capture)** — Microsoft Graph를 사용하여 OneNote와 같은 엔터프라이즈 도구에서 원시 콘텐츠를 직접 추출합니다.
*   **정규화(Normalize)** — PDF, 이미지, Excel 파일 및 이메일을 OCR 준비가 된 구조화된 표현으로 변환합니다.
*   **추출(Extract)** — Azure Document Intelligence (사전 구축 모델 및 미리보기 기능)를 사용하여 텍스트, 표 및 키-값 쌍을 추출합니다.
*   **검색(Retrieve)** — 벡터 데이터베이스에 임베딩을 저장하여 빠르고 코사인 기반의 의미론적 검색을 가능하게 합니다.
*   **합성(Synthesize)** — GPT-4o를 사용하여 인라인 인용(inline citations)이 포함된 근거 있는 답변과 구조화된 요약을 생성합니다.
*   **추적성 유지(Preserve Traceability)** — 모든 계층에서 노트북, 페이지 및 원본 메타데이터를 유지 관리합니다.

가장 중요한 것은, 이러한 종류의 AI 개념 증명(POC)은 막대한 예산을 요구하지 않는다는 것입니다. 현대적인 API와 오픈 소스 도구를 사용하면, 이러한 시스템은 종종 몇백 달러로 구축될 수 있으며, 기존 엔터프라이즈 라이선스 없이도 몇천 달러를 넘는 경우는 거의 없습니다. 이러한 낮은 진입 장벽은 조직이 가시적인 가치를 신속하게 입증하고, 의사 결정 마찰을 줄이며, 전체 규모 배포를 약속하기 전에 경영진의 신뢰를 구축할 수 있도록 합니다.

이곳에서 AI는 실용적이 됩니다.
이것은 전체 시스템을 대체하는 것이 아닙니다. 조직이 매일 생성하는 정보에서 가치를 추출하는 것입니다. 구조화된 추출, 의미론적 검색 및 생성적 합성(generative synthesis)이 사려 깊게 결합될 때, 정적인 저장소는 지능형 의사 결정 지원 플랫폼으로 진화합니다.

그리고 경영진이 최소한의 비용으로 측정 가능한 영향을 확인하면, 솔루션 확장은 투기적 투자가 아닌 전략적인 다음 단계가 됩니다.

궁금한 점이 있으시면 언제든지 연락 주세요!

---

**태그:**
*   AI
*   Microsoft
*   OpenAI
*   Python
*   LLM (Large Language Model)

---

**작성자:** 닐 파텔
팔로워 52명 · 팔로잉 31명
https://www.linkedin.com/in/neilpatel564