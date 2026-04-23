# 번역 기사

- **원본 URL:** https://pub.towardsai.net/pandas-vs-polars-vs-duckdb-a-data-scientists-guide-to-choosing-the-right-tool-9fd08f8e5119
- **번역 일시:** 2026-04-10 10:47:15

---

pandas vs Polars vs DuckDB: 데이터 과학자를 위한 올바른 도구 선택 가이드

### 서론

pandas는 지난 10년 이상 Python에서 테이블 형식 데이터를 다루는 표준 도구였습니다. 하지만 데이터셋이 커지고 성능 요구 사항이 증가함에 따라 두 가지 현대적인 대안이 등장했습니다. 바로 Rust로 작성된 DataFrame 라이브러리인 Polars와 분석에 최적화된 임베디드 SQL 데이터베이스인 DuckDB입니다.

각 도구는 다양한 시나리오에서 뛰어난 성능을 보입니다:

| 도구   | 백엔드   | 실행 모델                 | 최적의 사용처                                   |
| ------ | -------- | ------------------------- | ----------------------------------------------- |
| pandas | C/Python | 즉시 실행(Eager), 단일 스레드 | 소규모 데이터셋, 프로토타이핑, ML 통합          |
| Polars | Rust     | 지연/즉시 실행(Lazy/Eager), 다중 스레드 | 대규모 분석, 데이터 파이프라인                   |
| DuckDB | C++      | SQL 우선(SQL-first), 다중 스레드 | SQL 워크플로, 임베디드 분석, 파일 쿼리           |

이 가이드는 세 가지 도구를 실제 사례와 함께 비교하여 작업 흐름에 적합한 도구를 선택하는 데 도움을 줍니다.

### 도구별 강점 요약

#### pandas

pandas는 Python을 위한 오리지널 DataFrame 라이브러리로, 인터랙티브 데이터 탐색에 탁월하며 ML 생태계와 완벽하게 통합됩니다. 주요 기능은 다음과 같습니다:

*   scikit-learn, statsmodels 및 시각화 라이브러리와 직접 호환
*   풍부한 확장 생태계 (pandas-profiling, pandasql 등)
*   성숙한 시계열 기능
*   대부분의 데이터 과학자가 이미 알고 있는 익숙한 문법

#### Polars

Polars는 Rust 기반의 DataFrame 라이브러리로, Python에 다중 스레드 실행(multi-threaded execution) 및 쿼리 최적화(query optimization) 기능을 제공하여 속도를 향상하도록 설계되었습니다. 주요 기능은 다음과 같습니다:

*   기본적으로 사용 가능한 모든 CPU 코어를 사용하여 작업 속도를 높임
*   먼저 쿼리 계획(query plan)을 구축한 다음, 필요한 부분만 실행함
*   RAM보다 큰 데이터셋 처리를 위한 스트리밍 모드(streaming mode)
*   pandas와 유사한 API로 표현적인 메서드 체이닝(method chaining) 제공

#### DuckDB

DuckDB는 분석에 최적화된 임베디드 SQL 데이터베이스(embedded SQL database)로, 로컬 파일에 데이터베이스 수준의 쿼리 최적화 기능을 제공합니다. 주요 기능은 다음과 같습니다:

*   완전한 분석 쿼리(analytical query) 지원을 포함한 네이티브 SQL 문법
*   CSV, Parquet, JSON 파일을 로드 없이 직접 쿼리
*   사용 가능한 메모리를 초과하는 데이터의 경우 자동으로 디스크 저장 공간을 사용
*   서버 설정이 필요 없는 제로 구성(zero-configuration) 임베디드 데이터베이스

### 설정

세 라이브러리 모두 설치:

```bash
pip install pandas polars duckdb
```

벤치마킹을 위한 샘플 데이터 생성:

```python
import pandas as pd
import numpy as np
np.random.seed(42)
n_rows = 5_000_000
data = {
    "category": np.random.choice(["Electronics", "Clothing", "Food", "Books"], size=n_rows),
    "region": np.random.choice(["North", "South", "East", "West"], size=n_rows),
    "amount": np.random.rand(n_rows) * 1000,
    "quantity": np.random.randint(1, 100, size=n_rows),
}
df_pandas = pd.DataFrame(data)
df_pandas.to_csv("sales_data.csv", index=False)
print(f"Created sales_data.csv with {n_rows:,} rows")
```

```
Created sales_data.csv with 5,000,000 rows
```

### 문법 비교

세 가지 도구 모두 동일한 작업을 다른 문법으로 수행할 수 있습니다. 다음은 일반적인 작업에 대한 비교입니다.

#### 행 필터링

**pandas:**
부울 조건(boolean conditions)과 함께 대괄호 표기법을 사용하며, 각 조건마다 DataFrame 이름을 반복해야 합니다:

```python
import pandas as pd
df_pd = pd.read_csv("sales_data.csv")
result_bracket = df_pd[(df_pd["amount"] > 500) & (df_pd["category"] == "Electronics")]
result_bracket.head()
```

```
┌────┬─────────────┬────────┬────────────┬──────────┐
│    │ category    │ region │ amount     │ quantity │
├────┼─────────────┼────────┼────────────┼──────────┤
│ 7  │ Electronics │ West   │ 662.803066 │ 80       │
│ 15 │ Electronics │ North  │ 826.004963 │ 25       │
│ 30 │ Electronics │ North  │ 766.081832 │ 7        │
│ 31 │ Electronics │ West   │ 772.084261 │ 36       │
│ 37 │ Electronics │ East   │ 527.967145 │ 35       │
└────┴─────────────┴────────┴────────────┴──────────┘
```

대신, 더 깔끔한 SQL과 유사한 문법을 제공하는 `query()` 메서드를 사용할 수 있습니다:

```python
result_query = df_pd.query("amount > 500 and category == 'Electronics'")
```

그러나 `query()`는 문자열 기반이므로 IDE 자동 완성 기능이 없습니다. 문자열 메서드와 같은 복잡한 작업에는 여전히 대괄호가 필요합니다:

```python
result_str = df_pd[df_pd["category"].str.startswith("Elec")]
result_str.head()
```

```
┌────┬─────────────┬────────┬────────────┬──────────┐
│    │ category    │ region │ amount     │ quantity │
├────┼─────────────┼────────┼────────────┼──────────┤
│ 2  │ Electronics │ North  │ 450.941022 │ 93       │
│ 6  │ Electronics │ West   │ 475.843957 │ 61       │
│ 7  │ Electronics │ West   │ 662.803066 │ 80       │
│ 15 │ Electronics │ North  │ 826.004963 │ 25       │
│ 21 │ Electronics │ South  │ 292.399383 │ 13       │
└────┴─────────────┴────────┴────────────┴──────────┘
```

**Polars:**
pandas와 달리, Polars는 모든 필터에 대해 단일 문법을 사용합니다. `pl.col()` 표현식은 IDE 자동 완성 기능을 지원하는 타입 안전(type-safe) 방식이며, 단순 비교와 문자열 메서드와 같은 복잡한 작업을 모두 처리합니다:

