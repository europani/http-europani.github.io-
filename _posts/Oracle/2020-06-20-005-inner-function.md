---
layout: post
title: 내장함수
categories: Oracle
tags: [Oracle, DB]
---

### 문자관련 함수

● UPPER, LOWER, INITCAP(문자열) : 문자를 대,소문자로 변환

● LENGTH(문자열) : 문자길이 출력, 

    LENGTHB(문자열) : 문자바이트수 출력

● CONCAT(문자열1, 문자열2) : 문자열 조합 (|| 연산자와 같음)

● SUBSTR(문자열, 시작위치, 길이) 

 - 시작위치가 음수일 때 진행방향은 바뀌지 않고 오른쪽

  ex) SUBSTR(JOB, 2, 3) : 2번째 글자부터, 3글자 출력 -> 2, 3, 4번째 글자 출력 (오라클은 1부터 시작)

       SUBSTR(JOB, -3, 3) : 뒤에서부터 3번째 글자부터, 3글자 출력 -> -3, -2, -1번째 글자 출력 (마지막 글자위치 : -1)

● INSTR(문자열, 찾는문자, 시작위치\[선택, d:1\], 시작위치로부터 찾는 문자가 몇번째인지\[선택, d:1\])

 - 시작위치가 음수일 때 진행방향은 바뀌어 왼쪽

  ex) INSTR('A\*B\*C\*', '\*', 2, 3) : 2번째 글자부터 시작해 3번째 \* 자리수출력 = 6

       INSTR('A\*B\*C\*', '\*',\-3, 2) : 뒤에서부터 3번째 글자부터 시작해 2번째 \* 자리수출력 = 2

● REPLACE(문자열, 대체될문자, 대체할문자\[선택, d:공백\]) 

● LPAD(문자열, 자릿수, 빈공간 채울문자\[선택, d:공백\])

    RPAD(문자열, 자릿수, 빈공간 채울문자\[선택, d:공백\])

● LTRIM(문자열, 삭제할문자\[선택\])

    RTRIM(문자열, 삭제할문자\[선택\]) 

### 숫자관련 함수

● ROUND(숫자, 반올림위치\[선택, d:0\]) : 반올림

● TRUNC(숫자, 버림위치\[선택, d:0\]) : 버림

 ex) 1234.5678

<table style="border-collapse: collapse; width: 62.8266%; height: 132px;" border="1"><tbody><tr><td style="width: 20%; text-align: center;">-2</td><td style="width: 20%; text-align: center;">-1</td><td style="width: 20%; text-align: center;">0</td><td style="width: 20%; text-align: center;">1</td><td style="width: 20%; text-align: center;">2</td></tr><tr><td style="width: 20%; text-align: center;">자연수 2자리</td><td style="width: 20%; text-align: center;">자연수 1자리</td><td style="width: 20%; text-align: center;">소수점 1자리</td><td style="width: 20%; text-align: center;">소수점 2자리</td><td style="width: 20%; text-align: center;">소수점 3자리</td></tr><tr><td style="width: 20%; text-align: center;">1200</td><td style="width: 20%; text-align: center;">1230</td><td style="width: 20%; text-align: center;">1235</td><td style="width: 20%; text-align: center;">1234.6</td><td style="width: 20%; text-align: center;">1234.57</td></tr></tbody></table>

● CEIL : 수직선상에서 큰 정수

● FLOOR : 수직선상에서 작은 변수

● MOD : 나머지

● POWER(2, 4) : 2^4

### 날짜관련 함수

● SYSDATE : 현재 시간 표시

ALTER SESSION SET NLS\_DATE\_FORMAT = 'YYYY-MM-DD'; -> 날짜 포맷 바꾸기

```SQL
ALTER SESSION SET NLS_DATE_FORMAT = 'YYYY-MM-DD:HH24:MI:SS';
```

날짜 + 1 ☞ +1일, 날짜 - 1 ☞ -1일

ADD\_MONTHS(날짜, 1) ☞ +1달

날짜 - 날짜 ☞ 일수 차이

날짜 + 날짜 ☞ X(불가능)

MONTHS\_BETWEEN(날짜, 날짜) ☞ 달수 차이

● NEXT\_DAY(날짜, 요일) : 다가오는 요일날짜 출력

● LAST\_DAY(날짜) : 날짜의 마지막날 출력

● ROUND(날짜, 년/월/일) 

● TRUNC(날짜, 년/월/일)

◎ 캐스팅 : 숫자 ↔ 문자 ↔ 날짜 (문자를 기준으로 캐스팅됨)

● TO\_CHAR(날짜 or 숫자, 날짜 or 숫자포맷) : 날짜 or 숫자 -> 문자

```SQL
-- 날짜 -> 문자  TO_CHAR(날짜, 날짜포맷)
SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD:HH24:MI:SS')
FROM dual;
```

```SQL
-- 숫자 -> 문자  TO_CHAR(숫자, 숫자포맷)
SELECT TO_CHAR(pay*12, '9,999')
FROM professor
```

● TO\_NUMBER(문자, 숫자포맷) : 문자-> 숫자

```SQL
SELECT TO_NUMBER('23,444', '99,999')
FROM dual;
```

● TO\_DATE(문자, 날짜포맷) : 문자 -> 날짜

```SQL
SELECT TO_DATE('2012/01/01', 'YYYY-MM-DD')
FROM dual;
```

### NULL 관련 함수

● NVL(컬럼, 치환값) : 컬럼값이 NULL이 아니면 본래값, NULL이면 치환값을 대신 출력 (본래값 타입 = 치환값 타입)

● NVL2(컬럼, 치환값1, 치환값2) : 컬럼값이 NULL이 아니면 치환값1, NULL이면 치환값2를 대신 출력

   (치환값1 타입 = 치환값2 타입)

```SQL
NVL(sal, 0)  -- sal이 있으면 sal값 없으면 0 출력
NVL2(sal, 'O', 'X') -- sal이 있으면 'O' 없으면 'X' 출력
```

◎ case에 따른 함수

● CASE 칼럼 WHEN A THEN B WHEN A2 THEN B2 ELSE C END

```SQL
SELECT name, deptno, 
    CASE deptno 
         WHEN 101 THEN '컴공'
         WHEN 102 THEN '경영'
         ELSE 0
    END AS 별명
FROM professor;
```

```SQL
SELECT name, deptno, 
    CASE  
         WHEN deptno = 101 THEN '컴공'
         WHEN deptno = 102 THEN '경영'
         ELSE 0
    END AS 별명
FROM professor;
```

● DECODE(컬럼, A, B, A2, B2, ..., C\[선택, d:NULL\]) : 컬럼값이 A이면 B출력 A2이면 B출력, 모두 아니면(else) C출력

```SQL
SELECT name, deptno, 
    DECODE(deptno, 
            101, '컴공', 
            102, '경영', 
            0) AS 별명
FROM professor;
```