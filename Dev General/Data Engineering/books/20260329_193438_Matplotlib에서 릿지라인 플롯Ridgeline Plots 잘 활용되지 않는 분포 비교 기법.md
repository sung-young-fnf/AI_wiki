# 번역 기사

- **원본 URL:** https://medium.com/data-science-collective/ridgeline-plots-in-matplotlib-an-underused-way-to-compare-distributions-a12adf277c01
- **번역 일시:** 2026-03-29 19:34:38

---

Matplotlib에서 릿지라인 플롯(Ridgeline Plots): 잘 활용되지 않는 분포 비교 기법

여러 그룹 간의 형태, 왜도(skew), 분산(spread)을 보여주기 위해 스크래치(scratch) 방식과 joypy를 사용하여 스택형 KDE 플롯 구축하기

Andy McDonald
9분 분량
·
2026년 3월 11일

전체 크기로 이미지를 보려면 Enter를 누르거나 클릭하세요.

4개 또는 5개 이상의 그룹에 걸쳐 분포를 비교해야 할 때, 많은 차트 유형은 효과적으로 읽기 어려워집니다. 패싯 히스토그램(Faceted histograms)은 시선이 패널 사이를 오가게 만들고, 박스 플롯(box plots)은 각 그룹을 간결한 요약으로 축소하며, 바이올린 플롯(violin plots)은 그룹 수가 증가함에 따라 혼잡하게 느껴질 수 있습니다.

릿지라인 플롯은 각 그룹별로 KDE 곡선(KDE curves)을 수직으로 쌓아 올리고, 각 행을 아래 행에서 약간씩 오프셋(offset)하여 이 문제를 해결합니다. 모든 분포는 동일한 x축을 공유합니다. 모든 그룹을 한 번에 스캔할 수 있으며, 인접한 행 간의 비교가 즉각적으로 이루어집니다.

이 글에서는 릿지라인 플롯을 두 가지 방식으로 구축할 것입니다. 첫째, Matplotlib에서 스크래치(from scratch) 방식으로 구현하고, 둘째, 더 빠른 방법을 위해 joypy를 활용할 것입니다.

## 데이터셋 설정

일곱 가지 지질 형성(geological formations)에 걸친 공극률(porosity) 분포를 나타내는 합성 유정 검층(well log) 데이터를 사용할 것입니다.

```python
import matplotlib.pyplot as plt
import matplotlib.cm as cm
import pandas as pd
import numpy as np
from scipy.stats import gaussian_kde

np.random.seed(99)
formations = {
    'Vardan':   np.random.normal(0.29, 0.04, 120),
    'Kelvik':   np.random.normal(0.22, 0.05, 150),
    'Stenby':   np.random.normal(0.18, 0.04, 130),
    'Morvik':   np.random.normal(0.14, 0.03, 100),
    'Halvard':  np.random.beta(2, 8, 90) * 0.5,    # Skewed - mixed facies
    'Torven':   np.random.normal(0.20, 0.06, 140),
    'Elgfjord': np.random.normal(0.16, 0.03, 110),
}
# Clip to realistic porosity range
formations = {k: np.clip(v, 0, 0.5) for k, v in formations.items()}
```

Halvard 지층은 우측 왜도(right-skewed) 데이터셋을 시뮬레이션하기 위해 베타 분포(beta distribution)를 사용합니다. 이는 낮은 공극률 값이 지배적이며, 소수의 높은 공극률 사암층(interbeds)이 혼재된 셰일(shale) 위주의 구간을 합성적으로 표현한 것입니다.

## 1단계: 기본 방식: 패싯 히스토그램(Faceted Histograms)

먼저, 각 지층에 대해 개별 히스토그램을 사용해 보겠습니다. 이 방식은 분포를 분리하여 겹침을 방지하지만, 동일한 축을 사용할 수 있게 합니다.

```python
n = len(formations)
fig, axes = plt.subplots(n, 1, figsize=(8, 10))

for ax, (name, values) in zip(axes, formations.items()):
    ax.hist(values, bins=20)
    ax.set_ylabel(name)
plt.tight_layout()
plt.show()
```

전체 크기로 이미지를 보려면 Enter를 누르거나 클릭하세요.

