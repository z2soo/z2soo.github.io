---
title: "JAVA의 Class (연습) 커피가게 구현"
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

> \- 커피가게를 클래스로 구현한다. 
> \- 객체에 대한 CRUD 작업을 진행한다. 
> \- 컨테이너 ArrayList를 생성하여 다양한 coffee 클래스 객체를 저장하고 읽어오는 작업을 진행한다. 



Coffee에 대한 클래스는 아래 포스팅 중 coffee01 부분을 참고한다. 

[JAVA의 Class (2) 생성자 Constructor](https://z2soo.github.io/java/JAVA%EC%9D%98-Class-(2)-%EC%83%9D%EC%84%B1%EC%9E%90-Constructor/)



## **JAVA Code**

```java
package Pack02;
import java.util.ArrayList;

public class Coffee03 {
	public static void main(String[] args) {
		//1. Generic ArrayList 선언
		ArrayList <Coffee01> orderList = new ArrayList();
		
		//2. 커피 객체 생성 및 속성 저장
		Coffee01 order01 = new Coffee01();
		order01.setType("아이스아메리카노");
		order01.setPrice(1500);
		order01.setIced(true);
		
		Coffee01 order02 = new Coffee01();
		order02.setType("핫초코");
		order02.setPrice(4500);
		order02.setIced(false);
		
		//3. 오더 리스트에 저장
		orderList.add(order01);
		orderList.add(order02);
		
		//4. CRUD 연습
		Coffee01 temp = (Coffee01)orderList.get(0);
		temp.setPrice(1000);
		temp.setType("할인된 아이스아메리카노");
		
		//5. 검색
		boolean isfound=false;
		
		for(Coffee01 x: orderList) {
			if (x.getType().equals("핫초코")) {isfound = true;}
		}
		if (isfound) {
			System.out.println("핫초코가 오더 리스트에 있습니다.");}
		else {
			System.out.println("핫초코가 오더 리스트에 없습니다.");}
	}
}
```



## **Eclipse Screen**

![img](https://blog.kakaocdn.net/dn/dmrqQx/btrUJj5H3uN/J8A9w4KwT2OT6Ye2XFuJKk/img.png)

