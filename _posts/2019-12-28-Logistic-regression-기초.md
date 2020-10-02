---
title: "Logistic regression 기초"
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

## 1. 지도학습

1. Linear regression (선형회귀)
   : 가장 기초적인 머신러닝
   \- Simple linear regression (단순선형회귀)
   \- Multiple linear regression (다중선형회귀)
2. Logistic regression (Binary classification)
   : 알고리즘 중 가장 보편화되고 정확한 알고리즘

<br><br>

## 2. Logistic regression (Binary classification)

알고리즘 중 가장 보편화되고 정확한 알고리즘이다. 내일의 주가가 오를지 떨어질지, 이메일이 스팸인지 아닌지, 신용카드가 도난카드인지 아닌지 등을 작업할 수 있다.

- y lable이 0 또는 1, (True or False)로 표현된다.
- 머신러닝의 결과는 확률로 출력되며, 0.5 이상이면 1로 간주, 0.5 미만이면 0으로 간주한다. 

<br>

### Logistic regression를 사용하는 이유?

다음의 데이터를 가지고 linear regression으로 학습한 후, 7시간 공부 결과를 알아본 결과 0.6356531 값이 예측된다. 하지만, 만약 20 시간 공부해서 Pass 하는 data 하나가 추가된다면 그 값이 달라지게 된다. [0.45856267]
이를 방지하기 위해 logistic regression이라는 지도학습 방식이 생겨나게 되었다. 

 <br>

| x (시간) | y (시험 결과) |
| -------- | ------------- |
| 1        | Fail( 0 )     |
| 2        | Fail( 0 )     |
| 5        | Fail( 0 )     |
| 8        | Pass( 1 )     |
| 10       | Pass( 1 )     |

 <br>

```
import tensorflow as tf

x_data = [1,2,5,8,10]
y_data = [0,0,0,1,1]

x = tf.placeholder(dtype=tf.float32)
y = tf.placeholder(dtype=tf.float32)

W = tf.Variable(tf.random_normal([1]), name='weight')
b = tf.Variable(tf.random_normal([1]), name='bias')

H = W * x + b

cost = tf.reduce_mean(tf.square(H - y))

optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)
train = optimizer.minimize(cost)

sess = tf.Session()
sess.run(tf.global_variables_initializer())

for step in range(30000):
    _,cost_val = sess.run([train,cost],
                         feed_dict = {x: x_data,
                                     y: y_data})
    if step % 3000 ==0:
        print(f'cost값은:{cost_val}')
        
print(sess.run(H, feed_dict={x: 7})) 

#0.6356531
```

<br>

**데이터의 label이 0 혹은 1로 설정된 데이터는 기존의 선형회귀 방식으로는 learning 및 prediction이 불가능하다.** 이런 데이터를 학습시키고 예측하기 위해서 가설을 바꿔보자.

H = XW + b : 다중선형회귀에서 사용한 가설: 직선

**직선이 아닌 0에서 1의 값을 가지는 함수로 가설을 표현한다.**  아래 수식의 s 모형의 `시그모이드 함수(곡선 그래프)` x에 X * W + b 를 넣으면 가설이 된다.



![image](https://user-images.githubusercontent.com/58674365/94937750-f8fbde80-050a-11eb-926a-d2fbe2c82a8d.png)



- linear regression
  - H = WX+b(직선)
  - cost 함수가 최소제곱법을사용함(U자 모형)
- logistic regression
  - H = (1+e^(-WX-b))^(-1)
  - 아래와 같은 모형

 <br>

logistic에서 변경된 가설을 이용해서 cost 함수를 이용하면 local minimua를 찾고 끝나고, global minimum을 찾지 못하는 여지가 있다. 따라서cost함수도 변경해야 한다. 이는 cost = -ylog(H) - (1-y)log(1-H) 수학식을 이용해서 변경해준다. 



![image](https://user-images.githubusercontent.com/58674365/94937832-15981680-050b-11eb-9fd6-958b49a89877.png)

<br>

추가적인 설명에 대해서는 다음 블로그를 참조하도록 하면 될 것 같다.
https://wikidocs.net/22881