이것은 첫 시도로 충분히 잘 작동합니다. 각 지층의 대략적인 중심(centre)과 분산(spread)을 볼 수 있으며, 명백히 특이한 그룹이나 이상치(outliers)도 여전히 눈에 띕니다.

문제는 분포들을 서로 비교하려고 할 때 비교가 어려워진다는 것입니다. 한 지층을 다음 지층과 비교하려면 전체 그림에서 시선을 위아래로 움직여야 합니다. 히스토그램이 개별 패널로 분리되면 형태를 읽기도 어려워지는데, 특히 왜도(skew)나 다봉성(multi-modality)과 같은 미묘한 차이를 찾을 때 더욱 그렇습니다.

이것이 바로 릿지라인 플롯이 유용해지는 지점입니다. 릿지라인 플롯은 행당 하나의 그룹이라는 동일한 구조를 유지하면서도, 훨씬 더 빠르게 스캔할 수 있는 그림으로 바꿉니다.

## 2단계: 스크래치 방식으로 릿지라인 구축하기

릿지라인 버전은 패싯 히스토그램과 동일한 기본 아이디어를 유지합니다. 즉, 지층당 하나의 분포를 수직으로 쌓는 것입니다. 차이점은 빈(bins)에 따른 개수를 보여주는 대신, 각 그룹에 대해 부드러운 밀도 곡선(density curve)을 사용하고 각각을 다음 곡선보다 약간 위에 오프셋(offset)한다는 점입니다.

이것은 두 가지 유용한 기능을 제공합니다. 첫째, 각 분포의 전반적인 형태를 더 쉽게 읽을 수 있게 합니다. 둘째, 모든 곡선이 동일한 수평축을 공유하기 때문에 인접한 지층들을 한눈에 비교하기 훨씬 쉬워집니다.

메커니즘은 간단합니다. 각 지층에 대해 고정된 x 범위에서 KDE(커널 밀도 추정, Kernel Density Estimation)를 추정하고, 그 다음 그룹 간에 최고 높이가 비교 가능하도록 스케일을 조정하고, 마지막으로 곡선 아래 영역을 채우기 전에 수직 오프셋을 추가합니다.

```python
formations_list = list(formations.keys())
n_groups = len(formations_list)
overlap = 0.8

fig, ax = plt.subplots(figsize=(10, 8))
x_range = np.linspace(0, 0.5, 300)
colours = cm.Blues(np.linspace(0.35, 0.85, n_groups))

for i, (name, colour) in enumerate(zip(formations_list, colours)):
    kde = gaussian_kde(formations[name], bw_method=0.25)
    y = kde(x_range)
    y_norm = y / y.max()
    offset = (n_groups - 1 - i) * overlap
    ax.fill_between(x_range, offset, y_norm + offset,
                    alpha=0.8, color=colour, zorder=i)
    ax.plot(x_range, y_norm + offset,
            color='white', linewidth=1, zorder=i)
    ax.text(-0.01, offset + 0.05, name,
            ha='right', va='bottom',
            fontsize=9, fontweight='bold',
            fontfamily='monospace', color='#2c3e50')
plt.show()
```

전체 크기로 이미지를 보려면 Enter를 누르거나 클릭하세요.

오버랩(overlap) 값은 릿지들이 얼마나 밀접하게 배치되는지를 제어합니다. 값을 높이면 곡선들이 시각적으로 서로 섞이기 시작합니다. 값을 줄이면 그림이 분리된 미니 플롯(mini-plots) 세트처럼 동작하기 시작합니다. 보통 중간 정도의 값이 가장 좋습니다.

각 KDE를 정규화(normalising)하는 것도 주목할 만합니다. 이 단계가 없으면 표본 크기나 밀도 피크(density peaks)가 다른 지층들이 시각적으로 그림을 지배할 수 있습니다. 정규화는 절대적인 밀도 높이에서 비교적 형태(comparative shape)로 강조점을 이동시키는데, 이러한 종류의 플롯에서는 보통 비교적 형태가 더 유용한 정보입니다.

릿지라인 플롯은 모든 파이썬 데이터 실무자가 알아야 한다고 생각하는 세 가지 잘 활용되지 않는 차트 유형 중 하나입니다. 저는 각각을 언제 사용해야 하는지에 대한 내용을 포함하여 이 세 가지를 Substack에 모두 다루었습니다. 링크는 글의 끝에 있습니다.

