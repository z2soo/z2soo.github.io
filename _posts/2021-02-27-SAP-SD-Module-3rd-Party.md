---
title: "SAP SD Module 3자 거래(3rd Party)"
categories: 
  - MODULE
tags:
  - sd module
  - sap
toc: true
---

## 3rd Party Order

고객이 (우리 회사에) 구매를 요청하면 우리가 판매 오더를 생성함과 동시에 Vendor에 구매오더를 요청하여 납품하는 프로세스다. Vendor에게 받은 자재를 우리 회사에 받아서 전달해주는 것(긴납)과 Vendor에서 직접 고객에게 전달해주는 경우(직납) 2가지가 존재한다. 전자의 경우 구매, 판매에 대한 입고, 출고 처리와 동일하게 진행하면 된다. 주로 후자 경우에 속하는데, 이 경우 자재를 재고화 하지 않기 때문에 (납품X) 납품서로 대금청구하지 않고 판매 오더로 대금청구를 한다. 

추가적으로, 수출 뿐 아니라 국내 고객, 공급업체로도 3자 거래 직납 처리를 하는 경우도 있고, 직납인데도 강제로 간납처럼 프로세스를 태우는 회사도 있다. 이 경우는 주로 수불부에 포함시키기 위함이다. (직납은 수불부에 표시가 안됨) 3자 거래에 대해 MM IV 처리하고 SD Billing 처리했는데, 그 반대로 SD Billing 먼저 하고 MM IV를 처리하는 것도 가능한지 테스트해보자.  (고객에게 돈을 먼저 받아서 공급업체에 주는 경우에 해당)

- 긴납
  - 입고, 출고 있음
  - 판매오더 유형은 표준(OR)이고, 품목범주를 CBAB로 지정
  - 구매쪽 처리 필요
- 직납
  - 입고, 출고 없음
  - 판매오더 유형은 표준(OR)이고, 품목범주를 CB1~CB4로 지정
  - 구매쪽 처리 필요<br><br>

## 3rd Party Order - 직납 

3rd Party Order 중 직납에 대한 프로세스를 살펴보자. 직납의 구매 오더와 판매 오더는 다음과 같은 특성을 가진다.

- Purchase Order
  - Goods Receipt 없음
  - Invoice만 가능 : 매출원가 / AP
- Sales Order
  - Delivery Order, Goods Issue 없음
  - Billing만 가능 : AR / 매출액

<br>

### Sales Order 생성

- T-VA01

![image](https://user-images.githubusercontent.com/58674365/110053715-4079e580-7d9d-11eb-9810-12ed62c010e2.png)

품목 범주는 CB1 ~CB4로 규정해준다. <br>판매오더 생성 시, 미확인 로그, 단가, 오더유형, 품목 범주를 체크해준다. 아래는 내수 업체로 생성되었지만, 수출 고객으로 판매처를 잡도록 한다.

![image](https://user-images.githubusercontent.com/58674365/110053897-7b7c1900-7d9d-11eb-862c-9ba0e72f5660.png)

<br>

### Purchase Oder 생성

- T-ME53N : 구매요청 조회
- T-ME21N : 구매오더 생성 

3rd Party 품목으로 판매 오더를 생성하면 해당 값이 자동적으로 구매요청을 생성한다. 우선 구매요청 조회 화면으로 가서 잘 넘어왔는지 확인한다. 

![image](https://user-images.githubusercontent.com/58674365/110055152-c7c85880-7d9f-11eb-837d-f9a7b5740862.png)

해당 구매요청 번호로 구매오더를 생성한다. 

![image](https://user-images.githubusercontent.com/58674365/110055112-b3845b80-7d9f-11eb-9d4d-9bc304bc9f7c.png)

<br>

### Invoice

- T-MIRO

구매오더에 대한 입고는 따로 진행하지 않는다. 구매하는 자재를 우리가 받는 것이 아니라 우리에게 구매를 요청한 측에 전달하기 때문이다. 따라서 바로 송장 처리로 넘어간다.

![image](https://user-images.githubusercontent.com/58674365/110055763-e844e280-7da0-11eb-9277-1a03876b8033.png)

<br>

### Billing 

- T-VF01

판매오더에 대한 납품/출하/피킹은 따로 진행하지 않으며, 판매오더 번호로 대금청구를 진행한다.

![image](https://user-images.githubusercontent.com/58674365/110065587-d5d3a480-7db2-11eb-9571-945726cd23c6.png)