```python
import polars as pl
df_pl = pl.read_csv("sales_data.csv")
result_pl = df_pl.filter(
    (pl.col("amount") > 500) & (pl.col("category") == "Electronics")
)
result_pl.head()
```

```
┌───────────────┬─────────┬────────────┬──────────┐
│ category      │ region  │ amount     │ quantity │
├───────────────┼─────────┼────────────┼──────────┤
│ str           │ str     │ f64        │ i64      │
│ “Electronics” │ “West”  │ 662.803066 │ 80       │
│ “Electronics” │ “North” │ 826.004963 │ 25       │
│ “Electronics” │ “North” │ 766.081832 │ 7        │
│ “Electronics” │ “West”  │ 772.084261 │ 36       │
│ “Electronics” │ “East”  │ 527.967145 │ 35       │
└───────────────┴─────────┴────────────┴──────────┘
```

**DuckDB:**
WHERE 절을 사용하는 표준 SQL을 사용하며, SQL을 아는 사람들에게 더 읽기 쉽습니다.

```python
import duckdb
result_duckdb = duckdb.sql("""
    SELECT * FROM 'sales_data.csv'
    WHERE amount > 500 AND category = 'Electronics'
""").df()
result_duckdb.head()
```

```
┌───┬─────────────┬────────┬────────────┬──────────┐
│   │ category    │ region │ amount     │ quantity │
├───┼─────────────┼────────┼────────────┼──────────┤
│ 0 │ Electronics │ West   │ 662.803066 │ 80       │
│ 1 │ Electronics │ North  │ 826.004963 │ 25       │
│ 2 │ Electronics │ North  │ 766.081832 │ 7        │
│ 3 │ Electronics │ West   │ 772.084261 │ 36       │
│ 4 │ Electronics │ East   │ 527.967145 │ 35       │
└───┴─────────────┴────────┴────────────┴──────────┘
```

#### 열 선택

**pandas:**
이중 대괄호는 선택된 열들로 구성된 DataFrame을 반환합니다.

```python
result_pd = df_pd[["category", "amount"]]
result_pd.head()
```

```
┌───┬─────────────┬────────────┐
│   │ category    │ amount     │
├───┼─────────────┼────────────┤
│ 0 │ Food        │ 516.653322 │
│ 1 │ Books       │ 937.337226 │
│ 2 │ Electronics │ 450.941022 │
│ 3 │ Food        │ 674.488081 │
│ 4 │ Food        │ 188.847906 │
└───┴─────────────┴────────────┘
```

**Polars:**
`select()` 메서드는 열 선택 의도를 명확하게 전달합니다.

```python
result_pl = df_pl.select(["category", "amount"])
result_pl.head()
```

```
┌───────────────┬────────────┐
│ category      │ amount     │
├───────────────┼────────────┤
│ str           │ f64        │
│ “Food”        │ 516.653322 │
│ “Books”       │ 937.337226 │
│ “Electronics” │ 450.941022 │
│ “Food”        │ 674.488081 │
│ “Food”        │ 188.847906 │
└───────────────┴────────────┘
```

**DuckDB:**
SQL의 SELECT 절은 SQL 사용자에게 열 선택을 직관적으로 만듭니다.

```python
result_duckdb = duckdb.sql("""
    SELECT category, amount FROM 'sales_data.csv'
""").df()
result_duckdb.head()
```

```
┌───┬─────────────┬────────────┐
│   │ category    │ amount     │
├───┼─────────────┼────────────┤
│ 0 │ Food        │ 516.653322 │
│ 1 │ Books       │ 937.337226 │
│ 2 │ Electronics │ 450.941022 │
│ 3 │ Food        │ 674.488081 │
│ 4 │ Food        │ 188.847906 │
└───┴─────────────┴────────────┘
```

#### GroupBy 집계

**pandas:**
사전을 사용하여 집계(aggregation)를 지정하지만, 다단계(multi-level) 열 헤더를 반환하여, 종종 추가 사용 전에 평탄화 작업이 필요합니다.

```python
result_pd = df_pd.groupby("category").agg({
    "amount": ["sum", "mean"],
    "quantity": "sum"
})
result_pd.head()
```

```
┌─────────────┬──────────────┬────────────┬──────────┐
│             │ amount       │            │ quantity │
├─────────────┼──────────────┼────────────┼──────────┤
│             │ sum          │ mean       │ sum      │
│ Books       │ 6.247506e+08 │ 499.998897 │ 62463285 │
│ Clothing    │ 6.253924e+08 │ 500.139837 │ 62505224 │
│ Electronics │ 6.244453e+08 │ 499.938189 │ 62484265 │
│ Food        │ 6.254034e+08 │ 499.916417 │ 62577943 │
└─────────────┴──────────────┴────────────┴──────────┘
```

**Polars:**
각 집계에 대해 명시적인 `alias()` 호출을 사용하며, 후처리 없이 바로 평평한(flat) 열 이름을 생성합니다.

```python
result_pl = df_pl.group_by("category").agg([
    pl.col("amount").sum().alias("amount_sum"),
    pl.col("amount").mean().alias("amount_mean"),
    pl.col("quantity").sum().alias("quantity_sum"),
])
result_pl.head()
```

```
┌───────────────┬────────────┬─────────────┬──────────────┐
│ category      │ amount_sum │ amount_mean │ quantity_sum │
├───────────────┼────────────┼─────────────┼──────────────┤
│ str           │ f64        │ f64         │ i64          │
│ “Clothing”    │ 6.2539e8   │ 500.139837  │ 62505224     │
│ “Books”       │ 6.2475e8   │ 499.998897  │ 62463285     │
│ “Electronics” │ 6.2445e8   │ 499.938189  │ 62484265     │
│ “Food”        │ 6.2540e8   │ 499.916417  │ 62577943     │
└───────────────┴────────────┴─────────────┴──────────────┘
```

**DuckDB:**
열 별칭(column aliases)을 사용하는 표준 SQL 집계는 깔끔하고 평평한(flat) 출력을 생성하여 즉시 사용 가능합니다.

```python
result_duckdb = duckdb.sql("""
    SELECT
        category,
        SUM(amount) as amount_sum,
        AVG(amount) as amount_mean,
        SUM(quantity) as quantity_sum
    FROM 'sales_data.csv'
    GROUP BY category
""").df()
result_duckdb.head()
```

```
┌───┬─────────────┬──────────────┬─────────────┬──────────────┐
│   │ category    │ amount_sum   │ amount_mean │ quantity_sum │
├───┼─────────────┼──────────────┼─────────────┼──────────────┤
│ 0 │ Food        │ 6.254034e+08 │ 499.916417  │ 62577943.0   │
│ 1 │ Electronics │ 6.244453e+08 │ 499.938189  │ 62484265.0   │
│ 2 │ Clothing    │ 6.253924e+08 │ 500.139837  │ 62505224.0   │
│ 3 │ Books       │ 6.247506e+08 │ 499.998897  │ 62463285.0   │
└───┴─────────────┴──────────────┴─────────────┴──────────────┘
```

#### 열 추가

**pandas:**
`assign()` 메서드는 `df_pd["amount"]`와 같이 DataFrame 참조를 반복하여 새 열을 생성합니다.

