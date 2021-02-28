---
title: "SAP SD Module 기준정보 및 판매오더 생성"
categories: 
  - MODULE
tags:
  - sd module
  - sap
toc: true
---

## 1. SD 기준정보

> Sales & Distribution은 한 마디로 '판매'다. 

- **자재 마스터**<br>무엇을 팔 것인가?<br>판매할 자재 마스터에서 영업 View를 생성해줘야 한다.
- **고객 마스터**<br>누구에게 팔 것인가?<br>영업조직, 유통경로, 제품군 별로 생성해야 한다.

- **조건 유형 마스터**<br>T-VK11에서 생성 가능하다<BR>영업조직, 고객, 유통경로 별로 등록되어야 한다. 같은 제품이여도 다른 가격에 판매될 수 있기 때문이다. 이는 판매 가격과 세율 등을 포함한다.
- **고객자재정보 (레코드)**<br>고객이랑 고객이 사용하는 자재번호를 mapping한 것이며, 오더에서 고객의 자재번호를 입력하면 우리가 가진 자재번호 정보로 자동 전환된다.<br>

<BR><BR>

## 2. SD 조직구조

- 영업조직 
  - 유통채널 (내수, 수출, 로컬수출)
  - 제품군
- 플랜트 
- 출하지점<br>플랜트 하위로 assign 되어 관리된다.

로컬 수출은 구매자가 그 제품을 받아서 제품을 만들고 향후 수출하는 경우를 의미한다. 이 경우 세금이 따로 붙지 않는다. 만약 수출여부가 불확실한 경우 내수로 판매한 후, 이후에 수출한 자재에 대한 정보를 받아 내수를 취소하고 다시 로컬 수출로 잡는다. 로컬 수출로 한 경우 billing에서 세금이 0으로 찍히는지 꼭 확인하자. 반대로 내수 계정인데 세금이 0이라면 잘못된 데이터임을 알자. 

<br>

### 영업영역

`영업조직 - 유통채널 - 제품군` 의 조합을 영업영역이라 한다. <br>
즉, 그 갯수는 영업조직 수 * 유통채널 수 * 제품군 수가 된다. 만약 영업영역의 수가 많아지면 마스터 관리가 힘들어지기 때문에 그 수를 조절한다. 일반적으로 유통채널은 내수, 수출, 로컬수출 3가지로 두고 제품군은 1가지(00, 공통)로 둔다.   

<br><br>

## 3. 자재마스터 설정

자재를 판매하기 위해서는 자재마스터에 영업 View가 생성되어 있어야 한다. <br>PP 모듈 교육시 생성한 자재인 F-161(BOM 존재)을 사용하도록 한다. T-MM02에서 영업 View가 존재하는지 확인한 후 다음과 같이 영업에 대한 설정을 해준다. 이 때, 해당 자재를 내수고객 및 외수고객 모두에게 팔 것이기 때문에 이에 맞춰 수출이 가능한 자재로 설정해준다.

