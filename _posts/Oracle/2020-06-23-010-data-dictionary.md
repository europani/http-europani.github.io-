---
layout: post
title: 데이터 사전(Data Dictionary)
categories: Oracle
tags: [Oracle, DB]
---

시스템과 관련된 중요한 데이터 보관 -> 메타테이터

  -> 메모리, 성능, 사용자, 권한, 객체 등

시스템 데이터베이스, 시스템 카탈로그 라고도 함

일반 사용자는 SELECT로 열람만 가능

```SQL
SELECT * FROM dict;
SELECT * FROM dictionary;
```

● USER\_ : 접속한 사용자의 객체 정보

 - USER\_TABLES : 사용자가 사용할 수 있는 모든 table 출력

● ALL\_ : 접속한 사용자의 객체 정보 또는 다른 사용자의 객체 중 사용 허가받은 객체 정보

 - ALL\_TABLES : 사용자가 사용할 수 있는 모든 table 출력, owner와 같이 출력됨

● DBA\_ : DB관리를 위한 정보

● $V\_ : DB성능 관련 정보