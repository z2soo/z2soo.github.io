---
title: "Introduction to Pipeline Step"
categories: 
  - SAP
tags:
  - SAP
  - BTP
  - Integration Suite
  - Pipeline step
toc: true
---

## 1. Pipeline Steps

CPI(Cloud Platform Integration)은 아래와 같이 구성되어 있다. 

- Pools = Design
- Message Transformers
- Message Routing
- Message Validators
- Message Persistence
- Security Elements
- Events



## 2. Pools

- Integration Process

  CPI가 수행하는 메시지의 변경, 송수신 등의 스텝을 디자인 하는 것

- Local Integration Process

  - Process Call : Integration Process가 너무 복잡해지는 것을 막기 위해 특정 로직을 따로 구성하는 것
  - Looping Process Call : 특정 조건에서 루프를 돌며 수행할 수 있도록 하는 것

- Exception Subprocess

  예외 사항에 대한 핸들링 디자인 하는 것



## 3. Message Transformers

- Mapping

  Source structure를 Target structure로 변경하는 역할을 함

  - Message Mapping
  - XSLT Mapping

- Encoder

  메시지 변환 또는 압축 수행

- Decoder
  Encoding 전의 모습으로 메시지 변환 또는 압축 해제 수행

- Filter
  메시지를 원하는 형태로 가공하는 역할을 함

- Content Modifier
  메시지(Header, Body) 내의 변수들을 변경하는 역할을 함

- Converter
  메시지 포맷을 변경하는 기능을 함

  - CSV to XML
  - XML to CSV
  - XML to JSON
  - JSON to XML
  - XML to EDI
  - EDI to XML

- Script

  메시지 변환 등을 위한 java, groovy script를 실행하는 기능을 함



## 4. Routing

- Splitter
  XML 메시지를 특정 기준으로 해서 분리시키는 기능
- Router
  어떤 시스템으로 보낼 것인지에 대해 번호를 부여하는 기능
- Multicast
  동시에 실행시켜주는 기능
  - Pararell
  - Sequence
- Join / Gather / Aggregator
  XML 메시지를 특정 기준으로 해서 병합, 합쳐주는 기능



## 5. Persistence

- Data store Operations

  DB에 데이터를 CRUD 할 수 있는 역할, 기능

- Write Variables

  변수를 지정할 수 있는 역할



## 6. Send

비동기 방식의 adapter를 사용할 수 있다. 

- Mail adapter
- SFTP adapter



## 7. Event

Integration Process, Local Integration Process, Exceptional Process의 처음 시작과 끝 등을 정의해주는 기능을 한다. 

- End Message
- Terminate Message
- Error Start Event
- Error End Event
- Escalation
- Start Event
- End Event
- Timer



## 8. Security Element

- Encryptor
  메시지 암호화 기능
- Decryptor
  메시지 복호화 기능
- Message Signer
  전자서명 기능
- Signature Verifier
  상대의 전자서명이 유효한지 확인하는 기능
