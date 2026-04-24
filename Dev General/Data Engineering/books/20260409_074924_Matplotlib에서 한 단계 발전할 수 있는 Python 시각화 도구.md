# 번역 기사

- **원본 URL:** https://medium.com/data-science-collective/python-visualization-tools-to-level-up-from-matplotlib-0b0fa7fe1f81
- **번역 일시:** 2026-04-09 07:49:24

---

Matplotlib에서 한 단계 발전할 수 있는 Python 시각화 도구

이 글은 데이터 과학을 위한 6가지 훌륭한 패키지를 언제, 어떻게 사용하는지에 대한 간략한 개요를 제공합니다.

Python 생태계에는 데이터 시각화를 위한 수많은 라이브러리와 프레임워크가 존재합니다 (PyViz는 이들에 대한 훌륭한 개요 자료입니다). 이 글에서는 매우 잘 알려진 Matplotlib 및 Plotly 외에 몇 가지 다른 옵션들과 각각의 사용 사례를 소개할 것입니다.

먼저, 여기에 제시된 예제들을 실행하기 위한 설정 코드입니다.

이제 바로 본론으로 들어가겠습니다.

**Seaborn**

Seaborn은 Matplotlib 기반의 더 깔끔한 대안으로 오랫동안 자리매김해 왔습니다. '데이터 프레임'(Pandas) 입력을 지향하여 데이터 과학 용도에 매우 적합합니다. 집계되지 않은 데이터 분포를 시각화하는 데 탁월하며, 일반적으로 입력 데이터를 크게 조작할 필요 없이 원하는 결과를 제공합니다. 유사한 플롯 유형들이 유용한 일반 플롯 유형으로 그룹화되어 있어, 플롯 명령에서 매개변수 하나만 변경하여 다양한 플롯 유형에 접근할 수 있습니다 (예: `catplot`은 'box', 'swarm', 'bar' 등의 종류를 가질 수 있습니다).

데이터 분포를 플로팅하는 것이 Seaborn의 강점입니다.

와인 지역 및 마그네슘 함량에 대한 Seaborn의 Boxen 플롯

데이터 분포는 선 플롯(line plot)으로도 그릴 수 있는데, 이때 '원하는 동작'은 각 X 지점에서 여러 관측치를 음영 처리된 범위로 보여주는 것입니다.

시계열 데이터에 분포 음영이 있는 Seaborn의 선 플롯

`kind` 매개변수를 통한 플롯 유형 변경은 쉽지만, `displot`과 같은 함수는 `ax` 매개변수를 받지 않아 Matplotlib 그림이나 서브그림에 배치할 수 없다는 단점이 있습니다.

와인 데이터에 대한 Seaborn의 히스토그램 분포 플롯
와인 데이터에 대한 Seaborn의 KDE 분포 플롯
와인 데이터에 대한 Seaborn의 ECDF 분포 플롯

Hexbin을 포함하여 2차원 분포를 표시하는 여러 옵션이 있습니다.

모델 예측 및 실제 데이터에 대한 Seaborn의 Hexbin 및 막대 플롯

**Plotnine**

Plotnine은 본질적으로 ggplot2의 Python 버전으로, 아마도 현존하는 최고의 시각화 패키지일 것입니다. ggplot2와 거의 동일한 기능을 가지고 있으며, 데이터를 '미학'(aesthetics)에 매핑하고 스케일, 색 구성표, 패싯(faceting), 좌표계를 제어하는 다른 레이어를 추가하여 플롯을 생성하는 '그래픽 문법'(grammar of graphics) 구문을 공유합니다.

와인 데이터에 대한 주석 및 부제목이 있는 Plotnine의 점 플롯

Seaborn에서는 부족한 기능이지만, Plotnine에서는 스택형 막대 차트(stacked bar charts)를 쉽게 사용할 수 있습니다.

아이리스(iris) 데이터에 대한 Plotnine의 스택형 막대 플롯

혹은 이 경우 닷지(dodged, 나란히 배치된) 막대 차트가 더 좋을 수도 있습니다.

아이리스(iris) 데이터에 대한 Seaborn의 닷지 막대 플롯

**Yellowbrick**

