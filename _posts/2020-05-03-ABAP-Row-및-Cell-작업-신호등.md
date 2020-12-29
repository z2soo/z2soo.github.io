---
title: "ABAP Row 및 Cell 작업: 신호등 및 sort"
categories: 
  - ABAP
tags:
  - sap
  - abap
  - cell color
  - sort
toc: true
---

## 1. ALV 생성 화면

> 이전 포스팅에서 특정 조건의 cell의 색상을 변경하는 작업을 했다면, 이번에는 특정 조건에 따라 LED 신호등의 색상이 변경되도록 코드를 작성해본다. 

### Selection screen

![image](https://user-images.githubusercontent.com/58674365/103228404-1a3a6280-4974-11eb-99d6-cecff18b4fab.png)<br>

### Screen 0100

![image](https://user-images.githubusercontent.com/58674365/103228416-21fa0700-4974-11eb-8090-3a2824b1e85a.png)

<br><br>

## 2. ALV 생성 코드

이전 포스트의 코드와 겹치는 부분 외에 신호등 부분 및 sort에 대한 부분 만 주석으로 번호를 달아두었다. 

### REPORT PROGRAM

```mysql
*&---------------------------------------------------------------------*
*& Report Z_ALV_FLIGHT_24
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT ZALV24_03.

INCLUDE ZALV_03_TOP.
INCLUDE ZALV_03_B01.
INCLUDE ZALV_03_I01.
INCLUDE ZALV_03_F01.

INITIALIZATION.

AT SELECTION-SCREEN.

START-OF-SELECTION.

  PERFORM GET_DATA.
  PERFORM SET_SIGN.
  PERFORM SET_COLOR.

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

TYPE-POOLS COL.

DATA: OK_CODE TYPE SY-UCOMM.

* 신호등, ROW, CELL
TYPES: BEGIN OF GTY_SFLIGHT.
    INCLUDE TYPE SFLIGHT.
TYPES: LIGHT TYPE C LENGTH 1,          //신호등
       COLOR TYPE C LENGTH 4,          //ROW
       IT_COLFIELDS TYPE LVC_T_SCOL.   //CELL
TYPES: END OF GTY_SFLIGHT.
DATA: GS_SFLIGHT TYPE GTY_SFLIGHT,
      GT_SFLIGHT TYPE TABLE OF GTY_SFLIGHT,
      GS_COLFIELD TYPE LINE OF LVC_T_SCOL.

SELECT-OPTIONS: SO_CAR FOR GS_SFLIGHT-CARRID,
                SO_CON FOR GS_SFLIGHT-CONNID.

DATA: GO_CONT    TYPE REF TO CL_GUI_CUSTOM_CONTAINER,
      GO_ALV     TYPE REF TO CL_GUI_ALV_GRID,
      GS_LAYOUT  TYPE LVC_S_LAYO,
      LS_STBL    TYPE LVC_S_STBL,
      GS_VARIANT TYPE DISVARIANT,
      GV_SAVE    TYPE C LENGTH 1.

* 1. sort 위한 data 선언
DATA: GT_SORT TYPE LVC_T_SORT,
      GS_SORT TYPE LVC_S_SORT.

* 5. ALV TOOLBAR FUNCTION 제거를 위한 데이터 선언
* 숨기고자 하는 툴바 기능을 모아둘 데이터 변수
DATA GT_EXCLUDE TYPE UI_FUNCTIONS.

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

* 2. SORT 설정
    GS_SORT-FIELDNAME = 'PRICE'.      //어떤 필드로 정렬할지 설정
    GS_SORT-UP = 'X'.                 //오름차순 정렬
    APPEND GS_SORT TO GT_SORT.

*	정렬 기준을 하나 이상으로 줄 때는 WA를 clear하고 기준 추가
    CLEAR GS_SORT.
    GS_SORT-FIELDNAME = 'SEATSOCC'.   //내림차순 정렬
    GS_SORT-DOWN = 'X'.
    APPEND GS_SORT TO GT_SORT.

* 6. ALV TOOLBAR 숨김 설정
*	숨기고자 하는 것을 APPEND, 이때는 =>, TO 사용
*	MC_FC_DETAIL: TOOL BAR에서 &DETAIL 기능을 하는 부분
*   DETAIL 기능이 사라진다.
*	CL_GUI_ALV_GRID > ATTRIBUTES 에서 버튼 확인 가능
    APPEND CL_GUI_ALV_GRID=>MC_FC_DETAIL TO GT_EXCLUDE.
    APPEND CL_GUI_ALV_GRID=>MC_FC_REFRESH TO GT_EXCLUDE.
    PERFORM DISPLAY_ALV.

*   ->, => 차이??

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
      I_STRUCTURE_NAME = 'SFLIGHT'
      IS_VARIANT       = GS_VARIANT
      I_SAVE           = GV_SAVE
      I_DEFAULT        = 'X'
      IS_LAYOUT        = GS_LAYOUT
*     IS_PRINT         =
*     IT_SPECIAL_GROUPS             =

* 4. 제공되는 ALV TOOLBAR 중에서 숨기고 싶은 기능을 설정하고 싶다면: IT_TOOLVAR_EXCLUDING
* 참고로 SET_TABLE_FOR_FIRST_DISPLAY METHOD는 CL_GUI_ALV_GRID 클래스에서 받아옴
* 따라서 CL_GUI_CUSTOM_CONTAINER에 들어가서 뭐가 있는지 확인 가능

      IT_TOOLBAR_EXCLUDING          = GT_EXCLUDE

*     IT_HYPERLINK     =
*     IT_ALV_GRAPHICS  =
*     IT_EXCEPT_QINFO  =
*     IR_SALV_ADAPTER  =
    CHANGING
      IT_OUTTAB        = GT_SFLIGHT
*     IT_FIELDCATALOG  =

* 3. SORT 설정
     IT_SORT          = GT_SORT
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
*  GS_LAYOUT-CWIDTH_OPT = 'X'.
*  GS_LAYOUT-NO_TOOLBAR = 'X'.
*  GS_LAYOUT-NO_HEADERS = 'X'.
  GS_LAYOUT-TOTALS_BEF = 'X'.
*  GS_LAYOUT-EDIT = 'X'.
  GS_LAYOUT-SEL_MODE = 'A'.

* <신호등>
  GS_LAYOUT-EXCP_FNAME = 'LIGHT'.
  GS_LAYOUT-EXCP_LED = 'X'.

* <Row 색상>
  GS_LAYOUT-INFO_FNAME = 'COLOR'.

* <Cell 색상>
  GS_LAYOUT-CTAB_FNAME = 'IT_COLFIELDS'.
ENDFORM.

*&---------------------------------------------------------------------*
*& Form GET_DATA
*&---------------------------------------------------------------------*
FORM GET_DATA .
  SELECT *
  FROM SFLIGHT
  INTO CORRESPONDING FIELDS OF TABLE GT_SFLIGHT
  WHERE CARRID IN SO_CAR
  AND CONNID IN SO_CON AND CARRID IN SO_CAR.
ENDFORM.

*&---------------------------------------------------------------------*
*& Form SET_SIGN
*&---------------------------------------------------------------------*
FORM SET_SIGN .
  LOOP AT GT_SFLIGHT INTO GS_SFLIGHT.
    IF GS_SFLIGHT-SEATSMAX - GS_SFLIGHT-SEATSOCC = 0.
      GS_SFLIGHT-LIGHT = 1.
    ELSEIF GS_SFLIGHT-SEATSMAX - GS_SFLIGHT-SEATSOCC < 50.
      GS_SFLIGHT-LIGHT = 2.
    ELSE.
      GS_SFLIGHT-LIGHT = 3.
    ENDIF.
    MODIFY GT_SFLIGHT FROM GS_SFLIGHT.
  ENDLOOP.
ENDFORM.

*&---------------------------------------------------------------------*
*& Form SET_COLOR
*&---------------------------------------------------------------------*
FORM SET_COLOR .
  LOOP AT GT_SFLIGHT INTO GS_SFLIGHT.
      IF GS_SFLIGHT-FLDATE+4(2) = SY-DATUM+4(2).
        GS_SFLIGHT-COLOR = 'C' && COL_NEGATIVE && '01'. //01-글자색, 10-배경색
      ENDIF.
      MODIFY GT_SFLIGHT FROM GS_SFLIGHT TRANSPORTING COLOR.

      IF GS_SFLIGHT-PLANETYPE = '747-400'.
         GS_COLFIELD-FNAME = 'PLANETYPE'.
         GS_COLFIELD-COLOR-COL = COL_POSITIVE.
         GS_COLFIELD-COLOR-INT = '0'.
         GS_COLFIELD-COLOR-INV = '1'.
         APPEND GS_COLFIELD TO GS_SFLIGHT-IT_COLFIELDS.
         CLEAR: GS_COLFIELD.
      ENDIF.

      MODIFY GT_SFLIGHT FROM GS_SFLIGHT TRANSPORTING IT_COLFIELDS.

    ENDLOOP.
ENDFORM.
```
