---
title: "Fiori Input label (Demo kit)"
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

## 1. Input label

[Sap Demo Kit][https://sapui5.hana.ondemand.com/#/controls] 사이트에서 필요한 기능에 대한 부분을 가져와서 사용할 수 있다. 사이트에 들어가서 Sample에서 Label을 검색한다. 

![image](https://user-images.githubusercontent.com/58674365/95158421-84f55b00-07d6-11eb-84c4-a92e498781fe.png)

<br>이 중 label with input을 눌러준다. 

![image](https://user-images.githubusercontent.com/58674365/95158435-8cb4ff80-07d6-11eb-93dc-8dea210c3805.png)

<Br>download 옆 source 보기를 누르면 해당 소스를 확인할 수 있다.
필요한 부분만 복사해온다. 

![image](https://user-images.githubusercontent.com/58674365/95180720-550d7e00-07fd-11eb-962e-4823e0ca2651.png)

<br>

여기서는 두 번째 단락을 복사해온다.
이전에 만든 project1의 view1에 들어가서 넣어준다. 

```javascript
	<Label
			text="Label B (bold)"
			labelFor="input-b"
			design="Bold" />
		<Input id="input-b" />
```

![image](https://user-images.githubusercontent.com/58674365/95181176-eaa90d80-07fd-11eb-837b-80479466df1c.png)

<br>

적용시 다음과 같은 화면을 확인할 수 있다. 

![image](https://user-images.githubusercontent.com/58674365/95181197-f3014880-07fd-11eb-8cbd-65104ad66815.png)<br>

![image](https://user-images.githubusercontent.com/58674365/95181254-06141880-07fe-11eb-81cc-bf47ebd5a377.png)<br>
![image](https://user-images.githubusercontent.com/58674365/95181293-0f04ea00-07fe-11eb-8139-8834c82de9d7.png)