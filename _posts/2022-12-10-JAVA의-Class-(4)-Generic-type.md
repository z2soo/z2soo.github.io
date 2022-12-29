---
title: "JAVA의 Class (4) Generic type"
categories: 
  - JAVA
tags:
  - JAVA
  - Class
  - Method
  - 객체지향
toc: true

---

## **Key point**

> 1. JAVA에서 String 비교시 '==' 사용 불가 (숫자는 가능)
> 2. Generic type은 일종의 타입 제약을 거는 것



## **1. Generic type**

제네릭(Generic) 은 클래스 / 인터페이스 / 메서드 등의 타입을 파라미터로 사용할 수 있게 해주는 역할을 한다. 이를 통해 비제네릭 타입의 코드에서 발생하는 불필요한 타입 변환 또는 오류를 잡아낼 수 있게 된다. 해당 포스팅에서는 ArrayList에 넣을 객체를 특정 클래스로 제한하는 generic 선언을 예로 들겠다. 

<br>

코드는 이전의 코드와 이어지니 참고하도록 하자. <br>[JAVA의 Class (연습) 학생정보 클래스로 구현](https://z2soo.github.io/2022-12-09-JAVA%EC%9D%98-Class-(%EC%97%B0%EC%8A%B5)-%ED%95%99%EC%83%9D%EC%A0%95%EB%B3%B4-%ED%81%B4%EB%9E%98%EC%8A%A4-%EA%B5%AC%ED%98%84/)

<br>아래 코드를 보면 ArrayList 새로운 객체를 선언할 때 <studentInfo>라는 Generic type을 선언해주었다. 이 경우 stydentGen 컨테이너에는 studentIndo 라는 타입의 객체만 저장될 수 있다. 

```java
// Generic type
// 특정 타입만 들어갈 수 있도록 제약을 거는 것
	ArrayList <StudentInfo> studentsGen = new ArrayList();
		
	studentsGen.add(Kim);
	studentsGen.add(Park);
```

<br>

## 2. **객체의 for 문법**

컨테이너에 저장된 객체를 모두 체크 또는 젒근해야 하는 경우 아래와 같이 for 문을 사용한다. 

```java
// for each : 컨테이너 및 객체 존재 시에만 사용
	for( StudentInfo x : studentsGen) {
		String x_name = x.getName();
        //**java는 글자를 '=='로 비교할 수 없어서 String의 메소드 사용해야함!
		if (x_name.equals("김씨")) {
			System.out.println("김씨 찾았다!");
			x.putInfo();
		}
```

<br>

## 3. **Eclipse Screen**

![img](https://blog.kakaocdn.net/dn/cpVx2A/btrUL7jneDX/VSK0x8LmGKkFGBqY8ubDD0/img.png)