## 3단계: 올바른 대역폭(Bandwidth) 선택하기

기본 KDE 대역폭은 종종 데이터를 과도하게 부드럽게 만듭니다(over-smooths). 이는 실제 구조, 이봉성(bimodality), 클러스터링(clustering) 또는 왜곡된 꼬리(skewed tail)를 보고자 할 때 중요합니다.

```python
# Default bandwidth - tends to over-smooth
kde_default = gaussian_kde(formations['Halvard'])

# Narrower bandwidth - preserves more structure
kde_narrow = gaussian_kde(formations['Halvard'], bw_method=0.2)
```

Halvard 지층의 경우, 기본 대역폭은 우측 왜도(right skew)를 평탄화시킬 것입니다. 0.2에서 0.3 사이의 값은 이를 보존합니다.

## 4단계: 일관된 X축 범위

모든 그룹은 동일한 x축 범위를 공유해야 합니다. 각 KDE가 자동으로 자체 범위를 선택하도록 허용하면, 실제로는 비교할 수 없는 형태라도 비교 가능한 것처럼 보일 수 있습니다. 한 지층은 단순히 다른 값 범위에 걸쳐 플로팅되었기 때문에 더 넓거나 더 밀집된 것처럼 보일 수 있습니다.

이는 공통된 스케일(scale)에서 나란히 비교하는 릿지라인 플롯의 주요 장점을 깨뜨립니다.

이 예에서 공극률은 0과 0.5 사이로 잘려 있으므로, KDE도 동일한 고정된 구간에서 평가되어야 합니다.

```python
x_range = np.linspace(0, 0.5, 300)  # Fixed range across all formations
```

범위를 한 번 정의하고 모든 지층에 재사용하면 시각적 비교의 정직성을 유지할 수 있습니다. 최고점 위치, 분산, 꼬리 부분이 모두 동일한 참조 프레임(reference frame)에 놓이게 됩니다.

이는 일부 그룹이 다른 그룹보다 좁은 분포를 가질 때 특히 중요합니다. 각 곡선이 자동 스케일링되도록 허용하면, 좁은 분포를 가진 지층과 넓은 분포를 가진 지층이 실제보다 더 유사하게 보일 수 있습니다. x축을 고정하면 이러한 왜곡을 제거하고 형태의 차이가 스스로 드러나게 합니다.

## 5단계: 최종 다듬기

플롯의 구조가 작동하면 마지막 단계는 시각화(presentation)입니다. 릿지라인 플롯은 시각적으로 매우 빠르게 지저분해질 수 있으며, 특히 여러 곡선이 겹칠 때 더욱 그렇습니다. 몇 가지 작은 디자인 선택이 가독성에 큰 차이를 만듭니다.

첫째, 채워진 영역에 약간 투명한 파란색 그라데이션을 적용하여 쌓인 레이어들이 무겁게 느껴지지 않으면서도 구별되도록 합니다. 각 곡선의 흰색 윤곽선은 특히 인접한 분포가 겹치는 부분에서 한 릿지를 다음 릿지로부터 분리하는 데 도움을 줍니다.

지층 레이블(labels)은 범례(legend)에 두는 대신 각 릿지 바로 옆에 배치됩니다. 이는 독자가 그림과 별도의 키(key) 사이를 계속 오갈 필요가 없으므로 차트를 스캔하기 더 쉽게 만듭니다. 레이블이 이미 각 행이 무엇을 나타내는지 알려주기 때문에 y축 눈금(ticks)을 제거하는 것도 도움이 됩니다.

마지막으로, 대부분의 스파인(spines, 축 테두리)을 제거하여 분포 자체에 강조가 유지되도록 합니다. 여기서 정말 중요한 유일한 축은 하단에 있는 공유된 공극률 스케일입니다.

