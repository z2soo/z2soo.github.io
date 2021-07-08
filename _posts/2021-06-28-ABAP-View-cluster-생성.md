---
title: "View cluster 생성"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - view
  - maintenanve view
  - clsuter view
toc: true
---

# View cluster 생성

View를 생성함으로써 table 여러개 조인해 한 번에 유지보수가 가능하다면, View cluster는 이러한 view 여러개를 하나의 화면으로 제공해준다. View cluster를 생성하기에 앞서 2개 이상의 view를 먼저 생성하고 시작하자. 

- T-SE54 : Table, View, Cluster, Variant 관련

상단의 `Edit view cluster` 를 눌러 view cluster 항목으로 넘어간다.

![image](https://user-images.githubusercontent.com/58674365/124088170-a3e6fc00-da8d-11eb-899b-9953325296d7.png)



생성하고자 하는 view cluster 이름을 입력하고 생성한다. 

![image](https://user-images.githubusercontent.com/58674365/124088188-a8abb000-da8d-11eb-8ba3-7372cfac88f8.png)



좌측의 `Object structure` 를 눌러 해당 view cluster로 생성한 view 또는 table을 입력하고 `Field-dependence` 를 실행한다. 이는 한 줄 씩 선택하여 해줘야 한다. 

![image](https://user-images.githubusercontent.com/58674365/124088211-ad706400-da8d-11eb-99fa-1c3c8a1e1ccf.png)



`Field-dependence 가 완료된 후 short text를 입력해준다. 

![image](https://user-images.githubusercontent.com/58674365/124088228-b2cdae80-da8d-11eb-993f-d6bf995d1242.png)



다시 `Header entry` 로 돌아가, activate 해주면 된다. 

![image](https://user-images.githubusercontent.com/58674365/124088238-b82af900-da8d-11eb-81e1-d3030be92756.png)



그 결과 해당 view cluster에서 위에서 설정한 3개의 뷰를 한번에 조회 및 유지보수할 수 있다.

![image](https://user-images.githubusercontent.com/58674365/124089521-ed841680-da8e-11eb-9c71-011d9c95e0c5.png)
