---
title: "Logistic regression 과정"
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

```python
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
```

<br><br>

## 2. Data 준비 

따로 파일은 없고 아래와 같은 데이터를 직접 입력하여 사용하겠다.

| 공부 시간 | 어학연수 기간 | 시험성적  |
| --------- | ------------- | --------- |
| 1         | 1             | Fail( 0 ) |
| 2         | 0             | Fail( 0 ) |
| 5         | 1             | Fail( 0 ) |
| 2         | 3             | Pass( 1 ) |
| 3         | 3             | Pass( 1 ) |
| 8         | 1             | Pass( 1 ) |
| 10        | 0             | Pass( 1 ) |

<br>

### Train data 

```python
x_data = [[1,1],
         [2,0],
         [5,1],
         [2,3],
         [3,3],
         [8,1],
         [10,0]]
y_data = [[0],[0],[0],[1],[1],[1],[1]]
```

<br><br>

## 3. Machine learning: 설정

### 1) Placeholder

```python
X = tf.placeholder(shape=[None,2],dtype=tf.float32)
Y = tf.placeholder(shape=[None,1],dtype=tf.float32)
```

<br>

### 2) Weight, bias

```python
W = tf.Variable(tf.random_normal([2,1]), name='weight')
b = tf.Variable(tf.random_normal([1]), name='bias')
```

<br>

### 3) Hypothesis

기존에 H 라고 가설을 설정했다. **시그모이드를 이용**해서 곡선의 가설을 잡을 때는 **logit으로 설정**한다. 

- **logit = tf.matmul( X, W ) +b** 
  - logistic regression 가설을 의미
  - matmul은 매트릭스 곱셈을 의
- **H = tf.sigmoid( logit )**
  - 시그모이드 함수를 이용해 가설을 설정

 <br>

| 개념    | 설명                                                         |
| ------- | ------------------------------------------------------------ |
| Sigmoid | Softmax의 특수 케이스로 클래스 갯수만 2개일 뿐 같은 연산을 수행 |
| Softmax | 한 데이터, object가 동시에 여러 클래스를 가지는 멀티레이블에서 사용 |
| Logits  | softmax, sigmoid지나기 전 단계, model의 raw output을 의미    |

<br>

```python
logit= tf.matmul(X,W) + b
H = tf.sigmoid(logit) # sigmoid를 이용해서 곡선으로표현
```

<Br>

#### 시그모이드 함수 곡선의 가설

![image](https://user-images.githubusercontent.com/58674365/94992611-38d3cc00-05c6-11eb-9a8f-ee020ff2b78e.png)

<br>

### 4) Cost function

가설이 변경되었기 때문에 cost 함수 또한 변경된다. 일반적으로 tensorflow에서 제공하는 함수를 사용한다.

두 인자, `logits` 와 `labels` 가 들어간다. log 함수를 통해 그래프를 매끈하게 펴주고, 이를 미분함으로써 (최소 제곱법 이용) 최솟값을 찾을 수 있게 된다.

- **cost = tf.reduce_mean( tf.nn.sigmoid_cross_entropy_with_logits( logits = logit, labels = Y ))**

```python
cost = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(logits = logit, labels=Y))
```

<br>

### 5) Train data

```python
optimizer = tf.train.GradientDescentOptimizer(learning_rate = 0.1 )
train = optimizer.minimize(cost)
```

<br>

### 6) Session, 초기화

```python
sess = tf.Session()
sess.run(tf.global_variables_initializer())
```

 <br><br>

## 4. Machine learning: 학습

```python
for step in range(3000):
    _, cost_val = sess.run([train, cost], feed_dict={X: x_data,
                                                     Y: y_data})
    if step % 300 == 0:
        print(f'cost값은:{cost_val}')
        
# cost값은:1.894262433052063
# cost값은:0.36012598872184753
# cost값은:0.2540614902973175
# cost값은:0.21127891540527344
# cost값은:0.1861618608236313
# cost값은:0.16863754391670227
# cost값은:0.15521176159381866
# cost값은:0.1443301886320114
# cost값은:0.13518328964710236
# cost값은:0.1273004114627838
```

<br><br>

## 5. Machine learning: 예측

```python
result = sess.run(H, feed_dict={X: [[4,1]] })
print(result)

if result > 0.5:
    print(f'시험에 통과: {result}')
else:
    print(f'시험에 떨어짐: {result}')

# [[0.18584761]]
# 시험에 떨어짐: [[0.18584761]]
```