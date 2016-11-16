title: NULL和空字符陷阱
date: 2011-04-24 11:48:23
updated: 2013-08-22 03:11:04
tags: SQL
comments: true
categories: db
---
平时使用`NULL`和空字符`''`时碰到不少陷阱，本文描述了一些常见陷阱已经一些总结。

 - 使用`NOT IN`条件子集中含有`NULL`值时返回结果异常
``` sql
select * from
(
SELECT 1 AS ID FROM dual UNION SELECT 2 AS ID FROM dual UNION SELECT 3 AS ID FROM dual
) t1 where ID not in (select ID from
(
SELECT 1 AS ID FROM dual UNION SELECT 2 AS ID FROM dual UNION SELECT NULL AS ID FROM dual
) t2
)
```
`sqlplus`结果`no rows selected`
<!--more-->
明明t1中的`ID=1、3`在t2表中有对应ID相等情况只有`ID=2`在t2表中不存在，那为什么没有返回结果呢？
原因是当子查询返回含有`NULL`记录时，使用`NOT IN`时将不会有返回记录。Oracle中的`NULL`是一个不确定的状态。 t2表中的记录`ID=2`在与t2表中的`NULL`进行比较时，`NULL`既可以是`2`，也可以不是`2`。因为判断不了他们的关系，所以只能返回空记录。
 - 使用`IN`条件子集中含有`NULL`值时返回结果正常
``` sql
select * from
(
SELECT 1 AS ID FROM dual UNION SELECT 2 AS ID FROM dual UNION SELECT 3 AS ID FROM dual
) t1 where ID in (select ID from
(
SELECT 1 AS ID FROM dual UNION SELECT 2 AS ID FROM dual UNION SELECT NULL AS ID FROM dual
) t2
)
```
`sqlplus`结果`2`
上面1、中说明了`NULL`是不确定状态，使用`NOT IN`不能正确返回结果，而使用`IN`又可以正确返回结果呢？ 因为t2表中确实存在`ID=1`和`3`的记录，因此这两条记录被返回，而`ID=2`与`NULL`进行比较时仍然不确定，所以不返回。
 - 同样的陷阱也存在与`case`判断语句 例如
``` sql
select case when NULL NOT IN('1') then '真' else '假' end from dual;
select case when 1 IN (NULL) then '真' else '假' end from dual;
select case when  '' is null  then '真' else '假' end from dual;
```
①和② ‘假’③‘真’。同样的道理，①和②是返回‘假’已经在上面解释过了，那么③返回‘真’有是怎么回事呢？
 - 数学运算
``` sql
select nvl(null + 0,-1) from dual;--返回结果为null
```
 - 字符串连接操作
``` sql
select 'a' || null || 'b' from dual; --返回 ab
```
 - 内置函数
``` sql
select nvl(length(null),-1) from dual;
select nvl(min(null),'-1') from dual;
select nvl(max(null),'-1') from dual;
select nvl(sum(null),'-1') from dual;
select nvl(avg(null),'-1') from dual;
select nvl(sum(null),'-1') from dual;
select nvl(trim(null),-1) from dual;
select nvl(ltrim(null),'-1') from dual;
select nvl(rtrim(null),'-1') from dual;
select nvl(rtrim(null,' '),'-1') from dual;
select nvl(SubStr(null,1),-1) from dual;
select nvl(InStr(null,1),-1) from dual;
select nvl(replace(null,'a','b'),'-1') from dual;
select nvl(soundex(null),'-1') from dual;
```
以上函数返回结果为`null`
``` sql
select concat('1', null) from dual;
select concat(null, '1') from dual;
select count(null) from dual;
```
以上函数返回结果不为`null`
其他函数待整理
 - 总结：
oracle 将空字符串即`''`当成`NULL`，`NULL`是个不确定状态，不会返回‘预料’的比较结果，在使用的时候应该注意巧避陷阱；
排除`NOT IN`子查询中存在的`NULL`值
使用`NOT EXISTS`或者"外连接"
`NULL`和任何值做逻辑运算结果都是`flase`，包括`NULL`本身，不过`IS NULL`判断是，空字符`''`和`null`都是`IS NULL`；
`NULL`和任何数值进行数学运算，结果都为`NULL`，但是与字符串连接操作可以把`NULL`当作空字符`''`处理；
使用内置函数操作`NULL`时注意总结和测试；
