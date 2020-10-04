---
title: "Multinomial logistic regression 예제 (BMI)"
categories: 
  - MACHINE/DEEP LEARNING
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - multinomial logistic regression
toc: true
---

## 1. Module import

BMI 데이터를 다운받아 학습시킨 후, 자신의 키와 몸무게를 넣어 상태를 확인(예측)하는 것이 이번 목표다. 

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
from sklearn.linear_model import LogisticRegression
from sklearn.preprocessing import MinMaxScaler
```

<br><br>

## 2. Data preprocessing

### 1) 데이터 불러오기

```python
# data loading
data_df = pd.read_csv("C:/Users/student/Desktop/머신러닝/bmi/bmi.csv", skiprows=3)
```

<br>

### 2) 결측치 확인

```python
# 결측치 확인
data_df.isnull().sum(axis=0)
# 결측치 없음
```

<br>

### 3) 이상치 확인

```python
# 이상치 확인
plt.boxplot(data_df["height"])
plt.boxplot(data_df["weight"])
# 이상치 없음
```

boxplot을 통해 이상치를 확인하면 다음과 같다. 

![image](https://user-images.githubusercontent.com/58674365/95011025-bb14cc80-0668-11eb-8cf0-210e708c7cdf.png)

<br>

### 4) 정규화

**sklearn의 scaler를 사용하여 정규화를 진행**하도록 한다. 

scaler는 정규화한 정보를 가지고 있으며, fit_transform을 통해서 데이터를 불러와 알맞은 형태로 정규화를 시켜준다. 

- **from sklearn.preprocessing import MinMaxScaler**
- **scaler = MinMaxScaler( )**, **scaler.fit_transform( 정규화 진행 데이터 )**
  - scaler가 정규화 정보를 가지고, fit은 데이터를 불러오고 transform은 변환해줌

```python
# scaler 사용한 정규화
scaler = MinMaxScaler()
x_data = scaler.fit_transform(data_df[["height", "weight"]])

# scaler가 정보를 가지고 있으며 자동으로 정규화 진행해줌
scaler.data_min_ # array([120.,  35.])
scaler.data_max_ # array([200.,  80.])
```

<br>

### 5) 학습 데이터, 테스트 데이터 구분

예측을 하기에 앞서, 정확도를 측정하기 위해서 학습 데이터와 테스트 데이터로 구분해준다. 일반적으로 학습용 80%, 테스트용 20% 데이터로 사용한다.

 <br>

### 6) One Hot Encoding

**multinomial logistic regression 에서 가장 중요한 부분!** 여러 값이 있는 y 데이터를 one hot encoding 과정을 진행해준다. one hot encoding은 비록 숫자의 값을 가지더라도 오류를 줄이기 위해 T/F 즉, **1/0으로 y의 값을 나타내는 것!** <Br>

다음의 함수 중 하나를 이용한다. 

- **pandas.get.dummies( 해당 데이터셋, columns =[ 'column이름' ] )**
- tensorflow.one_hot( 해당 데이터, 범주 갯수 )
  - tensorflow의 one_hot 함수의 경우 node이기 때문에 sess.run을 통해 실행함
  - sess = tf.Session( )
    **sess.run( tensorflow.one_hot( 해당 데이터, 범주 갯수 ) )**<Br>

```python
# 80%는 학습용, 20%는 테스트용으로 사용할 것
split_num = int(data_df.shape[0]*0.8)

# x data 
train_x_data = x_data[:split_num]
test_x_data = x_data[split_num:]