```python
result_pd = df_pd.assign(
    amount_with_tax=df_pd["amount"] * 1.1,
    high_value=df_pd["amount"] > 500
)
result_pd.head()
```

```
┌───┬─────────────┬────────┬────────────┬──────────┬─────────────────┬────────────┐
│   │ category    │ region │ amount     │ quantity │ amount_with_tax │ high_value │
├───┼─────────────┼────────┼────────────┼──────────┼─────────────────┼────────────┤
│ 0 │ Food        │ South  │ 516.653322 │ 40       │ 568.318654      │ True       │
│ 1 │ Books       │ East   │ 937.337226 │ 45       │ 1031.070948     │ True       │
│ 2 │ Electronics │ North  │ 450.941022 │ 93       │ 496.035124      │ False      │
│ 3 │ Food        │ East   │ 674.488081 │ 46       │ 741.936889      │ True       │
│ 4 │ Food        │ East   │ 188.847906 │ 98       │ 207.732697      │ False      │
└───┴─────────────┴────────┴────────────┴──────────┴─────────────────┴────────────┘
```

**Polars:**
`with_columns()` 메서드는 DataFrame 이름을 반복하지 않고 자연스럽게 연결되는 조합 가능한 표현식을 사용합니다.

```python
result_pl = df_pl.with_columns([
    (pl.col("amount") * 1.1).alias("amount_with_tax"),
    (pl.col("amount") > 500).alias("high_value")
])
result_pl.head()
```

```
┌───────────────┬─────────┬────────────┬──────────┬─────────────────┬────────────┐
│ category      │ region  │ amount     │ quantity │ amount_with_tax │ high_value │
├───────────────┼─────────┼────────────┼──────────┼─────────────────┼────────────┤
│ str           │ str     │ f64        │ i64      │ f64             │ bool       │
│ “Food”        │ “South” │ 516.653322 │ 40       │ 568.318654      │ true       │
│ “Books”       │ “East”  │ 937.337226 │ 45       │ 1031.070948     │ true       │
│ “Electronics” │ “North” │ 450.941022 │ 93       │ 496.035124      │ false      │
│ “Food”        │ “East”  │ 674.488081 │ 46       │ 741.936889      │ true       │
│ “Food”        │ “East”  │ 188.847906 │ 98       │ 207.732697      │ false      │
└───────────────┴─────────┴────────────┴──────────┴─────────────────┴────────────┘
```

**DuckDB:**
SQL의 SELECT 절은 쿼리 내에서 직접 새 열을 정의하여 변환을 읽기 쉽게 만듭니다.

```sql
result_duckdb = duckdb.sql("""
    SELECT *,
        amount * 1.1 as amount_with_tax,
        amount > 500 as high_value
    FROM df_pd
""").df()
result_duckdb.head()
```

```
┌───┬─────────────┬────────┬────────────┬──────────┬─────────────────┬────────────┐
│   │ category    │ region │ amount     │ quantity │ amount_with_tax │ high_value │
├───┼─────────────┼────────┼────────────┼──────────┼─────────────────┼────────────┤
│ 0 │ Food        │ South  │ 516.653322 │ 40       │ 568.318654      │ True       │
│ 1 │ Books       │ East   │ 937.337226 │ 45       │ 1031.070948     │ True       │
│ 2 │ Electronics │ North  │ 450.941022 │ 93       │ 496.035124      │ False      │
│ 3 │ Food        │ East   │ 674.488081 │ 46       │ 741.936889      │ True       │
│ 4 │ Food        │ East   │ 188.847906 │ 98       │ 207.732697      │ False      │
└───┴─────────────┴────────┴────────────┴──────────┴─────────────────┴────────────┘
```

#### 조건부 논리

**pandas:**
`np.where()`에 조건이 추가될 때마다 중첩 수준(nesting level)이 하나씩 늘어납니다. 세 단계의 계층에서는 최종 값이 두 단계 깊이 중첩됩니다:

```python
import numpy as np
# 읽기 어려움: "low"는 두 개의 np.where() 호출 안에 중첩되어 있습니다.
result_np = df_pd.assign(
    value_tier=np.where(
        df_pd["amount"] > 700, "high",
        np.where(df_pd["amount"] > 300, "medium", "low")
    )
)
result_np[["category", "amount", "value_tier"]].head()
```

```
┌───┬─────────────┬────────────┬────────────┐
│   │ category    │ amount     │ value_tier │
├───┼─────────────┼────────────┼────────────┤
│ 0 │ Food        │ 516.653322 │ medium     │
│ 1 │ Books       │ 937.337226 │ high       │
│ 2 │ Electronics │ 450.941022 │ medium     │
│ 3 │ Food        │ 674.488081 │ medium     │
│ 4 │ Food        │ 188.847906 │ low        │
└───┴─────────────┴────────────┴────────────┘
```

숫자 구간화(numeric binning)의 경우 `pd.cut()`이 더 깔끔합니다:

```python
result_pd = df_pd.assign(
    value_tier=pd.cut(
        df_pd["amount"],
        bins=[-np.inf, 300, 700, np.inf],
        labels=["low", "medium", "high"]
    )
)
result_pd[["category", "amount", "value_tier"]].head()
```

```
┌───┬─────────────┬────────────┬────────────┐
│   │ category    │ amount     │ value_tier │
├───┼─────────────┼────────────┼────────────┤
│ 0 │ Food        │ 516.653322 │ medium     │
│ 1 │ Books       │ 937.337226 │ high       │
│ 2 │ Electronics │ 450.941022 │ medium     │
│ 3 │ Food        │ 674.488081 │ medium     │
│ 4 │ Food        │ 188.847906 │ low        │
└───┴─────────────┴────────────┴────────────┘
```

하지만 `pd.cut()`에는 몇 가지 단점이 있습니다:

*   숫자 범위에만 작동합니다.
*   조건(amount > 700) 대신 경계([-inf, 300, 700, inf])로 생각해야 합니다.
*   개방형 구간(open-ended bins)을 위해 NumPy가 필요합니다.

비숫자 조건이나 혼합 조건의 경우 다시 `np.where()`로 돌아가야 합니다:

```python
# Electronics AND amount > 500 일 경우 "premium" - pd.cut()으로는 불가능합니다.
result = df_pd.assign(
    tier=np.where(
        (df_pd["category"] == "Electronics") & (df_pd["amount"] > 500),
        "premium", "standard"
    )
)
result.head()
```

```
┌───┬─────────────┬────────┬────────────┬──────────┬──────────┐
│   │ category    │ region │ amount     │ quantity │ tier     │
├───┼─────────────┼────────┼────────────┼──────────┼──────────┤
│ 0 │ Food        │ South  │ 516.653322 │ 40       │ standard │
│ 1 │ Books       │ East   │ 937.337226 │ 45       │ standard │
│ 2 │ Electronics │ North  │ 450.941022 │ 93       │ standard │
│ 3 │ Food        │ East   │ 674.488081 │ 46       │ standard │
│ 4 │ Food        │ East   │ 188.847906 │ 98       │ standard │
└───┴─────────────┴────────┴────────────┴──────────┴──────────┘
```

