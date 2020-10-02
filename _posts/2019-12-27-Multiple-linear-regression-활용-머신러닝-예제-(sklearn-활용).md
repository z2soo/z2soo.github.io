---
title: "Multiple linear regression 활용 머신러닝 예제 (sklearn 활용)"
categories: 
  - Machine/Deep learning 
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - sklearn
  - multiple linear regression
toc: true
---

## 1. Multiple linear regression 활용 머신러닝 예제 - 데이터 준비

### 1) Module import

- sklearn 설치: anaconda 관리자 권한 실행 > 가상환경 진입 > **pip install sklearn**
- sklearn module 설치: **from sklearn import linear_model**

```python
import tensorflow as tf
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```
<br>

### 2) Data pre processing

필요한 training data 불러들이기 및 전처리 과정은 이전과 동일하다. 
단, **scipy, sklearn module은 표준화, 정규화를 자동적으로 처리해준다.** 

<br><br>

## 2. Multiple linear regression 활용 머신러닝 예제 - 과정

### 1) Training data set 설정

따로 shape, dtype 설정할 필요 없음!

```python
X = df[["Solar.R","Wind","Temp"]]
Y = df["Ozone"]
```

<br>

### 2) Learning

training data set 설정 이후, learning 이전의 모든 과정이 생략된다.

```python
lm = linear_model.LinearRegression()
lm.fit(X,Y)py
```

<br>

### 3) Prediction

데이터 설정부터 예측까지 간단하게 이루어지기에, 정확한 값을 도출했는지 확인하고자 할 때 사용하도록 한다. 

```python
prediction = lm.predict([[170,7.4,67]])
print(f'예측값은:{prediction}')

# 예측값은:[27.97594664]
```

