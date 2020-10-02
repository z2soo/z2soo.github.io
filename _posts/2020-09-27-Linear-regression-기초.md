---
title: "Linear regression 기초"
categories: 
  - Machine/Deep learning 
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - linear regression
toc: true
---

## 1. Linear regression(선형회귀)

Machine learning 지도학습 중 가장 기본이 되는 학습방식이다. 일반적으로 많은 현상(데이터)들이 선형의 형태를 가진다.

예시) 오랜 시간 공부할수록 시험 성적이 높아진다. 오랜시간 일을 하면 더 많이 벌어요.

단, 데이터가 linear한 현상, 즉 경향성을 가지는 경우에만 학습이 가능한 방식으로 우리의 예제도 linear한 형태라고 가정한다. 하나의 가설(hypothesis) 초록색 선을 데이터를 가장 잘 설명하는 형태(주황색 선)로 맞춰가는 것이 우리가 해야하는 것이다.



![image](https://user-images.githubusercontent.com/58674365/94575286-0cab0900-02af-11eb-836f-56255d1871d9.png)

<br>

### 학습, Learning이란?

초록색의 가설을 학습, Learning을 통해 수정해 나가면서 데이터를 가장 잘 표현하는 형태의 주황색의 가설을 완성해 나가야 한다.

<br>

### 최소제곱법이란?

가설을 수정해 갈 때, 이용하는 방식이다. 가설과 데이터의 오차의 제곱의 평균(Mean Squared Error, MSE)을 구해서 이 값이 작은 가설을 더 좋은 가설로 인정한다. 

가설과 데이터의 간격의 제곱의 평균을 구하는 일반식 = 비용함수(Cost Function, Loss Function)

<br>

### MSE 일반식, 비용함수란?

위 그래프의 초록색 선의 값에서 주황색 선의 값을 뺀 차이를 제곱하여 더하고 나눈 값이다. 이때, 가설 H(x) = Wx + b 으로 W는 가중치, b는 편향이지만, 직선의 방정식에서는 각각 직선의 기울기와 절편을 의미한다. Cost(W, b)가 최소가 되는 W와 b를 찾으면, x와 y의 관계를 적절히 모델링한 가설이 된다.

![image](https://user-images.githubusercontent.com/58674365/94575490-3fed9800-02af-11eb-8b11-6112e26f65c2.png)

<br><br>

## 2. Optimizer 알고리즘

선형회귀를 포함한 수많은 머신러닝, 딥 러닝의 학습은 결국 비용함수를 최소화하는 매개 변수, W와 b를 찾기 위한 작업을 수행한다. 이때 사용되는 알고리즘을 Optimizer(옵티마이저) 또는 최적화 알고리즘이라고 한다. 

Optimizer 알고리즘을 통해 적절한 W와 b를 찾는 과정이 머신러닝에서의 학습(learning, training)과정이다. 

<br>

### Cost함수와 기울기 W와의 관계?

- 기울기 W가 일정 값 이상이 된다면 오차의 값들도 커지고 Cost 함수 값도 커짐
- 기울기 W가 일정 값 이하가 된다면 오차의 값들도 커지고 Cost 함수 값도 커짐



![image](https://user-images.githubusercontent.com/58674365/94575423-306e4f00-02af-11eb-9ca5-cc04c4301fad.png)



다음과 같은 그래프가 도출되고, 우리가 찾아야 되는 부분은 cost 값이 가장 낮은 때의 W 값이다.



![image](https://user-images.githubusercontent.com/58674365/94575544-50057780-02af-11eb-973b-72be5c84b692.png)



이를 위해 기계는 임의로 W 값을 정한 후, 아래 볼록한 지점을 향해 W 값을 점차 수정해 간다. 이를 가능케 하는 것이 경사 하강법(Gradient Descent)으로써, 접선의 기울기가 0이 되는 지점을 찾는 것이다. 또한, 이는 미분값이 0이 되는 지점이다.

 경사 하강법의 아이디어는 비용함수를 미분하여 현재 W에서의 접선의 기울기를 구하고, 접선의 기울기가 낮은 방향으로 W의 값을 변경하고 다시 미분하고, 이 과정을 접선의 기울기가 0인 곳을 향해 W값을 변경하도록 하는 작업을 통한다.