**Polars:**
`when().then().otherwise()` 체인은 pandas의 두 가지 문제를 해결합니다: `np.where()`처럼 중첩되지 않고, `pd.cut()`처럼 숫자 범위에 국한되지 않고 모든 조건에 작동합니다. 동일한 문법으로 단순 구간화(binning) 및 복잡한 혼합 조건을 모두 처리합니다:

```python
result_pl = df_pl.with_columns(
    pl.when(pl.col("amount") > 700).then(pl.lit("high"))
      .when(pl.col("amount") > 300).then(pl.lit("medium"))
      .otherwise(pl.lit("low"))
      .alias("value_tier")
)
result_pl.select(["category", "amount", "value_tier"]).head()
```

```
┌───────────────┬────────────┬────────────┐
│ category      │ amount     │ value_tier │
├───────────────┼────────────┼────────────┤
│ str           │ f64        │ str        │
│ “Food”        │ 516.653322 │ “medium”   │
│ “Books”       │ 937.337226 │ “high”     │
│ “Electronics” │ 450.941022 │ “medium”   │
│ “Food”        │ 674.488081 │ “medium”   │
│ “Food”        │ 188.847906 │ “low”      │
└───────────────┴────────────┴────────────┘
```

**DuckDB:**
표준 SQL `CASE WHEN` 문법은 SQL을 아는 사람들에게 더 읽기 쉽습니다.

```sql
result_duckdb = duckdb.sql("""
    SELECT category, amount,
        CASE
            WHEN amount > 700 THEN 'high'
            WHEN amount > 300 THEN 'medium'
            ELSE 'low'
        END as value_tier
    FROM df_pd
""").df()
result_duckdb.head()
```

```
┌───┬─────────────┬────────────┬────────────┐
│   │ category    │ amount     │ value_tier │
├───┼─────────────┼────────────┼────────────┤
│ 0 │ Food        │ 516.653322 │ medium     │
│ 1 │ Books       │ 937.337226 │ high       │
│ 2 │ Electronics │ 450.941022 │ medium     │
│ 3 │ Food        │ 674.488081 │ medium     │
│ 4 │ Food        │ 188.847906 │ low        │
└───┴─────────────┴────────────┴────────────┘
```

#### 윈도우 함수

**pandas:**
`groupby().transform()`을 사용하며, 각 계산마다 `groupby` 절을 반복해야 합니다.

```python
result_pd = df_pd.assign(
    category_avg=df_pd.groupby("category")["amount"].transform("mean"),
    category_rank=df_pd.groupby("category")["amount"].rank(ascending=False)
)
result_pd[["category", "amount", "category_avg", "category_rank"]].head()
```

```
┌───┬─────────────┬────────────┬──────────────┬───────────────┐
│   │ category    │ amount     │ category_avg │ category_rank │
├───┼─────────────┼────────────┼──────────────┼───────────────┤
│ 0 │ Food        │ 516.653322 │ 499.916417   │ 604342.0      │
│ 1 │ Books       │ 937.337226 │ 499.998897   │ 78423.0       │
│ 2 │ Electronics │ 450.941022 │ 499.938189   │ 685881.0      │
│ 3 │ Food        │ 674.488081 │ 499.916417   │ 407088.0      │
│ 4 │ Food        │ 188.847906 │ 499.916417   │ 1015211.0     │
└───┴─────────────┴────────────┴──────────────┴───────────────┘
```

**Polars:**
`over()` 표현식은 어떤 계산에도 파티션(partition)을 추가하여 반복적인 그룹 정의를 피할 수 있습니다.

```python
result_pl = df_pl.with_columns([
    pl.col("amount").mean().over("category").alias("category_avg"),
    pl.col("amount").rank(descending=True).over("category").alias("category_rank")
])
result_pl.select(["category", "amount", "category_avg", "category_rank"]).head()
```

```
┌───────────────┬────────────┬──────────────┬───────────────┐
│ category      │ amount     │ category_avg │ category_rank │
├───────────────┼────────────┼──────────────┼───────────────┤
│ str           │ f64        │ f64          │ f64           │
│ “Food”        │ 516.653322 │ 499.916417   │ 604342.0      │
│ “Books”       │ 937.337226 │ 499.998897   │ 78423.0       │
│ “Electronics” │ 450.941022 │ 499.938189   │ 685881.0      │
│ “Food”        │ 674.488081 │ 499.916417   │ 407088.0      │
│ “Food”        │ 188.847906 │ 499.916417   │ 1015211.0     │
└───────────────┴────────────┴──────────────┴───────────────┘
```

**DuckDB:**
`OVER (PARTITION BY ...)`를 사용하는 SQL 윈도우 함수(window functions)는 이러한 유형의 계산을 위한 업계 표준입니다.

```sql
result_duckdb = duckdb.sql("""
    SELECT category, amount,
        AVG(amount) OVER (PARTITION BY category) as category_avg,
        RANK() OVER (PARTITION BY category ORDER BY amount DESC) as category_rank
    FROM df_pd
""").df()
result_duckdb.head()
```

```
┌───┬──────────┬────────────┬──────────────┬───────────────┐
│   │ category │ amount     │ category_avg │ category_rank │
├───┼──────────┼────────────┼──────────────┼───────────────┤
│ 0 │ Clothing │ 513.807166 │ 500.139837   │ 608257        │
│ 1 │ Clothing │ 513.806596 │ 500.139837   │ 608258        │
│ 2 │ Clothing │ 513.806515 │ 500.139837   │ 608259        │
│ 3 │ Clothing │ 513.806063 │ 500.139837   │ 608260        │
│ 4 │ Clothing │ 513.806056 │ 500.139837   │ 608261        │
└───┴──────────┴────────────┴──────────────┴───────────────┘
```

### 데이터 로딩 성능

pandas는 단일 CPU 코어에서 CSV 파일을 읽습니다. Polars와 DuckDB는 다중 스레드(multi-threaded) 실행을 사용하여 작업을 사용 가능한 모든 코어에 분산하여 파일의 여러 부분을 동시에 읽습니다.

#### pandas

단일 스레드(single-threaded) CSV 파싱(parsing)은 데이터를 순차적으로 로드합니다.

```text
┌─────────────────────────────────────────────┐
│ CPU Core 1                                  │
│ ┌─────────────────────────────────────────┐ │
│ │ Chunk 1 → Chunk 2 → Chunk 3 → ... → End │ │
│ └─────────────────────────────────────────┘ │
│ CPU Core 2  [idle]                          │
│ CPU Core 3  [idle]                          │
│ CPU Core 4  [idle]                          │
└─────────────────────────────────────────────┘
```

```python
pandas_time = %timeit -o pd.read_csv("sales_data.csv")
```

```
1.05 s ± 26.9 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

#### Polars

다중 스레드(multi-threaded) 파싱은 파일 읽기를 사용 가능한 모든 코어에 분산합니다.

```text
┌─────────────────────────────────────────────┐
│ CPU Core 1  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 2  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 3  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 4  ┌────────────────┐              │
│             │ ████████████   │              │
└─────────────────────────────────────────────┘
```

```python
polars_time = %timeit -o pl.read_csv("sales_data.csv")
```

```
137 ms ± 34 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

