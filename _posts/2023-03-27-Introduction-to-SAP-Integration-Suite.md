---
title: "Introduction to SAP Integration Suite"
categories: 
  - SAP
tags:
  - SAP
  - BTP
  - Integration Suite
toc: true
---

## 1. Integration Suite & Process Orchestration

Integration Suite와 Process Orchestration의 차이는 아래와 같다. 

- **SAP Integration Suite**
  - SAP Cloud, 3rd Party Cloud, On-premise 간의 연결
  - 개발과 모니터링은 SAP Integration Suite에서 클라우드 기반으로 하되 경우에 따라 On-Premise로 배포도 가능

- **SAP Process Orchestration**
  - On-Premise, 내부 시스템 간의 연결



## 2. Business Integration Suite

SAP의 4가지 주요 통합 원칙을 따르는 `EiPaaS (Enterprise Integration Platform as a Service) `는 SAP에서 관리하는 툴, 사전 구축된 콘텐츠, 온프레미스 및 클라우드 기반 애플리케이션과 프로세스를 통합하는 기능을 제공한다. 

- **Business Integration Suite 기능**
  - Cloud Integration
    - EDI, 어플리케이션 간의 연결, 신규 인터페이스 개발 또는 기존재 인터페이스 연결
  - Integration Advisor
    - B2B 시나리오에서 주로 사용됨, EDI 라는 산업 표준에 맞춰 비즈니스 거래를 연결하는 인터페이스
    - 비즈니스 산업에 따라 어떤 메세지 유형, 구조, 연결을 사용하는지를 추천함
  - Message Queues
    - Cloud Integration이 받아온 메시지를 저장하여 비동기로 처리
  - API Management
    - API 중심으로 통합할 때의 관리 지원
  - Open Connectors
    - 3rd party 솔루션을 쉽게 붙이기 위해 제공되는 커넥터

- **그 외의 기능**

  - SAP API Business Hub 
    - SAP 제공 API, 미리 만들어 놓은 통합 패키지

  - Cloud Connector
    - SAP BTP PaaS를 위한 BTP로 NonSAP과 On-Premise와 Public을 연결하는 커넥터



## 3. Integration Suite 연결 기술

SAP 및 다른 솔루션들은 api를 제공하고 이를 가지고 연결, 개발을 진행한다. 하지만 사용하는 패턴(메시지 처리, 오류 처리, 이벤트 처리 등)이 다르기 때문에 어떻게 사용할지를 습득하는 과정이 필요하다. 이렇게 다른 연결 방법을 가진 솔루션들을 쉽게 연결하기 위한 connector를 제공하는데 이 것이 바로 아래 내용 중 Open connector에 해당된다. 

아래 항목 외에도 추가 개발 및 도입도 가능하며 200개 이상의 연결 기술을 제공하고 있다.

- **Open spec connector**<br>ex. HTTP, HTTPS, SOAP, FTP, Mail, OData, IDoc ... 
- **Connectors to SAP & 3rd party apps**<br>ex. AWS, Ariba, Microsoft Synamics, Salesforce ...
- **Open connector**<br>ex. Cloud storage, CRM, Collaboration, ERP, ECommerce ...



## 4. Integration Suite 통합 기술

SAP Integration Suite에서는 아래와 같은 기술적 통합을 지원한다. 

- **A2A 통합** <br>: 가장 많이 사용되는 경우로 비즈니스 어플리케이션 간 비즈니스 프로세스 단계를 연결
- **B2B 통합**<br>: 서로 다른 프로토콜을 활용하는 비즈니스 파트너 간의 비즈니스 프로세스 단계를 연결하며 AI/ML 기반의 통합을 지원 
- **B2G 통합**<br>: 국가 기관과의 연결을 지원 
- **API 관리형 통합**<br>: EDI가 아닌 API로 직접 통신하는 경우 중앙에서 통제 및 사용량 모니터링 등 지원
- **이벤트 기반 통합** <br>: 이벤트를 따로 관리하여 인터페이스를 분리 관리하도록 지원
