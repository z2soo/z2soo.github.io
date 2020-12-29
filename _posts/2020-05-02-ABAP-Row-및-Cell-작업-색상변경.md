---
title: "ABAP Row 및 Cell 작업: 색상변경"
categories: 
  - ABAP
tags:
  - sap
  - abap
  - cell color
  - row color
toc: true
---

## 1. ALV 생성 화면

> Smoker Field에 'X' 표시가 된 cell을 초록색으로 표시하고, Carreir Field의 값이 'UA'인 cell의 글자 색상을 변경해보도록 한다. 

### Selection Screen

![image](https://user-images.githubusercontent.com/58674365/103228079-4bfef980-4973-11eb-823d-416c95f2334c.png)<br>

### Screen 0100

![image](https://user-images.githubusercontent.com/58674365/103228124-676a0480-4973-11eb-80fe-7acd9c837c7a.png)

<Br><br>

## 2. ALV 생성 코드

코드는 주석에 쓰인 순서를 참조하여 보도록 한다. 

### REPORT PROGRAM

```mysql
*&---------------------------------------------------------------------*
*& Report Z_ALV_FLIGHT_24
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT ZALV24_02.

INCLUDE ZALV_02_TOP.
INCLUDE ZALV_02_B01.
INCLUDE ZALV_02_I01.
INCLUDE ZALV_02_F01.

INITIALIZATION.

AT SELECTION-SCREEN.

START-OF-SELECTION.

* 9. 읽어오는 테이블 변경: SCARR
* <기존>
*  SELECT *
*    FROM SFLIGHT
*    INTO TABLE GT_SFLIGHT
*    WHERE CARRID IN SO_CAR
*    AND CONNID IN SO_CON.
* <새로>
*  SELECT *
*    FROM SCARR
*    INTO TABLE GT_SCARR
*    WHERE CARRID IN SO_CAR.


* 15. 읽어오는 테이블 변경: BOOKINGS
* <기존>
*  SELECT *
*    FROM SCARR
*    INTO GT_scarr
*    WHERE CARRID IN SO_CAR.
* <새로>
  SELECT *
    FROM SBOOK
    INTO CORRESPONDING FIELDS OF TABLE GT_BOOKINGS
    WHERE CARRID IN SO_CAR.


* 3. SEATOCC 값에 따라 신호등 아이콘을 넣어주고자 함
* SELECT 이후에 작성되야 함
  LOOP AT GT_SFLIGHT INTO GS_SFLIGHT.
    IF GS_SFLIGHT-SEATSMAX - GS_SFLIGHT-SEATSOCC < 5.
      GS_SFLIGHT-LIGHT = 1.
    ELSEIF GS_SFLIGHT-SEATSMAX - GS_SFLIGHT-SEATSOCC < 100.
      GS_SFLIGHT-LIGHT = 2.
    ELSE.
      GS_SFLIGHT-LIGHT = 3.
    ENDIF.
    MODIFY GT_SFLIGHT FROM GS_SFLIGHT.
  ENDLOOP.
* 10. 위 신호등 부분은 색상변경 부분에서 다른 테이블 사용하면서 무의미 해짐


* 6. 색상 설정
  PERFORM MAKE_DATA.

* 12. CELL 색상 변경을 위한 SELECT
  PERFORM CELL_COLOR.

END-OF-SELECTION.
  CALL SCREEN 100.
```

<br>

### SCREEN 0100

```mysql
PROCESS BEFORE OUTPUT.
  MODULE STATUS_0100.
  MODULE CREATE_ALV.

PROCESS AFTER INPUT.
  MODULE EXIT AT EXIT-COMMAND.
```

<br>

### TOP

```mysql
*&---------------------------------------------------------------------*
*& Include          Z_ALV_FLIGHT_24_TOP
*&---------------------------------------------------------------------*
* 8. TYPE 가져오기
TYPE-POOLS COL.                   //Dictionary에 있는 Type 가져와서 사용하기 위해서

* 신호등 만들기부터 시작
* 1. 신호등도 넣을 수 았는 새로운 타입 생성
TYPES: BEGIN OF GTY_SFLIGHT.
    INCLUDE TYPE SFLIGHT.          //SFLIGHT 구조 포함
TYPES: LIGHT TYPE C LENGTH 1.      //추가할 COL 항목
TYPES: END OF GTY_SFLIGHT.

* 2. 새로 만든 타입을 참조하여 ITAB 생성 및 기존 GT_SGLIGHT, GS_SFLIGHT 주석처리
*DATA: GS_SFLIGHT TYPE SFLIGHT,
*      GT_SFLIGHT TYPE TABLE OF SFLIGHT.
DATA: GS_SFLIGHT TYPE GTY_SFLIGHT,
      GT_SFLIGHT TYPE TABLE OF GTY_SFLIGHT.

DATA: OK_CODE TYPE SY-UCOMM.

SELECT-OPTIONS: SO_CAR FOR GS_SFLIGHT-CARRID,
                SO_CON FOR GS_SFLIGHT-CONNID.

DATA: GO_CONT    TYPE REF TO CL_GUI_CUSTOM_CONTAINER,
      GO_ALV     TYPE REF TO CL_GUI_ALV_GRID,
      GS_LAYOUT  TYPE LVC_S_LAYO,
      LS_STBL    TYPE LVC_S_STBL,
      GS_VARIANT TYPE DISVARIANT,
      GV_SAVE    TYPE C LENGTH 1.

* 5. ROW 색 설정을 위한 데이터 선언
TYPES: BEGIN OF GTY_SCARR.
    INCLUDE TYPE SCARR.
TYPES: COLOR TYPE C LENGTH 4.
TYPES: END OF GTY_SCARR.
DATA: GT_SCARR TYPE TABLE OF GTY_SCARR,
      GS_SCARR TYPE GTY_SCARR.

* 11. CELL 색 설정을 위한 데이터 선언
TYPES: BEGIN OF GTY_BOOKING.
       INCLUDE TYPE SBOOK.
TYPES: IT_COLFIELDS TYPE LVC_T_SCOL.
TYPES: END OF GTY_BOOKING.
DATA: GT_BOOKINGS TYPE TABLE OF GTY_BOOKING,
      GS_BOOKING TYPE GTY_BOOKING.
DATA: GS_COLFIELD TYPE LINE OF LVC_T_SCOL.

PARAMETERS: PA_VARI TYPE DISVARIANT-VARIANT.
```

<br>

### PBO

```mysql
*&---------------------------------------------------------------------*
*& Include          Z_ALV_FLIGHT_24_B01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*& Module STATUS_0100 OUTPUT
*&---------------------------------------------------------------------*
MODULE STATUS_0100 OUTPUT.
  SET PF-STATUS '100'.
  SET TITLEBAR '100'.
ENDMODULE.

*&---------------------------------------------------------------------*
*& Module CREATE_ALV OUTPUT
*&---------------------------------------------------------------------*
MODULE CREATE_ALV OUTPUT.
  IF GO_CONT IS INITIAL.
    PERFORM SET_OBJECT.
    PERFORM SET_LAYOUT.

    GS_VARIANT-REPORT = SY-CPROG.
    GS_VARIANT-VARIANT = PA_VARI.
    GV_SAVE = 'A'.
    PERFORM DISPLAY_ALV.

  ELSE.
    LS_STBL-ROW = 'X'.
    LS_STBL-COL = 'X'.

    CALL METHOD GO_ALV->REFRESH_TABLE_DISPLAY
      EXPORTING
        IS_STABLE      = LS_STBL
        I_SOFT_REFRESH = 'X'.
  ENDIF.

ENDMODULE.
```

<br>

### PAI

```mysql
*&---------------------------------------------------------------------*
*& Include          Z_ALV_FLIGHT_24_I01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*&      Module  EXIT  INPUT
*&---------------------------------------------------------------------*
MODULE EXIT INPUT.
  CASE OK_CODE.
    WHEN 'BACK'.
      LEAVE TO SCREEN 0.
    WHEN 'EXIT' OR 'CANC'.
      LEAVE PROGRAM.
  ENDCASE.
ENDMODULE.
```

<br>

### PERFORM

```mysql
*&---------------------------------------------------------------------*
*& Include          Z_ALV_FLIGHT_24_F01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*& Form SET_OBJECT
*&---------------------------------------------------------------------*
FORM SET_OBJECT .

  CREATE OBJECT GO_CONT
    EXPORTING
      CONTAINER_NAME = 'AREA_100'.

  CREATE OBJECT GO_ALV
    EXPORTING
      I_PARENT = GO_CONT.

ENDFORM.

*&---------------------------------------------------------------------*
*& Form DISPLAY_ALV
*&---------------------------------------------------------------------*
FORM DISPLAY_ALV .
  CALL METHOD GO_ALV->SET_TABLE_FOR_FIRST_DISPLAY
    EXPORTING
*     I_BUFFER_ACTIVE  =
*     I_BYPASSING_BUFFER            =
*     I_CONSISTENCY_CHECK           =
      "I_STRUCTURE_NAME = 'SFLIGHT'               //16. 변경
      I_STRUCTURE_NAME = 'SBOOK' "'SCARR'
      IS_VARIANT       = GS_VARIANT
      I_SAVE           = GV_SAVE
      I_DEFAULT        = 'X'
      IS_LAYOUT        = GS_LAYOUT
*     IS_PRINT         =
*     IT_SPECIAL_GROUPS             =
*     IT_TOOLBAR_EXCLUDING          =
*     IT_HYPERLINK     =
*     IT_ALV_GRAPHICS  =
*     IT_EXCEPT_QINFO  =
*     IR_SALV_ADAPTER  =
    CHANGING
      IT_OUTTAB        = GT_BOOKINGS  //14. 기존 GT_SCARR에서 읽어오는 table 바뀌었으니 변경
*      IT_OUTTAB        = GT_SCARR    //10. 기존 GT_SFLIGHT에서 읽어오는 table 바뀌었으니 변경
*     IT_FIELDCATALOG  =
*     IT_SORT          =
*     IT_FILTER        =
*    EXCEPTIONS
*     INVALID_PARAMETER_COMBINATION = 1
*     PROGRAM_ERROR    = 2
*     TOO_MANY_LINES   = 3
*     OTHERS           = 4
    .
ENDFORM.

*&---------------------------------------------------------------------*
*& Form SET_LAYOUT
*&---------------------------------------------------------------------*
FORM SET_LAYOUT .
  GS_LAYOUT-GRID_TITLE = 'LIST'(002).
  GS_LAYOUT-ZEBRA = 'X'.
*  GS_LAYOUT-CWIDTH_OPT = 'X'.        //열 넒이 자동 최적화(HEADER 길이에 맞춰짐)
*  GS_LAYOUT-NO_TOOLBAR = 'X'.
*  GS_LAYOUT-NO_HEADERS = 'X'.
  GS_LAYOUT-TOTALS_BEF = 'X'.
*  GS_LAYOUT-EDIT = 'X'.
  GS_LAYOUT-SEL_MODE = 'A'.
  GS_LAYOUT-EXCP_FNAME = 'LIGHT'.     //4. 신호등에 대한 설정
*   EXCEPT FIELD NAME: 해당 필드를  신호등으로 표현
*   이 설정 전에 아얘 LIGHT COLUMN 안보인 이유?
*   LIGHT의 FIELD TYPE이 숫자가 아니여서

*   위 DISPLAY: I_STRUATURE_NAME 변경은?
*   I_STRUCTURE_NAME은 무조건 DB TABLE 이름
*   만약 DB TABLE과 전혀 다른 STRUCTURE를 보이려면?
*   FIELD CATALOG를 생성하여 사용
*   GS_LAYOUT-EXCP_LED = 'X'.        //하나짜리 신호등으로 변경

* 8. Field color 설정
  GS_LAYOUT-INFO_FNAME = 'COLOR'.    //COLOR FIELD 값으로
*  INFO_FNAME 이란?
*  ALV Control: Field name with simple row color coding

* 17. cell color 설정
  GS_LAYOUT-CTAB_FNAME = 'IT_COLFIELDS'.  
*  ALV Control: Field name with simple cell color coding
ENDFORM.

*&---------------------------------------------------------------------*
*& Form MAKE_DATA
*&---------------------------------------------------------------------*
FORM MAKE_DATA .
* 7. 색상 설정
  LOOP AT GT_SCARR INTO GS_SCARR.
* <방법 1>
    IF GS_SCARR-CURRCODE = 'USD'.
      GS_SCARR-COLOR = 'C' && '5' && '10'.
*      GS_SCARR-COLOR = 'C' && COL_NEGATIVE && '10'.       //CONCATENATE-> C610S
*      COL_NEGATIVE는 이미 정해진 TYPE
*      따라서 TYPE POOLS로 불러와야 함
      GS_SCARR-URL = 'ABCDEF'.
    ENDIF.
    MODIFY GT_SCARR FROM GS_SCARR TRANSPORTING COLOR.      //TRANSPORTING: 변경된 사항만 MODIFY

* <방법 2>
*    GS_SCARR-COLOR = 'C' && '6' && '10'.
*    GS_SCARR-URL = 'ABCDEF'.
*    MODIFY GT_SCARR FROM GS_SCARR
*    TRANSPORTING COLOR
*    WHERE CURRCODE = 'USD'.

  ENDLOOP.
ENDFORM.

*&---------------------------------------------------------------------*
*& Form CELL_COLOR
*&---------------------------------------------------------------------*
FORM CELL_COLOR .
* 13. CELL 색상 변경 작성
  LOOP AT GT_BOOKINGS INTO GS_BOOKING
    WHERE SMOKER = 'X'.
    GS_COLFIELD-FNAME = 'SMOKER'.
    GS_COLFIELD-COLOR-COL = COL_POSITIVE.
    GS_COLFIELD-COLOR-INT = '1'.
    GS_COLFIELD-COLOR-INV = '0'.
    APPEND GS_COLFIELD TO GS_BOOKING-IT_COLFIELDS.
    CLEAR: GS_COLFIELD.
    GS_COLFIELD-FNAME = 'CARRID'.                         //대상 COL명
    GS_COLFIELD-COLOR-COL = COL_NEGATIVE.
    GS_COLFIELD-COLOR-INT = '0'.                          //INTENSIVE
    GS_COLFIELD-COLOR-INV = '1'.                          //INVERSE
    APPEND GS_COLFIELD TO GS_BOOKING-IT_COLFIELDS.
    CLEAR: GS_COLFIELD.

    MODIFY GT_BOOKINGS FROM GS_BOOKING TRANSPORTING IT_COLFIELDS.
  ENDLOOP.

ENDFORM.
```
