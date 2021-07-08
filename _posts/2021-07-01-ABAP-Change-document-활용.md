---
title: "ABAP Change document 활용"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - changing document
  - maintenance view
  - view
  - clsuter view
toc: true
---

# Change Document 활용

Change document를 기록하기 위해서는 CBO의 데이터 유지보수 시점에서 change document object의 funciton을 호출하여 로그를 작성해줘야 한다. CBO table이 아닌 maintenance view, view cluster의 경우는 각각의 event를 사용하여 특정 시점에 change document funciton을 넣어줘야 되고, 이전 생성한 view를 가지고 실습을 진행해보도록 하자.

<br>

## Maintenance view : Change document 

Maintenance view 의 change document function은 event를 통해 작성한다. <br>  `View > Table Maintenance Generator ` 로 Table dialog 설정으로 이동한다. 

![image](https://user-images.githubusercontent.com/58674365/124080005-ce808700-da84-11eb-9aab-9ce3949bebe4.png)



 Table dialog 설정에서 `Environment > Modification > Events`  설정으로 이동한다. 

![image](https://user-images.githubusercontent.com/58674365/124080018-d2aca480-da84-11eb-928e-76af947654f2.png)



`01. Before save` ,  `02. After save` 에 event를 추가해 작성한다. 

![image](https://user-images.githubusercontent.com/58674365/124080046-da6c4900-da84-11eb-8bfc-48bf0c4d5791.png)



저장 전에 DB 데이터와 비교하여 변경 전 데이터와 변경 후 데이터를 각각 따로 담아놓는다.

```sql
* TOP에 선언한 부분
data: gt_before_main like TABLE OF yZZ061T01 WITH HEADER LINE,
      gt_after_main like TABLE OF yZZ061T01 with HEADER LINE,
      gt_before_text like TABLE OF yZZ061T01t WITH HEADER LINE,
      gt_after_text like TABLE OF yZZ061T01t with HEADER LINE.
```



```sql
FORM BEFORE_SAVE.

* Get origin data from DB
  SELECT A~ZZTYTPE, b~ZZtytpet, A~LOEKZ,
         A~ERDAT, A~ERZET, A~ERNAM,
         A~AEDAT, A~AEZET, A~AENAM
    FROM ZZ061T01 AS A INNER JOIN ZZ061T01T AS B
                       ON A~ZZTYTPE = B~ZZTYTPE
    AND B~SPRAS EQ @SY-LANGU
    INTO TABLE @DATA(LT_ZZ061).

  LOOP AT TOTAL.
  
    ASSIGN COMPONENT 'ACTION' OF STRUCTURE TOTAL
                                 TO <L_FIELD>.

    CASE <L_FIELD>.
      WHEN 'N'.   "신규: 메인 테이블과 텍스트 테이블 모두 LOG UPDATE

        CLEAR: GT_AFTER_MAIN, GT_AFTER_TEXT.
        MOVE-CORRESPONDING TOTAL TO GT_AFTER_MAIN.
        MOVE-CORRESPONDING TOTAL TO GT_AFTER_TEXT.

        GT_AFTER_TEXT-SPRAS = SY-LANGU.
        GT_AFTER_TEXT-MANDT = SY-MANDT.
        GT_AFTER_MAIN-MANDT = SY-MANDT.
        APPEND: GT_AFTER_MAIN, GT_AFTER_TEXT.

      WHEN 'U'.   "수정: 메인테이블과 텍스트 테이블 중 수정된 것만 Log UPDATE

        CLEAR: GT_AFTER_MAIN, GT_AFTER_TEXT, GT_BEFORE_MAIN, GT_BEFORE_TEXT.
        MOVE-CORRESPONDING TOTAL TO GT_AFTER_MAIN.
        MOVE-CORRESPONDING TOTAL TO GT_AFTER_TEXT.
        
        READ TABLE LT_ZZ061 INTO DATA(LS_ZZ061) 
        				    WITH KEY ZZTYtPE = GT_AFTER_MAIN-ZZTYtPE.

        IF SY-SUBRC = 0.
        
          IF GT_AFTER_TEXT-ZZTYTPET NE LS_ZZ061-ZZTYtPET.
            MOVE-CORRESPONDING LS_ZZ061 TO GT_BEFORE_TEXT.
            GT_BEFORE_TEXT-MANDT = SY-MANDT.
            GT_BEFORE_TEXT-SPRAS = SY-LANGU.
            GT_AFTER_TEXT-MANDT = SY-MANDT.
            GT_AFTER_TEXT-SPRAS = SY-LANGU.
            APPEND : GT_AFTER_TEXT,GT_BEFORE_TEXT.
          ENDIF.

          IF GT_AFTER_MAIN-LOEKZ NE LS_ZZ061-LOEKZ.
            MOVE-CORRESPONDING LS_ZZ061 TO GT_BEFORE_MAIN.
            GT_BEFORE_MAIN-MANDT = SY-MANDT.
            GT_AFTER_MAIN-mandt = SY-MANDT.
            APPEND : GT_AFTER_MAIN, GT_BEFORE_MAIN.        
          ENDIF.
          
        ENDIF.
    ENDCASE.
  ENDLOOP.
ENDFORM.
```



그리고 변경 전, 후 데이터 테이블을 가지고 change document function을 호출해주면 된다.<br>테이블/뷰 이름에 따라 case를 나눠서 작성하는 것이 아닌, 어떤 뷰/테이블이 들어오던지 동적으로 테이블을 생성하고 값을 비교하게끔 클래스로 작성해보아도 좋을 듯 하다. 

```sql
FORM AFTER_SAVE.

  IF GT_AFTER_MAIN[] IS NOT INITIAL.
    CALL FUNCTION 'ZMMCDO01_WRITE_DOCUMENT'
      EXPORTING
        OBJECTID     = 'ZZ061T01'
        TCODE        = SY-TCODE
        UTIME        = SY-UZEIT
        UDATE        = SY-DATUM
        USERNAME     = SY-UNAME
        UPD_ZZ061T01 = 'U'
      TABLES
        XZZ061T01    = GT_AFTER_MAIN[]
        YZZ061T01    = GT_BEFORE_MAIN[].
  ENDIF.

  IF GT_AFTER_TEXT[] IS NOT INITIAL.
    CALL FUNCTION 'ZMMCDO01_WRITE_DOCUMENT'
      EXPORTING
        OBJECTID      = 'ZZ061T01t'
        TCODE         = SY-TCODE
        UTIME         = SY-UZEIT
        UDATE         = SY-DATUM
        USERNAME      = SY-UNAME
        UPD_ZZ061T01T = 'U'
      TABLES
        XZZ061T01T    = GT_AFTER_TEXT[]
        YZZ061T01T    = GT_BEFORE_TEXT[].
  ENDIF.

ENDFORM.
```

<br>Change document를 추가한 상태에서 해당 뷰의 데이터를 변경해보자.<br>마지막 줄의 `view type -> view` , `삭제 플레그 체크함 -> 체크 안함` 으로 변경하고 저장했다. 

![image](https://user-images.githubusercontent.com/58674365/124085047-97ad6f80-da8a-11eb-8cf7-28e17052fd3c.png)



Change document 내역은 `T-RSSCD100` 에서 확인 가능하다.

![image](https://user-images.githubusercontent.com/58674365/124083550-f376f900-da88-11eb-9a48-1629aff8bce9.png)



아래 두 로그를 보면 각 필드가 어떤 값에서 어떻게 변경되었는지 확인 가능하다. 

![image](https://user-images.githubusercontent.com/58674365/124084927-78aedd80-da8a-11eb-9d45-a83f215be1fd.png)

![image](https://user-images.githubusercontent.com/58674365/124084954-7e0c2800-da8a-11eb-8fd4-968afb45ffde.png)

