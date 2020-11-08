---
title: "Fiori view 및 이동버튼 생성"
categories: 
  - FIORI
tags:
  - abap
  - fiori
  - scp
  - javascript
  - sn4hana
  - sap
toc: true
---

## 1. View 생성 

### 1) View 생성

생성된 프로젝트 좌측 툴바를 보면 자동적으로 생성된 파일을 확인할 수 있다. 이 중, view 부분은 사용자에게 보여지는 부분으로써 view 폴더 하위에 존재하는 view 파일과 동일한 명으로 controller 폴더 하위에도 자동적으로 생성되어 존재하게 된다. 기존에 default로 생성된 view 외에 새로운 view를 만들어서 확인해보자. 

![image](https://user-images.githubusercontent.com/58674365/95154843-bb7aa800-07cd-11eb-8b09-797675feab29.png)
![image](https://user-images.githubusercontent.com/58674365/95154854-c2a1b600-07cd-11eb-9795-b0330d8b95c2.png)

<br>

생성된 View를 가지고 간단한 기능만 구현해보자. 우선 편집하려는 View 파일을  우클릭하여 Open Layout Editor로 열어준다. 그러면 코드가 아닌 툴바로 작업 가능한 화면이 나온다.

![image](https://user-images.githubusercontent.com/58674365/95154887-d816e000-07cd-11eb-9edd-e7932711821f.png)
![image](https://user-images.githubusercontent.com/58674365/95154917-e402a200-07cd-11eb-8c88-6ea46e4657bc.png)

<br>

### 2) View 생성 확인

Label을 추가해서 Hello world! 를 입력하고, view 생성이 제대로 되었는지 확인해본다. 

![image](https://user-images.githubusercontent.com/58674365/95155043-3479ff80-07ce-11eb-9130-732d29ed2456.png)<br>

실행시 다음과 같은 화면이 나온다면 제대로 연결되었으니 계속 진행한다.  

![image](https://user-images.githubusercontent.com/58674365/95156907-a0f6fd80-07d2-11eb-8979-3fd88050892d.png)

<br><br>

## 2. 이동버튼 생성 

우선 버튼을 하나 넣어주고, 해당 버튼을 누른 상태에서 우측에 뜨는 설정 값 중 Icon을 클릭한다. 버튼을 누르면 다른 창으로 이동하는  action을 취하게끔 할 것이니 action icon을 추가해준다. 

- 버튼 생성 > Properties > Icon action 추가 > Event > Press > Navigate to  설정 

<br>원래 Button은 화살표 방향이 없었는데,  
action을 추가함으로써 이동 의미의 화살표 icon이 추가되었다. 

![image](https://user-images.githubusercontent.com/58674365/95155253-b10cde00-07ce-11eb-9976-16a069d1760f.png)<br>이동 버튼 클릭시 어디로 이동하는지 설정해줘야 한다. 해당 버튼 위에서 생성한 View2로 이동하게 생성해준다. 해당 버튼을 누르고 우측 설정 창 중 Event > Press > Navigate to 를 통해 설정해준다.

![image](https://user-images.githubusercontent.com/58674365/95155301-ceda4300-07ce-11eb-86c3-1ad7ca76be5b.png)

![image](https://user-images.githubusercontent.com/58674365/95155350-eadde480-07ce-11eb-90c3-74cde7bc02fb.png)<br>Navigate to 설정은 다음과 같이 View2로 설정해준다. 

![image](https://user-images.githubusercontent.com/58674365/95156945-b66c2780-07d2-11eb-807e-2b853da50517.png)

<br>

실행하면 다음과 같이 버튼이 생성되고 클릭하면 View2 창으로 이동한다. 

![image](https://user-images.githubusercontent.com/58674365/95157000-da2f6d80-07d2-11eb-9b28-784a2d9b69c6.png)