#### DuckDB

Polars와 유사하게, 파일 읽기는 사용 가능한 모든 코어에 분산됩니다.

```text
┌─────────────────────────────────────────────┐
│ CPU Core 1  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 2  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 3  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 4  ┌────────────────┐              │
│             │ ████████████   │              │
└─────────────────────────────────────────────┘
```

```python
duckdb_time = %timeit -o duckdb.sql("SELECT * FROM 'sales_data.csv'").df()
```

```
762 ms ± 77.8 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

```python
print(f"Polars는 pandas보다 {pandas_time.average / polars_time.average:.1f}배 빠릅니다")
print(f"DuckDB는 pandas보다 {pandas_time.average / duckdb_time.average:.1f}배 빠릅니다")
```

```
Polars는 pandas보다 7.7배 빠릅니다
DuckDB는 pandas보다 1.4배 빠릅니다
```

Polars가 CSV 읽기에서 7.7배 빠른 속도를 보이지만, DuckDB의 1.4배 향상은 파싱이 주요 초점이 아님을 보여줍니다. DuckDB는 파일을 직접 쿼리하거나 복잡한 분석 쿼리를 실행할 때 빛을 발합니다.

### 쿼리 최적화

#### pandas: 최적화 없음

pandas는 각 단계에서 중간 DataFrame을 생성하며 즉시(eagerly) 작업을 실행합니다. 이로 인해 메모리가 낭비되고 최적화가 불가능해집니다.

```text
┌─────────────────────────────────────────────────────────────┐
│ 단계 1: 모든 행 로드         → 메모리에 1천만 행         │
│ 단계 2: 필터링 (amount > 100)  → 메모리에 5백만 행         │
│ 단계 3: GroupBy                → 새 DataFrame              │
│ 단계 4: 평균                   → 최종 결과                 │
└─────────────────────────────────────────────────────────────┘
메모리: ████████████████████████████████ (높음 - 모든 중간 결과 저장)
```

```python
def pandas_query():
    return (
        pd.read_csv("sales_data.csv")
        .query('amount > 100')
        .groupby('category')['amount']
        .mean()
    )
pandas_opt_time = %timeit -o pandas_query()
```

```
1.46 s ± 88.9 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

이 접근 방식에는 세 가지 문제가 있습니다:

*   **전체 CSV 로드:** 모든 행이 필터링 전에 읽힙니다.
*   **조건자 푸시다운(Predicate pushdown) 없음:** 전체 파일을 메모리에 로드한 후에 행이 필터링됩니다.
*   **프로젝션 푸시다운(Projection pushdown) 없음:** 사용하지 않는 열도 모두 로드됩니다.

`usecols`를 수동으로 추가하여 더 적은 열을 로드할 수 있습니다:

```python
def pandas_query_optimized():
    return (
        pd.read_csv("sales_data.csv", usecols=["category", "amount"])
        .query('amount > 100')
        .groupby('category')['amount']
        .mean()
    )
pandas_usecols_time = %timeit -o pandas_query_optimized()
```

```
1.06 s ± 48.2 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

이것은 더 빠르지만 두 가지 단점이 있습니다:

*   **수동 추적:** 열을 직접 지정해야 합니다. 쿼리를 변경하면 `usecols`도 업데이트해야 합니다.
*   **행 필터링 없음:** 필터가 적용되기 전에 모든 행이 여전히 로드됩니다.

Polars와 DuckDB는 실행 전에 쿼리를 분석하여 이 두 가지를 자동으로 처리합니다.

#### Polars: 지연 평가

Polars는 지연 평가(lazy evaluation)를 지원하며, 실행 전에 쿼리 계획(query plan)을 구축하고 최적화합니다.

```text
┌─────────────────────────────────────────────────────────────┐
│ 쿼리 계획 구축:                                           │
│   scan_csv → filter → group_by → agg                        │
│                                                             │
│ 적용된 최적화:                                              │
│   • 조건자 푸시다운 (스캔 중에 필터링)                      │
│   • 프로젝션 푸시다운 (필요한 열만 읽기)                  │
│   • 다중 스레드 실행 (CPU 코어 전체에 걸쳐 병렬 처리)     │
└─────────────────────────────────────────────────────────────┘
메모리: ████████ (낮음 - 중간 DataFrame 없음)
```

```python
query_pl = (
    pl.scan_csv("sales_data.csv")
    .filter(pl.col("amount") > 100)
    .group_by("category")
    .agg(pl.col("amount").mean().alias("avg_amount"))
)
# 최적화된 쿼리 계획 보기
print(query_pl.explain())
```

```
AGGREGATE[maintain_order: false]
  [col("amount").mean().alias("avg_amount")] BY [col("category")]
  FROM
  Csv SCAN [sales_data.csv] [id: 4687118704]
  PROJECT 2/4 COLUMNS
  SELECTION: [(col("amount")) > (100.0)]
```

쿼리 계획은 다음 최적화를 보여줍니다:

*   **조건자 푸시다운(Predicate pushdown):** `SELECTION`은 로드 후가 아닌 스캔 중에 필터링합니다.
*   **프로젝션 푸시다운(Projection pushdown):** `PROJECT 2/4 COLUMNS`는 필요한 열만 읽습니다.
*   **작업 재정렬(Operation reordering):** 집계(Aggregate)는 전체 데이터셋이 아닌 필터링된 데이터에서 실행됩니다.

최적화된 쿼리 실행:

```python
def polars_query():
    return (
        pl.scan_csv("sales_data.csv")
        .filter(pl.col("amount") > 100)
        .group_by("category")
        .agg(pl.col("amount").mean().alias("avg_amount"))
        .collect()
    )
polars_opt_time = %timeit -o polars_query()
```

```
148 ms ± 32.3 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

#### DuckDB: SQL 최적화 도구

DuckDB의 SQL 최적화 도구는 유사한 최적화를 자동으로 적용합니다.

```text
┌─────────────────────────────────────────────────────────────┐
│ 쿼리 계획 구축:                                           │
│   SQL → 파서 → 최적화 도구 → 실행 계획                      │
│                                                             │
│ 적용된 최적화:                                              │
│   • 조건자 푸시다운 (스캔 중 WHERE 절 적용)                 │
│   • 프로젝션 푸시다운 (SELECT 절에서 필요한 열만 선택)    │
│   • 벡터화된 실행 (배치당 1024행 처리)                      │
└─────────────────────────────────────────────────────────────┘
메모리: ████████ (낮음 - 스트리밍 실행)
```

```python
def duckdb_query():
    return duckdb.sql("""
        SELECT category, AVG(amount) as avg_amount
        FROM 'sales_data.csv'
        WHERE amount > 100
        GROUP BY category
    """).df()
duckdb_opt_time = %timeit -o duckdb_query()
```

