---
title: "2020-12-16-ABAP-Tips-for-development"
categories: 
  - ABAP
tags:
  - sap
  - abap
  - tips
  - excel
  - read table
  - for all entries
toc: true
---

## 1. Tips for ABAP Development

- report상단에 프로그램 개발에 대한 정보 넣기

- 코드에 대한 주석 달기: 유지보수 및 본인 코드 확인에 용이

- 프로그램을 타는 순서 잘 알기 

  - initialization: transaction 실행 프로그램 시작과 다시 1000번 화면으로 올때(1000번 화면이 보여지기 전)

  - at selection screen output: pbo, 화면 출력 직전 타는 프로세스

  - at selection screen: pai, 화면에 값이 입력되었을 때 타는 프로세스

    get data에서 pai를 잘 활용하면 동적 쿼리를 사용하지 않고 효율적으로 짤 수 있음
    input 직후의 프로세스

- tables 선언하면 광역 변수가 선언되는데 프로그램 이해에 힘들어서 지양하는 편

  - select options에서 사용하려고 tables를 선언하는데 그냥 top의 internal table을 참조하는 편

- selection screen

  - order 번호는 extension 없애고 interval 남김
  - 날짜는 extension 들어가면 로직 구현이 힘들어서 개발자를 위해 없앰

- types 생성 활용

  - form get_data_init tables pt_data라고 써있는데 이때 pt_data가 광역변수로 되어 있어야지 perform 내에서 인식 가능. 그러나 광역변수 선언 지양하기 떄문에 이때 types를 선언해두었다면 이를 통해 해당 구조를 form 안에서 활용할 수 있음. perfom을 통해 tables를 던지면 form 내부에 선언되는 table은 header line을 가진 table이 생성됨.(sap가이드는 header line을 지양하지만 이러한 아이러니가 존재)
  - perform에서 tables로 넘겨주는 것은 데이터 담고자 하는 table이 지역변수이기 때문.
  - 이때 perform 내에 넘겨진 테이블 뒤에 어떤 structure를 참조하는지 명시적으로 선언해주지 않으면 넘겨진 테이블에 어떤 필드가 있는지 프로그램상 인지를 못하기 떄문에 꼭! structure 참조를 달아주도록 한다.

- perform 이후에 (for all entries in전) 모수데이터가 있는지 check 하는 센스

  - check하여 모수데이터가 없다면 이하 perform을 타지 않을 것

- for all entries in 할 때, 데이터를 모으고 있는 최종 테이블을 건들이지 말고 더미 테이블을 만들어서 모수 테이블로 사용하도록 한다.

- 테이블 값을 가져올 때 언어키에 유의

  - spras eq sy-lang 조건을 넣어주기

- table대 table로 같은 필드 값이 있는 값 바로 옮기기

  - move-corresponding 테이블1 to 테이블2
  - 만약 header가 있는 테이블이라면 [] body 표시 해주기

```sql
* 올드한 방식
  DATA: LT_DATA TYPE GTY_T_DATA.

  LT_DATA = GT_DATA.

  SORT LT_DATA BY MATNR.
  DELETE ADJACENT DUPLICATES FROM LT_DATA COMPARING MATNR.

  IF LT_DATA IS NOT INITIAL.
    SELECT *
      INTO TABLE PT_MAKT
      FROM MAKT
       FOR ALL ENTRIES IN LT_DATA
     WHERE MATNR EQ LT_DATA-MATNR
       AND SPRAS EQ SY-LANGU.

  ENDIF.
  
* 새로운 방식
*  CLEAR: PT_MAKT, PT_MAKT[].

  MOVE-CORRESPONDING GT_DATA TO PT_MAKT[] KEEPING TARGET LINES.

  SORT PT_MAKT BY MATNR.
  DELETE ADJACENT DUPLICATES FROM PT_MAKT COMPARING MATNR.

  IF PT_MAKT IS NOT INITIAL.

    SELECT *
      INTO TABLE PT_MAKT
* 			     for all entries in와 동일한 테이블을 동시에 사용한다?
*			     into tables는 무조건 대상 table을 clear 시킴
*                사실상 위의 clear가 불필요하다는 점
      FROM MAKT
       FOR ALL ENTRIES IN PT_MAKT
     WHERE MATNR EQ PT_MAKT-MATNR
       AND SPRAS EQ SY-LANGU.

  ENDIF.

  SORT PT_MAKT BY MATNR.
```

- loop를 돌 때는 index 번호를 가지고 있어서 modify가 되지만, transporting을 활용해서 특정 필드만 modify 가능. 물론 더 효율적인 방법은 ASSIGN FIELD SYMBOL을 활용하는 것. 굳이 MODIFY 하지 않아도 되기 떄문
- ALV는 일반적으로 TEMPLATE 사용, 핵심은 DATA HANDLING
- MERGE FUNCTION 사용시 덤프, BACKGROUD 돌 때 덤프 난다는 점
  - 이 중 백그라운드 작업 덤프를 잡기 위해 SY-BTCH IS INITAL 을 확인
  - 백그라운드 작업을 돌면 0이 들어온다 (1인가..?)



- custom보다 docking container 활용
- 