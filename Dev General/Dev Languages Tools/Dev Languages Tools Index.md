# Dev Languages Tools Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-07-05 |
| 키워드 | `Rust`, `Tauri`, `WASM`, `R 언어`, `tidyverse`, `Tmux`, `Alacritty`, `WezTerm`, `Starship`, `fzf`, `ripgrep`, `eza`, `zoxide`, `httpie`, `tldr`, `UV`, `pyproject`, `Faster CPython`, `TikZ`, `LaTeX`, `cbonsai` |
| 관련문서 | [[Dev General Index]], [[ArticleScrap Platform Index]] |

## 개요

기타 개발 언어·도구 — Rust(Tauri/마이그레이션)·R 언어·터미널(Tmux/Alacritty/WezTerm/fzf/ripgrep/exa)·UV·CLI 명령어·Python 자체 진화·TikZ/LaTeX.

## 파일 목록 (19개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260326_161324_더 이상 기술 그래픽을 손으로 그리지 않습니다]] | draw.io나 Visio 대신 TikZ로 기술 그래픽을 코드화해 버전 관리와 재사용성을 높이는 방법을 설명한다. `rectangle`, `node`, `circuitikz`, `PGFPlots` 같은 LaTeX 생태계 도구를 통해 수식과 다이어그램을 같은 소스에서 관리하고, 해상도와 정렬 문제를 근본적으로 없애자는 주장이다. | TikZ, PGF, LaTeX, circuitikz, PGFPlots, draw.io 대체, node anchor, vector graphics |
| [[20260329_180922_다음은 웹페이지에서 추출한 텍스트를 한국어로 자연스럽고 전문적으로 번역한 내용입니다]] | Bhavyansh의 2025년판 현대 CLI 도구 8선: fzf(퍼지 검색 Ctrl-R/Ctrl-T), ripgrep(grep 대비 15배, 2GB 코드 0.8초), bat(구문강조 cat), fd(.gitignore 존중 find), exa(git-aware ls, 후속 eza로 포크됨), zoxide(frecency 기반 cd), httpie(가독성 좋은 curl), tldr(간결 man). .zshrc 별칭 세트와 brew/apt 일괄 설치 스크립트 제공. 추정 시간 절약 주당 2~4시간. | fzf, ripgrep, rg, bat, fd, exa, eza, zoxide, httpie, tldr, frecency, Bhavyansh |
| [[20260329_181043_터미널 완벽 마스터 2025년 시니어 개발자 환경 설정 단계별 가이드]] | 시니어 개발자가 생산성을 극대화하기 위한 터미널 환경 구축 전체 가이드로, 에뮬레이터·셸·멀티플렉서 선택을 단계별로 설명한다. Alacritty(Rust 기반 최고속), WezTerm(올인원), Fish 4.0(C++→Rust 재작성, 2025년 9월 4.1 출시), Oh My Zsh+zsh-autosuggestions+zsh-syntax-highlighting 조합을 추천. tmux 세션 관리, Starship 프롬프트 등 단계별 설정법 제공. | Alacritty, WezTerm, Fish 4.0, Oh My Zsh, zsh-autosuggestions, zsh-syntax-highlighting, tmux, Starship, GPU 가속 터미널, Puneet |
| [[20260329_192749_엑셀이 처리할 수 없는 모든 작업을 다루는 7가지 파이썬 라이브러리]] | (요약 누락 — Phase 2 표에서 매칭 실패) | (키워드 누락) |
| [[20260329_192839_2026년 개발자를 위한 최고의 자체 호스팅 오픈소스 앱 개발자 관점]] | (요약 누락 — Phase 2 표에서 매칭 실패) | (키워드 누락) |
| [[20260329_193045_Python과 Typst의 만남 깔끔한 기술 문서 작성]] | (요약 누락 — Phase 2 표에서 매칭 실패) | (키워드 누락) |
| [[20260329_202000_당신이 아마도 한 번도 들어보지 못했을 10가지 저평가된 CLI 명령어]] | 이미 널리 알려진 fzf나 zoxide 대신 잘 덜 알려졌지만 실용적인 CLI 명령 10개를 소개하는 도구 모음이다. `cbonsai`, `asciinema`, `croc`, `ttyd`, `jrnl`, `wttr.in`, `newsboat`, `lolcat`, `faker`, `grex`를 통해 파일 전송·브라우저 터미널·RSS·가짜 데이터·정규식 생성 같은 틈새 생산성 영역을 다룬다. | cbonsai, asciinema, croc, ttyd, jrnl, wttr.in, newsboat, lolcat, faker, grex |
| [[20260402_072538_Python Just Killed The Nested Loop Nightmare with 1 Simple Symbol The Logic Secret Every Beginner Mu]] | 중첩 루프 대신 set 연산자와 union, intersection, difference를 사용해 데이터 관계를 더 빠르고 명확하게 표현하는 방식을 설명한다. 파이프 연산자와 교집합 연산을 벤 다이어그램 수준의 수학적 로직으로 해석해 초보자의 반복문 습관을 교정한다. | Python set, union, intersection, difference, Venn Diagram, pipe operator, nested loop replacement |
| [[20260402_073752_수 시간을 아껴준 10가지 터미널 명령어 10 Terminal Commands That Saved Me Hours of Clicking]] | cd, mkdir -p, cp -r, grep -r 같은 기본 터미널 명령어가 클릭 위주의 파일 탐색과 백업 작업을 얼마나 줄여 주는지 초보자 관점에서 설명한다. 자동 완성, 와일드카드, 재귀 복사처럼 적은 수의 명령으로 작업 흐름을 크게 단축하는 것이 요지다. | cd, mkdir -p, cp -r, grep -r, ls -la, touch, wildcard mv |
| [[20260404_104515_51개의 이미지로 배우는 컴퓨터 과학 스택의 모든 것]] | 논리 게이트부터 CPU, 튜링 머신, 컴파일러, 자료구조까지 컴퓨터 과학 스택 전반을 51개 도식으로 연결하는 교육용 개요다. 트랜지스터와 NAND, 중앙처리장치, 컴파일러와 인터프리터 같은 계층을 하나의 연속된 정신 모델로 묶어 설명하는 것이 핵심이다. | logic gates, transistor, CPU, Turing machine, compiler, interpreter, computer science stack, mental models |
| [[20260404_184631_파이썬 패턴 매칭으로 리팩토링하기 코드의 품질을 높이는 새로운 방법]] | Python 패턴 매칭을 활용해 조건문과 분기 코드를 더 읽기 쉽고 안전하게 리팩토링하는 방법을 설명한다. 기존 if-elif 체인을 구조적 패턴으로 바꾸어 유지보수성과 표현력을 높이는 실전 리팩토링 관점이 중심이다. | Python pattern matching, structural pattern matching, refactoring, match-case, code quality, Python 3.10 |
| [[20260409_074345_6 Websites I Visit Every Day as a Developer in 2025 2025년 개발자가 매일 방문하는 6가지 웹사이트]] | 개발자가 매일 찾는 학습·프로토타이핑·글쓰기 웹사이트 6곳을 소개하며 각각의 용도를 설명한다. In Plain English, CodeSandbox, Differ 같은 사이트를 통해 학습, 데모 제작, 협업, 글쓰기까지 일상 개발 루틴을 최적화하는 방식이 핵심이다. | In Plain English, CodeSandbox, Differ, developer workflow, daily websites, prototyping, interactive demos, learning hub |
| [[20260418_114725_존재조차 몰랐던 10가지 틈새 개발자 도구]] | 널리 알려지지 않았지만 특정 문제를 크게 줄여 주는 틈새 개발자 도구 10개를 소개하는 글이다. 반복 작업 감소와 로컬 개발 환경 개선에 도움이 되는 실전 툴 추천에 가깝다. | niche developer tools, productivity utilities, dev workflow tools, lesser-known tooling, engineering stack |
| [[20260420_220338_Python 개발자를 위한 UV 치트시트 초보자 친화적 가이드]] | UV를 파이썬 패키지 설치와 가상환경, 의존성 잠금까지 통합하는 빠른 도구로 소개하며 기본 명령을 치트시트 형태로 정리한다. `pip`와 `venv`를 따로 다루지 않고 단일 인터페이스로 개발 환경을 운영하는 관점이 중심이다. | uv, Python packaging, virtualenv, dependency lock, Astral, uv cheatsheet |
| [[20260420_221824_특징Feature 생성 시 평균과 표준편차 사용을 지양해야 하는 이유]] | 특성 생성에서 평균과 표준편차에 과도하게 의존하면 분포 형태와 이상치 구조를 놓칠 수 있다는 점을 설명한다. 더 강건한 통계와 도메인 맞춤 피처가 모델 품질을 높인다는 실무형 머신러닝 글이다. | feature engineering, mean and std, robust statistics, ML features, outlier sensitivity |
| [[20260421_142232_베이즈 모델링을 활용한 인과 관계 발견 완벽 입문 가이드]] | 관측 데이터에서 상관관계가 아니라 인과 구조를 추정하기 위해 DAG와 베이지안 네트워크를 사용하는 입문 가이드다. pgmpy, bnlearn, PyMC, CausalImpact, Hyperopt를 묶어 구조 학습과 개입 효과 추정 흐름을 정리한다. | 베이지안 네트워크, DAG, pgmpy, bnlearn, PyMC, CausalImpact, Hyperopt, 인과 발견 |
| [[20260421_224824_폴리마켓에서 퀀트들이 87의 트레이더를 압도하는 마르코프 연쇄 알고리즘]] | Polymarket에서 정량 트레이더가 우위를 갖는 배경을 마르코프 연쇄와 몬테카를로 관점에서 설명한다. PageRank 계열 사고와 상태 전이 모델을 예측시장 전략에 연결하는 점이 핵심이다. | Polymarket, 마르코프 연쇄, 몬테카를로, PageRank, 예측시장, 퀀트 트레이딩 |
| [[20260421_225851_Timber 프로덕션 ML 모델을 위한 Ollama]] | Timber를 트리 기반 ML 모델의 Ollama 같은 배포 도구로 소개하며 프로덕션 추론 최적화 관점을 설명한다. XGBoost, LightGBM, scikit-learn, CatBoost, ONNX 모델을 C99로 컴파일해 서빙 효율을 높이는 점이 특징이다. | Timber, XGBoost, LightGBM, scikit-learn, CatBoost, ONNX, C99 serving |
| [[20260421_231252_Python에서 효과적인 설정 관리 pydantic-settings 활용 가이드]] | Python 프로젝트에서 환경 변수와 설정 객체를 안정적으로 다루기 위해 `pydantic-settings`를 쓰는 방법을 설명한다. `.env`, 타입 검증, 구성 분리 같은 실무 패턴을 코드 수준에서 정리한다. | pydantic-settings, Python config, .env, settings management, type validation, environment variables |

## 관련 카테고리

* > 상위: [[Dev General Index]]
* > 최상위: [[ArticleScrap Platform Index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
