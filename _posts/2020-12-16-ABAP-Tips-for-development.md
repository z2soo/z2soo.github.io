---
title: "2020-12-16-ABAP-Tips-for-development"
categories: 
  - ABAP
tags:
  - sap
  - abap
  - tips
  - excel
toc: true
---

## 1. Tips for ABAP

- report상단에 프로그램 개발에 대한 정보 넣기

- 프로그램을 타는 순서 잘 알기 

  - initialization: transaction 실행 프로그램 시작과 다시 1000번 화면으로 올때(1000번 화면이 보여지기 전)

  - at selection screen output: pbo, 화면 출력 직전 타는 프로세스

  - at selection screen: pai, 화면에 값이 입력되었을 때 타는 프로세스

    get data에서 pai를 잘 활용하면 동적 쿼리를 사용하지 않고 효율적으로 짤 수 있음
    input 직후의 프로세스

- tables 선언하면 광역 변수가 선언되는데 프로그램 이해에 힘들어서 지양하는 편

  - select options에서 사용하려고 tables를 선언하는데 그냥 top의 internal table을 참조하는 편

- types 생성 활용

  - form get_data_init tables pt_data라고 써있는데 이때 pt_data가 광역변수로 되어 있어야지 perform 내에서 인식 가능. 그러나 광역변수 선언 지양하기 떄문에 이때 types를 선언해두었다면 이를 통해 해당 구조를 form 안에서 활용할 수 있음. perfom을 통해 tables를 던지면 form 내부에 선언되는 table은 header line을 가진 table이 생성됨.(sap가이드는 header line을 지양하지만 이러한 아이러니가 존재)

- 

