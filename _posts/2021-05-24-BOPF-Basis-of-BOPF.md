---
title: "BOPF: Basis of BOPF"
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

# 1. Business Object Process Framework basis

## BOPF란?

Business object processing frame work, 즉 ABAP OO 기반의 프레임워크로 개발의 속도 향상, 표준화, 모듈화가 가능한 서비스와 기능을 제공한다. 이를 통해 개발자는 인프라 개발보다 로직 구현에 더 집중할 수 있게 된다.



## BOPF 장점?

- rapid prototyping
- reusability
- less implementation
- more stable

다양한 비즈니스 어플리케이션 구성요소를 원할하게 통합할 수 있는 프레임워크로 쉽게 구축할 수 있는 장점을 지닌다. 아래 이미지처럼 BOPF는 다양한 구성요소 사이에서의 징검다리 역할을 한다. 

![image](https://user-images.githubusercontent.com/58674365/120150322-e1096580-c225-11eb-9e48-32a781ac72a7.png)
`https://blogs.sap.com/2013/01/04/navigating-the-bopf-part-1-getting-started/`



## Business object

sales order, purchase order와 같은 하나의 비즈니스 단위를 의미한다. 이는 BOPF에 생성될 것이며, 각 BO의 entity는 node형태로 정의된다. 정의된 노드는 트리구조(계층구조)로 이루어진다.



## Business object components

- Node
  : business object의 data modeling을 위해 사용되며 데이터 타입 등을 정의하는 속성을 가짐
- Action
  : business object의 서비스 또는 행위를 정의하며 각 노드에 assign됨
- Association
  : business object 간의 관계 혹 통합을 의미
- Determination
  : business object node의 element로서 특적 조건 충족시 실행하는 internal changing logic
- Validation
  : business object node의 element로서 internal checking 즉 유효성 체크를 할 뿐 데이터 변경x
- Query
  : 특정 조건에 맞는 business node를 조호



## Basic architecture of BOPF

- Consumer layer / UI layer
  : 클라이언트는 BOPF의 객체지향 API를 활용해 business object에 접근 가능함
- Transaction layer
  : business object node의 변경 사항에 대한 commit, rollback... 등 DB에 반영하는 transaction 처리를 함
- Runtime layer
  : business object를 인스턴스화하고, life cycle 이벤트 처리를 하고, 클라이언트 수준의 상호작용에 응답하는데 필요한 작업을 수행함
- Persistence layer
  : business object 인스턴스를 DB에 유지하는데 필요한 기능을 제공함
  (persistence <> transient: runtime 시에만 데이터를 유지하고 DB에 반영하지는 않음)

![image](https://user-images.githubusercontent.com/58674365/120150358-ee265480-c225-11eb-9fe6-54d2e575b2f4.png)

`https://mysapblogs.com/2017/03/28/business-object-processing-framework-bopf-introductionarchitecture/`



## Service & Transaction manager API

Business object는 service API를 통해서만 접근이 가능하고, business ibject에 대한 행위는 transaction manager API를 통해서만 접근이 가능하다. 두 개를 통하지 않으면 business object에 어떤 작업도 불가하다.

- Service manager 
  : business object 생성, 업데이트 등 business object node 값에 영향을 미치는 작업 수행
- Transaction layer
  : commit, rollback 등 변경된 business object node 값을 DB에 반영하는 작업 수행

![image](https://user-images.githubusercontent.com/58674365/120150382-f5e5f900-c225-11eb-8e41-7fb70204b185.png)

`https://www.youtube.com/watch?v=Exk_JZGasWE`

<Br>

# 2. Node

## Node란?

노드 기반의 모델링 과정은 객체지향 프로그래밍과 동일하다. 간단히 말해 노드를 하나의 클래스 정의로 생각하자. 노드는 상황에 따라 다른 의미를 가진다.

- Design time
  : business object의 개별적인 부분의 데이터와 행위를 모델링하기 위해 사용
    ex. Sales Order business object에 대한 ITEM 노드는 데이터, 행위, 다른 sales order와 관계 등 정의
- Runtime
  : runtime의 node는 컨테이너로써 internal table을 생각하면 되며 node row라고 불림

![image](https://user-images.githubusercontent.com/58674365/120249857-32ae0080-c2b7-11eb-89cd-d7e92b72bad1.png)

`https://blogs.sap.com/2013/01/04/navigating-the-bopf-part-2-business-object-overview/`

![image](https://user-images.githubusercontent.com/58674365/120249787-f8dcfa00-c2b6-11eb-93a4-32f685389445.png)

`https://saphack.wordpress.com/2015/09/17/business-object-processing-framework-bopf/`



## Node and Attribute Types

데이터 모델링 관점에서 노드와 노드의 속성은 두 개의 다른 카테고리로 구분된다.

- persistent nodes/attributes
  : 시스템 데이터 베이스에 대해 영구적(persistent)이며, 데이터베이스 테이블과 매핑됨
- transient nodes/attributes
  : 검색 혹 계산을 위한 데이터 모델로써, runtime 시에만 존재하고 데이터 베이스에 반영x

<br>

# 3. Action

node를 class로 생각한다면, node에 특정한 행위인 operations/methods를 action이라 정의한다.
단, 개념적으로 action과 OOP의 method가 비슷하지만, BOPF의 action의 범위가 더 크다. 대부분의 attributes level 수정사항이 standard API 메소드를 통해 이루어지기 때문이다. 그 결과, BOPF 노드 정의에서 getter/setter 메소드가 필요없고 보여지지도 않는다. 대신, BOPF action은 large-scale business operations를 위해 활용된다.

예를 들어, 하나 이상의 sales order에 영향을 주는 delivery라는 action은 다른 node attribute의 값에 영향을 주는 것은 물론 이로 인해 trigger를 야기할 수 있다. 

<Br>

# 4. Determination

business object 인스턴스의 lifecycle 동안 반응이 필요한 event이 있고, 이를 위해 BOPF object model은 determination이라는 것을 제공한다. 데이터 베이스 trigger와 마찬가지로 determination은 실행 조건을 충족하면 BOPF에 의해 자동적으로 수행된다.

<Br>

# 5. Validation

validation 규칙에 대해 개별적으로 로직을 짜기보다 BOPF는 validation rule을 그룹화하도록 한다.  이렇게 그룹화된 validation으로 business object에 대해 유효성 체크가 가능하다. 특정 시점에서 BOPF이 validation을 자동적으로 수행하고 불균등한 데이터를 막는다. 

<br>

# 6. Association

business object를 수행할 때, node가 다른 node와 어떻게 연관되어 있는지 알아야 한다. 이러한 연관 관계를 association이라 한다. 

<Br>

# 7. Query

business object/node를 찾는 로직으로써 BOPF object model에는 두 타입의 쿼리가 있다.

- node attributes queries
  : logic이 BOPF runtime 내에서 정의된 쿼리로써, 
   해당 쿼리를 implement하기 위해 어떠한 custom coding도 필요로 되지 않음
- custom queries
  : 복잡한 쿼리를 핸들링하기 위해 사용되며, custom ABAP object class에 정의됨

<br>

# 8. BOPF Client API

어떻게 business object의 인스턴스를 생성하고 ABAP 프로그램 내에서 조작할 수 있을까?
SAP는 object-oriented API를 제공하여 이를 가능케 한다. 
BOPF API는 크게 3 개의 주요 object type으로 나눠진다. 

- /BOPF/IF_TRA_SERVICE_MANAGER
  : node 정보에 대한 변경 
- /BOPF/IF_TRA_TRANSACTION_MGR
  : node 변경 정보를 DB에 반영, transactin 실행
- /BOPF/IF-FRW_CONFIGURATION
  : BOPF configuration 정보에 대한 접근



factory class를 통해 위의 세 object를 인스턴스화 한 후 제공되는 메소드를 실행할 수 있다. 

![image](https://user-images.githubusercontent.com/58674365/120149163-478d8400-c224-11eb-8ac2-3136d915a495.png)

`https://blogs.sap.com/2013/01/16/navigating-the-bopf-part-3-working-with-the-bopf-api/`

위의 factory method를 살펴보면, method는 business object key 값을 받도록 되어있다. 
business object key란 무엇일까?
이는 BOPF configuration 에서 business object의 metadata를 조회하는데 사용되는 키 값이다. 따라서, 만약 여러 business object를 가지고 작업을 한다면, 각 business object의 service/configuration manager instance를 분리할 필요가 있다. 

business key 값은 자동적으로 생성되는 값이나 쉽게 그 값을 알 수 없다. 따라서 각 business object는 이와 연관된 constants interface 를 가지고, 지속적으로 business object에 대한 정보를 자동 업데이트 한다.