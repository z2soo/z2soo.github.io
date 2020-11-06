---
title: "ABAP Field catalog 최적화 클래스 개발 작성중.md"
categories: 
  - ABAP
tags:
  - abap
  - sap
toc: true
---

## 1. Field catalog

필드 카탈로그는 ALV에서 조회되는 칼럼들의 필드 정보를 포함하는 slis_t_fieldcat_alv 타입의 테이블 구조를 가진다. ALV는 필드 카탈로그 정보를 저장하는 internal table을 이용해 필드 타입을 인식하게 된다. 예를 들어, 필드의 타입과 화면에 보여줄 필드 길이를 지정할 수 있다. 그리고 체크박스, 라디오 버튼으로 보이게 하고, 필드 수정이 가능하게 하는 등 많은 역활을 하게 된다.

Grid를 이용힌 ALV와 같이 필드 카탈로그를 정의하는 방법은 3 가지가 있다. 

- ABAP Dictionary 오브젝트를 이용하는 방법
- 프로그램 내에서 스크립트로 수정으로 구성하는 방법: 필드 카탈로그
- 위의 두 방법을 혼합하여 사용하는 방법

마지막 방법의 경우 ABAP Dictionary 오브젝트 구조체와 필드 카탈로그에 같은 필드가 존재하게 되면, 필드 카탈로그에 정의한 필드가 높은 우선순위를 가진다.

<br><br>

## 2. Field catalog 최적화 클래스 개발



