---
title: "Advanced Event Mesh"
categories: 
  - SAP
tags:
  - SAP
  - BTP
  - Integration Suite
  - Event Mesh
toc: true
---

## 1. Advanced Event Mesh 

SAP Integration Suite, Advanced Event Mesh는 기업의 이벤트 기반 아키텍처를 지원하기 위해 이벤트 스트리밍 및 이벤트 관리 서비스를 제공하는 솔루션으로 아래의 기능을 제공한다. 

- 다양한 클라우드 및 온프레미스 환경에 이벤트 브로커를 구축하고 이벤트 메시 형성 가능
- 다이나믹 메시지 라우팅을 통해 브로커들에 연결된 어플리케이션 및 기기들에 자동으로 이벤트 배포
- 토픽 및 세분화된 필터링을 통해 필요한 이벤트 선택 후 구독 및 배포 가능
- 수평 및 수직적 확장 가능 
- 다양한 환경에서의 이벤트 검색, 카탈로그, 디자인, 관리 및 시각화 기능
- 이벤트 API 프로덕트 제공



SAP 통합 솔루션 내에서 Advanced Event Mesh 솔루션이 포지셔닝 되어 있는 부분은 아래와 같다. 실제 SAP Integration Suite 내에 포함되어 있지는 않지만, 이벤트 기반의 시나리오 요구가 생기면 SAP Integration Suite와 연계하여 사용된다. 



![image-20230330141613402](C:\Users\Metanet\Desktop\img\image-20230330141613402.png)



현재 주로 사용되어 있는 연계는 아래와 같으며, 어플리케이션 증가에 따라 아키텍쳐의 복잡도가 증가하게 된다. 아키텍쳐가 복잡해질수록 신규 확장과 유지보수에 어려움이 생기게 된다. 



![image-20230330141800957](C:\Users\Metanet\Desktop\img\image-20230330141800957.png)



이벤트 브로커 사용을 통해 이러한 문제를 해결할 수 있다.  

![image-20230330141954157](C:\Users\Metanet\Desktop\img\image-20230330141954157.png)



## 2. Event, Event Broker

Event와 Event Broker를 정의하자면 다음과 같다. 

- Event
  - 상태(State)의 변화를 의미
  - 이벤트 발생 시, 이벤트 소스에서 이벤트 프로커로 이벤트가 전달됨
- Event Broker
  - 사전에 해당 이벤트를 구독한 이벤트 소비자들에게 이벤트를 전달함



이벤트 소스와 이벤트 메시가 연결되는 방법, 그리고 이벤트 종류는 다양하지만 SAP 기반 이벤트 소스에서 이벤트 종류는 크게 2 종류로 나눠진다. 

- Add-on : 고객에 맞춰 이벤트를 생성
- Standard : SAP에서 사전에 생성 및 배포한 이벤트



## 3. EDA 장단점

이벤트 기반의 아키텍쳐 즉, `EDA (Event Driven Architecture)` 는 분산된 시스템에서 이벤트를 관리(생성)하고 해당 이벤트를 수신자에게 전송하여 각 수신자가 그 이벤트를 처리하는 구조다. 이는 분산 아키텍쳐 환경에서 상호 서비스 간 결합도를 낮추기 위해 비동기 방식으로 메시지를 전달하는 패턴이다. 

EDA에 대하여 상세한 설명은 다음 블로그를 참조하자. [EDA이란?][https://akasai.space/architecture/about_event_driven_architecture/]

### 장점

- 루즈 커플링(Loose Coupling)을 통해 높은 유연성 및 확장성
- 실시간 데이터 전달을 통해 시스템 및 어플리케이션 간 데이터 동기화
- 다양한 IT 환경 및 시스템 통합(하이브리드 및 멀티-클라우드 구성)
- 이벤트 소비자와 이벤트 소스를 단계적으로 추가함으로 간편한 운영

### 단점

- 루즈 커플링 및 분리된 환경을 사용함으로써 모니터링, 추적 및 디버깅이 상대적으로 어려울 수 있음



## 4. AEM 사용자 콘솔

하나의 사용자 콘솔에서 이벤트 스트리밍, 관리, 정의, 이벤트 브로커 및 이벤트 메시 구성 모든 기능을 제공한다. 



![image-20230330145053259](C:\Users\Metanet\Desktop\img\image-20230330145053259.png)



### Event Portal - Event Management

EDA 관리, 디자인 및 가시화를 포함하여 EDA 관리를 지원한다. 

- Event Design

  어떤 이벤트 소스가 있고, 어떻게 연결되어 정보가 주고받아지는지 확인 가능

- Event Discovery

  정의하지 않은 이벤트라고 하더라도 찾아올 수 있음

- Event Catalog

  찾은 이벤트를 포함하여 전체 이벤트에 하여 확인 가능

- Event API Product

  직접 API 생성하여사용 가능



![img](C:\Users\Metanet\Desktop\img\SNAGHTMLf1dbf17-1680156099972-2.PNG)



### Event Portal - Mission Control 

Cluster Manager 및 Mesh Manager를 통해 이벤트 브로커 및 메시 생성과 관리를 지원한다.

- Cluster Manager

  생성된 Broker에 대해 정보 확인, 관리는 물론 버튼 클릭을 통해 새로 생성도 가능

- Mesh Manager

  Event broker를 연결하여 하나의 broker로 일할 수 있도록 mesh를 생성 및 관리



![image-20230330150150275](C:\Users\Metanet\Desktop\img\image-20230330150150275.png)



### Event Portal - Insight

사용 중인 이벤트 브로커 및 메시가 최상 상태로 유지될 수 있도록 모니터링 기능을 제공한다. 



![image-20230330150157115](C:\Users\Metanet\Desktop\img\image-20230330150157115.png)



