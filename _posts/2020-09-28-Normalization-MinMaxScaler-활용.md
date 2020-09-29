---
title: "Normalization: MinMaxScaler 활용"
categories: 
  - Machine/Deep learning 
tags:
  - python
  - machine learning
  - deep learning
  - data analysis
  - normalization
  - minmaxscaler
toc: true
---

## 1. Normalization(정규화)

Data pre processing(데이터 전처리 과정) 중 결측값, 이상치 처리를 비롯하여 실행하는 과정이다. 

Normalization(졍규화) 또는 Standardization(표준화) 라고 한다. 이는, 0-1 사이로 전체적인 scale을 맞춰준다. 표준화를 하지 않아도 머신러닝이 가능하지만, 값의 범위가 커짐에 따라서 학습이 정상적으로 이루어지지 않는 경우가 있다. (범위가 더 큰 변수의 영향을 더 받게 된다.) 이에 데이터를 표준화 또는 정규화를 이용해서 값의 범위를 조절하여 사용한다. 즉, 데이터의 값이 0-1 범위내에 존재하도록 비율적으로 축소시키는 작업을 의미한다.



![image](https://user-images.githubusercontent.com/58674365/94580713-2fd8b700-02b5-11eb-84a4-e65aea2f9ea0.png)





## 2. MinMaxScaler

정규화를 일일이 진행한다면 복잡스럽기 때문에, sklearn에서 제공하는 MinMaxScaler module을 사용한다.



### 1) Module import

- from sklearn.preprocessing import MinMaxScaler

``` python
from sklearn.preprocessing import MinMaxSxaler	
```



### 2) Data preprocessing

- 표준화를 제외한 결측치 처리, 이상치 처리
- 이전 포스팅 참조



### 3) Training data set: MinMaxScaler 사용

- x_data=df이름[["변수col 이름1", "변수col 이름2", "변수 col 이름3"]]
- x_data=MinMaxScaler().fit_transform( x_data )

```python
x_data = df[["Solar.R", "Wind", "Temp"]]
x_data = MinMaxScalar().fit_transform( x_data )
print(x_data)

y_data = df[["Ozone"]]
y_data = MinMaxScaler().fit_transform( y_data )
print(y_data)
```



![image](https://user-images.githubusercontent.com/58674365/94581535-2dc32800-02b6-11eb-8211-94e56690cfbe.png)



![image](https://user-images.githubusercontent.com/58674365/94581600-39aeea00-02b6-11eb-892f-0efd32034ae7.png)



### 4) Etc

이후의 모든 과정은 동일하게 진행된다.