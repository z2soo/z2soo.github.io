---
title: "Multinomial logistic regression 예제 (tensorflow MNIST 활용)"
categories: 
  - MACHINE/DEEP LEARNING
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - multinomial logistic regression 
  - mnist
toc: true
---

## 1. MNIST 란?

**MNIST: Modified Nationnal Institute of Standards and Technology**

해당 데이터는 숫자 이미지에 대한 픽셀 데이터를 24*24 크기로 제공한다. 데이터는 tensorflow 내에서 예제로써 보다 활용하기 편하게 제공하기도 하고, kaggle에서 제공하기도 한다. tensorflow에서 제공하는 예제 데이터는 관련 함수도 활용할 수 있어서 보다 편하게 활용이 된다. 

<br><Br>

## 2. Preprocessing

### 1) Module import

```python
import numpy as np
import tensorflow as tf
import pandas as pd
import matplotlib.pyplot as plt 
from tensorflow.examples.tutorials.mnist import input_data
```

<br>

### 2) Data 불러오기

데이터를 다운받아서 data 폴더 내 mnist 폴더에 압축 파일을 생성( gz 파일 ) 한다.

단, tensorflow에서 예제로 제공되는 것이기에 one hot encoding 작업된 것을 다운받을 수 있지, 실제 다른 때에 사용되지는 않는 one_hot 함수이다. (사실 직접 one hot encoding을 해야 함)

저장된 데이터를 확인해보면, 두 개는 학습용, 두 개는 테스트용, x, y data로 분리되어 있다. 

```python
mnist = input_data.read_data_sets('C:/python_DA/data/mnist',one_hot = True)
```

<br>

### 3) X 데이터 확인

학습용 이미지 데이터 중 숫자 이미지를 픽셀로 나타낸 데이터다. 만들어진 파일을 노트패드로 열어보면, 숫자로 표현되어 있다.

x 데이터의 각 column, 픽셀 값은 0-1 숫자로 값으로 표현되어 있고, 흰색-검정색 명암(색의 진하기 정도)를 의미한다.

다른말로, 이미 scale이 되어있는 상태라고 볼 수 있다. 

```python
mnist.train.images
mnist.train.images.shape #(55000, 784)

train_x_data_df = pd.DataFrame(mnist.train.images)
train_x_data_df.to_csv('./mnist_x_data.csv', index=False)
```

<br>

### 4) Y 데이터 확인

학습용 이미지 데이터 중 실제 어떤 숫자인지 one hot encoding으로 나타낸 데이터다. 학습용 x 데이터와 마찬가지로 파일로 저장해서 확인한다.

```python
mnist.train.labels
train_y_data_df = pd.DataFrame(mnist.train.images)
train_y_data_df.to_csv('./mnist_y_data.csv', index=False)
```

<br><br>

## 3. Tensorflow machine learning: 설정

```python
# palceholder
X = tf.placeholder(shape=[None, 784], dtype=tf.float32)
Y = tf.placeholder(shape=[None, 10], dtype=tf.float32)

# weight, bias
W = tf.Variable(tf.random_normal([784, 10]), name='weight')
b = tf.Variable(tf.random_normal([10]), name='bias')

# H
logit = tf.matmul(X,W) + b
H = tf.nn.softmax(logit)

# cost
cost = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits_v2(logits=logit,
                                                                labels=Y))

# train
train = tf.train.GradientDescentOptimizer(learning_rate=0.5).minimize(cost)

# session, 초기화
sess = tf.Session()
sess.run(tf.global_variables_initializer())
```

<br><br>

## 4. Tensorflow machine learning: 학습

### 학습시, 유의!!

- 기존에 연습하던 것보다 데이터 사이즈가 커졌기 때문에 이전 처럼 몇천번씩 학습을 시킬때, epoch, batch를 사용한다.
- 위에서 데이터 프레임으로 만든 데이터가 아닌 기존의 데이터를 사용한다.
- numpy array를 사용하는 것이 헷갈리지 않기 때문에 통일해준다. 

 <Br>

- epoch : 전체 데이터를 총 몇 번 학습시킬지에 대한 정보
- batch : 전체 데이터를 몇 개의 구간으로 나눌지에 대한 정보
- **mnist.train_next_batch(배치 사이즈)**
  - tensorflow mnist 예제에서 제공하는 함수, 배치 사이즈로 자동 할당 

