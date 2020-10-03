---
title: "Multiple linear regression 활용 머신러닝 과정"
categories: 
  - Machine/Deep learning 
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - multiple linear regression
toc: true
---

## 1. Multiple linear regression 

`Multiple linear regression(다중선형회귀)` 지도 학습은 독립변수가 2개 이상인 경우에 사용된다.

기본적으로 **가정이 변경**되고, **training data set 설정, weight 와 bias 설정, Hyphotesis 설정에서 변화**가 나타난다.
나머지 과정은 동일하다.

- **단순 선형회귀: H = W \* x + b**
- **다중 선형회귀: H = X \* W + b**

<br><br>

## 2. Multiple linear regression 과정 

### 1) Import module

독립 변수가 2개 이상인 경우, multipple linear regression(다중선형회귀)를 사용하며, module 사용은 동일하다.

```python
import tensorflow as tf
import numpy as np
import pandas as pd
```

<br>

### 2)  Training data set 설정

- 필요한 training data set 불러와서 전처리( 결측치, 극단치, 표준화 ) 잊지말고 해주기!
- 매트릭스 연산으로 넘어가면서 관용적으로 **대문자 X, Y를 사용**한다. 
- **가장 중요한 것은 shape 맞춰 주는 것!**
- **X = tf.placeholder( shape = [ 행, 열 ], dtype = )**
- **Y = tf.placeholder( shape = [ 행, 열], dtype = )**

```python
# 점수에 대한 데이터를 예시로 들어본다.
x_data[[73, 80, 75],
      [93, 88, 93],
      [89, 91, 90],
      [96, 98, 100],
      [73, 66, 70]]
y_data = [[152],[185],[180], [196],[142]]

# X = tf.placeholder(shape=[5,3],dtype=tf.float32)
X = tf.placeholder(shape=[None,3],dtype=tf.float32)
Y = tf.placeholder(shape=[None,1], dtype=tf.float32 )
```

 <br>

위의 X와 Y는 **학습할 데이터를 받는 역할**, 이후에는 **예측을 위한 데이터를 받는 역할**을 한다.

simple linear regression에서는 두 데이터 모두 1차원이였기 때문에 큰 문제가 없었지만, 
multiple linear regression에서는 학습을 위한 데이터와 예측을 위한 데이터의 형태가 동일하지 않기 때문에 shape 설정, 데이터의 형태[ 행, 열 ]에 주의해야 한다.

예를 들어, 학습 데이터는 x_data로써 shape = [ 5, 3 ]의 형태를 가지지만, 
예측을 위한 데이터는 그만큼 많은 열의 데이터를 맞춰서 넣지 않는다. 

따라서, **학습과 예측할 때의 행의 개수가 달라지기 때문에 행의 형태는 None 으로 설정한다.** 

 <br>

### 3) Weight, bias 설정

W와 b 또한 2차원 매트릭스의 형태로 나타나며, shape을 맞춰줘야 한다.
이때, W의 shape은 학습을 위해 넣는 X 데이터의 shape을 보고 결정하고, b의 shape은 결국 1개로 나오게 된다. 

- **W = tf.Variable( tf.random_normal( [ 행, 열 ] ), name = '이름' )**
- **b = tf.Variable( tf.random_normal( [ 행, 열 ] ), name = '이름' )**

 <br>

### 4) Hypothesis 설정

다중선형회귀에서는 가설 H = W * x + b 식이 H = X * H + b 으로 바뀌게 된다.
입력되는 X 데이터의 shape은 열의 수가 정해진 상태로 행의 형태로 입력되지만, 구해야 하는 W의 shape은 정해져 있지 않기 때문에, 보다 편한 연산을 위해 W와 X의 자리를 바꾼다.

- 가설: **H = X \* W + b** 
- 가설: **H = tf.matmul( X, W ) + b**

```python
H = tf.matmul(X,W) + b
```

<br>

### 5) Etc

이후의 과정( cost function, train node, runner session, learning, prediction )은 단일선형회귀와 동일하게 진행된다.