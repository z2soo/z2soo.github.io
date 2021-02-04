---
title: "SAP MM Module Overview"
categories: 
  - Module
tags:
  - sap
  - mm module
  - material managements
  - module
toc: true
---

# MM Module Overview

> 회사의 모든 것을 녹아내서 관리하는 시스템이 ERP이며, 그 중 하나의 ERP Solution이 SAP라고 본다. 추후에 다루겠지만, SAP는 accounting document와 material document가 같이 생성되고 맞물린다는 것이 큰 장점이자 수요가 높은 이유이다. 




## 01. SAP 구성요소

SAP는 3개 구성으로 이루어진다. 

- Organization structure 
- Master Data
- Transaction Data



### Organizatin structire

Oraganizaiont structure는 4개 계층 구조를 가진다.

- Clint 최상위 > Company > Plant > Storage location

이 중 MM module은 storage, plant 부분을, FI module은 Company, Client 부분을 다룬다고 생각하면 된다. 
참고로 material은 plant level로 생성되고 sap에서 수익을 볼 수 있는 최소 단위는 plant 이다. 
만약 Storage location 보다 deep 하게 관리하고 싶다면, Warehouse module(WM)로 관리 가능하다. 



### Master data

땅이 Organization structure라면 이제 위에 골격 쌓는 단계이다. 
MM의 Master data는 다음과 같다.

- Vender master, Business partner
- Info record (구매단가)
- Material master (General master: 전 module에 연관)
- Source list

R3에서는 Vender와 Client가 나눠지지만, HANA에서는 Business partner로 통칭된다. 

Source list는 존재하지 않는 경우도 있다. 
Source list란 마지막 연결 데이터이다. 예를 들어 참치 공장에서 문제가 나서 더 이상 참치 발주를 하면 안되는 경우, 참치 마스터 소스리스트 uncheck 하면 발주하고 싶어도 발주 불가능하다. 이런 방식으로 관리가 용이하다. 



### Transaction data

Organization structure 와 Master data를 연결해주는 것이 바로 Transaction data이다.



## 2. MM Module 이해 

SAP 중 다음에 해당하는 module을 통틀어 Material Management Module이라고 한다. 

- SD  : Sales & Distribution
- MM: Material Management
- PP  : Production Planning