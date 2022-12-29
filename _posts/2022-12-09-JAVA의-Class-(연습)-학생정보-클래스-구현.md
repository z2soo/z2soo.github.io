---
title: "JAVA의 Class (연습) 학생정보 클래스 구현"
categories: 
  - JAVA
tags:
  - JAVA
  - Class
  - Method
  - 객체지향
toc: true
---

## **To-Do**

> 1. 학생 정보를 클래스로 구현한다. 
> 2. 국어, 영어, 수학 점수 저장
> 3. 국어, 영어, 수학 점수 합산하여 저장
>
> \- 하나의 성적이라도 음수면 합산하지 말고 오류처리
>
> 4. 합산된 성적으로 평균 점수 저장
>
> \- 총점이 음수면 평균 계산 하지말고 오류처리



## **Key point**

> **IF 문법**
> if (조건문) {실행문;} else (조건문) {실행문;}
>
> **소수 출력 (소수점 3개까지만 출력)**
> System.out.printf("소수출력: %.3f", 소수변수); 



## **Java Code**

### **StudentInfo**

학생 정보에 대한 클래스, 속성, 메소드를 생성한다. 

```java
package Pack03;

public class StudentInfo {
	
	// 1. 생성자
	public StudentInfo() {
	}
	
	// 2. 속성
	private String studentName;
	private int scoreEng;
	private int scoreKor;
	private int scoreMath;
	private float scoreAvg;
	private float scoreTot;		
	
	// 2.Setter
	void setName(String name) {studentName=name;}
	void setEng(int eng) {scoreEng=eng;}
	void setKor(int kor) {scoreKor=kor;}
	void setMath(int math) {scoreMath=math;}
	void setAvg(float avg) {scoreAvg=avg;}
	void setTot(float total) {scoreTot=total;}
	
	// 3. Getter
	String getName() {return studentName;}
	int getKor() {return scoreKor;}
	int getEng() {return scoreEng;}
	int getMath() {return scoreMath;}
	float getAvg() {return scoreAvg;}
	float getTot() {return scoreTot;}
	
	// 4. Putter
	void putName() {System.out.println("이름: "+studentName);}
	void putEng() {System.out.println("영어성적: "+scoreEng);}
	void putKor() {System.out.println("국어성적: "+scoreKor);}
	void putMath() {System.out.println("수학성적: "+scoreMath);}
	// 실수는 소수점 3자리까지만 출력하도록 함
	void putTot() {System.out.printf("성적총합: %.3f\n", scoreTot);}
	void putAvg() {System.out.printf("성적평균: %.3f\n", scoreAvg);}
	void putInfo() {
		putName();
		putEng();
		putKor();
		putMath();
		putTot();
		putAvg();
	}
	
	// 5. 기타
	boolean processTotal() {
		if ( getEng() < 0 || getKor() < 0 || getMath() < 0 ){
			return false;
		} else {
			setTot(scoreEng+scoreKor+scoreMath);
			return true;
		}
	}
	
	boolean processAvg() {
		if ( getEng() < 0 || getKor() < 0 || getMath() < 0 ) {
			return false;
		} else {
			processTotal();
			setAvg(scoreTot/3.0f);
			return true;
		}
	}		
}
```

<br>

### StudentEx

학생 정보 클래스로 두 학생의 객체를 만들고, 하나의 컨테이너에 저장한다. 

```java
package Pack03;
import java.util.ArrayList;

public class StudentEx {	
	public static void main(String[] args) {
		
		//1. Kim 
		StudentInfo Kim = new StudentInfo();
		Kim.setName("김씨");
		Kim.setEng(100);
		Kim.setKor(80);
		Kim.setMath(45);
		Kim.processTotal();
		Kim.processAvg();
//		Kim.putInfo();

		//2. Park 
		StudentInfo Park = new StudentInfo();
		Park.setName("박씨");
		Park.setEng(90);
		Park.setKor(74);
		Park.setMath(99);
		Park.processTotal();
		Park.processAvg();
//		Park.putInfo();
		
		//3. 두 객체를 하나의 컨테이너에 넣기
		ArrayList students = new ArrayList();
		students.add(Kim);
		students.add(Park);
		System.out.println(students);		
		System.out.println((StudentInfo)students.get(0));
		((StudentInfo)students.get(0)).putInfo();
		
		//4.Generic type
		//특정 타입만 들어갈 수 있도록 제약을 거는 것
		ArrayList studentsGen = new ArrayList<StudentInfo>();
	}	
}
```

<br>

## **Eclipse Screen**

### StudentInfo

학생 정보에 대한 클래스, 속성, 메소드를 생성한다. 

![img](https://blog.kakaocdn.net/dn/bhps9M/btrULO47JhT/8UQhqqVh6alsUbuQbQIslK/img.png)

<br>

### StudentEx

학생 정보 클래스로 두 학생의 객체를 만들고, 하나의 컨테이너에 저장한다. 컨테이너에 저장된 객체는 object type으로 저장되기에, 저장된 학생 정보 객체를 불러올 때, StudentInfo 라는 객체 타입으로 type casting을 해줘야 한다. 



![img](https://blog.kakaocdn.net/dn/ccM3hU/btrUGGUMxxV/uVpP6qc1W9BLpTFQRdqrrk/img.png)

<br>

![img](https://blog.kakaocdn.net/dn/dE6d6b/btrUIgBj0oj/8be4X5SUmK7TVoZvC7ysTk/img.png)

<br>

컨테이너에 StudentInfo 라는 특정 클래스 객체만 저장하고 싶은 경우는 어떻게 할까?

바로 Generic 객체로 객체 생성시 <타입> 을 입력하여 제한을 거는 것이다. 이 내용은 다음 글에 이어 진행하겠다. 
