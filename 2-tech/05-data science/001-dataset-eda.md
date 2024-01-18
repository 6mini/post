---
title: '데이터셋(Dataset)과 EDA(탐색적 데이터 분석)란?'
description: '데이터셋(Dataset)에 대해 간단히 알아보고, 분석을 하기 위해 EDA(Exploratory Data Analysis, 탐색적 데이터 분석)의 개념과 데이터 프리프로세싱(Pre-processing) 과정에 대해 안내한다.'
date: '2023-12-12'
tags: [데이터 사이언스(Data Science), 데이터셋(Dataset), EDA, 데이터 프리프로세싱(Pre-processing)]
---

# 1. 데이터셋(Dataset)이란?

![데이터셋(Dataset)과 EDA(탐색적 데이터 분석)일러스트레이션](https://yoonminlee-blog-image.s3.ap-northeast-2.amazonaws.com/dataset-eda-1.png)

일반적으로 구조화된 데이터의 모음을 의미한다. 이 데이터는 다양한 형태로 존재할 수 있으며, 주로 특정 주제나 목적을 위해 수집되고 조직된다. 예를 들어, 연구, 기계 학습, 통계 분석 등을 위한 데이터셋이 있을 수 있다. 데이터셋은 다음과 같은 형태로 구성될 수 있다:

- **테이블 형식 데이터셋**: 가장 흔한 형태의 데이터셋으로, 행과 열로 구성되어 있다. 예를 들어, 스프레드시트나 데이터베이스 테이블이 여기에 해당한다.
- **이미지 데이터셋**: 이미지 분석이나 기계 학습의 컴퓨터 비전 분야에서 사용되는 이미지들의 모음이다.
- **텍스트 데이터셋**: 자연어 처리(NLP) 작업을 위해 사용되는 텍스트 데이터의 모음이다. 예를 들어, 책, 기사, 트윗 등이 포함될 수 있다.
- **시계열 데이터셋**: 시간에 따라 변화하는 데이터의 모음으로, 주식 시장 데이터나 기상 데이터가 여기에 해당한다.
- **오디오 데이터셋**: 음성 인식, 음악 분석 등의 작업을 위한 오디오 파일의 모음이다.

데이터셋은 연구, 비즈니스 분석, 기계 학습 모델 훈련 등 다양한 목적으로 활용된다. 데이터의 품질과 구조는 해당 데이터셋을 사용하는 작업의 성공에 중요한 영향을 미친다.


## 1.1 데이터셋 불러오기

### 1.1.1 데이터셋 정보 파악

데이터셋을 불러오기 전 데이터셋의 정보를 먼저 파악한다.

  - 행과 열의 수
  - 헤더(header) 여부
  - 결측값 여부
  - 원본 형태

#### 1.1.1.1 불러오기 전 정보 파악을 하는 이유

1. 예상하는 형태가 아닌 데이터일 수 있어서 불러오기조차 안되는 경우도 있을 수 있다.
2. **CSV**(Comma-Separated values)는 몇 가지 필드를 쉼표(,)로 구분한 텍스트 데이터 및 텍스트 파일이다.
    - 확장자는 `.csv`이며 `MIME` 형식은 `text/csv`이다.

#### 1.1.1.2 좋은 데이터셋

![좋은 데이터셋의 조건(출처: 캐글(Kaggle))](https://user-images.githubusercontent.com/79494088/143974323-104db07a-dc6e-4f23-a5fe-ac91a9a30443.png)

# 2. EDA(Exploratory Data Analysis, 탐색적 데이터 분석)

로우(raw)한 데이터를 바로 분석에 사용하기에는 어려움이 많기 때문에, 수집한 데이터가 들어왔을 때, 이를 다양한 각도에서 관찰하고 이해하는 과정이다. 데이터를 분석하기 전 복잡한 모델링이나 수식을 쓰지 않고 그래프나 통계적인 방법으로 자료를 직관적으로 탐색하는 것이 주목적이다.

## 2.1 견적을 내는 분석

사각화 같은 도구를 통해 패턴을 발견한다. 데이터의 특이성을 확인한다. 통계와 그래픽을 통해 가설을 검정하는 과정을 포함한다.

> 확증적 데이터 분석(CDA, Confirmatory Data Analysis)과의 차이
>
> EDA는 쌓여있는 데이터를 기반으로 가설을 세워 데이터를 분석하는 방법이다. 데이터의 구조와 특징을 파악하며 여기서 얻은 정보를 바탕으로 통계모형을 만든다. 보통빅데이터 분석에 사용된다.
>
> CDA는 목적을 가지고 데이터를 확보하여 분석하는 방법이다. 관측된 형태나 효과의 재현성 평가, 유의성 검정, 신뢰구간 추정 등 통계적 추론을 하는 단계이다. 가설검정, 보통은 설문조사, 논문에 대한 내용을 입증하는데 많이 사용된다.

## 2.2 방법

EDA에는 다음과 같은 방법이 있다:

- **Graphic**: 차트 혹은 그림을 이용하여 데이터를 확인한다.
- **Non-Graphic**: 요약 통계(Summary Statistics)를 통해 확인한다.
- 데이터는 또한 Univariate, Multi-vairate로 나눠진다.

### 2.2.1 Uni - Non Graphic

샘플 데이터의 분산을 확인하는 것이 주목적이다. 숫자형 데이터의 경우 요약 통계(Summary Statistics)를 제일 많이 활용한다. 이에는 다음과 같은 방법들이 존재한다:

  - Center (Mean, Median, Mod)
  - Spread (Variance, SD, IQR, Range)
  - Modality (Peak)
  - Shape (Tail, Skewness, Kurtosis)
  - Outliers
  - 범주형 데이터의 경우:
    - occurence
    - frequency
    - tabulation

### 2.2.2 Uni - Graphic

Histogram, Pie chart, Stem-leaf plot, Boxplot, QQplot 등을 사용한다. 만약 값들이 너무 다양하다면 Binning, Tabulation등을 활용한다.

#### 2.2.2.1 QQPlot

![QQPlot 개념](https://user-images.githubusercontent.com/79494088/143972754-fa3f2b35-242b-4da9-9a20-8823d553ac9a.png)

`QQPlot`은 '데이터의 분포와 이론상 분포가 잘 일치하는가?'를 확인할 수 있는 방법이다.
  - ex) 성적 분포가 고를 것이다.

### 2.2.3 Multi - Non Graphic

관계(Relationship)를 보는 것이 주된 목표이다. Cross-Tabulation과 Cross-Statistics(Correlation, Covariance) 등을 사용한다.

![Cross-Tabulation 적용](https://user-images.githubusercontent.com/79494088/143972944-3644602d-eb9b-4b62-8373-6f08cc399893.png)

위와 같이 Cross-Tabulation을 적용할 수 있다.

![Cross-Statistics 적용](https://user-images.githubusercontent.com/79494088/143973015-90e0db45-a3ee-4c61-bd3b-a7c4dcec19ad.png)

숫자형 피쳐들에 경우 위와 같이 Cross-Statistics를 통해 EDA를 진행할 수 있다.

### 2.2.4 Multi - Graphic

![Boxplots, Stacked bar, Parallel Coordinate](https://user-images.githubusercontent.com/79494088/143973160-6ddf4c40-5851-4aa5-8c8d-fdf3b678e5c1.png)

Category & Numeric 데이터에 대해서 Boxplots, Stacked bar, Parallel Coordinate, Heatmap을 사용한다.

![image](https://user-images.githubusercontent.com/79494088/143973211-d0e531fe-6c3f-48a3-9cfc-0488ddc029fa.png)

Numeric & Numeric 데이터에 대해서 Scatter Plot을 사용한다.

# 3. 판다스(Pandas) 활용 기초 EDA

## 3.1 자주 쓰이는 내장 함수

아래는 판다스(Pandas) 데이터프레임(Dataframe)을 활용하며 자주 사용하는 내장 함수 목록이다. 절대 외울 필요는 없다.

### 3.1.1 결측값(Missing Data)
- isna
- isnull
- notna
- notnull
- dropna
- fillna

### 3.1.2 Data Frame
- index
- columns
- dtypes
- info
- select_dtypes
- loc
- iloc
- insert
- head
- tail
- apply
- aggregate
- drop
- rename
- replace
- nsmallest
- nlargest
- sort_values
- sort_index
- value_counts
- describe
- shape

### 3.1.3 시각화
- plot
- plot.area
- plot.bar
- plot.barh
- plot.box
- plot.density
- plot.hexbin
- plot.hist
- plot.kde
- plot.line
- plot.pie
- plot.scatter

# 4. 데이터 프리프로세싱(Pre-processing)

![Garbage In Garbage Out](https://user-images.githubusercontent.com/79494088/143973360-88517ead-edab-4153-9e13-39544efccb2b.png)

좋은 모델을 위해서는 GIGO(Garbage In Garbage Out)를 잘 이해하는 것이 필수적이다.

## 4.1 Cleaning

노이즈를 제거하거나, inconsistency를 보정하는 과정이다. 값이 빠져있거나, 잘못 입력되어 있거나, 일관성을 가지지 않는 데이터를 제거/보정하는 과정이 포함되어 있다. 데이터를 분석하기 전 오류를 깨끗이 다듬지 않으면 잘못된 인사이트를 얻을 수 있다.

### 4.1.1 Missing Values

- Ignore the tuple (결측치가 있는 데이터 삭제)
- Manual Fill (수동으로 입력)
- Global Constant ("Unknown")
- Imputation (All mean, Class mean, Inference mean, Regression 등)

### 4.1.2 Noisy data

큰 방향성에서 벗어난 random error 혹은 variance를 포함하는 데이터를 말하며, 대부분 descriptive statistics 혹은 visualization(eda) 등을 통해 제거가 가능하다.

### 4.1.3 etc.

- Binning
- Regression
- Outlier analysis

## 4.2 Integration

여러개로 나누어져 있는 데이터들을 분석하기 편하게 하나로 합치는 과정이다.

## 4.3 Transformation

데이터의 형태를 변환하는 작업으로, 스케일링(scaling)이라고 부른다.

## 4.4 Reduction

데이터를 의미있게 줄이는 것을 의미하며, dimension reduction과 유사한 목적을 가진다.

# 5. 참고

- [공대인들이 직접쓰는 컴퓨터공부방](https://hackersstudy.tistory.com/122)