Yellowbrick은 Matplotlib 기반으로 예측 모델, 특히 scikit-learn과 함께 사용하도록 설계되었습니다. 일부 플롯 유형에서 scikit-learn 위에 진정으로 새로운 기능을 많이 추가하지는 않지만, 플롯을 매우 빠르게 생성하고 대개 깔끔하게 보입니다.

와인 데이터셋에 대한 Yellowbrick의 순위 상관 행렬

모델을 한 번의 명령으로 피팅하고 진단 플롯을 그릴 수 있습니다.

로지스틱 회귀 모델에 대한 Yellowbrick의 ROC 플롯

랜덤 포레스트 모델에서 Yellowbrick의 특성 중요도 순위

사용 가능한 다른 플롯 유형에는 평행 좌표(parallel coordinates), 매니폴드 임베딩(manifold embedding) 또는 t-SNE, 모델 잔차(model residuals), 학습 곡선(learning curve, 훈련 크기에 따른 모델 성능) 등이 있습니다.

**Altair**

Pandas가 내장 플롯 옵션으로 Matplotlib를 사용하는 반면(df.plot을 통해), Polars는 더 깔끔한 Altair를 사용합니다. Altair 플롯은 상호작용성(interactivity, 툴팁, 조정 가능한 범위 등)을 포함할 수 있지만, 시각적 요소를 깔끔하게 구성하려면 약간의 노력이 더 필요할 수 있습니다.

Pandas의 `.plot`은 변수를 색상으로 인코딩할 수 없지만, Polars의 표준 메서드는 가능합니다.

클릭 가능한 범례를 통해 한 종이 강조된 Polars 아이리스(iris) 데이터의 Altair 산점도

또 다른 예로, 툴팁과 연결된 스크롤 가능한 축을 가진 패싯('columns')이 있습니다 (아래 정적 그림에서는 보이지 않음).

Polars 와인 데이터의 Altair 패싯 막대 플롯

Polars는 또한 hvplot으로 플로팅하는 옵션도 제공하는데, 이는 기본적으로 상호작용적인 Plotly와 유사한 결과를 제공합니다.

Polars 아이리스(iris) 데이터의 hvplot 산점도 (Altair의 대안)

**Geoplot**

마지막 두 패키지는 지리공간 데이터(geospatial data) 시각화라는 특수한 경우에 사용됩니다. Python에서 이러한 경우 플로팅을 위한 데이터 소스로는 앞선 예제들의 Pandas와 동일한 GeoPandas 데이터 프레임을 사용할 수 있습니다.

geoplot은 Matplotlib 및 Cartopy의 확장 기능입니다. Seaborn이 Pandas 데이터 프레임과 작동하는 방식과 유사하게 GeoPandas 데이터 프레임과 함께 작동합니다.

수치 변수에 따라 색상이 지정된 미국 주에 대한 Geoplot의 코로플레스(choropleth) 플롯

미국 주의 수치 변수에 대한 코로플레스(choropleth, 히트맵)를 생성하는 것은 간단하지만, 주 경계에 대한 좌표 정보가 포함된 셰이프 파일(shape file)을 별도로 얻어야 합니다. 사용 가능한 다른 플롯 유형으로는 카토그램(cartogram) 및 KDE(밀도) 플롯이 있습니다. geoplot의 출력은 정적(static, 비대화형)입니다.

**Bokeh**

또 다른 옵션은 Bokeh입니다. 유연하고 강력한 플로팅 라이브러리이지만, 지리공간 시각화에 특별히 초점을 맞추지는 않습니다. 문법(syntax)이 다소 덜 익숙하지만, 적당한 노력으로 GeoPandas 객체와도 함께 작동하여 상호작용적인 플롯을 생성할 수 있습니다.

툴팁이 표시된 수치 변수에 따른 미국 주에 대한 Bokeh 코로플레스(choropleth) 플롯

개인적인 선호도는 Plotnine, 이어서 Seaborn 순이며, Geoplot은 Seaborn과의 유사성 때문에 편리합니다. 여기에 소개된 모든 패키지는 적은 노력으로도 유익하고 아름다운 차트를 만들 수 있는 기능을 제공하며, 시간을 들여 알아볼 가치가 있습니다.