---
title: "ABAP ALV Toolbar 메뉴 타입 버튼 생성 (Drop List)"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - toolbar
  - event
toc: true

---

<br>

# ALV Toolbar 메뉴 타입 버튼 생성

## 1. Toolbar button 생성

ALV Toolbar에 button을 생성해준다. 아래 코드에는 없지만 잊지 말고 Event Handler도 등록해준다. 비교를 위해 drop list로 생성할 버튼과 일반적인 push 버튼을 생성했다.<br>아래는 Toolbar 버튼만 생성한 상태이다.

<br>

![image](https://user-images.githubusercontent.com/58674365/161659360-23e9d566-dfbe-4c83-8a03-06de7184bb92.png)

<br>

```abap
*&---------------------------------------------------------------------*
*& Include          CLASS
*&---------------------------------------------------------------------*
CLASS gi_alvgrid DEFINITION DEFERRED.
*&---------------------------------------------------------------------*
*&      Class DEFINITION
*&---------------------------------------------------------------------*
CLASS gi_alvgrid DEFINITION INHERITING FROM cl_gui_alv_grid FINAL.
ENDCLASS. 

*----------------------------------------------------------------------*
*       CLASS gi_receivr DEFINITION
*----------------------------------------------------------------------*
CLASS gi_receivr DEFINITION FINAL.
  PUBLIC SECTION.
    DATA gm_gridnm TYPE /accgo/e_grid_name.
    METHODS:
      constructor
        IMPORTING VALUE(iv_name) TYPE /accgo/e_grid_name,
        
      handle_toolbar
        FOR EVENT toolbar OF cl_gui_alv_grid
        IMPORTING sender e_object e_interactive.
ENDCLASS. "lcl_recivr DEFINITION

*&---------------------------------------------------------------------*
*&      CLASS lcl_recivr IMPLEMENTATION
*&---------------------------------------------------------------------*
CLASS gi_receivr IMPLEMENTATION.
  METHOD constructor.
    gm_gridnm = iv_name.
  ENDMETHOD.                    "constructor

  METHOD handle_toolbar.
        PERFORM zf_handle_toolbar USING e_object	  #아래 perform 참조
                                        e_interactive.
  ENDMETHOD.
ENDCLASS.
*&---------------------------------------------------------------------*
*& Form ZF_HANDLE_TOOLBAR
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*&      --> E_OBJECT
*&      --> E_INTERACTIVE
*&---------------------------------------------------------------------*
FORM zf_handle_toolbar  USING  pi_object TYPE REF TO cl_alv_event_toolbar_set
                                pi_interactive.

  DATA : ls_toolbar TYPE stb_button.

  CLEAR : ls_toolbar.
  ls_toolbar-butn_type = 2.
  ls_toolbar-function  = 'BTN_LIST'.			#drop list로 생성할 버튼
  ls_toolbar-quickinfo = 'List Button'.
  ls_toolbar-text      = 'List Button'.
  ls_toolbar-disabled  = abap_off.
  ls_toolbar-icon      = icon_dropdownlist.
  APPEND ls_toolbar TO pi_object->mt_toolbar.

  CLEAR : ls_toolbar.
  ls_toolbar-function  = 'BTN_PUSH'.		     #일반적인 push 버튼
  ls_toolbar-quickinfo = 'Push Button'.
  ls_toolbar-text      = 'Push Button'.
  ls_toolbar-disabled  = abap_off.
  APPEND ls_toolbar TO pi_object->mt_toolbar.

ENDFORM.
```

 <br>

이때, drop list로 생성할 버튼의 **butn_type = 2** 로 설정했는데, 이는 **메뉴 타입**에 해당된다. (=drop list 형태)<br>

![image](https://user-images.githubusercontent.com/58674365/161659679-76499c45-c982-4407-acc8-4f4968e6eef5.png)

<br>

## 2. Menu List (Drop List) 생성

메뉴 타입으로 설정한 버튼은 메뉴 구조(Menu List, Drop List)를 따로 만들어줘야 한다. 

메뉴 타입의 버튼을 클릭했을 때의 function code는 **HANDLE_MENU_BUTTON** 이벤트에서 인식 가능하기에 해당 메소드에서 보여질 drop list를 만들어준다. 아래 코드에는 없지만 잊지 말고 Event Handler도 등록해준다.

**메뉴 구조는 리스트로 보여지는 Text와 해당 값을 클릭했을 때의 Function code값으로 구성된다.**<br>그리고 선택된 리스트 값의 function code는 Event의 user command로 인식되니 **HANDLE_USER_COMMAND** 이벤트를 추가하여 값에 따른 추가 로직/동작을 작성해주면 된다. <br>

 <br>

```abap
*----------------------------------------------------------------------*
*       CLASS DEFINITION
*----------------------------------------------------------------------*
CLASS gi_receivr DEFINITION FINAL.
	..(일부 생략)
    METHODS:
      handle_menu_button
        FOR EVENT menu_button  OF cl_gui_alv_grid
        IMPORTING sender e_object e_ucomm.
ENDCLASS.
*&---------------------------------------------------------------------*
*&      CLASS IMPLEMENTATION
*&---------------------------------------------------------------------*
CLASS gi_receivr IMPLEMENTATION.
	..(일부 생략)
  METHOD handle_menu_button.
    PERFORM zf_menu_button USING e_object  e_ucomm.
  ENDMETHOD.
ENDCLASS.

*&---------------------------------------------------------------------*
*& Form ZF_MENU_BUTTON
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*&      --> E_OBJECT
*&      --> E_UCOMM
*&---------------------------------------------------------------------*
FORM zf_menu_button  USING    p_e_object
                              p_e_ucomm.

  DATA: lt_drop  TYPE lvc_t_dral,
        lv_fcode TYPE sy-ucomm,
        lv_text  TYPE  text40.

#--List 생성
  APPEND VALUE #( handle = space value = 'Choice 1' int_value = 'A' ) TO lt_drop.
  APPEND VALUE #( handle = space value = 'Choice 2' int_value = 'B' ) TO lt_drop.
  APPEND VALUE #( handle = space value = 'Choice 3' int_value = 'C' ) TO lt_drop.
  
#--List 등록   
  CASE pv_ucomm.
    WHEN 'BTN_LIST'.

      LOOP AT lt_drop INTO DATA(ls_drop).
        CLEAR: lv_text, lv_fcode.
        IF ls_drop-int_value IS NOT INITIAL.
          lv_text  = ls_drop-value.			#보여지는 text
          lv_fcode = ls_drop-int_value.		#선택시 sy-ucomm이 되는 값
        ELSE.
          lv_text  = gc_text.
          lv_fcode = gc_drop_cmd-000.		#Enter치는 user command 구별 위함
        ENDIF.

        CALL METHOD pi_object->add_function
          EXPORTING
            fcode = lv_fcode
            text  = lv_text.
      ENDLOOP.
  ENDCASE.
ENDFORM.
```

 <br>HANDLE_MENU_BUTTON 으로 리스트 적용 후 버튼 클릭시

![image](https://user-images.githubusercontent.com/58674365/161677188-3a2ed355-16d7-40d9-a760-11aaf30486f2.png)

<br>

## 3. List 값 선택에 따른 로직 추가

선택된 리스트 값의 function code는 Event의 user command로 인식되니 **HANDLE_USER_COMMAND** 이벤트를 추가하여 값에 따른 추가 로직/동작을 작성해주면 된다. 아래 코드에는 없지만 잊지 말고 Event Handler도 등록해준다.<br> 

```ABAP
*----------------------------------------------------------------------*
*       CLASS DEFINITION
*----------------------------------------------------------------------*
CLASS gi_receivr DEFINITION FINAL.
	..(일부 생략)
    METHODS:
      handle_user_command
        FOR EVENT user_command OF cl_gui_alv_grid
        IMPORTING sender e_ucomm.
ENDCLASS.
*&---------------------------------------------------------------------*
*&      CLASS IMPLEMENTATION
*&---------------------------------------------------------------------*
CLASS gi_receivr IMPLEMENTATION.
	..(일부 생략)
  METHOD handle_user_command.
    PERFORM zf_user_comm USING e_ucomm.
  ENDMETHOD.
ENDCLASS.

*&---------------------------------------------------------------------*
*& Form ZF_USER_COMM
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*&      --> E_UCOMM
*&---------------------------------------------------------------------*
FORM zf_user_comm  USING pv_ucomm TYPE sy-ucomm.
  # 여기에 pv_ucomm 값으로 function code 값이 들어옴
  # 위의 코드에 따르면 A,B,C 값
ENDFORM.
```

 <br>

리스트의 두번째 값인 Choice 2를 선택했을 때의 HANDLE_USER_COMM에 들어오는 값은 'B' 이다.<br>HANDLE_USER_COMM 확인!<br>

![image](https://user-images.githubusercontent.com/58674365/161677292-9f24c7a0-4667-4835-9355-5e0190bb75f4.png)

<br>

![image](https://user-images.githubusercontent.com/58674365/161677302-dd3121a4-ac8e-46e9-abc8-501927c5680d.png)
