---
title: "ABAP Toolbar 버튼 및 이벤트 추가"
categories: 
  - ABAP
tags:
  - abap
  - sap
toc: true

---

## 1. 툴바 버튼 추가 

LCL_EVENT_RECEIVER CLASS에서 HANDLE_TOOLBAR을 추가해준다. (Definition, Implementation)

- CLASS > LCL_EVENT_RECEIVER > HANDLE_TOOLBAR 추가

![image](https://user-images.githubusercontent.com/58674365/98483636-6c50e880-224d-11eb-8205-095e0aaaefb2.png)<br>

Implementation에서 다음과 같은 perform, HANDLE_TOOLBAR 을 작성해준다. 

- Perform > HANDLE_TOOLBAR

![image](https://user-images.githubusercontent.com/58674365/98483637-6eb34280-224d-11eb-80b3-8baee28aa1ea.png)<br>

### Definition

![image](https://user-images.githubusercontent.com/58674365/98483639-707d0600-224d-11eb-8fd6-0624b6447466.png)<br>

### Implementation

![image](https://user-images.githubusercontent.com/58674365/98483642-7246c980-224d-11eb-9929-41be970d568e.png)<br>

### Event 선언

![image](https://user-images.githubusercontent.com/58674365/98483645-770b7d80-224d-11eb-8c24-45b4b114b2c1.png)<br>

### 결과 화면

사원 등록을 누르면 데이터 추가가 가능하다. 

![image](https://user-images.githubusercontent.com/58674365/98483648-7a066e00-224d-11eb-9e69-3d8a893a91ae.png)<br>
![image](https://user-images.githubusercontent.com/58674365/98483649-7bd03180-224d-11eb-8eda-5d0456ada858.png)

<br><br>

## 2. 사원 등록 상황 설정

사원등록시 사번이 없으면 새로 생성, 있으면 내부 데이터 가져와서 변경으로 하는 등 기타 설정을 해준다. 

- SCREEN 0100 > USE_COMMAND_0100 > SAVE_DATA Perform 생성<br>

![image](https://user-images.githubusercontent.com/58674365/98483651-7ecb2200-224d-11eb-93d0-51f2345cbd17.png)<br>

변경 데이터가 없는 경우 다음과 같은 결과가 나온다. 

![image](https://user-images.githubusercontent.com/58674365/98483654-8094e580-224d-11eb-9077-aebd1f911976.png)