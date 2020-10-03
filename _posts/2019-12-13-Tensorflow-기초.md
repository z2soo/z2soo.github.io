---
title: "Tensorflow 기초"
categories: 
  - MACHINE/DEEP LEARNING
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - tensorflow
toc: true
---

## 1. Tensorflow 기본 개념

Google의 Tensorfloe 프로그램을 통해서 머신러닝을 해보도록 한다. 다른 package, module에 비해 살짝 특이한 형태의 library를 가진다.

<br>

### 3가지 구성요소

- Node: 수학적 연산을 담당, 데이터의 입출력을 담당
- Edge: 동적 데이터를 노드로 실어나르는 역할을 담당
- Tensor: 다차원 배열 형태의 동적 데이터

<br><br>

## 2. Tensorflow 설치

- Anaconda prompt 관리자 권한으로 실행 > 가상환경 진입 > **pip install tensorflow==1.5.0**

```python
pip install tensorflow==1.5.0
# 그냥 설치하면 많이 바뀐 최신 버전이 설치되기 떄문에 버전을 설정해줌

import tensorflow as tf
# 설치 후 puthon에 import해서 사용
```

<br><br>

## 3. Tensorflow 사용

[코드입력]

```python
import tensorflow as tf

# 그래프를 그려주는 tensorflow
node1 = tf.constant("Hello World!!")

# 그래프를 실행히키기 위해서는 runner 필요
# session이라고 불리는 runner 생성
sess = tf.Session()

# run 함수를 이용해 해당 node를 실행한다.

print(sess.run(node1))
# print 했을 때 앞에 붙는 b는 bite string의 약자이다.
# 일반적인 문자열 처리를 하려면 decode 해준다.

print(sess.run(node1).decode())
```



[결과 출력]

```python
b'Hello World!!'
Hello World!!
```

<br>

[코드 입력]
Q. 2개의 값을 더하는 tensorflow graph를 생성, 실행하여 값을 구하시오.

```python
import tensorflow as tf

# 수치 연산의 기본은 실수
# tensorflow에서 dtype 중요!

node1 = tf.constant(10, dtype=tf.float32)
node2 = tf.constant(20, dtype=tf.float32)
node3 = node1 + node2

sess = tf.Session()
print(sess.run(node1))
print(sess.run([node1, node2]))
print(sess.run(node3))
```



[결과 출력]

```python
10.0
[10.0, 20.0]
30.0
```

<br>

[코드 입력]

```python
# 위의 node는 실행 시점에 값이 정해져 있다.
# constant(상수)이기 때문
# 만약 node1,2에 대한 값을 직접 입력해서 실행하고 싶다면?

import tensorflow as tf
node4 = tf.placeholder(dtype=tf.float32)
node5 = tf.placeholder(dtype=tf.float32)
node6 = node4 + node5
sess = tf.Session()

# node1,2에 값을 feed하되 dict 사용
result = sess.run(node6,
				  feed_dict={node4: input(),
                             node5: input()})
print(f'덧셈결과: {result}')
```



[결과 출력]

```python
10
40
덧셈결과: 50.0
```