---
title: "Purchase Order 화면 호출 (조회/변경 모드)"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - ME21N
  - ME22N
  - ME23N
  - Purchase Order
toc: true
---

<br>

# Purchase Order 화면 호출

Purchase Order 화면을 호출하는 방법은 2가지가 있다.<br>일반적으로 타 transaction을 호출할 때 사용하는 것처럼 1) CALL TRANSACTION을 활용하는 방법과 2) 특정 펑션을 이용하는 방법이다. 

조회 모드로 조회시, CALL TRANSACTION 보다는 아래 펑션을 활용하기를 추천한다. 

- CALL TRNASACTION 활용
- Function 활용



### CALL TRANSACTION 활용

```ABAP
SET PARAMETER ID 'BES' FIELD '오더번호'.
CALL TRANSACTION 'ME23N'.
```



### Function 활용

```ABAP
    CALL FUNCTION 'ME_DISPLAY_PURCHASE_DOCUMENT'
      EXPORTING
        i_ebeln              = 오더번호          " Document Number
        i_ebelp              = 품목번호          " Document Item
*       i_enjoy              = 'X'              " Checkbox
*       i_preview            =                  " Checkbox
*       i_data_from_buffer   =                  " Checkbox
*       i_bstyp              =                  " Document Category
        i_display_only       = 조회모드시 체크  " Checkbox
*       i_edit               = space            " Change
*       i_etenr              =                  " Delivery Schedule Line Counter
*       i_doc_initiator      =                  " Initiator
*       i_commit_wait        =                  " Checkbox
      EXCEPTIONS
        not_found            = 1
        no_authority         = 2
        invalid_call         = 3
        preview_not_possible = 4
        OTHERS               = 5.
```