<br>

```python
train_epoch = 30
batch_size = 100


for step in range(train_epoch):
    num_of_iter = int(mnist.train.num_examples / batch_size)
    cost_val = 0
    
    for i in range(num_of_iter):
        batch_x, batch_y = mnist.train.next_batch(batch_size)
        _, cost_val = sess.run([train, cost],
                               feed_dict={X: batch_x,
                                          Y: batch_y})
    if step % 10 == 0:
        print(f'cost값: {cost_val}')
```

<br>

학습 중간 cost 값을 출력한 결과는 다음과 같다.

![image](https://user-images.githubusercontent.com/58674365/95011496-13010280-066c-11eb-844d-98107dee5b1b.png)

<br><br>

## 5. Tensorflow machine learning: 예측

### 정확도 측정

정확도에 따르면, 우리가 학습시킨 모델은 10 중 1은 잘못 예측한다. 즉, 90%의 정확도를 가진다.

```python
predict = tf.argmax(H,1)
correct = tf.equal(predict, tf.argmax(Y,1))
accuracy = tf.reduce_mean(tf.cast(correct, dtype=tf.float32))
print(f'정확도:{sess.run(accuracy, feed_dict = {X: mnist.test.images, Y: mnist.test.labels})}')

# 정확도:0.9187999963760376
```

<br>

### 예측

예측할 데이터가 없기 때문에 해당 데이터에서 임의로 x data를 가져온다. 이때, 예측 값과 동일한지 확인하기 위해 해당 x data 이미지가 표현하는 수(label)가 무엇인지도 뽑아준다. 주의할 점은, 2차원을 slicing 하면 1차원이 나오기 때문에 기존처럼 axis=1을 설정하면 오류가 난다. 설정을 안하면 axis=0으로 default가 잡히고 직접 axis=0으로 설정해도 된다.

다른 방법으로는 구간을 설정해서 slicing 해오는 것이 있다. 이때는 기존처럼 axis=1을 설정해주는 것이 옳다. 

**중요한 것!! tensorflow가 붙는 것은 node이기 때문에 sess.run으로 실행시켜줘야지 해당 값을 얻을 수 있다.** 

숫자가 아닌 이미지로 불러오기 위해서는 28*28 픽셀 형태로 reshape을 해줘야하며, plt.show( )함수를 사용한다. 

 <br>

- **np.random.randint( 시작 숫자, 끝 숫자, (shape 설정))** 
  - 해당범위에서 설정 크기로 난수를 불러온다. 
- **np.random.choice( 데이터, n, replace = T/F, p = 데이터와 같은 크기 )** 
  - n개 난수를 뽑되 중복(replace) 허용, 확률 설정
- **plt.imshow( 데이터, cmap = ' 색상 설정' )** 
  - 해당 데이터를 이미지로 나타냄

<br>

```python
# 랜덤으로 하나의 데이터를 추출해서 이를 가지고 prediction을 한 후 결과를 비교하시오
# 랜덤으로 몇번째 행을 난수 값으로 불러오는 것: 행번호
r = np.random.randint(0, mnist.test.num_examples)


# 난수가 의미하는 행의 실제 label값: mnist.test.labels[r] : 어떤 수인지 one hot encoding으로 표현
# 1차원
sess.run(tf.argmax(mnist.test.labels[r]))
print(f'숫자: {sess.run(tf.argmax(mnist.test.labels[r]))}')
# 2차원
sess.run(tf.argmax(mnist.test.labels[r:r+1], axis=1))
print(f'숫자: {sess.run(tf.argmax(mnist.test.labels[r:r+1], axis=1))}')


# 예측해보기
sess.run(tf.argmax(H,1),
        feed_dict = {X: mnist.test.images[r:r+1]})
print(f'예측: {sess.run(tf.argmax(H,1),feed_dict = {X: mnist.test.images[r:r+1]})}')


# 이미지로 불러오기
# 값을 가져와서 28*28 픽셀 형태로 reshape
result = mnist.test.images[r:r+1].reshape(28,28)             
# plt.imshow( ) 함수 사용
plt.imshow(result, cmap='gray')
```

<br>

불러온 이미지와 이미지에 대한 예측 값은 다음과 같다. 

![image](https://user-images.githubusercontent.com/58674365/95011517-29a75980-066c-11eb-90c4-86d7b3c57c47.png)