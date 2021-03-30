---
layout: post
title: 계층형 쿼리
categories: Oracle
tags: [Oracle, DB]
---

한 테이블 내에서 칼럼간의 계층을 표시할 때 사용

```SQL
-- ex) dcode=0001인 부서에서 시작해 pdept 방향으로 계층 작성
SELECT level, lpad(dname, LEVEL*6, '*') AS 부서명
FROM dept2
CONNECT BY dcode = PRIOR pdept		-- dcode -> pdept
START WITH dcode = 0001;
```

CONNECT BY : 칼럼간의 연결관계

START WITH : 계층을 나타내기 시작할 칼럼값

PRIOR : 기준설정(방향)

LEVEL : 계층의 깊이