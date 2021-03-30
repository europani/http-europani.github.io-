---
layout: post
title: 사용자 정의 함수
categories: Oracle
tags: [Oracle, DB]
---

프로시저랑은 다르게 RUTURN(반환값) 갖음, 반환값은 단일값

일반적으로 DML문 안에서 사용하기 위해 만듦

```SQL
CREATE [OR REPLACE] FUNCTION 함수명(
    파라미터명1	[IN]	자료형,		-- IN 모드 :기본값 (생략가능)
    파라미터명2	[IN]	자료형,
    파라미터명3	[IN]	자료형
)
RETURN 리턴자료형
IS
    선언부;
BEGIN
    실행부;
    RETURN 리턴값;
END;

```

```SQL
CREATE OR REPLACE FUNCTION avg_sal(
  v_deptno  emp.deptno%TYPE)
  return number
IS
  v_avg   number;
BEGIN
  SELECT round(avg(sal), 2) INTO v_avg
  FROM emp
  where deptno = v_deptno;
  return v_avg;
END;
/

SELECT DISTINCT deptno, avg_sal(deptno) FROM emp;

```