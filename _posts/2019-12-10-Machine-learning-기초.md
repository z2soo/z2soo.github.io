---
title: "Machine learning 기초"
categories: 
  - Machine/Deep learning 
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - regression
toc: true
---

## 1. Regression Analysis (회귀분석)

통계적 가설 검정 기법 중 하나로써, 통계학에서 중요한 역할을 담당하는 자료분석 방법 관찰된 자료의 변수들 사이에서 나타나는 **경향성 혹은 의존성을 수학적으로 판별하고자 하는 기법**이다. 회귀분석을 먼저하는 이유는 회귀분석이 machine learning의 지도학습에 연관되기 때문!

<br>

### 경향성

회귀분석의 출발점은 "우리가 얻은(관찰한) 데이터가 어떤 특정한 경향성을 가지고 있지 않을까?" 이다. 이러한 경향성 혹은 의존성을 발견할 수 있다면, 앞으로 발생할 일에 대해 prediction(예측) 할 수 있다. 

<br>

### 변수

- 단순 회귀분석: 독립변수 1개
- 다중 회귀분석: 독립변수 2개 이상

<br><br>

## 2. Machine Learning 유래 및 응용 

Machine Learning은 한 마디로 소프트웨어, 프로그램이다. Explicit program(정해진 input - output을 제공하는 프로그램)의 한계로 인해 대두되어 사용되어지게 되었다. 

<br>


### 유래 

- Explicit program으로 해결 불가한 문제 존재
- 1959년 Arther Lee Samuel이 Machine Learning 개념을 제안하며 연구 시작
- 프로그램 자체가 데이터를 기반으로 학습을 통해 배우는 능력을 가지는 프로그래밍

<br>

### 응용

- 이미지 검색
- 도난 신용카드 판별: 기존의 소비 패턴과 다른 소비에 대해 전화 알람 제공
- 예상 매출액 산출
- 시험 성적 예측

<br><br>

## 3. Learning (학습) 종류 

Label의 유무에 따른 데이터가 학습의 종류를 결정한다.

<br>

### Supervised Learning (지도 학습)

Regression을 이용해 이 작업을 처리할 것!

- Training data set 학습용 데이터가 존재하며, label이 되어 있는 데이터
- 학습과정 '오리'라고 label 붙여진 사진 여러장 제공 → 학습 → 새로운 '오리' 사진을 보여줘도 인식!
- 3가지 유형 존재

<br>

### Unsupervised Learning(비지도 학습)

- Training data set 학습용 데이터가 존재하지만, label 되지 않은 데이터
- 학습과정 여러 사진을 제공 → 학습(프로그램이 직접 분류) → 새로운 사진을 보여주면 직접 분류

<br>

### Reinforcement Learning(강화 학습)

- 이후에 배울 부분

<br><br>

## 4. Supervised Learning 

위에서 언급한 supervised learning(지도 학습)에 대한 3가지 유형을 살펴보도록 한다. 

<br>

### Linear regression (선형회귀)

- Simple linear regression
- Multiple linear regression
- label 값이 선형의 값을 가지는 경우

```python
x(시간)   y(점수)
1        5
2        15
5        68
8        80
```

<br>

### Logistic regression (Binary classification)

- label 값이 둘 중의 하나의 값으로 떨어지는 경우

```python
x(시간)   y(점수)
1        FAIL
2        FAIL
5        PASS
8        PASS
```

<br>

### Multinomial classification

- label 값이 여러 범주 값으로 떨어지는 경우

```python
x(시간)   y(점수)
1        F
2        F
5        C
8        B
```