---
title: '판다스(Pandas) 활용 효율적인 데이터 통합(concat, merge) 및 Tidy 데이터 형식의 이해'
description: '데이터 분석에서 중요한 데이터 통합의 개념과 그 중요성을 다룬다. 판다스(Pandas)를 이용한 데이터 합치기 기술인 concat과 merge 함수의 사용법을 상세히 설명하고, 이를 통해 얻을 수 있는 데이터 분석의 정확도와 효율성 향상 방법을 알아본다. 또한, Tidy 데이터 형식이 데이터 분석 및 시각화에 어떻게 도움이 되는지 설명하며, 이를 위한 실제 코드 예시를 제공한다. 데이터 사이언스 분야에서 데이터를 효과적으로 다루고자 하는 분들에게 유용한 정보를 제공한다.'
date: '2023-12-14'
tags: [데이터 사이언스(Data Science), 데이터프레임(Dataframe), 판다스(Pandas)]
---

# 1. 데이터 통합의 중요성

데이터 분석에서 가장 중요한 단계 중 하나는 여러 소스로부터 수집된 데이터를 효과적으로 통합하는 것이다. 데이터 분석의 정확도와 효율성은 데이터가 얼마나 잘 통합되었는지에 크게 의존한다. 이런 맥락에서 판다스(Pandas)는 데이터를 통합하고 분석하는 데 필수적인 파이썬 라이브러리다.

## 1.1 판다스(Pandas)를 활용한 데이터 통합 방법

판다스(Pandas)는 데이터를 합치는 데 사용할 수 있는 다양한 기능을 제공한다. 그 중에서도 `concat`과 `merge` 함수는 데이터를 통합하는 데 특히 유용하다.

### 1.1.1 Concat(Concatenate) 기능

![concat 예시(출처: https://datascienceparichay.com/article/concat-dataframes-in-pandas/)](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/concat-merge-tidy-3.png)

`concat` 함수는 '더하다', '붙이다'라는 의미를 가지며, 데이터 프레임을 행이나 열 기준으로 단순히 이어 붙이는 작업을 수행한다. 이때, 데이터 프레임의 인덱스나 열의 이름이 일치하지 않으면, 비어있는 부분은 `NaN` 값으로 채워진다. 이러한 특징은 데이터가 서로 다른 출처에서 온 경우나 다른 형식을 가지고 있을 때 유용하다.

#### 1.1.1.1 Concat 예시 코드

```py
import pandas as pd

# 예시 데이터프레임 생성
df1 = pd.DataFrame({'A': ['A0', 'A1'], 'B': ['B0', 'B1']})
df2 = pd.DataFrame({'A': ['A2', 'A3'], 'B': ['B2', 'B3']})
```

출력:

|    | A  | B  |
|----|----|----|
| 0  | A0 | B0 |
| 1  | A1 | B1 |

|    | A  | B  |
|----|----|----|
| 0  | A2 | B2 |
| 1  | A3 | B3 |

```py
# 행 방향으로 Concat
row_concat = pd.concat([df1, df2], axis=0)

# 열 방향으로 Concat
col_concat = pd.concat([df1, df2], axis=1)
```

행 방향 출력:

|    | A  | B  |
|----|----|----|
| 0  | A0 | B0 |
| 1  | A1 | B1 |
| 0  | A2 | B2 |
| 1  | A3 | B3 |

열 방향 출력:

|    | A  | B  | A  | B  |
|----|----|----|----|----|
| 0  | A0 | B0 | A2 | B2 |
| 1  | A1 | B1 | A3 | B3 |

### 1.1.2 Merge 기능

![merge 예시(출처: https://medium.com/analytics-vidhya/python-tip-6-pandas-merge-dev-skrol-bf0be41f29b7)](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/concat-merge-tidy-4.png)

merge 함수는 두 데이터 프레임 간의 공통된 열이나 인덱스를 기준으로 데이터를 합치는 방법을 제공한다. 이는 데이터베이스의 조인 연산과 유사하며, 두 데이터 세트 간의 관계를 정의할 때 유용하다.

#### 1.1.2.1 Merge 예시 코드

```py
# 예시 데이터프레임 생성
df1 = pd.DataFrame({'Key': ['K0', 'K1', 'K2'], 'A': ['A0', 'A1', 'A2']})
df2 = pd.DataFrame({'Key': ['K0', 'K1', 'K2'], 'B': ['B0', 'B1', 'B2']})
```

출력:

|    | Key | A  |
|----|-----|----|
| 0  | K0  | A0 |
| 1  | K1  | A1 |
| 2  | K2  | A2 |

|    | Key | B  |
|----|-----|----|
| 0  | K0  | B0 |
| 1  | K1  | B1 |
| 2  | K2  | B2 |

```py
# Key 열을 기준으로 Merge
merged_df = pd.merge(df1, df2, on='Key')
```

출력:

|    | Key | A  | B  |
|----|-----|----|----|
| 0  | K0  | A0 | B0 |
| 1  | K1  | A1 | B1 |
| 2  | K2  | A2 | B2 |

# 2. Tidy 데이터

![Tidy 예시](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/concat-merge-tidy-1.png)

데이터 분석 및 시각화를 위해 데이터의 형식을 맞추는 것도 중요하다. 이를 위해 'Tidy 데이터' 형식을 사용할 수 있다. Tidy 데이터는 각 변수가 열을 형성하고, 각 관측치가 별도의 행을 구성하는 형태를 말한다. 이러한 구조는 데이터 분석과 시각화를 위한 다양한 라이브러리, 특히 Seaborn과 같은 시각화 도구에서 매우 효과적이다.


## 2.1 Tidy 데이터란?

Tidy 데이터는 다음과 같은 특징을 가지고 있다:

1.  각 변수는 개별의 열로 존재해야 한다.
1.  각 관측치는 별도의 행을 구성해야 한다.
1.  각 표는 단 하나의 관측기준으로 구성되어야 한다.

## 2.2 Tidy 예시 코드

```py
# 예시 데이터프레임 생성
df = pd.DataFrame({
    'Name': ['John', 'Jane'],
    'Height': [180, 165],
    'Weight': [75, 60]
})
```

출력:

| 이름  | 키  | 몸무게 |
|-------|-----|-------|
| John  | 180 | 75    |
| Jane  | 165 | 60    |

```py
# Tidy 형식으로 변환
tidy_df = df.melt(id_vars='Name', var_name='Measurement', value_name='Value')
```

| 이름  | 측정치 | 값  |
|-------|--------|-----|
| John  | 키     | 180 |
| John  | 몸무게 | 75  |
| Jane  | 키     | 165 |
| Jane  | 몸무게 | 60  |


## 2.3 Tidy 데이터의 이점

![지저분하고 깔끔한 데이터의 예시](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/concat-merge-tidy-2.png)

Tidy 데이터 형식은 데이터 분석 및 시각화를 보다 쉽고 효과적으로 만들어 준다. 데이터 처리의 간소화, 시각화와 통계 분석의 용이성, 유연성과 확장성 등이 주요 이점이다.


# 3. 참고
- [깔끔한 데이터(Tidy data) - Taeyoon Kim](https://partrita.github.io/posts/tidy-data/)