# y data: one hot encoding
# sess.run(tf.one_hot(data_df.loc[:split_num,"label"],3)).shape #(16001, 3)
# df.loc의 :는 inclusive, np.array의 :는 exclusive 이기에 shape에서 차이가 날 수 있음 주의!
train_y_data = sess.run(tf.one_hot(data_df.loc[:split_num-1,"label"],3))
test_y_data = sess.run(tf.one_hot(data_df.loc[split_num:,"label"],3))
```

<br><br>

## 3. Multinomial logistic regression 과정

### 1) Placeholder

```python
X = tf.placeholder(shape=[None, 2], dtype=tf.float32)
Y = tf.placeholder(shape=[None, 3], dtype=tf.float32)
```

<br>

### 2) Weight, bias

X, Y placeholder shape을 보고 W, b의 shape을 맞춰줘야 한다.

**H = X \* W + b 가 성립할 수 있도록 shape을 맞춰준다.** 

```python
W = tf.Variable(tf.random_normal([2,3]), name='weight')
b = tf.Variable(tf.random_normal([1,3]), name='bias')
```

<br>

### 3) Hypothesis

하나의 가설이 아닌 **여러 개의 가설이 나오는 multinomial logistic regression이 보이는 가장 큰 차이점!!**

여러 가설을 tf.matmul을 사용해서 하나의 식으로 표현해 logit 값에 넣어준다. 여러 가설의 값, 확률이 들어있는 logit 값들을 tf.nn.softmax를 통해 그 모든 확률의 합이 1이 되게끔 만들어준다. softmax는 입력받은 값을 출력으로 0~1사이의 값으로 모두 정규화하며, 출력 값들의 총합은 항상 1이 된다.

- **logit = tf.matmul( X, W)** 
  - X와 W에 대해 매트릭스 곱셈 연산을 실행
- **tf.nn.softmax( logit )** 

```python
logit = tf.matmul(X,W) + b
H = tf.nn.softmax(logit)
```

<br>

### 4) Cost

가설이 변경됨에 따라, 가설을 가지고 작성하는 cost 함수의 값 또한 변경된다. 이때, 다음의 함수를 사용한다. 

- **tf.nn.softmax_cross_entropy_with_logits_v2( logits = logit, labels = Y)**

```python
cost = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits_v2(
                     logits = logit,
                     labels = Y))
```

<br>

### 5) Session, 초기화

```python
sess = tf.Session()
sess.run(tf.global_variables_initializer())
```

<br>

### 6) Train

```python
train = tf.train.GradientDescentOptimizer(learning_rate = 0.1).minimize(cost)
```

<br>

### 7) 학습

```python
for step in range(30000):
    _, val_cost = sess.run([train,cost], feed_dict = {X: train_x_data,
                                                     Y: train_y_data})
    if step % 6000 == 0:
        print(f'cost값은 : {val_cost}')
```

<br>

학습 중간마다 cost 값을 확인한 결과 다음과 같다. 

![image](https://user-images.githubusercontent.com/58674365/95011198-f4017100-0669-11eb-8a54-542d9eec7ca9.png)

<br>

### 8) 예측 정확도

data를 구성하는 과정에서 학습(train) 데이터와 테스트(test) 데이터를 구분했었다.

위에서 학습 데이터로 학습을 시켰다면, 테스트 데이터를 사용해서 어느 정도의 정확도를 가지는지 확인해야한다. <br>

- **predict = tf.argmax( H, axis=1 )**  
  - H가설 중, axis=1 즉 열에 대해서 가장 큰 값을 가지는 값을 predict로 받음
- **correct = tf.equal(predict, tf.argmax( Y, axis=1 ))** 
  - Y 중 열에 대해서 가장 큰 값을 가지는 것이 predict 값과 같은지 확인
- **accuracy = tf.reduce_mean(tf.cast(correct, dtype=tf.float32))** 
  - T/F로 나온 correct 값을 1/0 숫자로 변환하고 평균을 구함<Br>

```python
predict = tf.argmax(H, axis=1) 
correct = tf.equal(predict, tf.argmax(Y, axis=1))
accuracy = tf.reduce_mean(tf.cast(correct, dtype=tf.float32))
print(f'정확도: {sess.run(accuracy, feed_dict={X: test_x_data, Y: test_y_data})}')

