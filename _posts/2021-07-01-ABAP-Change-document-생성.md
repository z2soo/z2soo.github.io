---
title: "ABAP Change document 생성"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - changing document
toc: true
---

# Change Document 생성

CBO 테이블을 생성했을 때, 이에 대한 데이터 변경 이력을 필요로 하는 경우가 있다. <br>이를 위해 사용하는 것이 `Change document` 이다. `change document` 에 변경 이력이 들어가게 하기 위해서는 우선적으로 확인해야 하는 부분이 있다. 

**Change log를 남기고자 하는 테이블의 필드 값에 대한 element에 Change document 설정이 필수!**

이력을 남기고자 하는 테이블의 필드에 대해 해당 element의 설정에 아래와 같이 체크되어있어야 한다. 

![image](https://user-images.githubusercontent.com/58674365/124055294-949c8a00-da5e-11eb-99fd-09d2078e850d.png)

<br>

## Change document object 생성

Change document log는 function을 통해 기록되는데, 이를 위해 우선 아래 T-code에서 change document object를 생성 및 generate 해준다.

- T-SCDO : Change document object

![image](https://user-images.githubusercontent.com/58674365/124050054-b133c480-da54-11eb-8bf3-5efe57504a2a.png)



Change document를 생성할 테이블을 추가해준다. <br>이전 포스팅에서 생성한 `ZZ061T01` , `ZZ061T01T` 외에 동일하게 main - text table로 생성한 테이블을 넣어주었다. main 테이블의 값, text 테이블의 description 변경을 로그로 남기고자 한다. 

![image](https://user-images.githubusercontent.com/58674365/124066166-26ae8d80-da73-11eb-9dbf-97f1c181dd34.png)



상단의 `Generate` 를 눌러준다. 

![image](https://user-images.githubusercontent.com/58674365/124066127-15658100-da73-11eb-8331-e8f2d849e709.png)



해당 Change document object 가 생성될 Function group을 지정해주고 Generate 해준다.<br>이 때, Function module이 이미 존재하는 Function group이면 Generate 되지 않으니 새로운 Funciton group을 생성해줘야 한다. 

![image](https://user-images.githubusercontent.com/58674365/124063382-ae919900-da6d-11eb-97cf-8ab399bd366a.png)



Generate 되어진 후, Activate를 잊지 말고 해주도록 하자. 

![image](https://user-images.githubusercontent.com/58674365/124066387-84db7080-da73-11eb-955a-d13c0032c17e.png)

<br>

## Change document : write fuction

Change document object는 생성되었지만, 해당 테이블의 데이터가 유지보수 되는 시점에서 위 object의 function module을 사용해 document에 기록을 해줘야 변경 로그가 저장이 된다. 

change document object 생성에 자동적으로 생성된 funciton module은 다음과 같다.<br>데이터가 유지보수되는 시점에 작성하면 된다 하였는데, maintenance view와 view cluster에서는 어떻게 change document를 작성하는지 이후에 다루도록 하겠다.

```ABAP
  CALL FUNCTION 'ZMMCDO01_WRITE_DOCUMENT'
    EXPORTING
      OBJECTID                      = ZZ061T01 "change document 기록할 대상 테이블
      TCODE                         = SY-TCODE
      UTIME                         = SY-UZEIT
      UDATE                         = SY-DATUM
      USERNAME                      = SY-UNAME
*     PLANNED_CHANGE_NUMBER         = ' '
*     OBJECT_CHANGE_INDICATOR       = 'U'
*     PLANNED_OR_REAL_CHANGES       = ' '
*     NO_CHANGE_POINTERS            = ' '
*     UPD_ICDTXT_ZMMCDO01           = ' '
*     UPD_ZZ061T01                  = ' '
*     UPD_ZZ061T01T                 = ' '
*     UPD_ZZ061T02                  = ' '
*     UPD_ZZ061T02T                 = ' '
*     UPD_ZZ061T03                  = ' '
*     UPD_ZZ061T03T                 = ' '
    TABLES
      ICDTXT_ZMMCDO01               =
      XZZ061T01                     = "로그 작성할 XZZ061T01의 변경 후 테이블
      YZZ061T01                     = "로그 작성할 XZZ061T01의 변경 전 테이블
*     XZZ061T01T                    =
*     YZZ061T01T                    =
*     XZZ061T02                     =
*     YZZ061T02                     =
*     XZZ061T02T                    =
*     YZZ061T02T                    =
*     XZZ061T03                     =
*     YZZ061T03                     =
*     XZZ061T03T                    =
*     YZZ061T03T                    =
            .
```










