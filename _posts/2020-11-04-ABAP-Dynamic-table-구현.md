---
title: "ABAP Dynamic table 구현.md"
categories: 
  - ABAP
tags:
  - abap
  - abap study
  - sap
  - field catalog
  - dynamic table
toc: true
---

## 1. Dynamic table

Dynamic table method를 사용하여 개발자가 별도의 internal table을 선언하지 않고 구성할 수 있는 방법에 대해 알아보도록 한다. 즉, 따로 internal table을 사용하지 않아도 SAP에서 제공하는 class를 사용하여 사용자가 입력한 테이블명에 따라 internal table의 데이터를 담을 수 있게 해준다. 

Dynamic table을 구현하기 위해서는 다음 기능을 필요로 한다.

- Field symbol
- Class
  - CL_ALV_TABLE_CREATE
  - CL_ABAP_TYPEDESCR
  - CL_ABAP_STRUCTDESCR
- Field catalog
  - LVC_S_FCAT
  - LVC_T_FCAT

<br><br>

## 2. Dynamic table 구현





