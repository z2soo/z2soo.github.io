---
title: "ABAP Configuration table setting"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - table control
  - field order setting
toc: true
---

## ABAP Configuration table setting

Table 형태로 보여지는 화면에서 필드의 순서의 변경하거나 특정 컬럼을 숨기고 싶은 경우가 있을 것이다. 필드를 드래그해서 순서를 변경하는 방법이 있지만, 이는 개인 사용자에게만 한정되어 적용된다. 전체 사용자에게 해당 설정을 적용하기 위해서는 table 우측 상단의 `Configuration` 을 통해 `Variant` 를 설정해준다. 

![image](https://user-images.githubusercontent.com/58674365/129647877-5d67ea2e-3443-4280-8135-3a917a9e44cc.png)



예를 들어 (T-ME21) Material 컬럼과 Short text 컬럼의 순서를 바꾸기를 원하는 상황이다. <br>두 컬럼을 drag 하여 원하는 순서로 둔다.  그리고, 우측 상단의 작은 아이콘에 커서를 올리면 `Configuration` 이라고 뜨는데 이를 클릭한다. 

![image](https://user-images.githubusercontent.com/58674365/129647933-14dfbad8-e61a-4da2-a093-d196074aa7d1.png)



아래와 같이 Table setting 에 대한 variant 생성 및 설정이 가능한 팝업창이 뜬다. <br>Variant에 원하는 이름을 입력하고 Create를 눌러 새로운 layout을 생성 및 지정해준다. 해당 variant가 user-specific이 아닌, 모든 user 및 프로그램에 적용되기 위해서는 하단의 `Administrator` 를 클릭한다. 

![image](https://user-images.githubusercontent.com/58674365/129682304-450f2b1f-2bb8-4427-870a-d292beae8d38.png)



Administrator 항목에서 각 컬럼에 대한 필드명, 순서, 길이 그리고 숨김설정을 확인할 수 있다. <br>Invisible 항목에 체크하면 해당 컬럼을 숨길 수 있으며, `Further setting`을 통해서는 테이블의 가로/세로 선 설정이 가능하다. 모든 설정이 완료된 후에는 하단의 `Activate` 버튼을 눌러 적용해주고, 이전 창에서 `Save`를 눌러 저장해준다.  

![image](https://user-images.githubusercontent.com/58674365/129682357-8726e3c9-0d20-4a9a-b6a2-6054625e3137.png)



간단하게 컬럼의 순서와 숨김 설정이 가능한 방법이지만, 키 컬럼인 경우를 비롯해 특정 프로그램에서는 (e.x. ME21N의 Overview table) 컬럼 Invisible 설정이 불가한 경우가 있으며, 순서 또한 설정한 것과 다르게 기존 설정으로 돌아오는 경우도 있다. <br>또한, transport request를 딴 후 다른 서버로 cts를 넘겼을 때, 해당 variant가 general 하게 적용되지는 않았고 user-specific 하게 variant가 보여졌다. (variant 생성 및 cts 따서 넘긴 user) 그리고 다시 `Administrator` 에서 Activate 해줘야 전반적으로 적용되었다. 