![image](https://user-images.githubusercontent.com/58674365/108819106-be036000-75fd-11eb-952f-e059a4816738.png)

<BR>

![image](https://user-images.githubusercontent.com/58674365/108819340-133f7180-75fe-11eb-9dd3-d54e9224ec6e.png)

<BR>

손익센터의 경우 현재 자재의 재고가 남아있어 변경이 되지 않는다. 나중에 FI/CO 부분에서 다뤄보도록 한다.

![image](https://user-images.githubusercontent.com/58674365/108819754-9234aa00-75fe-11eb-9ae4-62b75b87f8f0.png)

<br><br>

## 4. Business Partner 생성 및 설정 

고객마스터, Business Partner를 생성해보도록 한다. 

- T-BP

### A. 내수고객 생성

#### 1) 비즈니스 파트너 생성

상단의 조직을 눌러 새로운 고객 마스터를 생성한다. 

![image](https://user-images.githubusercontent.com/58674365/108820230-3c143680-75ff-11eb-8167-c9a175fc0cef.png)

<br>

그룹화에서 `BP02`로 설정하는 것에 유의한다. 만약 구매 Vendor를 생성하는 경우가 된다면, 그룹화 `1000` 으로 설정한다. 우선 비즈니스 파트너 일반 데이터를 생성한 후 이를 판매/구매 비즈니스 파트너로 확장하도록 한다. 

![image](https://user-images.githubusercontent.com/58674365/108820317-60701300-75ff-11eb-9144-7579dd6a1419.png)

<br>

내수 고객을 생성하는 것이기 때문에 주소는 한국으로 넣어주고 언어도 선택해준다.

![image](https://user-images.githubusercontent.com/58674365/108820639-d07e9900-75ff-11eb-8036-53637941233c.png)

<br>

#### 2) FI 고객 확장

저장 후, 상단 `PB 역할`에서 `I 고객` 을 눌러 확장해준다. <br>그리고 상단의 `회사 코드` 항목에 들어간다.

![image](https://user-images.githubusercontent.com/58674365/108820911-33703000-7600-11eb-9716-66afc2e5a0a9.png)

<br>

다음과 같이 회사 코드를 추가하고 계정 설정을 해준다.

![image](https://user-images.githubusercontent.com/58674365/108821100-792cf880-7600-11eb-99dd-16c10b47f7ed.png)

<br>

![image](https://user-images.githubusercontent.com/58674365/108821028-61557480-7600-11eb-9f5c-533130f89e38.png)

<br>

![image](https://user-images.githubusercontent.com/58674365/108821053-69adaf80-7600-11eb-84ac-73bf8864cfde.png)

<br>

#### 3) 고객 확장

`PB 역할에서 조회` 에서 `고객` 으로 확장을 해주고 `판매관리` 로 들어가서 다음과 같이 설정해 준다.

![image](https://user-images.githubusercontent.com/58674365/108821477-fe181200-7600-11eb-95d2-3623011d7746.png)

<br>

국내에서 이루어지는 거래이기 때문에 통화는 한국 통화로 맞춰주도록 한다. 

![image](https://user-images.githubusercontent.com/58674365/108821497-05d7b680-7601-11eb-86a2-62450a1aa234.png)

<br>

![image](https://user-images.githubusercontent.com/58674365/108821510-0a03d400-7601-11eb-9fef-25fb61e3d63f.png)

<br>

국내 거래는 세금이 붙기 때문에 과세 대상으로 설정해줘야 한다. <br>국내 경우 인코텀스 장소가 필요없다고 생각되지만, 판매문서 생성 과정에서 오류 메세지가 나오는 관계로 인코텀스도 정의해준다. 또한, 고객 계정 지정 그룹을 설정해줘야 이후 Billing 에서 회계 문서가 정상적으로 분개된다.

![image](https://user-images.githubusercontent.com/58674365/108821523-0ec88800-7601-11eb-8f9e-71c8f76540d2.png)

<br><br>

### B. 외수고객 생성 (수출)

#### 1) 비즈니스 파트너 생성

상단의 조직을 눌러 새로운 고객 마스터를 생성한다. 

![image](https://user-images.githubusercontent.com/58674365/108820230-3c143680-75ff-11eb-8167-c9a175fc0cef.png)

<br>

그룹화에서 `BP02`로 설정하는 것에 유의한다. 우선 비즈니스 파트너 일반 데이터를 생성한 후 이를 판매/구매 비즈니스 파트너로 확장하도록 한다. 수출 고객을 생성하는 것이기에 주소 및 언어는 외국으로 설정해준다. 

![image](https://user-images.githubusercontent.com/58674365/108821760-72eb4c00-7601-11eb-8342-2d60930d55da.png)

<br>

#### 2) FI 고객 확장

상단의 `PB 역할에서 조회` 를 `FI 고객` 으로 두고 확장한 후, `회사코드` 영역으로 들어간다. 

![image](https://user-images.githubusercontent.com/58674365/108822059-e3926880-7601-11eb-8dc9-7c2d1f12a828.png)

<br>

회사코드를 설정해주고 다음과 같이 나머지를 설정해준다. 

![image](https://user-images.githubusercontent.com/58674365/108822097-f0af5780-7601-11eb-86b5-1680ea03eb05.png)

<br>

![image](https://user-images.githubusercontent.com/58674365/108822082-eab97680-7601-11eb-9d6b-16d07689f148.png)

<br>

![image](https://user-images.githubusercontent.com/58674365/108822112-f73dcf00-7601-11eb-987d-f9a3fca73527.png)

<br>

#### 3) 고객 확장

`PB 역할에서 조회` 에서 `고객` 으로 확장을 해주고 `판매관리` 로 들어가서 다음과 같이 설정해 준다.

![image](https://user-images.githubusercontent.com/58674365/108821226-a11c5c00-7600-11eb-98ec-a7473162f66e.png)

<br>

영업 영역을 선택해준다. 

![image](https://user-images.githubusercontent.com/58674365/108822417-674c5500-7602-11eb-8010-8bdfb501cfd8.png)

<br>

외부(수출) 거래이기 때문에 통화는 해당 국가에 맞춰 설정해준다.

![image](https://user-images.githubusercontent.com/58674365/108821241-a8436a00-7600-11eb-8582-6f2edb90a767.png)

<br>

![image](https://user-images.githubusercontent.com/58674365/108821262-ada0b480-7600-11eb-94b9-5378a4bb05fa.png)

<br>

인코텀스 장소 및 세금 부분을 확인한다. 수출 거래이기 때문에 세금은 0으로 들어가야 된다. <br>국내에서 판매하는 것이 아니기 때문에 구매자에게서 국가에 지불할 세금을 받을 수 없기 때문이다. 대신, 반대로 생각하여, 구입하는 입장에서 구매처에 세금을 내지 않기 때문에 자국에 `관세` 를 내게 된다. (관세를 내지 않으면 자국 동일 자재보다 수입 제품이 경쟁력 있기 때문)

![image](https://user-images.githubusercontent.com/58674365/108821275-b2fdff00-7600-11eb-8445-6e379025d7f8.png)

<br>

다음과 같이 설정해준다. 이 항목이 빠지만, 나중에 회계 문서가 발행되지 않는다. 

![image](https://user-images.githubusercontent.com/58674365/108959050-324e0a00-76b7-11eb-93d0-afd45a6467f2.png)

<br><br>

## 5. Sales Order 생성 

내수 및 외수 거래에 대한 판매 문서를 각각 생성해보자.

- T-VA01: 판매 문서 생성 

![image](https://user-images.githubusercontent.com/58674365/108822862-07a27980-7603-11eb-9a69-3ed20755369d.png)

<br>

### A. 내수 판매 문서

이미 문서를 생성한 관계로 문서를 조회해보았다. <br>문서 생성시, 판매처에 위에서 생성한 수출 비즈니스 파트너를 넣으면 나머지 항목은 자동적으로 가져온다. <br>자재, 수량, 가격, 고객참조 정보를 넣어주면 된다. 인코텀스 오류가 난다면 해당 판매처에 대한 인코텀스를 설정해주자.

![image](https://user-images.githubusercontent.com/58674365/108823929-716f5300-7604-11eb-8abe-8cf9f5175333.png)

<br><br>

### B. 수출 판매 문서

이미 문서를 생성한 관계로 문서를 조회해보았다. <br>문서 생성시, 판매처에 위에서 생성한 수출 비즈니스 파트너를 넣으면 나머지 항목은 자동적으로 가져온다. <br>자재, 수량, 가격, 고객참조 정보를 넣어주면 된다. 

![image](https://user-images.githubusercontent.com/58674365/108823080-50f2c900-7603-11eb-95e1-dcc456be5bf4.png)

<br>

만약 내부 가격에 대한 오류 메세지가 뜬다면, 다음을 확인해보자. <br>오류 내역의 분석을 누르면 가격 결정에서 세율이 정해지지 않았음이 보인다. 세율을 정해주면 된다. (SAP GUI 전체 프로그램 내부 설정)

![image](https://user-images.githubusercontent.com/58674365/108823430-c1014f00-7603-11eb-9c68-2ea4b62983c2.png)

<br>

![image](https://user-images.githubusercontent.com/58674365/108823450-c8c0f380-7603-11eb-9859-eb4a0851ee42.png)