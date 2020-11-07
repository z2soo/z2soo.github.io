---
stitle: "ABAP Field catalog 최적화 클래스 개발"
categories: 
  - ABAP
tags:
  - abap
  - abap study
  - sap
  - field catalog
  - dynamic table
toc: true
---

## 1. Field catalog

필드 카탈로그는 ALV에서 조회되는 칼럼들의 필드 정보를 포함하는 slis_t_fieldcat_alv 타입의 테이블 구조를 가진다. ALV는 필드 카탈로그 정보를 저장하는 internal table을 이용해 필드 타입을 인식하게 된다. 예를 들어, 필드의 타입과 화면에 보여줄 필드 길이를 지정할 수 있다. 그리고 체크박스, 라디오 버튼으로 보이게 하고, 필드 수정이 가능하게 하는 등 많은 역활을 하게 된다.

<br>

### Field catalog 생성 방법

Grid를 이용힌 ALV와 같이 필드 카탈로그를 정의하는 방법은 3 가지가 있다. 마지막 방법의 경우 ABAP Dictionary 오브젝트 구조체와 필드 카탈로그에 같은 필드가 존재하게 되면, 필드 카탈로그에 정의한 필드가 높은 우선순위를 가진다.

- ABAP Dictionary 오브젝트를 이용해 자동으로 구성하는 방법

- 프로그램 내에서 스크립트로 수동으로 구성하는 방법 (필드 카탈로그)

- 위의 두 방법을 혼합하여 사용하는 방법

  <br>

### 1) 자동 구성

ALV를 출력하는 메소드를 사용할 때, `I_STRUCTURE_NAME`에 구조명을 넣어서 구조와 똑같이 필드 카탈로그를 구성한다. 이런 식으로 구성하면 `MARA` 테이블과 동일한 구조의 필드 카탈로그가 자동 생성되며, 사용하는 구조는 `TABLES`로 선언되어 있어야 한다. 

```sql
CALL METHOD g_alv->set_table_foe_first_display
	EXPORTING
		i_struacture_name		=		'MARA'
```

<br>

### 2) 수동 구성

ABAP Dictionary 오브젝트 중 구조체 하나 전체가 아닌 부분적으로 특정 필드만 ALV에 구현하고 싶은 경우 필드를 하나씩 입력하여 필드 카탈로그를 생성하는 방법이다. `IT_FIELDCATALOG`를 사용한다. 
이 때, 수동으로 구성하는 필드 카탈로그는 ALV 출력 구문 전에 넣어줘야 한다.

```sql
*1. 필드 카탈로그 속성을 가진 구조를 참조하는 구조체를 선언
DATA: gs_fieldcat TYPE lvc_s_fcat,
      gt_fieldcat TYPE lvc_t_fcat.
      
*2. 필드 카탈로그 구성      
gs_fieldcat-fieldname = 'VBELN'.	#필드명
gs_fieldcat-coltext = '판매문서'. 	 #컬럼명 설정
gs_fieldcat-just = 'C'. 			#가운데 정렬
gs_fieldcat-key = 'X'. 				#키값 고정
gs_fieldcat-hotspot = 'X'. 			#핫스팟
APPEND gs_fieldcat TO gt_fieldcat.  
CLEAR: gs_fieldcat.

gs_fieldcat-fieldname = 'NETWR'.
gs_fieldcat-coltext = '정가'.
gs_fieldcat-just = 'C'.
gs_fieldcat-currency = 'KRW'.		 #통화필드 하드코딩
gs_fieldcat-cfieldname = 'WAERK'. 	 #참조한 통화단위에 대한 필드이름, 통화필드 참조해서 출력
APPEND gs_fieldcat TO gt_fieldcat. 
CLEAR gs_fieldcat.      
      
*3. ALV 출력 메소드에 필드 카탈로그 구조 사용
CALL METHOD g_alv->set_table_foe_first_display
	CHANGING
		it_outtab			=		gt_tab
		it_fieldcatalog		=		gt_fieldcat
```

