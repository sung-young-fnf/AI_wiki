# Vision Multimodal Index

| 필드 | 값 |
|---|---|
| 도메인 | AI스크랩 |
| 플랫폼 | `article_wiki` |
| 유형 | 인덱스 (중분류) |
| 상태 | 초안 |
| 소유자 | @윤형도 |
| 최종수정 | 2026-04-23 |
| 문서ID | IX-AW-02-05 |
| 키워드 | `Nano Banana`, `Nano Banana Pro`, `Gemini 2.5 Flash Image`, `ComfyUI`, `Flux outpainting`, `Multi-Angle LoRA`, `image-to-video`, `Chandra OCR 2`, `dots.ocr`, `multimodal OCR`, `identity lock`, `AI portrait`, `AI filmmaking`, `WAN 2.2`, `LTX 2.3` |
| 관련문서 | [[LLM Models Index]], [[ArticleScrap Platform Index]] |

## 개요

비전·이미지·비디오·OCR 멀티모달 모델 — Nano Banana·ComfyUI·Chandra OCR 2·dots.ocr·AI 인물 사진·AI 영화 제작.

## 파일 목록 (9개)

| 파일 | 요약 | 키워드 |
|---|---|---|
| [[20260228_222210_Nano Banana 시스템 지침 해킹 Gemini 25 Flash Image Nano Banana가 시스템 프롬프트를 공개하다]] | Jim the AI Whisperer가 Gemini 2.5 Flash Image(Nano Banana)의 시스템 프롬프트 전문을 추출 공개. `<img>` 태그로 이미지 생성을 트리거하며 "묘사 프로토콜"(Depiction is not Endorsement)·"금지된 응답 패턴"(I'm unable to create 등 거부 문구 금지)·콘텐츠 판단을 외부 안전 시스템에 완전 위임하는 설계가 드러남. 안전 장치가 생성 후 사후 필터링이라는 보안 함의 제기. | Nano Banana, Gemini 2.5 Flash Image, 시스템 프롬프트 유출, 묘사 프로토콜 Depiction Protocol, <img> 태그 트리거, Jim the AI Whisperer, 외부 안전 장치, 사후 필터링, 금지된 응답 패턴, 왕의 명령 메타프롬프트 |
| [[20260302_194548_와이어프레이밍을 위한 Nano Banana Pro 영감을 주는 6가지 뛰어난 예시]] | Nick Babich(UX Planet 편집장)가 Gemini 3 이미지 모드(Nano Banana Pro)로 와이어프레임을 6가지 스타일로 생성하는 프롬프트 모음 — (1) 구겨진 냅킨 스케치, (2) 시안 블루 건축 청사진, (3) 종이 오리기 3D, (4) 몰스킨 점선 그리드 펜 스케치, (5) 글래스모피즘 아이소메트릭 3단계 가입 플로우, (6) 마커+포스트잇 화이트보드 세션. 맥락→UI 상세→시각 스타일 3단 구조 프롬프트 템플릿 공유. | Nano Banana Pro 와이어프레임, Gemini 3 이미지 모드, Nick Babich UX Planet, 냅킨 스케치 3단 프롬프트, 건축 청사진 시안 블루, 종이 오리기 3D, 몰스킨 점선 그리드, 아이소메트릭 글래스모피즘, 화이트보드 세션 포스트잇 |
| [[20260318_213513_AI 내부 들여다보기 나노 바나나의 시스템 지침 해킹 제미니 25 플래시 이미지 나노 바나나 시스템 프롬프트가 드러나다]] | Jim the AI Whisperer가 Gemini 2.5 Flash Image(Nano Banana)의 시스템 지침을 프롬프트 해킹으로 추출해 전문 공개. `<img>` 태그로 별도 이미지 생성·편집 모델을 트리거하는 설계, 직접 요청·이미지 수정·선제적 삽화 3가지 트리거 규칙, 'The King's Command'라는 자동 제목 부여까지 분석. | Nano Banana 시스템 프롬프트, Gemini 2.5 Flash Image, img 태그 트리거, 시스템 지침 유출, Jim the AI Whisperer, 선제적 삽화, The King's Command |
| [[20260412_082838_ChatGPT 사용을 중단한 이유 그리고 대신 사용하는 것들]] | 지시사항 이행은 Claude, 오디오와 비디오는 Gemini 3, 음성 모드는 ChatGPT, 이미지는 Nano Banana와 ChatGPT가 각각 강하다는 식으로 사용처별 모델을 재배치한다. 트래픽 점유율 86.7퍼센트에서 64.5퍼센트 하락 수치와 Sora 2, Veo 3, Claude Code까지 묶어 멀티 모델 실사용 지도를 만든다. | ChatGPT, Claude, Gemini 3, Nano Banana, Sora 2, Veo 3, voice mode, traffic share 64.5% |
| [[20260418_194208_AI 인물 사진 내 얼굴을 그대로 유지하는 프롬프트]] | 얼굴 정체성이 계속 변형되는 AI 인물 사진 문제를 해결하기 위해 identity lock 중심 프롬프트와 입력 이미지 규칙을 제시한다. LinkedIn용 회색 배경, 피부 톤 유지, `<IDENTITY LOCK>` 수준의 제약을 통해 스타일보다 얼굴 보존을 우선해야 한다고 설명한다. (NEW 제안: Creative_AI) | identity lock, AI portrait prompt, LinkedIn headshot, face consistency, studio retouch, uploaded reference image |
| [[20260420_223202_고급 로컬 AI 이미지에서 AI 영화 제작까지 ComfyUI로 나만의 AI 영화 만들기]] | ComfyUI를 사용해 아웃페인팅, 멀티 앵글 LoRA, 이미지-투-비디오를 조합해 일관된 장면 시퀀스와 AI 영화를 만드는 과정을 설명한다. Flux, Qwen, LTX 2.3, WAN 2.2 기반 워크플로가 중심이며 창작형 로컬 이미지·영상 제작에 특화되어 있다. (NEW 제안: Creative_AI) | ComfyUI, Flux outpainting, Multi-Angle LoRA, image-to-video, LTX 2.3, WAN 2.2, AI filmmaking |
| [[20260421_142747_상업용 OCR의 시대는 끝났다 오픈소스 모델 모든 벤치마크를 석권하다]] | 상용 OCR API 대신 오픈소스 문서 인식 모델이 벤치마크를 뒤집는 흐름을 Chandra OCR 2 중심으로 소개한다. 4B 규모와 90개 언어, olmOCR 85.9 같은 수치를 통해 GPT-4o와 Gemini 계열 대비 경쟁력을 강조한다. | Chandra OCR 2, OCR, olmOCR, 4B 모델, 90개 언어, GPT-4o, Gemini |
| [[20260421_143141_Google의 Nano Banana를 활용해 모든 것을 아름다운 인포그래픽으로 바꾸는 방법]] | Google의 Nano Banana Pro를 이용해 텍스트와 조사 결과를 시각 중심 인포그래픽으로 바꾸는 활용법을 소개한다. Gemini 계열 생성 모델을 학습 자료와 리서치 요약에 연결해 시각 전달력을 높이는 워크플로가 핵심이다. | Nano Banana Pro, Gemini, 인포그래픽 생성, 시각 요약, Google AI Studio, visual learning |
| [[20260421_231016_17억 파라미터 다국어 모델을 이용한 문서 파싱 dotsocr]] | dots.ocr를 이용해 문서 OCR과 파싱을 한 번에 처리하는 다국어 모델 사례를 소개한다. 1.7B 파라미터와 100개 이상 언어 지원, vLLM과 Hugging Face 배포 경로가 실전 포인트다. | dots.ocr, 1.7B, document parsing, multilingual OCR, vLLM, Hugging Face, VLM |

## 관련 카테고리

* > 상위: [[LLM Models Index]]
* > 최상위: [[ArticleScrap Platform Index]]

## 변경 이력

| 버전 | 일자 | 작성자 | 변경내용 |
|---|---|---|---|
| v1.0 | 2026-04-23 | AI(claude-code) | Phase 3 자동 생성 — books/ 파일 목록 + summary_table 매칭 |
