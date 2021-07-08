---
title: "Maintenance view: Event 활용"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - view
  - maintenanve view
  - clsuter view
  - event
toc: true
---

# Maintenance view : Event 활용

Event는 view에 대한 데이터 및 상태 변화에 따라 추가 로직을 작성할 수 있게 한다. <br>예를 들어, Maintenance view 를 초기 생성하면 아래와 같이 데이터를 생성 및 수정할 때, `생성자/날짜/시간` 및 `수정자/날짜/시간` 값에 대한 저장이 이루어지지 않는다. 이에 대한 로직은 Event를 활용하여 추가해줘야 한다.<br>

그렇다면, view의 데이터 수정 및 생성에 대한 사용자, 날짜, 시간 값을 넣는 event를 구현해보도록 하자.

![image](https://user-images.githubusercontent.com/58674365/123922966-83eb0600-d9c3-11eb-9785-78efe1016d3d.png)

<br>

## Before save event 

### Event 생성

Event를 생성할 view에 들어가서 상단의 `Utility > Table Maintenance Generator` 로 들어간다.

![image](https://user-images.githubusercontent.com/58674365/123928441-df6bc280-d9c8-11eb-9c4f-c2827d2b8a52.png)



해당 Dialog 설정 화면에서 상단의 `Environment > Modification > Events` 로 들어간다. <br>이 때, 수정 모드 상태로 들어가야 된다.

![image](https://user-images.githubusercontent.com/58674365/123928457-e4307680-d9c8-11eb-8591-d3d127e093a7.png)



해당 view에 대한 event를 관리하는 화면이다. <br>event는 다양한 시점에 따라 다양하게 생성 가능하다. 그리고 event로 사용하기 위해 생성한 perfom 문은 Maintenance view dialog generation 시에 할당한 Function group에 생성된다. 

![image](https://user-images.githubusercontent.com/58674365/123928574-ff9b8180-d9c8-11eb-976d-dc637cd80f02.png)



다양한 event 시점 중 **01. Before saving the data in the database** 를 선택하여 생성하자.<br>Form의 이름은 원하는 이름으로 생성해주면 된다.<br>그 후, 우측 버튼을 눌러 코드를 작성하러 가보자.

![image](https://user-images.githubusercontent.com/58674365/123928618-0a561680-d9c9-11eb-8b44-a389554e288c.png)

![image](https://user-images.githubusercontent.com/58674365/123930817-0a571600-d9cb-11eb-912d-ed892449efcc.png)

<br>

### Event 작성

view에 대한 데이터는 `<total>`,` <extract>` 에서 받아올 수 있다. <br>아래 코드는 데이터 생성, 변경에 따라 사용자, 날짜, 시간을 저장 전 view에 값을 넣도록 한다. <br>데이터를 삭제하는 부분에 대한 case는 따로 나누지 않았는데, 이는 일반적으로 사용자가 값을 생성 및 수정하는 것은 가능하지만 완전히 DB에서 데이터를 날려버리는 것이 위험하여 삭제 기능을 막기 때문에 삭제에 대한 고려가 불필요하기 때문이다. <br>DB에서 직접 삭제하는 대신 삭제 필드 flag를 사용한다. 

```abap
FORM BEFORE_SAVE.

  CONSTANTS : l_name TYPE string VALUE 'N_____'.
  DATA : l_index LIKE sy-tabix.
  DATA : ls_component TYPE abap_componentdescr,
         lt_component TYPE abap_component_tab,

         lr_structure TYPE REF TO cl_abap_structdescr,
         lr_handle    TYPE REF TO data,
         lr_before    TYPE REF TO data,
         lr_change    TYPE REF TO data,
         lr_move      TYPE REF TO data.

  FIELD-SYMBOLS : <l_structure> TYPE any,
                  <l_field>     TYPE any,
                  <ls_change>   TYPE any,
                  <lv_view>     TYPE any.
  
*-- Get data
  lr_structure  ?=
     cl_abap_structdescr=>describe_by_name( x_header-viewname ).
  lt_component = lr_structure->get_components( ).
  ls_component-type ?= cl_abap_datadescr=>describe_by_data( <action> ).  
  ls_component-name = l_name.
  APPEND ls_component TO lt_component.

  lr_structure = cl_abap_structdescr=>create( lt_component ).

  CREATE DATA lr_handle TYPE HANDLE lr_structure.
  ASSIGN lr_handle->* TO <l_structure>.
  
  
*-- Set user, time, date
  LOOP AT total.
    
    IF <action> = neuer_eintrag OR <action> = aendern.
      
      READ TABLE extract WITH KEY <vim_xtotal_key>.
      
      IF sy-subrc EQ 0.
        l_index = sy-tabix.
      ELSE.
        CLEAR l_index.
      ENDIF.

      CHECK l_index GT 0.
      MOVE-CORRESPONDING total TO <l_structure>.

      CASE <action>.       
        WHEN aendern. "Change/Update
          ASSIGN COMPONENT 'AEDAT' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE sy-datum TO <l_field>.
          ENDIF.

          ASSIGN COMPONENT 'AEZET' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE sy-uzeit TO <l_field>.
          ENDIF.

          ASSIGN COMPONENT 'AENAM' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE sy-uname TO <l_field>.
          ENDIF.

        WHEN neuer_eintrag. "New Entries
          ASSIGN COMPONENT 'MANDT' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE sy-mandt TO <l_field>.
          ENDIF.

          ASSIGN COMPONENT 'ERDAT' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE sy-datum TO <l_field>.
          ENDIF.

          ASSIGN COMPONENT 'ERZET' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE sy-uzeit TO <l_field>.
          ENDIF.

          ASSIGN COMPONENT 'ERNAM' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE sy-uname TO <l_field>.
          ENDIF.

          ASSIGN COMPONENT 'AEDAT' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE space TO <l_field>.
          ENDIF.

          ASSIGN COMPONENT 'AEZET' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE  space TO <l_field>.
          ENDIF.

          ASSIGN COMPONENT 'AENAM' OF STRUCTURE <l_structure>
                                             TO <l_field>.
          IF sy-subrc = 0.
            MOVE space TO <l_field>.
          ENDIF.
      ENDCASE.

      MOVE-CORRESPONDING <l_structure> TO total.
      MODIFY total.
      extract = total.
      MODIFY extract INDEX l_index.
    ENDIF.
  ENDLOOP.

ENDFORM.
```



### Event 결과

event 생성 전에는 create, change에 대한 정보가 추가되지 않았지만, 위의 event를 추가한 후에는 아래와 같이 데이터 유지보수에 대한 `사용자/날짜/시간` 정보가 저장된다.

![image](https://user-images.githubusercontent.com/58674365/123934313-20b2a100-d9ce-11eb-9482-1a53a3301b32.png)