```python
fig, ax = plt.subplots(figsize=(10, 8), facecolor='white')

x_range = np.linspace(0, 0.5, 300)
colours = cm.Blues(np.linspace(0.35, 0.85, n_groups))
overlap = 0.8

for i, (name, colour) in enumerate(zip(formations_list, colours)):
    kde = gaussian_kde(formations[name], bw_method=0.25)
    y = kde(x_range)
    y_norm = y / y.max()
    offset = (n_groups - 1 - i) * overlap
    ax.fill_between(x_range, offset, y_norm + offset,
                    alpha=0.85, color=colour, zorder=i)
    ax.plot(x_range, y_norm + offset,
            color='white', linewidth=1.2, zorder=i)
    ax.text(-0.005, offset + 0.06, name,
            ha='right', va='bottom',
            fontsize=9, fontweight='bold',
            fontfamily='monospace', color='#2c3e50')

ax.set_xlabel('Porosity (v/v)', fontsize=12,
              fontweight='bold', labelpad=10)
ax.set_title('Porosity Distribution by Formation',
             fontsize=14, fontweight='bold', pad=15)
ax.set_xlim(0, 0.5)
ax.set_yticks([])
for spine in ['top', 'right', 'left']:
    ax.spines[spine].set_visible(False)
ax.spines['bottom'].set_visible(True)
ax.tick_params(axis='x', labelsize=10)
plt.tight_layout()
plt.savefig('ridgeline_porosity.png', dpi=150,
            bbox_inches='tight', facecolor='white')
plt.show()
```

그 결과는 여전히 상당한 정보를 담고 있지만, 기본 버전보다 훨씬 깔끔하고 읽기 쉬운 차트입니다.

전체 크기로 이미지를 보려면 Enter를 누르거나 클릭하세요.

## 릿지라인이 보여주는 것

Halvard 지층은 즉시 눈에 뜁니다. 다른 모든 지층이 대략 대칭적인 종형 곡선(bell curve)을 보이는 반면, Halvard는 높은 공극률 값으로 길게 이어지는 꼬리(tail)를 가진 우측 왜곡 분포(right-skewed distribution)를 보입니다. 박스 플롯에서는 이것이 양의 왜곡 분포(positively skewed distribution)로 나타날 것입니다. 릿지라인 플롯에서는 그 꼬리가 정확히 어디에 위치하며 주 클러스터(main cluster)와 어떻게 관련되는지 볼 수 있습니다.

또한 이전의 다중 히스토그램 플롯(multi-histogram plot)과 비교하여 각 분포의 다봉성 분포(multi-modal distributions)를 보는 것도 더 쉽습니다.

이 모든 것이 정확한 값을 읽을 필요는 없습니다. 형태 자체가 스토리를 직접 전달합니다.

## joypy 사용하기

각 레이어를 수동으로 구축하지 않고 릿지라인 효과를 원한다면, joypy는 훨씬 더 빠른 방법을 제공합니다. joypy는 KDE 계산, 스태킹(stacking), 오프셋(offsetting) 기능을 단일 함수 호출로 묶어주어 탐색적 작업이나 빠른 초안 작성에 유용합니다.

사용하려면 데이터를 그룹 레이블(group labels)용 열 하나와 플로팅할 값용 열 하나를 가진 간단한 긴 형식 데이터프레임(long-form dataframe)으로 재구성(reshape)해야 합니다.

```python
import joypy

records = []
for name, values in formations.items():
    for v in values:
        records.append({'Formation': name, 'Porosity': v})
df = pd.DataFrame(records)

fig, axes = joypy.joyplot(
    df,
    by='Formation',
    column='Porosity',
    figsize=(10, 7),
    colormap=cm.Blues,
    alpha=0.8,
    linecolor='white',
    linewidth=1.2,
    title='Porosity Distribution by Formation',
    x_range=[0, 0.5]
)
```

이것만으로도 훨씬 적은 코드로 읽기 쉬운 릿지라인 플롯을 얻을 수 있습니다.

절충점(trade-off)은 제어권(control)입니다. 라이브러리 접근 방식은 편리하지만, 스크래치 방식은 대역폭 선택, 레이블 배치, 색상 처리, 간격, 그리고 그림의 전체적인 구성에 대해 훨씬 더 많은 권한을 줍니다. 아이디어를 탐색하는 중이라면 joypy가 편리합니다. 실제로 게시할 차트를 목표로 한다면 수동 버전이 보통 더 적절하게 조정하기 쉽습니다.

## 릿지라인 플롯을 사용할 때

릿지라인 플롯은 다음과 같은 경우에 가장 효과적입니다.

