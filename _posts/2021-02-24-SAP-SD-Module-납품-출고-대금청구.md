---
title: "SAP SD Module 납품 출고 대금청구"
categories: 
  - MODULE
tags:
  - sd module
  - sap
toc: true
---

> SAP Sales & Distribution 즉, 판매 프로세스는 다음과 같다.<br>판매오더 생성 → 아웃바운드 납품 → 출고전기 → 대금청구<br>이전 포스팅에서 마스터 데이터 및 판매오더 생성 과정을 보았다면, 이번에는 그 이후의 과정을 살펴보도록 한다. 



## 1. 조건 레코드

SD의 가격 책정은 가격 계산시 일련의 조건으로 정의된다. 고객, 제품, 주문 수량, 날짜 등의 모든 요소가 가격을 결정하며, 이러한 가격 책정 요소가 SAP 시스템에서 조건 유형으로 정의된다. 이 가격 책정 요소에 대한 가격 책정 정보를 관리하기 위해서는 조건 레코드를 작성해야 한다. 

- T-VK11: 조건 등록

![image](https://user-images.githubusercontent.com/58674365/108948420-cb742500-76a5-11eb-85b3-63ebdf24d37e.png)

등록된 마스터 데이터가 저장되는 테이블은 다음과 같다.

- A305: 고객/자재
- A304: 자재
- KONP: 조건

VBAK의 문서 조건 번호(KNUMV)를 가지고 PRCD_ELEMENTS에 들어가면 해당 조건을 볼 수 있다. 

![image](https://user-images.githubusercontent.com/58674365/108948773-7684de80-76a6-11eb-8fdf-31736ad169ff.png)

![image](https://user-images.githubusercontent.com/58674365/108948814-8e5c6280-76a6-11eb-891c-41c48e9ef47c.png)

<br><br>

## 2. 판매오더 미완료 로그

판매오더를 생성시, 미완료 로그가 존재해도 저장이 가능했다. 미완료 항목을 config에서 따로 설정해줄 수도 있지만, sales order를 기준으로 다음 작업을 진행하기 위해 미완료 항목을 모두 해결해줘야 한다. 

Sales Order 리스트 보는 티코드는 다음과 같다.

- T-VA05N : ECC 버전
- T-VA05 : HANA 버전

T-SE11에서 VBAK 테이블을 통해서도 조회가 가능하다.<br>필드 중 마지막 두 필드를 보면 `C` 표시는 미완료 로그 없이 완료되었음을, `A`  또는 다른 표기는 미완료 항목이 존재함을 의미한다. 해당 문서는 T-VA02에서 수정해주도록 한다. 

- T-V.02: 미완료 판매오더만 조회

![image](https://user-images.githubusercontent.com/58674365/108949487-d8921380-76a7-11eb-948b-f61e1b579c92.png)

<br>

### 미완료 로그 주요 내역

1. 중량정보

   - 자재 기본 view에서 중량 및 순중량을 입력한다.
   - 이미 생성된 오더에서는 직접 수정하도록 한다.

2. 가격결정

   - 판가
   
   - 세금
     - 영세 확인
     - 고객 0/1
     - 자재 1
     
   - 내부원가
     
- 자재 표준가 > 원가추정
     - 신규 가격결정 수행
     
   - 출하지점
   
     - 고객 > 출하조건
     - 자재 > 적재그룹
   
   - 계정결정
   
     - 고객 > 계정지정 그룹
     - 자재 > 계정지정 그룹
   
   - 인도조건
   
     - 고객 > 인도조건

<br>

ex. 신규 가격결정 수행 과정은 다음과 같다.<br>오더의 자재에서 다음과 같이 가격에 오류가 나면 조건을생성해주고, 조건 탭에서 갱신을 클릭한다. 

![image](https://user-images.githubusercontent.com/58674365/108952293-94554200-76ac-11eb-8a48-60940d879b2a.png)

![image](https://user-images.githubusercontent.com/58674365/108952328-9fa86d80-76ac-11eb-8b30-3f7eabc6529d.png)

![image](https://user-images.githubusercontent.com/58674365/108952342-a46d2180-76ac-11eb-8fe4-8e6c4b3a09db.png)

<br><br>

## 3. Delivery Order 생성

아웃바운드 납품은 실제 납품할 품번과 수량을 확정하는 과정이다. 

Delivery Order는 두 가지 방법으로 생성 가능하다. 출하하고자 하는 sales order 번호와 출하 지점을 다 입력하여 조회 및 생성하는 방법과 이전에 만든 sales order에서 바로 납품으로 넘어가는 방법이다. 

- T-VL01: 판매오더 참조
- T-VA03: 판매오더 조회 화면에서 바로 납품



### T-VL01

해당 티코드에서 납품 문서를 생성하기 위해서는 날짜 및 출하지점까지 알아야 한다. 출하 지점은 sales order 품목의 출하 부분에서 확인 가능하다.

![image](https://user-images.githubusercontent.com/58674365/108952449-d1b9cf80-76ac-11eb-863e-b2bb7cc0d8ae.png)

<br>

### T-VA02/VA03

Sales order를 조회하고 바로 납품으로 넘어가는 방법은 다음과 같다. 납품에서 피킹 처리까지 같이할 수 있다.  

![image](https://user-images.githubusercontent.com/58674365/108952569-0af23f80-76ad-11eb-9f51-58a6b520a395.png)

![image](https://user-images.githubusercontent.com/58674365/108952626-28270e00-76ad-11eb-9a48-41a10ac436cd.png)

<br><br>

## 4. Picking / Packing / Shipment

출하 변경에서 Picking을 생성해준다. 납품 과정에서 피킹도 같이 처리했다면, 바로 출고 전기 단계로 넘어가면 된다. 

- T-VL02N

![image](https://user-images.githubusercontent.com/58674365/108971398-9c6dab80-76c5-11eb-9a67-b437a4520bd7.png)

### Picking 

피킹, 출고할 자재를 가져온다. SAP WM Module로 상세 관리가 가능하다.

### Packing

적하, 가져온 자재를 운송하기 위해 일종의 포장 작업을 진행한다. SAP HU Module로 상세 관리가 가능하다. 

### Shipment

선적, 출고 자재에 대한 운반에 대한 배치 작업을 관리한다. (운송 수량 배분, 배차 등 모든 것) 수출품의 경우 항구 이후의 과정도 있지만 일반적으로 그 전까지의 과정을 관리한다. 피킹과 적하를 건너뛰고 DO에서 바로 Shipment가 가능하다.  

<br><br>

## 5. Goods Issue

출고전기는 실제로 고객에게 제품을 납품하는 행위를 말한다. 이 과정에서 재고가 감소하는 회계상의 거래가 발생한다. 즉, 매출원가를 인식하게 된다. <br>출고전기는 피킹을 진행한 transaction에서 진행 가능하다. 

- T-VL02N

![image](https://user-images.githubusercontent.com/58674365/108971398-9c6dab80-76c5-11eb-9a67-b437a4520bd7.png)

<br><br>

## 6. Billing

대금 청구는 다음과 같이 진행한다. 생성된 대금 문서를 조회할 때 회계 부분을 봐야한다. 표준원가 추정에서 원가가 0원 이상이어야 매출이 있는 것이기에 0인 것은 처리 못하게끔 해준다. 만약 대금 청구는 되었지만 분개가 되지 않는다고 오류가 나면 회계 처리가 되지 않은 것이다. 미결된 항목 조회에서 확인 할 수 있으며, 해결 후 저장해줘야 청구가 완료된다. 

- T-VF01
- T-VFX3 : 미결된 항목 조회

![image](https://user-images.githubusercontent.com/58674365/108958234-08e0ae80-76b6-11eb-8d37-0b29faecde7a.png)



![image](https://user-images.githubusercontent.com/58674365/108958161-ed75a380-76b5-11eb-9f1b-0767799124b6.png)

<br><br>

## 7. 기타 개념

### Credit Memo 대변메모

우리가 줘야하는 돈이다. 즉, `-` 로 표시되어야 하는 부분이다. 



### Debit Memo 차변메모

우리가 받아야 하는 돈이다. 즉, `+` 정상 케이스에 대한 +a 경우를 의미한다. 100원에 팔았지만 판가가 올라 110인 경우 10을 더 받아야 되는 경의 금액을 의미한다. 



### Incoterms 인도조건

 수출 경우에 해당한다. 당사에서 수출 항구로 내보내면, 항구에서 이를 컨테이너에 싣는다. 이 때, 어떤 자재가 어떤 컨테이너 박스에 얼마나 들어있는지, 중량이 어느 정도인지, 주문 정보(IV, 금액) 등의 정보가 `Packing list` 에 있다. 이를 `Invoice Packing List`  혹` Commercial Invoice` 라고 한다. 선박에 싣기 위해서는 무조건 필요하다. 이를 가지고 세관에서 수출 신고를 하면 신고 `필증(=면장)` 이 발행된다. (수입하는 경우에도 마찬가지) 이 필증을 가지고 싣고자 하는 선사에 신청하면 `B/L (Bill of Lading)`, 선박에 실었다는 문서를 준다. 이 문서는 배가 출발한다는 문서이기도 하며 주로 전날이나 당일날 발행된다. 

보통` Forwarding, 포워딩` 이라고 하는 대행업체는, packing list 정보를 보내면 이후의 과정을 대행해준다. 다른 말로 관세법인이라 한다. 

해상 관계에서 만약 두 회사간 신뢰가 없으면 은행을 사이에 두기도 한다. 각 국가의 표준 수출입 은행이다. 이 때는, 거래가 은행을 통해 이루어지고 `L/C` 라고 한다. 

이러한 은행이 끼는 경우가 아닌, 기업 끼리의 거래에서는 받는 입장에서는 구매의 DO - GR - IV 과정을 거치게 된다.

위와 같은 수출입의 인도는 다음과 같이 책임 범위가 나눠진다. 

* DDP<BR>고객(Vendor) 입장에서 우리가 귀사, 즉 목적지까지 배송해주는 것이다. 그리고 그 책임을 우리가 진다. ~도착지
* FOB<BR>운임을 포함하고, 배에 싣는 것 까지가 책임이다. 만약 바다에서 침몰 등 문제가 생겨도 우리는 책임이 없다. ~도착항구
* CIF<BR>운임 및 보험료를 포함하고, 배에 싣는 것 까지가 책임이다. ~도착항구
* EXW<BR>공장을 나서는 순간 우리의 책임을 떠난다. 귀사가 모든 것을 책임 및 담당한다. ~출발지