---
title: "Logistic regression 예제 (대학원 입학 예측)"
categories: 
  - Machine/Deep learning 
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - logistic regression
toc: true
---

## 1. Module import

주어진 admission 데이터 자료를 다운받아서 머신러닝 학습을 한다. 파일은 git에 올려두었다. 학습을 마친 후, 임의의 성적을 입력하여 대학원 입학 시험에 대한 결과를 예측해본다.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import tensorflow as tf
from sklearn.preprocessing import MinMaxScaler
```

<br><br>

## 2. Data 

데이터를 불러들인다.

데이터가 어떤 관계에 있는지를 확인하는 작업도 필요하다. 따라서 데이터의 상관계수를 확인한다.

확인 결과, gre, gpa는 admit과 양의 상관관계를 보이지만, rank는 음의 상관관계를 보인다. 즉, gpa, gre가 높을수록 합격할 가능성이 높고, rank가 올라갈수록 (등수가 낮을 수록) 불합격될 확률이 낮아진다. 따라서 rank를 역순으로 돌려준다. 이를 위한 함수를 작성했다. 

- **dataframe이름.corr( )** 
  - 해당 데이터 프레임 (전체)의 상관관계를 보여줌
- lambda x 
  - x에 대한 간단한 함수식
- **dataframe이름.apply( lambda x : 일회성 함수 설정 )** 
  - 해당 데이터프레임에 함수 적용

```python
df = pd.read_csv("C:/Users/student/Desktop/머신러닝/admission.csv")
display(df)

print(f'상관계수:{df.corr()}')

def change_rank(x):
    if x == 4:
        return 1
    elif x == 3:
        return 2
    elif x == 2:
        return 3
    else:
        return 4
        
df["rank"] = df["rank"].apply(lambda x: change_rank(x))
display(df)
```

<br>

초기 display(df) 결과는 다음과 같다.

![image](https://user-images.githubusercontent.com/58674365/94992833-8e5ca880-05c7-11eb-9ba5-39d8e07e91fa.png)



상관계수는 다음과 같다.

![image](https://user-images.githubusercontent.com/58674365/94992849-ae8c6780-05c7-11eb-981e-ad16e949e264.png)



lambda 적용 후 display(df) 결과는 다음과 같다.

 ![image](https://user-images.githubusercontent.com/58674365/94992864-cbc13600-05c7-11eb-9586-b0948201998e.png)

<br><br>

## 3. Data preprocessing

### 1) 결측치

- **dataframe이름.dropna( how = 'any/all', inplace = True/False )**

```python
df = df.dropna(how="any", inplace=False)
```

<br>

### 2) 이상치

```python
'''
def process_outlier(tmp_df, tmp):
q1, q3 = np.percentile(tmp, [25,75])
iqr = q3-q1
lower = q1 - iqr*1.5
upper = q3 + iqr*1.5
upper_mask = tmp >upper
lower_mask = tmp <lower

이렇게 함수로 만들어서 쓰면 굳이 여러번 작성하지 않고 다음 식으로 가능
df = process_outlier(df,df["gre"])
df = process_outlier(df,df["gpa"])
'''


plt.boxplot(df["gre"]) # 이상치 (하)
gre_q1, gre_q3 = np.percentile(df['gre'],[25,75])
gre_lower = gre_q1 - (gre_q3-gre_q1) *1.5
gre_mask = df['gre'] < gre_lower
df = df.loc[~gre_mask]

plt.boxplot(df["gpa"]) # 이상치 (하)
gpa_q1, gpa_q3 = np.percentile(df['gpa'],[25,75])
gpa_lower = gpa_q1 - (gpa_q3-gpa_q1) *1.5
gpa_mask = df['gpa'] < gpa_lower
df = df.loc[~gpa_mask]
display(df)

# plt.boxplot(df["rank"]) # 없음
# plt.boxplot(df["admit"]) # 없음
```

<br>

이상치를 boxplot으로 확인한 결과는 다음과 같다.

![image](https://user-images.githubusercontent.com/58674365/94992887-16db4900-05c8-11eb-87c8-30cddee539a0.png)



이상치 제거 후 display(df)한 결과는 다음과 같다.

![image](https://user-images.githubusercontent.com/58674365/94992891-1b9ffd00-05c8-11eb-9f2c-684b2fee6342.png)

<br>

### 3) 표준화

```python
# training data set 표준화
x_data = df[["gre","gpa","rank"]]
x_data = MinMaxScaler().fit_transform(x_data)
y_data = df[["admit"]]
y_data = MinMaxScaler().fit_transform(y_data)
# y_data = df[["admit"]].values.reshape(-1,1)
```

<br><br>

## 3. Machine learning: 설정

```python
# placeholder
X = tf.placeholder(shape=[None, 3], dtype=tf.float32)
Y = tf.placeholder(shape=[None, 1], dtype=tf.float32)

# Weight, bias
W = tf.Variable(tf.random_normal([3,1]), name="weight")
b = tf.Variable(tf.random_normal([1]), name="bias")

# Hypothesis
logit = tf.matmul(X,W) + b
H = tf.sigmoid(logit)

# cost
cost = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(logits = logit, labels=Y))

# train
optimizer = tf.train.GradientDescentOptimizer(learning_rate = 0.01)
train = optimizer.minimize(cost)

# sess, 초기화
sess = tf.Session()
sess.run(tf.global_variables_initializer())
```

<br><br>

## 4. Machine learning: 학습

```python
for step in range(30000):
    _, cost_val = sess.run([train, cost], feed_dict = {X: x_data,
                                                       Y: y_data})
    if step % 10000 == 0:
        print(f'cost값은:{cost_val}')
 
# cost값은:0.6924879550933838
# cost값은:0.5829955339431763
# cost값은:0.5774873495101929
```

<br><br>

## 5. Machine learning: 예측

Logistic regression 학습부터는 반드시 정확도를 측정할 것!

여기서는 test 데이터가 없기 때문에 train 데이터를 사용하고, 따로 주어지지 않더라도 8:2의 비율로 train:test 데이터를 나눠 사용한다. 

최소 95% 이상, 일반적으로는 98% 이상은 나와야 실제 적용 가능하다. 70%가 넘지 않는다면 사용할 수 없는 모델이다.

- **tf.cast( 조건, dtype = tf.float32 )**
  - cast 함수는 해당되는 조건에 따른 값 뒤의 값으로 바꿔준다.
- **tf.equal( X, Y )**
  -  X와 Y가 같은지 True/False로 출력
- **tf.reduce_mean( )** 
  - 평균 출력

아래의 식을 해석하자면, 가설의 값이 0.5 보다 크고 작음에 따라 T/F로 나오는 값을 tf.float32 즉, 0, 1 값으로 cast 바꿔준다. 그리고 이 값과 Y의 값(실제 값)이 같은지를 T/F로 나타낸다. 이를 다시 0, 1 값으로 바꾸고, 그 값들의 평균을 내면 정확도가 된다. 

```python
# predict한 결과를 알아온다

predict = tf.cast( H>0.5, dtype=tf.float32 ) 
correct= tf.equal(predict, Y)
# correct = tf.cast( correct, dtype=tf.float32 ) 
accuracy = tf.reduce_mean(tf.cast( correct, dtype=tf.float32 ))
print(f'정확도:{sess.run(accuracy, feed_dict={X:x_data,Y:y_data})}')

# 정확도:0.701265811920166
```

<br><br>

## 6. Etc

위의 과정을 한번에 실행할 수 있는 모듈로 수행해보자.

```python
from sklearn.linear_model import LogisticRegression
```

 

 