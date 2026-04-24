# 번역 기사

- **원본 URL:** https://medium.com/data-storytelling-corner/how-to-create-vibrant-data-visuals-in-jupyter-with-pygwalker-fabf0bdb2430
- **번역 일시:** 2026-04-20 22:29:19

---

# PyGWalker를 활용한 Jupyter 내 Tableau 스타일 데이터 시각화 가이드

최근 저는 2024년 세계 행복 보고서 (World Happiness Report 2024) 데이터셋을 입수했고, 유용한 데이터 시각화를 통해 이를 탐색하고 싶었습니다.

완전한 BI (Business Intelligence) 도구에 바로 뛰어들기보다는, 오픈소스 Tableau와 유사한 인터페이스를 모방한 무료 파이썬 라이브러리인 PyGWalker를 사용해 보기로 결정했습니다.

이 라이브러리의 정말 멋진 점은 무료이며 Jupyter Notebook에서 실행할 수 있다는 것입니다.

이 도구를 설치하는 방법과 몇 가지 탐색적 데이터 시각화를 만드는 방법을 보여드리겠습니다.

*   행복 점수의 시계열 (time-series)
*   기대 수명 대 행복 산점도 (scatterplot)
*   2024년 가장 행복한/덜 행복한 상위 10개국을 보여주는 가로 막대 차트 (horizontal bar charts)

이 모든 작업은 차트 작성을 위한 사용자 친화적인 포인트 앤 클릭 (point-and-click) UI (User Interface)를 갖춘 Jupyter Notebook 내에서 이루어집니다.

### Jupyter Notebook에 PyGWalker 설정하기

시작하기 전에 PyGWalker를 설치해야 합니다. pip (및 conda-forge)를 통해 사용할 수 있습니다. 터미널 또는 노트북 셀에서 다음 명령을 실행하세요.

```bash
pip install pygwalker
```

설치가 완료되면 Jupyter Notebook 환경을 열거나 다시 시작하세요.

참고: PyGWalker를 실행한 후 "Loading Graphic-Walker UI…" 메시지만 표시되고 완료되지 않는 경우, Jupyter 위젯 (widgets)을 설치해야 할 수 있습니다.

```bash
pip install --upgrade ipywidgets
```

노트북 커널 (kernel)을 다시 시작하면 준비가 완료됩니다!

### 데이터와 함께 Graphic Walker 인터페이스 실행하기

PyGWalker가 준비되었으니, 데이터셋을 로드하고 Graphic Walker 인터페이스를 띄워봅시다.

Jupyter Notebook에서 이 도구를 실행하는 것이 정말 이렇게 간단합니다!
2024년 세계 행복 보고서 데이터셋 (WHR2024.csv)에는 전 세계 국가의 행복 점수("Ladder score"라고 함)와 함께 GDP, 사회적 지원, 기대 수명 등이 포함되어 있으며, 여러 연도의 데이터가 들어있습니다.

이 데이터셋(및 .ipynb 파일)은 GitHub에서 다운로드할 수 있습니다.
이 튜토리얼을 위해 저는 WHR2024.csv를 작업 디렉토리(.ipynb 파일과 동일한 디렉토리)에 저장했습니다.

다음은 노트북에 복사/붙여넣기할 수 있는 PyGWalker 코드 전체입니다.

```python
import pandas as pd
import pygwalker as pyg

df = pd.read_csv("WHR2024.csv")

walker = pyg.walk(
    df,
    appearance="light",        # force light theme
    default_tab="vis",         # open on the viz tab
    kernel_computation=True    # use DuckDB backend (good with bigger CSVs)
)

walker   # run it
```

셀을 실행하면 PyGWalker가 셀 바로 아래에 인터랙티브한 Graphic Walker UI를 엽니다.

데이터 미리보기와 비어 있는 차트 캔버스 (canvas)가 있는 UI를 볼 수 있습니다. 이것이 데이터 시각화를 위한 드래그 앤 드롭 (drag-and-drop) 인터페이스인 Graphic Walker입니다.
이전에 Tableau나 Power BI를 사용해 보셨다면 익숙할 것입니다. 차트 유형을 선택하고, 필드를 축에 할당하고, 필터 (filter)를 추가하는 등의 작업을 할 수 있습니다.

데이터 미리보기 탭은 데이터프레임 (DataFrame)을 보여주며 기본적인 필터링 및 유형 조정을 허용합니다. "Visualizations" 창은 차트를 만드는 곳입니다.
이제 행복 데이터를 사용하여 몇 가지 특정하고 유용한 차트를 만드는 과정을 안내해 드리겠습니다.

### 시계열 차트: 시간 경과에 따른 Ladder Score 추세

Graphic Walker를 사용하여 라인 차트 (line chart)를 만들 수 있습니다.

*   X축 (차원, Dimension): Year (숫자 또는 시간 필드로 처리되는지 확인하세요.)
*   Y축 (측정값, Measure): Ladder score (행복 점수) — 연간 전체 평균을 얻기 위해 "Mean"으로 설정할 수 있습니다.

