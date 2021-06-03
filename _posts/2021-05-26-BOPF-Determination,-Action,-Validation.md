---
title: "BOPF Determination, Action, Validation"
categories: 
  - BOPF
  - ABAP
tags:
  - abap
  - sap
  - bopf	
  - fpm
  - fbi
  - action
  - determination
  - validation
toc: true
---

# Lecture 2. Determination, Action, Validation

## 1. Introduction to Determination 

> Overview of standard TOR determination

Business object instance의 lifecycle 동안 연쇄적인 반응이 필요한 이벤트가 있을 수 있다.<br> 예를 들어 특정 노드 정보가 생성, 저장, 수정 되었을 때, 관련된 노드에서 마찬가지로 정보의 업데이트가 필요할 수 있다. <br>즉, 특정 노드의 정보 변경에 대해 reaction을 제공하는 것이다. 



### 1) Determination configuration

TOR business object의 특정 determination을 클릭하여 determination의 설정에 대해 보도록 하자. 

![image](https://user-images.githubusercontent.com/58674365/120603816-5f187700-c487-11eb-91a0-8acc34279718.png)

- **Determination category** <br>
  determination이 persistent 인지 혹 transient 인지 설정
- **Change mode**<br>
  Persistenr => Exclusive Write
  Transient => Onlt Read
- **Clss/Interface**<br>
  determinaton implementation을 위한 class



아래의 체크박스는 해당 class에 어떤 method를 가지고 있는지를 나타낸다. 

![image](https://user-images.githubusercontent.com/58674365/120603935-7bb4af00-c487-11eb-92e2-dda090b9f6a2.png)



**Request, Read & write nodes** 부분은 언제 determination이 호출되는지를 설정한다. 

![image](https://user-images.githubusercontent.com/58674365/120603994-8cfdbb80-c487-11eb-9d66-51d4b9181c5b.png)

- **Request nodes** <br>
  determination을 trigger 하는 node
- **Read nodes** <br>
  determination이 실행되기 전에 buffer에 load 되어야 하는 node
- **Write nodes** <br>
  After Loading Determination의 경우에만 사용
- **Modeled Only** <br>
  체크표시되면 해당 행의 전체 configuration이 runtime effect을 가지지 않음

참고로  create, updated ... 마지막 determin의 경우 사용되는 것을 본 적이 없다. 



**Node category assignment**는 정확히 어느 시점에 determination이 trigger 되는지 설정한다. 

![image-20210525143928747](C:\Users\에코아이티\Desktop\typora img\image-20210525143928747.png)

기술적으로는 하나의 determination을 여러 event에서 사용하는 것이 가능하다. <br>하지만 최적은 1개 determination을 1개의 event에 사용하는 것이고, 1개의 event에 대해서도 여러 determination을 가지는 것을 권장하지 않는다. 만약, after modify 에 대해 서로 다르게 행동하는 여러 determination을 가지게 된다고 가정하면, BOPF가 각 determination을 trigger 할지 말지 판단하면서 느려지게 된다. 



**Determination dependency**는 determination의 순서를 정의한다. 

![image-20210525144749007](C:\Users\에코아이티\Desktop\typora img\image-20210525144749007.png)

![image-20210525144821970](C:\Users\에코아이티\Desktop\typora img\image-20210525144821970.png)

- **Necessary Determination** <br>
  해당 determination 이전에 발생하는 determination
- **Dependent Determination** <br>
  해당 determination의 변경사항으로 인해 발생하는 determination



### 2) Determination implementation

Determination은 `/BOBF/IF_FRW_DETERMINATION` interface를 implement 하고, 3가지 method를 제공한다.

- **CHECK_DELTA**<br>
  reduce set of changed node instance
- **CHECK**<br>
  reduce the amount of node instance
- **EXECUTE**<br>
  catains the main determination business logic



## 2. Introduction to Actions

> Creation of "Set Load Plan status to completed" action

Action에 대한 설명은 다음과 같다.

-  service consumer, 다른 business object, 다른 action/determination에 의해 실행 가능
- business object node에 할당된 business logic
- action을 통해 node 인스턴스의 데이터 생성, 삭제, 수정 가능



### 1) Action configuration

action이름을 정할 때, `동사 + 정확히 어디에서 해당 action이 수행되는지` 의 네이밍 룰을 따르도록 하자.

![image-20210525151059343](C:\Users\에코아이티\Desktop\typora img\image-20210525151059343.png)



![image-20210525151450099](C:\Users\에코아이티\Desktop\typora img\image-20210525151450099.png)

- **Node**<br>
  action이 할당된 노드
- **Action cardinality**<br>
  얼마나 많은 노드 인스턴스에 해당 action이 한번에 실행 될 수 있는지 차수
- **Change mode**<br>
  determination의 change mode와 유사하게 해당 action으로 다른 변경이 이루어지는지 여부,
  Persistent action => Exclusive write
  Action only on transient fields => Only Read Mode
- **Execute Action only if ~ 체크박스**<br>
  체크시 모든 노드에 대해서 해당 action을 실행할 수 있다.

- **Class/interface**<br>
  해당 action을 implement하는 class



**Read & Write Nodes** 에서는 action 실행에 대한 상황을 선택한다. 선택 사항은 determination을 참고하자.

![image-20210525152710756](C:\Users\에코아이티\Desktop\typora img\image-20210525152710756.png)



**Node category assignment **에서는 어느 노드에서 해당 action이 실행 가능지 체크한다. 

![image-20210525152924840](C:\Users\에코아이티\Desktop\typora img\image-20210525152924840.png)



**Property change trigger** 는 property에 대해 설정하지만 이는 주로 property determination에서 설정된다.

![image-20210525153005123](C:\Users\에코아이티\Desktop\typora img\image-20210525153005123.png)



## 3. Introduction to Validation

Validation은 특정 상황에서 실행되는 체크 로직으로써 어떤 데이터도 수정하지 않는다. <br>
대신, 실패한 노드 인스턴스에 대한 키 값과 메세지를 리턴한다. 



Validaiton은 다음 두 카테고리가 있다.

- **Action validation**<br>
  action을 실행하기 위한 전제 조건이 충족되었는지 체크
- **Consistency validation**<br>
  node instance의 일관성을 체크

<br>

Determination 및 action의 configuration과 마찬가지로 validation 또한 실행 상황, 시점, 노드 설정이 가능하다.<br>
단, 다른 것과 다르게 `Failed early` 라는 원칙을 가진다. 이는 만약 10개의 step이 수행되어야 하는 경우 2에서 fail 하면 나머지 step을 수행하지 않는 것이다. 이미 consistency가 깨졌기 때문에 굳이 validation check를 할 필요가 없다고 판단하기 때문이다. <br>그래서 신규 개발시 모든 check를 초반부에 두려고 한다. <br>input parameter에서 inconsistency가 존재하면 performance에 대해 영향을 줄 수 있는 로직을 실행하지 않아도 되기 때문이다. 