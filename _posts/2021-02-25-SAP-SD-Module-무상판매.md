---
title: "SAP SD Module 무상판매"
categories: 
  - MODULE
tags:
  - sd module
  - sap
toc: true
---

> SAP Sales & Distribution Process 에서 무상 판매 처리에 대한 두 가지 케이스를 살펴보도록 한다. 판매오더를 생성하는 `T-VA01` 에서 무상판매 오더를 생성하는 방법은 다음 두 가지가 있다. 
>
> 1. 오더 유형을 OR로 하여 생성 
> 2. 오더 유형을 FD로 하여 생성
>
> 처음의 경우 100% 매출 할인으로 billing 처리하면 회계 전표에 AR이 없고 매출 할인으로 기록된다. 반면, 후자의 경우 판가 없이 처리되고 billing 처리하면 수익성 분석만 생성된다. (매출원가를 수익성 분석에 반영하기 위함)



## 1. 무상판매 - OR

`T-VA01` 에서 오더 유형을 OR로 해서 무상판매 오더를 생성하는 경우 100% 매출할인으로 처리된다. 따라서 대금청구에서 회계 전표에 AR이 없고 매출할인으로 기록된다. 해당 무상판매 생성시 다음에 유의한다.

- 품목 범주를 CBXN으로 설정

![image](https://user-images.githubusercontent.com/58674365/109423621-87757d00-7a23-11eb-87c6-cfa3249ad8f4.png)



Sales order를 생성할 때, 값의 변수를 `CBXN` 으로 설정하면, 자동으로 단가를 0으로 인식한다. 이후 납품, 출고, 대금청구 과정은 동일하다. 

![image](https://user-images.githubusercontent.com/58674365/108962209-c6ba6b80-76bb-11eb-8ef5-a21ccaad05d4.png)

![image](https://user-images.githubusercontent.com/58674365/108962225-cc17b600-76bb-11eb-897a-95d099872c15.png)

<br><br>

## 2. 무상판매 - FD

`T-VA01` 에서 오더 유형을 FD로 해서 무상판매 오더를 생성하는 경우 따로 품목범주를 설정할 필요는 없다. 판가 없이 처리되며, 대금청구시 수익성 분석만 생성한다. (매출원가를 수익성 분석에 반영하기 위함) 해당 무상판매 생성시 다음에 유의한다.

- 오더사유 작성

![image](https://user-images.githubusercontent.com/58674365/109423727-ff43a780-7a23-11eb-9dca-e540cdb6d3ff.png)



Sales order 생성시, 따로 품목범주를 설정하지 않아도 판가 없이 생성된다. 납품, 출고, 대금처리 과정은 동일하다.

![image](https://user-images.githubusercontent.com/58674365/109423776-226e5700-7a24-11eb-99b7-2fa1009cd900.png)

