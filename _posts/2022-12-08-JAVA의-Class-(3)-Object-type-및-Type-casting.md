---
title: "JAVA의 Class (3) Object type 및 Type casting"
categories: 
  - JAVA
tags:
  - JAVA
  - Class
  - Method
  - 객체지향
  - Type casting
toc: true
---

## **Lesson point**

> 1. 클래스를 ArrayList에 담으면 특정 클래스 타입으로 저장되지 않음 (무조건 Object 타입)
> 2. 타입 캐스팅 방법 : (To-Be 타입)대상 변수 또는 객체명

<br>

## **Object type**

코드 내용은 이전 포스팅을 참조하자. [JAVA의 Class (2) 생성자 Constructor](https://z2soo.github.io/java/JAVA%EC%9D%98-Class-(2)-%EC%83%9D%EC%84%B1%EC%9E%90-Constructor/)

생성한 Coffee01 이라는 클래스를 가지고 Coffee01 타입의 객체를 생성했다. (아래 코드의 c 객체)

생성한 객체를 ArrayList 일명 컨테이너에 add를 사용하여 넣어준 후, get으로 다시 가져와본다. 출력을 해도 해당 객체에 대한 정보 id 값이 출력될 뿐, 해당 객체에 저장된 내용 (ex. 아이스커피, 3500, true) 값이 출력되지는 않는다. 

![img](https://blog.kakaocdn.net/dn/58AOY/btrUE5NqQ16/r2DQRXwOpWeL5KGm5ufoNK/img.png)

<br>

객체에 있는 정보를 출력하기 위해 putter를 사용하려고 한다. 하지만 아래의 코드로 putter를 불러올 수 없다. 

```
// 컨테이너에 저장된 0번째 객체인 Coffee01 클래스의 putter를 불러오려고 한다
container.get(0)).putType();
```

<br>

그 이유는 container.get(0) 으로 불러온 클래스가 Coffee01 타입이 아닌 Object type으로 저장되었기 떄문이다.

해당 객체의 메소드에 접근하려면 아래와 같이 앞에 클래스 타입을 명시하여 type casting을 해주도록 하자. 

![img](https://blog.kakaocdn.net/dn/cPAHPG/btrUHOxA3oW/9iFmAmmN44LZiPTQg7tf1k/img.png)