*   4개에서 20개 사이의 그룹을 가지고 있을 때. 그룹 수가 더 적으면 간단한 오버랩 KDE(overlapping KDE) 또는 바이올린 플롯(violin plot)이 더 깔끔합니다. 더 많으면 레이블이 읽기 어려워집니다.
*   각 그룹이 최소 50개 이상의 관측치(observations)를 가지고 있을 때. KDE는 작은 표본에서는 신뢰할 수 없습니다.
*   x축이 연속적이며 모든 그룹에서 공유될 때.
*   단순 요약 통계(summary statistics) 대신 형태, 왜도(skewness), 이봉성(bimodality), 분산(spread)을 보여주고 싶을 때.

## 전체 코드

```python
import matplotlib.pyplot as plt
import matplotlib.cm as cm
import numpy as np
from scipy.stats import gaussian_kde

np.random.seed(99)
formations = {
    'Vardan':   np.random.normal(0.32, 0.04, 120),
    'Kelvik':   np.random.normal(0.22, 0.05, 150),
    'Stenby':   np.random.normal(0.18, 0.04, 130),
    'Morvik':   np.random.normal(0.14, 0.03, 100),
    'Halvard':  np.random.beta(2, 8, 90) * 0.5,
    'Torven':   np.random.normal(0.20, 0.06, 140),
    'Elgfjord': np.random.normal(0.16, 0.03, 110),
}
formations = {k: np.clip(v, 0, 0.5) for k, v in formations.items()}
formations_list = list(formations.keys())
n_groups = len(formations_list)
overlap = 0.8

fig, ax = plt.subplots(figsize=(10, 8), facecolor='white')
x_range = np.linspace(0, 0.5, 300)
colours = cm.Blues(np.linspace(0.35, 0.85, n_groups))

for i, (name, colour) in enumerate(zip(formations_list, colours)):
    kde = gaussian_kde(formations[name], bw_method=0.25)
    y = kde(x_range)
    y_norm = y / y.max()
    offset = (n_groups - 1 - i) * overlap
    ax.fill_between(x_range, offset, y_norm + offset,
                    alpha=0.85, color=colour, zorder=i)
    ax.plot(x_range, y_norm + offset,
            color='white', linewidth=1.2, zorder=i)
    ax.text(-0.005, offset + 0.06, name,
            ha='right', va='bottom',
            fontsize=9, fontweight='bold',
            fontfamily='monospace', color='#2c3e50')

ax.set_xlabel('Porosity (v/v)', fontsize=12, fontweight='bold', labelpad=10)
ax.set_title('Porosity Distribution by Formation',
             fontsize=14, fontweight='bold', pad=15)
ax.set_xlim(0, 0.5)
ax.set_yticks([])
for spine in ['top', 'right', 'left']:
    ax.spines[spine].set_visible(False)
ax.spines['bottom'].set_visible(True)
ax.tick_params(axis='x', labelsize=10)
plt.tight_layout()
plt.savefig('ridgeline_porosity.png', dpi=150,
            bbox_inches='tight', facecolor='white')
plt.show()
```

릿지라인 플롯을 스크래치 방식으로 구축하면 출력에 대한 완전한 제어권을 가질 수 있지만, 다음과 같은 분명한 질문이 생깁니다: 어떤 다른 차트 유형을 도구 키트(toolkit)에 추가할 가치가 있을까요? 릿지라인 플롯은 범프 차트(bump charts) 및 비스웜 플롯(beeswarm plots)과 함께 대부분의 사람들이 잘 사용하지 않는 시각화 유형으로, 심지어 히스토그램이나 박스 플롯보다 훨씬 더 이야기를 잘 전달할 수 있을 때조차도 그렇습니다. Substack에 유정 검층 데이터를 사용한 전후 예시와 함께 이 세 가지를 모두 다루는 글을 작성했습니다.

→ 아마도 사용하지 않지만 사용해야 할 파이썬 차트 3가지

데이터 과학 (Data Science)
데이터 시각화 (Data Visualization)
파이썬 (Python)
데이터 분석 (Data Analysis)
지구과학 (Geoscience)

작성자: Andy McDonald
1.16만 팔로워 · 48 팔로잉

데이터 분석, 머신러닝(Machine Learning), AI에 대한 열정을 가진 석유 지질학자(Petrophysicist)이자 데이터 과학자(Data Scientist). 더 많은 정보는 Substack에서: https://andymcdonaldgeo.substack.com/