<br>

### 3) Function 사용

- LVC_FIELDCATALOG_MERGE: DB Dictionary 구성원 중 구조체로부터 Field Catalog를 생성

위에서는 수동으로 구성한 field catalog를 `LVC_FIELDCATALOG_MERGE` 함수를 사용하여 생성해준다. `LVC_FIELDCATALOG_MERGE` function으로는 ABAP Dictionary 구조체만 merge가 가능하하고, Internal table은 merge가 불가능하다. Internal table merge를 위해서는 `REUSE_ALV_FIELDCATALOG_MERGE`를 사용해야 한다. 

다음 코드를 작성하여 디버깅하면 `LVC_FIELDCATALOG_MERGE`에 대한 감이 잡힐 것이다. 
참고로 `REUSE_FIELDCATALOG_MERGE`랑 기능은 유사하나 다른 함수임에 유의하자!

```sql
# LVC_FIELDCATALOG_MERGE function에 대한 이해를 돕기 위한 코드 

SELECTION-SCREEN BEGIN OF BLOCK SEL_BLOCK WITH FRAME TITLE FRM_TITL.
  PARAMETERS : PA_TAB TYPE DD02L-TABNAME OBLIGATORY.
SELECTION-SCREEN END OF BLOCK SEL_BLOCK.

DATA: GT_FIELDCAT TYPE LVC_T_FCAT,
      GS_FIELDCAT TYPE LVC_S_FCAT.

CALL FUNCTION 'LVC_FIELDCATALOG_MERGE'
  EXPORTING
    I_STRUCTURE_NAME  = PA_TAB
  CHANGING
    CT_FIELDCAT       = GT_FIELDCAT.
```

단, `LVC_FIELDCATALOG_MERGE`는 class용 (`LVC_T_FCAT`)이고, `REUSE_ALV_FIELDCATALOG_MERGE`는 function용 (`SLIS_T_FIELDCAT_ALV`)이라 두 개 구조가 다르다. 따라서 class로 구현하는 ALV에서 `REUSE_ALV_FIELDCATALOG_MERGE`를 사용하려면 conversion 작업이 필요하다. `LVS_TRANSFER_FROM_SLIS`  function을 이용해 conversion 작업 (function용 fcat → class용 fcat)을 해주고, `LVC_FIELDCAT_COMPLETE ` function으로 마무리해주면 된다.

```sql
# 실제 LVC_FIELDCATALOG_MERGE function을 사용해 field catalog를 생성하는 코드

CLEAR   : gt_fieldcat.
  REFRESH : gt_fieldcat.

  CALL FUNCTION 'LVC_FIELDCATALOG_MERGE'
    EXPORTING
      i_structure_name       = 'VBRk'
    CHANGING
      ct_fieldcat            = gt_fieldcat
    EXCEPTIONS
      inconsistent_interface = 1
      program_error          = 2
      OTHERS                 = 3.
```

만약 특정 필드에 추가 설정을 하고 싶다면 다음과 같은 구문으로 추가해주면 된다. 

```sql
 LOOP AT gt_fieldcat into gs_fieldcat.
    IF gs_fieldcat-fieldname = 'VBELN'.
      gs_fieldcat-coltext = '판매문서번호'.
      gs_fieldcat-hotspot = 'X'.
      MODIFY gt_fieldcat FROM gs_fieldcat.
    ENDIF.
  ENDLOOP.
```

<br><br>

## 2. Field catalog 최적화 클래스 개발

Field catalog의 필드명을 하나씩 직접 설정해줘야 한다는 것에 번거로움을 느껴서 찾아보니 DB table 혹 internal table을 가지고 자동 field catalog 생성을 해주는 function이 존재했다. 그래서 이 method들을 가지고 field catalog 생성과 관련된 클래스를 하나 생성해보려고 한다. <br>

### 1) Class / Interface 생성

Field catalog를 생성하는 최적화 클래스를 T-Code SE80에서 생성해준다. 

