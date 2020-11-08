---
title: "ABAP Enhancement category error"
categories: 
  - ABAP
tags:
  - abap
  - sap
  - error
toc: true
---

## 1. Enhancement category error 발생 

테이블을 생성한 후 실행하였을 때, 다음과 같 Enhancement category error가 발생하는 경우가 있다.  이는 테이블의 append, include 기능을 사용하기 위한 유형을 지정하라는 메세지다.<br>

![image](https://user-images.githubusercontent.com/58674365/96694683-b7e94280-13c3-11eb-92e3-e010d2725b16.png)

<br><br>

## 2. Enhancement category error 해결

- 메뉴 > Extra > Enhancement category > 유형선택

해당 에러를 해결하기 위해서는 테이블의 enhancement 유형을 선택해줘야 한다.<br>

![image](https://user-images.githubusercontent.com/58674365/96694904-faab1a80-13c3-11eb-9ffa-14528be4b347.png)

<br>

![image](https://user-images.githubusercontent.com/58674365/96694940-0565af80-13c4-11eb-8a4c-5a1959e06e42.png)

<br>

### Enhancement category 유형

| 유형                                             | 내용                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Cannot Be Enhanced                               | Append, Include 기능 사용 불가                               |
| Can be enhanced<br />(Character-type)            | Append, Include 기능 사용Character 타입을 가진 필드로만 확장 가능 |
| Can be enhanced<br />(Character-type or numeric) | Append, Include 기능 사용Character, 숫자 타입 사용 가능      |
| Can be Enhanced<br />(Deep)                      | Append, Include 기능 사용<br />모든 데이터 타입 사용 가능    |



