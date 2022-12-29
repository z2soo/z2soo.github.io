---
title: "JAVA의 Class (2) 생성자 Constructor
categories: 
  - JAVA
tags:
  - JAVA
  - Class
  - Method
  - 객체지향
toc: true
---

## Lesson point

> 1. 생성자 = 초기 자동 실행 메소드
> 2. 생성자 이름 = 클래스 이름

<br>

## 생성자

JAVA의 생성자 Constructor란 무엇이며 어떤 때 사용하는가?

- **생성자는 메소드다.** 다만, 실행되는 시점이 정해져있다. 
  new를 사용할 때 자동으로 적용된다.
- new를 사용한다는 건, 객체를 만들어낸다는 것이다. 
  즉, **객체 생성시 자동으로 설정하고 싶은 것**들을 적어준다. 
- 문법적으로 특이한 점은 **클래스와 이름이 무조건 같은 메소드**라는 것이다. 

<br>

생성자는 java class 생성시, 자동으로 생성되도록 설정할 수 있다. 
클래스를 생성하고 생성자를 설정하여 어떻게 동작하는지 확인해보자. 

<br>

![img](https://blog.kakaocdn.net/dn/cfs7ZP/btrUKY7haVP/s2z7A4IK0qcPxaKKRy8Jpk/img.png)

<br>

### Coffee01

```java
package Pack02;

public class Coffee01 {
	
	// 1. 생성자
	// 유일하게 직접적으로 호출하지 않는 메소드
	// new로 객체 생성시 자동 실행
	public Coffee01() {
		Type = "Unknown";
		Price = 0;
		Iced = false;
	}
	
	// 2. 커피의 정보 모델링
	private String Type;
	private int Price;
	private boolean Iced;

	// 3. Setter
	void setType(String t) {Type = t;}
	void setPrice(int p) {Price = p;}
	void setIced(boolean i) {Iced = i;}
	
	// 4. Getter
	String getType() {return Type;}
	int getPrice() {return Price;}
	boolean GetIced() {return Iced;}
	
	// 5. Putter
	void putType() {System.out.println("커피종류: "+Type);}
	void putPrice() {System.out.println("커피가격: "+Price);}
	void putIced() {System.out.println("아이스여부: "+Iced);}
}
```

<br>

### Coffee02

```java
package Pack02;

public class Coffee02 {

	public static void main(String[] args) {
		// 1. 객체 생성
		Coffee01 c = new Coffee01();
		
		// 2. 생성자를 통해 초기화된 정보 확인
		c.putType();
		c.putPrice();
		c.putIced();		
	}
}
```

<br>putter를 통해 생성자를 통해 초기화된 정보는 아래와 같이 확인 가능하다. 

<br>

![img](https://blog.kakaocdn.net/dn/CErUZ/btrUKXggmme/JHm9bDDQSafql0QkotJ0t0/img.png)