```
245 ms ± 12.1 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

최적화된 쿼리의 성능을 비교해 봅시다:

```python
print(f"Polars는 pandas보다 {pandas_opt_time.average / polars_opt_time.average:.1f}배 빠릅니다")
print(f"DuckDB는 pandas보다 {pandas_opt_time.average / duckdb_opt_time.average:.1f}배 빠릅니다")
```

```
Polars는 pandas보다 9.9배 빠릅니다
DuckDB는 pandas보다 6.0배 빠릅니다
```

Polars는 이 벤치마크에서 DuckDB(9.9배 대 6.0배)보다 우수한 성능을 보입니다. 이는 Rust 기반 엔진이 필터-집계 패턴을 효율적으로 처리하기 때문입니다. DuckDB의 강점은 조인(join) 및 서브쿼리(subquery)가 포함된 복잡한 SQL 쿼리에 있습니다.

### GroupBy 성능

집계(aggregates)를 계산하려면 모든 행을 스캔해야 하며, 이 작업은 CPU 코어 수에 선형적으로 비례합니다. 이로 인해 groupby 작업은 병렬 실행(parallel execution)을 테스트하는 가장 명확한 기준이 됩니다.

groupby 벤치마크를 위한 데이터를 로드해 봅시다:

```python
# 공정한 비교를 위해 데이터 로드
df_pd = pd.read_csv("sales_data.csv")
df_pl = pl.read_csv("sales_data.csv")
```

#### pandas: 단일 스레드

pandas는 단일 CPU 코어에서 groupby 작업을 처리하므로, 대규모 데이터셋에서는 병목 현상이 발생합니다.

```text
┌─────────────────────────────────────────────────────────────┐
│ CPU Core 1                                                  │
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ 그룹 A → 그룹 B → 그룹 C → 그룹 D → ... → 집계          │ │
│ └─────────────────────────────────────────────────────────┘ │
│ CPU Core 2  [idle]                                          │
│ CPU Core 3  [idle]                                          │
│ CPU Core 4  [idle]                                          │
└─────────────────────────────────────────────────────────────┘
```

```python
def pandas_groupby():
    return df_pd.groupby("category")["amount"].mean()
pandas_groupby_time = %timeit -o pandas_groupby()
```

```
271 ms ± 135 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

#### Polars: 다중 스레드

Polars는 데이터를 코어에 분할하고, 부분 집계(partial aggregates)를 병렬로 계산한 다음, 결과를 병합합니다.

```text
┌─────────────────────────────────────────────────────────────┐
│ CPU Core 1  ┌──────────────┐                                │
│             │ ████████████ │ → 부분 집계                     │
│ CPU Core 2  ┌──────────────┐                                │
│             │ ████████████ │ → 부분 집계                     │
│ CPU Core 3  ┌──────────────┐                                │
│             │ ████████████ │ → 부분 집계                     │
│ CPU Core 4  ┌──────────────┐                                │
│             │ ████████████ │ → 부분 집계                     │
│                      ↓                                      │
│             최종 병합 → 결과                                │
└─────────────────────────────────────────────────────────────┘
```

```python
def polars_groupby():
    return df_pl.group_by("category").agg(pl.col("amount").mean())
polars_groupby_time = %timeit -o polars_groupby()
```

```
31.1 ms ± 3.65 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
```

#### DuckDB: 다중 스레드

Polars와 유사하게, DuckDB는 데이터를 코어에 분할하고, 부분 집계를 병렬로 계산한 다음, 결과를 병합합니다.

```text
┌─────────────────────────────────────────────────────────────┐
│ CPU Core 1  ┌──────────────┐                                │
│             │ ████████████ │ → 부분 집계                     │
│ CPU Core 2  ┌──────────────┐                                │
│             │ ████████████ │ → 부분 집계                     │
│ CPU Core 3  ┌──────────────┐                                │
│             │ ████████████ │ → 부분 집계                     │
│ CPU Core 4  ┌──────────────┐                                │
│             │ ████████████ │ → 부분 집계                     │
│                      ↓                                      │
│             최종 병합 → 결과                                │
└─────────────────────────────────────────────────────────────┘
```

```python
def duckdb_groupby():
    return duckdb.sql("""
        SELECT category, AVG(amount)
        FROM df_pd
        GROUP BY category
    """).df()
duckdb_groupby_time = %timeit -o duckdb_groupby()
```

```
29 ms ± 3.33 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
```

```python
print(f"Polars는 pandas보다 {pandas_groupby_time.average / polars_groupby_time.average:.1f}배 빠릅니다")
print(f"DuckDB는 pandas보다 {pandas_groupby_time.average / duckdb_groupby_time.average:.1f}배 빠릅니다")
```

```
Polars는 pandas보다 8.7배 빠릅니다
DuckDB는 pandas보다 9.4배 빠릅니다
```

DuckDB와 Polars는 모두 병렬 실행(parallel execution)을 활용하여 유사한 성능(9.4배 대 8.7배)을 보입니다. DuckDB의 약간 우세한 점은 지연된 구체화(late materialization)와 벡터 단위 파이프라인(vector-at-a-time pipelined execution)에서 비롯되며, 이는 Polars가 일부 작업에서 여전히 구체화할 수 있는 중간 결과를 생성하는 것을 피합니다.

### 메모리 효율성

#### pandas: 전체 메모리 로드

pandas는 전체 데이터셋을 RAM에 로드합니다.

```text
┌─────────────────────────────────────────────────────────────┐
│  RAM                                                        │
│  ┌────────────────────────────────────────────────────────┐ │
│  │████████████████████████████████████████████████████████│ │
│  │██████████████████ 모든 1천만 행 ████████████████████████│ │
│  │████████████████████████████████████████████████████████│ │
│  └────────────────────────────────────────────────────────┘ │
│  사용량: 707,495 KB (전체 데이터셋 메모리 로드)           │
└─────────────────────────────────────────────────────────────┘
```

```python
df_pd_mem = pd.read_csv("sales_data.csv")
pandas_mem = df_pd_mem.memory_usage(deep=True).sum() / 1e3
print(f"pandas 메모리 사용량: {pandas_mem:,.0f} KB")
```

```
pandas 메모리 사용량: 707,495 KB
```

RAM보다 큰 데이터셋의 경우 pandas는 메모리 부족 오류(out-of-memory error)를 발생시킵니다.

#### Polars: 스트리밍 모드

Polars는 스트리밍 모드(streaming mode)로 데이터를 처리하여 모든 것을 로드하지 않고도 청크(chunks)를 처리할 수 있습니다.

```text
┌─────────────────────────────────────────────────────────────┐
│  RAM                                                        │
│  ┌────────────────────────────────────────────────────────┐ │
│  │█                                                       │ │
│  │                    (결과만)                            │ │
│  │                                                        │ │
│  └────────────────────────────────────────────────────────┘ │
│  사용량: 0.06 KB (청크 스트리밍, 결과만 유지)             │
└─────────────────────────────────────────────────────────────┘
```

```python
result_pl_stream = (
    pl.scan_csv("sales_data.csv")
    .group_by("category")
    .agg(pl.col("amount").mean())
    .collect(streaming=True)
)
polars_mem = result_pl_stream.estimated_size() / 1e3
print(f"Polars 결과 메모리: {polars_mem:.2f} KB")
```

```
Polars 결과 메모리: 0.06 KB
```

