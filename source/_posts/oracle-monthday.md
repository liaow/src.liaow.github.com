title: oracle月份日期列表
date: 2013-01-10 06:48:23
updated: 2013-08-22 03:11:04
tags:
- oracle
- SQL
comments: true
categories: db
---
通常获取月份和日期的下拉列表都采用N多个SELECT FROM DUAL来union得到。这样不但写法死板，而且不具备可优性能。下面介绍几个月份和日期的下来SQL样例供大家参考：

1、历史6月的所有月份
```sql
SELECT TO_CHAR(ADD_MONTHS(sysdate, - ROWNUM), 'YYYYMM') bcyc_id FROM DUAL CONNECT BY ROWNUM <= 6;
```
<!--more-->
2、时间段之间的所有月份
```sql
SELECT TO_CHAR(ADD_MONTHS(to_date('2010-03', 'yyyy-mm'), - ROWNUM), 'YYYYMM') bcyc_id FROM DUAL CONNECT BY ROWNUM <= months_between (to_date('2010-03', 'yyyy-mm'), to_date('2009-03', 'yyyy-mm')) + 1;
```

3、历史10天所有日期
```sql
SELECT TO_CHAR(sysdate - ROWNUM,'YYYYMMDD') DAY_ID FROM DUAL CONNECT BY ROWNUM<= 10;
```

4、日期段之间的所有日期
```sql
SELECT TO_CHAR(TO_DATE('2009-04-1','YYYY-MM-DD')+ROWNUM-1,'YYYY-MM-DD') DAY_ID FROM DUAL CONNECT BY ROWNUM<=trunc(to_date('2009-05-23', 'yyyy-mm-dd')-to_date('2009-04-1', 'yyyy-mm-dd'))+1;
```

5、本月所有日期
```sql
SELECT TO_CHAR(last_day(sysdate) - ROWNUM +1,'DD') DAY_ID FROM DUAL CONNECT BY ROWNUM<=trunc(last_day(sysdate) - last_day (ADD_MONTHS(sysdate,-1)) )
```
