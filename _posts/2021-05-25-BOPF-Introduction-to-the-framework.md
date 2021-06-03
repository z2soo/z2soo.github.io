---
title: "BOPF Introduction to the framework"
categories: 
  - BOPF
  - ABAP
tags:
  - abap
  - sap
  - bopf	
  - fpm
  - fbi
toc: true
---

# Lecture 1. BOPF: Introduction to the framework

> 앞으로의 교육 과정 및 개략

![image](https://user-images.githubusercontent.com/58674365/120099592-4c482e80-c177-11eb-8a78-296a04bffee1.png)

SAP TM은 다양한 프레임워크에 기반한다. 그 중 BOPF는 Business Object Process Framework의 약어로 TM의 근간을 이룬다. 이는 Backend framework으로써 business object 및 business documents를 다루는데 사용된다. JAVA, C# 등 비교해보면 사실상 `어떤 어플리케이션에 어떤 데이터를 가져갈 것인가?` 와 같은 동일한 목적을 가진다. 

- Frontend framework : FPM (Floor Plan Manager)
- Backend framework : BOPF (Business Object Processing Framework)
- 둘을 연결하는 framework : FBI (FPM BOPF Integration)

Business object는 BOPF에 의해 모델링되고 implement 된다. UI는 Web Dynpro에 기반하고, modeling, implementing, configuring 하는 FPM에 의해 구현된다. Floor Plan Manager BOPF(FBI)는 backend와 UI를 연결한다. 참고로 SAP 서비스에서는 웹을 사용해 통신하지 않고 integration의 개념을 사용한다. 

교육 내용은 back-end의 BOPF와 frontend의 FPM의 내용을 훑은 후, customization에 대한 내용인 POWL (Personal Object Worklist Component)를 통해 다양한 enhancement 기술인 BAdI 및 실제 다양한 파트너사와 고객사들이 extend 할 때 사용하는 기술에 대해 다룰 것이다. 그리고 마지막으로는 PPF(Post Processing Framework), PCF(Post Controller Framework), BRF+(Business Rule Framework Plus) 에 대해 다룰 것이다. 

PPF는 SAP 개발에서 광법위하게 사용된다. Maon transaction에서 데이터가 변경된 후 이를 확정시켜 EWM으로 메시지를 전송하는데, 시간이 많이 소요되기에 백그라운드에서 작동하게 하는 작업이다. 

PCF는 디자인 패턴을 파악하고 일종의 전략을 customize하는 것이다. 예를  들면, 하나의 toward document에서 변경 사항이 발생했을 때 시스템에서 어떻게 반응할지 정의하는 것이다. PCF 안에서 특정 메소드를 사용하여 변화에 대해 반응하도록 할 수 있다. 

BRF+ 를 통해서는 다양성은 물론 backend custimizing 즉 ABAP Code 작성이 아니더라도 business rule  정의 및 extend가 가능하다.

<Br>

## 1. Lecture overview

### BOPF란?

- Business Object Process Framework
- 다른 back-end framework와 마찬가지로 data access, buffering, modeling, locking 등에 대해 다루는 framework



### TM에서 중요한 이유? 선택한 이유?

- create, save, query 등 기본적인 기능 제공을 통한 빠른 prototype 생성 가능
- 재사용 가능한 코드를 통한 implementation(구현)의 재사용성
- Enhancement를 통한 customizing 가능
- Buffer와 DB layer의 데이터 및 코드 관리 가능

- 공통의 개발 규칙 적용 가능 



### 기타

- 복잡하지만 그만큼 막강한 기능을 가진 프레임워크
- 이해하고 배우는데 객체지향 개념의 문턱이 있지만 이를 넘으면 유용하게 사용할 수 있음
- 타 프레임워크와의 차이점은 데이터베이스 구조에 계층적인(hierarchy) 구조를 가지고, 
  document가 가진 계층적인(tree) 구조와 잘 맞음
- BOPF는 tree 구조에 근간을 둠
- 소프트웨어 개발자들이 관계형 데이터베이스에서 관계차수(cardinarlity) 및 개체 간 관계차수(cardinarlity)를 생각하면서 개발하는 것처럼, BOPF도 이를 사용하지만 항상 tree 구조로써 root와 node로 구성되어 있음

<br>

## 2. BOPF Introduction

TM의 business object는 BOPF Modeing Tool에서 볼 수 있다. 원래는 `T-code BOPF (이하 T-BOPF 형태로 표기)` 로 시작했지만, business 요소가 들어가면서 `T-BOBF` 로 변경되었다. 해당 티코드로 들어가면 BO 목록, 노드의 계층(hierarchy), configuration, 각 노드의 elements(association, action, determination etc)은 물론 TM 개발 외에도 S/4HANA와 관련된 개발 objects를 볼 수 있다. 

![image](https://user-images.githubusercontent.com/58674365/120593155-0a223400-c47a-11eb-84c4-8cb6e8dd806c.png)



다양한 business object(이하 BO) group을 볼 수 있다. 
처음에 보이는 것이 **Business Process Object** 이며, ERP에 있는 transactional document를 볼 수 있다. 

![image](https://user-images.githubusercontent.com/58674365/120593233-23c37b80-c47a-11eb-914b-7dadb4f2d339.png)



그 다음 보이는 그룹이 **Dependent Object** 이다. 
이는 BO의 재사용 가능한 부분으로 TM에서 사용되며, 혼자 독자적으로 존재가 불가능한 오브젝트로 main에 종속된다. 예를 들어, 운송비 항목(dependent object)은 계산이 되는 근거 문서(main) 없이는 존재 불가한 것과 같다.

![image](https://user-images.githubusercontent.com/58674365/120593280-376ee200-c47a-11eb-8c7e-9147a9ad6c95.png)



그 다음은 기준정보 **Master Data Object** 로 material, business partner 등에 대한 정보가 있다. 
대부분 마스터 데이터 BO는 SCM Basis Master Data Layer (MDL)로 단순 읽기만 가능한 경우이다. (read-only way) 따라서 이 마스터 데이터 object의 내용은 SCM Basis의 standard transaction에 의해 영향을 받는다. 
SAP ERP와 SAP TM 간의 마스터 데이터 공유는 SCM Core Interface (CIF)의 미들웨어 구조에 따른다. SAP TM 내 마스터 데이터에 대한 접근은 BOPF의 master data object에 의해 데이터베이스에서 읽어서 (read) 접근하는 경우에만 사용되고 데이터 변경(data modify)에는 사용되지 않는다. 그 예로, 장소, business partner, 자재 등이 있다. 

![image](https://user-images.githubusercontent.com/58674365/120593337-49508500-c47a-11eb-851d-14cab5be54fb.png)



그 외에도 다음과 같은 정보를 볼 수 있다.

- **Meta Data Object**<br>
  BO의 행위를 정의하기 위해 고려되는 define data이다. 예를 들어 운송 주문 데이터에서 화물 단위를 정의하는 방법이나 규칙이 있다. (ex. Freight unit building rule)
- **Combined Structure and Table Type**<br>
  이 Database Dictionary(DDIc)는 노드의 데이터 구조(data structure)를 포함한다. 또한, 노드 instance의 키(key), 부모 노드의 키(parent_key), 관련된 BO instance 키(root_key)를 포함한다. 
- **Data Structure / Data Structure(tr.)**<br>
  이 DDIC structure는 노드 데이터를 대표하는, 노드의 속성(attributes)을 포함한다. 
  (tr.) 이 포함된 사항은 노드의 transient attribute를 포함하며, 이는 런타임 동안에만 사용된다.
- **Extension Include / Extension Include(tr.)**<br>
  노드의 field extension은 해당 include를 통해 structure append 방식으로 가능하다. 오직 런타임에만 연관되는 경우 transient extension include로 구분한다.
- **Database Table**<br>
  노드 정보가 영구적으로 저장되는 데이터베이스 테이블이다.

<br>

## 3. Get familiar with BOPF configuration tool

> Usage in TM based on example with TOR



### 1) Business object

TM에서 주로 사용되는 TOR business object를 가지고 보다 상세한 설명을 해보도록 하겠다. 

![image](https://user-images.githubusercontent.com/58674365/120098963-cc6c9500-c173-11eb-9493-816803991eff.png)

Business object는 개발을 시작하면 일반적으로 현실 업무에 사용되는 instance 별로 데이터 베이스에 객체를 만들게 된다. 좌측 트리 구조를 보면, 해당 business object에 대한 node가 어떤 것이 있는지 확인 가능하다. 

- **Node structure**<br>노드를 트리구조로 보임
- **Node element**<br>
  어떤 노드가 있는지 리스트로 보임 
- **Group** <br>
  business object를 save, modification 하는 시점에 적용되는 validation을 모은 것



우측을 보면 해당 business object, `/SCMTMS/TOR` 에 대한 정보를 확인할 수 있다. 

![image](https://user-images.githubusercontent.com/58674365/120099452-7a793e80-c176-11eb-8d73-e3d99c367b85.png)

- **Business object model generated** <br>
  다른 데이터 element framework를 이용해서 node를 생성할 때 체크한다. 
  TM에서는 BO를 직접 생성하기 때문에 사용하지 않는다.
  
- **Business object can be enhanced** <br>
  Enhancement개념으로 타 object로부터 inherit 가능 여부를 의미한다. 이 세팅 안에서 적용되는 BOPF는 standard enhancement보다 더 나아가서 BO를 enhancement하는 것 외에도 신규 function 개발 및 기존 object 재정의를 가능하게 한다. Enhancement BO는 뒤에서 다시 상세하게 다루겠지만, standard BO와 다르게 이름을 z/y로 시작한다.  

- **Business object has authorization checks** <br>
  BOPF를 이용하면 다양한 level에서 권한 체크가 가능하다. items, root, different node 등 사용자가 어떤 데이터 모델에 엑세스 및 수정할 수 있는지 권한을 체크할 수 있다.

- **Business object is Enter. search enabled** <br>
  Business client에 검색 기능을 활용할 수 있게 해주는 기능이다. 

- **Business object is abstract** <br>
  인스턴스(document)를 생성하기 위해 사용되는 BO가 아니다. 대신, 여러 BO들의 공통된 기능을 하나로 묶는 역할을 한다. 일종의 super class 역할을 하면서 inherit 하는 개념이다.  

- **Business object is final** <br>
  해당 business object를 타 BO가 물려받지 못하게 강제하는 역할을 한다.

- **Business object is GenIL enabled** <br>

  TM에서 사용되는 것을 본 적이 없어 설명 생략 



![image](https://user-images.githubusercontent.com/58674365/120099651-a2b56d00-c177-11eb-8b92-8a151fdf5f64.png)

- **Root node** <br>
  어떤 노드가 root인지 기술하는 것으로 root 외 다른 이름으로 변경하는 것은 권장하지는 않는다. 특히 root 외의 다른 노드 이름을 root로 칭하는것은 시스템에 혼란을 줄 수 있다. BO에는 하나의 root만 존재하며, BO 생성시 자동적으로 생성된다. 
- **Buffer** <br>
  select 된 데이터를 일시적으로 모아두는 메모리
- **Buffer class** <br>
  다수의 데이터의 변경 사항에 대한 정보가 저장된 클래스 
- **Buffer dispatcher** <br>
  참고로 TM의 buffer dispatcher는 standard에서 function, method를 inherit 하여 enhance 시킨 것



그렇다면 buffer, buffer class, buffer dispatcher의 역할은 무엇일까?
Applicaiton을 시작으로 데이터가 일정하게 흐른다. Frontend에서 데이터베이스로 데이터를 요청한다. 
데이터베이스에서 데이터가 select 되고 나면, 그 데이터를 넣어두는 곳을 buffer로 이해하자. 
보통은 node 별 하나의 buffer를 가진다. 이 경우, 향후 동일한 데이터를 select 할 때, 
다시 데이터베이스에 요청하지 않아도 되고, 따라서 시스템의 performance 또한 보장할 수 있게 된다. 

데이터 여러 건을 수정하고 수정 사항을 rollback 해야 하는 상황을 가정해보자. 
이 때, buffer class는 modification에 대해 그 상태를 저장을 한다. 예를 들어 처음은 state1, 두 번째는 state 2 ... etc. 이러한 buffer class 덕분에 현재와 이전 상태 비교시 어떤 데이터가 어떻게 변경되었는지 인지 가능하게 된다. 

그래서 buffer, buffer class, buffer dispatcher를 이용하면 buffer의 상태 관리, 데이터의 관리가 가능하다.
각 노드는 노드 별 buffer class가 있고, 하나의 business object는 buffer class를 관리하는 하나의 buffer dispatcher를 가진다.

아래는 TM의 buffer dispatcher 로써 파란색은 inherit 되어진 메소드, 검정색은 재정의한 메소드이다.

![image](https://user-images.githubusercontent.com/58674365/120593482-8caaf380-c47a-11eb-9c23-9e35193e3fe4.png)



### 2) Node

해당 business object의 노드에 대해 알아보자. 우선, 동일한 business object 내에서 노드명은 중복될 수 없다. 

<img src="https://user-images.githubusercontent.com/58674365/120107982-1f5b4200-c19e-11eb-818e-6580a8e83e77.png" alt="image" style="zoom: 100%;" />



노드 설정에서 노드의 타입 및 체크 사항은 다음과 같다. 

![image](https://user-images.githubusercontent.com/58674365/120593550-a2b8b400-c47a-11eb-83b9-a6915d60832e.png)

- **Standard node**<br>
  특정한 목적의 노드(lock, property... 등 값 가지는 경우)가 아닌 대부분의 경우 해당 노드에 속한다.
- **Locking node** <br>
  모든 standard node에는 locking handling을 위한 sub node가 존재하며, lock 값을 저장한다.
- **Property node** <br>
  수정 가능한지의 여부 즉, property 값을 저장한다. 
- **Standard node Cat.** <br>
  해당 노드가 루트 노드인지 아닌지를 정한다. 



- **Node can be loaded seperately** <br>
  명시적으로 request 할 때만 node를 로드하거나 아니면 root node를 로드할 때 전부 로드할지를 정한다.

- **Node can be loacked seperately** <br>
  parent node가 lock되었을 때, child node도 lock 하는지 여부를 정한다. 

- **Transient node** <br>
  DB에 저장될 필요 없는 temporary 계산 등에 사용된다. 이 노드는 runtime 시에만 활성화 된다. 

- **Subtree properties used** <br>
  subtree node의 properties를 정의하는 경우, parents node 변경시 child node는 어떻게 할지 등에 대한 정의이나 잘 사용되지는 않는다. 

- **Node can be enhanced** <br>

  주로 체크한다. 다른 사람에 의해 해당 노드가 enhance될 수 있는지 여부를 정하며, 체크되지 않으면 BO enhancement로 해당 node를 enhance 할 수 없다.

<br>

해당 노드의 데이터 모델 설정은 다음과 같다. 

![image](https://user-images.githubusercontent.com/58674365/120593608-b49a5700-c47a-11eb-988e-2bbad12b528a.png)

- **Data structure** <br>
  해당 노드의 모든 attribute를 정의한다. 이는 DB structure와 유사하나 DB key, client number가 없다는 차이점이 있다. 각 노드는 persistent fields를 가진 internal data structure를 가진다.
- **Transient structure** <br>
  해당 노드의 transient fields를 포함한 data structure이다.
  (transient fields는 DB에 값이 저장되지 않고 buffer에만 존재하는 필드)
- **Combined structure** <br>
  framework에 의해 만들어지는 structure로써 추가적인 technical key를 가진다. 
- **Combined table type** <br>
  노드 인스턴스의 데이터를 저장하는 테이블 형태

![image](https://user-images.githubusercontent.com/58674365/120598745-9e43c980-c481-11eb-9c3e-a5df844dde3c.png)

<br>

## 4. Core BOPF components

>  BOPF에는 다음의 다양한 components가 있다. 상세한 내용은 뒤에서 개별 문서로 다루도록 한다. 

- **Node**
  - Business object의 데이터를 모델링하기 위해 사용
  - 계층구조를 이룸
  - 다양한 노드 타입이 있지만, `persistent node` 를 주로 사용
  - 각 노드는 노드 내에 저장되는 데이터의 타입을 정의하는 1개 이상의 속성으로 구성됨
- **Determination**
  - 이벤트 및 이벤트 핸들러
  - BO 인스턴스 변경에 의해 야기되는 내부적 이벤트 (나비효과로 BO 인스턴스 변경) 
- **Action** 
  - 노드에서 수행하는 일반적인 서비스
  - 사용자에 의해 명시적으로 발생하는 BO 인스턴스 변경
- **Assosication** 
  - 노드 간 관계
  - 두 노드에 존재하는 인스턴스 연결
- **Query** 
  - 데이터 접근
  - 특정 조회 기준을 만족하는 BO 조회
- **Validation** 
  - 노드에 대한 유효성 및 액션 체크
  - BO 인스턴스에 대한 어떤 변경도 발생 X



## 5. Transaction in BOPF

Transaction은 BOPF 모델에서 수행할 모든 작업과 변경사항을 포함한다. 개별 작업들이 진행되는 흐름으로 보면 된다. 그리고 이를 위해 manager가 필요로 된다. 

![image](https://user-images.githubusercontent.com/58674365/120097663-ce7f2580-c16c-11eb-854f-d94c61354fe2.png)

`https://software-heroes.com/en/blog/abap-bopf-manager-en`



### Transaction manager

- 변경된 데이터를 데이터 베이스에 반영

`/BOBF/IF_TRA_TRANSACTION_MGR` 는 transactional change에 대한 부분을 담당한다. BOPF 모델에 대한 모든 변경 사항을 모니터링 및 수집하고 LUW를 형성하여 데이터의 일관성을 유지한다. manager는 전체 transaction의 전반적 life cycle을 담당하며, 하나의 transaction에 대해 하나의 transaction manager가 있다. 예를 들어, 변경 사항이 일관적이지 않은 경우의  rollback 기능을 수행하는 것 등이 있다. 중요한 부분은 바로 TM에서는 이러한 Rollback,  Save 등의 transaction을 transaction manager를 사용해서 해야한다는 것이다. 



### Service manager

- 데이터를 변경

`/BOBF/IF_TRA_SERVICE_MANAGER` 는 노드에 대한 접근 및 컴포넌트에 대한 컨트롤을 위한 메소드를 제공한다. 하나의 BO에는 하나의 service manager가 있다. 일종의 transaction manager를 돕는 역할을 하며, 다음의 주요 메소드를 제공한다.

| 메소드                  | 설명                                           |
| ----------------------- | ---------------------------------------------- |
| QUERY                   | BO query를 찾고 실행함                         |
| RETRIEVE                | 주어진 노드 인스턴스 키를 가지고 데이터를 읽음 |
| RETRIEVE_BY_ASSICIATION | association을 가지고 데이터를 읽음             |
| DO_ACTION               | BO 노드의 주어진 action을 실행함               |
| CONVERT_ALTERN_KEY      | alternative key를 technical key로 변환함       |
| MODIFY                  | BO 노드 인스턴스를 생성, 수정, 삭제함          |

<br>

## 6. BOPF test tool : T-BOBT

`T-BOBT` 에서 생성한 business object에 대해 테스트해볼 수 있다. 마찬가지로 데이터를 보기 위해 Web UI를 열 필요 없고 `T-BOBT` 에서 찾아보면 된다. 

![image](https://user-images.githubusercontent.com/58674365/120593721-da276080-c47a-11eb-8f63-3096876ea8b9.png)



작업하고픈 business object에 대해 데이터를 조회할 때, query, key, alternative key로 조회 가능하다.  

![image](https://user-images.githubusercontent.com/58674365/120593763-e6abb900-c47a-11eb-9570-caf41ed5c23b.png)



예를 들어 키 값으로 데이터를 조회하는 경우, 다음과 같이 해당 키 값에 대한 데이터를 확인할 수 있다. 
조회된 데이터에 대해서는 Lock, Action, Association 실행이 가능하다. 

![image](https://user-images.githubusercontent.com/58674365/120098798-d8a42280-c172-11eb-81c3-bb1e2c45d281.png)

![image](https://user-images.githubusercontent.com/58674365/120593793-f4613e80-c47a-11eb-8eb8-b86c1d490732.png)



### Lock 

![image](https://user-images.githubusercontent.com/58674365/120098776-c1653500-c172-11eb-9a71-0c3ed3bd154c.png)

사용자가 UI application에 가서 열 때 마다 optimistic lock이 적용된다. Optimistic lock 이 적용되면 여러 사용자가  UI를 edit mode로 열 수 있다. 그러나 특정 사용자가 data change를 시작하면 해당 UI를 실행한 다른 사용자는 optimistic lock을 잃게 된다. Data change 한 사용자가 exclusive lock을 가지게 되기 때문이다. Exclusive lock은 한 사용자만이 가질 수 있다. 타 사용자는 데이터 변경에 대한 notificaition을 받고 다시 시작하라는 말을 받게 된다. 즉, optimistic lock은 여러개 동시에 열릴 수 있지만, exclusive lcok은 하나의 인스턴스에 대해 하나만 가능하다.



### Action

![image](https://user-images.githubusercontent.com/58674365/120098725-85ca6b00-c172-11eb-9b76-1cacf7bab976.png)

![image](https://user-images.githubusercontent.com/58674365/120098741-9aa6fe80-c172-11eb-9286-acb3f11247ad.png)



Lock 옆의 아이콘을 누르면 해당 데이터에 대해 action 실행이 가능하다. 여기서 보이는 action 이름은 technical 이름으로 실제 UI에서 보이는 이름과 조금씩 다를 수 있다. 어떤 action은 실행 불가하여 회색으로 표시되는데, 특정 property에 의해 금지되었음을 의미한다. 각 노드는 property 값을 이용해서 해당 인스턴스가 modify 가능한지 아닌지 확인한다. 

데이터를 추가하거나 action을 실행하는 등 데이터에 대한 변경이 발생한 후 save 누르면 transaction manager가 실행되면 데이터 베이스에 변경 사항이 저장된다. 