RAM보다 큰 파일의 경우 `collect()` 대신 `sink_parquet`을 사용하십시오. 이는 청크가 처리될 때 결과를 직접 디스크에 기록하여 전체 데이터셋을 메모리에 유지하지 않습니다.

```python
(
    pl.scan_csv("sales_data.csv")
    .filter(pl.col("amount") > 500)
    .sink_parquet("filtered_sales.parquet")
)
```

#### DuckDB: 자동 디스크 스필

DuckDB는 데이터가 사용 가능한 RAM을 초과할 경우 중간 결과를 자동으로 임시 파일에 기록합니다.

```text
┌─────────────────────────────────────────────────────────────┐
│  RAM                              디스크 (필요한 경우)        │
│  ┌──────────────────────────┐     ┌──────────────────────┐  │
│  │█                         │     │░░░░░░░░░░░░░░░░░░░░░░│  │
│  │     (최대 500MB)         │  →  │    (여기에 오버플로)   │  │
│  │                          │     │                      │  │
│  └──────────────────────────┘     └──────────────────────┘  │
│  사용량: 0.42 KB (RAM이 가득 차면 디스크로 스필)            │
└─────────────────────────────────────────────────────────────┘
```

```python
# 메모리 제한 및 임시 디렉터리 구성
duckdb.sql("SET memory_limit = '500MB'")
duckdb.sql("SET temp_directory = '/tmp/duckdb_temp'")
# DuckDB는 RAM보다 큰 데이터를 자동으로 처리합니다
result_duckdb_mem = duckdb.sql("""
    SELECT category, AVG(amount) as avg_amount
    FROM 'sales_data.csv'
    GROUP BY category
""").df()
duckdb_mem = result_duckdb_mem.memory_usage(deep=True).sum() / 1e3
print(f"DuckDB 결과 메모리: {duckdb_mem:.2f} KB")
```

```
DuckDB 결과 메모리: 0.42 KB
```

DuckDB의 코어 외부(out-of-core) 처리는 메모리가 제한된 임베디드 분석에 이상적입니다.

```python
print(f"pandas: {pandas_mem:,.0f} KB (전체 데이터셋)")
print(f"Polars: {polars_mem:.2f} KB (결과만)")
print(f"DuckDB: {duckdb_mem:.2f} KB (결과만)")
print(f"\nPolars는 pandas보다 {pandas_mem / polars_mem:,.0f}배 적은 메모리를 사용합니다")
print(f"DuckDB는 pandas보다 {pandas_mem / duckdb_mem:,.0f}배 적은 메모리를 사용합니다")
```

```
pandas: 707,495 KB (전체 데이터셋)
Polars: 0.06 KB (결과만)
DuckDB: 0.42 KB (결과만)

Polars는 pandas보다 11,791,583배 적은 메모리를 사용합니다
DuckDB는 pandas보다 1,684,512배 적은 메모리를 사용합니다
```

수백만 배의 감소는 스트리밍에서 비롯됩니다. Polars와 DuckDB는 데이터를 청크 단위로 처리하고 4행의 결과만 메모리에 유지하는 반면, pandas는 동일한 집계(aggregation)를 계산하기 위해 1천만 행 전체를 유지해야 합니다.

### 조인 작업

테이블 조인(Joining tables)은 데이터 분석에서 가장 일반적인 작업 중 하나입니다. 1백만 개의 주문과 10만 명의 고객 테이블 간의 좌측 조인(left join)을 각 도구가 어떻게 처리하는지 비교해 보겠습니다.

조인 벤치마킹을 위한 두 개의 테이블을 만들어 봅시다:

```python
# 주문 테이블 생성 (1백만 행)
orders_pd = pd.DataFrame({
    "order_id": range(1_000_000),
    "customer_id": np.random.randint(1, 100_000, size=1_000_000),
    "amount": np.random.rand(1_000_000) * 500
})
# 고객 테이블 생성 (10만 행)
customers_pd = pd.DataFrame({
    "customer_id": range(100_000),
    "region": np.random.choice(["North", "South", "East", "West"], size=100_000)
})
# Polars로 변환
orders_pl = pl.from_pandas(orders_pd)
customers_pl = pl.from_pandas(customers_pd)
```

#### pandas: 단일 스레드

pandas는 단일 CPU 코어에서 조인(join)을 처리합니다.

```text
┌─────────────────────────────────────────────┐
│ CPU Core 1                                  │
│ ┌─────────────────────────────────────────┐ │
│ │ 행 1 → 행 2 → 행 3 → ... → 1백만 행     │ │
│ └─────────────────────────────────────────┘ │
│ CPU Core 2  [idle]                          │
│ CPU Core 3  [idle]                          │
│ CPU Core 4  [idle]                          │
└─────────────────────────────────────────────┘
```

```python
def pandas_join():
    return orders_pd.merge(customers_pd, on="customer_id", how="left")
pandas_join_time = %timeit -o pandas_join()
```

```
60.4 ms ± 6.98 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
```

#### Polars: 다중 스레드

Polars는 사용 가능한 모든 CPU 코어에 걸쳐 조인(join)을 분산합니다.

```text
┌─────────────────────────────────────────────┐
│ CPU Core 1  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 2  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 3  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 4  ┌────────────────┐              │
│             │ ████████████   │              │
└─────────────────────────────────────────────┘
```

```python
def polars_join():
    return orders_pl.join(customers_pl, on="customer_id", how="left")
polars_join_time = %timeit -o polars_join()
```

```
11.8 ms ± 6.42 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
```

#### DuckDB: 다중 스레드

Polars와 유사하게, DuckDB는 사용 가능한 모든 CPU 코어에 걸쳐 조인(join)을 분산합니다.

```text
┌─────────────────────────────────────────────┐
│ CPU Core 1  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 2  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 3  ┌────────────────┐              │
│             │ ████████████   │              │
│ CPU Core 4  ┌────────────────┐              │
│             │ ████████████   │              │
└─────────────────────────────────────────────┘
```

```python
def duckdb_join():
    return duckdb.sql("""
        SELECT o.*, c.region
        FROM orders_pd o
        LEFT JOIN customers_pd c ON o.customer_id = c.customer_id
    """).df()
duckdb_join_time = %timeit -o duckdb_join()
```

```
55.7 ms ± 1.14 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
```

조인 성능을 비교해 봅시다:

```python
print(f"Polars는 pandas보다 {pandas_join_time.average / polars_join_time.average:.1f}배 빠릅니다")
print(f"DuckDB는 pandas보다 {pandas_join_time.average / duckdb_join_time.average:.1f}배 빠릅니다")
```

```
Polars는 pandas보다 5.1배 빠릅니다
DuckDB는 pandas보다 1.1배 빠릅니다
```

Polars는 5.1배의 속도 향상을 제공하는 반면, DuckDB는 1.1배의 개선만 보입니다. 두 도구 모두 다중 스레딩(multi-threading)을 사용하지만, Polars의 조인 알고리즘과 네이티브 DataFrame 출력은 DuckDB가 `.df()`를 통해 결과를 반환할 때 발생하는 변환 오버헤드(conversion overhead)를 피합니다.

### 상호 운용성

