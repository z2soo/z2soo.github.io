---
title: "JAVA의 Class (1) Putter, Setter, Getter"
categories: 
  - JAVA
tags:
  - JAVA
  - Class
  - Method
  - 객체지향
toc: true

---

## **Lesson point**

> 1. 클래스 = 데이터 + 기능(메소드)  
> 2. Java 하나의 파일에는 하나의 Class만 생성할 것 (클래스 파일)  
> 3. 어떤 언어를 공부하던 연산자를 암기하기 위해 시간을 보내지 말 것 (instanceof 연산자 정도는 OK)

  <br>

## **1. 메소드 4가지 스타일**

-   입력 없고, 출력 없는 스타일 ( void Temp() )
-   입력 있고, 출력 없는 스타일 ( void Temp(int X) )
-   입력 없고, 출력 있는 스타일 ( int Temp() )
-   입력 있고, 출력 있는 스타일 ( int Temp(int X) )

<br>

```java
public class Test {

	# 입력x, 출력x
	static Pluse01( ){
		int num1=100; int num2=200;
        int result = num1 + num2;
	}
    
    # 입력o, 출력x
    # 메소드 괄호 안에 입력받는 변수의 타입과 변수명을 입력
	static Pluse02( int X ){
		int num3 = X;
	}
    
    # 입력x, 출력o
    # 메소드 앞에 return 되는 데이터의 타입을 입력
	static int Pluse03( ){
		return 12345;
	}
    
    # 입력o, 출력o
	static float Pluse04( float X ){
        return X;
	}
    
    # 자바는 main메소드에서 시작해서 끝남 > 실행되는 부분
    public static void main(String[] args) {
    	# return 값의 타입과 return 값을 담을 변수의 타입이 동일해야 함
        int result01 = Plus01( );        
        Plus02( 12345 );
        int result03 = Plus03( );
        float result04 =Plus04( 123.03f );
    }
}
```

  <br>

## **2. 클래스**

클래스란 설계도라고 보면 된다. 즉, 대량의 물건을 생산할 수 있다는 의미로 붕어빵을 찍어내는 붕어빵 틀을 비유들고는 한다. 그리고 클래스를 이용해 생성되는 대상을 Object 라고 보며 그것이 java의 스타일이다. 간단하게 회원정보에 대한 클래스(설계도)를 생성해보자.

<br>

**MemberInfo**

MemberInfo 라는 java 파일을 생성하여 회원 정보에 속성, putter, getter, setter를 생성해본다.  
이 때, 위에서 언급한 4가지 스타일의 클래스 중 return 값이 없는 스타일이 setter, putter에 속하는데 이 경우는 method 앞에 return 되는 값의 타입 대신  void를 입력하면 된다.

-   Setter : 클래스의 객체화된 속성에 값을 저장 및 수정하기 위한 메소드
-   Getter : 클래스의 객체화된 속성에 값을 추출하기 위한 메소드
-   Putter : 클래스의 객체화된 속성의 값을 출력하기 위한 메소드

<br>

```java
public class MemberInfo {
	//회원 정보를 저장
	String mName;	//회원이름 문자열
	String mPhone;  //전화번호 문자열 
	
	//객체화된 속성
	private String mStatus;	//회원상태 문자열
	
	//setter: 데이터 저장 및 수정
	//void는 return 값이 없음을 의미함
	void setStatus(String x) {
		mStatus = x;
	}
	
	//getter: 데이터 추출
	String getStatus( ) {
		return mStatus;
	}
	
	//putter: 데이터 출력
 	void putStatus() {
 		System.out.printf("회원의 상태는: %s\n", mStatus);
 	}
}
```

<br>

**MemberTest**

위의 클래스를 사용하여 객체를 생성하고 값을 입력, 출력해보도록 하자. 위 클래스에서 mName, mPhone의 경우는 private 속성이 아니다. default 설정으로 같은 패키지 소속 클래스라면 접근이 가능한 상태로 아래와 같이 외부 클래스에서 접근(access)이 가능하다.

```java
package Pack01;

public class MemberTest {
		
	public static void main(String[] args) {
		// 클래스(설계도)를 이용하여 객체를 생성하자
		// m(객체이름)은 레퍼런스 변수라고 한다
		MemberInfo m = new MemberInfo();	
		m.mName = "홍길동";				// 이때의 '.'은 참조 연산자
		m.mPhone = "010-1234-4321";			
	}
}
```

<br>

하지만 객체지향에서는 위와 같이 접근이 가능하면 안된다. 따라서 mStatus 속성과 같이 private (동일 클래스 내에서만 접근 가능)으로 설정하고, 대신 접근할 수 있는 대리인(메소드)를 사용한다.

```java
package Pack01;

public class MemberTest {

	public static void main(String[] args) {

		MemberInfo m = new MemberInfo();	
        // Setter : 속성 값 설정
		m.setStatus("휴먼고객");
		
        // Getter : 속성 값 추출, return 값 존재
        String vStatus = m.getStatus();
		System.out.printf("%s\n");
		System.out.printf("%s\n", m.getStatus() );
		
        // Putter : 속성 값 출력
        m.putStatus();
	}
}
```

  <br>

## **3. Eclipse Screen**

이클립스에서 위의 코드를 작성한 화면이다. 객체를 생성하여 객체의 속성을 레퍼런스할 때 mName, mPhone 항목과 같이 앞에 삼각형 모양으로 표시되는 부분이 해당 객체의 속성이다.

![](https://blog.kakaocdn.net/dn/ccFESC/btrUl7S9heq/FL4Av7AnEfVi6sqO7kWNTK/img.png)

  <br>

객체의 속성을 레퍼런스 할 떄, 해당 속성이 private 속성이라면 아래와 같이 오류가 나타난다.

![](https://blog.kakaocdn.net/dn/cOeuGx/btrUojkrNbn/tSaNpObGOx5NpPqcCkFYz1/img.png)
