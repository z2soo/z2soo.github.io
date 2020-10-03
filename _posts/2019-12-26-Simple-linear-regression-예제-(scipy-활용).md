---
title: "Simple linear regression 예제 (scipy 활용)"
categories: 
  - Machine/Deep learning 
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - scipy
  - simple linear regression
toc: true
---

## 1. Simple linear regression 예제: tensorflow 활용

지도학습 종류 `simple linear regression`, `multiple linear regression`, `logistic regression`3 가지!
지금까지 다룬 부분은 simple linear regression(단순회귀)이며, 앞서 예제를 통해 사용해보았다.
앞선 예제에서 tensorflow 모듈을 사용했다면, 이번에는 scipy 모듈을 사용하여 두 방법의 차이를 비교보도록 한다. 

tensorflow 활용 방식은 설명 없이 식만 서술했다. 
보다 자세한 설명은 이전 예제 포스팅을 보면 된다. (같은 학습 데이터 사용)<br>

```python
# 1. module import
import tensorflow as tf
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from scipy import stats


# 2. data loading(raw data)
df = pd.read_csv('C:/Users/student/Desktop/머신러닝/ozone.csv')
df = df[["Ozone","Temp"]]


# 3. 데이터 전처리 진행 (Data Pre Processing)
# 3-1. 결측치 처리
df = df.dropna(how="any", inplace=False)


# 3-2. 이상치 확인 및 처리
plt.boxplot(df['Ozone']) # 상위 이상치 존재
plt.boxplot(df['Temp']) # 이상치 존재 안함

# IQR = 3사분위 - 1사분위
q1, q3 = np.percentile(df['Ozone'],[25, 75])
iqr = q3 - q1
upper = q3 + iqr * 1.5
mask = df["Ozone"] > upper
df = df.loc[~mask]


# 3-3. 정규화, 표준화
# 정규화(normalization): (요소값-최소값) / (최대값-최소값)
# 표준화(standardzation): (요소값-평균) / 표준편차
df["Temp_Norm"] = (df["Temp"] - df["Temp"].min()) / (df["Temp"].max() - df["Temp"].min()) 
df["Ozone_Norm"] = (df["Ozone"] - df["Ozone"].min()) / (df["Ozone"].max() - df["Ozone"].min()) 


# 4. tensorflow 활용 machine learning
# 4-1. training data set 
x_data = df["Temp_Norm"]
y_data = df["Ozone_Norm"]
x = tf.placeholder(dtype=tf.float32)
y = tf.placeholder(dtype=tf.float32)


# 4-2. weight, bias
W = tf.Variable(tf.random_normal([1]), name="weight")
b = tf.Variable(tf.random_normal([1]), name="bias")


# 4-3. hypothesis
H = W * x + b


# 4-4. cost function
cost = tf.reduce_mean(tf.square(H - y))


# 4-5. train node
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)
train = optimizer.minimize(cost)


# 4-6. session 
sess = tf.Session()
sess.run(tf.global_variables_initializer())


# 4-7. learning
for step in range(3000):
    _, cost_val = sess.run([train, cost],
                          feed_dict={x: x_data,
                                    y: y_data})
    if step % 300 == 0:
        print(f'cost값:{cost_val}')
        

# 5. prediction
# 가설이 완성되었고, 온도가 80도 일때의 오존량을 알고 싶다면?
# 80 값을 0-1 값으로 표준화 시켜서 계산해야함
tmp = (80-df["Temp"].min()) / (df["Temp"].max()-df["Temp"].min())
print(tmp)     #0.575
result_scale = sess.run(H, feed_dict={x: tmp})
print(result_scale)     #[0.36654675]
result = result_scale * (df["Ozone"].max()-df["Ozone"].min()) + df["Ozone"].min()
print(result)     #[45.352158]
```

<br><br>

## 2. Simple linear regression 예제: scipy 활용

scipy의 stats module을 사용하면, 기본적인 전처리 외에 표준화, 정규화 이후의 과정은 진행하지 않아도 된다.
scipy stats module 내에서 자동적으로 처리해 준다. <br>

### 1) Module import

```python
import numpy as np
from scipy import stats
```

<br>

### 2) Training data set 설정 

- training data 불러오기 및 전처리 진행( 결측치, 이상치 )
- **scipy는 표준화, 정규화 작업이 불필요! 알아서 해결해준다.** 

```python
df = pd.read_csv('C:/Users/student/Desktop/머신러닝/ozone.csv') 
# 데이터 전처리 과정은 위의 tensorflow 코드 참조

x = df["Temp"]
y = df["Ozone"]
```

<br>

### 3) Machine learning : Scipy 활용

- 데이터만 준비되면, 이후의 학습과 예측 모두 한번에 가능하다. 
- **tensorflower 처럼 placeholder 사용할 필요가 없다.**
- x, y를 활용한 simple linear regression = **stats.linregress( x, y )**
- 출력되는 값: slope = W
- 출력되는 값: intercept = b
- 출력되는 **slope 값과 intercept 값을 H 식에 대입하면 prediction 값이 출력된다.**<br>

```python
x = df["Temp"]
y = df["Ozone"]

result = stats.linregress(x,y)
print(result)
print(80*result[0] + result[1])

LinregressResult(slope=2.344807849550286, intercept=-142.19084219133276, rvalue=0.7515377011359844, pvalue=5.802324426210489e-22, stderr=0.19448560867502498)
45.39378577269011
```