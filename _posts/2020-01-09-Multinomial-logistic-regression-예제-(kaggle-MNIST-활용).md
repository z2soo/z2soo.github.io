---
title: "Multinomial logistic regression 예제 (kaggle MNIST 활용)"
categories: 
  - MACHINE/DEEP LEARNING
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - multinomial logistic regression
  - mnist
  - kaggle
toc: true
---

## 1. MNIST: kaggle

kaggle 사이트에서 MNIST를 검색하면 나오는 digit recognizer 를 통해 데이터 파일을 다운받아 사용한다.

multinomial logistic regression tensorflow 활용 MNIST 예제에서 사용한 것과 같은 MNIST 데이터 파일이지만, 전처리부터 one hot encoding, batch 설정까지 직접 해야한다. 해당 데이터를 사용하여 학습 진행 후, 예측 결과는 kaggle 사이트에 올려보도록 한다. 현저히 낮은 정확도이지만, 이후의 딥러닝 과정을 통해서 지속적으로 정확도를 올려 업데이트 하도록 한다. 

<br><br>

## 2. Preprocessing

### 1) 데이터 불러오기

해당 데이터는 x data, y data 구분되어 있지 않기 때문에 이를 구분해주고, numpy array로 변환해준다. 

```python
# 데이터 불러오기
train_data = pd.read_csv('C:/Users/student/Desktop/머신러닝/data/digit-recognizer/train.csv')
test_data = np.array(pd.read_csv('C:/Users/student/Desktop/머신러닝/data/digit-recognizer/test.csv'))

# x data, y data 구분
x_data = np.array(train_data.drop(['label'], axis='columns', inplace=False))
y_data = np.array(train_data['label'])
```

<br>

### 2) One hot encoding

y data에 대해 one hot encoding 작업을 해준다. 

- **pandas.get_dummies( 해당 데이터 ).values**
- **tensorflow.one_hot( 해당 데이터, 범주 갯수 )**

```python
y_data = pd.get_dummies(train_data.label).values
y_data.shape

# (42000, 10)
```

<br>

### 3) 정규화

```python
scaler=MinMaxScaler()
x_data = scaler.fit_transform(x_data)
```

<br>

### 4) Train data, Test data 분리

학습 이후, 정확도를 측정하기 위해 8:2의 비율로 학습 데이터, 테스트 데이터를 구분해준다.

헷갈리지 않게 조심할 점은 파일 자체 이름이 test인 파일은 예측해서 제출해야하는 데이터 파일이라는 점!

```python
split_num = int(x_data.shape[0]*0.8)
train_x_data = x_data[:split_num]
test_x_data = x_data[split_num:]
train_y_data = y_data[:split_num]
test_y_data = y_data[split_num:]
```

 <br>

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

```python
train_epoch = 500
batch_size = 300


for step in range(train_epoch):
    num_of_iter = int(x_data.shape[0] / batch_size)
    
    for i in range(num_of_iter):
        batch_x = x_data[i*batch_size:(i+1)*batch_size]
        batch_y = y_data[i*batch_size:(i+1)*batch_size]
        
        
        
        _, cost_val = sess.run([train, cost],
                               feed_dict={X: batch_x,
                                          Y: batch_y})
    if step % 100 == 0:
        print(f'cost값: {cost_val}')

# cost값: 1.212005376815796
# cost값: 0.2462136298418045
# cost값: 0.20747937262058258
# cost값: 0.1955113410949707
# cost값: 0.18978384137153625
```

<br><br>

## 5. Tensorflow machine learning: 예측

### 정확도

```python
predict = tf.argmax(H,1)
correct = tf.equal(predict, tf.argmax(Y,1))
accuracy = tf.reduce_mean(tf.cast(correct, dtype=tf.float32))
print(f'정확도:{sess.run(accuracy, feed_dict = {X: test_x_data, Y: test_y_data})}')

# 정확도:0.9469047784805298
```

<br>

### 예측

테스트 파일을 가지고 예측을 진행하고, submission_example 파일 형태에 맞춰서 저장하고 kaggle에 제출한다.

- DataFrame( { "생성 열이름1" : [ 값1 ], "생성 열이름2" : [ 값2 ] .. } ) 
  - 여러 열, 값의 data frame 생성
- DataFrame( columns=( "열이름1", "열이름2", "열이름3" )) 
  - 열만 있는 data frame 생성
- dataframe이름[ "열이름" ] = 값 
  - 새로운 column 추가

```
# 예측
result = sess.run(tf.argmax(H,1),
                  feed_dict={X: test_data})
print(result)
# array([2, 0, 9, ..., 3, 9, 2], dtype=int64)


# 제출 파일로 저장
submission = pd.read_csv('C:/Users/student/Desktop/머신러닝/data/digit-recognizer/sample_submission.csv')
final_result= pd.DataFrame({'Label':result}) 
submission['Label'] = final_result['Label']
submission.to_csv('C:/Users/student/Desktop/머신러닝/data/submission.csv', index=False)
```

<br><br>

## 6. 이미지 예측

MNIST 데이터는 숫자 이미지를 픽셀 값으로 변환한 데이터였다. 그렇다면 직접 이미지를 업로드해서 이를 픽셀 값으로 변환하여 위와 같은 과정을 진행해보도록 하자. 

이미지를 넣었을 때, 어떤 숫자인지 예측을 통해 알려주도록 만들어보자.

<br>

### cv2 사용

- **from PIL import Image**
- **Image.open( '주소 및 파일명' )**

```python
from PIL import Image
import cv2


img = Image.open('C:/Users/student/Desktop/머신러닝/data/숫자5.jpg')
display(img)

img_np = np.array(img)

img_np.transpose(2, 0, 1)
r = img_np.transpose(2, 0, 1)[0]
g = img_np.transpose(2, 0, 1)[1]
b = img_np.transpose(2, 0, 1)[2]
img_gray= r*0.299 + g*0.587 + b*0.114

img_data = scaler.fit_transform(img_gray)
print(img_data)


img_data = cv2.resize(img_data,(784, 1))


sess.run(tf.argmax(H,1),
                  feed_dict={X:img_data})
```

사용한 이미지와 그에 대한 예측은 다음과 같다. 

![image](https://user-images.githubusercontent.com/58674365/95011559-8571e280-066c-11eb-9e5b-a7d3ab2f20ac.png)

<br>

### pillow 사용

- numpy array 이름.resize( shape=( ) )
  - 기존 array는 변화하지 않고 새로 assign 해줘야 함
- numpy array 이름.reshape( shape=( ) ) 
  - 기존 array 자체가 변함

```python
import PIL.Image as pilimg
import numpy as np
import matplotlib.pyplot as plt


img2 = pilimg.open('C:/Users/student/Desktop/머신러닝/data/숫자5.jpg').convert('L')
display(img2)

resize_img2 = img2.resize((28,28))


# Fetch image pixel data to numpy array
pix = np.array(resize_img2)
plt.imshow(pix)

pix = 255-pix
pix = pix.reshape(1,-1)

pix = scaler.fit_transform(pix)
sess.run(tf.argmax(H,1),
                  feed_dict={X:pix})
```

사용한 이미지와 그에 대한 예측은 다음과 같다. 

![image](https://user-images.githubusercontent.com/58674365/95011565-902c7780-066c-11eb-900b-48f30bc18ee8.png)