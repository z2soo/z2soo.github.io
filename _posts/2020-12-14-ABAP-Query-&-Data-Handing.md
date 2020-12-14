---
title: "ABAP Query & Data Handing"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - for all entries
  - sql
toc: true
---

## 1. CTS

SAP 기본 데이터 사항은 single data 처리지만,  한국 SAP 환경은 multi 처리 기능을 많이 요구한다. 

실제 프로젝트에 투입되면 CTS 부분이 힘들게 느껴진다. Transport Organizer, 개발기-품질기-운영기 과정을 통해 실제 개발이 업데이트 되는데 (시스템 이관) 이 부분을 담당하는 부분이다. 이 기본 키가 되는 것이 CTS key로, 이 밑에 Task 통해 object를 묶어둔다. 이 때, 중요한 것은 순서라는 점 유의하자. 

**가급적이면 프로그램 별로 CTS를 따도록 한다.** 

<br><br>

## 2. ABAP Data

기본적인 사항 및 문법은 알고있다는 가정 하에 헷갈리는 부분에 대해서만 추가적으로 정리해보았다. 

- 데이터 선언시, TYPES와 DATA의 차이
  - types는 뼈대만 만들어둔 것, data는 실제 메모리를 할당하는 것
- Domain 선언시, sign 및 case-sensitive
  - sign은 음수를 구분한다는 것
  - case-sensitve는 lower 사용 못하게 설정

<br><br>

## 3. Internal Table

SAP에만 존재하는 구조로써 DB에서 가져온 데이터를 Header 및 Work area로 옮겨서 작업 후 다시 DB로 올리는 과정을 거친다. Internal table에 대해 잘 알고 작업하는 것이 중요하다. 다음과 같은 기본적인 명령어가 있고, COLLECT를 제외하고는 알고 있는 내용들이기에 이후 생략했다.

- INSERT
- APPEND
- COLLECT
  - CHAR 필드를 기준으로 숫자인 필드 값을 더해서 산출함
  - SQL 중 GROUP BY + SUM 혹 WINDOW 함수를 사용하는 것과 같은 원리
- READ 
  - WITH KEY
  - BINARY SEARCH : Key 값으로 sort 필수! 잘못하면 데이터가 어그러짐
- MODIFY
  - performance가 상당히 낮음
  - ASSIGNING FIELD-SYMBOL을 사용하면 바로 적용이 되기 때문에 performance 향상
- ...

<br>

## 4. For All Entries In

조인이 안되는 경우가 존재한다. 품목 번호 필드가 있는 테이블들을 조인하려 한다. 이 때, 품목 번호가 존재하지 않는 경우가 있는데, 이는 하나의 오더에 여러 품목이 아닌 하나의 품목 만을 넣었기 때문에 따로 존재하지 않아서 품목 번호로 조인이 불가능한 경우이다. 그런 경우 FOR_ALL_ENTREIS를 사용한다. 

중요한 것은 **사용 전, 다음을 유의하자!**

- 정렬 및 중복제거
- DB 기반의 Loop 이기 때문에 EQ만 가능하며, LIKE와 같은 구문 불가
- 비어있는 테이블로 돌리는 경우 메모리 덤프 발생

For all entries in 예시 코드는 다음과 같으니 참고하자. 

```sql
TABLES: VBAK.

TYPES: BEGIN OF GTY_S_DATA,
                 ERDAT TYPE VBAK-ERDAT,
                 VBELN TYPE VBAK-VBELN,
                 POSNR TYPE VBAP-POSNR,
                 KWMENG TYPE VBAP-KWMENG,
                 VRKME TYPE VBAP-VRKME,
                 BSTKD TYPE VBKD-BSTKD,
                 BSTKD_E TYPE VBKD-BSTKD_E,
            END OF GTY_S_DATA.

 TYPES: GTY_T_DATA TYPE TABLE OF GTY_S_DATA.

 DATA: GS_DATA TYPE GTY_S_DATA,
           GT_DATA TYPE GTY_T_DATA.
 DATA: LS_DATA TYPE GTY_S_DATA,
           LT_DATA TYPE GTY_T_DATA.

SELECT-OPTIONS: S_ERDAT FOR VBAK-ERDAT, S_VBELN FOR VBAK-VBELN.

START-OF-SELECTION.
  SELECT A~VBELN
         A~ERDAT
         B~POSNR
         B~KWMENG
         B~VRKME
    INTO CORRESPONDING FIELDS OF TABLE GT_DATA
    FROM VBAK AS A INNER JOIN VBAP AS B
              ON B~VBELN EQ A~VBELN
    WHERE A~VBELN IN S_VBELN
    AND   A~ERDAT IN S_ERDAT.

    LT_DATA = GT_DATA.

    SORT LT_DATA BY VBELN.
    DELETE ADJACENT DUPLICATES FROM LT_DATA COMPARING VBELN.

   IF LT_DATA IS  NOT INITIAL.
     DATA: LT_VBKD TYPE TABLE OF VBKD.
     SELECT *
       INTO TABLE LT_VBKD
       FROM VBKD
        FOR ALL ENTRIES IN LT_DATA
      WHERE VBELN EQ LT_DATA-VBELN.
   ENDIF.
```

