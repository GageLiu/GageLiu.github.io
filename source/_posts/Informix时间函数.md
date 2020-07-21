---
title: informix时间函数
date: 2018-09-20 11:13:36
index_img:  /img/indexImg/javautil.png
categories: 工具字典
tags: 工具字典
---

# Informix时间函数

## 转载：[Informix时间函数](https://blog.csdn.net/u012895467/article/details/21173547)

### 函数：

- DATE

  - DATE(VARCHAR(10))返回DATE类型

    该函数以一个字符串变量为参数，其格式由环境变量 DBDATE 指定，并返回一个 DATE 类型。US English 地区的缺省格式是“MDY4/”。

  - DATE(DATETIME) 返回 DATE 类型

    这个函数与上一个函数相同，但是其输入参数是可以为任意精度的 DATETIME。

  - DATE(INTEGER) 返回 DATE 类型

    INTEGER 参数表示从 1899 年 12 月 31 日以来的天数。

- DAY

  - DAY(DATE) 返回 INTEGER 类型

    DAY 函数返回月中的日，格式为 INTEGER。

  - DAY(DATETIME) 返回 INTEGER 类型

    与上一个函数相同，只是这个函数的输入参数为任意精度的 DATETIME。

- EXTEND

  - EXTEND(DATE, precision) 返回 DATETIME 类型

    EXTEND 函数调整 DATE 参数的精度，并返回适当的 DATETIME。由于说起来有点儿模糊，这里举一个例子： EXTEND(DATE(1), YEAR TO SECOND)

  - EXTEND(DATETIME, precision) 返回 DATETIME 类型 与上一个函数相同，但操作的对象是一个 DATETIME，而不是一个 DATE。

- MONTH

  - MONTH(DATE) 返回 INTEGER 类型

    MONTH 从参数 DATE 中提取出月份。

  - MONTH(DATETIME) 返回 INTEGER 类型

    该函数从任意精度的 DATETIME 中提取出月份。

- WEEKDAY

  - WEEKDAY(DATE) 返回 INTEGER 类型

    WEEKDAY 函数根据指定的 DATE 返回一个 INTEGER，表示星期几。0 表示星期天，6 表示星期六。

  - WEEKDAY(DATETIME) 返回 INTEGER 类型

    与上一个函数相同，但操作对象是 DATETIME。

- YEAR

  - YEAR(DATE) 返回 INTEGER 类型

    该函数从指定的参数 DATE 中提取出年份。

  - YEAR(DATETIME) 返回 INTEGER 类型

    与上一个函数相同，但操作对象是 DATETIME。

- MDY

  - MDY(INTEGER, INTEGER, INTEGER) 返回 DATE 类型

    该函数根据三个 INTEGER 参数创建一个 DATE。这些参数分别指定月、日和年。注意，年是四位的整数。

- TO_CHAR

  - TO_CHAR(DATE, VARCHAR(??)) 返回 VARCHAR(??) 类型

    该函数带一个 DATE 参数和一个格式参数，并返回一个表示日期的字符串，该字符串遵从要求的格式。格式字符串可以包括：

    %A: 周
    %B: 月
    %d: 十进制表示的日
    %Y: 4 位数表示的年
    %R: 按 24 小时计的时间

  - TO_CHAR(DATE, VARCHAR(??)) 返回 VARCHAR(??) 类型

    同上。

  - TO_DATE(VARCHAR(??), VARCHAR(??)) 返回 DATE 类型

    这是 TO_CHAR 的逆向操作，使用相同的格式字符串作为第二个参数

### informix数据库中的时间运算

下面的语句中extend用来限制到年、月、日，interval是说明间隔数减去（加上）天数，interval最多取两位

\###ep：

- 从time_stamp字段减去两天时间
  select extend(time_stamp-interval(2) day to day) from users;
- 从当前时间减去两天
  select extend(current-interval(2) day to day) from users;
  减去（加上）月数，interval最多取两位
- 从time_stamp字段减去两个月
  select extend(time_stamp-interval(2) month to month) from users;
- 从当前时间减去两个月
  select extend(current-interval(2) month to month) from users;
  减去（加上）年数，interval最多取四位
- 从time_stamp字段减去两个年
  select extend(time_stamp-interval(2) year to year) from users;
- 从当前时间减去两年
  select extend(current-interval(2) year to year) from users;
  试了一下，上面的写法会显示到毫秒，如
  2004-09-11 17:24:03.000
  2004-09-11 17:24:03.000
  2004-09-11 17:24:03.000
  可以再加个extend限制，如下
  显示到月
  select extend(((extend(current-interval(2) day to day))),year to month) from users;
  显示到天
  select extend(((extend(current-interval(2) month to month))),year to day) from users;
  显示到秒
  select extend(((extend(current-interval(2) year to year))),year to second) from users;
  上面讲的是其中一种方法，今天又学到一种，可以用units关键字。
  –年
  select current-5 units year from users
  –月
  select current-5 units month from users
  –日
  select current-5 units day from users
  –时
  select current-5 units hour from users
  –分
  select current-5 units minute from users
  –秒
  select current-5 units second from users

### 几种时间:

select today+1-MDY(1,1,YEAR(today)) from your_table – day of year
select TRUNC((today+6-MDY(1,1,YEAR(today))-WEEKDAY(today))/7,0)+1 your_table – week of year
SELECT TODAY - DAY(TODAY) AS LDOLM FROM your_table – last_day_of_last_month
SELECT LDOLM - DAY(LDOLM) + 1 AS FDOLM FROM your_table – first_day_of_last_month
SELECT (TODAY - DAY(TODAY)) - DAY(TODAY - DAY(TODAY)) + 1 AS FDOLM FROM your_table
SELECT ((TODAY) + 1 UNITS MONTH - (DAY(TODAY)) UNITS DAY) FROM your_table – last_day_of_month
select TODAY - DAY(TODAY) + 1 UNITS DAY from your_table – first day of month
select MDY(1,1,YEAR(_date)) from your_table – first day of year