각 필드를 해당 위치로 드래그하면 차트가 완성됩니다.
이제 이를 변경하려면 툴바의 "Mark type" 아이콘을 클릭하여 "line chart"로 변경할 수 있습니다. 그러면 전 세계 연간 평균 차트가 완성됩니다.

완벽합니다. 하지만 이것은 다소 평범합니다. 전 세계 평균을 보여주는 대신, 몇 개 국가를 추가하여 비교해 보겠습니다.

#### 1단계. 필터 추가 (국가별)

먼저 "Country name"을 "Filters" 창으로 드래그할 수 있습니다. "Country Name"을 클릭하면 필터 상자가 나타납니다.
비교할 국가를 선택합니다 (예: 아프가니스탄, 브라질, 캐나다, 핀란드, 예멘).

#### 2단계: 각 국가를 색상으로 필터링

다음으로 "Country name"을 "Color" 창으로 드래그합니다. 이렇게 하면 라인 차트에서 각 국가가 색상별로 정렬됩니다 (Tableau에서 하는 것과 동일).

매우 쉽고, 편리하며, 무료입니다.

### 산점도: 건강 기대 수명 대 행복

우리는 종종 건강과 장수가 행복과 밀접한 관련이 있다고 생각합니다.

데이터셋에는 건강과 관련된 지표인 "Healthy life expectancy"(행복 점수에 기여하는 요인)가 포함되어 있습니다. 산점도를 사용하여 건강과 행복 사이의 관계를 살펴볼 수 있습니다.

*   X축: Healthy life expectancy (각 국가의 건강 요인을 나타내는 숫자 값)
*   Y축: Ladder score (행복 점수)
*   마지막으로 "Country name"을 "Details" 창으로 드래그합니다.

각 점은 한 국가를 나타냅니다. 일반적으로 상승하는 추세를 볼 수 있습니다. 즉, 건강 기대 수명이 높은 국가들이 더 높은 행복을 보고하는 경향이 있습니다. 이는 더 건강하고 사람들이 더 오래 사는 사회가 일반적으로 더 행복하다는 직관적인 통찰력을 뒷받침합니다.
다른 차원(예를 들어, 지역 또는 대륙 필드가 있다면)으로 점에 색을 입혀 지역별 패턴을 볼 수도 있습니다.

이제 2024년 가장 행복한/덜 행복한 국가에 대한 막대 차트를 만들어 봅시다.

### 가로 막대 차트: 가장 행복한/덜 행복한 국가 (2024)

마지막 시각화는 2024년 가장 행복한/덜 행복한 국가를 보여주는 가로 막대 차트입니다. 이를 위해:

*   X축: Ladder score (행복 점수)
*   Y축: Country Name
*   다음으로 "Year" 필드를 "Filters" 창으로 드래그합니다. 2024년만 선택합니다.
*   마지막으로 "Ladder score" 필드를 "Filters" 창으로 드래그합니다. 범위를 조정하여 먼저 가장 덜 행복한 상위 10개국을 보여주고, 그 다음 가장 행복한 상위 10개국을 보여줍니다.
Ladder score 범위를 조정하여 가장 덜 행복한 10개국을 표시합니다 (가장 행복한 10개국에도 동일한 과정이 적용됩니다).

확인을 클릭하면 차트가 업데이트됩니다.
2024년 가장 덜 행복한 10개국 (연도 및 Ladder score별 필터가 적용됩니다.)

이제 가장 행복한 상위 10개국을 표시하려면 "Ladder score" 범위를 조정합니다.
2024년 가장 행복한 상위 10개국 (Ladder score 범위 변경)

새로운 "Ladder score" 범위가 높은 쪽으로 설정된 것을 볼 수 있습니다 (이전 예시와 동일한 단계를 사용). 툴바의 설정 아이콘을 클릭하여 색상을 쉽게 변경할 수도 있습니다.

수고하셨습니다. 정말 간단하죠. 이것으로 설명을 마칩니다!

### 요약

행복 보고서와 같은 데이터셋이 있고 최소한의 번거로움으로 시각적으로 탐색하고 싶다면 PyGWalker를 사용해 보세요.

설치는 매우 간단하며 (Jupyter Notebook에서 2줄의 코드), 기존 노트북 환경 내에서 바로 실행됩니다.

시계열, 산점도, 막대 차트에서 작동하는 것을 보았으며, 더 많은 차트 옵션 (예: 상자 그림 (box-plot), 영역 차트 (area charts))이 있습니다.

PyGWalker 도구는 탐색적 데이터 연구에 탁월하며, 추가 애플리케이션 설치 없이 무료로 실행할 수 있습니다.

한번 시도해 보세요!

참고: 데이터셋 (WHR2024.csv)과 Jupyter Notebook 파일 (pygwalker01.ipynb)은 GitHub에서 다운로드할 수 있습니다.