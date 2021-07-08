---
title: "Maintenance view: T-code 생성"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - view
  - maintenanve view
  - clsuter view
  - transaction
  - tcode
toc: true
---

# Maintenance view : T-code 생성

프로그램에 대한 T-code를 생성하는 것처럼 `T-SE11` 에서 veiw를 조회하는 번거로운 방법 대신, 자주 사용하는 view에 대해 transaction code 생성이 가능하다.

- **T-SE93** : Maintain transaction

`T-SE93` 으로 들어가서 만들 transaction code 이름을 입력하고 생성을 눌러준다.<br>여기서는 앞서 만든 view의 이름과 동일하게 만들었다. 

![image](https://user-images.githubusercontent.com/58674365/123924833-4be4c280-d9c5-11eb-8c49-c14a075d9b5b.png)



short text에는 view의 description과 동일하게 넣어주었다. <br>program이 아니고 다른 T-code를 활용할 것이기에 마지막을 체크해준다. 

![image](https://user-images.githubusercontent.com/58674365/123924921-5e5efc00-d9c5-11eb-8890-ed447e16ade5.png)



View를 유지보수할 수 있는 `T-code: SM30` 을 넣어주고, default value, 처음화면을 skip 설정을 넣어주고 저장하면 해당 T-code가 생성된다.

![image](https://user-images.githubusercontent.com/58674365/123925714-2310fd00-d9c6-11eb-838e-12ce8a2fb0d1.png)



![image](https://user-images.githubusercontent.com/58674365/123925903-53589b80-d9c6-11eb-967d-cb8231050c14.png)

