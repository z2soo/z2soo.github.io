---
title: "Simple linear regression 과정"
categories: 
  - MACHINE/DEEP LEARNING
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - simple linear regression
  - tensorflow
toc: true
---

## 1. Module import

google의 tensorflow를 사용한 머신러닝을 위한 모듈을 import 해준다.

```python
import tensorflow as tf
```

<br><br>

## 2. Training data 설정

값을 직접 정해줘도 되지만, placeholder parameter를 사용하면 실행시킬 때 값을 받도록 node만 잡아줄 수 있다.

- data set 이름 = **tf.placeholder( dtype = 타입설정 )**

```
x_data = [1,2,3]
y_data = [3,5,7]

x = tf.placeholder(dtype=tf.float32)
y = tf.placeholder(dtype=tf.float32)
```

<br><br>

## 3. Weight, bias 설정

가설 설정을 위해 필요한 Weight(가중치)와 bias의 node를 설정해준다. 보다 적절한 W, b의 값을 찾는 것이 모든 것의 최종 목적!!!임을 잊지 말자.

W는 학습을 통해 계속해서 값이 변하고, 직접 입력하는 값이 아니기 때문에 변수 node, variable로 설정한다. 
**초기 값이 0-1 사이 랜덤으로 설정되도록 random_normal 함수를 사용한다.**
1개의 W 값을 필요로 하기 때문에, shape 형태에 숫자로 표현하고, 뒤의 name은 프로그램 내에서 사용할 이름을 설정한다. 

- 가설: **H(x) = Wx + b**
- **tf.Variable( tf.random_normal([갯수]), name = '이름' )**

``` python
W = tf.Variable(tf.random_normal([1]), name = 'weight')
b = tf.Variable(tf.random_normal([1]), name = 'bias')	
```

<br><br>

## 4. Hypothesis(가설) 설정

- 가설: H = W*x + b

지금까지 생성된 node와 가설 H 노드가 활용하는 node를 이미지로 보이면 다음과 같다.

![image](https://user-images.githubusercontent.com/58674365/94804065-32f2b500-0425-11eb-87b5-3bae4ee5d223.png)

<br><br>

## 5. Cost function, Loss function(비용함수) 설정

비용함수는 '최소제곱법: 가설에서 y 값을 빼고 그 값을 제곱하여 평균을 구하는 것'으로 정의된다.

- 괄호 안의 평균을 구하는 함수: **tf.reduce_mean()**
- 괄호 안의 값을 제곱하는 함수: **tf.square()**

```python
cost = tf.reduce_mean(tf.square(H - y))
```

<br>

cost 함수 node는 H와 y의 node를 사용한다. 이미지로 보면 다음과 같다.

![image](https://user-images.githubusercontent.com/58674365/94804324-967ce280-0425-11eb-9d1b-42418979f137.png)

<br><br>

## 6. Train node 설정

optimizer 알고리즘: 경사 하강법 알고리즘을 사용하여 cost를 minimize 하도록 설정한다.
optimizer 알고리즘은 일종의 미분과 같은 실행은 한다. 

- 알고리즘 노드: **optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)**
- 학습 실행 노드: **train = optimizer.minimize(cost)**

```python
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)
train = optimizer.minimize(cost)
```

<br>

optimizer와 train의 node 생성이 완료되었다. train 실행 시 모든 node가 실행된다. 

![image](https://user-images.githubusercontent.com/58674365/94804580-025f4b00-0426-11eb-90ea-53ed68ab222e.png)

<br><br>

## 7. Runner(session) 설정 및 변수 초기화

node를 실행시키기 위해서는 runner, 즉 session이 필요하다. 

또한, **변수 variable을 사용하면 학습 시작 전에 반드시 초기화!!**를 해야하기에 다음의 코드를 꼭 사용한다. 

- node 실행 runner: **sess = tf.Session( )**
- 변수 초기화 코드: **sess.run( tf.global_variables_initializer( ) )** 

```python
sess = tf.Session() 
sess.run(tf.global_variables_initializer()) 
```

<br><br>

## 8. Learning 작업 (학습)

기계학습, 머신러닝을 통해 최적의 W와 b를 찾아서 최적의 가설을 완성시키는 과정이 바로 우리의 목표이다.

이를 위해 train을 한번만 수행시키는 것이 아니다. 한 번만 수행한다면 cost값은 한 번만 줄어든다. 반복 실행 필요!

- for step in range(학습 횟수)
- 실행한 node값을 저장할 변수 = **sess.run( [실행 원하는 node 이름], feed_dict = { x: 데이터, y: 데이터 })**

```python
for step in range(3000): 
    _, W_val, cost_val, b_val = sess.run([train,W,cost,b], 
                                        feed_dict={ 
                                            x : x_data, 
                                            y : y_data   
                                        }) 
    if step % 300 == 0: 
        print(f'W값: {W_val}, cost값:{cost_val}, bias값:{b_val}') 
```

<br>

위의 코드를 실행한, 3000번 돌면서 train을 실행시키는 중간마다 출력한 값을 확인해보면 다음과 같다.

![image](https://user-images.githubusercontent.com/58674365/94804965-9b8e6180-0426-11eb-9ff0-f5914f03c69c.png)



train은 값이 없기에 값이 안나와서 _로 wraping 시키고, 나머지는w_val, cost_val, b_val에 입력시켰다. train만 실행시키면 되지만 굳이 W, cost,b를 넣은 이유는 값을 출력하면서 확인하기 위함이었다. 결과적으로 W와 b의 값을 찾아내서 H 가설을 만들었고, 이제 가설 실행을 통해 예측이 가능해졌다.

<br><br>

##  9, Prediction

x의 값이 200인 경우를 예측한다면?

```python
print(sess.run(H,feed_dict={ x: 200 }))
```
