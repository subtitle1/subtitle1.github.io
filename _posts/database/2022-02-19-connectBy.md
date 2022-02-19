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

![image](https://user-images.githubusercontent.com/87356533/154790444-16e8fb7e-f886-4bf5-8d7c-fdd70eb0a83a.png)

아래 sql문을 돌렸을 때 위 사진과 같은 결과가 나온다. 

```sql
    SELECT
        TO_CHAR (FIRST_DAY + LEVEL - 1, 'YYYYMMDD') days
    FROM 
        (SELECT TRUNC(SYSDATE - 15) FIRST_DAY FROM DUAL)
    CONNECT BY FIRST_DAY + LEVEL - 1 <= TRUNC(SYSDATE);
```

> 전체 sql

```sql
<select id="getTransactionAmountPerDay" resultType="com.hoge.dto.LabelDataDto">
    SELECT 
        SUBSTR(B.TODAY,-4) AS label, 
        NVL(a.TRANSACTION_AMOUNT, 0) as data
    FROM 
        (SELECT 
            TO_CHAR(TRANSACTION_DATE, 'YYYYMMDD') AS TRANSACTION_DATE,
            SUM(TRANSACTION_AMOUNT) AS TRANSACTION_AMOUNT
        FROM 
            TB_FINAL_TRANSACTIONS
        where 
            TRANSACTION_TYPE=1
        GROUP BY TO_CHAR(TRANSACTION_DATE, 'YYYYMMDD')) A, 
        
        (SELECT
            TO_CHAR (FIRST_DAY + LEVEL -1, 'YYYYMMDD') TODAY
        FROM 
            (SELECT TRUNC(SYSDATE - 15) FIRST_DAY FROM DUAL)
            CONNECT BY FIRST_DAY + LEVEL - 1 &lt;= TRUNC(SYSDATE)) B
        WHERE B.TODAY = A.TRANSACTION_DATE(+)
        ORDER BY B.TODAY;
  </select>
```