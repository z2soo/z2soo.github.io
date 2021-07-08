---
title: "Maintenance view 생성"
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

# Maintenance view

다음 과정을 통해 CBO table 및 CBO text table을 생성하고, 이 두 테이블을 사용하여 maintenance view를 생성해보도록 할 것이다.

1. `ZZ061T01` standard CBO table  생성
2. `ZZ061T01T` standard CBO text table 생성
3. `ZZ061T01T` 에 대해 `ZZ061T01` foreign key 생성
4. `ZZ061V01` maintenance view 생성 



Mainteneance view는 말 그대로 SAP ABAP에서 여러 테이블의 정보를 한 번에 유지보수하기 위해 생성되고 사용된다. 하나의 view에서 수정된 데이터는 자동적으로 해당 데이터가 속한 각 테이블에 이를 반영한다. 

이번에는 여러 테이블을 가지고 생성되는 Maintenance view에 대해 다룰 것이며, <br>다음에는 이렇게 생성된 Maintenance view 여러개를 가지고 생성되는 View cluster에 대해 다뤄볼 것 이다.<br>여러 종류의 view에 대해 간단하게 특징만을 설명하자면 다음과 같다.

- **Database view** : Read only, inner join
- **Help view** : Read only, outer join
- **Projection view** : Single table
- **Maintenence view** : Editable, outer join

<br>

## Maintenance view 생성

### CBO table 생성 

우선 maintenance view 생성에 사용한 CBO table을 두 개 생성해주도록 하자. (T-SE11) <br>다른 여러 테이블을 조인하여 데이터를 한 번에 조회 및 유지보수하기 위한 것이 목적인 만틈 테이블 간 foreign key 관계가 존재하게끔 생성해주자. <br>이미 존재하는 테이블을 사용한다면, foreign key 관계가 있는 테이블을 사용하자.

이번 실습 뿐 아니라 이후에도 계속 사용하기 위해 다음과 같은 테이블을 생성했다. <br>잊지말고 main table에는 생성/변경에 대한 기록을 위한 필드도 넣어주자

- ZZ061T01 : 타입, 삭제 플레그
- ZZ061T01T : 타입, 타입에 대한 설명, 언어키 

![image](https://user-images.githubusercontent.com/58674365/123914062-f2c36180-d9b9-11eb-8fad-547403afa31c.png)



![image](https://user-images.githubusercontent.com/58674365/123914344-45048280-d9ba-11eb-8177-c0619ab054d1.png)



![image](https://user-images.githubusercontent.com/58674365/123917875-3cae4680-d9be-11eb-869b-235f2ac6dbbe.png)

<br>

### Foreign key 설정

그 다음 두 테이블에 대한 외래키를 설정한다. <br>여기서는 text table에서 main table과 공통되는 키로 걸어준다. 해당 필드에 커서를 두고 열쇠 모양을 클릭하여 다음과 같이 입력한다. 

![image](https://user-images.githubusercontent.com/58674365/123917777-21433b80-d9be-11eb-9a98-c42a181e9ba0.png)



![image](https://user-images.githubusercontent.com/58674365/123914701-a3c9fc00-d9ba-11eb-96f4-9dfa86603783.png)

<br>

### Maintenenace view 생성 

T-SE11 에서 View > Maintenance view 를 생성한다. 테이블을 입력하고 하단의 `Relationship` 을 누르면 `FK` 로 엮인 테이블을 확인 및 추가할 수 있다. <br>

![image](https://user-images.githubusercontent.com/58674365/123019235-0e9f9400-d40b-11eb-8a32-445c450493ca.png)



![image](https://user-images.githubusercontent.com/58674365/123915076-12a75500-d9bb-11eb-98df-c0db734579db.png)



선택 및 추가하면 어떤 필드를 조건으로 외래키가 엮여있는지 자동적으로 표시된다. 

![image](https://user-images.githubusercontent.com/58674365/123915095-1935cc80-d9bb-11eb-87fa-c060d77df27f.png)



View Field 탭으로 가서 어떤 필드를 view에서 보여줄지 추가한다. <br> Table fields 버튼을 눌러 추가 가능하다. 


![image](https://user-images.githubusercontent.com/58674365/123915193-336faa80-d9bb-11eb-8ce0-ac44dc4cc476.png)



다음과 같이 필드를 추가했다.<br>이 때, `생성자/생성날짜/생성시간` , `수정자/수정날짜/수정시간` 에 대한 정보는 오직 조회만 가능하고 변경(Edit)이 불가해야하기에 `R` 값을 넣어준다. 

![image](https://user-images.githubusercontent.com/58674365/123918394-cb22c800-d9be-11eb-812d-a7aa3f83bc23.png)

 <br>

### Generate table maintenance

View를 활성화한 후 상단 `Utility > Table Maintenance Generator` 로 들어간다.<br>만약 view/table의 필드 중 `string` 타입이 있다면 generate가 되지 않으니 확인해주도록 하자.

![image](https://user-images.githubusercontent.com/58674365/123919707-2b663980-d9c0-11eb-96bf-72715bffefaf.png)



생성된 각 View에 대해서는 이후에 다루겠지만, 데이터 저장 전후에 대한 event 생성은 물론 하나의 화면으로 보여지기 때문에 PAI, PBO 에 대한 로직 추가가 가능하다. <br>따라서 관련 정보와 함께 저장될 Function group을 할당해줘야 하고, screen 번호 또한 입력해줘야 한다. 해당 Function group에 몇 개의 (몇 번의) screen 까지 생성되어 있는지 모르다면 상단의 `Find Scr.Number` 를 통해  제안받을 수 있다. <br>Maintenance type은 해당 view가 단계적으로 이루어지는지 혹 따로 step/tree 구조 없이 이루어지는지를 설정해준다. 

설정을 완료했다면, 상단 좌측의 종이 표시를 눌러 생성해준다.<br>만약 Generate Table Maintenance Dialog 생성 전 단계의 설정에서 변경이 발생하여 수정하게 된다면, Dialog 또한 지우고 다시 생성해줘야 함을 잊지 말자. 

![image](https://user-images.githubusercontent.com/58674365/123920106-8dbf3a00-d9c0-11eb-9d64-9b6d3cc5e9b7.png)



Dialog 생성 후 아래 버튼을 클릭하면 생성된 view가 보여진다. 

![image](https://user-images.githubusercontent.com/58674365/123922605-2a82d700-d9c3-11eb-8389-9fb66f548e35.png)



값을 수정하고 저장하려고 보면, `R` 표시한 부분은 변경이 불가하게 block 된 상태로 나타난다. 값을 저장하면 자동적으로 main table  `ZZ061T01`,  text table `ZZ061T01T` 에 값이 저장된다. 

![image](https://user-images.githubusercontent.com/58674365/123922793-5900b200-d9c3-11eb-81c8-5fd2a8c9f2c3.png)



그러나 저장을 해도`생성자/날짜/시간`,  `수정자/날짜/시간` 에 대한 값이 들어가지 않는다. <br> 이 부분은 아래 내용과 더불어 다음 글로 짤막하게 다루고자 한다. 

- view transaction code 생성
- view 데이터 변경에 대한 change document 생성
- view event 생성 (변경 전, 후)
- 여러 maintenance view를 사용한 view cluster 생성  ...

![image](https://user-images.githubusercontent.com/58674365/123922966-83eb0600-d9c3-11eb-9785-78efe1016d3d.png)



