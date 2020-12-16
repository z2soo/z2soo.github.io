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

  - 중복된 값이 있는 경우 자동으로 한 번만 받아오기 때문에 사용 전 SORT, 중복제거 실행
  - `SELECT * `이 아닌  중복되지 않을 필드 설정
- 비교 구분 불가

  - DB 기반의 Loop 이기 때문에 EQ만 가능하며, LIKE, BETWEEN, IN 비교 구문 불가
  - 마찬가지로 WHERE 조건에서 EQ 사용시, 타입도 동일해야 함
- 모수 테이블 값 확인

  - 기준이 되는 테이블에 데이터가 없는 상태(INITIAL)면 FULL SCAN을 하여 덤프 발생
  -  `IF ~ IN NOT INITIAL` 로 체크해주기
- 집계함수 사용 불가 

S/HANA에서 for all entries in을 지양하고, Loop 안에서 SELECT 하라는 것이 SAP의  가이드지만, 현업에서는 for all entries in 사용이 편리하다고 본다. Loop에서 돌리는 것보다 빠르기 때문이다. Join이 가능한 경우는 join을 하는 것이 더 빠르지만, 불가능한 경우에 for all entries in을 사용한다. For all entries in 예시 코드는 다음과 같으니 참고하자. 

```sql

  SELECT  A~VBELN, A~ERDAT,
          B~POSNR, B~KWMENG, B~VRKME
    INTO CORRESPONDING FIELDS OF TABLE @GT_DATA
    FROM VBAK AS A INNER JOIN VBAP AS B ON B~VBELN EQ A~VBELN
    WHERE A~VBELN IN @S_VBELN
    AND A~ERDAT   IN @S_ERDAT.
* GT_DATA에 BSTKD, BSTKD_E 값이 아직 채워지지 않은 상태

  LT_DATA = GT_DATA.

* 중복제거
* DELETE ADJACENT DUPLICATES FROM 테이블명 COMPARING 기준필드
* SORT 작업 필수

  SORT LT_DATA BY VBELN.
  DELETE ADJACENT DUPLICATES FROM LT_DATA COMPARING VBELN.
* GT_DATA와 동일 데이터 가진 테이블 생성 및 문서번호로 정렬, 중복제거
* 동일 구매문서 번호를 가지면 동일 BSTKD, BSTKD_E 값을 가질 것
* DB에서 BSTKD, BSTKD_E 값을 가져오기 위한 기준 테이블을 생성하는 작업으로 이해

* FOR ALL ENTRIES IN
* 모수 테이블 확인 필수: FULL SCAN 방지
* 중복된 값은 하나만 들어가기 때문에 이전에 SORT, 중복제거 필수

  IF LT_DATA IS NOT INITIAL.
    DATA: LT_VBKD TYPE TABLE OF VBKD.

    SELECT *
      INTO CORRESPONDING FIELDS OF TABLE LT_VBKD
      FROM VBKD
      FOR ALL ENTRIES IN LT_DATA
      WHERE VBELN EQ LT_DATA-VBELN.
*     위에서 생성한 LT_DATA에 정보가 있는 것의 값만 LT_VBKD로 받음(BSTKD, BSTKD_E)

    SORT LT_VBKD BY VBELN POSNR.
   ENDIF.

    LOOP AT GT_DATA ASSIGNING FIELD-SYMBOL(<LS_DATA>).
*	BSTKD, BSTKD_E 값이 비어있는 GT_DATA 읽어옴

      READ TABLE LT_VBKD WITH KEY VBELN = <LS_DATA>-VBELN
                                  POSNR = <LS_DATA>-POSNR
                         BINARY SEARCH
                         INTO DATA(LS_VBKD).
* 코드 1
*        IF SY-SUBRC = 0.
*          <LS_DATA>-BSTKD    = LS_VBKD-BSTDK.
*          <LS_dATA>-BSTKD_E  = LS_VBKD-BSTKD_E.
* 		   BSTKD, BSTKD_E 값을 가진 LT_VBKD 읽어와 GT_DATA에 넣어줌: 품목번호가 있는 경우
*
*        ELSE.
*          READ TABLE LT_VBKD WITH KEY VBELN = <LS_dATA>-VBELN
*                             BINARY SEARCH
*                             INTO LS_VBKD.
*          IF SY-SUBRC = 0.
*            <LS_DATA>-BSTKD    = LS_VBKD-BSTDK.
*            <LS_dATA>-BSTKD_E  = LS_VBKD-BSTKD_E.
*          ENDIF.
*          BSTKD, BSTKD_E 값을 가진 LT_VBKD 읽어와 GT_DATA에 넣어줌: 품목번호가 없는 경우
*
*        ENDIF.

* 코드 2: 중복 코드 정리
        IF SY-SUBRC <> 0.
          CLEAR LS_VBKD.
          READ TABLE LT_VBKD WITH KEY VBELN = <LS_dATA>-VBELN
                             BINARY SEARCH
                             INTO LS_VBKD.
        ENDIF.

        <LS_DATA>-BSTKD = LS_VBKD-BSTKD.
        <LS_DATA>-BSTKD_E = LS_VBKD-BSTKD_E.
*       위 코드의 SY-SUBRC = 0 부분을 IF절 밖으로 빼서 언제나 실행되게 함

* WORK AREA를 따로 사용해서 값을 변경한 경우 'MODUFY 테이블명 FROM WA명' 실행 해줘야 함
* 그러나 FIELD SYMBOL은 직접 테이블을 건드는 것이기에 추가 작업 불필요


* COLLECT
* ITAB의 숫자 또는 금액 필드의 값을 합을 구할 때 사용
* 특정 필드만을 집계 할 수가 없으며 ITAB의 데이터가 완전히 동일해야 사용이 가능하다.

*   CLEAR LT_DATA.
*   LOOP AT GT_DATA INTO GS_DATA.
*     LS_DATA-VBELN   = GS_DATA-VBELN.
*     LS_DATA-KWMENG  = GS_DATA-KWMENG.
*     COLLECT LS_DATA INTO LT_DATA.
*   ENDLOOP.
	ENDLOOP.
```

