---
title: "ABAP Quick viewer"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - quick viewer
toc: true

---

# Quick viewer

Quick viewer는 maintenance view를 생성하지 않고 테이블을 조인해서 view로 볼 수 있는 간단한 툴이다. <br> Report 프로그램 화면과 같이 조회되는 만큼 program 이름이 자동 생성되고, 이를 통해 다른 사용자도 조회할 수 있지만 굳이 사용하지는 않는다.  즉, user-specific 툴로 다른 사용자와 공유하지 않는다. 또한, CTS 번호 생성이 불가하다. 

Quick viewer 생성 및 사용에 어려운 것은 없기에 간단하게 생성해보자. <br>여기서는 `OBJK` 와 `EQBS` 테이블을 사용하였다.

<BR>

## Quick viewer 생성

`T-SQVI` 를 통해 Quick viewer 툴을 실행한다. <br>생성할 Quick view 이름을 입력하고 `create` 를 눌러 새로 생성한다. 

![image](https://user-images.githubusercontent.com/58674365/123755244-ab27d180-d8f6-11eb-9972-fde995a7f403.png)



Quick view 이름 외에 view 조회시 상단에 표시될 title을 입력하고, 어떤 형태의 view를 생성할지 선택한다. <br>이번에는 두 테이블을 조인하여 조회하는 것이 목적이라 `Table join` 을 선택한다. 

![image](https://user-images.githubusercontent.com/58674365/123755500-eaeeb900-d8f6-11eb-80b2-aa0882e70ae2.png)



입력 및 체크표시를 누른 후 실행(F8) 하면 다음과 같은 빈 화면을 볼 수 있다. <br>좌측 상단의 아이콘을 눌러 추가하고자 하는 테이블을 입력해준다. <br>두 테이블의 조인을 위한 view 라면, header 테이블 부터 추가해주도록 한다. 

![image](https://user-images.githubusercontent.com/58674365/123755610-0e196880-d8f7-11eb-81f3-a60d6adfc866.png)



`OBJK` & `EQBS` 테이블을 순서대로 추가하면 다음과 같이 자동적으로 foreign key 를 통해 조인이 된다.

![image](https://user-images.githubusercontent.com/58674365/123755787-3acd8000-d8f7-11eb-8ec7-2c7bbda3e4e0.png)



다시 뒤로 돌아가면, 추가한 테이블에 대한 내역이 보인다. 

![image](https://user-images.githubusercontent.com/58674365/123756104-8c760a80-d8f7-11eb-907f-cedbede445e0.png)



이제 조인된 두 테이블, view에 대해 어떤 값을 조회조건으로 주고 어떤 값을 출력할지 선택해준다.<br>좌측의 테이블을 클릭하면 각 테이블의 필드가 나열된다. <br>**Selection Fields** 는 만들어진 view에 대한 조회 조건이다. <br>**List Fields** 는 출력되는 view의 필드이다. <br>List fields를 선택함에 따라 우측에 필드가 추가되며, 그 순서대로 컬럼 순서도 결정된다.

![image](https://user-images.githubusercontent.com/58674365/123756680-22aa3080-d8f8-11eb-8b43-c96b8f76e3a2.png)



실행(F8) 하면 위에서 선택한 `Selection fields` 에 따른 조회 조건을 확인할 수 있다.  

![image](https://user-images.githubusercontent.com/58674365/123756739-2fc71f80-d8f8-11eb-86d3-3be0b5935b1a.png)



마찬가지로 선택한 `List fields` 에 따라 view의 데이터가 조회된다.

![image](https://user-images.githubusercontent.com/58674365/123756750-348bd380-d8f8-11eb-953e-6f26a274ff45.png)

<br>

## Quick view 수정

생성된 quick view에 대한 테이블 조인 조건 등을 변경하고 싶다면 해당 화면의 `Data Source` 부분의 `Change join` 을 클릭하면 초기 조인 테이블 설정 화면으로 이동한다. 

![image](https://user-images.githubusercontent.com/58674365/123756781-3f466880-d8f8-11eb-92fd-4a260a59762c.png)



테이블 추가/변경은 물론 조인의 성격 (left, inner ... ) 또한 변경 가능하다.


![image](https://user-images.githubusercontent.com/58674365/123756803-42d9ef80-d8f8-11eb-9ff2-2c068fcf51ce.png)

<br>

## 프로그램명으로 Quick view 조회

Quick view는 user-specific 이고 다른 사용자가 조회하지 못하는 것이 맞다.<br>그러나 report 프로그램으로 view의 데이터가 출력되기 때문에 자동적으로 프로그램을 생성하기 때문에 (추천하지도 않고 사용하지도 않지만) 다른 사용자가 프로그램 명으로 조회할 수 있기도 하다.

프로그램명은 다음과 같이 자동적으로 생성되는 이름이며, 실행시 동일한 Quick view 가 실행된다

 ![image](https://user-images.githubusercontent.com/58674365/123757562-12468580-d8f9-11eb-8be3-8f7650498520.png)



![image](https://user-images.githubusercontent.com/58674365/123756739-2fc71f80-d8f8-11eb-86d3-3be0b5935b1a.png)