세 가지 도구 모두 원활하게 함께 작동합니다. 단일 파이프라인에서 각 도구의 장점을 최대한 활용하세요.

#### pandas DataFrame을 DuckDB로

SQL로 pandas DataFrame을 직접 쿼리합니다:

```python
df = pd.DataFrame({
    "product": ["A", "B", "C"],
    "sales": [100, 200, 150]
})
# DuckDB는 변수 이름으로 pandas DataFrame을 쿼리합니다.
result = duckdb.sql("SELECT * FROM df WHERE sales > 120").df()
print(result)
```

```
  product  sales
0       B    200
1       C    150
```

#### Polars를 pandas로

ML 라이브러리가 pandas를 필요로 할 때 Polars DataFrame을 변환합니다:

```python
df_polars = pl.DataFrame({
    "feature1": [1, 2, 3],
    "feature2": [4, 5, 6],
    "target": [0, 1, 0]
})
# scikit-learn을 위해 pandas로 변환
df_pandas = df_polars.to_pandas()
print(type(df_pandas))
```

```
<class 'pandas.core.frame.DataFrame'>
```

#### DuckDB를 Polars로

쿼리 결과를 Polars DataFrame으로 가져옵니다:

```python
result = duckdb.sql("""
    SELECT category, SUM(amount) as total
    FROM 'sales_data.csv'
    GROUP BY category
""").pl()
print(type(result))
print(result)
```

```
<class 'polars.dataframe.frame.DataFrame'>
shape: (4, 2)
┌─────────────┬──────────┐
│ category    ┆ total    │
│ ---         ┆ ---      │
│ str         ┆ f64      │
╞═════════════╪══════════╡
│ Electronics ┆ 6.2445e8 │
│ Food        ┆ 6.2540e8 │
│ Clothing    ┆ 6.2539e8 │
│ Books       ┆ 6.2475e8 │
└─────────────┴──────────┘
```

#### 결합된 파이프라인 예시

각 도구는 고유한 강점을 가지고 있습니다: DuckDB는 SQL 쿼리를 최적화하고, Polars는 변환을 병렬화하며, pandas는 ML 라이브러리와 통합됩니다. 세 가지 모두를 활용하기 위해 단일 파이프라인에서 이들을 결합할 수 있습니다:

```python
# 1단계: 초기 SQL 쿼리를 위한 DuckDB
aggregated = duckdb.sql("""
    SELECT category, region,
           SUM(amount) as total_amount,
           COUNT(*) as order_count
    FROM 'sales_data.csv'
    GROUP BY category, region
""").pl()
# 2단계: 추가 변환을 위한 Polars
enriched = (
    aggregated
    .with_columns([
        (pl.col("total_amount") / pl.col("order_count")).alias("avg_order_value"),
        pl.col("category").str.to_uppercase().alias("category_upper")
    ])
    .filter(pl.col("order_count") > 100000)
)
# 3단계: 시각화 또는 ML을 위해 pandas로 변환
final_df = enriched.to_pandas()
print(final_df.head())
```

```
  category region  total_amount  order_count  avg_order_value category_upper
0      Food   East  1.563586e+08       312918       499.679004           FOOD
1      Food  North  1.563859e+08       312637       500.215456           FOOD
2  Clothing  North  1.560532e+08       311891       500.345286       CLOTHING
3  Clothing   East  1.565054e+08       312832       500.285907       CLOTHING
4      Food   West  1.560994e+08       312662       499.259318           FOOD
```

### 의사결정 매트릭스

모든 시나리오에서 단일 도구가 최고인 경우는 없습니다. 다음 표를 사용하여 작업 흐름에 적합한 도구를 선택하세요.

#### 성능 요약

단일 머신에서 1천만 행 데이터를 사용한 벤치마크 결과:

| 작업            | pandas | Polars              | DuckDB                  |
| --------------- | ------ | ------------------- | ----------------------- |
| CSV 읽기 (1천만 행) | 1.05초 | 137ms               | 762ms                   |
| GroupBy         | 271ms  | 31ms                | 29ms                    |
| 조인 (1백만 행)   | 60ms   | 12ms                | 56ms                    |
| 메모리 사용량   | 707 MB | 0.06 KB (스트리밍)  | 0.42 KB (디스크 스필)   |

Polars는 CSV 읽기(pandas보다 7.7배 빠름) 및 조인(5배 빠름)에서 선두를 달립니다. DuckDB는 groupby 성능에서 Polars와 동등하며, 자동 디스크 스필(spill-to-disk)을 통해 가장 적은 메모리를 사용합니다.

#### 기능 비교

각 도구는 속도, 메모리, 생태계 통합 사이에서 다른 장단점(trade-offs)을 가집니다:

| 기능                  | pandas    | Polars    | DuckDB        |
| --------------------- | --------- | --------- | ------------- |
| 다중 스레딩(Multi-threading)    | 아니오      | 예        | 예            |
| 지연 평가(Lazy evaluation)    | 아니오      | 예        | 해당 없음(SQL) |
| 쿼리 최적화(Query optimization) | 아니오      | 예        | 예            |
| RAM 초과 데이터 처리      | 아니오      | 스트리밍  | 디스크 스필   |
| SQL 인터페이스        | 아니오      | 제한적    | 네이티브      |
| ML 통합               | 탁월함    | 좋음      | 제한적        |

pandas는 Polars와 DuckDB를 빠르게 만드는 성능 기능을 가지고 있지 않지만, ML 워크플로에는 여전히 필수적입니다. DataFrame 체이닝을 선호하는지 또는 SQL 문법을 선호하는지에 따라 Polars와 DuckDB 중 하나를 선택하십시오.

### 권장 사항

최고의 도구는 데이터 크기, 워크플로 선호도 및 제약 조건에 따라 달라집니다:

*   **소규모 데이터(<1백만 행):** 단순성을 위해 pandas 사용
*   **대규모 데이터(1백만~1억 행):** 5~10배의 속도 향상을 위해 Polars 또는 DuckDB 사용
*   **SQL 선호 워크플로:** DuckDB 사용
*   **DataFrame 선호 워크플로:** Polars 사용
*   **메모리 제약이 있는 경우:** Polars (스트리밍) 또는 DuckDB (디스크 스필) 사용
*   **ML 파이프라인 통합:** pandas 사용 (필요에 따라 Polars/DuckDB에서 변환)
*   **운영 데이터 파이프라인:** 팀 선호도에 따라 Polars (DataFrame) 또는 DuckDB (SQL) 사용

### 결론

코드가 모두 pandas로 작성되어 있다면, 전부 다시 작성할 필요는 없습니다. 중요한 부분만 마이그레이션할 수 있습니다:

*   **먼저 프로파일링(Profile)하세요:** 어떤 pandas 작업이 느린지 찾아내세요.
*   **Polars로 대체하세요:** CSV 읽기, groupby, 조인에서 가장 큰 성능 향상을 볼 수 있습니다.
*   **DuckDB를 추가하세요:** SQL이 체인형 DataFrame 작업보다 더 깔끔할 때 사용하세요.

최종 ML 단계에서는 pandas를 유지하십시오. 필요할 때 `df.to_pandas()`로 변환하세요.