# 정확도: 0.9797499775886536
```

<br><Br>

## 4. Prediction

예측을 위해 사용할 데이터 또한 정규화를 진행해줘야 한다. 이때, scaler 사용하면 위에서 사용된 정보가 저장되있다.

예측을 위한 데이터를 정규화 하여 변수에 저장하고, 이 변수를 가설에 넣어 sess.run을 실행하면 각 범주 값에 대한 확률이 나온다. 즉, array로 나오며 그 모든 값의 합은 1이다. 보기 편하게 정리하기 위해서는 가장 큰 값을 뽑는 함수, tf.argmax를 사용하면 된다. <Br>

- **scaler.transform( 에측 데이터 )** 
  - 해당 데이터를 정규화 
- **tf.equal( A, B )** 
  - A, B의 값이 동일한지 T/F 값으로 출력
- **tf.argmax( 데이터, axis = 0/1 )** 
  - 행, 열 기준으로 가장 큰 값을 출력<Br>

```python
prediction_data = scaler.transform([[168, 62]]) 
prediction_data 
#array([[0.6, 0.6]]) 

sess.run(H, feed_dict={X : prediction_data}) 
# array([[0.03564807, 0.9094091 , 0.05494288]], dtype=float32) 
# 두번째의 확률이 제일 큼: 따라서 1, 즉 평균이라고 나올 확률이 가장 높다 

# 보기 편하게 정리 
result = sess.run(tf.argmax(H,1), feed_dict={X : prediction_data}) 

if result == 0: 
    print("말랐어어어어") 
elif result == 1: 
    print("보통!") 
elif result == 2: 
    print("운동하쟛")
    
# 보통!
```

<br><br>

## 5. 다른 방식의 코드

```python
# 필요한 모듈 삽입
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
from sklearn.linear_model import LogisticRegression
from sklearn.preprocessing import MinMaxScaler


# data 설정
data_bmi = pd.read_csv("C:/Users/student/Desktop/머신러닝/bmi/bmi.csv", skiprows=3)
data_bmi.head

x_data = np.array(data_bmi[["height", "weight"]])
x_data = MinMaxScaler().fit_transform(x_data)
y_data = np.array(data_bmi["label"])
y_data = tf.one_hot(y_data,depth=3).eval(session=tf.Session())

x_data.shape #(20000, 2)
train_num = int(x_data.shape[0]*0.8)
test_num = x_data.shape[0]-train_num

train_x_data = x_data[:train_num]
test_x_data = x_data[train_num:]

train_y_data = y_data[:train_num]
test_y_data = y_data[train_num:]


## logistic regression 과정 
# palceolder
X = tf.placeholder(shape=[None, 2], dtype=tf.float32)
Y = tf.placeholder(shape=[None, 3], dtype=tf.float32)

# weight, bias
W = tf.Variable(tf.random_normal([2,3]), name='weight')
b = tf.Variable(tf.random_normal([1,3]), name='bias')

# Hypothesis
logit = tf.matmul(X,W) + b
H = tf.nn.softmax(logit)

# cost
cost = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits_v2(
                     logits = logit,
                     labels = Y))

# session, 초기화
sess = tf.Session()
sess.run(tf.global_variables_initializer())

# train
train = tf.train.GradientDescentOptimizer(learning_rate = 0.1).minimize(cost)

# 학습
for step in range(30000):
    _, val_cost = sess.run([train,cost], feed_dict = {X: train_x_data,
                                                     Y: train_y_data})
    if step % 6000 == 0:
        print(f'cost값은 : {val_cost}')
               
# 예측, 정확도
predict = tf.argmax(H, axis=1) 
correct = tf.equal(predict, tf.argmax(Y, axis=1))
accuracy = tf.reduce_mean(tf.cast(correct, dtype=tf.float32))
print(f'정확도: {sess.run(accuracy, feed_dict={X: test_x_data, Y: test_y_data})}')


# 정확도: 0.9797499775886536
```

