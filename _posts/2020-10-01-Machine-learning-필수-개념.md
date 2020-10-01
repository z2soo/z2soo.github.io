---
title: "Machine Learning 필수 개념"
categories: 
  - Machine/Deep learning 
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - tensorflow
toc: true
---

## 1. Learning rate

Learning rate를 사용하는 것은 cost 값을 떨어뜨리기 위함이다. 정해진 값은 없으며, 임의로 설정한다. 

만약 learning rate가 너무 크면 over shooting 현상이 발생하고, 
learning rate가 너무 작으면 수렴하는 속도가 너무 느리고 local minimum 현상에 빠질 수 있다. 

cost 함수에서 최소가 되는 때의 W 값을 아는 것이 목적이므로, 현재 W1 - (learnign rate \* 기울기 ) 과정으로 수행하면서 적합한 W를 찾아간다. 그런데 learnign rate * 기울기 값이 큰 경우 반대편으로 뛰게 되고 이른 over shooting이라 한다. 적합한 learning rate는 cost 값을 기준으로 커스터마이징을 해야 한다.



![image](https://user-images.githubusercontent.com/58674365/94802169-215bde00-0422-11eb-874a-5d991e5e2c69.png)



### Overshooting 현상

![image](https://user-images.githubusercontent.com/58674365/94802190-2de03680-0422-11eb-9eda-2b4edad1fb06.png)





## 2. Data preprocessing

feature engineering을 포함해서 각 데이터의 범주와 크기를 살펴봐야 한다. 
데이터 전처리 과정에는 결측값, 이상치 처리를 포함하여 정규화, 표준화 등의 과정이 모두 포함된다. 

- 정규화 : (normalization) : x의 최대, 최소값을 가지고 scale 한다 해서 Min Max Scale 이라고도 함
- 표준화 : (standardlization) : 분산과 표준 편차를 이용해서 값을 sclae 하는 방식





## 3. Over fitting (과적합 현상)

모델을 만들어서 학습을 하는데, 이것이 학습 데이터에 너무 잘 들어맞는 모델이 형성되는 것을 과적합 현상이라고 부른다. 모델이 딱 맞다면 좋다고 생각할 수 있으나 아님에 유의한다. 


모델이 학습데이터에 꼭 맞춰진다면 특수한 현상에 대해 맞춤된 모델이 된 것이라는 의미이기 때문이다.

원래 목적은 예측을 하는 것, 즉 실제 데이터를 적용할 때 잘 적용되어야 하지만, 특수 현상에 맞춤이 되어있다면 결과값 예측이 잘 안되는 경우라고 볼 수 있다. 이러한 과적합 현상을 피하기 위한 방법은 다음과 같다.  

- 많은 training data set이 있어야 하고, 데이터가 적을수록 과적합 가능성 높아진다.
- feature(column)의 갯수를 가능한 줄여야 한다.





## 4. 학습과정

일반적으로 training data set의 크기는 굉장히 크기 때문에, 1 epoch을 수행하는 시간이 오래 걸린다.
따라서 epoch을 많이 수행시킬 수 없고, batch 처리를 통해 학습 진행을 설정해야 한다.





## 5. 정확도

일반적으로 raw data set을 우리가 얻게 되면, 7:3 혹은 8:2 비율로 traininf data set, test data set으로 분하여 사용하도록 한다. 평가가 이루어지지 않으면 우리가 만든 모델이 정확한지 알 길이 없기 때문이며, n fold cross validation, 교차 검증의 과정을 사용하기도 한다.