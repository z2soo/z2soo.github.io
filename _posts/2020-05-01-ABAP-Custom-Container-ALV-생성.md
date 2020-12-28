---
title: "ABAP Custom Container ALV 생성"
categories: 
  - ABAP
tags:
  - sap
  - abap
  - alv
  - custom container
toc: true
---

## 1. ALV 생성 화면

### Selection Screen

![image](https://user-images.githubusercontent.com/58674365/103227434-b747cc00-4971-11eb-896d-0221ffa582ff.png)

<br>

### Screen 0100

![image](https://user-images.githubusercontent.com/58674365/103227452-c0d13400-4971-11eb-95f1-834afa2019a4.png)

<Br><br>

## 2. ALV 생성 코드

ALV 생성에 대한 코드와 주석으로 달아놓은 설명 및 번호를 참조한다. 

### REPORT PROGRAM

```mysql
*&---------------------------------------------------------------------*
*& Report Z_SL24_030
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT ZALV_00.

INCLUDE ZALV_00_TOP.
INCLUDE ZALV_00_B01.
INCLUDE ZALV_00_I01.
INCLUDE ZALV_00_F01.


* 10. EVENT BLOCK
INITIALIZATION.

AT SELECTION-SCREEN.

START-OF-SELECTION.

PERFORM GET_DATA.

End-of-SELECTION.
CALL SCREEN 100.
```

<br>

### SCREEN 0100

```mysql
PROCESS BEFORE OUTPUT.
* 6. STATUS 생성
  MODULE STATUS_0100.
* 12. ALV 생성
  MODULE CREATE_ALV.

PROCESS AFTER INPUT.
* 8. PF-STATUS: BACK, EXIT, CANC 입력시 실행되는 모듈 생성
  MODULE EXIT AT EXIT-COMMAND.
* MODULE USER_COMMAND_0100.
```

<br>

### TOP

```mysql
*&---------------------------------------------------------------------*
*& Include          ZSL24_030_TOP
*&---------------------------------------------------------------------*

* 1. table, structure 선언
DATA GS_SCARR TYPE SCARR.
DATA: GT_ITAB1 TYPE TABLE OF SCARR,
      GT_ITAB2 TYPE TABLE OF SCARR.
DATA GT_SPFLI TYPE TABLE OF SPFLI.
TABLES: SCARR.

* 2. Function code 받을 변수 선언
DATA OK_CODE TYPE SY-UCOMM.


* 5. parameter 입력
*parameters: pa_car3 type scarr-carrid.

* 11. Container 변수 선언
DATA GO_CONTAINER TYPE REF TO CL_GUI_CUSTOM_CONTAINER.

* 13. ALV grid 변수 선언
DATA GO_ALV_GRID TYPE REF TO CL_GUI_ALV_GRID.

* 21. SET TABLE FOR FIRST DISPLAY METHOD 사용을 위한 변수 선언
DATA: GS_VARIANT TYPE DISVARIANT,
      GV_SAVE TYPE C LENGTH 1.

* 26. LAYOUT 변수 설정
DATA: GS_LAYOUT TYPE LVC_S_LAYO.

* 3. Selection option 선언
SELECT-OPTIONS: SO_CAR1 FOR GS_SCARR-CARRID,   //Search help 미포함
                SO_CAR2 FOR SCARR-CARRID.      //Search help 포함

* 24. 입력하는 값에 따라 다른 레이아웃을 가지도록 입력 변수 설정
PARAMETERS: pa_vari TYPE disvariant-variant.
```

<br>

### PBO

```mysql
*&---------------------------------------------------------------------*
*& Include          ZSL24_030_O01
*&---------------------------------------------------------------------*

*&---------------------------------------------------------------------*
*& Module STATUS_0100 OUTPUT
*&---------------------------------------------------------------------*
MODULE STATUS_0100 OUTPUT.
* 7. STATUS, TITLE 생성
  SET PF-STATUS '0100'.
  SET TITLEBAR '0100'.
ENDMODULE.

*&---------------------------------------------------------------------*
*& Module CREATE_ALV OUTPUT
*&---------------------------------------------------------------------*
MODULE CREATE_ALV OUTPUT.	 //선언된 변수를 INSTANCE로 만듦
* 19. 조건을 걸어보자
  IF GO_CONTAINER IS INITIAL.       //CONTAINER가 INITIAL인 경우에 생성하도록 조건 넣음
                                    //없다면 계속해서 CONTAINER를 만들게 됨
    PERFORM CREATE_OBJ.

* 22. SETTING, 제한 변수 설정
     GS_VARIANT-REPORT = SY-CPROG. //현재프로그램명
     GV_SAVE = 'A'.                //x: 저장시 / 붙어서 GLOBAL LAYOUT 세팅
                                   //U: 특정 사용자에 한해서 레이아웃 세팅
                                   //A: U, X, 모두 가능
                                   //'': 레이아웃 저장 안함, 저장 권한 주지 않음

* 25. 레이아웃 변수값 입력받은 것 할당
*	  변수를 이용하면 사용자마다 다른 값 들어오게 가능
*	  사용자에 따라 저장, 세팅 권한 설정 가능해짐
    GS_VARIANT-VARIANT = PA_VARI.

    PERFORM SET_LAYOUT.
    PERFORM DISPLAY_ALV.

  ELSE.

* 20. Pattern 사용: REFRESH_TABLE_DISPLAY
* 21. PATTERN에서 사용할 변수 선언: LVC_S_STBL 참조
    DATA LS_STBL TYPE LVC_S_STBL.
    LS_STBL-ROW = 'X'.
    LS_STBL-COL = 'X'.

    CALL METHOD GO_ALV_GRID->REFRESH_TABLE_DISPLAY
*   데이터변경/SELECT재수행 시 ALV 오브젝트 생성않고 데이터만 다시 보여줌
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
*& Include          ZSL24_030_I01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*&      Module  EXIT  INPUT
*&---------------------------------------------------------------------*
MODULE EXIT INPUT.
* 9. EXIT, BACK, CANC 버튼 동작
  CASE OK_CODE.
    WHEN 'BACK'.
* 15. PERFORM 생성
      PERFORM FREE_CONTROL_RESOURCE.
      LEAVE TO SCREEN 0.
    WHEN 'EXIT' OR 'CANC'.
* 17. PERFORM 생성
      PERFORM FREE_CONTROL_RESOURCE.
      LEAVE PROGRAM.
  ENDCASE.
ENDMODULE.
```

<br>

### PERFORM

```mysql
	
*&---------------------------------------------------------------------*
*& Include          ZSL24_030_F01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
* 16. 생성한 PERFORM - FORM 입력: 메모리 FREE
FORM FREE_CONTROL_RESOURCE.
  CALL METHOD GO_ALV_GRID->FREE. 	//INSTANCE로 연결?된? 변수를 끊어냄
  CALL METHOD GO_CONTAINER->FREE.
  CLEAR: GO_ALV_GRID, GO_CONTAINER.
ENDFORM.

*&---------------------------------------------------------------------*
*& Form GET_DATA
*&---------------------------------------------------------------------*
FORM GET_DATA .
* 4. DB에서 데이터 읽어오기
  SELECT *
  FROM SPFLI
  INTO TABLE GT_SPFLI
  WHERE CARRID IN SO_CAR1.  	//3번으로 입력시 사용: 범위 내에 있을 때 
*  WHERE CARRID = PA_CAR3.  	//5번으로 입력시 사용: 동일 값일 때 

ENDFORM.

*&---------------------------------------------------------------------*
*& Form CREATE_OBJ
*&---------------------------------------------------------------------*
FORM CREATE_OBJ .

*   12. Pattern 사용한 create object: create object / 변수명 / CL_GUI_CUSTOM_CONTAINER
    CREATE OBJECT GO_CONTAINER
      EXPORTING
        CONTAINER_NAME = 'CONTROL_AREA'.   //CUSTOM CONTROL 이름으로 연결

*   14. Pattern 사용한 create object: create object / 변수명 / CL_GUI_ALV_GRID
    CREATE OBJECT GO_ALV_GRID
      EXPORTING
        I_PARENT = GO_CONTAINER.           //CONTAINER 이름으로 연결
        
*   그 결과 CONTAINER, GRID 연결!
ENDFORM.

*&---------------------------------------------------------------------*
*& Form SET_LAYOUT
*&---------------------------------------------------------------------*
FORM SET_LAYOUT .

* 27. LAYOUT 값 설정
    GS_LAYOUT-GRID_TITLE = 'LIST'(002).
    GS_LAYOUT-ZEBRA = 'X'.
    GS_LAYOUT-CWIDTH_OPT = 'X'.
*    GS_LAYOUT-NO_TOOLBAR = 'X'.
*    GS_LAYOUT-NO_HEADERS = 'X'.
    GS_LAYOUT-TOTALS_BEF = 'X'.       //작성시 상단에 표시됨, DEFAULT 값은 하단에 표시
*    GS_LAYOUT-EDIT = 'X'.            //수정 가능, 수정 툴바 생성
    GS_LAYOUT-SEL_MODE = 'A'.         //' ': B와 동일
                                      //'A':
                                      //'B': 행 하나만 선택 가능, 드레그 불가
                                      //'C': 행 하나만 선택 가능, 드레그 가능
                                      //'D': 복수 가능, 드레그 원하는 형태로 가능
ENDFORM.

*&---------------------------------------------------------------------*
*& Form DISPLAY_ALV
*&---------------------------------------------------------------------*
FORM DISPLAY_ALV .

*   18. Internal table 값을 ALV에 보여주도록 설정
*   23. SETTING 하고 설정 추가
    CALL METHOD GO_ALV_GRID->SET_TABLE_FOR_FIRST_DISPLAY
      EXPORTING
*        I_BUFFER_ACTIVE               =
*        I_BYPASSING_BUFFER            =
*        I_CONSISTENCY_CHECK           =
* FIELD CATALOG, DB의 SPFLI와 같은 구조임을 선언
        I_STRUCTURE_NAME              = 'SPFLI'  
        IS_VARIANT                    = GS_VARIANT    //VARIANT LAYOUT 이름(?)
        I_SAVE                        = GV_SAVE
        I_DEFAULT                     = 'X'           //VARIANT 레이아웃 DEFAULT 설정
        IS_LAYOUT                     = GS_LAYOUT     //레이아웃 설정
*        IS_PRINT                      =
*        IT_SPECIAL_GROUPS             =
*        IT_TOOLBAR_EXCLUDING          =
*        IT_HYPERLINK                  =
*        IT_ALV_GRAPHICS               =
*        IT_EXCEPT_QINFO               =
*        IR_SALV_ADAPTER               =
      CHANGING
        IT_OUTTAB                     = GT_SPFLI      //해당 ITAB을 통해 보여줌
*        IT_FIELDCATALOG               =
*        IT_SORT                       =
*        IT_FILTER                     =
*      EXCEPTIONS
*        INVALID_PARAMETER_COMBINATION = 1
*        PROGRAM_ERROR                 = 2
*        TOO_MANY_LINES                = 3
*        OTHERS                        = 4
            .
ENDFORM.
```

