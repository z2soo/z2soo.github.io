---
title: "ABAP Docking container 생성"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - abap study
toc: true
---

## 1. Grid ALV

### Grid ALV란?

ABAP List Viewer의 약자로 ERP 업무를 보는 현업 담당자들이 데이터를 볼 때 excel sheet와 유사한 형태로 보여지는 화면이다.

<br>

### Grid setting 

- Docking Container -> Splitter Container -> CL_DD_Document (Top of Page 구성)
- Docking Container -> Splitter Container -> CL_GUI_ALV_GRID (Grid ALV 구성)

<br><br>

## 2. Docking container ALV 생성

### 1) 레포트 생성 및 INCLUDE 생성

![image](https://user-images.githubusercontent.com/58674365/98088864-5518a080-1ec5-11eb-92c5-d22d99766504.png)<BR><BR>

### 2) INCLUDE - TOP

![image](https://user-images.githubusercontent.com/58674365/98088987-809b8b00-1ec5-11eb-90f4-c7b5ef47a236.png)

<BR><BR>

### 3) SCREEN 생성

![image](https://user-images.githubusercontent.com/58674365/98063361-3c8f9280-1e93-11eb-8c7c-269de5fe9ac3.png)

![image](https://user-images.githubusercontent.com/58674365/98063402-516c2600-1e93-11eb-944e-14ceab46df70.png)

![image](https://user-images.githubusercontent.com/58674365/98081204-087b9800-1eba-11eb-9e65-9e6c3ae9ca34.png)

![image](https://user-images.githubusercontent.com/58674365/98064007-a8262f80-1e94-11eb-9b7a-338054ea674d.png)

MODULE USER_COMMAND_0100 AT EXIT-COMMAND에는 버튼 클릭에 따른 뒤로가기 설정을 해주며 코드는 아래 소스코드를 참조한다. 

<BR><BR>

### 4) INCLUDE - SELECTION

![image](https://user-images.githubusercontent.com/58674365/98064519-db1cf300-1e95-11eb-9aa9-f9477893d805.png)

<BR><BR>

### 5) ALV 설정

PBO 부분 중 DISPLAY_ALV_0100에 대해 PERFORM으로 부분별로 나누어 작성해본다. 

![image](https://user-images.githubusercontent.com/58674365/98075650-1a583d80-1eb0-11eb-9cc4-175eb5d1b93c.png)

![image](https://user-images.githubusercontent.com/58674365/98075912-96eb1c00-1eb0-11eb-92d8-4d227370da87.png)

<BR>

#### SET_CONTAINER

ALV를 위, 아래로 나눠 출력하면 다음과 같은 화면이 보여진다. 

![image](https://user-images.githubusercontent.com/58674365/98080115-437ccc00-1eb8-11eb-9789-3b716bea3fd8.png)

![image](https://user-images.githubusercontent.com/58674365/98080564-f4836680-1eb8-11eb-87c4-1fa87b4dd443.png)

![image](https://user-images.githubusercontent.com/58674365/98079515-43c89780-1eb7-11eb-9e74-2fa1dcddf9c5.png)

<BR>

#### SET_DISPLAY

![image](https://user-images.githubusercontent.com/58674365/98081371-4c6e9d00-1eba-11eb-8027-7e3c4b474a6f.png)

<BR>

#### SET_LAYOUT

출력하는 ALV의 LAYOUT을 아래와 같이 설정한 후 실행하면 다음과 같이 출력된다.  

![image](https://user-images.githubusercontent.com/58674365/98081397-5abcb900-1eba-11eb-822f-39970d3ab6fe.png)

![image](https://user-images.githubusercontent.com/58674365/98080964-a753c480-1eb9-11eb-92c2-2e306d9fc024.png)

<BR>

### 6) INCLUDE - CLASS

상단 GRID에 텍스트를 보여주고자 한다. 이에 대한 클래스를 생성하고 이벤트를 추가해 본다.

![image](https://user-images.githubusercontent.com/58674365/98081626-cef75c80-1eba-11eb-93de-c90845524870.png)

<BR>

#### SET_GRID_EVENT

상단 GRID에 대해 텍스트를 보여주는 이벤트를 추가해본다. 

![image](https://user-images.githubusercontent.com/58674365/98089517-2cdd7180-1ec6-11eb-843b-5b12e3a1c83a.png)

![image](https://user-images.githubusercontent.com/58674365/98089581-45e62280-1ec6-11eb-87f8-45693f530fd6.png)

<BR>

날짜로 값을 만든 GT_TITLE을 출력하려고 보니 TABLE TYPE ERROR가 발생했다. 

출력되는지 확인하기 위해 직접 입력한 한 줄 텍스트로 테스트 해보았지만 여전히 텍스트가 나타나지 않는다. 어떤 부분을 수정할 지에 대한 업데이트가 필요한 상황이다. 

![image](https://user-images.githubusercontent.com/58674365/98091807-3ddbb200-1ec9-11eb-979e-92d326cf3497.png)

<BR><BR>

## 3. 코드

### REPORT

```SQL
*&---------------------------------------------------------------------*
*& Report Z0001
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT Z0001.

INCLUDE Z0001_TOP.  "전역변수, ITAB 선언
INCLUDE Z0001_CLS.  "로컬 클래스
INCLUDE Z0001_SEL.  "조회화면
INCLUDE Z0001_F01.  "함수
INCLUDE Z0001_O01.  "PBO
INCLUDE Z0001_I01.  "PAI
```

<BR>

### INCLUDE - TOP

```SQL
*&---------------------------------------------------------------------*
*& Include          Z0001_TOP
*&---------------------------------------------------------------------*

*1. INTERNAL TABLE 선언
DATA: GT_DETAIL TYPE TABLE OF SFLIGHT,
      GS_DETAIL TYPE          SFLIGHT,
      SCARR     TYPE          SCARR.

*ALV 상단 첫번째 CONTAINER에 출력할 ITAB: TOP-OF-PAGE를 위한 선언
DATA: BEGIN OF GS_TITLE,
        TYP(001)   TYPE C,
        INFO(030)  TYPE C,
        KEY(200)   TYPE C,
      END OF GS_TITLE,
      GT_TITLE LIKE TABLE OF GS_TITLE.

*OK_CODE
DATA: OK_CODE TYPE SY-SUBRC.

*2. DOCKING CONTAINER 선언
DATA: GO_DOCK TYPE REF TO CL_GUI_DOCKING_CONTAINER.

*3. ALV GRID 선언
DATA: GO_GRID TYPE REF TO CL_GUI_ALV_GRID.

*4. 로컬클래스 선언이 아래에 있음을 선언
"CLASS LO_CLASS DEFINITION DEFERRED.

*5. LAYOUT 선언: ALV에 출력될 ITAB 전체 레이아웃 설정
DATA: GS_LAYO TYPE LVC_S_LAYO.

*6. SPLITTER CONTAINER: ALV 화면분할을 위한 클래스 선언
DATA: GO_SPLIT TYPE REF TO CL_GUI_SPLITTER_CONTAINER,
      GO_TOP   TYPE REF TO CL_GUI_CONTAINER,  "위
      GO_BOT   TYPE REF TO CL_GUI_CONTAINER,  "아래
      GO_DOC   TYPE REF TO CL_DD_DOCUMENT.    "TOP-OF-PAGE
```

<BR>

### INCLUDE - SELECTION

```SQL
*&---------------------------------------------------------------------*
*& Include          Z0001_SEL
*&---------------------------------------------------------------------*

*1. 조회용 화면 구성
SELECTION-SCREEN BEGIN OF BLOCK B1 WITH FRAME TITLE TEXT-001.
SELECT-OPTIONS: S_CARRID FOR SCARR-CARRID.  "조회용 ITAB
SELECTION-SCREEN END OF BLOCK B1.

*2. 사용자가 F8 클릭시 실행될 로직을 구현
START-OF-SELECTION.
  "DB TABLE에서 ITAB으로 WHERE 조건에 맞게 데이터 추출
  SELECT *
    INTO CORRESPONDING FIELDS OF TABLE GT_DETAIL
    FROM SFLIGHT
    WHERE CARRID IN S_CARRID.

*3. ITAB의 데이터를 출력할 화면구성
END-OF-SELECTION.   "출력부
  CALL SCREEN 100.  "SCREEN 100 호출
```

<BR>

### INCLUDE - CLASS

```SQL
*&---------------------------------------------------------------------*
*& Include          Z0001_CLS
*&---------------------------------------------------------------------*

*원리
*ALV 클래스 CL_GUI_ALV_GRID의 INSTANCE EVENT 기능으로 LOCAL INSTANCE METHOD를 선언

*LOCAL CLASS 정의
CLASS LO_CLASS DEFINITION.
  PUBLIC SECTION.
  METHODS: HANDLE_TOP_OF_PAGE FOR EVENT TOP_OF_PAGE OF CL_GUI_ALV_GRID
                              IMPORTING
                                E_DYNDOC_ID.
ENDCLASS.

*LOCAL CLASS 구현
CLASS LO_CLASS IMPLEMENTATION.
  METHOD HANDLE_TOP_OF_PAGE.
    PERFORM TOP_OF_PAGE USING E_DYNDOC_ID.
  ENDMETHOD.
ENDCLASS.

DATA: GO_EVENT TYPE REF TO LO_CLASS.
```

<BR>

### INCLUDE - PBO

```sql
*&---------------------------------------------------------------------*
*& Include          Z0001_O01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*& Module STATUS_0100 OUTPUT
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
MODULE STATUS_0100 OUTPUT.
  SET PF-STATUS '100'.
  SET TITLEBAR '100'.
ENDMODULE.

*&---------------------------------------------------------------------*
*& Module DISPLAY_ALV_0100 OUTPUT
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
MODULE DISPLAY_ALV_0100 OUTPUT.
  IF GO_GRID IS INITIAL.        "GRID 인스턴스 최초 실행시
    PERFORM SET_CONTAINER.      "1.CONTAINER 생성
    PERFORM SET_LAYOUT.         "2.ALV LAYOUT 설정
    PERFORM SET_GRID_EVENT.     "3.ALV EVENT 등록
    PERFORM DISPLAY_ALV.        "4.ALV GRID 출력
  ENDIF.
ENDMODULE.
```

<BR>

### INCLUDE - PAI

```SQL
*&---------------------------------------------------------------------*
*& Include          Z0001_I01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*&      Module  USER_COMMAND_0100  INPUT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
MODULE USER_COMMAND_0100 INPUT.
  LEAVE TO SCREEN 0.
ENDMODULE.
```

<BR>

### INCLUDE - PERFORM

```SQL
*&---------------------------------------------------------------------*
*& Include          Z0001_F01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*& Form SET_CONTAINER
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM SET_CONTAINER .
*OBJECT 생성
  CREATE OBJECT GO_DOCK
    EXPORTING
      DYNNR      = SY-DYNNR  "현재 화면 번호
      REPID      = SY-CPROG  "현재 프로그램
      "SIDE       = 1         "SPLIT SCROLL BAR 조정-가로
      SIDE       = 2         "SPLIT SCROLL BAR 조정-세로
      EXTENSION = 2000.      "너비 길이

  CREATE OBJECT GO_SPLIT
    EXPORTING
      ROWS      = 2          "행 갯수
      COLUMNS   = 1          "열 갯수
      PARENT    = GO_DOCK.   "컨테이너 설정

*CONTAINER 설정
  CALL METHOD GO_SPLIT->GET_CONTAINER
    EXPORTING
      ROW       = 1
      COLUMN    = 1
    RECEIVING
      CONTAINER = GO_TOP.

  CALL METHOD GO_SPLIT->GET_CONTAINER
    EXPORTING
      ROW       = 2
      COLUMN    = 1
    RECEIVING
      CONTAINER = GO_BOT
    EXCEPTIONS
      OTHERS    = 1.

*높이 설정
  CALL METHOD GO_SPLIT->SET_ROW_HEIGHT
    EXPORTING
      ID          = 1  "1번 CONTAINER
      HEIGHT      = 20 "높이를 20으로 설정
    EXCEPTIONS
      CNTL_ERROR  = 1.

* GRID 설정: ALV GRID는 GO_BOT라는 컨테이너로 연결
  CREATE OBJECT GO_GRID
    EXPORTING
      I_PARENT    = GO_BOT.

ENDFORM.

*&---------------------------------------------------------------------*
*& Form SET_LAYOUT
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM SET_LAYOUT .
*LAYOUT 설정
  GS_LAYO-CWIDTH_OPT = ABAP_TRUE.   "컬럼길이 최적화
  GS_LAYO-GRID_TITLE = '첫번째 ALV'.
  GS_LAYO-EDIT       = 'X'.         "전체필드 수정 가능
  GS_LAYO-SEL_MODE   = 'A'.         "열,행 선택 가능
  GS_LAYO-ZEBRA      = 'X'.         "얼룩무늬
  GS_LAYO-TOTALS_BEF = 'X'.         "총계를 최상단에 출력
  GS_LAYO-NO_ROWINS  = 'X'.         "Excel 데이터 alv로 복사 방지
ENDFORM.

*&---------------------------------------------------------------------*
*& Form SET_GRID_EVENT
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM SET_GRID_EVENT .
*EVENT CLASS 등록
  CREATE OBJECT GO_EVENT.

*EVENT METHOD 등록: GO_GRID 인스턴스에 EVENT METHOD를 등록
  SET HANDLER GO_EVENT->HANDLE_TOP_OF_PAGE FOR GO_GRID.

*TOP-OF-PAGE 이벤트를 실행시키는 메서드
  CALL METHOD GO_GRID->LIST_PROCESSING_EVENTS
    EXPORTING
      I_EVENT_NAME  = 'TOP_OF_PAGE'
      I_DYNDOC_ID   = GO_DOC.

ENDFORM.

*&---------------------------------------------------------------------*
*& Form DISPLAY_ALV
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM DISPLAY_ALV .
*ALV GRID 출력
  CALL METHOD GO_GRID->SET_TABLE_FOR_FIRST_DISPLAY
    EXPORTING
      IS_LAYOUT           =   GS_LAYO
      I_STRUCTURE_NAME    =   'SFLIGHT'   "SFLIGGHT DB TABLE과 동일한 구조
    CHANGING
      IT_OUTTAB           =   GT_DETAIL.
ENDFORM.

*&---------------------------------------------------------------------*
*& Form TOP_OF_PAGE
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*      -->P_E_DYNDOC_ID  text
*&---------------------------------------------------------------------*
FORM TOP_OF_PAGE  USING E_DYNDOC_ID TYPE REF TO CL_DD_DOCUMENT.

  CLEAR: GS_TITLE, GT_TITLE.
  DATA: S_BUDAT TYPE SY-DATUM.

  S_BUDAT       = SY-DATUM.
  GS_TITLE-TYP  = 'S'.
  GS_TITLE-KEY  = 'KEY1'.

*날짜 설정
  CONCATENATE S_BUDAT+0(4) ' ' S_BUDAT+4(2) ' ' S_BUDAT+6(2) ' ' ` ~ `
              S_BUDAT+0(4) ' ' S_BUDAT+4(2) ' ' S_BUDAT+6(2) ' '
              INTO GS_TITLE-INFO.
  APPEND GS_TITLE TO GT_TITLE.

*화면에 출력
  PERFORM DISPLAY_DOCUMENT USING E_DYNDOC_ID.

ENDFORM.

*&---------------------------------------------------------------------*
*& Form DISPLAY_DOCUMENT
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*      -->P_E_DYNDOC_ID  text
*&---------------------------------------------------------------------*
FORM DISPLAY_DOCUMENT  USING E_DYNDOC_ID TYPE REF TO CL_DD_DOCUMENT.
*TEXT 한 줄 만 출력하고 싶을 때
  CALL METHOD E_DYNDOC_ID->ADD_TEXT
    EXPORTING
      TEXT        = 'ADD_TEXT 메서드 출력'.    "255자리까지 입력가능.
      "TEXT_TABLE  = GT_TITLE.

*METHOD DOCUMENT 출력
*  CALL METHOD E_DYNDOC_ID->DISPLAY_DOCUMENT
*    EXPORTING
*      REUSE_CONTROL   = 'X'
*      PARENT          = GO_TOP.
ENDFORM.
```

