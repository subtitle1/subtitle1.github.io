---
layout: single
title: "[DB] Connect by level"
excerpt: ''
categories: Database
tag: sql, db, table
---
**[참고링크](https://loveuceci.tistory.com/1226)**

connect by절을 사용해서 오늘 기준 지난 15일 간의 날짜를 찾는 법을 알아보자. 응용하면 월, 연 단위로도 사용할 수 있고 통계 자료를 뿌릴 때 유용하게 사용될 sql이다.

>
1. from절에서 첫번째 날을 15일 전으로 세팅한다. <br>
2. connect by는 조건절로 생각하면 된다. first_day부터 trunc(sysdate)까지! <br>
3. 오늘(19일) 기준으로 sysdate - 15는 22/02/04이다. <br>
4. first_day + level - 1에서 level - 1은 x - 1로 생각하자.
{: .notice}

```sql
    SELECT
        TO_CHAR (FIRST_DAY + LEVEL - 1, 'YYYYMMDD') days
    FROM 
        (SELECT TRUNC(SYSDATE - 15) FIRST_DAY FROM DUAL)
    CONNECT BY FIRST_DAY + LEVEL - 1 <= TRUNC(SYSDATE);
```
