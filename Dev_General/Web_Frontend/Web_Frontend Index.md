# Web_Frontend Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-07-03 |
| 키워드 | `HTML5`, `HTMX`, `HTML Invokers`, `React`, `OKLCH`, `UI 디자인`, `color palette`, `design rules`, `Sheerpower`, `React Flow`, `Recharts`, `web platform`, `service worker`, `contenteditable`, `frontend optimization`, `Tailwind` |
| 관련문서 | [[Dev_General Index]], [[wiki_index]] |

## 개요

웹·프론트엔드 — HTML5 API·HTMX·HTML Invokers·React·OKLCH 색상·UI 디자인 규칙·Sheerpower·React Flow·Recharts.

## 파일 목록 (17개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260317_204746_3년간 React 속도를 늦춰왔습니다 제 코드 리뷰에서 드러난 사실들입니다]] | React 3년차 개발자가 동료의 PR 리뷰에서 `useMemo` 과용을 지적당한 회고. 문자열 연결 같은 마이크로초 계산에 `useMemo` 래퍼를 감싸면 캐시 저장·의존성 비교·생명주기 관리 비용이 계산 비용을 초과해 React가 오히려 더 많은 일을 하게 만든다는 교훈을 사례 코드로 정리한다. | useMemo 과용, React 메모이제이션 비용, 의존성 비교 오버헤드, React 성능 안티패턴, 캐시 생명주기 |
| [[20260318_181240_Rust에 감탄하다 Electron Python에서 Tauri Rust로의 전환 그리고 다시 돌아가지 않는 이유]] | Electron+React+Flask 번들 데스크톱 앱에서 서버 라이프사이클·포트 충돌·OS별 패키징·Electron 번들 비대화 문제에 시달리다 Tauri+Rust로 전환한 경험담. The Rust Book 3~4일, 첫 Tauri 앱 5~6일 학습 곡선 끝에 소유권·Option/Result·borrowing 규율이 안정성을 가져오고 시스템 웹뷰 사용으로 더 작은 바이너리를 확보한 과정을 코드 예시와 함께 설명. | Tauri Rust, Electron to Tauri, The Rust Book, Option Result, 소유권 borrowing, 시스템 웹뷰, Rust 컴파일러 규율, 크로스 플랫폼 데스크톱 앱 |
| [[20260326_161132_Python Panel 튜토리얼 인터랙티브 대시보드 구축하기]] | Panel을 이용해 웹 개발 지식 없이도 Jupyter 기반 인터랙티브 대시보드와 웹 앱을 만드는 입문 튜토리얼이다. `pn.widgets.IntSlider`, `pn.bind`, Matplotlib 사인파 예제, `pip install panel` 흐름을 통해 최소 코드로 브라우저 UI를 구성하는 방법을 보여준다. | Python Panel, pn.widgets.IntSlider, pn.bind, Jupyter Notebook, Matplotlib, Bokeh, Plotly, Dr. Shouke Wei |
| [[20260329_104057_UI를 고급스럽게 보이게 하는 단 하나의 색상 결정]] | 프리미엄 UI가 순수한 무채색 대신 브랜드 색조가 섞인 중립색을 쓰는 이유를 배경, 그림자, 텍스트 계층 세 영역으로 설명한다. `#F5F7FF` 같은 tint neutral과 브랜드 색 그림자, hairline border용 `0 0 0 1px` 레이어를 활용하면 Linear나 Stripe류의 완성도를 낼 수 있다는 실무 팁이 핵심이다. | tinted neutrals, `#F5F7FF`, box-shadow temperature, hairline border, Linear, Stripe, Vercel, Raycast |
| [[20260402_072900_Sheerpower HTMX HTML을 다시 신뢰하기 Trusting HTML Again]] | Sheerpower FastCGI와 HTMX를 결합해 JSON API와 하이드레이션 없이도 서버가 HTML을 보내고 브라우저가 교체하는 단순한 웹 아키텍처를 복원하는 글이다. 메모리 상주 클러스터, hx-get, hx-swap 흐름으로 CRUD와 검색 UI의 복잡도를 줄이고 밀리초 미만 응답을 지향한다. | Sheerpower, HTMX, FastCGI, hx-get, hx-swap, server-rendered HTML, memory cluster |
| [[20260404_105041_사내 도구Internal Tools 구축에 완벽한 7가지 파이썬 라이브러리]] | 승인 워크플로, KPI 대시보드, 내부 관리자 페이지 같은 사내 도구를 빠르게 만드는 데 유용한 파이썬 라이브러리 7가지를 소개한다. Reflex, FastAPI, NiceGUI, Textual 등으로 웹과 TUI를 빠르게 구현하는 예제와 함께, 프론트엔드 부담 없이 내부 시스템을 구축하는 흐름을 보여준다. | Reflex, FastAPI, NiceGUI, Textual, internal tools, KPI dashboard, approval workflow, Python web UI |
| [[20260404_184217_HTML5만으로 웹 앱을 구축하며 놀랐던 9가지 기능]] | 최신 브라우저가 이미 프레임워크 없이도 파일 업로드, 검증, media API, contenteditable, local storage, service worker, websocket 같은 기능을 제공한다는 점을 사례로 보여준다. 단순 내부 도구를 다시 구현해 자바스크립트 양을 80% 줄인 경험을 통해 platform-first 설계를 강조한다. | HTML5, contenteditable, service worker, local storage, WebSockets, native validation, file upload, browser platform |
| [[20260404_184310_7 Design Rules That Take 10 Minutes to Learn and Fix Your UI Forever]] | UI를 어설프게 보이게 만드는 흔한 실수를 간단한 디자인 규칙으로 교정하는 글이다. spacing scale, 폰트 수 제한, 시각적 일관성 같은 원칙을 CSS 예시와 함께 소개하며 프론트엔드 품질 개선에 초점을 둔다. | spacing scale, typography, UI design rules, CSS layout, interface polish, visual hierarchy |
| [[20260404_184333_12 React Libraries for Visual Builders Flows Charts Interactive UI That Feel Like Magic]] | 워크플로 빌더, 그래프, 차트, 화이트보드 같은 고상호작용 UI를 구현할 때 유용한 React 라이브러리 12가지를 비교한다. React Flow, Recharts, VisX, React Diagrams, Konva, Nivo, mxGraph, Graphin 같은 도구를 업무 유형별로 정리한다. | React Flow, Recharts, VisX, React Diagrams, Konva, Nivo, mxGraph, Graphin, visual builder UI |
| [[20260404_184501_프레임워크의 필요성에 의문을 갖게 만든 9가지 HTML5 기능]] | 프레임워크 없이도 최신 브라우저만으로 상당한 수준의 웹 앱을 구현할 수 있음을 보여주는 HTML5 기능 정리다. native file upload, form constraints, media API, contenteditable, autocomplete, canvas, local storage, service workers, WebSockets를 통해 빌드 파이프라인 의존을 줄이는 관점을 제시한다. | HTML5, native file upload, form constraints, contenteditable, canvas, local storage, service workers, WebSockets |
| [[20260406_225308_대부분의 튜토리얼이 건너뛰는 Python GUI 도구 타당한 이유 없이]] | 터미널에서만 돌던 자동화 스크립트가 채택되지 않는 문제를 PySide6 기반 데스크톱 UI로 해결하자는 글이다. PyMuPDF, sentence-transformers `all-MiniLM-L6-v2`, KMeans 같은 기존 파이프라인을 백엔드로 분리하고 GUI를 얹어 자동화를 실제 제품으로 바꾸는 과정을 설명한다. | PySide6, PyMuPDF, all-MiniLM-L6-v2, KMeans, desktop GUI, PDF organizer, Qt for Python, automation UI |
| [[20260412_083814_프런트엔드를 압도하는 10가지 강력한 HTML5 API]] | 현대 브라우저가 제공하는 HTML5 API를 활용해 프런트엔드 기능을 크게 확장하는 방법을 정리한다. 네이티브 웹 기능을 별도 라이브러리 없이 활용하는 관점에서 UI와 상호작용 구현 폭을 넓힌다. | HTML5 API, frontend, browser API, native web platform, Web API, client-side, interactive UI, browser capability |
| [[20260418_112338_HTML 인보커Invokers 아직 활용되지 않는 가장 강력한 API]] | HTML Invokers를 활용해 복잡한 자바스크립트 없이도 상호작용을 구성하는 웹 플랫폼 기능을 설명한다. 프런트엔드 구현에서 아직 덜 알려진 네이티브 API를 발굴하는 성격의 글이다. | HTML Invokers, web platform, browser API, frontend interaction, native HTML, declarative UI, web standards, JavaScript alternative |
| [[20260418_194510_컬러 팔레트 제작 중단 OKLCH로 동적이고 접근성 높은 시스템 구축하기]] | HSL 대신 OKLCH로 색 체계를 설계하면 동일한 lightness와 chroma에서도 인간 시각에 더 일관된 대비를 제공할 수 있다는 점을 보여준다. 브랜드 색 `#7276cb`를 기준으로 foreground와 background를 계산식으로 파생해 접근성 높은 동적 팔레트를 만드는 과정이 핵심이다. | OKLCH, accessibility palette, color system, contrast ratio, dynamic palette, #7276cb |
| [[20260420_222646_2026년에 주목해야 할 5가지 신흥 개발 도구]] | Temporal Cloud 2.0, Pkl, Grafana Faro 같은 도구들이 분산 워크플로 오케스트레이션, 타입드 설정, 프런트엔드 관측성 문제를 어떻게 해결하는지 설명한다. 과장된 유행 목록이 아니라 실제 운영 환경의 취약한 워크플로와 설정 문제를 줄이는 개발 도구에 초점을 둔다. | Temporal Cloud 2.0, Pkl, Grafana Faro, workflow orchestration, typed config, frontend observability |
| [[20260421_224456_HTML 프레젠테이션의 혁신 레거시 PPTX 사고방식을 넘어 상하좌우 스크롤 활용]] | PPTX 중심 사고에서 벗어나 HTML 프레젠테이션을 상하좌우 스크롤 공간으로 설계하는 접근을 제안한다. 웹 네이티브 상호작용과 비선형 탐색을 활용해 발표 자료의 구조를 다시 정의하는 것이 핵심이다. | HTML presentation, PPTX 대체, 스크롤 프레젠테이션, 웹 네이티브, 비선형 내비게이션 |
| [[20260421_231955_스프레드시트를 대체하는 파이썬 TextualRich 대시보드 10가지]] | 스프레드시트 대신 터미널 기반 대시보드를 만들 수 있는 Python 예제를 소개한다. Textual과 Rich를 이용해 표, 차트, 입력 UI를 구성해 데이터 작업을 코드 중심으로 옮기는 흐름을 보여준다. | Textual, Rich, 터미널 대시보드, spreadsheet replacement, TUI, Python dashboard |

## 관련 카테고리

* > 상위: [[Dev_General Index]]
* > 최상위: [[wiki_index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
