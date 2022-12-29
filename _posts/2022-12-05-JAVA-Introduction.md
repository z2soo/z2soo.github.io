---
title: "JAVA Introduction
categories: 
  - JAVA
tags:
  - JAVA
  - Class
  - Method
  - 객체지향
toc: true
---

## **1. 자바엔진 (JDK, JRE, JVM) 이란?**

| 자바엔진 | 설명                                                         |
| -------- | ------------------------------------------------------------ |
| **JDK**  | - Java Development Kit  <br>- JRE를 포함함  - 개발자를 위해 만들어 놓은 세트   <br>- ex. 라이브러리 포함, 디버깅 하기 좋은 환경, 세팅할 사항 존재, 설치 용량 큼 |
| **JRE**  | - Java Runtime Enviromment  <br>- 자바코드를 작동시킬 수 있는 환경 세트 (일반 유저용) |
| **JVM**  | - Java Vertual Machine  <Br>- 자바코드로 만들어 놓은 실행파일을 작동시킬 수 있게 해주는 엔진 <br>- 컴파일러, 인터프리터, 해석기 등 <br>- 어느 OS 환경이든 JVM을 설치하면 실행 가능함 > 플랫폼에 독립적! |



## **2. Java를 위해 설치해야 할 프로그램**

### Java

Java는 아래 Oracle 공식 사이트를 통해 설치해준다. 단, 기존에 Java 프로그램이 설치되어 있다면, 다시 설치할 때 버전 등의 오류가 날 수 있으니 기존에 설치되어 있는지 확인하자.

[Download the Latest Java LTS Free](www.oracle.com)



명령 프롬프트 창에 java 라고 단순하게 입력했을 때, 아래와 같은 코드가 출력되면 이미 존재한 상태이다. 

```
C:\Users\user>java
```



![img](https://blog.kakaocdn.net/dn/cTQ5EQ/btrSUS9rTIe/GVUBvxwJtjVStIZjyAkCmk/img.png)

<br>

추가로 아래와 같이 입력하여 JVM 또한 잘 설치되어 있는지 확인하자. 

```
C:\Users\user>javac
```



![img](https://blog.kakaocdn.net/dn/slGiK/btrSVaoz9IK/roryM7jusm1OpDiE5FakvK/img.png)

<Br>

### 이클립스 설치

이클립스 공식 사이트에서 다운을 받아보자. 

[Eclipse Downloads | The Eclipse Foundation](www.eclipse.org)

개발을 위해 다운을 받기에 가장 상단의 for JAVA Developers를 선택해주고, 그 외에는 기본 설정으로 설치한다. JAVA와 Eclipse 설치를 완료하였다면, JAVA 코드를 작성하여 개발할 수 있는 환경을 다 구성하였다. 

<br>

![img](https://blog.kakaocdn.net/dn/b4GzKC/btrSUH1hfxH/iWBFwK6M9uktLOavikyqAK/img.png)