![image](https://user-images.githubusercontent.com/58674365/98358260-c2fdcd00-2069-11eb-84d6-6cddb140abcb.png)

<br>

### 2) Attribute 및 Method 생성

 Class 내에 속한 method와 method 내에서 사용할 변수를 생성해준다. 

![image](https://user-images.githubusercontent.com/58674365/98358984-de1d0c80-206a-11eb-8db8-32125508cf96.png)<br>

![image](https://user-images.githubusercontent.com/58674365/98358772-8ed6dc00-206a-11eb-9c28-d13d5d469d28.png)<br>

![image](https://user-images.githubusercontent.com/58674365/98358953-d198b400-206a-11eb-8ef0-3b6c9dd28cb8.png)

<br>

### 3) Method - LVC_FIELD_CATALOG

`LVC_FIELD_CATALOG` 를 활용하여 DB Table의 정보로 field catalog를 자동 생성해주는 method를 만들어본다. 

위에서 한 번에 method를 정의해 줬지만, 하나씩 생성한 후 정상적으로 실행되는지 확인해주고 다음 method를 생성해줘야 한다. 참고로 class는 T-Code SE80 외에도 class builder에서도 생성 및 조회가 가능하다. 

- T-Code SE24: Class Builder

![image](https://user-images.githubusercontent.com/58674365/98429011-9092b500-20e7-11eb-9bbb-da5c1fc79246.png)<br>

![image](https://user-images.githubusercontent.com/58674365/98429030-a7d1a280-20e7-11eb-80fe-c9496974a868.png)

<br>

source code에 들어가서 다음 코드를 작성해준다. 
위에서 `GT_FCAT`, `GS_FCAT` 을 attribute로 설정해주었기 때문에 이에 대한 오류는 없지만, `i_db_table`, `e_field_catalog` 에 대한 설정은 없기 때문에 오류가 난다. 

```sql
method LVC_FIELD_CATALOG.
  
    clear : gt_fcat.

    CALL FUNCTION 'LVC_FIELDCATALOG_MERGE'
      EXPORTING
        i_structure_name       = i_db_table
*       I_INTERNAL_TABNAME     = l_table
      CHANGING
        ct_fieldcat            = gt_fcat
      EXCEPTIONS
        inconsistent_interface = 1
        program_error          = 2
        OTHERS                 = 3.

    IF sy-subrc EQ 0.
      e_field_catalog = gt_fcat.
    ENDIF.
    
endmethod.
```

![image](https://user-images.githubusercontent.com/58674365/98429120-2fb7ac80-20e8-11eb-99dc-bf9dfe7b5f98.png)

따라서 method의 parameters 설정으로 들어가서 다음과 같이 importing, exporting parameter를 설정해준다. Associated type으로는 `LVC_FIELDCATALOG_MERGE` method가 실제 사용하는 type을 찾아서 넣어준다. 

![image](https://user-images.githubusercontent.com/58674365/98429154-61307800-20e8-11eb-92c8-114e05663618.png)

특정 function에 대해 알고 싶다면, 다음과 같이 접근해주면 된다. 

- T-Code SE37: Function Builder

![image](https://user-images.githubusercontent.com/58674365/98429240-d7cd7580-20e8-11eb-92ee-3d7c2900f6b6.png)

해당 method가 제대로 생성되었는지 DB table 예시로 `SFLIGHT` 을 넣어 실행해 확인해보자. 

![image](https://user-images.githubusercontent.com/58674365/98431821-2c2e2080-20fc-11eb-8a1a-31d3696e608d.png)<br>

![image](https://user-images.githubusercontent.com/58674365/98429436-e5372f80-20e9-11eb-9e73-0c50dcaac8d6.png)<br>

![image](https://user-images.githubusercontent.com/58674365/98431858-816a3200-20fc-11eb-8662-0ab87034f8ce.png)<br>

![image](https://user-images.githubusercontent.com/58674365/98431854-77483380-20fc-11eb-9061-2ea62b5e22db.png)

실행한 결과 DB SFLIGHT 테이블 필드를 가지고 field catalog가 생성되었음을 확인할 수 있다. 이 결과 값이 `LVC_FIELD_cATALOG` function  실행 결과와 동일한 값인지 알기 원한다면 T-Code SE37 에서 function을 실행하여 대조해보면 된다.

<br>

### 4) Method - REUSE_FIELD_CATALOG

`REUSE_FIELD_cATALOG` 를 활용해 header line이 있는 internal table로 field catalog가 자동 생성되는 method를 만들어본다. 

```sql
method REUSE_FIELD_CATALOG.

# Header Line이 있는 internal table 이름을 exporting parameter에 기술
# LVC_T_FCAT 데이터 타입의 internal table을 선언      
    
    DATA: gs_sflight TYPE          slis_fieldcat_alv,
          gt_sflight TYPE          slis_t_fieldcat_alv.
    DATA: gt_scarr   TYPE TABLE OF sflight.
    
    CLEAR: GT_FCAT.
    
    CALL FUNCTION 'REUSE_ALV_FIELDCATALOG_MERGE'
      EXPORTING
        i_program_name         = sy-cprog
        i_internal_tabname     = i_internal_table
        i_inclname             = sy-cprog
      CHANGING
        ct_fieldcat            = gt_fieldcat
      EXCEPTIONS
        inconsistent_interface = 1
        program_error          = 2
        OTHERS                 = 3.

    IF sy-subrc EQ 0.

      CLEAR gs_fieldcat.
      LOOP AT gt_fieldcat INTO gs_fieldcat.
        gs_fcat-fieldname  = gs_fieldcat-fieldname.
        gs_fcat-col_pos    = gs_fieldcat-col_pos.
        gs_fcat-row_pos    = gs_fieldcat-row_pos.
        gs_fcat-ref_table  = gs_fieldcat-ref_tabname.
        gs_fcat-ref_field  = gs_fieldcat-ref_fieldname.
        gs_fcat-intlen     = gs_fieldcat-intlen.
        gs_fcat-inttype    = gs_fieldcat-inttype.
        gs_fcat-datatype   = gs_fieldcat-datatype.
        gs_fcat-checktable = gs_fieldcat-checkbox.
        gs_fcat-scrtext_l  = gs_fieldcat-seltext_l.
        gs_fcat-scrtext_s  = gs_fieldcat-seltext_s.
        gs_fcat-scrtext_m  = gs_fieldcat-seltext_m.
        APPEND gs_fcat TO gt_fcat.
      ENDLOOP.

      e_field_catalog = gt_fcat.
    ENDIF.
    
  endmethod.
```

![image](https://user-images.githubusercontent.com/58674365/98432310-5d105480-2100-11eb-8e31-6dc70a342af4.png)<br>
![image](https://user-images.githubusercontent.com/58674365/98432319-6699bc80-2100-11eb-9834-e027ab9d0dba.png) 

마찬가지로 attributes와 parameter를 설정해줘야 한다.





<br>

### 5) Method - SET_DYNAMIC_INTERNAL_TABLE

사용자가 입력하는 테이블명에 따라 자동으로 internal table을 구성하는 method이다. 
Dynamic table 구현에 대한 추가 내용은 이전 Dynamic table 구현 글을 참조하도록 한다. 

```sql
method SET_DYNAMIC_INTERNAL_TABLE.

     FIELD-SYMBOLS : <ft_table> TYPE ANY TABLE,
                    <fs_table> TYPE any.

    CALL METHOD cl_alv_table_create=>create_dynamic_table
      EXPORTING
        it_fieldcatalog = i_fieldcat
      IMPORTING
        ep_table        = et_table.


    ASSIGN et_table->* TO <ft_table>.

    CREATE DATA es_table LIKE LINE OF <ft_table>.
    ASSIGN es_table->* TO <fs_table>.


#여기까지가 internal table 발췌부분

endmethod.
```





